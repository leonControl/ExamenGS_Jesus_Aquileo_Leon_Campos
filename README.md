# ExamenGS_Jesus_Aquileo_Leon_Campos
Aplicación desarrollada en Kotlin para consumo de API themoviedb


# ING. JESÚS AQUILEO LEÓN CAMPOS

**leon.automatizacion@gmail.com**
**5524161665**

---
---

## 1. Configuración de dependencias


```xml

	 // Recyclerview  
	 implementation 'androidx.recyclerview:recyclerview:1.2.1'  
	 // Cardview  
	 implementation 'androidx.cardview:cardview:1.0.0'  
	  
	 // Retrofit  
	 implementation "com.squareup.retrofit2:retrofit:2.9.0"  
	 implementation "com.squareup.retrofit2:converter-gson:2.9.0"  
	 implementation 'com.squareup.okhttp3:logging-interceptor:3.9.1'  
	  
	 // Glide  
	 implementation 'com.github.bumptech.glide:glide:4.10.0'  
	  
	 // Corrutinas  
	 implementation "com.squareup.picasso:picasso:2.71828"  
	 implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.6'  
	  
	 // Gson  
	 implementation 'com.google.code.gson:gson:2.8.6'  

```


## Estructura del proyecto

El proyecto se conforma de los siguientes módulos:
- **Alerts** -> Crea Alerts para ser aplicados durante el proyecto
- **API´s** -> Conección con la API de themoviedb.org, se definen las Interfaces, Adaptadores, y Clases para la obtención de Movies y TV Shows
- **CreateRecyclers** -> Son 2 clases en las que se realiza la implementación de los 2 tipos de RecyclerViews empleados en el proyecto 
	1) Recycler vacios (RecyclerView)
	2) Recycler con la data desde la API (Apis)
- **Permissions** -> Se realiza la verificación y habilitación de permisos requeridos para la aplicación:
	1) INTERNET
	2) ACCESS_NETWORK_STATE
- **RecyclerView** -> Realiza la implementación del Recycler view cuando la aplicación se encuentra sin internet
- **VerifyEthernet** -> Realiza la verificación de la conectividad
	1) Conectado con wifi
	2) Conectado con datos móviles
	3) Sin conexión de internet 
- **MainActivity.kt** -> Inicializa la ejecución de la aplicación

![[Arbol del proyecto.png]]

## Verificar la conectividad
1. Verificar estado de la conectividad, se cuenta con 3 funciones para ver el estatus del internet:
	-  isOnline(context: Context): Boolean 
	- isConnectedWifi(context: Context): Boolean
	- isConnectedMobile(context: Context): Boolean
2. La lógica se realiza con la función verifyEthernet, la cual si no se cuenta con internet crea el recycler con las imágenes por default, si cuenta con internet crea los recyclers con la API de themoviedb.org
	- verifyEthertet( c : Activity )

```kotlin
class VerifyEthernet {  
  
    fun verifyEthertet( c : Activity ) {  
        val onLine: Boolean = VerifyEthernet().isOnline(c)  
        val wifi : Boolean = VerifyEthernet().isConnectedWifi(c)  
        val mobileData : Boolean = VerifyEthernet().isConnectedMobile(c)  
  
        if(onLine) {  
            if(wifi) {  
                CreateRecyclers().createRecyclers(c)  
                Toast.makeText(c, "Conectado a WiFi", Toast.LENGTH_SHORT).show()  
            } else if(mobileData){  
                CreateRecyclers().createRecyclers(c)  
                Toast.makeText(c, "Conectado a datos móviles", Toast.LENGTH_SHORT).show()  
            }  
        }  
        else {  
            CreateEmptyRecyclers().createRecyclers(c)  
            Alerts().showMyAlert(  
                c,  
 "SIN CONEXIÓN DE INTERNET",  
 "Vuelve a iniciar la aplicación con servicios de internet!"  
 )  
            Toast.makeText(c, "Sin conexión a internet", Toast.LENGTH_SHORT).show()  
        }  
    }  
  
    fun isOnline(context: Context): Boolean {  
        val connectivityManager =  
            context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager  
        val networkInfo = connectivityManager.activeNetworkInfo  
 return networkInfo != null && networkInfo.isAvailable && networkInfo.isConnected  
 }  
  
    fun isConnectedWifi(context: Context): Boolean {  
        val connectivityManager =  
            context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager  
        val networkInfo = connectivityManager.activeNetworkInfo  
 return networkInfo != null && networkInfo.type == ConnectivityManager.TYPE_WIFI  
 }  
  
    fun isConnectedMobile(context: Context): Boolean {  
        val connectivityManager =  
            context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager  
        val networkInfo = connectivityManager.activeNetworkInfo  
 return networkInfo != null && networkInfo.type == ConnectivityManager.TYPE_MOBILE  
 }  
  
}
```


## Habilitación de permisos
1. Se agregan los permisos en el manifest

```xml
<uses-permission android:name="android.permission.INTERNET" />  
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Se crean un arreglo de permisos, una función para recorrer el arreglo de permisos, se verifica que cada permiso del arreglo este autorizado, de no ser así se realiza la autorización, se lanza un Toast informando el estatus de los permisos.

```kotlin
class Permissions {  
    // PERMISSION VARIABLES  
	 private val myPermissions = arrayOf(  
	     android.Manifest.permission.INTERNET,  
		 android.Manifest.permission.ACCESS_NETWORK_STATE  
	 )  
  
  
    fun checkPermission(act: Activity) {  
        !hasPermissions(act, myPermissions)  
    }  
  
    fun hasPermissions(context: Context, permissions: Array<String>): Boolean {  
        if (permissions != null) {  
            for (permission in permissions) {  
                if (ActivityCompat.checkSelfPermission(  
                        context,  
 permission  
                    ) != PackageManager.PERMISSION_GRANTED  
 ) {  
                    Toast.makeText(context, "Permiso: " + permission + " deshabilitado", Toast.LENGTH_SHORT).show()  
                    return false  
 } else {  
                    Toast.makeText(context, "Permiso: " + permission + " habilitado", Toast.LENGTH_SHORT).show()  
                }  
            }  
        }  
        return true  
 }  
  
}
```


## Función para lanzar alertas, se habilito únicamente un botón, pero se puede habilitar mas funcionalidades

```kotlin
class Alerts : AppCompatActivity() {  
  
    fun showMyAlert(  
         c : Context,  
		 title : String,  
		 msg : String) {  
  
        val dialogBuilder = AlertDialog.Builder(c)  
  
        dialogBuilder.setMessage(msg)  
            //.setCancelable(false)  
		 .setPositiveButton("Aceptar", {  
		 dialog, id ->  
		 //Toast.makeText(c, "ACEPTAR", Toast.LENGTH_SHORT).show()  
		 finish()  
		            })  
                /*  
		 .setNegativeButton("Rechazar", DialogInterface.OnClickListener { dialog, id -> Toast.makeText(c, "RECHAZAR", Toast.LENGTH_SHORT).show() dialog.cancel() }) */  
		 // create dialog val alert = dialogBuilder.create()  
        alert.setTitle(title)  
        alert.show()  
    }  
}
```


## Creación del RecyclerView para el modo offline

1. Se crea un data class con los elementos que contendra cada Card, el view holder y se cargan las imágenes para el modo offline para generar un Recycler View.

```kotlin
data class ListAdapter(  
     val image: Int,  
	 val title: String  
)  
  
class EmptyAdapter(  
	private var mList: List<ListAdapter>,  
	) : RecyclerView.Adapter<EmptyAdapter.ViewHolder>() {  
	  
	    // CREATE VIEW HOLDER  
	 override fun onCreateViewHolder(  
	        parent: ViewGroup,  
	 viewType: Int  
	    ): ViewHolder {  
	        val view = LayoutInflater.from(parent.context)  
	            .inflate(R.layout.card, parent, false)  
	        return ViewHolder(view)  
	    }  
	  
	    // VIEW HOLDER  
	 class ViewHolder(ItemView: View) : RecyclerView.ViewHolder(ItemView) {  
	        val imageView: ImageView = itemView.findViewById(R.id.imageCardMovies)  
	        val textView: TextView = itemView.findViewById(R.id.titleCardMovies)  
	    }  
	  
	    override fun onBindViewHolder(holder: ViewHolder, position: Int){  
	        val ItemsViewModel = mList[position]  
	        holder.imageView.setImageResource(ItemsViewModel.image)  
	        holder.textView.text = ItemsViewModel.title  
		 }  
	  
	    // return the number of the items in the list  
	 override fun getItemCount(): Int {  
	        return mList.size  
	 }  
	  
}
```

## Creación del RecyclerView para el modo online y Conectividad con API themoviedb.org

1. Dentro de la carpeta se genera una estructura de archivos para tener un mejor manejo de codigo

![[arbolApi.png]]

2. En la carpeta Data se crean 4 data clases en las que se especifican las respuestas por parte del api , se crean 2 de elementos (Movie y TV) y 2 para los arrays del json

```kotlin
data class Movie(  
    // MOVIE  
	 @SerializedName("original_title") val original_title: String,  
	 @SerializedName("overview") val overview: String,  
	 @SerializedName("poster_path") val poster_path: String,  
)
```

```kotlin
data class TV(  
    // TV  
	 @SerializedName("name") val name: String,  
	 @SerializedName("overview") val overview: String,  
	 @SerializedName("poster_path") val poster_path: String,  
)
```

```kotlin
data class GetMoviesResponse(  
     @SerializedName("page") val page: Int,  
	 @SerializedName("results") val movies: List<Movie>,  
	 @SerializedName("total_pages") val pages: Int  
)
```

```kotlin
data class GetTVResponse(  
     @SerializedName("page") val page: Int,  
	 @SerializedName("results") val tv: List<TV>,  
	 @SerializedName("total_pages") val pages: Int  
)
```

3. Se genera un adaptador para Movies y otro adaptador para TV, ya que la respuesta por parte del Api es distinta para cada caso.

```kotlin
class MoviesAdapter(  
     private var movies: List<Movie>,  
	 private var c : Context,  
	 //private var cardListener: OnCardClickListener  
  
) : RecyclerView.Adapter<MoviesAdapter.CardViewHolder>() {  

 override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): CardViewHolder {  
        val view = LayoutInflater  
            .from(parent.context)  
            .inflate(R.layout.card, parent, false)  
        return CardViewHolder(view)  
    }  
  
	override fun getItemCount(): Int = movies.size  
  
	 override fun onBindViewHolder(holder: CardViewHolder, position: Int) {  
         
		 when(holder){  
            else -> holder.bind(movies[position], position)  
        }  
  
        // CLICK 1  
	 // ************************* holder.itemView.setOnClickListener {  
	 MyClickListener().onClick(position, holder.itemView, movies, c)  
	        }  
	 // *************************  
 }  
  
  
    fun updateMovies(movies: List<Movie>) {  
        this.movies = movies  
        notifyDataSetChanged()  
    }  
  
  
    inner class CardViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {  
        val poster: ImageView = itemView.findViewById(R.id.imageCardMovies)  
        val titlePoster : TextView = itemView.findViewById(R.id.titleCardMovies)  
  
        fun bind(movie: Movie, position: Int) {  

 Glide.with(itemView)  
                .load("https://image.tmdb.org/t/p/w342${movie.poster_path}")  
                .transform(CenterCrop())  
                .into(poster)  
  
            titlePoster.text = movies[position].original_title  
  
 }  
  
    }  
  
}  
  
// CLICK 1  
class MyClickListener {  
  
    fun onClick(pos: Int, aView: View, movies : List<Movie>, c : Context) {  
        var title = movies[pos].original_title  
 var overview = movies[pos].overview  
 var image = movies[pos].poster_path  
  
 Alerts().showMyAlert(c, "$title", "$overview" )  
    }  
}

```

```kotlin
class TVAdapter(  
    private var tv: List<TV>,  
 private var c : Context  
    ) : RecyclerView.Adapter<TVAdapter.ViewHolder>() {  
  
    inner class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {  
        private val poster: ImageView = itemView.findViewById(R.id.imageCardMovies)  
        val titlePoster : TextView = itemView.findViewById(R.id.titleCardMovies)  
  
        fun bind(tv: TV) {  
            Glide.with(itemView)  
                .load("https://image.tmdb.org/t/p/w342${tv.poster_path}")  
                .transform(CenterCrop())  
                .into(poster)  
        }  
    }  
  
    fun updateTV(tv: List<TV>) {  
        this.tv = tv  
        notifyDataSetChanged()  
    }  
  
  
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {  
        val view = LayoutInflater  
            .from(parent.context)  
            .inflate(R.layout.card, parent, false)  
        return ViewHolder(view)  
    }  
  
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {  
        holder.bind(tv[position])  
        holder.titlePoster.text = tv[position].name  
 // CLICK  
 // ************************* holder.itemView.setOnClickListener {  
 MyClickListenerTV().onClick(position, holder.itemView, tv, c)  
        }  
 // *************************  
  
 }  
  
    override fun getItemCount(): Int = tv.size  
  
}  
  
class MyClickListenerTV {  
    fun onClick(pos: Int, aView: View, tv : List<TV>, c : Context) {  
        val title = tv[pos].name  
 val overview = tv[pos].overview  
 var image = tv[pos].poster_path  
  
 Alerts().showMyAlert(c, title, overview)  
    }  
}
```

5. Se realiza la interfaz Api la cual contiene las 6 peticiones a la Api, en dónde se agrega la API KEY, por fines de seguridad la API KEY se omiten los últimos 4 dígitos.

```kotlin
interface Api {  
    // +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 // MOVIES @GET("movie/popular/")  
    fun getPopularMovies(  
        @Query("api_key") apiKey: String = "cc853221dccce9f5784ea0e34b",  
 @Query("page") page: Int  
    ): Call<GetMoviesResponse>  
  
    @GET("movie/top_rated")  
    fun getTopRatedMovies(  
        @Query("api_key") apiKey: String = "cc853221dccce9f5784ea0e34b",  
 @Query("page") page: Int  
    ): Call<GetMoviesResponse>  
  
    @GET("movie/upcoming")  
    fun getUpcomingMovies(  
        @Query("api_key") apiKey: String = "cc853221dccce9f5784ea0e34b",  
 @Query("page") page: Int  
    ): Call<GetMoviesResponse>  
  
  
    // +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 // TV SHOWS @GET("tv/popular/")  
    fun getPopularTV(  
        @Query("api_key") apiKey: String = "cc853221dccce9f5784ea0e34b",  
 @Query("page") page: Int  
    ): Call<GetTVResponse>  
  
    @GET("tv/top_rated")  
    fun getTopRatedTV(  
        @Query("api_key") apiKey: String = "cc853221dccce9f5784ea0e34b",  
 @Query("page") page: Int  
    ): Call<GetTVResponse>  
  
    @GET("tv/upcoming")  
    fun getUpcomingTV(  
        @Query("api_key") apiKey: String = "cc853221dccce9f5784ea0e34b",  
 @Query("page") page: Int  
    ): Call<GetTVResponse>  
  
}
```

5. Se crea un repositorio para contener las funciones de las 6 peticiones distintas a la Apis 

	- Se inicializa retrofit para la conexión con la Api
	- Se crean las funciones para realizar cada petición a la Api

```kotlin
object Repository {  
    private val api: Api  
  
    init {  
        val retrofit = Retrofit.Builder()  
            .baseUrl("https://api.themoviedb.org/3/")  
            .addConverterFactory(GsonConverterFactory.create())  
            .build()  
  
        api = retrofit.create(Api::class.java)  
    }  
  
    // MOVIES ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 fun getPopularMovies(page: Int = 1,  
 onSuccess: (movies: List<Movie>) -> Unit,  
 onError: () -> Unit) {  
        api.getPopularMovies(page = page)  
            .enqueue(object : Callback<GetMoviesResponse> {  
                override fun onResponse(  
                    call: Call<GetMoviesResponse>,  
 response: Response<GetMoviesResponse>  
                ) {  
                    if (response.isSuccessful) {  
                        val responseBody = response.body()  
  
                        if (responseBody != null) {  
                            onSuccess.invoke(responseBody.movies)  
                        } else {  
                            onError.invoke()  
                        }  
                    } else {  
                        onError.invoke()  
                    }  
                }  
  
                override fun onFailure(call: Call<GetMoviesResponse>, t: Throwable) {  
                    onError.invoke()  
                }  
            })  
    }  
  
  
    fun getTopRatedMovies(  
        page: Int = 1,  
 onSuccess: (movies: List<Movie>) -> Unit,  
 onError: () -> Unit  
    ) {  
        api.getTopRatedMovies(page = page)  
            .enqueue(object : Callback<GetMoviesResponse> {  
                override fun onResponse(  
                    call: Call<GetMoviesResponse>,  
 response: Response<GetMoviesResponse>  
                ) {  
                    if (response.isSuccessful) {  
                        val responseBody = response.body()  
  
                        if (responseBody != null) {  
                            onSuccess.invoke(responseBody.movies)  
                        } else {  
                            onError.invoke()  
                        }  
                    } else {  
                        onError.invoke()  
                    }  
                }  
  
                override fun onFailure(call: Call<GetMoviesResponse>, t: Throwable) {  
                    onError.invoke()  
                }  
            })  
    }  
  
  
    fun getUpcomingMovies(  
        page: Int = 1,  
 onSuccess: (movies: List<Movie>) -> Unit,  
 onError: () -> Unit  
    ) {  
        api.getUpcomingMovies(page = page)  
            .enqueue(object : Callback<GetMoviesResponse> {  
                override fun onResponse(  
                    call: Call<GetMoviesResponse>,  
 response: Response<GetMoviesResponse>  
                ) {  
                    if (response.isSuccessful) {  
                        val responseBody = response.body()  
  
                        if (responseBody != null) {  
                            onSuccess.invoke(responseBody.movies)  
                        } else {  
                            onError.invoke()  
                        }  
                    } else {  
                        onError.invoke()  
                    }  
                }  
  
                override fun onFailure(call: Call<GetMoviesResponse>, t: Throwable) {  
                    onError.invoke()  
                }  
            })  
    }  
  
  
  
    // TV SHOWS ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 fun getPopularTV(page: Int = 1,  
 onSuccess: (tv: List<TV>) -> Unit,  
 onError: () -> Unit) {  
        api.getPopularTV(page = page)  
            .enqueue(object : Callback<GetTVResponse> {  
                override fun onResponse(  
                    call: Call<GetTVResponse>,  
 response: Response<GetTVResponse>  
                ) {  
                    if (response.isSuccessful) {  
                        val responseBody = response.body()  
  
                        if (responseBody != null) {  
                            onSuccess.invoke(responseBody.tv)  
                        } else {  
                            onError.invoke()  
                        }  
                    } else {  
                        onError.invoke()  
                    }  
                }  
  
                override fun onFailure(call: Call<GetTVResponse>, t: Throwable) {  
                    onError.invoke()  
                }  
            })  
    }  
  
    fun getTopRatedTV(page: Int = 1,  
 onSuccess: (tv: List<TV>) -> Unit,  
 onError: () -> Unit) {  
        api.getTopRatedTV(page = page)  
            .enqueue(object : Callback<GetTVResponse> {  
                override fun onResponse(  
                    call: Call<GetTVResponse>,  
 response: Response<GetTVResponse>  
                ) {  
                    if (response.isSuccessful) {  
                        val responseBody = response.body()  
  
                        if (responseBody != null) {  
                            onSuccess.invoke(responseBody.tv)  
                        } else {  
                            onError.invoke()  
                        }  
                    } else {  
                        onError.invoke()  
                    }  
                }  
  
                override fun onFailure(call: Call<GetTVResponse>, t: Throwable) {  
                    onError.invoke()  
                }  
            })  
    }  
  
    fun getUpcomingTV(page: Int = 1,  
 onSuccess: (tv: List<TV>) -> Unit,  
 onError: () -> Unit) {  
        api.getUpcomingTV(page = page)  
            .enqueue(object : Callback<GetTVResponse> {  
                override fun onResponse(  
                    call: Call<GetTVResponse>,  
 response: Response<GetTVResponse>  
                ) {  
                    if (response.isSuccessful) {  
                        val responseBody = response.body()  
  
                        if (responseBody != null) {  
                            onSuccess.invoke(responseBody.tv)  
                        } else {  
                            onError.invoke()  
                        }  
                    } else {  
                        onError.invoke()  
                    }  
                }  
  
                override fun onFailure(call: Call<GetTVResponse>, t: Throwable) {  
                    onError.invoke()  
                }  
            })  
    }  
  
}
```


# Invocar los Recycler en modo Online y Offline

1. Para crear los Recycles en modo Offline se crean 2 funciones, uno para recyclers de Movies y otro de TV, también una función para agregar los tutulos en el Recycler

```kotlin
class CreateEmptyRecyclers() : AppCompatActivity() {  
  
    fun createRecyclers(act: Activity) {  
        createTitles(act)  
        createRecyclerMovies(act)  
        createRecyclerTV(act)  
  
    }  
  
    private fun createRecyclerMovies(act: Activity) {  
        val rv1 = act.findViewById<RecyclerView>(R.id.recyclerview1)  
        val rv2 = act.findViewById<RecyclerView>(R.id.recyclerview2)  
        val rv3 = act.findViewById<RecyclerView>(R.id.recyclerview3)  
  
        var adapter : EmptyAdapter  
  
        val rvArray = arrayOf<RecyclerView>(rv1, rv2, rv3)  
  
        for (i in 0 .. 2) {  
  
            rvArray[i].layoutManager = LinearLayoutManager(  
                this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
            val data = ArrayList<ListAdapter>()  
  
            for (i in 1..20) {  
                data.add(ListAdapter(R.drawable.movieimage, "Movie " + i))  
            }  
  
            adapter = EmptyAdapter(data)  
            rvArray[i].adapter = adapter  
        }  
    }  
  
    private fun createRecyclerTV(act: Activity) {  
        val rv1 = act.findViewById<RecyclerView>(R.id.recyclerview4)  
        val rv2 = act.findViewById<RecyclerView>(R.id.recyclerview5)  
        val rv3 = act.findViewById<RecyclerView>(R.id.recyclerview6)  
  
        var adapter : EmptyAdapter  
  
        val rvArray = arrayOf<RecyclerView>(rv1, rv2, rv3)  
  
        for (i in 0 .. 2) {  
  
            rvArray[i].layoutManager = LinearLayoutManager(  
                this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
            val data = ArrayList<ListAdapter>()  
  
            for (i in 1..20) {  
                data.add(ListAdapter(R.drawable.tvimage, "Tv Show " + i))  
            }  
  
            adapter = EmptyAdapter(data)  
            rvArray[i].adapter = adapter  
        }  
    }  
  
    private fun createTitles (act: Activity) {  
        // TITLES  
 val tv1 = act.findViewById<TextView>(R.id.title1)  
        val tv2 = act.findViewById<TextView>(R.id.title2)  
        val tv3 = act.findViewById<TextView>(R.id.title3)  
        val tv4 = act.findViewById<TextView>(R.id.title4)  
        val tv5 = act.findViewById<TextView>(R.id.title5)  
        val tv6 = act.findViewById<TextView>(R.id.title6)  
  
        val tvArray = arrayOf<TextView>(tv1, tv2, tv3, tv4, tv5, tv6)  
        val tvStr = arrayOf(  
             "Películas favoritas",  
			 "Películas recomendadas",  
			 "Películas mejor calificadas",  
			 "TV Show favoritos",  
			 "TV Show recomendados",  
			 "TV Show mejor calificados"  
			 )  
  
        for (i in 0 .. 5) {  
            tvArray[i].text = tvStr[i]  
        }  
    }  
  
}
```

2. Para crear los Recycles en modo Offline se crean 2 funciones, uno para recyclers de Movies y otro de TV, también una función para agregar los tutulos en el Recycler

```kotlin
class CreateRecyclers : AppCompatActivity() {  
  
    fun createRecyclers(act: Activity) {  
        createTitles(act)  
  
        createPopularMovies(act)  
        createTopRatedMovies(act)  
        createUpComingMovies(act)  
  
        createPopularTV(act)  
        createTopRatedTV(act)  
        createUpcomingTV(act)  
  
    }  
  
  
    // ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 // POPULAR MOVIES private fun createPopularMovies(act: Activity) {  
        val rv = act.findViewById<RecyclerView>(R.id.recyclerview1)  
  
            rv.layoutManager = LinearLayoutManager(  
                this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
            val adapter = MoviesAdapter(listOf(), act)  
            rv.adapter = adapter  
  
            Repository.getPopularMovies(  
                onSuccess = { movies ->  
 adapter.updateMovies(movies)  
                    Log.d("MainActivity", "Movies: $movies")  
                },  
 onError = {  
 Toast.makeText(  
                        this,  
 "ERROR: MoviesRepository.getPopularMovies" ,  
 Toast.LENGTH_SHORT).show()  
                }  
 )  
  
        }  
  
  
    // TOP RATED MOVIES  
 private fun createTopRatedMovies(act: Activity) {  
        val rv = act.findViewById<RecyclerView>(R.id.recyclerview2)  
  
        rv.layoutManager = LinearLayoutManager(  
            this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
        val adapter = MoviesAdapter(listOf(), act)  
        rv.adapter = adapter  
  
        Repository.getTopRatedMovies(  
            onSuccess = { movies ->  
 adapter.updateMovies(movies)  
                Log.d("MainActivity", "Movies: $movies")  
            },  
 onError = {  
 Toast.makeText(  
                    this,  
 "ERROR: MoviesRepository.getTopRatedMovies" ,  
 Toast.LENGTH_SHORT).show()  
            }  
 )  
  
    }  
  
    // TOP RATED MOVIES  
 private fun createUpComingMovies(act: Activity) {  
        val rv = act.findViewById<RecyclerView>(R.id.recyclerview3)  
  
        rv.layoutManager = LinearLayoutManager(  
            this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
        val adapter = MoviesAdapter(listOf(), act)  
        rv.adapter = adapter  
  
        Repository.getUpcomingMovies(  
            onSuccess = { movies ->  
 adapter.updateMovies(movies)  
                Log.d("MainActivity", "Movies: $movies")  
            },  
 onError = {  
 Toast.makeText(  
                    this,  
 "ERROR: MoviesRepository.getUpcomingMovies" ,  
 Toast.LENGTH_SHORT).show()  
            }  
 )  
  
    }  
  
 // ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++ // TV SHOWS private fun createPopularTV(act: Activity) {  
        val rv = act.findViewById<RecyclerView>(R.id.recyclerview4)  
  
        rv.layoutManager = LinearLayoutManager(  
                this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
            val adapter = TVAdapter(listOf(), act)  
        rv.adapter = adapter  
  
            Repository.getPopularTV(  
                onSuccess = { tv ->  
 adapter.updateTV(tv)  
                    Log.d("MainActivity", "TV: $tv")  
                },  
 onError = {  
 Toast.makeText(  
                        this,  
 "ERROR: MoviesRepository.getPopularTV",  
 Toast.LENGTH_SHORT).show()  
                }  
 )  
    }  
  
    private fun createTopRatedTV(act: Activity) {  
        val rv = act.findViewById<RecyclerView>(R.id.recyclerview5)  
  
        rv.layoutManager = LinearLayoutManager(  
            this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
        val adapter = TVAdapter(listOf(), act)  
        rv.adapter = adapter  
  
        Repository.getTopRatedTV(  
            onSuccess = { tv ->  
 adapter.updateTV(tv)  
                Log.d("MainActivity", "TV: $tv")  
            },  
 onError = {  
 Toast.makeText(  
                    this,  
 "ERROR: MoviesRepository.getTopRatedTV",  
 Toast.LENGTH_SHORT).show()  
            }  
 )  
    }  
  
    private fun createUpcomingTV(act: Activity) {  
        val rv = act.findViewById<RecyclerView>(R.id.recyclerview6)  
  
        rv.layoutManager = LinearLayoutManager(  
            this,  
 LinearLayoutManager.HORIZONTAL,  
 false )  
  
        val adapter = TVAdapter(listOf(), act)  
        rv.adapter = adapter  
  
        //MoviesRepository.getUpcomingTV(  
 Repository.getPopularTV(  
            onSuccess = { tv ->  
 adapter.updateTV(tv)  
                Log.d("MainActivity", "TV: $tv")  
            },  
 onError = {  
 Toast.makeText(  
                    this,  
 "ERROR: MoviesRepository.getUpcomingTV",  
 Toast.LENGTH_SHORT).show()  
            }  
 )  
    }  
  
 // TITLES private fun createTitles (act: Activity) {  
        // TITLES  
 val tv1 = act.findViewById<TextView>(R.id.title1)  
        val tv2 = act.findViewById<TextView>(R.id.title2)  
        val tv3 = act.findViewById<TextView>(R.id.title3)  
        val tv4 = act.findViewById<TextView>(R.id.title4)  
        val tv5 = act.findViewById<TextView>(R.id.title5)  
        val tv6 = act.findViewById<TextView>(R.id.title6)  
  
        val tvArray = arrayOf<TextView>(tv1, tv2, tv3, tv4, tv5, tv6)  
        val tvStr = arrayOf(  
             "Películas favoritas",  
			 "Películas recomendadas",  
			 "Películas mejor calificadas",  
			 "TV Show favoritos",  
			 "TV Show recomendados",  
			 "TV Show mejor calificados"  
			 )  
  
        for (i in 0 .. 5) {  
            tvArray[i].text = tvStr[i]  
        }  
    }  
  
}

```

## MainActivity.ky
1. Inicializar la aplicación desde el main mediante los métodos de verificación de internet (El cual realiza la lógica de lanzar el Recycler Online o el Recycler Online), y el método para otorgar los permisos de internet.

```kotlin
class MainActivity : AppCompatActivity() {  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        setContentView(R.layout.activity_main)  
  
        VerifyEthernet().verifyEthertet(this)  
        Permissions().checkPermission(this)  
    }  
}
```


## Verificación de funcionamiento

![[verificacion1.png]]


![[Verificación2.jpg]]

![[Verificacion3.jpg]]
