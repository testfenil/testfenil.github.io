# testfenil.github.io

// onbackPress extention updated onbackPress

     fun AppCompatActivity.handleBackPress(onBackPressed: () -> Unit) {
           onBackPressedDispatcher.addCallback(this, object : OnBackPressedCallback(true) {
               override fun handleOnBackPressed() {
                   onBackPressed()
               }
           })
       }
       
// BaseAct

    abstract class BaseAct<T : ViewBinding> : AppCompatActivity() {
        lateinit var bind: T
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            bind = getActivityBinding(layoutInflater)
            setContentView(bind.root)
            initUI()
        }
    
        abstract fun getActivityBinding(inflater: LayoutInflater): T
    
        abstract fun initUI()
    
        fun <T> T.tos() = Toast.makeText(this@BaseAct, "$this", Toast.LENGTH_SHORT).show()
        fun <T> T.tosL() = Toast.makeText(this@BaseAct, "$this", Toast.LENGTH_LONG).show()
    }


// ColorComination Task Logical Task ColorCombination

    data class ColorItems(var text: String, var color: Int)
    class ColorComboAct : BaseAct<ActivityColocComboBinding>() {
        private val GRID_SIZE = 5
        private var adapter: ColorComboMatrixAdapter? = null
        private var matrix: ArrayList<ColorItems> = arrayListOf()
        private var colorlist: ArrayList<Int> = arrayListOf()
        override fun getActivityBinding(inflater: LayoutInflater) =
            ActivityColocComboBinding.inflate(layoutInflater)
    
        override fun initUI() {
    
            bind.btncolorid.onClick {
                ("Clicik").log()
                adapter!!.findLargestGroupAndUpdate()
            }
    
            matrix = ArrayList()
    
            colorlist.add(Color.RED)
            colorlist.add(Color.BLACK)
            colorlist.add(Color.GREEN)
            colorlist.add(Color.YELLOW)
            colorlist.add(Color.BLUE)
    
            for (i in 0 until GRID_SIZE * GRID_SIZE) {
                matrix.add(
                    ColorItems(
                        (i + 1).toString(),
                        colorlist[(0 until colorlist.size).random()]
                    )
                ) // Fill with numbers 1-25
            }
            val itemCount = GRID_SIZE * GRID_SIZE
            val gridSize = sqrt(itemCount.toDouble()).toInt()
    
            val layoutManager = GridLayoutManager(this, gridSize)
            bind.recyclerView.setLayoutManager(layoutManager)
    
            // Initialize the adapter
    
            // Initialize the adapter
            adapter = ColorComboMatrixAdapter(matrix, gridSize)
            bind.recyclerView.setAdapter(adapter)
        }
    }

    -------   ColorComboMatrixAdapter.kt-----
        
    class ColorComboMatrixAdapter(
        private val matrix: ArrayList<ColorItems>,
        private var gridSize: Int
    ) : RecyclerView.Adapter<ColorComboMatrixAdapter.ViewHolder>() {
    
        private var largestGroup: List<Int> = emptyList()
    
        override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
            val view: View =
                LayoutInflater.from(parent.context).inflate(R.layout.item_cell, parent, false)
            return ViewHolder(view)
        }
    
        override fun onBindViewHolder(holder: ViewHolder, position: Int) {
            val item = matrix[position]
            holder.textView.text = item.text
    
            // Set the background color based on whether it's in the largest group
            if (largestGroup.contains(position)) {
                holder.textView.setBackgroundColor(Color.MAGENTA) // Pink for the largest group
            } else {
                holder.textView.setBackgroundColor(item.color)
            }
    
            holder.textView.setTextColor(Color.WHITE)
        }
    
        override fun getItemCount(): Int {
            return matrix.size
        }
    
        class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
            var textView: TextView = itemView.findViewById(R.id.textView)
        }
    
        // Function to find the largest group of adjacent same-colored items
        fun findLargestGroupAndUpdate() {
            val visited = BooleanArray(matrix.size) { false }
            var maxGroupSize = 0
            var maxGroup: List<Int> = emptyList()
    
            for (i in matrix.indices) {
                if (!visited[i]) {
                    val group = mutableListOf<Int>()
                    dfs(i, visited, group, matrix[i].color)
    
                    if (group.size > maxGroupSize) {
                        maxGroupSize = group.size
                        maxGroup = group
                    }
                }
            }
    
            largestGroup = maxGroup
            notifyDataSetChanged()
        }
    
        // Depth First Search (DFS) to find adjacent items of the same color
        private fun dfs(index: Int, visited: BooleanArray, group: MutableList<Int>, color: Int) {
            if (index < 0 || index >= matrix.size || visited[index] || matrix[index].color != color) {
                return
            }
    
            visited[index] = true
            group.add(index)
    
            val row = index / gridSize
            val col = index % gridSize
    
            // Check adjacent cells
            if (col > 0) dfs(index - 1, visited, group, color) // Left
            if (col < gridSize - 1) dfs(index + 1, visited, group, color) // Right
            if (row > 0) dfs(index - gridSize, visited, group, color) // Up
            if (row < gridSize - 1) dfs(index + gridSize, visited, group, color) // Down
        }

      ------- item_cell.xml ------
      
          <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="2dp"
        android:gravity="center"
        android:orientation="vertical">
    
        <TextView
            android:id="@+id/textView"
            android:layout_width="50dp"
            android:layout_height="50dp"
            android:background="#FFFFFF"
            android:gravity="center"
            android:textColor="#000000"
            android:textSize="18sp"
            android:textStyle="bold" />
    </LinearLayout>


// Custom Popupwindow dialog popup dialog popupmenu
                        
                         //Demo Api api call apidemo demoapi
                           https://d2is1ss4hhk4uk.cloudfront.net/contct.json
                            implementation("com.github.kakajika:RelativePopupWindow:0.4.1")
                        
                            
                           class CustomPopup(
                               private val activity: Activity, private val isTouchableOutside: Boolean = true, // Whether the popup can be dismissed by touching outside
                               private val isFocusable: Boolean = true, var onClick: (String) -> Unit
                           ) {
                               private var popupWindow: RelativePopupWindow? = null
                               var binding: PopupdialogBinding = PopupdialogBinding.inflate(LayoutInflater.from(activity))
                           
                               fun show(anchorView: View, xOffsetDp: Int = 0, yOffsetDp: Int = 0) {
                                   if (popupWindow == null) {
                           
                                       popupWindow = RelativePopupWindow(
                                           binding.root, ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT, isFocusable
                                       ).apply {
                                           isOutsideTouchable = isTouchableOutside
                                           elevation = TypedValue.applyDimension(
                                               TypedValue.COMPLEX_UNIT_DIP, 30f, activity.resources.displayMetrics
                                           )
                                       }
                                   }
                           
                                   val xOffsetPx = TypedValue.applyDimension(
                                       TypedValue.COMPLEX_UNIT_DIP, xOffsetDp.toFloat(), activity.resources.displayMetrics
                                   ).toInt()
                                   val yOffsetPx = TypedValue.applyDimension(
                                       TypedValue.COMPLEX_UNIT_DIP, yOffsetDp.toFloat(), activity.resources.displayMetrics
                                   ).toInt()
                           
                           //        popupWindow?.showAsDropDown(anchorView, xOffsetPx, yOffsetPx)
                           
                                   popupWindow?.showOnAnchor(
                                       anchorView, RelativePopupWindow.VerticalPosition.ABOVE, RelativePopupWindow.HorizontalPosition.RIGHT, xOffsetDp, yOffsetDp, true
                                   )
                           
                                   binding.apply {
                           
                                       propertiesid.onClick {
                                           onClick.invoke("Properties")
                                           popupWindow!!.dismiss()
                                       }
                                   }
                               }
                           
                               fun dismiss() {
                                   popupWindow?.dismiss()
                               }
                           }
                        
                            binding.moremultiid.onClick {
                                val customPopup = CustomPopup(this, onClick = {
                                    when (it) {
                                    }
                                })
                                customPopup.show(binding.moremultiid, 0, 0)
                            }
                            
                        
                           ------ Xml
                           <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_centerInParent="true"
                        android:layout_margin="@dimen/_12sdp"
                        android:clipChildren="false"
                        android:elevation="@dimen/_10sdp"
                        android:clipToPadding="false">
                        
                        <LinearLayout
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:layout_gravity="center"
                            android:layout_marginEnd="@dimen/_12sdp"
                            android:layout_marginBottom="@dimen/_5sdp"
                            android:background="@drawable/rounded_shape"
                            android:backgroundTint="@color/white"
                            android:gravity="center"
                            android:elevation="@dimen/_10sdp"
                            android:orientation="vertical"
                            android:paddingStart="@dimen/_10sdp"
                            android:paddingTop="@dimen/_5sdp"
                            android:paddingEnd="@dimen/_10sdp"
                            android:paddingBottom="@dimen/_5sdp">
                        
                            <TextView
                                android:id="@+id/propertiesid"
                                android:layout_width="match_parent"
                                android:layout_height="wrap_content"
                                android:padding="@dimen/_7sdp"
                                android:background="?selectableItemBackground"
                                android:text="Properties"
                                android:textColor="@color/black"
                                android:textSize="@dimen/_12sdp" />
                        
                                </LinearLayout?
                                </RelativeLayout>
                                
// DaggerHilt call api demo
            
        id("com.google.dagger.hilt.android") version "2.51.1" apply false
        
          id("kotlin-kapt")
        id("com.google.dagger.hilt.android")
        
         buildConfigField("String", "BASE_URL", "\"https://det/\"")
           
              buildFeatures {
                   viewBinding = true
                   buildConfig = true
               }
               
             //Dagger Hilt
               implementation("com.google.dagger:hilt-android:2.51.1")
               kapt("com.google.dagger:hilt-compiler:2.51.1")
           
               //Coroutine Scop
               implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.8.7")
               implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.3")
               implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
               implementation("androidx.fragment:fragment-ktx:1.6.2")
           
           @HiltAndroidApp
           class MyApplicationClass : Application() {
               override fun onCreate() {
                   super.onCreate()
               }
           }
        
         
                @AndroidEntryPoint
           class DaggerHiltAct : BaseAct<ActivityDaggerHiltBinding>() {
           //    private val contactadapter = ContactAdapter()
               private val viewModel: YourViewModel by viewModels()
           
               override fun getActivityBinding(inflater: LayoutInflater) =
                   ActivityDaggerHiltBinding.inflate(layoutInflater)
           
               override fun initUI() {
                   lifecycleScope.launch {
                       viewModel.data.collectLatest { data ->
                           // Update your UI with the fetched data
           //                contactadapter.submitList(data)
                       }
                   }
           
                   lifecycleScope.launch {
                       viewModel.error.collectLatest { error ->
                           error?.let {
                               // Display a Toast or update an error view
                               ("Error: $it").log()
                               Toast.makeText(this@DaggerHiltAct, "Error: $it", Toast.LENGTH_SHORT).show()
                           }
                       }
                   }
           
                   viewModel.fetchData()
           
                   bind.apply {
           //            rvid.adapter = contactadapter
                   }
               }
           }
        
           //DaggerService.kt
                  
                 
        interface ApiService {
            @GET("contct.json")
            suspend fun getYourData(): Response<ArrayList<ContactResModelItem>>
        }
        
        @Module
        @InstallIn(SingletonComponent::class)
        object NetworkModule {
        
            @Provides
            @Singleton
            fun provideLoggingInterceptor(): Interceptor {
                return Interceptor { chain ->
                    val request: Request = chain.request()
                    val requestBody = request.body
                    val buffer = okio.Buffer()
                    requestBody?.writeTo(buffer)
                    val requestBodyString = buffer.readUtf8()
        
                    println("Request URL: ${request.url}")
                    println("Request Method: ${request.method}")
                    println("Request Headers: ${request.headers}")
                    println("Request Body: $requestBodyString")
        
                    chain.proceed(request)
                }
            }
        
            @Provides
            @Singleton
            fun provideHttpLoggingInterceptor(): HttpLoggingInterceptor {
                val logger = HttpLoggingInterceptor()
                logger.level = HttpLoggingInterceptor.Level.BODY
                return logger
            }
        
            @Provides
            @Singleton
            fun provideOkHttpClient(
                loggingInterceptor: Interceptor,
                httpLoggingInterceptor: HttpLoggingInterceptor
            ): OkHttpClient {
                return OkHttpClient.Builder()
                    .addInterceptor(loggingInterceptor)
                    .addInterceptor(httpLoggingInterceptor)
                    .build()
            }
        
            @Provides
            @Singleton
            fun provideRetrofit(okHttpClient: OkHttpClient): Retrofit {
                return Retrofit.Builder()
                    .baseUrl(com.example.testvs.BuildConfig.BASE_URL) // Using buildConfigField
                    .client(okHttpClient)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build()
            }
        
            @Provides
            @Singleton
            fun provideApiService(retrofit: Retrofit): ApiService {
                return retrofit.create(ApiService::class.java)
            }
        }

           
           @HiltViewModel
           class YourViewModel @Inject constructor(private val repository: YourRepository) : ViewModel() {
               // StateFlow for holding data (non-nullable)
               private val _data = MutableStateFlow<ArrayList<ContactResItem>>(arrayListOf())
               val data: StateFlow<ArrayList<ContactResItem>> get() = _data
           
               // StateFlow for handling errors
               private val _error = MutableStateFlow<String?>(null)
               val error: StateFlow<String?> get() = _error
           
               fun fetchData() {
                   viewModelScope.launch {
                       try {
                           val response = repository.fetchData() // Fetch data
                           _data.value = response // Update state with new data
                       } catch (e: Exception) {
                           _error.value = "Exception: ${e.message}" // Update state with error message
                       }
                   }
               }
           }


// Ai Image Genrater Api
        
        https://lexica.art/api/v1/search?q=Lifestyle Tools
        
        Api Chat BOT AI
        @POST
        Api ---> https://openai-cookbook-node.vercel.app/api/generate-answer
        Body x-www-from-urlencoded --> question: What is Android
        
        Api Chat BOT AI
        @POST
        http://keyword.saetatech.com/api/chat/word
        raw body: content: "what is android"



// Pick a image from camera opencamera camera image pick camera pick camera image      
        
        private val CAMERA_REQUEST_CODE = 100
          
            <provider
                      android:name="androidx.core.content.FileProvider"
                      android:authorities="${applicationId}.fileprovider"
                      android:enabled="true"
                      android:exported="false"
                      android:grantUriPermissions="true">
                      <meta-data
                          android:name="android.support.FILE_PROVIDER_PATHS"
                          android:resource="@xml/file_paths" />
                  </provider>
                  
        fun openCamera() {
            val intent = Intent(MediaStore.ACTION_IMAGE_CAPTURE)
            val photoFile = createImageFile()
            imageUri = FileProvider.getUriForFile(
                this, "${packageName}.fileprovider", photoFile
            )
            intent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri)
            startActivityForResult(intent, CAMERA_REQUEST_CODE)
        }
        
                onActivityResult
               if (requestCode == CAMERA_REQUEST_CODE && resultCode == RESULT_OK) {
                      try {
                          // Decode the captured image to a Bitmap
                          val bitmap = MediaStore.Images.Media.getBitmap(contentResolver, imageUri)
                          ("FAT: Bitmap HGEre: " + imageUri).log()
                          (imageUri.toString()).startUcrop(this@MainMainActivity)
          //                extractTextWithFirebase(bitmap)
                      } catch (e: IOException) {
                          e.message?.log()
                      }
                  }

// Google ad error addmob lib errror ad issues ad error
    
      in menifest
     <property
                android:name="android.adservices.AD_SERVICES_CONFIG"
                android:resource="@xml/ga_ad_services_config"
                tools:replace="android:resource" />

// Synonyms

      https://www.wordsapi.com/mashape/words/apple/synonyms?when=2024-12-03T05:22:20.276Z&encrypted=8cfdb18ee723919bea9207bfea58bcbbaeb22d0932f891b8
    
      // Dictionary API
    
      https://www.wordsapi.com/mashape/words/Hell/definitions?when=2024-12-03T05:22:20.276Z&encrypted=8cfdb18ee723919bea9207bfea58bcbbaeb22d0932f891b8
    
      //paraPhraseApi
    
      http://147.182.230.132:51230/generate-reply/?option=8&user_input=abc
    
      // ton changer
    
      http://147.182.230.132:51230/generate-reply/?option=1&user_input=my
    
      // checkGrammerURL
    
      http://147.182.230.132:33230/gram_spell_checker


// Pivot number logic optimum logical task logical programm

    class Main {
        public static void main(String[] args) {
            System.out.println("Try programiz.pro");
            
            int start = 1;
            int end = 9;
            int pivot = 5;
            
            int temp = pivot;
            
            for(int i=start;i<=end;i++){
                if(pivot >= i){
                    System.out.print(", " + temp);
                    temp--;
                }
                else{
                    pivot++;
                    System.out.print(", " + pivot);
                   
                }
            }
            
        }
    }
    
// Image to text get text from image firebaseml kit image to text
    
    <uses-permission android:name="android.permission.INTERNET" />
    
       <meta-data
                android:name="com.google.firebase.ml.vision.DEPENDENCIES"
                android:value="ocr" />
                
    
        implementation(platform("com.google.firebase:firebase-bom:33.6.0"))
        implementation("com.google.firebase:firebase-ml-vision:24.0.3")
    
    
        val bitmap = BitmapFactory.decodeResource(
            resources, R.drawable.testimg
        ) // Replace with your image
        extractTextWithFirebase(bitmap)
        }
    
        private fun extractTextWithFirebase(bitmap: Bitmap) {
            try {
                val image = FirebaseVisionImage.fromBitmap(bitmap)
                val recognizer = FirebaseVision.getInstance().onDeviceTextRecognizer
    
                recognizer.processImage(image)
                    .addOnSuccessListener { firebaseVisionText: FirebaseVisionText ->
                        // Extracted text
                        val recognizedText = firebaseVisionText.text
                        Log.d("TextRecognition", "Recognized Text: $recognizedText")
                    }.addOnFailureListener { e: Exception? ->
                        // Handle failure
                        Log.e("TextRecognition", "Text recognition failed", e)
                    }
            } catch (e: Exception) {
                Log.e("TextRecognition", "Error processing image", e)
            }
        }
    // Google STT TTS
    
        private val SPEECH_REQUEST_CODE = 1
        
     if (ContextCompat.checkSelfPermission(
                    this, Manifest.permission.RECORD_AUDIO
                ) != PackageManager.PERMISSION_GRANTED
            ) {
                ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.RECORD_AUDIO), 100)
            }
    
            bind.apply {
                btnsttid.onClick {
                    startGoogleVoiceInput()
                }
            }
    
               private fun startGoogleVoiceInput() {
        val intent = Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH).apply {
            putExtra(
                RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM
            )
            putExtra(RecognizerIntent.EXTRA_LANGUAGE, "en-US") // Set desired language
            putExtra(RecognizerIntent.EXTRA_PROMPT, "Speak something...")
        }
    
        try {
            startActivityForResult(intent, SPEECH_REQUEST_CODE)
            } catch (e: ActivityNotFoundException) {
                Toast.makeText(
                    this, "Google Voice Input not supported on your device", Toast.LENGTH_SHORT
                ).show()
            }
        }
    
        override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
            super.onActivityResult(requestCode, resultCode, data)
    
            if (requestCode == SPEECH_REQUEST_CODE && resultCode == RESULT_OK && data != null) {
                val results = data.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS)
                val spokenText = results?.get(0) ?: ""
                ("Text: $spokenText").log()
    //            Toast.makeText(this, "You said: $spokenText", Toast.LENGTH_LONG).show()
            }
        }

// View Animation Rotate Icon and expandeable view left to right animation view and left to right animation
              
              public void rotateIcon(View icon) {
                  ObjectAnimator rotateAnimator = ObjectAnimator.ofFloat(icon, "rotation", 0f, 360f);
                  rotateAnimator.setDuration(1000); // Duration in milliseconds
                  rotateAnimator.setRepeatCount(0); // No repeat
                  rotateAnimator.start();
              }
              
              
                 private void animateHorizontalExpansion(final View view, int startWidth, int endWidth, long duration) {
                  ValueAnimator animator = ValueAnimator.ofInt(startWidth, endWidth);
                  animator.addUpdateListener(animation -> {
                      int animatedValue = (int) animation.getAnimatedValue();
                      ViewGroup.LayoutParams layoutParams = view.getLayoutParams();
                      layoutParams.width = animatedValue;
                      view.setLayoutParams(layoutParams);
                  });
              
                  animator.setDuration(duration);
                  animator.start();
              }
              
              private void openViewFromLeftToRight(View view, int targetWidth) {
                  view.setVisibility(View.VISIBLE);
                  animateHorizontalExpansion(view, 0, targetWidth, 800); // Expand from 0 to targetWidth
              }
              
              private void closeViewFromRightToLeft(View view) {
                  int currentWidth = view.getWidth();
                  animateHorizontalExpansion(view, currentWidth, 0, 800); // Collapse from current width to 0
              }
                
              View expandableView = layoutmoreoptionid;
                            int targetWidth = 1080; // Set your desired target width
                            openViewFromLeftToRight(expandableView, targetWidth);


// Huwaie lib image background remover huwaie lib

       <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission
            android:name="android.permission.READ_EXTERNAL_STORAGE"
            android:maxSdkVersion="32" />
        <uses-permission
            android:name="android.permission.WRITE_EXTERNAL_STORAGE"
            tools:ignore="ScopedStorage" />
            
    // Meni fest
          <meta-data
            android:name="com.huawei.agconnect.AccessNetwork"
            android:value="false" />
            
    app -> agconnect-services.json
    ADD SHA1 key in huwaie consol 

        classpath 'com.android.tools.build:gradle:8.0.2'
        classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:1.9.0'
        classpath 'com.huawei.agconnect:agcp:1.9.1.301'
     

          google()
        mavenCentral()
        gradlePluginPortal()
        jcenter() // Warning: this repository is going to shut down soon
        maven { url "https://jitpack.io" }
        maven { url 'https://developer.huawei.com/repo/' }
   
       gradl.property
       apmsInstrumentationEnabled=false

       distributionUrl=https\://services.gradle.org/distributions/gradle-8.0-all.zip

     compileSdk 34
     
        plugins {
            id 'com.huawei.agconnect'
        }

       implementation "org.jetbrains.kotlin:kotlin-stdlib:1.8.0"
        implementation 'com.huawei.agconnect:agconnect-core:1.9.1.301'
        implementation 'com.huawei.hms:ml-computer-vision-segmentation:3.4.0.300'
    // Import the multiclass segmentation model package.
        implementation 'com.huawei.hms:ml-computer-vision-image-segmentation-multiclass-model:3.4.0.300'
    // Import the human body segmentation model package.
        implementation 'com.huawei.hms:ml-computer-vision-image-segmentation-body-model:3.4.0.300'
            
        #Huwaie Lib
        
        -ignorewarnings
        -keep class com.huawei.agconnect.** {*;}
        -ignorewarnings
        -keep class com.huawei.agconnect.**{*;}
        
        -ignorewarnings
        -keepattributes *Annotation*
        -keepattributes Exceptions
        -keepattributes InnerClasses
        -keepattributes Signature
        -keepattributes SourceFile,LineNumberTable
        -keep class com.huawei.hianalytics.**{*;}
        -keep class com.huawei.updatesdk.**{*;}
        -keep class com.huawei.hms.**{*;}

        
         val imageUrl = "https://images.pexels.com/photos/4773066/pexels-photo-4773066.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1"
        
                // Load image as a Bitmap
                Glide.with(this)
                        .asBitmap()
                        .load(imageUrl)
                        .into(object : CustomTarget<Bitmap>() {
                            override fun onResourceReady(resource: Bitmap, transition: Transition<in Bitmap>?) {
                                bind.imgid.setImageBitmap(resource)
                                ("Origini nal").log()
                                imageSegmentation(resource)
                            }
        
                            override fun onLoadCleared(placeholder: Drawable?) {
                                // Handle cleanup if needed
                            }
                        })
    
                        private fun imageSegmentation(bitmap: Bitmap?) {
            if (bitmap == null) {
                Toast.makeText(this, "Something went wrong. Try again!", Toast.LENGTH_LONG).show()
                return
            }
            ("1212312").log()
    
            val setting =
                    MLImageSegmentationSetting.Factory()
                            .setAnalyzerType(MLImageSegmentationSetting.BODY_SEG)
                            .setExact(true)
                            .create()
            val analyzer = MLAnalyzerFactory.getInstance().getImageSegmentationAnalyzer(setting)
            val mlFrame = MLFrame.Creator().setBitmap(bitmap).create()
            val task = analyzer?.asyncAnalyseFrame(mlFrame)
            task?.addOnSuccessListener { mlImageSegmentationResults ->
                if (mlImageSegmentationResults != null) {
                    ("mlImageSegmentationResults").log()
                    removalBitmap = mlImageSegmentationResults.foreground
                    bind.imgid.setImageBitmap(removalBitmap)
                } else
                    Toast.makeText(this, "No human body is detected!", Toast.LENGTH_LONG).show()
            }?.addOnFailureListener {
                Toast.makeText(this, "No human body is detected!", Toast.LENGTH_LONG).show()
            }
        }


// Translater Api Free language transfer api

    https://translate.googleapis.com/translate_a/single?client=gtx&sl=auto&tl=de&dt=t&q=Hello World
       
       //cheack grammer 
        @Post 
        http://147.182.230.132:33230/gram_spell_checker
        from urlencoded
        direct_text: "I have no work".
       
       //ton changer
       @GET
       http://147.182.230.132:51230/generate-reply/?option=1&user_input=Hellow World this are great things
       
       //paraPhraseApi
       @GET
       http://147.182.230.132:51230/generate-reply/?option=8&user_input=My Name is a best

// Loti Animation video xml lotianimation view
    
     implementation(libs.lottie)
    lottie = "6.5.2"
    lottie = { module = "com.airbnb.android:lottie", version.ref = "lottie" }
    
     <com.airbnb.lottie.LottieAnimationView
            android:id="@+id/animationView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@+id/compassid"
            android:layout_centerHorizontal="true"
            android:padding="@dimen/_75sdp"
            app:lottie_autoPlay="true"
            app:lottie_colorFilter="@color/cardview_dark_background"
            app:lottie_loop="true"
            app:lottie_rawRes="@raw/loadingsplace" />

// QiblaCompassAct quibla compass qubla compass

        class QiblaCompassAct : com.digital.compass.direction.Activity.BaseAct<ActivityQiblaCompassBinding>(), SensorEventListener {
                override fun getActivityBinding(inflater: LayoutInflater) =
                    ActivityQiblaCompassBinding.inflate(layoutInflater)
            
                private var currentDegree = 0f
                private var qiblaDirection = 0f
            
                private lateinit var fusedLocationClient: FusedLocationProviderClient
                private lateinit var sensorManager: SensorManager
                private var lastAzimuth = 0f
                private var accelerometer: Sensor? = null
                private var magnetometer: Sensor? = null
                private var gravity = FloatArray(3)
                private var geomagnetic = FloatArray(3)
                private var azimuth = 0f
            
                private val startForResult =
                    registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->
                        if (result.resultCode == RESULT_OK) {
                            val data: Intent? = result.data
                            val selectedCompass = data?.getStringExtra("from")
                            bind.compassImage.setImageResource(getItemsList().find { it.id == selectedCompass }!!.item)
                        }
                    }
            
                @SuppressLint("DefaultLocale")
                private fun updateCompassUI(direction: Float) {
                    // Rotate the compass image
                    val rotateAnimation = android.view.animation.RotateAnimation(
                        currentDegree,
                        direction,
                        android.view.animation.Animation.RELATIVE_TO_SELF,
                        0.5f,
                        android.view.animation.Animation.RELATIVE_TO_SELF,
                        0.5f
                    )
                    rotateAnimation.duration = 500
                    rotateAnimation.fillAfter = true
            
            //        findViewById<ImageView>(R.id.compassImageView).startAnimation(rotateAnimation)
                    currentDegree = direction
            
                    // Update the direction text
                    val cardinalDirection = getCardinalDirection(direction)
                    bind.tvid.text = String.format("%.0f° %s", direction, cardinalDirection)
                }
            
                private fun getCardinalDirection(degree: Float): String {
                    return when {
                        degree >= 337.5 || degree < 22.5 -> "N"
                        degree >= 22.5 && degree < 67.5 -> "NE"
                        degree >= 67.5 && degree < 112.5 -> "E"
                        degree >= 112.5 && degree < 157.5 -> "SE"
                        degree >= 157.5 && degree < 202.5 -> "S"
                        degree >= 202.5 && degree < 247.5 -> "SW"
                        degree >= 247.5 && degree < 292.5 -> "W"
                        degree >= 292.5 && degree < 337.5 -> "NW"
                        else -> ""
                    }
                }
            
                override fun initUI() {
                    // Initialize the SensorManager and sensors
                    sensorManager = getSystemService(SENSOR_SERVICE) as SensorManager
                    accelerometer = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER)
                    magnetometer = sensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD)
            
            
                    bind.toolbarid.apply {
                        tvtoolname.text =
                            getString(R.string.qibla_compass)
            
                        backid.onClick {
                            finish()
                        }
                    }
            
                    fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
            
                    bind.apply {
                        typeid.onClick {
                            startForResult.launch(
                                Intent(
                                    this@QiblaCompassAct, CompassSelectionAct::class.java
                                )
                            )
                        }
                    }
            
                    bind.tvid.visibility = View.VISIBLE
            
                    requestLocationPermission()
                }
            
                @SuppressLint("SetTextI18n")
                private fun displayDirection(azimuth: Float) {
                    val roundedAzimuth = azimuth.roundToInt()
                    val direction = when (roundedAzimuth) {
                        in 0..22, in 338..360 -> "N"
                        in 23..67 -> "NE"
                        in 68..112 -> "E"
                        in 113..157 -> "SE"
                        in 158..202 -> "S"
                        in 203..247 -> "SW"
                        in 248..292 -> "W"
                        in 293..337 -> "NW"
                        else -> "N" // Default case, though not typically needed
                    }
                    lastAzimuth = azimuth
                }
            
            
                private fun getLocation() {
                    if (ActivityCompat.checkSelfPermission(
                            this, ACCESS_FINE_LOCATION
                        ) != PackageManager.PERMISSION_GRANTED && ActivityCompat.checkSelfPermission(
                            this, ACCESS_COARSE_LOCATION
                        ) != PackageManager.PERMISSION_GRANTED
                    ) {
                        return
                    }
            
                    fusedLocationClient.lastLocation.addOnSuccessListener { location: Location? ->
                        location!!.latitude.toString().log()
                        displayLocationInfo(location)
                    }
                }
            
                private fun displayLocationInfo(location: Location) {
                    val geocoder = Geocoder(this, Locale.getDefault())
                    val addresses = geocoder.getFromLocation(location.latitude, location.longitude, 1)
                    addresses?.firstOrNull()?.let {
                        val city = it.locality ?: "Unknown city"
                        val country = it.countryName ?: "Unknown country"
                        ("Location: $city, $country").log()
                        bind.tvlocationclickid.text = "$city, $country"
                    }
                }
            
                private fun requestLocationPermission() {
                    val locationPermissionRequest = registerForActivityResult(
                        ActivityResultContracts.RequestPermission()
                    ) { isGranted ->
                        if (isGranted) {
                            getLocation()
                        } else {
                        }
                    }
                    locationPermissionRequest.launch(ACCESS_FINE_LOCATION)
                }
            
                override fun onResume() {
                    super.onResume()
                    accelerometer?.also {
                        sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_UI)
                    }
                    magnetometer?.also {
                        sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_UI)
                    }
                }
            
                override fun onPause() {
                    super.onPause()
                    sensorManager.unregisterListener(this)
                }
            
                override fun onSensorChanged(event: SensorEvent) {
            
                    if (event == null) return
            
                    when (event.sensor.type) {
                        Sensor.TYPE_ACCELEROMETER -> gravity = event.values
                        Sensor.TYPE_MAGNETIC_FIELD -> geomagnetic = event.values
                    }
            
                    if (gravity != null && geomagnetic != null) {
                        val rotationMatrix = FloatArray(9)
                        val inclinationMatrix = FloatArray(9)
                        if (SensorManager.getRotationMatrix(
                                rotationMatrix, inclinationMatrix, gravity, geomagnetic
                            )
                        ) {
                            val orientation = FloatArray(3)
                            SensorManager.getOrientation(rotationMatrix, orientation)
            
                            val azimuth = Math.toDegrees(orientation[0].toDouble()).toFloat()
                            val adjustedAzimuth = (azimuth + 360) % 360
            
                            // Calculate the difference between azimuth and Qibla direction
                            val directionToQibla = (qiblaDirection - adjustedAzimuth + 360) % 360
            
                            // Update UI (e.g., rotate a compass image)
                            updateCompassUI(directionToQibla)
                        }
                    }
            
                    when (event.sensor.type) {
                        Sensor.TYPE_ACCELEROMETER -> {
                            gravity[0] = event.values[0]
                            gravity[1] = event.values[1]
                            gravity[2] = event.values[2]
                        }
            
                        Sensor.TYPE_MAGNETIC_FIELD -> {
                            geomagnetic[0] = event.values[0]
                            geomagnetic[1] = event.values[1]
                            geomagnetic[2] = event.values[2]
                        }
                    }
            
                    val R = FloatArray(9)
                    val I = FloatArray(9)
                    if (SensorManager.getRotationMatrix(R, I, gravity, geomagnetic)) {
                        val orientation = FloatArray(3)
                        SensorManager.getOrientation(R, orientation)
                        val newAzimuth = Math.toDegrees(orientation[0].toDouble()).toFloat()
            
                        // Rotate compass image
                        bind.compassImage.rotation = -newAzimuth
                        azimuth = newAzimuth
                    }
            
                    event?.let {
                        when (it.sensor.type) {
                            Sensor.TYPE_ACCELEROMETER -> {
                                System.arraycopy(
                                    it.values, 0, accelerometerReading, 0, accelerometerReading.size
                                )
                            }
            
                            Sensor.TYPE_MAGNETIC_FIELD -> {
                                System.arraycopy(it.values, 0, magnetometerReading, 0, magnetometerReading.size)
                            }
                        }
            
                        if (SensorManager.getRotationMatrix(
                                rotationMatrix, null, accelerometerReading, magnetometerReading
                            )
                        ) {
                            SensorManager.getOrientation(rotationMatrix, orientationAngles)
                            val azimuthInDegrees = Math.toDegrees(orientationAngles[0].toDouble()).toFloat()
                            displayDirection(azimuthInDegrees)
                        }
                    }
                }
            
                private var accelerometerReading = FloatArray(3)
                private var magnetometerReading = FloatArray(3)
                private var rotationMatrix = FloatArray(9)
                private var orientationAngles = FloatArray(3)
            
                override fun onAccuracyChanged(sensor: Sensor, accuracy: Int) {
                    // We won’t be using this method here
                }
            }

// BuildConfig is not show

    // gradle.properties
        android.defaults.buildfeatures.buildconfig=true
            clearn Project -> Rebuild Project
            versionid.text = "Version ${BuildConfig.VERSION_NAME}"
        
          buildFeatures {
                    viewBinding = true
                    buildConfig = true
                }

// statusbar hide & hide statusbar statusbar color
              
         <style name="Base.Theme.CompassApp" parent="Theme.Material3.Light.NoActionBar">
                <!-- Customize your light theme here. -->
                <!-- <item name="colorPrimary">@color/my_light_primary</item> -->
                <item name="android:statusBarColor">#FFFFFF</item>
                <item name="android:windowLightStatusBar">true</item>
                <item name="backgroundColor">#FFFFFF</item>
                <item name="android:backdropColor" tools:ignore="NewApi">#FFFFFF</item>
                <item name="android:background">#FFFFFF</item>
            </style>
        
            <style name="Theme.CompassApp" parent="Base.Theme.CompassApp" />
        
        
                    applyStatusBarColor()
            
            private fun applyStatusBarColor() {
                    val isDarkMode = AppCompatDelegate.getDefaultNightMode() == AppCompatDelegate.MODE_NIGHT_YES
            
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
                        // For Android 11 (API 30+) and above
                        val controller = window.insetsController
                        if (controller != null) {
                            if (isDarkMode) {
                                // White text/icons for dark mode
                                controller.setSystemBarsAppearance(
                                    0, WindowInsetsController.APPEARANCE_LIGHT_STATUS_BARS
                                )
                            } else {
                                // Black text/icons for light mode
                                controller.setSystemBarsAppearance(
                                    WindowInsetsController.APPEARANCE_LIGHT_STATUS_BARS,
                                    WindowInsetsController.APPEARANCE_LIGHT_STATUS_BARS
                                )
                            }
                        }
                    } else {
                        // For Android 6.0 (API 23) to Android 10 (API 29)
                        @Suppress("DEPRECATION") if (isDarkMode) {
                            window.decorView.systemUiVisibility = 0 // Clear light status bar flag
                        } else {
                            window.decorView.systemUiVisibility =
                                View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR // Add light status bar flag
                        }
                    }
                }

                
// Google map library googlemap lib locationmap
    
         osmdroidAndroid = "6.1.10"
            
            osmdroid-android = { module = "org.osmdroid:osmdroid-android", version.ref = "osmdroidAndroid" }
            
            
                implementation(libs.osmdroid.android)
            
                play-services-location = { module = "com.google.android.gms:play-services-location", version.ref = "playServicesLocation" }
            playServicesLocation = "21.3.0"
        
        
            <org.osmdroid.views.MapView
                android:id="@+id/osmMapView"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
        
        
                class MapViewActivity : BaseAct<ActivityMapViewBinding>() {
            private lateinit var mapView: MapView
            private lateinit var fusedLocationClient: FusedLocationProviderClient
        
            override fun getActivityBinding(inflater: LayoutInflater) =
                ActivityMapViewBinding.inflate(layoutInflater)
        
            @SuppressLint("SetTextI18n")
            override fun initUI() {
                bind.toolid.apply {
                    backid.onClick {
                        finish()
                    }
                    tvtoolname.text = "Map View"
                }
        
                Configuration.getInstance().load(
                    applicationContext,
                    applicationContext.getSharedPreferences("osm_prefs", MODE_PRIVATE)
                )
        
                // Initialize MapView and LocationClient
                mapView = findViewById(R.id.osmMapView)
                mapView.setMultiTouchControls(true)
                fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
                // Request location permission and get current location
                requestLocationPermission()
            }
        
            private fun requestLocationPermission() {
                if (ActivityCompat.checkSelfPermission(
                        this,
                        Manifest.permission.ACCESS_FINE_LOCATION
                    ) != PackageManager.PERMISSION_GRANTED
                ) {
                    ActivityCompat.requestPermissions(
                        this,
                        arrayOf(Manifest.permission.ACCESS_FINE_LOCATION),
                        1
                    )
                } else {
                    getCurrentLocation()
                }
            }
        
            override fun onRequestPermissionsResult(
                requestCode: Int,
                permissions: Array<out String>,
                grantResults: IntArray
            ) {
                super.onRequestPermissionsResult(requestCode, permissions, grantResults)
                if (requestCode == 1 && grantResults.isNotEmpty() && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    getCurrentLocation()
                }
            }
        
            private fun getCurrentLocation() {
                if (ActivityCompat.checkSelfPermission(
                        this,
                        Manifest.permission.ACCESS_FINE_LOCATION
                    ) != PackageManager.PERMISSION_GRANTED
                ) {
                    return
                }
        
                fusedLocationClient.lastLocation.addOnSuccessListener { location: Location? ->
                    location?.let {
                        val currentGeoPoint = GeoPoint(it.latitude, it.longitude)
                        showLocationOnMap(currentGeoPoint)
                    }
                }
            }
        
            private fun showLocationOnMap(location: GeoPoint) {
                // Set map controller and zoom to the current location
                val mapController: IMapController = mapView.controller
                mapController.setZoom(15.0)
                mapController.setCenter(location)
        
                // Add a marker to indicate the current location
                val marker = Marker(mapView)
                marker.position = location
                marker.title = "You are here"
                marker.setAnchor(Marker.ANCHOR_CENTER, Marker.ANCHOR_BOTTOM)
                mapView.overlays.add(marker)
                mapView.invalidate()
            }
        
            override fun onResume() {
                super.onResume()
                mapView.onResume()
            }
        
            override fun onPause() {
                super.onPause()
                mapView.onPause()
            }
        
            override fun onDestroy() {
                super.onDestroy()
                mapView.onDetach()
            }
        }


// Compass Logic android compassview compass
    
    
      class QiblaCompassAct : BaseAct<ActivityQiblaCompassBinding>(), SensorEventListener {
            override fun getActivityBinding(inflater: LayoutInflater) =
                ActivityQiblaCompassBinding.inflate(layoutInflater)
        
            private lateinit var fusedLocationClient: FusedLocationProviderClient
            private lateinit var sensorManager: SensorManager
            private var lastAzimuth = 0f
        
            private var accelerometer: Sensor? = null
            private var magnetometer: Sensor? = null
            private val gravity = FloatArray(3)
            private val geomagnetic = FloatArray(3)
            private var azimuth = 0f
        
            override fun initUI() {
                // Initialize the SensorManager and sensors
                sensorManager = getSystemService(SENSOR_SERVICE) as SensorManager
                accelerometer = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER)
                magnetometer = sensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD)
        
                bind.toolbarid.apply {
                    tvtoolname.text = "Qibla compass"
                    backid.onClick {
                        finish()
                    }
                }
        
                fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
        
                bind.apply {
                    typeid.onClick {
        
                    }
                }
        
                requestLocationPermission()
            }
        
            @SuppressLint("SetTextI18n")
            private fun displayDirection(azimuth: Float) {
                val roundedAzimuth = azimuth.roundToInt()
                val direction = when (roundedAzimuth) {
                    in 0..22, in 338..360 -> "N"
                    in 23..67 -> "NE"
                    in 68..112 -> "E"
                    in 113..157 -> "SE"
                    in 158..202 -> "S"
                    in 203..247 -> "SW"
                    in 248..292 -> "W"
                    in 293..337 -> "NW"
                    else -> "N" // Default case, though not typically needed
                }
                bind.tvid.text = "$roundedAzimuth° - $direction"
                lastAzimuth = azimuth
            }
        
        
            private fun getLocation() {
                if (ActivityCompat.checkSelfPermission(
                        this,
                        ACCESS_FINE_LOCATION
                    ) != PackageManager.PERMISSION_GRANTED && ActivityCompat.checkSelfPermission(
                        this,
                        ACCESS_COARSE_LOCATION
                    ) != PackageManager.PERMISSION_GRANTED
                ) {
                    return
                }
        
                fusedLocationClient.lastLocation.addOnSuccessListener { location: Location? ->
                    location!!.latitude.toString().log()
                    location?.let {
                        displayLocationInfo(it)
                    }
                }
            }
        
            private fun displayLocationInfo(location: Location) {
                val geocoder = Geocoder(this, Locale.getDefault())
                val addresses = geocoder.getFromLocation(location.latitude, location.longitude, 1)
                addresses?.firstOrNull()?.let {
                    val city = it.locality ?: "Unknown city"
                    val country = it.countryName ?: "Unknown country"
                    ("Location: $city, $country").log()
                    bind.tvlocationclickid.text = "$city, $country"
                }
            }
        
            private fun requestLocationPermission() {
                val locationPermissionRequest = registerForActivityResult(
                    ActivityResultContracts.RequestPermission()
                ) { isGranted ->
                    if (isGranted) {
                        getLocation()
                    } else {
                    }
                }
                locationPermissionRequest.launch(ACCESS_FINE_LOCATION)
            }
        
            override fun onResume() {
                super.onResume()
                accelerometer?.also {
                    sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_UI)
                }
                magnetometer?.also {
                    sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_UI)
                }
            }
        
            override fun onPause() {
                super.onPause()
                sensorManager.unregisterListener(this)
            }
        
            override fun onSensorChanged(event: SensorEvent) {
                when (event.sensor.type) {
                    Sensor.TYPE_ACCELEROMETER -> {
                        gravity[0] = event.values[0]
                        gravity[1] = event.values[1]
                        gravity[2] = event.values[2]
                    }
        
                    Sensor.TYPE_MAGNETIC_FIELD -> {
                        geomagnetic[0] = event.values[0]
                        geomagnetic[1] = event.values[1]
                        geomagnetic[2] = event.values[2]
                    }
                }
        
                val R = FloatArray(9)
                val I = FloatArray(9)
                if (SensorManager.getRotationMatrix(R, I, gravity, geomagnetic)) {
                    val orientation = FloatArray(3)
                    SensorManager.getOrientation(R, orientation)
                    val newAzimuth = Math.toDegrees(orientation[0].toDouble()).toFloat()
        
                    // Rotate compass image
                    bind.compassImage.rotation = -newAzimuth
                    azimuth = newAzimuth
                }
        
                event?.let {
                    when (it.sensor.type) {
                        Sensor.TYPE_ACCELEROMETER -> {
                            System.arraycopy(
                                it.values,
                                0,
                                accelerometerReading,
                                0,
                                accelerometerReading.size
                            )
                        }
        
                        Sensor.TYPE_MAGNETIC_FIELD -> {
                            System.arraycopy(it.values, 0, magnetometerReading, 0, magnetometerReading.size)
                        }
                    }
        
                    if (SensorManager.getRotationMatrix(
                            rotationMatrix,
                            null,
                            accelerometerReading,
                            magnetometerReading
                        )
                    ) {
                        SensorManager.getOrientation(rotationMatrix, orientationAngles)
                        val azimuthInDegrees = Math.toDegrees(orientationAngles[0].toDouble()).toFloat()
                        displayDirection(azimuthInDegrees)
                    }
                }
            }
        
            private var accelerometerReading = FloatArray(3)
            private var magnetometerReading = FloatArray(3)
            private var rotationMatrix = FloatArray(9)
            private var orientationAngles = FloatArray(3)
        
            override fun onAccuracyChanged(sensor: Sensor, accuracy: Int) {
                // We won’t be using this method here
            }
        }


// Current location get current location

        class MapLocationActivity : BaseAct<ActivityMapLocationBinding>() {
                override fun getActivityBinding(inflater: LayoutInflater) =
                    ActivityMapLocationBinding.inflate(layoutInflater)
            
                private lateinit var fusedLocationClient: FusedLocationProviderClient
            
                override fun initUI() {
                    fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
            
                    bind.toolid.apply {
                        backid.onClick {
                            finish()
                        }
                        tvtoolname.text = "Map Location"
                    }
            
                    bind.apply {
                        btnid.gone()
                        btnshareid.gone()
                        btnid.onClick {
                            startActivityWithBundle<MapViewActivity>()
                        }
            
                        btnshareid.onClick {
                            val detail =
                                "Location: ${tvaddressid.text.toString()} \n " + "Latitude: ${latitudeid.text.toString()} \n " + "Longitude: ${longtitudeid.text.toString()} \n " + "Altitude: ${altitudeid.text.toString()} \n " + "Speed: ${speedid.text.toString()} \n "
            
                            shareDetails(detail)
                        }
                    }
                    requestLocationPermission()
                }
            
                private fun shareDetails(details: String) {
                    val shareIntent = Intent().apply {
                        action = Intent.ACTION_SEND
                        putExtra(Intent.EXTRA_TEXT, details)
                        type = "text/plain"
                    }
                    startActivity(Intent.createChooser(shareIntent, "Share details via"))
                }
            
                private fun requestLocationPermission() {
                    val locationPermissionRequest = registerForActivityResult(
                        ActivityResultContracts.RequestPermission()
                    ) { isGranted ->
                        if (isGranted) {
                            startLocationUpdates()
                        } else {
                            "Location permission is required to display location data.".tos(this)
                        }
                    }
                    locationPermissionRequest.launch(ACCESS_FINE_LOCATION)
                }
            
                private fun startLocationUpdates() {
                    if (ActivityCompat.checkSelfPermission(
                            this, ACCESS_FINE_LOCATION
                        ) != PackageManager.PERMISSION_GRANTED
                    ) {
                        return
                    }
            
                    val locationRequest = LocationRequest.create().apply {
                        interval = 10000 // Update interval in milliseconds
                        fastestInterval = 5000 // Fastest update interval in milliseconds
                        priority = LocationRequest.PRIORITY_HIGH_ACCURACY
                    }
                    fusedLocationClient.requestLocationUpdates(locationRequest, locationCallback, null)
                }
            
                private val locationCallback = object : LocationCallback() {
                    override fun onLocationResult(locationResult: LocationResult) {
                        locationResult.lastLocation?.let { location ->
                            displayLocationInfo(location)
                        }
                    }
                }
            
                private fun displayLocationInfo(location: Location) {
                    val latitude = location.latitude
                    val longitude = location.longitude
                    val altitude = location.altitude
                    val speed = location.speed
            
                    // Get address using Geocoder
                    val geocoder = Geocoder(this, Locale.getDefault())
                    val address = try {
                        val addresses = geocoder.getFromLocation(latitude, longitude, 1)
                        addresses?.get(0)?.getAddressLine(0) ?: "Address not available"
                    } catch (e: Exception) {
                        "Address not available"
                    }
            
                    // Display information
                    bind.latitudeid.text = latitude.toString()
                    bind.tvaddressid.text = address.toString()
                    bind.longtitudeid.text = longitude.toString()
                    bind.altitudeid.text = "$altitude meters"
                    bind.speedid.text = "${speed} m/s"
                    bind.btnid.visible()
                    bind.btnshareid.visible()
                }
            
                override fun onStop() {
                    super.onStop()
                    // Stop location updates when activity is no longer visible
                    fusedLocationClient.removeLocationUpdates(locationCallback)
                }
            
                private fun openLocationInMap(latitude: Double, longitude: Double) {
                    val uri = "geo:$latitude,$longitude?q=$latitude,$longitude(Current Location)"
                    val mapIntent = Intent(Intent.ACTION_VIEW, Uri.parse(uri))
                    mapIntent.setPackage("com.google.android.apps.maps")
                    if (mapIntent.resolveActivity(packageManager) != null) {
                        startActivity(mapIntent)
                    } else {
                        // Handle case where Google Maps is not installed
                        ("Google Maps is not installed.").tos(this)
                    }
                }
            }


// weather data waither data weather data    
        
          class WeatherActivity : BaseAct<ActivityWeatherBinding>() {
                override fun getActivityBinding(inflater: LayoutInflater) =
                    ActivityWeatherBinding.inflate(layoutInflater)
            
                private lateinit var fusedLocationClient: FusedLocationProviderClient
            
                private val LOCATION_PERMISSION_REQUEST_CODE = 1
            
                override fun initUI() {
                    bind.toolbarid.apply {
                        backid.onClick {
                            finish()
                        }
                        tvtoolname.text = "Weather"
                    }
            
                    val dateFormat = SimpleDateFormat("yyyy-MM-dd HH:mm", Locale.getDefault())
                    val currentDateAndTime: String = dateFormat.format(Date())
            
                    bind.apply {
                        tvdatetimeid.text = currentDateAndTime
                    }
            
                    fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
            
                    checkLocationPermissionAndFetchTemperature()
            
                }
            
            
                private fun checkLocationPermissionAndFetchTemperature() {
                    if (ContextCompat.checkSelfPermission(
                            this, ACCESS_FINE_LOCATION
                        ) == PackageManager.PERMISSION_GRANTED
                    ) {
                        fetchLocationAndTemperature()
                    } else {
                        ActivityCompat.requestPermissions(
                            this, arrayOf(ACCESS_FINE_LOCATION), LOCATION_PERMISSION_REQUEST_CODE
                        )
                    }
                }
            
                override fun onRequestPermissionsResult(
                    requestCode: Int, permissions: Array<String>, grantResults: IntArray
                ) {
                    super.onRequestPermissionsResult(requestCode, permissions, grantResults)
                    if (requestCode == LOCATION_PERMISSION_REQUEST_CODE && grantResults.isNotEmpty() && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                        fetchLocationAndTemperature()
                    } else {
                        ("Location permission denied").tos(this)
                    }
                }
            
                private fun fetchLocationAndTemperature() {
                    if (ActivityCompat.checkSelfPermission(
                            this, ACCESS_FINE_LOCATION
                        ) != PackageManager.PERMISSION_GRANTED && ActivityCompat.checkSelfPermission(
                            this, Manifest.permission.ACCESS_COARSE_LOCATION
                        ) != PackageManager.PERMISSION_GRANTED
                    ) {
            
                        return
                    }
                    fusedLocationClient.lastLocation.addOnSuccessListener { location ->
                        if (location != null) {
                            val latitude = location.latitude
                            val longitude = location.longitude
            
                            fetchTemperature(latitude, longitude)
            
                            val geocoder = Geocoder(this, Locale.getDefault())
                            try {
                                val addressList =
                                    geocoder.getFromLocation(latitude, longitude, 1) // Get the closest address
                                if (addressList != null && addressList.isNotEmpty()) {
                                    val address = addressList[0] // Get the first address
                                    val city = address.locality ?: "Unknown City"
                                    val country = address.countryName ?: "Unknown Country"
            
                                    // Show the address in a TextView
                                    val closestArea = "$city, $country"
                                    bind.tvlocationid.text = closestArea // Update TextView with location
                                } else {
                                    bind.tvlocationid.text = "Unable to retrieve address"
                                }
                            } catch (e: IOException) {
                                e.printStackTrace()
                                bind.tvlocationid.text = "Error retrieving address"
                            }
                        } else {
                            bind.tvlocationid.text = "Unable to retrieve location"
                        }
                    }
                }
            
                private fun fetchTemperature(latitude: Double, longitude: Double) {
                    // Modify the URL to request both current weather and 3-day forecast
                    val url =
                        "https://api.open-meteo.com/v1/forecast?latitude=$latitude&longitude=$longitude&current_weather=true&daily=temperature_2m_max,temperature_2m_min&timezone=auto"
            
                    val client = OkHttpClient()
                    val request = Request.Builder().url(url).build()
            
                    client.newCall(request).enqueue(object : okhttp3.Callback {
                        override fun onFailure(call: okhttp3.Call, e: IOException) {
                            runOnUiThread {
                                // Handle failure case
                                ("Failed to retrieve weather data").tos(this@WeatherActivity)
                            }
                        }
            
                        override fun onResponse(call: okhttp3.Call, response: okhttp3.Response) {
                            if (response.isSuccessful) {
                                response.body?.let { responseBody ->
                                    val responseData = responseBody.string()
                                    val jsonObject = JSONObject(responseData)
            
                                    // Parse the current weather data
                                    val currentWeather = jsonObject.getJSONObject("current_weather")
                                    val temperature =
                                        currentWeather.getDouble("temperature") // Current temperature
                                    val windSpeed = currentWeather.getDouble("windspeed") // Windspeed in km/h
                                    val windDirection =
                                        currentWeather.getDouble("winddirection") // Wind direction in °
                                    val humidity = currentWeather.optInt("humidity", -1) // Today's humidity
            
                                    // Parse the 3-day forecast temperatures
                                    val dailyWeather = jsonObject.getJSONObject("daily")
                                    val nextThreeDaysTemps = ArrayList<String>()
                                    val nextThreeDaysNames = ArrayList<String>()
            
                                    val calendar = Calendar.getInstance()
            
            
                                    for (i in 1..3) {
                                        calendar.add(Calendar.DAY_OF_YEAR, 1)
                                        val dayName =
                                            SimpleDateFormat("EEEE", Locale.getDefault()).format(calendar.time)
            
                                        val day = dailyWeather.getString("time").split(",")[i]
                                        val maxTemp =
                                            dailyWeather.getJSONArray("temperature_2m_max").getDouble(i)
                                        val minTemp =
                                            dailyWeather.getJSONArray("temperature_2m_min").getDouble(i)
                                        nextThreeDaysTemps.add("$maxTemp")
                                        nextThreeDaysNames.add(dayName)
                                    }
            
                                    // Log the data (for debugging)
                                    Log.d("WeatherData", "Today's Temperature: $temperature°C")
                                    Log.d("WeatherData", "Today's Wind Speed: $windSpeed km/h")
                                    Log.d("WeatherData", "Today's Wind Direction: $windDirection°")
                                    Log.d("WeatherData", "Today's Humidity: $humidity%")
                                    nextThreeDaysTemps.forEach {
                                        Log.d("WeatherData", "Next 3 Days: $it")
                                    }
            
                                    // Update the UI with the parsed data
                                    runOnUiThread {
                                        bind.tvtempid.text = "$temperature° C" // Current temperature
                                        bind.tvsectempid.text = "$temperature°"
                                        bind.tvwindid.text = "$windSpeed km/h"
                                        bind.tvhumidityid.text = "$humidity%" // Today's humidity
            
                                        // Display the next 3 days' temperatures
            
                                        (nextThreeDaysTemps.joinToString("\n")).log()
                                        bind.tvtempnextonedayid.text = "${nextThreeDaysTemps[0]}°C"
                                        bind.tvtempnexttwodayid.text = "${nextThreeDaysTemps[1]}°C"
                                        bind.tvtempnextthreedayid.text = "${nextThreeDaysTemps[2]}°C"
            
                                        bind.tvdaynameoneid.text = "${nextThreeDaysNames[0]}"
                                        bind.tvdaynametwoid.text = "${nextThreeDaysNames[1]}"
                                        bind.tvdaynamethreeid.text = "${nextThreeDaysNames[2]}"
            
                                    }
                                }
                            } else {
                                runOnUiThread {
                                    // Handle response failure
                                    ("Failed to retrieve weather data").tos(this@WeatherActivity)
                                }
                            }
                        }
                    })
                }
            }

// Ext Funcation

    inline fun <reified T : Activity> Context.startActivityWithBundle(bundle: Bundle? = null) {
        val intent = Intent(this, T::class.java).apply {
            bundle?.let { putExtras(it) }
        }
        startActivity(intent)
    }
            
// Bitmap Funcation
    
      fun Drawable.applyColorFilter(color: Int) = mutate().setColorFilter(color, PorterDuff.Mode.SRC_IN)
            
            fun Drawable.convertToBitmap(): Bitmap {
                val bitmap = if (intrinsicWidth <= 0 || intrinsicHeight <= 0) {
                    Bitmap.createBitmap(1, 1, Bitmap.Config.ARGB_8888)
                } else {
                    Bitmap.createBitmap(intrinsicWidth, intrinsicHeight, Bitmap.Config.ARGB_8888)
                }
            
                if (this is BitmapDrawable) {
                    if (this.bitmap != null) {
                        return this.bitmap
                    }
                }
            
                val canvas = Canvas(bitmap)
                setBounds(0, 0, canvas.width, canvas.height)
                draw(canvas)
                return bitmap
            }
            
// Direct call without showing a system launcher in call
    
     try {
                                if (ActivityCompat.checkSelfPermission(
                                        context, Manifest.permission.CALL_PHONE
                                    ) != PackageManager.PERMISSION_GRANTED
                                ) {
                                    return@RecentCallsAdapter
                                }
                                (context.getSystemService(TELECOM_SERVICE) as TelecomManager).placeCall(
                                    Uri.fromParts(
                                        "tel", recentCall.phoneNumber, null
                                    ), Bundle()
                                )
                            } catch (unused2: java.lang.Exception) {
                                val intent = Intent(
                                    "android.intent.action.CALL", Uri.parse(
                                        "tel:$str"
                                    )
                                )
                                intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
                                try {
                                    context.startActivity(intent)
                                } catch (unused3: ActivityNotFoundException) {
                                    Toast.makeText(
                                        context,
                                        "Could not find an activity to place the call.",
                                        Toast.LENGTH_SHORT
                                    ).show()
                                }
                            }
                            
// Show First App in Top in display overlay permission
        
                       android:label="#appname"
        
                               <activity
                android:name=".activities.SplashActivity"
                android:exported="true"
                android:label="@string/app_name"
        
        
        
              <activity
                android:name=".activities.TranslucentActivity"
                android:configChanges="orientation"
                android:exported="false"
                android:screenOrientation="portrait"
                android:theme="@style/AppTheme.TransparentWindow" />
        
        
                    <style name="AppTheme.TransparentWindow" parent="@style/Theme.AppCompat.DayNight.NoActionBar">
            <item name="android:windowBackground">@android:color/transparent</item>
            <item name="android:windowNoTitle">true</item>
            <item name="android:windowIsFloating">false</item>
            <item name="android:windowIsTranslucent">true</item>
            <item name="android:windowContentOverlay">@null</item>
            <item name="android:backgroundDimEnabled">false</item>
            <item name="android:windowDisablePreview">true</item>
            <item name="android:colorBackgroundCacheHint">@null</item>
            <item name="android:statusBarColor">#aa000000</item>
            <item name="backgroundColor">@android:color/transparent</item>
        </style>
        
        
        
          val intent = Intent(
                    Settings.ACTION_MANAGE_OVERLAY_PERMISSION, Uri.parse("package:$packageName")
                )
                startActivityForResult(intent, REQUEST_OVERLAY_PERMISSION)
                Timer().schedule(object : TimerTask() {
                    override fun run() {
                        startActivity(
                            Intent(
                                this@SetDefaultActivity, TranslucentActivity::class.java
                            ).setFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP).putExtra(
                                "autostart",
                                resources.getString(com.trucontactsapp.callerdailer.R.string.allow_displaying_over_other_apps)
                            )
                        )
                    }
                }, 50L)
                    
        public class TranslucentActivity extends AppCompatActivity {
            private RelativeLayout relLay;
        
            @Override
            public void onCreate(Bundle bundle) {
                super.onCreate(bundle);
                setContentView(R.layout.translucent_activity);
        
                Log.wtf("FATZ", "Come in Heree Trasla");
        
                this.relLay = (RelativeLayout) findViewById(R.id.translayRel);
                TextView textView = (TextView) findViewById(R.id.text_trans);
                this.relLay.setOnClickListener(new View.OnClickListener() {
                    @Override
                    public void onClick(View view) {
                        finish();
                    }
                });
        
                String stringExtra = getIntent().getStringExtra("autostart");
                if (stringExtra != null) {
                    textView.setText(stringExtra);
                }
            }
        }
        
        
                  <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:id="@+id/translayRel"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
        
            <RelativeLayout
                android:id="@+id/topLayout"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_above="@+id/lout_bottom"
                android:background="#22000000" />
        
            <LinearLayout
                android:id="@+id/lout_bottom"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_alignParentBottom="true"
                android:background="@color/white"
                android:orientation="vertical"
                android:paddingTop="@dimen/_6sdp"
                android:paddingBottom="@dimen/_8sdp">
        
                <TextView
                    android:id="@+id/text_trans"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginLeft="@dimen/_8sdp"
                    android:padding="@dimen/_5sdp"
                    android:text="Find Contact and allow permission"
                    android:textColor="@color/black"
                    android:textSize="@dimen/_12sdp" />
        
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginLeft="@dimen/_4sdp"
                    android:orientation="horizontal"
                    android:padding="@dimen/_8sdp">
        
                    <androidx.appcompat.widget.AppCompatImageView
                        android:id="@+id/appIcon"
                        android:layout_width="@dimen/_32sdp"
                        android:layout_height="@dimen/_32sdp"
                        android:layout_gravity="center"
                        android:src="@mipmap/ic_launcher" />
        
                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center"
                        android:layout_marginLeft="@dimen/_12sdp"
                        android:layout_weight="1"
                        android:text="#Contacts"
                        android:textColor="@color/black"
                        android:textSize="@dimen/_16sdp" />
        
                    <com.airbnb.lottie.LottieAnimationView
                        android:id="@+id/animation_view"
                        android:layout_width="@dimen/_50sdp"
                        android:layout_height="@dimen/_50sdp"
                        app:lottie_autoPlay="true"
                        app:lottie_loop="true"
                        app:lottie_rawRes="@raw/animation" />
        
                </LinearLayout>
            </LinearLayout>
        </RelativeLayout>

// Firebase Error in menifesterror and release apk and bundl time menifest error

        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
    
      <property
            android:name="android.adservices.AD_SERVICES_CONFIG"
            android:resource="@xml/gma_ad_services_config"
            tools:replace="android:resource" />
    
    
              <meta-data
            android:name="com.google.android.gms.ads.flag.OPTIMIZE_INITIALIZATION"
            android:value="true" />
        <meta-data
            android:name="com.google.android.gms.ads.flag.OPTIMIZE_AD_LOADING"
            android:value="true" />

// number logic for practicle intereview

    // activity_main.xml
    
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        xmlns:app="http://schemas.android.com/apk/res-auto">
    
        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/recyclerView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:padding="16dp"
            android:layout_centerInParent="true"
            android:clipToPadding="false"
            android:layout_margin="16dp"/>
    </RelativeLayout>

    // MainActivity
    
    class MainActivity : BaseAct<ActivityMainBinding>() {
        private val GRID_SIZE = MATRIX_ITEM
        private var adapter: MatrixAdapter? = null
        private var matrix: ArrayList<Int> = arrayListOf()
        var MATRIX_ITEM = 5;

        override fun getActivityBinding(inflater: LayoutInflater) =
            ActivityMainBinding.inflate(layoutInflater)
    
        override fun initUI() {
    
            matrix = ArrayList()
            for (i in 0 until GRID_SIZE * GRID_SIZE) {
                matrix.add(i + 1) // Fill with numbers 1-25
            }
              val itemCount = GRID_SIZE * GRID_SIZE
            val gridSize = sqrt(itemCount.toDouble()).toInt()
    
            val layoutManager = GridLayoutManager(this, gridSize)
            bind.recyclerView.setLayoutManager(layoutManager)
    
            // Initialize the adapter
    
            // Initialize the adapter
            adapter = MatrixAdapter(matrix, gridSize)
            bind.recyclerView.setAdapter(adapter)
        }
    }


      //  MatrixAdapter.kt adapter

    class MatrixAdapter(private val matrix: List<Int>,var gridSize: Int) :
        RecyclerView.Adapter<MatrixAdapter.ViewHolder?>() {
        private var selectedPosition = -1
        override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
            val view: View =
                LayoutInflater.from(parent.context).inflate(R.layout.item_cell, parent, false)
            return ViewHolder(view)
        }
    
        override fun onBindViewHolder(holder: ViewHolder, @SuppressLint("RecyclerView") position: Int) {
            holder.textView.text = matrix[position].toString()
            if (selectedPosition == position || isInHighlightPath(position)) {
                holder.textView.setBackgroundColor(Color.RED)
                holder.textView.setTextColor(Color.WHITE)
            } else {
                holder.textView.setBackgroundColor(Color.WHITE)
                holder.textView.setTextColor(Color.BLACK)
            }
            holder.itemView.setOnClickListener { v: View? ->
                selectedPosition = position
                notifyDataSetChanged()
            }
        }
    
        override fun getItemCount(): Int {
            return matrix.size
        }

      private fun isOnBorder(position: Int): Boolean {
        val row = position / gridSize
        val col = position % gridSize

        // Check if the position is in the first or last row or column
        return row == 0 || row == gridSize - 1 || col == 0 || col == gridSize - 1
    }

        private fun isEvenRow(position: Int): Boolean {
        val row = position / MATRIX_SIZE
        return row % 2 == 0
    }

    private fun isOddRow(position: Int): Boolean {
        val row = position / MATRIX_SIZE
        return row % 2 != 0
    }


    private fun isChessBoardPattern(position: Int): Boolean {
        val row = position / gridSize
        val col = position % gridSize

        // Returns true if the cell should be black, false if it should be white
        return (row + col) % 2 == 0
    }
    
        private fun isInHighlightPath(position: Int): Boolean {
            if (selectedPosition == -1) return false
            val selectedRow: Int = selectedPosition / gridSize
            val selectedCol: Int = selectedPosition % gridSize
            val row: Int = position / gridSize
            val col: Int = position % gridSize
    
            // Check row, column, and diagonal intersections
            return row == selectedRow || col == selectedCol || abs((selectedRow - row).toDouble()) == abs(
                (selectedCol - col).toDouble()
            )
        }
    
        class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
            var textView: TextView
    
            init {
                textView = itemView.findViewById<TextView>(R.id.textView)
            }
        }
        private val GRID_SIZE = MATRIX_ITEM
    }

    //    item_cell.xml

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="2dp"
        android:gravity="center"
        android:orientation="vertical">
    
        <TextView
            android:id="@+id/textView"
            android:layout_width="50dp"
            android:layout_height="50dp"
            android:background="#FFFFFF"
            android:gravity="center"
            android:textColor="#000000"
            android:textSize="18sp"
            android:textStyle="bold" />
    </LinearLayout>




// ZoomIn and Zoomout animation   
                        
                            fun zoomInOutWithImageChange(imageView: ImageView) {
                                var isZoomingIn = true
                        
                                fun createScaleAnimation(startScale: Float, endScale: Float): ScaleAnimation {
                                    val scaleAnimation = ScaleAnimation(
                                        startScale,
                                        endScale,
                                        startScale,
                                        endScale,
                                        Animation.RELATIVE_TO_SELF,
                                        0.5f,
                                        Animation.RELATIVE_TO_SELF,
                                        0.5f
                                    )
                                    scaleAnimation.duration = 500
                                    scaleAnimation.fillAfter = true
                        
                                    return scaleAnimation
                                }
                        
                                fun startAnimation() {
                                    val scaleAnimation = if (isZoomingIn) {
                                        createScaleAnimation(1f, 1.2f)
                                    } else {
                                        createScaleAnimation(1.2f, 1f)
                                    }
                        
                                    scaleAnimation.setAnimationListener(object : Animation.AnimationListener {
                                        override fun onAnimationStart(animation: Animation?) {
                        
                                        }
                        
                                        override fun onAnimationEnd(animation: Animation?) {
                                            isZoomingIn = !isZoomingIn
                                            if (!isZoomingIn) {
                                                imageIndex = (imageIndex + 1) % imageList.size // Cycle through images
                                                imageView.setImageResource(imageList[imageIndex])
                                            }
                                            startAnimation() // Start the next animation
                                        }
                        
                                        override fun onAnimationRepeat(animation: Animation?) {}
                                    })
                                    imageView.startAnimation(scaleAnimation)
                                }
                        
                                startAnimation()
                            }


                            
                            
// Make a eassy way to make a bundle

            org.gradle.jvmargs=-Xmx4g -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8


// Ffmpeg Demo & Image to background ML Kit
            
            //ML KIT
            implementation(libs.segmentation.selfie)
            implementation ("com.google.mlkit:image-labeling:17.0.8")
                 segmentation-selfie = { module = "com.google.mlkit:segmentation-selfie", version.ref = "segmentation-selfie" }segmentation-selfie = "16.0.0-beta5"
                 
                 //ffmpeg
                     implementation("com.arthenica:ffmpeg-kit-full-gpl:6.0-2")
            
            
            
                     // Ffmpege main command Class
                          
                                       
                          fun mp4toAvi(input : String, output : String) {
                              val command = "-i \"$input\" \"$output\""
                              FFmpegKit.executeAsync(command) {
                                  if(it.returnCode.value == ReturnCode.SUCCESS) {
                                      Log.d("FFmpeg", "Command execution succeeded.")
                                  } else if(it.returnCode.value == ReturnCode.CANCEL) {
                                      Log.d("FFmpeg", "Command execution cancelled.")
                                  } else {
                                      Log.d("FFmpeg", "Command execution failed with code: ${it.returnCode.value}")
                                  }
                              }
                          }
                          
                          fun Context.makeVideoFromImages(listOfImages : ArrayList<String>, output : String, dur : Int = 9, onGoPlay : (String) -> Unit) {
                              var duration = dur
                          
                              if(dur < listOfImages.size * 2) {
                                  duration = (listOfImages.size * 2)
                                  Log.e("TAG", "hdbvhusbcvhjudsvb: $duration")
                              }
                              duration += listOfImages.size - 1
                          //    if(listOfImages.size % 2 == 1) {
                          //        for (i in 1..listOfImages.size - 1) {
                          //            duration += 1
                          //        }
                          //    }
                              var eachImageDur = duration.toFloat().div(listOfImages.size)
                          
                              Log.e("KP FFMPEG", "makeSingleImageVideo: $listOfImages")
                              val folder = File(cacheDir, "FFMPEG Demo/temp")
                              val scope = CoroutineScope(Dispatchers.IO)
                              val countDownLatch = CountDownLatch(listOfImages.size)
                              var progressDialog = ProgressDialog(this)
                              progressDialog.apply {
                                  setTitle("Making Video")
                                  setCancelable(false)
                                  setMessage("0% Progressing")
                              }
                              progressDialog.show()
                              var tmpTargetPercentage = 80
                              var targetDuration = duration * 1000f
                              var progressFile = 0f
                              var mainProgress = 0f
                              if(! folder.exists()) {
                                  if(folder.mkdirs()) {
                                      Log.e("KP FFMPEG", "1 makeSingleImageVideo: ${folder.absolutePath}")
                          
                                      scope.launch {
                                          listOfImages.forEachIndexed { index, path ->
                                              launch {
                                                  makeSingleImageVideo(eachImageDur, path, index, folder, {
                          
                                                      (this@makeVideoFromImages as Activity).runOnUiThread {
                                                          if(! it) {
                                                              toast("have an Issue!")
                                                          }
                                                      }
                          
                                                      countDownLatch.countDown() // Signal completion of this task
                                                  }, {
                                                      progressFile += it
                                                      mainProgress = (tmpTargetPercentage * progressFile) / targetDuration
                                                      (this@makeVideoFromImages as Activity).runOnUiThread {
                                                          progressDialog.setMessage("${String.format("%.2f", mainProgress)} Progressing")
                                                      }
                                                  })
                                              }
                                          }
                                          countDownLatch.await() // Wait for all tasks to complete
                                          // After all makeSingleImageVideo calls are done
                                          var tmpTargetPercentageFinal = 20
                                          var targetDuration = duration * 1000.0f
                                          var lastProgress = 0.0f
                                          Log.e("TAG", "makeVideoFromImages: ------------------->lastProgress:$lastProgress")
                                          makeVideo(listOfImages, folder, output, duration, { result, path ->
                                              progressDialog.dismiss()
                                              if(result) {
                                                  onGoPlay(path)
                                              }
                                          }, {
                                              lastProgress += it
                                              var progress = mainProgress + (tmpTargetPercentageFinal * lastProgress).div(targetDuration)
                                              progressDialog.setMessage("${String.format("%.2f", progress)} Progressing")
                          
                                          })
                                      }
                          
                                  }
                                  Log.e("KP FFMPEG", "exists makeSingleImageVideo: ${folder.exists()}")
                          
                              } else {
                                  Log.e("KP FFMPEG", "2 makeSingleImageVideo: ${folder.absolutePath}")
                                  scope.launch {
                                      listOfImages.forEachIndexed { index, path ->
                                          launch {
                                              makeSingleImageVideo(eachImageDur, path, index, folder, {
                                                  (this@makeVideoFromImages as Activity).runOnUiThread {
                                                      if(! it) {
                                                          toast("have an Issue!")
                                                      }
                                                  }
                                                  countDownLatch.countDown() // Signal completion of this task
                                              }, {
                                                  progressFile += it
                                                  mainProgress = (tmpTargetPercentage * progressFile) / targetDuration
                                                  (this@makeVideoFromImages as Activity).runOnUiThread {
                                                      progressDialog.setMessage("${String.format("%.2f", mainProgress)} Progressing")
                                                  }
                                              })
                                          }
                                      }
                                      countDownLatch.await() // Wait for all tasks to complete
                                      // After all makeSingleImageVideo calls are done
                          
                                      var tmpTargetPercentageFinal = 20
                                      var targetDuration = duration * 1000.0f
                                      var lastProgress = 0.0f
                                      makeVideo(listOfImages, folder, output, duration, { result, path ->
                                          progressDialog.dismiss()
                                          if(result) {
                                              onGoPlay(path)
                                          }
                                      }, {
                                          lastProgress += it
                                          Log.e("TAG", "makeVideoFromImages: ---------targetDuration :$targetDuration ---------->lastProgress:$lastProgress")
                          
                                          var progress = mainProgress + (tmpTargetPercentageFinal * lastProgress).div(targetDuration)
                                          progressDialog.setMessage("${String.format("%.2f", progress)} Progressing")
                          
                                      })
                                  }
                          
                              }
                          }
                          
                          
                          /*fun Context.makeVideo(listOfVideos : List<String>, folder : File, output : String) {
                              val fileListBuilder = StringBuilder()
                          
                              listOfVideos.forEachIndexed { index, s ->
                                  fileListBuilder.append("file '").append(File(folder, "vd$index.mp4").absolutePath).append("'\n")
                              }
                              // Write the list to a file
                              val listFile = File(File(getExternalFilesDir(null), "FFMPEG Demo"), "file_list.txt")
                              try {
                                  FileWriter(listFile).use { writer -> writer.write(fileListBuilder.toString()) }
                              } catch (e : IOException) {
                                  e.printStackTrace()
                                  return
                              }
                              // Build the complete FFmpeg command
                              val command = "-f concat -safe 0 -i \"${listFile.absolutePath}\" -c copy \"$output\""
                          
                              // Log the command for debugging
                              Log.e("ffmpeg-kit ", "makeVideo: $command")
                          
                              // Execute the command asynchronously
                              FFmpeg.executeAsync(command) { executionId, returnCode ->
                                  deleteImagesData()
                          
                                  listOfVideos.forEachIndexed { index, _ ->
                                      val tempFile = File(folder, "vd$index.mp4")
                                      if(tempFile.exists()) {
                                          tempFile.delete()
                                      }
                                  }
                                  Log.e("KP FFMPEG", "listFile: ${listFile.absolutePath} returnCode: $returnCode")
                          
                                  if(returnCode == RETURN_CODE_SUCCESS) {
                                      val outputFile = File(output)
                                      if(outputFile.exists()) {
                                          Log.d("FFmpeg KP", "Video created successfully at ${outputFile.absolutePath}")
                                      } else {
                                          Log.d("FFmpeg KP", "Video creation failed.")
                                      }
                                      Log.d("FFmpeg KP", "Command execution succeeded.")
                                  } else if(returnCode == RETURN_CODE_CANCEL) {
                                      Log.d("FFmpeg KP", "Command execution cancelled.")
                                  } else {
                                      Log.d("FFmpeg KP", "Command execution failed with code: $returnCode")
                                  }
                              }
                          }*/
                          
                          
                          /*
                          fun Context.makeVideo(listOfImages : List<String>, folder : File, output : String) {
                              // Build input files string
                              val listPath = listOfImages.indices.joinToString(separator = " ") { index ->
                                  "-i \"${File(folder, "vd$index.mp4").absolutePath}\""
                              }
                          
                          // Build filter_complex string
                              val filterComplex = listOfImages.indices.joinToString(separator = ";") { index ->
                                  "[${index}:v]scale=720:1280,setdar=16/9[v$index]"
                              } + "; " +
                                      listOfImages.indices.joinToString(separator = ";") { index ->
                                          "[v$index]"
                                      } +
                                      "concat=n=${listOfImages.size}:v=1:a=0[v]"
                          
                          // Escape output path
                              val quotedOutputPath = "\"${output}\""
                          
                          // Build the complete FFmpeg command
                              val command = "$listPath -filter_complex \"$filterComplex\" -map \"[v]\" -r 25 -c:v libx264 -pix_fmt yuv420p -preset ultrafast $quotedOutputPath"
                          
                          // Log the command for debugging
                              Log.e("ffmpeg-kit ", "makeVideo: $command")
                          
                              // Execute the command asynchronously
                              FFmpeg.executeAsync(command) { executionId, returnCode ->
                                  deleteImagesData()
                          
                                  listOfImages.forEachIndexed { index, s ->
                                      var tempFile = File(folder, "vd$index.mp4")
                                      tempFile.delete()
                                  }
                                  Log.e("KP FFMPEG", "3 listPath  returnCode: $returnCode  $listPath ")
                          
                                  if(returnCode == RETURN_CODE_SUCCESS) {
                                      val outputFile = File(output)
                                      if(outputFile.exists()) {
                                          Log.d("FFmpeg KP", "Video created successfully at ${outputFile.absolutePath}")
                                      } else {
                                          Log.d("FFmpeg KP", "Video creation failed.")
                                      }
                                      Log.d("FFmpeg KP", "Command execution succeeded.")
                                  } else if(returnCode == RETURN_CODE_CANCEL) {
                                      Log.d("FFmpeg KP", "Command execution cancelled.")
                                  } else {
                                      Log.d("FFmpeg KP", "Command execution failed with code: $returnCode")
                                  }
                              }
                          }
                          */
                          
                          fun Context.makeVideo1(listOfImages : List<String>, folder : File, output : String) {
                              // Create a text file with paths of input videos
                              val fileListPath = File(cacheDir, "filelist.txt")
                              fileListPath.writeText(listOfImages.joinToString(separator = "\n") { index ->
                                  "file '$${File(folder, "vd$index.mp4").absolutePath}'"
                              })
                              // Build the FFmpeg command to concatenate videos using the file list
                              val command = "ffmpeg -f concat -safe 0 -i ${fileListPath.absolutePath} -c copy \"$output\""
                          
                          // Execute the command asynchronously
                              FFmpegKit.executeAsync(command) { session ->
                                  when (session.returnCode.value) {
                                      ReturnCode.SUCCESS -> {
                                          Log.d("FFmpeg_", "Merge successful: ${session.output}")
                                      }
                          
                                      ReturnCode.CANCEL -> {
                                          Log.d("FFmpeg_", "Merge canceled")
                                      }
                          
                                      else -> {
                                          Log.e("FFmpeg_", "Merge failed: ${session.statistics}")
                                      }
                                  }
                              }
                          
                          
                          }
                          
                          fun Context.makeVideo(
                              listOfImages : List<String>, folder : File, output : String, duration : Int, onComplate : (Boolean, String) -> Unit, progress : (Int) -> Unit
                          ) {
                              val transitionDuration = 1
                              var pr = 0.0
                              val inputOptions = listOfImages.indices.joinToString(separator = " ") { index ->
                                  "-y -i \"${File(folder, "vd$index.mp4").absolutePath}\""
                              }
                              var lastOffsetValue = 0
                              val filerComplex = buildString {
                                  // Task 3 : Prepare FilterComplex For Video Ratio and bitrate/ fps etc
                                  if(listOfImages.size > 1) {
                                      for (i in 0 until listOfImages.size) {
                                          append("[$i:v]settb=AVTB,setdar=720/1280,fps=25[v${i}];")
                                      }
                                      var totalVideoLength = 0
                          
                                      // Task 4 : Prepare FilterComplex For Each Temp Video's Transitions
                                      for (i in 0 until listOfImages.size - 1) {
                                          val currentVideoLength = duration.div(listOfImages.size)
                                          totalVideoLength += currentVideoLength
                                          if(i == 0) {
                                              if(listOfImages.size - 1 == 1) {
                                                  lastOffsetValue = totalVideoLength - transitionDuration
                                                  val transaction =
                                                      "[v0][v1]xfade=transition=${transitionList.get((0..transitionList.size - 1).random())}:duration=$transitionDuration:offset=${lastOffsetValue}"
                                                  append(transaction)
                                                  Log.e("allVideosList", "makeVideo: idfhnn 1")
                                              } else {
                                                  lastOffsetValue = totalVideoLength - transitionDuration
                                                  val transaction =
                                                      "[v0][v1]xfade=transition=${transitionList.get((0..transitionList.size - 1).random())}:duration=$transitionDuration:offset=${lastOffsetValue}[f0]; "
                                                  append(transaction)
                                                  Log.e("allVideosList", "makeVideo: idfhnn 2")
                                              }
                                          } else if(i == listOfImages.lastIndex - 1) {
                                              lastOffsetValue = totalVideoLength - ((i + 1) * transitionDuration)
                                              val transaction =
                                                  "[f${i - 1}][v${i + 1}]xfade=transition=${transitionList.get((0..transitionList.size - 1).random())}:duration=$transitionDuration:offset=${lastOffsetValue}"
                                              append(transaction)
                                              Log.e("allVideosList", "makeVideo: idfhnn 3")
                                          } else {
                                              lastOffsetValue = totalVideoLength - ((i + 1) * transitionDuration)
                                              val transaction =
                                                  "[f${i - 1}][v${i + 1}]xfade=transition=${transitionList.get((0..transitionList.size - 1).random())}:duration=$transitionDuration:offset=${lastOffsetValue}[f$i]; "
                                              append(transaction)
                                              Log.e("allVideosList", "makeVideo: idfhnn 4")
                                          }
                                          Log.e("TAG", "makeVideo: lastOffsetValue---> $lastOffsetValue  $currentVideoLength $totalVideoLength")
                                      }
                                  }
                              }
                              val quotedOutputPath = "\"${output}\""
                          
                              var command = ""
                              if(filerComplex.isEmpty()) {
                                  command = "-y $inputOptions -c:v copy $quotedOutputPath"
                              } else {
                                  command =
                                      "$inputOptions" + " -filter_complex" + " \"${filerComplex}\"" + " -r 25" + " -c:v libx264" + " -pix_fmt yuv420p" + " -preset ultrafast" + " $quotedOutputPath"
                              }
                              Log.e("ffmpeg-kit ", "makeVideo: $command")
                          
                              /**
                               *     val command = "-y -i \"/data/user/0/com.kp.demo.bgremoval/cache/FFMPEG Demo/temp/vd0.mp4\" -i \"/data/user/0/com.kp.demo.bgremoval/cache/FFMPEG Demo/temp/vd1.mp4\" -filter_complex \"[0:v]scale=720:1280[v0];[1:v]scale=720:1280[v1];[v0][v1]xfade=transition=fade:duration=0.5:offset=0.0[vout]\" -map \"[vout]\" \"/storage/emulated/0/Pictures/FFMPEG Demo/30_07_2024__16_54_57.mp4\""
                               * */
                          // Execute the command asynchronously
                              FFmpegKit.executeAsync(command, {
                                  deleteImagesData()
                          //        listOfImages.forEachIndexed { index, s ->
                          //            var tempFile = File(folder, "vd$index.mp4")
                          //            tempFile.delete()
                          //        }
                                  Log.e("KP FFMPEG", "3 listPath  returnCode: ${it.returnCode}  ")
                          
                          
                                  if(it.returnCode.value == ReturnCode.SUCCESS) {
                                      val outputFile = File(output)
                                      this.runOnUi {
                                          onComplate(true, outputFile.absolutePath)
                                          toast("Video created successfully")
                                      }
                                      if(outputFile.exists()) {
                          
                                          Log.d("FFmpeg KP", "Video created successfully at ${outputFile.absolutePath}")
                                      } else {
                                          Log.d("FFmpeg KP", "Video creation failed.")
                                      }
                                      Log.d("FFmpeg KP", "Command execution succeeded.")
                                  } else if(it.returnCode.value == ReturnCode.CANCEL) {
                                      this.runOnUi {
                                          onComplate(false, "")
                                          toast("Video creation cancelled.")
                                      }
                                      Log.d("FFmpeg KP", "Command execution cancelled.")
                                  } else {
                                      this.runOnUi {
                                          onComplate(false, "")
                                          toast("Video creation failed.")
                                      }
                                      Log.d("FFmpeg KP", "Command execution failed with code: ${it.returnCode.value}")
                                  }
                              }, {
                          
                              }, {
                                  var progressTime = it.time - pr
                                  pr = it.time
                                  this.runOnUi {
                                      progress(progressTime.toInt())
                                  }
                                  Log.e("FFmpeg KP", "makeVideo: Progress -> ${it.toJson}")
                              })
                          }
                          
                          suspend fun makeSingleImageVideo(
                              eachImageDur : Float, imagePath : String, index : Int, folder : File, onComplete : (Boolean) -> Unit, progress : (Int) -> Unit
                          ) {
                              var pr = 0.0
                              var resizeImagePath = File(folder, "tmp_image_$index.jpg")
                              // Task 1 : Making temp Images for next Temp Video Operations
                              var resizeCommand =
                                  "-y" + " -i \"${imagePath}\"" + " -vf \"[0:v]split=2[blur][vid];" + " [blur]scale=720:1280:force_original_aspect_ratio=increase,crop=720:1280,boxblur=luma_radius=min(h\\,w)/5:luma_power=1:chroma_radius=min(cw\\,ch)/5:chroma_power=1[bg];" + " [vid]scale=720:1280:force_original_aspect_ratio=decrease[ov];" + " [bg][ov]overlay='(main_w-overlay_w)/2:(main_h-overlay_h)/2'\"" + " -preset ultrafast" + " \"${resizeImagePath}\""
                          
                              FFmpegKit.executeAsync(resizeCommand, {
                                  if(ReturnCode.isSuccess(it.returnCode)) {
                                      var tempFile = File(folder, "vd$index.mp4")
                          
                                      // Task 2 : Making temp Video for Final Video
                                      var command =
                          //        "-y" + " -framerate 30" + " -i \"$imagePath\"" + " -t ${eachImageDur.toString()} -c:v libx264 -pix_fmt yuv420p -preset ultrafast \"${tempFile.absolutePath}\""
                                          "-y -loop 1" + " -t $eachImageDur" + " -framerate 24" + " -i \"${resizeImagePath}\"" + " -c:v libx264" + " -pix_fmt yuv420p" + " -preset ultrafast" + " \"${tempFile.absolutePath}\""
                          
                                      Log.e("KP FFMPEG", "FFMPEG Command: $command")
                          
                                      FFmpegKit.executeAsync(command, {
                                          Log.e("ffmpeg-kit ", "makeSingleImageVideo:${tempFile.absolutePath}  ${it.returnCode.value == ReturnCode.SUCCESS} --> ${it.returnCode.value}")
                                          onComplete(ReturnCode.isSuccess(it.returnCode))
                                      }, {
                          
                                      }, {
                                          var progressTime = it.time - pr
                                          pr = it.time
                                          progress(progressTime.toInt())
                                          Log.d("TAG", "makeSingleImageVideo: progress -->$index ${it.toJson}")
                                      })
                                  } else {
                                      Log.d("TAG", "resize Image :FAILED $$resizeImagePath ====>")
                          
                                  }
                              }, {}, {
                                  Log.e("TAG", "resize Image : status --> ${it.toJson}")
                              })
                          
                          
                          }
                          
                          fun Context.executeFFmpegCommand(input : File, output : String, durationPerImage : Int = 5) {
                              // Wrap paths with quotes
                              val quotedInputPath = "\"${input.absolutePath}\""
                              val quotedOutputPath = "\"${output}\""
                          
                              // Construct the command string
                          //    val command = "-f concat -safe 0 -i $quotedInputPath -vf fps=1/$durationPerImage -c:v libx264 -pix_fmt yuv420p -r 1/$durationPerImage $quotedOutputPath"
                          //    val command = "-f concat -safe 0 -i $quotedInputPath -vf \"scale=ceil(iw/2)*2:ceil(ih/2)*2\" -c:v libx264 -r 30 -pix_fmt yuv420p $quotedOutputPath"
                          
                          //        val command = "-f concat -safe 0 -i $quotedInputPath -vf \"scale=720:1280,setsar=1\" -framerate 1 -t 00:00:05 -c:v libx264 -r 24 -pix_fmt yuv420p $quotedOutputPath"
                              val command = "-f concat -safe 0 -i $quotedInputPath -vf \"scale=720:1280,setsar=1\" -t 5 -framerate 25 -c:v libx264 -pix_fmt yuv420p $quotedOutputPath"
                          
                          
                              Log.d("FFmpeg", "Command: $command")
                          
                              // Execute the command asynchronously
                              FFmpegKit.executeAsync(command) {
                                  deleteImagesData()
                          
                                  if(it.returnCode.value == ReturnCode.SUCCESS) {
                                      val outputFile = File(output)
                                      if(outputFile.exists()) {
                                          Log.d("FFmpeg KP", "Video created successfully at ${outputFile.absolutePath}")
                                      } else {
                                          Log.d("FFmpeg KP", "Video creation failed.")
                                      }
                                      Log.d("FFmpeg KP", "Command execution succeeded.")
                                  } else if(it.returnCode.value == ReturnCode.CANCEL) {
                                      Log.d("FFmpeg KP", "Command execution cancelled.")
                                  } else {
                                      Log.d("FFmpeg KP", "Command execution failed with code: ${it.returnCode.value}")
                                  }
                              }
                          }
            
            
                          --- appUtils.kt
                              
            fun Context.toast(st : String) {
                Toast.makeText(this, st, Toast.LENGTH_SHORT).show()
            }
            
            fun Context.checkPermissions() : Boolean {
                return if(Build.VERSION.SDK_INT >= 33) {
                    PermissionChecker.checkSelfPermission(
                            this, Manifest.permission.READ_MEDIA_IMAGES
                    ) == PermissionChecker.PERMISSION_GRANTED
                } else {
                    PermissionChecker.checkSelfPermission(
                            this, Manifest.permission.READ_EXTERNAL_STORAGE
                    ) == PermissionChecker.PERMISSION_GRANTED && PermissionChecker.checkSelfPermission(
                            this, Manifest.permission.WRITE_EXTERNAL_STORAGE
                    ) == PermissionChecker.PERMISSION_GRANTED
                }
            }
            
            fun Context.runOnUi(action:Runnable){
                (this as Activity).runOnUiThread(action)
            }
            
            val Any.toJson : String
                get() = Gson().toJson(this)
            
                  --- utils.kt
                    
                    
                              
                    
                    fun View.click(action : (View) -> Unit) = setOnClickListener {
                        action(it)
                    }
                    
                    fun processImageFromUri(context : Context, imageUri : Uri?) : Bitmap? {
                        try {
                            // Get the content resolver
                            val contentResolver : ContentResolver = context.contentResolver
                    
                            // Open an input stream from the URI
                            val inputStream = contentResolver.openInputStream(imageUri !!)
                    
                            // Decode the stream into a bitmap
                            val originalBitmap = BitmapFactory.decodeStream(inputStream)
                    
                            // Close the input stream
                            inputStream !!.close()
                    
                            // Process the bitmap with segmentation
                            return originalBitmap
                        } catch (e : IOException) {
                            // Handle potential exceptions (e.g., file not found, permission issues)
                            Log.e("TAG", "Error loading image from URI: " + e.message)
                        }
                    
                        return null
                    }
                    //fun getMutableBitmapFromUri(context : Context, uri : Uri, config : Bitmap.Config) : Bitmap? {
                    //    var inputStream : InputStream? = null
                    //    return try {
                    //        inputStream = context.contentResolver.openInputStream(uri)
                    //        val options = BitmapFactory.Options()
                    //        options.inMutable = true
                    //        val bitmap = BitmapFactory.decodeStream(inputStream, null, options)
                    //        if(bitmap != null && bitmap.config != config) {
                    //            val mutableBitmap = bitmap.copy(config, true)
                    //            bitmap.recycle() // Recycle the original bitmap
                    //            mutableBitmap // Return the mutable bitmap with the desired configuration
                    //        } else {
                    //            bitmap // Return the original mutable bitmap
                    //        }
                    //    } catch (e : Exception) {
                    //        e.printStackTrace()
                    //        null
                    //    } finally {
                    //        inputStream?.close()
                    //    }
                    //}
                    
                    // Function to export the bitmap
                    fun View.exportBitmap(bitmap : Bitmap, quality : Int, filename : String) {
                        // Get the directory for the app's private pictures directory.
                    
                        var path = File(
                                Environment.getExternalStoragePublicDirectory(
                                        Environment.DIRECTORY_PICTURES
                                ).absolutePath + "/Bg Removal"
                        )
                        if(! path.exists()) {
                            path.mkdir()
                        }
                        val file = File(path, filename)
                    //    if(! file.exists()) {
                    //        file.createNewFile()
                    //    }
                    
                        // Initialize a FileOutputStream object
                    //    val outputStream : OutputStream = FileOutputStream(file)
                    
                        tryWithLogging {
                            // Compress the bitmap into the OutputStream
                    //        bitmap.compress(Bitmap.CompressFormat.PNG, quality, outputStream)
                    //        // Flushes the stream
                    //        outputStream.flush()
                    //        // Closes the stream
                    //        outputStream.close()
                            saveBitmapWithTransparency(bitmap, file)
                        }
                    }
                    
                    suspend fun getSelfieSegmentBitmap(
                        source : Any,
                        context : Context,
                        response : (Bitmap, SEGMENT_RESULT) -> Unit,
                        process : (Int) -> Unit
                    ) {
                        var originalBitmap : Bitmap? = null
                        if(source is Bitmap) {
                            originalBitmap = source
                        } else if(source is Uri) {
                            originalBitmap = processImageFromUri(context, source)
                        } else {
                    
                        }
                        val options = SelfieSegmenterOptions.Builder().setDetectorMode(SelfieSegmenterOptions.SINGLE_IMAGE_MODE).build()
                        val segmenter = Segmentation.getClient(options)
                        segmenter.process(originalBitmap !!, 0).addOnSuccessListener { results ->
                            // Task completed successfully
                            if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
                                response.invoke(startImageBuffer(results, originalBitmap, process) !!, SEGMENT_RESULT.SUCCESS)
                            }
                    
                        }.addOnFailureListener { e ->
                            response.invoke(originalBitmap, SEGMENT_RESULT.FAIL)
                        }
                    }
                    
                    private fun startImageBuffer(segmentationMask : SegmentationMask, bitmap : Bitmap, process : (Int) -> Unit) : Bitmap? {
                        val mask = segmentationMask.getBuffer()
                        val width = segmentationMask.getWidth()
                        val height = segmentationMask.getHeight()
                    
                    //        var segmentedImage = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888)
                        var res = height * width
                        var s=res/6
                        var p = 0
                        var per = 0
                        var segmentedImage = bitmap !!.copy(bitmap !!.config, true)
                        for (y in 0 until height) {
                            for (x in 0 until width) {
                                val foregroundConfidence : Float = mask.float
                                p ++
                                if(foregroundConfidence < 0.7f) {// < for Bg & > for person
                    
                                    var s = if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
                                        var c = bitmap.getColor(x, y)
                                        Color.rgb(c !!.red(), c !!.green(), c !!.blue())
                                    } else {
                                        Color.argb(((1.0 - foregroundConfidence) * 255.0).toInt(), 0, 0, 0)
                                    }
                                    segmentedImage.setPixel(x, y, s)
                                } else {
                                    segmentedImage.setPixel(x, y, Color.TRANSPARENT)
                                }
                            }
                    //        if((y*width)%s==0) {
                    //            if(((100 * p) / res) != per) {
                    //                per = ((100 * p) / res)
                    //                CoroutineScope(Dispatchers.Main).launch {
                    //                    process.invoke(per)
                    //                }
                    //            }
                    //        }
                        }
                        mask.rewind()
                        return segmentedImage
                    }
                    
                    
                    fun saveBitmapWithTransparency(erasedBitmap : Bitmap, outputFile : File) {
                        // Create a new bitmap with an alpha channel
                        val resultBitmap = Bitmap.createBitmap(erasedBitmap.width, erasedBitmap.height, Bitmap.Config.ARGB_8888)
                    
                        // Create a canvas with the result bitmap
                        val canvas = Canvas(resultBitmap)
                    
                        // Draw the erased bitmap onto the result bitmap's canvas
                        canvas.drawBitmap(erasedBitmap, 0f, 0f, null)
                    
                        // Save the result bitmap to a file
                        try {
                            FileOutputStream(outputFile).use { out ->
                                resultBitmap.compress(Bitmap.CompressFormat.PNG, 100, out)
                            }
                            Log.d("SaveBitmap", "Bitmap saved successfully")
                        } catch (e : IOException) {
                            e.printStackTrace()
                            Log.e("SaveBitmap", "Error saving bitmap: ${e.message}")
                        }
                    }
                    
                    inline fun <T> tryWithLogging(block : () -> T) : T? {
                        return try {
                            block()
                        } catch (e : Exception) {
                            // Log the exception here
                            Log.e("TAG", "tryWithLogging: Exception occurred: ${e.message}")
                            null
                        }
                    }
                    
                    fun cropBitmap(bitmap : Bitmap, targetWidth : Int, targetHeight : Int) : Bitmap? {
                        // Calculate the dimensions for cropping
                        val width = bitmap.width
                        val height = bitmap.height
                    
                        // Calculate the final crop dimensions
                        val cropWidth = if(width >= targetWidth) targetWidth else width
                        val cropHeight = if(height >= targetHeight) targetHeight else height
                    
                        // Calculate the starting position for cropping
                        val x = (width - cropWidth) / 2
                        val y = (height - cropHeight) / 2
                    
                        // Create a cropped bitmap
                        return try {
                            Bitmap.createBitmap(bitmap, x, y, cropWidth, cropHeight)
                        } catch (e : Exception) {
                            // Handle any exceptions, such as OutOfMemoryError
                            e.printStackTrace()
                            null
                        }
                    }
            
                          
                 ---  fileUtils.kt
                          
                                   
                          fun getFilePathFromUri(context : Context, uri : Uri) : String? {
                              var filePath : String? = null
                          
                              // DocumentProvider
                              if(DocumentsContract.isDocumentUri(context, uri)) {
                                  when {
                                      isExternalStorageDocument(uri) -> {
                                          val docId = DocumentsContract.getDocumentId(uri)
                                          val split = docId.split(":").toTypedArray()
                                          val type = split[0]
                                          if("primary".equals(type, ignoreCase = true)) {
                                              filePath = Environment.getExternalStorageDirectory().toString() + "/" + split[1]
                                          } else {
                                              // Handle non-primary volumes
                                              val externalMediaDirs = context.externalMediaDirs
                                              for (file in externalMediaDirs) {
                                                  if(file.absolutePath.contains(type)) {
                                                      filePath = file.absolutePath.substringBefore("/Android") + "/" + split[1]
                                                      break
                                                  }
                                              }
                                          }
                                      }
                          
                                      isDownloadsDocument(uri) -> {
                                          val id = DocumentsContract.getDocumentId(uri)
                                          if(id.startsWith("raw:")) {
                                              filePath = id.removePrefix("raw:")
                                          } else {
                                              val contentUri = ContentUris.withAppendedId(
                                                      Uri.parse("content://downloads/public_downloads"), id.toLongOrNull() ?: return null
                                              )
                                              filePath = getDataColumn(context, contentUri, null, null)
                                          }
                                      }
                          
                                      isMediaDocument(uri) -> {
                                          val docId = DocumentsContract.getDocumentId(uri)
                                          val split = docId.split(":").toTypedArray()
                                          val type = split[0]
                                          val contentUri : Uri = when (type) {
                                              "image" -> MediaStore.Images.Media.EXTERNAL_CONTENT_URI
                                              "video" -> MediaStore.Video.Media.EXTERNAL_CONTENT_URI
                                              "audio" -> MediaStore.Audio.Media.EXTERNAL_CONTENT_URI
                                              else -> return null
                                          }
                                          val selection = "_id=?"
                                          val selectionArgs = arrayOf(split[1])
                                          filePath = getDataColumn(context, contentUri, selection, selectionArgs)
                                      }
                                  }
                              }
                              // MediaStore (and general)
                              else if("content".equals(uri.scheme, ignoreCase = true)) {
                                  filePath = getDataColumn(context, uri, null, null)
                              }
                              // File
                              else if("file".equals(uri.scheme, ignoreCase = true)) {
                                  filePath = uri.path
                              }
                          
                              return filePath
                          }
                          
                          private fun isExternalStorageDocument(uri : Uri) : Boolean {
                              return "com.android.externalstorage.documents" == uri.authority
                          }
                          
                          private fun isDownloadsDocument(uri : Uri) : Boolean {
                              return "com.android.providers.downloads.documents" == uri.authority
                          }
                          
                          private fun isMediaDocument(uri : Uri) : Boolean {
                              return "com.android.providers.media.documents" == uri.authority
                          }
                          
                          fun Context.deleteImagesData() {
                              File(File(getExternalFilesDir(null), "FFMPEG Demo"), "images.txt").delete()
                              File(File(getExternalFilesDir(null), "FFMPEG Demo"), "file_list.txt").delete()
                              File(filesDir, "filelist.txt").delete()
                          }
                          
                          private fun getDataColumn(context : Context, uri : Uri, selection : String?, selectionArgs : Array<String>?) : String? {
                              var cursor : Cursor? = null
                              val column = "_data"
                              val projection = arrayOf(column)
                              try {
                                  cursor = context.contentResolver.query(uri, projection, selection, selectionArgs, null)
                                  if(cursor != null && cursor.moveToFirst()) {
                                      val columnIndex = cursor.getColumnIndexOrThrow(column)
                                      return cursor.getString(columnIndex)
                                  }
                              } finally {
                                  cursor?.close()
                              }
                              return null
                          }
                          
                          val getRandomName : String
                              get() = SimpleDateFormat("dd_MM_yyyy__HH_mm_ss", Locale.getDefault()).format(Date())
                          
                          //fun getTmpImagePath() : String {
                          //    var name= (20..5000).random().toString()+".jpg"
                          //File()
                          //
                          //}
                          
                          fun prepareFileList(filePaths : List<String>, context : Context, folder : File) : File {
                              val fileListContent = filePaths.indices.joinToString("\n") { index ->
                                  "file '${File(folder, "vd$index.mp4").absolutePath}'"
                              }
                              val fileList = File(context.filesDir, "filelist.txt")
                              fileList.writeText(fileListContent)
                              return fileList
                          }
                          
                          fun Context.getBitmapFromUri( uri: Uri): Bitmap? {
                              return try {
                                  val inputStream = contentResolver.openInputStream(uri)
                                  inputStream?.use {
                                      BitmapFactory.Options().run {
                                          inPreferredConfig = Bitmap.Config.ARGB_8888 // Ensure high-quality bitmap
                                          BitmapFactory.decodeStream(it, null, this)
                                      }
                                  } ?: run {
                                      // Log or handle the case where inputStream is null
                                      Log.e("TAG", "getBitmapFromUri: ee", )
                                      null
                                  }
                              } catch (e: Exception) {
                                  Log.e("TAG", "getBitmapFromUri: $e", )
                                  e.printStackTrace() // Log error
                                  null
                              }
                          }
            
                 --- MLKiitUtil.kt
                                   
                          fun Activity.bgDetection(uri : Uri, info : (String) -> Unit) {
                          //        binding.imgState.setImageURI(uri)
                              var progressDialog = ProgressDialog(this)
                              progressDialog.apply {
                                  setTitle("Objects Find")
                                  setCancelable(false)
                                  setMessage("0% Progressing")
                              }
                              if(! isFinishing) {
                                  progressDialog.show()
                              }
                              var process = - 1
                          
                          
                              CoroutineScope(Dispatchers.IO).launch {
                                  var mainProgress = 0
                                  getSelfieSegmentBitmap(uri, this@bgDetection, { selfie, segmentResult ->
                                      takeLabels(selfie) { process, info ->
                                          mainProgress = process
                                          CoroutineScope(Dispatchers.Main).launch {
                                              progressDialog.setMessage("$mainProgress% Progressing")
                                              info(info)
                                              Handler(Looper.getMainLooper()).postDelayed({
                                                  if (!isFinishing) {
                                                      progressDialog.dismiss()
                                                  }
                                              }, 1200)
                                          }
                                      }
                                  }, { it ->
                                      if (it != process) {
                                          process = it
                                          Log.e("TAG", "bgDetection: -progress-> $process")
                                          CoroutineScope(Dispatchers.Main).launch {
                                              progressDialog.setMessage("$process% Progressing")
                                          }
                                      }
                                  })
                              }
                          
                          }
                          
                          fun Activity.takeLabels(bitmap : Bitmap?, process : (Int, String) -> Unit = { _, o -> }) {
                              val image : InputImage
                              try {
                                  image = InputImage.fromBitmap(bitmap !!, 0)
                                  val labeler = ImageLabeling.getClient(ImageLabelerOptions.DEFAULT_OPTIONS)
                          
                          
                                  labeler.process(image).addOnSuccessListener {
                                      Log.e("ML KIT Scope", "takeLabels: Success!")
                                      var info = ""
                                      it.forEachIndexed { index, information ->
                                          info += "${index + 1}:\t${information.text}\n"
                                          Log.e("ML KIT Scope", "takeLabels: lebel ${information.index}:\n\t${information.text}\n\t${information.confidence}")
                                      }
                                      process(99, info)
                          
                                  }.addOnFailureListener {
                                      Log.e("ML KIT Scope", "takeLabels: Fail :${it.message}")
                                  }
                          
                              } catch (e : IOException) {
                                  e.printStackTrace()
                              }
                          
                          
                          }
            
                      ---- const.kt
            
                                        
                          object Constant {
                          
                              var VIDEO_PATH = "video_path"
                          
                          
                              var transitionList = listOf(
                                      "fade",
                                      "fadeblack",
                                      "fadewhite",
                                      "distance",
                                      "wipeleft",
                                      "wiperight",
                                      "wipeup",
                                      "wipedown",
                                      "slideleft",
                                      "slideright",
                                      "slidedown",
                                      "smoothleft",
                                      "smoothright",
                                      "smoothup",
                                      "smoothdown",
                                      "circlecrop",
                                      "rectcrop",
                                      "circleclose",
                                      "circleopen",
                                      "horzclose",
                                      "horzopen",
                                      "vertclose",
                                      "vertopen",
                                      "diagbl",
                                      "diagbr",
                                      "diagtl",
                                      "diagtr",
                                      "hlslice",
                                      "hrslice",
                                      "vuslice",
                                      "vdslice",
                                      "dissolve",
                                      "pixelize",
                                      "radial",
                                      "hblur",
                                      "wipetl",
                                      "wipetr",
                                      "wipebl",
                                      "wipebr",
                                      "squeezev",
                                      "squeezeh",
                                      "zoomin",
                              )
                          }
                          
// Animation Poster Maker a wattermark

      vibrate.xml

      <rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="20"
    android:fromDegrees="-5"
    android:pivotX="50%"
    android:pivotY="50%"
    android:repeatCount="20"
    android:repeatMode="reverse"
    android:toDegrees="5" />



      val paramsAnimation = FrameLayout.LayoutParams(
                    if (bgFrameLayout.width <= bgFrameLayout.height) bgFrameLayout.width / 6 else FrameLayout.LayoutParams.WRAP_CONTENT,
                    if (bgFrameLayout.height <= bgFrameLayout.width) bgFrameLayout.height / 6 else FrameLayout.LayoutParams.WRAP_CONTENT
            )
            paramsAnimation.gravity = Gravity.BOTTOM or Gravity.END
            paramsAnimation.bottomMargin = 25
            paramsAnimation.marginEnd = 25
            bind.ivWaterMarkAnimation.layoutParams = paramsAnimation
            bind.ivWaterMarkAnimation.startAnimation(AnimationUtils.loadAnimation(this, R.anim.vibrate))

            setAnimation()

            
    private var handler: Handler? = null
    private val runnable = Runnable {
        bind.ivWaterMarkAnimation.startAnimation(AnimationUtils.loadAnimation(this, R.anim.vibrate))
        setAnimation()
    }

    private fun setAnimation() {
        handler?.postDelayed(runnable, 3000)
    }
    

// Crop and resize bitmap images resizebitmap cropbitmap

                           fun cropAndResizeBitmap(
                               src: Bitmap?,
                               startX: Int,
                               startY: Int,
                               targetWidth: Int,
                               targetHeight: Int
                           ): Bitmap? {
                               var startX = startX
                               var startY = startY
                               var targetWidth = targetWidth
                               var targetHeight = targetHeight
                               if (src == null || targetWidth <= 0 || targetHeight <= 0 || startX < 0 || startY < 0) {
                                   return null
                               }
                               val srcWidth = src.width
                               val srcHeight = src.height
                       
                               // Ensure crop area stays within source image bounds (avoid out-of-bounds issues)
                               startX = Math.max(0, startX)
                               startY = Math.max(0, startY)
                       
                               // Calculate the maximum possible crop width and height within the source image
                               val maxWidth = srcWidth - startX
                               val maxHeight = srcHeight - startY
                       
                               // Adjust target dimensions if they exceed source image boundaries
                               targetWidth = Math.min(targetWidth, maxWidth)
                               targetHeight = Math.min(targetHeight, maxHeight)
                       
                               // Calculate center point within the crop area
                               val centerX = startX + targetWidth / 2
                               val centerY = startY + targetHeight / 2
                       
                               // Calculate adjusted crop coordinates to maintain center based on target dimensions
                               var cropX = centerX - targetWidth / 2
                               var cropY = centerY - targetHeight / 2
                       
                               // Ensure final crop area stays within source image bounds
                               cropX = Math.max(0, cropX)
                               cropY = Math.max(0, cropY)
                       
                               // Calculate final crop width and height (might be smaller if source is smaller)
                               val cropWidth = Math.min(targetWidth, srcWidth - cropX)
                               val cropHeight = Math.min(targetHeight, srcHeight - cropY)
                       
                               // Create a new cropped bitmap
                               val croppedBitmap: Bitmap
                               croppedBitmap = try {
                                   Bitmap.createBitmap(src, cropX, cropY, cropWidth, cropHeight)
                               } catch (e: OutOfMemoryError) {
                                   Log.e("CropAndResizeBitmap", "Error cropping image: OutOfMemoryError", e)
                                   return null
                               }
                       
                               // Resize the cropped bitmap (optional)
                               var resizedBitmap: Bitmap? = null
                               if (targetWidth != cropWidth || targetHeight != cropHeight) {
                                   resizedBitmap = resizeBitmap(croppedBitmap, targetWidth, targetHeight)
                                   croppedBitmap.recycle() // Recycle croppedBitmap if resized version is needed
                               } else {
                                   resizedBitmap = croppedBitmap // No resizing needed, use the cropped bitmap directly
                               }
                               return resizedBitmap
                           }
                       
                           fun resizeBitmap(src: Bitmap?, targetWidth: Int, targetHeight: Int): Bitmap? {
                               return if (src == null || targetWidth <= 0 || targetHeight <= 0) {
                                   null
                               } else try {
                                   // Create a new resized bitmap with desired dimensions
                                   Bitmap.createScaledBitmap(src, targetWidth, targetHeight, true)
                               } catch (e: OutOfMemoryError) {
                                   Log.e("ResizeBitmap", "Error scaling image: OutOfMemoryError", e)
                                   null
                               }
                           }
                      
                      
                                  val srcBitmap = BitmapFactory.decodeResource(
                                   resources,
                                   com.example.testproject.R.drawable.testforbigimg
                               )
                       
                               val targetWidth = 1440
                               val targetHeight = 2020
                       
                       // Resize and center crop the image
                               srcBitmap.width.log()
                       
                               val croppedBitmap = cropAndResizeBitmap(
                                   srcBitmap,
                                   srcBitmap.width / 2,
                                   srcBitmap.height / 2,
                                   targetWidth,
                                   targetHeight
                               )

// Lassi For Pick Images & Videos
                      
                      
                       https://github.com/Mindinventory/Lassi-Android
                      
                      
                        if (checkStoragePermission()) {
                                      val intent = Lassi(this@HomeActivity)
                                          .with(LassiOption.GALLERY)
                                          .setMaxCount(100)
                                          .setGridSize(3)
                                          .setMinTime(3)
                                          .setMaxTime(300)
                                          .setMinFileSize(0)
                                          .setMaxFileSize(100000)
                                          .setMediaType(MediaType.VIDEO)
                                          .setPlaceHolder(com.lassi.R.drawable.ic_image_placeholder)
                                          .setErrorDrawable(com.lassi.R.drawable.ic_image_placeholder)
                                          .build()
                                      inAppVideoSelectionLauncher.launch(intent)
                                  } else {
                                      toastLong(resources.getString(R.string.please_allow_permission_first_after_you_can_choose_images_or_videos))
                                  }

 https://github.com/Mindinventory/Lassi-Android

           
             if (checkStoragePermission()) {
                           val intent = Lassi(this@HomeActivity)
                               .with(LassiOption.GALLERY)
                               .setMaxCount(100)
                               .setGridSize(3)
                               .setMinTime(3)
                               .setMaxTime(300)
                               .setMinFileSize(0)
                               .setMaxFileSize(100000)
                               .setMediaType(MediaType.VIDEO)
                               .setPlaceHolder(com.lassi.R.drawable.ic_image_placeholder)
                               .setErrorDrawable(com.lassi.R.drawable.ic_image_placeholder)
                               .build()
                           inAppVideoSelectionLauncher.launch(intent)
                       } else {
                           toastLong(resources.getString(R.string.please_allow_permission_first_after_you_can_choose_images_or_videos))
                       }

                             
                               
// PDF Download Code Download PDF Files
           
                
                class DownloadPdfTaskKt(var activity: Activity, var onNext: (File) -> Unit) :
                    AsyncTask<String, Void, File>() {
                    override fun doInBackground(vararg urls: String): File? {
                        val url = urls[0]
                        var pdfFile: File? = null
                        try {
                            val downloadUrl = URL(url)
                            val urlConnection = downloadUrl.openConnection() as HttpURLConnection
                            if (urlConnection.responseCode == HttpURLConnection.HTTP_OK) {
                                // Download and write to storage
                                val filename = url.substring(url.lastIndexOf("/") + 1) // Extract filename
                                val file = File(
                                    (activity.getExternalFilesDir(
                                        Environment.getExternalStoragePublicDirectory(DIRECTORY_DOCUMENTS).toString()
                                    )!!.path)
                                )
                
                //                file.path.log()
                                if (!file.exists()) file.mkdir()
                
                                pdfFile = File(
                                    activity.getExternalFilesDir(
                                        Environment.getExternalStoragePublicDirectory(DIRECTORY_DOCUMENTS)
                                            .path
                                    ), "mysallery.pdf"
                                )
                
                                val outputStream = FileOutputStream(pdfFile)
                                val inputStream: InputStream = BufferedInputStream(urlConnection.inputStream)
                                val buffer = ByteArray(1024)
                                var bytesRead: Int
                                while (inputStream.read(buffer).also { bytesRead = it } != -1) {
                                    outputStream.write(buffer, 0, bytesRead)
                                }
                                outputStream.close()
                                inputStream.close()
                            }
                        } catch (e: Exception) {
                            e.printStackTrace() // Log or handle exceptions
                        }
                        return pdfFile
                    }
                
                    override fun onPostExecute(downloadedFile: File?) {
                        if (downloadedFile != null) {
                            // Handle download success (e.g., show success message, open PDF viewer)
                            onNext(downloadedFile)
                            Toast.makeText(activity, "PDF downloaded successfully!", Toast.LENGTH_SHORT).show()
                            // Open downloaded PDF (consider using a PDF viewer library)
                        } else {
                            // Handle download failure (e.g., show error message)
                            Toast.makeText(activity, "Download failed!", Toast.LENGTH_SHORT).show()
                        }
                    }
                }


        
          var downloapdf = DownloadPdfTaskKt(this) {
                    ("Patgh: " + it.path).log()
                    savefilepath = it.path
                    CoroutineScope(Dispatchers.Main).launch {
                        bind.pdfView.initWithFile(
                            it
                        )
        //                openPdfFromFile(it.path)
                    }
                }
        
                downloapdf.execute(pdfUrl);



                 fun openPdfFromFile(filePath: String) {
        val context = this // Assuming you're calling this from an activity or fragment

        val authority = "$packageName.provider"
        val uri = FileProvider.getUriForFile(context, authority, File(filePath));

        val intent = Intent(Intent.ACTION_VIEW)
        intent.setDataAndType(uri, "application/pdf")
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); // Grant temporary read permission
        startActivity(intent)
    }


        
             fun sharePdfFromFile(pdfPath: String) {
                val context = this // Assuming you're calling this from an activity or fragment
        
                val authority = packageName + ".provider"
                val uri = FileProvider.getUriForFile(context, authority, File(pdfPath));
        
                val shareIntent = Intent(Intent.ACTION_SEND)
                shareIntent.setType("application/pdf")
                shareIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); // Grant temporary read permission
                shareIntent.putExtra(Intent.EXTRA_STREAM, uri);
                startActivity(Intent.createChooser(shareIntent, "Share PDF"));
            }

                
                  <provider
                            android:name="androidx.core.content.FileProvider"
                            android:authorities="${applicationId}.provider"
                            android:exported="false"
                            android:grantUriPermissions="true">
                            <meta-data
                                android:name="android.support.FILE_PROVIDER_PATHS"
                                android:resource="@xml/provider_paths" />
                        </provider>



                        ///  PDF Viewr view pdf libra

                           <com.rajat.pdfviewer.PdfRendererView
            android:id="@+id/pdfView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_above="@+id/shareid"
            app:pdfView_divider="@drawable/pdf_viewer_divider"
            app:pdfView_showDivider="false" />
            
        
          //Pdf View
            implementation("io.github.afreakyelf:Pdf-Viewer:2.1.1")
            
// WhatsApp Sticker add to Whatsapp

        
         Android Menifest
        
           <queries>
        
            <!-- Explicit apps you know in advance about: -->
            <package android:name="com.whatsapp" />
            <package android:name="com.whatsapp.w4b" />
        </queries>
        
        
        
          <provider
                android:name=".foldername.whatsappsticker.StickerContentProvider"
                android:authorities="${contentProviderAuthority}"
                android:exported="true"
                android:readPermission="com.whatsapp.sticker.READ"
                android:grantUriPermissions="true">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW"/>
                    <category android:name="android.intent.category.DEFAULT"/>
                </intent-filter>
                <meta-data
                    android:name="android.support.FILE_PROVIDER_PATHS"
                    android:resource="@xml/provider_path" />
            </provider>
        
        
            provider_path.xml
            
        <paths xmlns:android="http://schemas.android.com/apk/res/android">
            <external-path name="external_files" path="Android/data/com.name.photo.birthday.cake.quotes.frame.editor/files/StickerPacks/"/>
        </paths>
        
        
            ---- First Download a file path and give file path and identifire 
            
          public fun Activity.addStickerPackToWhatsAppKP(stickerPackIdentifier : String?, stickerPackName : String?) {
                Log.e(TAG, "addStickerPackToWhatsAppKP: $stickerPackIdentifier")
                val intent = Intent()
                intent.setAction("com.whatsapp.intent.action.ENABLE_STICKER_PACK")
                intent.putExtra("sticker_pack_id", stickerPackIdentifier)
                intent.putExtra("sticker_pack_authority", BuildConfig.CONTENT_PROVIDER_AUTHORITY)
                intent.putExtra("sticker_pack_name", stickerPackName)
                try {
                    startActivityForResult(intent, ADD_PACK)
                } catch (e : ActivityNotFoundException) {
                    Toast.makeText(this, "WhatsApp not installed.", Toast.LENGTH_SHORT).show()
                }
            }


// Revanue Cat


            
                                      --Splace Screen 
                    
                   RevenueCatHelper.revenueCateInit(this,
                             BuildConfig.REVANUECAT_ID,
                             "Current Plan",
                             object : ProductPurchaseListener {
                                 override fun onPurchase(message: String) {
                                     message.messageLog()
                                 }
                 
                                 override fun onPurchased(purchase: Purchase) {
                                 }
                 
                                 override fun onRevenueCatPurchased(purchases: CustomerInfo) {
                 //                        if(purchases.activeSubscriptions)
                                     Log.e("TAG", "onRevenueCatPurchased: ${purchases.activeSubscriptions}")
                                     Log.e("TAG", "onRevenueCatPurchased: ${purchases.entitlements.active}")
                 
                                     val data = purchases.entitlements.active.toString()
                                     val isActive: Boolean = extractBooleanValue(data, "isActive")
                                     ("isActive: $isActive").log("onRevenueCatPurchased")
                                     SubsSharedFile(this@SplashActivity).isSubscriber =
                                         purchases.activeSubscriptions.isNotEmpty()
                                     subsSharedFile?.purchaseProduct = purchases.toGson()
                                     subsSharedFile?.purchaseProduct?.logPurchase()
                                 }
                 
                                 override fun onPurchaseFail(errorMessage: String) {
                 
                                 }
                             }) {
                             runOnUiThread {
                                 if (isFirstTime) {
                                     isFirstTime = false
                                     ("Time 2222222").log("TIMECHECK")
                                     gotoLoadApp()
                                 }
                             }
                         }
            
            
            
                   ---- Revanue Cat Class
                          
                                                         object RevenueCatHelper {
                              var currentSub: Map<String, EntitlementInfo>? = null
                              var isSplash = true
                          
                              var liverevenuecatedatalist = MutableLiveData<List<RevenueCatData>>()
                              var revenueCatDataList = ArrayList<RevenueCatData>()
                              var allAvailablePackages = ArrayList<MyOffer>()
                              var currentUserPackage = MyOffer()
                          
                              private fun saveJsonToFile(jsonString: String) {
                                  val path = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOCUMENTS)
                                  val file = File(path, "ravanuecat.json")
                                  try {
                                      path.mkdirs()
                                      val fos = FileOutputStream(file)
                                      fos.write(jsonString.toByteArray())
                                      fos.close()
                          //                Toast.makeText(this, "JSON saved to " + file.path, Toast.LENGTH_LONG).show()
                                  } catch (e: IOException) {
                                      e.printStackTrace()
                          //                Toast.makeText(this, "Error saving JSON", Toast.LENGTH_LONG).show()
                                  }
                              }
                          
                              fun revenueCateInit(
                                  context: Activity,
                                  REVENUECAT_GOOGLE_API: String = BuildConfig.REVANUECAT_ID,
                                  abTestPackage: String,
                                  productPurchaseListener: ProductPurchaseListener,
                                  openNextScreen: () -> Unit
                              ) {
                                  StaticParam.isRevenueCate = true
                                  StaticParam.productPurchaseListener = productPurchaseListener
                          
                                  var isSubscribe = false
                          
                                  Purchases.debugLogsEnabled = false
                                  Purchases.configure(
                                      PurchasesConfiguration.Builder(context, REVENUECAT_GOOGLE_API).appUserID(null).build()
                                  )
                                  ("Heree innerrr").log("onRevenueCatPurchased")
                          
                                  Purchases.sharedInstance.getCustomerInfo(
                                      CacheFetchPolicy.NOT_STALE_CACHED_OR_CURRENT,
                                      object : ReceiveCustomerInfoCallback {
                                          override fun onError(error: PurchasesError) {
                                              ("on Received on Error : ${error.message}").log("onRevenueCatPurchased")
                                          }
                          
                                          override fun onReceived(customerInfo: CustomerInfo) {
                                              ("on Received Subscription: ${customerInfo.activeSubscriptions} || inApp : ${customerInfo.entitlements.active}").log(
                                                  "onRevenueCatPurchased"
                                              )
                          
                                              if (customerInfo.activeSubscriptions.isNotEmpty()) {
                                                  isSubscribe = true
                                                  isSubscribedForAllGlobal(context, true)
                                              }/* else if (customerInfo.entitlements.active.isNotEmpty()) {
                                                  isSubscribe = false
                          //                        isSubscribedForAllGlobal(context, true)
                                              } else {
                                                  isSubscribe = true
                                                  isSubscribedForAllGlobal(context, false)
                                              }*/
                          
                                              if (App.getBoolean("isForRevalueFirstTime") == false) {
                                                  App.putBoolean("isForRevalueFirstTime", true)
                          //                    if (!context.isDestroyed) {
                                                  ("Life Time Success: ---  ").log(
                                                      "onRevenueCatPurchased"
                                                  )
                                                  if (!context.isDestroyed && isSubscribe) {
                                                      productPurchaseListener.onRevenueCatPurchased(customerInfo)
                                                      openNextScreen.invoke()
                                                  }
                          //                    }
                                              }
                                          }
                                      })
                          
                                  Purchases.sharedInstance.syncPurchases(object : SyncPurchasesCallback {
                                      override fun onError(error: PurchasesError) {
                                          ("is syncPurchases syncPurchases Time Plan Error: --- ${error.message}").log(
                                              "onRevenueCatPurchased"
                                          )
                          
                                          if (!isSubscribe)
                                              isSubscribedForAllGlobal(context, false)
                          
                                          if (App.getBoolean("isForRevalueFirstTime") == false) {
                                              App.putBoolean("isForRevalueFirstTime", true)
                                              if (!context.isDestroyed) {
                                                  openNextScreen.invoke()
                                              }
                                          }
                                      }
                          
                                      override fun onSuccess(customerInfo: CustomerInfo) {
                                          ("syncPurchases onSuccess : ${customerInfo.entitlements.active} \n Active Sub: ${customerInfo.activeSubscriptions.toGson()} ").log(
                                              "onRevenueCatPurchased"
                                          )
                                          if (customerInfo.activeSubscriptions.isNotEmpty()) {
                                              isSubscribedForAllGlobal(context, true)
                                          } else if (customerInfo.entitlements.active.isNotEmpty()) {
                                              isSubscribedForAllGlobal(context, true)
                                          } else {
                                              isSubscribedForAllGlobal(context, false)
                                          }
                          
                                          if (App.getBoolean("isForRevalueFirstTime") == false) {
                                              App.putBoolean("isForRevalueFirstTime", true)
                          //                    if (!context.isDestroyed) {
                                              ("Life Time Success: ---  ").log(
                                                  "onRevenueCatPurchased"
                                              )
                                              productPurchaseListener.onRevenueCatPurchased(customerInfo)
                                              if (!context.isDestroyed)
                                                  openNextScreen.invoke()
                          //                    }
                                          }
                                      }
                                  })
                          
                                  Purchases.sharedInstance.getOfferings(object : ReceiveOfferingsCallback {
                                      override fun onError(error: PurchasesError) {
                                          "${error}".log("Purchase List error-->")
                                      }
                          
                                      override fun onReceived(offerings: com.revenuecat.purchases.Offerings) {
                                          (offerings.all.toGson()).log("REVANUEPLAN")
                          
                                          if (BuildConfig.DEBUG) saveJsonToFile(Gson().toJson(offerings))
                          
                                          offerings.all.forEach { (s, offering) ->
                                              val offers = MyOffer(s, offering.availablePackages)
                                              allAvailablePackages.add(offers)
                                          }
                          
                                          allAvailablePackages.forEach { myoffer ->
                                              myoffer.packages.forEach {
                                                  val freeTrial = it.product.subscriptionOptions?.freeTrial?.let { it ->
                                                      it.freePhase?.billingPeriod
                                                  }
                                                  revenueCatDataList.add(
                                                      RevenueCatData(
                                                          it.product.id,
                                                          myoffer.key,
                                                          it.packageType,
                                                          it.product.price.formatted,
                                                          it.product.price.amountMicros,
                                                          it.product.period.toString(),
                                                          it,
                                                           FreeTrils(
                                                    freeTrial?.value,
                                                    freeTrial?.unit.toString(),
                                                    freeTrial?.iso8601.toString()
                                                )
                                                      )
                                                  )
                                              }
                                          }
                          
                                          allAvailablePackages.find { it.key == abTestPackage }?.let {
                                              currentUserPackage = it
                                          }
                                          liveMyPlan.postValue(currentUserPackage.key)
                                          currentUserPackage?.packages?.forEach {
                                              val freeTrial = it.product.subscriptionOptions?.freeTrial?.let { it ->
                                                  it.freePhase?.billingPeriod
                                              }
                                              revenueCatDataList.add(
                                                  RevenueCatData(
                                                      it.product.id,
                                                      currentUserPackage.key,
                                                      it.packageType,
                                                      it.product.price.formatted,
                                                      it.product.price.amountMicros,
                                                      it.product.period.toString(),
                                                      it,
                                                       FreeTrils(
                                                    freeTrial?.value,
                                                    freeTrial?.unit.toString(),
                                                    freeTrial?.iso8601.toString()
                                                )
                                                  )
                                              )
                                          }
                          
                                          val myProducts = ArrayList<BillingProduct>()
                          
                                          revenueCatDataList.forEach {
                                              val billingProduct = BillingProduct()
                                              billingProduct.id = it.id
                                              billingProduct.title = it.packageName
                          
                                              it.product.product.let { q ->
                                                  billingProduct.productType =
                                                      if (q.type == ProductType.INAPP) INAPP else SUBS
                                              }
                          
                                              when (it.packageType) {
                                                  PackageType.MONTHLY -> {
                                                      billingProduct.packageType = MyPackageType.MONTH
                                                  }
                          
                                                  PackageType.ANNUAL -> {
                                                      billingProduct.packageType = MyPackageType.ANNUAL
                                                  }
                          
                                                  PackageType.WEEKLY -> {
                                                      billingProduct.packageType = MyPackageType.WEEK
                                                  }
                          
                                                  PackageType.LIFETIME -> {
                                                      billingProduct.packageType = MyPackageType.LIFETIME
                                                  }
                          
                                                  else -> {
                                                      billingProduct.packageType = MyPackageType.SIX_MONTH
                                                  }
                                              }
                                              billingProduct.price = it.price
                                              billingProduct.period = it.period
                                              billingProduct.amountmicros = it.amountmicros
                                              billingProduct.freeTrials = it.freeTrial
                          
                                              myProducts.add(billingProduct)
                                          }
                                          SubscriptionSetter.livePackageProduct.postValue(myProducts)
                                          liverevenuecatedatalist.postValue(revenueCatDataList)
                                      }
                                  })
                          
                                  if (App.getBoolean("isForRevalueFirstTime") == true && !context.isDestroyed) openNextScreen.invoke()
                              }
                          
                              fun RestorePurchase(activity: Activity, onNext: () -> Unit) {
                          
                                  if (!isOnline(activity)) {
                                      Toast.makeText(
                                          activity, activity.getString(R.string.please_connect_internet), Toast.LENGTH_SHORT
                                      ).show()
                                      return
                                  }
                          
                                  if (Purchases.isConfigured && activity.window != null && activity.windowManager != null) {
                                      Purchases.sharedInstance.restorePurchases(object : ReceiveCustomerInfoCallback {
                                          override fun onError(error: PurchasesError) {
                                              "onRevenueCatPurchased".log("Error--> $error")
                                              activity.runOnUiThread {
                                                  Snackbar.make(
                                                      activity.findViewById(android.R.id.content),
                                                      activity.getString(R.string.nothing_to_restore),
                                                      Snackbar.LENGTH_SHORT
                                                  ).apply {
                                                      setActionTextColor(Color.WHITE)
                                                      show()
                                                  }
                                              }
                                              onNext()
                                          }
                          
                                          override fun onReceived(customerInfo: CustomerInfo) {
                                              ("restorePurchases $customerInfo").log("onRevenueCatPurchased")
                                              if (customerInfo?.activeSubscriptions?.isNotEmpty() == true) {
                                                  isSubscribedForAllGlobal(activity, true)
                                                  onNext()
                                                  activity.setResult(Activity.RESULT_OK)
                                                  activity.onBackPressed()
                                              } else {
                                                  if (customerInfo.entitlements?.active?.isNotEmpty() == true) {
                                                      isSubscribedForAllGlobal(activity, true)
                                                      onNext()
                                                      activity.setResult(Activity.RESULT_OK)
                                                      activity.onBackPressed()
                                                  } else {
                                                      onNext()
                                                      activity.runOnUiThread {
                                                          Snackbar.make(
                                                              activity.findViewById(android.R.id.content),
                                                              activity.getString(R.string.nothing_to_restore),
                                                              Snackbar.LENGTH_SHORT
                                                          ).apply {
                                                              setActionTextColor(Color.WHITE)
                                                              show()
                                                          }
                                                      }
                                                  }
                                              }
                                          }
                                      })
                                  } else {
                                      Purchases.configure(
                                          PurchasesConfiguration.Builder(activity, BuildConfig.REVANUECAT_ID).build()
                                      )
                                  }
                              }
                          
                              fun purchaseMonth(context: Activity, onSuccess: (StoreTransaction, CustomerInfo) -> Unit) {
                          //        var purchaseProduct = allAvailablePackages.find { it.key == key }
                                  var product = revenueCatDataList.find { it.packageType == PackageType.MONTHLY }
                                  if (product != null) {
                          //            var product = purchaseProduct.packages.find { it.packageType == PackageType.MONTHLY }
                                      product?.let {
                                          Purchases.sharedInstance.purchaseWith(PurchaseParams.Builder(
                                              context, product.product
                                          ).build(), { error: PurchasesError, userCancelled: Boolean ->
                                              Log.e("TAG", "initview:MonthPackage $error")
                                          }, { purchase, customerInfo ->
                                              purchase?.let { onSuccess.invoke(it, customerInfo) }
                                          })
                                      }
                                  } else {
                                      Log.e("TAG", "initview:purchaseProduct null!")
                          
                                  }
                              }
                          
                              fun purchaseOnTime(
                                  context: Activity,
                                  productkey: PackageType,
                                  onSuccess: (StoreTransaction, CustomerInfo) -> Unit
                              ) {
                          //        var purchaseProduct = allAvailablePackages.find { it.key == key }
                                  val product = revenueCatDataList.find { it.packageType == productkey }
                                  if (product != null) {
                                      product?.let {
                                          Purchases.sharedInstance.purchaseWith(PurchaseParams.Builder(
                                              context, product.product
                                          ).build(), { error: PurchasesError, userCancelled: Boolean ->
                                              Log.e("TAG", "initview:MonthPackage $error")
                                          }, { purchase, customerInfo ->
                                              purchase?.let { onSuccess.invoke(it, customerInfo) }
                                          })
                                      }
                                  } else {
                                      Log.e("TAG", "initview:purchaseProduct null!")
                                  }
                              }
                          }
                       
                 @Keep
                 data class BillingProduct(
                     var id: String = "",
                     var title: String = "",
                     var description: String = "",
                     var packageType: MyPackageType? = null,
                     var price: String = "",
                     var amountmicros: Long = 0L,
                     var amountmicrostoPriceInt: Int = 0,
                     var period: String = "",
                     var freeTrials: FreeTrils? = null,
                     var productType: String = BillingClient.ProductType.SUBS,
                 )
                 
                 @Keep
                 data class BillingINAPPProduct(
                     var id: String = "",
                     var title: String = "",
                     var description: String = "",
                     var packageType: MyPackageType? = null,
                     var price: String = "",
                     var amountmicros: Long = 0L,
                     var amountmicrostoPriceInt: Int = 0,
                     var period: String = "",
                     var productType: String = BillingClient.ProductType.SUBS,
                 )
                 
                 @Keep
                 data class ProductBillingIDS(var id: String, var billingType: String)
            
             
                          @Keep
                          data class RevenueCatData(
                              var id : String,
                              var packageName:String,
                              var packageType : PackageType,
                              var price : String,
                              var amountmicros : Long,
                              var period : String,
                              var product : Package,
                              var freeTrial : FreeTrils = FreeTrils()
                          )
                          
                          @Keep
                          data class FreeTrils(var period : Int? = null, var TimeType : String = "", var i8 : String = "")
                          @Keep
                          data class MyOffer(var key : String="", var packages : List<Package> = listOf())
            
                 ----- SubscriptionSetter.clas
            
                                 
                        object SubscriptionSetter {
                            var livePackageProduct = MutableLiveData<List<BillingProduct>>()
                            var liveMyInAppProducts = MutableLiveData<List<BillingINAPPProduct>>()
                        
                            var liveMyPlan = MutableLiveData<String>()
                        
                            fun makeMySubs(
                                activity: Activity,
                                packageType: MyPackageType,
                                onSuccess: (StoreTransaction, CustomerInfo) -> Unit
                            ) {
                                if (StaticParam.isRevenueCate) {
                                    when (packageType) {
                                        MyPackageType.MONTH -> {
                                            RevenueCatHelper.purchaseOnTime(activity, PackageType.MONTHLY, onSuccess)
                                        }
                        
                                        MyPackageType.ANNUAL -> {
                                            RevenueCatHelper.purchaseOnTime(activity, PackageType.ANNUAL, onSuccess)
                                        }
                        
                                        MyPackageType.SIX_MONTH -> {
                                            RevenueCatHelper.purchaseOnTime(activity, PackageType.SIX_MONTH, onSuccess)
                                        }
                        
                                        MyPackageType.WEEK -> {
                                            RevenueCatHelper.purchaseOnTime(activity, PackageType.WEEKLY, onSuccess)
                                        }
                        
                                        else -> {
                                            RevenueCatHelper.purchaseOnTime(activity, PackageType.SIX_MONTH, onSuccess)
                                        }
                                    }
                                } else {
                                    BillingHelper.makePurchase(packageType)
                                }
                            }
                        
                            fun makeMyProduct(
                                activity: Activity,
                                packageType: MyPackageType/* here packageType is Product's  */,
                                onSuccess: (StoreTransaction, CustomerInfo) -> Unit
                            ) {
                                if (StaticParam.isRevenueCate) {
                                    RevenueCatHelper.purchaseOnTime(activity, getRevenuePackage(packageType), onSuccess)
                                } else {
                                    BillingHelper.makeProductPurchase(packageType)
                                }
                            }
                            fun getRevenuePackage(packageType : MyPackageType) : PackageType {
                                if(packageType == MyPackageType.MONTH) {
                                    return PackageType.MONTHLY
                                } else if(packageType == MyPackageType.ANNUAL) {
                                    return PackageType.ANNUAL
                                } else if(packageType == MyPackageType.SIX_MONTH) {
                                    return PackageType.SIX_MONTH
                                } else if(packageType == MyPackageType.WEEK) {
                                    return PackageType.WEEKLY
                                } else if(packageType == MyPackageType.LIFETIME) {
                                    return PackageType.LIFETIME
                                } else {
                                    return PackageType.UNKNOWN
                                }
                            }
                        }
            
            
                     
                    --- Subscription Act
            
                       -- For LifeTime ------
            
                         SubscriptionSetter.makeMyProduct(
                                this@NewForYearSubAct,
                                MyPackageType.LIFETIME,
                                { storeTransaction, customerInfo -> })
            
                       --- For Plan Subscription ----
                       
                      SubscriptionSetter.makeMySubs(
                this@NewForYearSubAct, MyPackageType.WEEK
            ) { storeTransaction, customerInfo ->
                isSubscribeFun(this@NewForYearSubAct, true)
                ("onSuccess WEEK").log("onRevenueCatPurchased")
            }

              SubscriptionSetter.makeMyProduct(
                    this@NewForYearSubAct, MyPackageType.LIFETIME
                ) { storeTransaction, customerInfo ->

                    ("onSuccess LifeTime").log("onRevenueCatPurchased")

                    isSubscribeFun(this@NewForYearSubAct, true)

                }
                
                            purchaseListener.postValue(false)
            
                    purchaseListener.observe(this@NewForYearSubAct) { purchase ->
                        //finish
                        ("Purchasing: $purchase").log()
            
                        if (purchase) {
                            setResult(Activity.RESULT_OK)
                            isSubscribedForAllGlobal(this@NewForYearSubAct, purchase)
                            onBackPressed()
                        }
                    }
            
            
            
                    ---- Live All Produt Detail 
            
                         val plan =
                                            RevenueCatHelper.revenueCatDataList.find { it.packageType == PackageType.SIX_MONTH }
                                        if (plan?.freeTrial?.period != null) {
                                            ("Free Trial ANNUAL").log("onRevenueCatPurchased")
                                            bind.startfreetrybtn.text = getString(R.string.start_free_trial)
                                        } else {
                                            bind.startfreetrybtn.text = getString(R.string.continue_name)
                                            ("NON Trial ANNUAL").log("onRevenueCatPurchased")
                                        }
            
            
            
                                    -------- Live Package Products Details. 
                        
                                                     SubscriptionSetter.livePackageProduct.observe(
                                        this@NewForYearSubAct,
                                        object : Observer<List<BillingProduct>?> {
                                            @SuppressLint("SetTextI18n")
                                            override fun onChanged(billingProducts: List<BillingProduct>?) {
                                                billingProducts?.let { productsList ->
                                                    try {
                                                        if (!NetworkHelper.isOnline(this@NewForYearSubAct)) return
                        
                                                        val annualPlan =
                                                            productsList.find { it.packageType == MyPackageType.ANNUAL }
                                                        val weekPlan =
                                                            productsList.find { it.packageType == MyPackageType.WEEK }
                                                        val sixPlan =
                                                            productsList.find { it.packageType == MyPackageType.SIX_MONTH }
                        
                                                        val lifetime =
                                                            productsList.find { it.packageType == MyPackageType.LIFETIME && it.productType == BillingClient.ProductType.INAPP }
                        
                                                        lifetime?.let {
                                                            ("Sucsess INAPP Data: ${it.toGson()}").log("FATZ")
                                                            bind.lifetimebtn.visibility = View.VISIBLE
                                                            bind.lifetimepriceid.text =
                                                                removeTrailingZeros(lifetime?.price!!)
                        
                                                            if (!isOnline(this@NewForYearSubAct)) {
                                                                bind.lifetimepriceid.text = "₹2,000"
                                                            }
                                                        }
                                                        
                        //                            bind.txtPurchaseMonth.text = monthPlan?.title
                        //                            bind.priserid.text = weekPlan?.title
                        //                            binding.thirdidyearly.text = sixPlan?.title
                        
                                                        bind.tvPriceyear.text = annualPlan?.price
                                                        bind.priserid.text = weekPlan?.price
                                                        bind.sixmonthjid.text = sixPlan?.price
                                    
                                                                    ("Micro Annual: " + annualPlan!!.amountmicros + " | Week: " + weekPlan!!.amountmicrostoPriceInt + " | 6 Month: " +                         sixPlan!!.amountmicrostoPriceInt).log(
                                                                        "FATZ"
                                                        )
                        
                                                        //todo: Revanue Cat Change divided
                                                        val annulemicro = (annualPlan?.amountmicros?.div(1000000))
                                                        val weekmicro = (weekPlan?.amountmicros?.div(1000000))
                                                        val sixmonthmicro = (sixPlan?.amountmicros?.div(1000000))
                        
                                                        bind.weeklytvlongtvid.text =
                                                            "• ${getString(R.string.weeksubtv)} : ${weekPlan?.price}"
                                                        bind.monthlytvlongtvid.text =
                                                            "• ${getString(R.string.sixmonthsubtv)} : ${sixPlan?.price}"
                                                        bind.yearlytvlongtvid.text =
                                                            "• ${getString(R.string.yearsubtv)} : ${annualPlan?.price}"
                        
                                                        if (annulemicro != null) {
                                                            bind.montbottomid.text =
                                                                "${getString(R.string.permonth)} \n ₹${annulemicro / 12}/-"
                                                        }
                                                        if (sixmonthmicro != null) {
                                                            bind.sixmonthbottom.text =
                                                                "${getString(R.string.perweek)} \n ₹${sixmonthmicro / 24}/-"
                                                        }
                        
                                                        if (weekmicro != null) {
                                                            bind.linabc.text = "₹${(weekmicro * 24)}/-"
                                                        }
                                                        if (annulemicro != null) {
                                                            bind.linabcde.text = "₹${(annulemicro * 48)}/-"
                                                        }
                        
                                                        if (sixmonthmicro != null) {
                                                            val yearperchantage =
                                                                ((24 * weekmicro!!) - sixmonthmicro).toDouble()
                                                            val sec = (yearperchantage * 100 / (24 * weekmicro)).toDouble()
                        
                                                            ("Six: " + sec).log()
                        
                                                            bind.sixmonthtvbg.text = "${(sec).roundToInt()}${
                                                                getString(
                                                                    R.string.off
                                                                )
                                                            }"
                                                        }
                        
                                                        if (annulemicro != null) {
                        
                                                            val yearperchantage =
                                                                (52 * weekmicro!!) - annulemicro.toDouble()
                                                            val sec = yearperchantage * 100 / (52 * weekmicro).toDouble()
                        
                                                            ("Annul: " + sec).log()
                        
                                                            bind.monthtvdid.text = "${(sec).roundToInt()}${
                                                                getString(
                                                                    R.string.off
                                                                )
                                                            }"
                                                        }
                        
                                                        if (annulemicro != null) {
                                                            ("Name: " + ((annulemicro * 100) / (annulemicro * 48))).log()
                                                        }
                        
                                                        val isFreeTrial = productsList.any { it.price == "Free" }
                                                        ("isFreeTrial $isFreeTrial").log()
                        
                                                        if (isFreeTrial) {
                                                            App.putBoolean("isFreeTry", true)
                                                            bind.startfreetrybtn.text =
                                                                getSubscribeText(this@NewForYearSubAct, true)
                                                        } else {
                                                            App.putBoolean("isFreeTry", false)
                                                            bind.startfreetrybtn.text =
                                                                getSubscribeText(this@NewForYearSubAct, false)
                                                        }
                                                    } catch (e: Exception) {
                                                        e.printStackTrace()
                                                    }
                                                }
                                            }
                                        })

                                                                
                                    fun removeTrailingZeros(text: String): String {
                                return if (text.endsWith(".00")) {
                                    text.substring(0, text.length - 3)
                                } else {
                                    text
                                }
                            }
                            
                              @SuppressLint("StringFormatMatches")
                                private fun getSubTrial(trial: String): String? {
                                    //Parse I8
                                    return try {
                                        val size = trial.length
                                        val period = trial.substring(1, size - 1)
                                        val str = trial.substring(size - 1, size)
                                        Log.d("TAG", "getSubTrial <------------------>: ${size} $period - $str")
                                        when (str) {
                                            "D" -> resources.getString(R.string.days, period)
                                            "M" -> resources.getString(R.string.months, period)
                                            "Y" -> resources.getString(R.string.months, (period.toInt() * 12))
                                            "W" -> resources.getString(R.string._7_day)
                                            else -> resources.getString(R.string.months, period)
                                        }
                                    } catch (e: Exception) {
                                        resources.getString(R.string._12_months)
                                    }
                                }
                                        


// Connection Live Data

        
            class ConnectionLiveData(val context: Context) : LiveData<Boolean>() {
            
                private var connectivityManager: ConnectivityManager = context.getSystemService(CONNECTIVITY_SERVICE) as ConnectivityManager
            
                private lateinit var connectivityManagerCallback: ConnectivityManager.NetworkCallback
            
                private lateinit var  networkRequestBuilder: NetworkRequest.Builder
                
                @SuppressLint("MissingPermission")
                override fun onActive() {
                    super.onActive()
                    updateConnection()
                    when {
                        Build.VERSION.SDK_INT >= Build.VERSION_CODES.N -> connectivityManager.registerDefaultNetworkCallback(
                            getConnectivityMarshmallowManagerCallback()
                        )
                        Build.VERSION.SDK_INT >= Build.VERSION_CODES.M -> marshmallowNetworkAvailableRequest()
                        else -> lollipopNetworkAvailableRequest()
                    }
                }
            
                override fun onInactive() {
                    super.onInactive()
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                        try { connectivityManager.unregisterNetworkCallback(connectivityManagerCallback) }catch (_: Exception){}
                    }
                }
            
                @SuppressLint("MissingPermission")
                private fun lollipopNetworkAvailableRequest() {
                    if(!::networkRequestBuilder.isInitialized) {
                        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                            networkRequestBuilder = NetworkRequest.Builder()
                                .addTransportType(NetworkCapabilities.TRANSPORT_CELLULAR)
                                .addTransportType(NetworkCapabilities.TRANSPORT_WIFI)
                        }
                    }
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                        connectivityManager.registerNetworkCallback(
                            networkRequestBuilder.build(),
                            getConnectivityLollipopManagerCallback()
                        )
                    }
                }
            
                @SuppressLint("MissingPermission")
                @TargetApi(Build.VERSION_CODES.M)
                private fun marshmallowNetworkAvailableRequest() {
                    if(!::networkRequestBuilder.isInitialized) {
                        networkRequestBuilder = NetworkRequest.Builder()
                            .addTransportType(NetworkCapabilities.TRANSPORT_CELLULAR)
                            .addTransportType(NetworkCapabilities.TRANSPORT_WIFI)
                    }
                    connectivityManager.registerNetworkCallback(
                        networkRequestBuilder.build(),
                        getConnectivityMarshmallowManagerCallback()
                    )
                }
            
                private fun getConnectivityLollipopManagerCallback(): ConnectivityManager.NetworkCallback {
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                        connectivityManagerCallback = object : ConnectivityManager.NetworkCallback() {
                            override fun onAvailable(network: Network) {
                                postValue(true)
                            }
            
                            override fun onLost(network: Network) {
                                postValue(false)
                            }
                        }
                    }
                    return connectivityManagerCallback
                }
            
                private fun getConnectivityMarshmallowManagerCallback(): ConnectivityManager.NetworkCallback {
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                        connectivityManagerCallback = object : ConnectivityManager.NetworkCallback() {
                            override fun onCapabilitiesChanged(
                                network: Network,
                                networkCapabilities: NetworkCapabilities
                            ) {
                                networkCapabilities.let { capabilities ->
                                    if (capabilities.hasCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET) && capabilities.hasCapability(
                                            NetworkCapabilities.NET_CAPABILITY_VALIDATED
                                        )
                                    ) {
                                        postValue(true)
                                    }
                                }
                            }
            
                            override fun onLost(network: Network) {
                                postValue(false)
                            }
                        }
                        return connectivityManagerCallback
                    } else {
                        throw IllegalAccessError("Accessing wrong API version")
                    }
                }
            
                private val networkReceiver = object : BroadcastReceiver() {
                    override fun onReceive(context: Context, intent: Intent) {
                        updateConnection()
                    }
                }
            
                @SuppressLint("MissingPermission")
                private fun updateConnection() {
                    val activeNetwork: NetworkInfo? = connectivityManager.activeNetworkInfo
                    postValue(activeNetwork?.isConnected == true)
                }
            }
        
        
        // Activity class 
        
        
         connectionLiveData = ConnectionLiveData(requireActivity())
                connectionLiveData?.observe(requireActivity()) { isConnected ->
                    isConnected?.let {
                        if (it) {
                            Handler(Looper.myLooper()!!).postDelayed({
                                loadNative()
                            }, 100)
                        } else {
                            binding.apply{
                                noAdsImg.visible()
                                FBAdContainer.gone()
                                mframlayout.gone()
                            }
                        }
                    }
                }
                
    
// InApp Update
    
     Lib
            configurations {
           all {
               resolutionStrategy {
                   // Exclude specific modules causing conflicts
                   exclude group: 'com.google.android.play', module: 'core'
               }
           }
       }
    
    implementation 'com.google.android.play:app-update:2.1.0'
    
       class InAppUpdate(activity: Activity) : InstallStateUpdatedListener {
       
           private var appUpdateManager: AppUpdateManager
           private val MY_REQUEST_CODE = 500
           private var parentActivity: Activity = activity
       
           private var currentType = AppUpdateType.FLEXIBLE
       
           init {
               appUpdateManager = AppUpdateManagerFactory.create(parentActivity)
               appUpdateManager.appUpdateInfo.addOnSuccessListener { info ->
       //            ("Isupdatecheck $info").log()
                   // Check if update is available
                   if (info.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE) {
                       // UPDATE IS AVAILABLE
                       if (info.availableVersionCode() > BuildConfig.VERSION_CODE) {
                           startUpdate(info, AppUpdateType.FLEXIBLE)
                       }
       
       //                if (info.updatePriority() == 5) { // Priority: 5 (Immediate update flow)
       //                    if (info.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)) {
       //                        startUpdate(info, AppUpdateType.IMMEDIATE)
       //                    }
       //                } else if (info.updatePriority() == 4) { // Priority: 4
       //                    val clientVersionStalenessDays = info.clientVersionStalenessDays()
       //                    if (clientVersionStalenessDays != null && clientVersionStalenessDays >= 5 && info.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)) {
       //                        // Trigger IMMEDIATE flow
       //                        startUpdate(info, AppUpdateType.IMMEDIATE)
       //                    } else if (clientVersionStalenessDays != null && clientVersionStalenessDays >= 3 && info.isUpdateTypeAllowed(AppUpdateType.FLEXIBLE)) {
       //                        // Trigger FLEXIBLE flow
       //                        startUpdate(info, AppUpdateType.FLEXIBLE)
       //                    }
       //                } else if (info.updatePriority() == 3) { // Priority: 3
       //                    val clientVersionStalenessDays = info.clientVersionStalenessDays()
       //                    if (clientVersionStalenessDays != null && clientVersionStalenessDays >= 30 && info.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)) {
       //                        // Trigger IMMEDIATE flow
       //                        startUpdate(info, AppUpdateType.IMMEDIATE)
       //                    } else if (clientVersionStalenessDays != null && clientVersionStalenessDays >= 15 && info.isUpdateTypeAllowed(AppUpdateType.FLEXIBLE)) {
       //                        // Trigger FLEXIBLE flow
       //                        startUpdate(info, AppUpdateType.FLEXIBLE)
       //                    }
       //                } else if (info.updatePriority() == 2) { // Priority: 2
       //                    val clientVersionStalenessDays = info.clientVersionStalenessDays()
       //                    if (clientVersionStalenessDays != null && clientVersionStalenessDays >= 90 && info.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)) {
       //                        // Trigger IMMEDIATE flow
       //                        startUpdate(info, AppUpdateType.IMMEDIATE)
       //                    } else if (clientVersionStalenessDays != null && clientVersionStalenessDays >= 30 && info.isUpdateTypeAllowed(AppUpdateType.FLEXIBLE)) {
       //                        // Trigger FLEXIBLE flow
       //                        startUpdate(info, AppUpdateType.FLEXIBLE)
       //                    }
       //                } else if (info.updatePriority() == 1) { // Priority: 1
       //                    // Trigger FLEXIBLE flow
       //                    startUpdate(info, AppUpdateType.FLEXIBLE)
       //                } else { // Priority: 0
       //                    // Do not show in-app update
       //                }
                   } else {
                       // UPDATE IS NOT AVAILABLE
                   }
               }
               appUpdateManager.registerListener(this)
           }
       
           private fun startUpdate(info: AppUpdateInfo, type: Int) {
               appUpdateManager.startUpdateFlowForResult(info, type, parentActivity, MY_REQUEST_CODE)
               currentType = type
           }
       
           fun onResume() {
               appUpdateManager.appUpdateInfo.addOnSuccessListener { info ->
                   if (currentType == AppUpdateType.FLEXIBLE) {
                       // If the update is downloaded but not installed, notify the user to complete the update.
                       if (info.installStatus() == InstallStatus.DOWNLOADED) flexibleUpdateDownloadCompleted()
                   } else if (currentType == AppUpdateType.IMMEDIATE) {
                       // for AppUpdateType.IMMEDIATE only, already executing updater
                       if (info.updateAvailability() == UpdateAvailability.DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS) {
                           startUpdate(info, AppUpdateType.IMMEDIATE)
                       }
                   }
               }
           }
       
           fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
               if (requestCode == MY_REQUEST_CODE) {
                   if (resultCode != AppCompatActivity.RESULT_OK) {
                       // If the update is cancelled or fails, you can request to start the update again.
                       Log.e("ERROR", "Update flow failed! Result code: $resultCode")
                   }
               }
           }
       
           private fun flexibleUpdateDownloadCompleted() {
               Snackbar.make(
                   parentActivity.findViewById(android.R.id.content),
                   "An update has just been downloaded.",
                   Snackbar.LENGTH_INDEFINITE
               ).apply {
                   setAction("RESTART") { appUpdateManager.completeUpdate() }
                   setActionTextColor(Color.WHITE)
                   show()
               }
           }
       
           fun onDestroy() {
               appUpdateManager.unregisterListener(this)
           }
       
           override fun onStateUpdate(state: InstallState) {
               if (state.installStatus() == InstallStatus.DOWNLOADED) {
                   flexibleUpdateDownloadCompleted()
               }
           }
       }
    
       in MainAct
           private var inAppUpdate: InAppUpdate? = null
    
    
        inAppUpdate = InAppUpdate(this)
    
        inAppUpdate?.onActivityResult(requestCode, resultCode, data)
        inAppUpdate?.onResume()
        inAppUpdate?.onDestroy()
        
// WrapHeightViewPager

       public class WrapHeightViewPager extends ViewPager {
           public WrapHeightViewPager(Context context) {
               super(context);
           }
       
           public WrapHeightViewPager(Context context, AttributeSet attrs) {
               super(context, attrs);
           }
       
           @Override
           protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
               int height = 0;
               View view = null;
               for (int i = 0; i < getChildCount(); i++) {
                   view = getChildAt(i);
                   view.measure(widthMeasureSpec, MeasureSpec.makeMeasureSpec(0, MeasureSpec.UNSPECIFIED));
                   int h = view.getMeasuredHeight();
                   if (h > height) height = h;
               }
       
               if (height != 0) {
                   heightMeasureSpec = MeasureSpec.makeMeasureSpec(height, MeasureSpec.EXACTLY);
               }
               super.onMeasure(widthMeasureSpec, heightMeasureSpec);
               setMeasuredDimension(getMeasuredWidth(), measureHeight(heightMeasureSpec, view));
           }
       
           private int measureHeight(int measureSpec, View view) {
               int result = 0;
               int specMode = MeasureSpec.getMode(measureSpec);
               int specSize = MeasureSpec.getSize(measureSpec);
       
               if (specMode == MeasureSpec.EXACTLY) {
                   result = specSize;
               } else {
                   // set the height from the base view if available
                   if (view != null) {
                       result = view.getMeasuredHeight();
                   }
                   if (specMode == MeasureSpec.AT_MOST) {
                       result = Math.min(result, specSize);
                   }
               }
               return result;
           }
       }


// Safty Net

    
      // play intigrity
          coreLibraryDesugaring("com.android.tools:desugar_jdk_libs:2.0.3")
         implementation("com.google.apis:google-api-services-playintegrity:v1-rev20220211-1.32.1")
         implementation("com.google.apis:google-api-services-playintegrity:v1-rev20231109-2.0.0")
         //  Google Authentication
         implementation("com.google.api-client:google-api-client-jackson2:1.20.0")
         implementation("com.google.auth:google-auth-library-credentials:1.20.0")
         implementation 'com.google.auth:google-auth-library-credentials:1.20.0'
         implementation("com.google.auth:google-auth-library-oauth2-http:1.20.0")
         implementation("com.google.android.play:integrity:1.3.0")
         implementation 'com.scottyab:rootbeer-lib:0.1.0'
    
         in asset -> addJson File 
         in Build.gradl File ->     
         buildConfigField("String", "CLOUD_PROJECT_NUM", "100200")
    
       buildTypes {
             release {
                 minifyEnabled true
                 shrinkResources true
     //            signingConfig signingConfigs.release
                 proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
                 firebaseCrashlytics {
                     nativeSymbolUploadEnabled true
                 }
                 signingConfig signingConfigs.debug
                 buildConfigField("String", "CLOUD_PROJECT_NUM", "\"00000\"")
     //            signingConfig signingConfigs.debug
                 ndk {
                     abiFilters 'x86', 'x86_64', 'armeabi-v7a', 'arm64-v8a'
                 }
             }
     
             debug {
                 buildConfigField("String", "CLOUD_PROJECT_NUM", "\"00000\"")
                 minifyEnabled false
                 shrinkResources false
                 proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
                 firebaseCrashlytics {
                     nativeSymbolUploadEnabled false
                 }
                 ndk {
                     abiFilters 'armeabi-v7a', 'arm64-v8a', 'x86'
                 }
     //            ndk {
     //                abiFilters 'x86', 'x86_64', 'armeabi-v7a', 'arm64-v8a'
     //            }
             }
         }
    
               packagingOptions {
             exclude("META-INF/DEPENDENCIES")
             exclude("META-INF/LICENSE")
             exclude("META-INF/LICENSE.txt")
             exclude("META-INF/license.txt")
             exclude("META-INF/NOTICE")
             exclude("META-INF/NOTICE.txt")
             exclude("META-INF/notice.txt")
             exclude("META-INF/ASL2.0")
             exclude("META-INF/*.kotlin_module")
         }
    
            
        /*
        
        
        -keep class com.google.api.services.playintegrity.** { *; } #REQUIRED
        -keep class com.google.api.client.** { *; } #REQUIRED
        
        
        private fun playSafety() {
                Protectivity(this, BuildConfig.CLOUD_PROJECT_NUM.toLong(), {
                    initview()
                }, {
                    startActivity(Intent(Intent.ACTION_VIEW, Uri.parse("https://play.google.com/store/apps/details?id=$packageName")))
                })
            }
        
                buildConfigField("String", "CLOUD_PROJECT_NUM", "\"787207734962\"")
        
                coreLibraryDesugaringEnabled = true
        
        *  //Play integrity
            implementation("com.google.android.play:integrity:1.3.0")
        
            implementation 'com.google.android.gms:play-services-auth:20.7.0'
            implementation("com.google.apis:google-api-services-playintegrity:v1-rev20231109-2.0.0") {}
            //  Google Authentication and api Access
            implementation("com.google.api-client:google-api-client-jackson2:1.20.0")
            implementation 'com.google.auth:google-auth-library-credentials:1.20.0'
            implementation("com.google.auth:google-auth-library-oauth2-http:1.20.0")
        
            //desugar
            coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:2.0.4'
        * */
        
        class Protectivity {
            private var onNext : () -> Unit
            private var onReCheck : () -> Unit
            var activity : Activity
            private lateinit var interPin : StandardIntegrityManager.StandardIntegrityToken
        
            constructor(activity : Activity, cloudProjectNumber : Long, onNext : () -> Unit, onReCheck : () -> Unit) {
                this.onNext = onNext
                this.onReCheck = onReCheck
                this.activity = activity
        
                if(activity.isOnlineed) {
                    val standardIntegrityManager : StandardIntegrityManager = IntegrityManagerFactory.createStandard(activity)
                    var integrityTokenProvider : StandardIntegrityManager.StandardIntegrityTokenProvider
                    val standardIntegrityResponse = standardIntegrityManager.prepareIntegrityToken(
                            StandardIntegrityManager.PrepareIntegrityTokenRequest.builder().setCloudProjectNumber(cloudProjectNumber).build()
                    )
                    standardIntegrityResponse.addOnSuccessListener {
                        integrityTokenProvider = it
                        val integrityTokenResponse : Task<StandardIntegrityManager.StandardIntegrityToken>? = integrityTokenProvider.request(
                                StandardIntegrityManager.StandardIntegrityTokenRequest.builder().setRequestHash(generateNonce()).build()
                        )
                        integrityTokenResponse?.addOnSuccessListener { response ->
                            interPin = response
                            decryptToken(response.token())
                        }?.addOnFailureListener {
                            rootHunter(activity)
                        }
                    }.addOnFailureListener {
                        "Token Exception $it".logPlay()
                        rootHunter(activity)
                    }
        
                } else {
                    rootHunter(activity)
                }
            }
        
            private fun decryptToken(token : String) {
        
                val requestObj = DecodeIntegrityTokenRequest()
                requestObj.integrityToken = token
                val credentials = GoogleCredentials.fromStream(activity.assets.open("credentials.json"))
                val requestInitializer : HttpRequestInitializer = HttpCredentialsAdapter(credentials)
                val mHttpTransport = NetHttpTransport()
                val mJacksonFactory = JacksonFactory()
                val mInitializer : GoogleClientRequestInitializer = PlayIntegrityRequestInitializer()
        
        
                val playIntegrity =
                    PlayIntegrity.Builder(mHttpTransport, mJacksonFactory, requestInitializer).setApplicationName(activity.resources.getString(
                        R.string.app_name))
                        .setGoogleClientRequestInitializer(mInitializer)
                val play = playIntegrity.build()
        
                CoroutineScope(Dispatchers.IO).launch {
                    try {
                        val response = play.v1().decodeIntegrityToken(activity.packageName, requestObj).execute()
                        "response $response".logPlay()
                        recognizer(response)
                    } catch (e : Exception) {
                        "response Exception :$e".logPlay()
                        rootHunter(activity)
                    }
                }
            }
        
            private fun recognizer(response : DecodeIntegrityTokenResponse) {
        
                val appLicensingVerdict = response.tokenPayloadExternal.accountDetails.appLicensingVerdict
        
                val appRecognitionVerdict = response.tokenPayloadExternal.appIntegrity.appRecognitionVerdict
        
                val deviceRecognitionVerdict = response.tokenPayloadExternal.deviceIntegrity.deviceRecognitionVerdict
                val isSafeLast =
                    response.tokenPayloadExternal.accountDetails.appLicensingVerdict.isNotEmpty() && appLicensingVerdict.contains("LICENSED") && appRecognitionVerdict.contains(
                            "PLAY_RECOGNIZED"
                    ) && deviceRecognitionVerdict.contains("MEETS_DEVICE_INTEGRITY")
                if(isSafeLast) {
                    CoroutineScope(Dispatchers.Main).launch {
                        onNext.invoke()
                    }
        
                } else {
                    CoroutineScope(Dispatchers.Main).launch {
                        this@Protectivity.playDialog(isLICENSED = getDialogTypeCode(appLicensingVerdict))
                    }
                }
            }
        
            private fun generateNonce() : String {
                val length = 50
                var nonce = ""
                val allowed = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"
                for (i in 0 until length) {
                    nonce += allowed[floor(Math.random() * allowed.length).toInt()].toString()
                }
                return nonce
            }
        
            private fun rootHunter(activity : Activity, respond : () -> Unit = onNext) {
                val rootBeer = RootBeer(activity)
                if(! rootBeer.isRooted || ! rootBeer.isRootedWithBusyBoxCheck) {
                    CoroutineScope(Dispatchers.Main).launch {
                        respond.invoke()
                    }
                } else {
                    CoroutineScope(Dispatchers.Main).launch {
                        if(! activity.isFinishing) {
                            PlayInitDialog(activity)
                        }
                    }
                }
            }
        
            private fun playDialog(isLICENSED : Int) {
                interPin.showDialog(activity, /*isLICENSED*/ GET_LICENSED).addOnCanceledListener {
                    "Dialog closed".logPlay()
                }.addOnSuccessListener {
                    "==> ${it}".logPlay()
                }.addOnCompleteListener {
                    if(it.result == DIALOG_SUCCESSFUL) {
                        "DIALOG_SUCCESSFUL".logPlay()
                        onReCheck.invoke()
                    }
                    if(it.result == DIALOG_CANCELLED) {
                        "DIALOG_CANCELLED".logPlay()
                        activity.finishAffinity()
                        exitProcess(0)
                    }
                    if(it.result == DIALOG_FAILED) {
                        "DIALOG_FAILED".logPlay()
                    }
                    if(it.result == DIALOG_UNAVAILABLE) {
                        "DIALOG_UNAVAILABLE".logPlay()
                    }
                }.addOnFailureListener {
                    "dialog Exception = ${it.message}".logPlay()
                }
            }
        
            private fun getDialogTypeCode(appLicensingVerdict : String) : Int {
                return if(appLicensingVerdict == "UNLICENSED") {
                    GET_LICENSED
                } else 0
            }
        }
          
          //todo: Need to Add Below Lib
          //implementation("com.google.apis:google-api-services-playintegrity:v1-rev20220211-1.32.1")
          //    implementation("com.google.apis:google-api-services-playintegrity:v1-rev20231109-2.0.0")
          //    //  Google Authentication
          //    implementation("com.google.api-client:google-api-client-jackson2:1.20.0")
          ////    implementation ("com.google.auth:google-auth-library-credentials:1.20.0")
          //    implementation 'com.google.auth:google-auth-library-credentials:1.20.0'
          //    implementation("com.google.auth:google-auth-library-oauth2-http:1.20.0")
          //implementation("com.google.android.play:integrity:1.3.0")
          //
          //configurations {
          //    all {
          //        resolutionStrategy {
          //            // Exclude specific modules causing conflicts
          //            exclude group: 'com.google.android.play', module: 'core'
          //        }
          //    }
          //}
          //implementation 'com.google.android.play:app-update:2.1.0'
          
          //-keep class com.google.api.services.playintegrity.** { *; } #REQUIRED
          //-keep class com.google.api.client.** { *; } #REQUIRED
          
          
          //  PlaySafetyNetByFenil(this, 787207734962) {
          //            //(Your Next Code)
          //        }.sft()
          
          
          // Asset-> add json credentials.json
    
     PlayInitDialog.kt
     
     class PlayInitDialog {
         constructor(activity: Activity) {
     
     //        init {
             var dialog: Dialog = Dialog(activity)
             var bind: PlayinitLayoutBinding = PlayinitLayoutBinding.inflate(activity.layoutInflater)
             dialog.setContentView(bind.root)
             dialog.window!!.setBackgroundDrawableResource(android.R.color.transparent)
             dialog.setCancelable(false)
             dialog.show()
     
             bind.apply {
                 btnExit.click {
                     activity.finishAffinity()
                 }
                 btnInstallNow.click {
                     try {
                         activity.startActivity(
                             Intent(
                                 Intent.ACTION_VIEW,
                                 Uri.parse("market://details?id=${activity.packageName}")
                             )
                         )
                     } catch (e: ActivityNotFoundException) {
                         activity.startActivity(
                             Intent(
                                 Intent.ACTION_VIEW,
                                 Uri.parse("https://play.google.com/store/apps/details?id=${activity.packageName}")
                             )
                         )
                     }
     //                }
                 }
             }
         }
     }
    
     playinit_layout.xml
    
          <androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
         xmlns:cardview="http://schemas.android.com/apk/res-auto"
         android:layout_width="match_parent"
         android:layout_height="wrap_content"
         android:layout_gravity="top|bottom|left|right|center_vertical|fill_vertical|center_horizontal|fill_horizontal|center|fill|start|end"
         android:layout_marginLeft="10dp"
         android:layout_marginRight="10dp"
         android:elevation="5dp"
         android:gravity="top|bottom|left|right|center_vertical|fill_vertical|center_horizontal|fill_horizontal|center|fill|start|end"
         android:layoutDirection="ltr"
         android:orientation="vertical"
         cardview:cardCornerRadius="20dp"
         cardview:cardElevation="5dp"
         cardview:cardUseCompatPadding="true">
     
         <LinearLayout
             android:layout_width="match_parent"
             android:layout_height="wrap_content"
             android:background="@color/white"
             android:orientation="vertical">
     
             <LinearLayout
                 android:layout_width="match_parent"
                 android:layout_height="wrap_content"
                 android:layout_gravity="top|bottom|left|right|center_vertical|fill_vertical|center_horizontal|fill_horizontal|center|fill|start|end"
                 android:gravity="top|bottom|left|right|center_vertical|fill_vertical|center_horizontal|fill_horizontal|center|fill|start|end"
                 android:orientation="vertical">
     
                 <TextView
                     android:id="@+id/dialogTitle"
                     android:layout_width="match_parent"
                     android:layout_height="wrap_content"
                     android:layout_marginTop="10dp"
                     android:layout_marginBottom="10dp"
                     android:fontFamily="@font/poppins"
                     android:gravity="center"
                     android:text="Alert"
                     android:textColor="#FF0000"
                     android:textSize="25sp" />
     
                 <TextView
                     android:id="@+id/dialogMessage"
                     android:layout_width="match_parent"
                     android:layout_height="wrap_content"
                     android:layout_marginHorizontal="10dp"
                     android:layout_marginBottom="10dp"
                     android:fontFamily="@font/poppins"
                     android:gravity="center|left"
                     android:justificationMode="inter_word"
                     android:text="This app is not licensed or legally available on the Play Store. We kindly ask you to uninstall the current app. Please search for on the Play Store and install the official app to ensure a legal and authorized experience."
                     android:textColor="@color/black"
                     android:textSize="15sp" />
     
                 <LinearLayout
                     android:layout_width="match_parent"
                     android:layout_height="wrap_content"
                     android:layout_marginStart="15dp"
                     android:layout_marginTop="5dp"
                     android:layout_marginEnd="15dp"
                     android:layout_marginBottom="15dp"
                     android:orientation="horizontal">
     
                     <TextView
                         android:id="@+id/btnExit"
                         android:layout_width="match_parent"
                         android:layout_height="wrap_content"
                         android:layout_marginLeft="10dp"
                         android:layout_marginRight="10dp"
                         android:layout_weight="1"
                         android:background="@drawable/edround"
                         android:backgroundTint="#FF0000"
                         android:fontFamily="@font/poppins"
                         android:gravity="center"
                         android:padding="7dp"
                         android:text="Exit"
                         android:textAllCaps="true"
                         android:textColor="@color/white"
                         android:textSize="13sp"
                         android:textStyle="bold" />
     
                     <TextView
                         android:id="@+id/btnInstallNow"
                         android:layout_width="match_parent"
                         android:layout_height="wrap_content"
                         android:layout_marginLeft="10dp"
                         android:layout_marginRight="10dp"
                         android:layout_weight="1"
                         android:background="@drawable/edround"
                         android:backgroundTint="#F44336"
                         android:ellipsize="end"
                         android:fontFamily="@font/poppins"
                         android:gravity="center"
                         android:padding="7dp"
                         android:singleLine="true"
                         android:text="Install Now"
                         android:textAllCaps="true"
                         android:textColor="@color/white"
                         android:textSize="13sp"
                         android:textStyle="bold" />
     
                 </LinearLayout>
     
             </LinearLayout>
     
         </LinearLayout>
     
     </androidx.cardview.widget.CardView>
    
    // Now in SplaceScreen
    
      if (NetworkHelper.isOnline(this)) {
                        PlaySafety(this, BuildConfig.CLOUD_PROJECT_NUM.toLong()) {
                            if (isOpenSearchAct) {
                                openActivity(SearchDataActivity::class.java) {
                                    putString("keyword", firebase_value)
                                    putBoolean("isFromNotify", true)
                                }
                                finish()
                            } else {
                                //            openActivity(MainActivity::class.java)
                                if (isSubScribe) openActivity(MainActivity::class.java)
                                else openSubscription()
                            }
                            overridePendingTransition(0, 0)
                        }.sft()
                    } else {
                        PlaySafety(this, BuildConfig.CLOUD_PROJECT_NUM.toLong()) {
    
                            if (isOpenSearchAct) {
                                openActivity(SearchDataActivity::class.java) {
                                    putString("keyword", firebase_value)
                                    putBoolean("isFromNotify", true)
                                }
                                finish()
                            } else {
                                //            openActivity(MainActivity::class.java)
                                if (isSubScribe) openActivity(MainActivity::class.java)
                                else openSubscription()
                            }
                            overridePendingTransition(0, 0)
                        }.cxroot()
                    }


// Subscription & Revanue Cat class
                    
                    
                    implementation 'com.revenuecat.purchases:purchases:7.0.0'
                    implementation "com.android.billingclient:billing-ktx:6.1.0"
                      
    
     ------ Splace Screen -------
    
       var productIds = Arrays.asList(
        ProductBillingIDS("subscribe_weekly_kriadl", BillingClient.ProductType.SUBS),
        ProductBillingIDS("subscribe_sixly_kriadl", BillingClient.ProductType.SUBS),
        ProductBillingIDS("subscribe_yearly_kriadl", BillingClient.ProductType.SUBS),
        ProductBillingIDS("subscribe_lifetime_kriadl", BillingClient.ProductType.INAPP)
    )
    
    ---------- call onCreate() -------
    
           BillingHelper.getBillingInit(this, productIds, object : ProductPurchaseListener {
                override fun onPurchase(message: String) {
                    ("onPurchase: $message").log()
    //                message.messageLog()
                }
    
                override fun onRevenueCatPurchased(purchases: CustomerInfo) {
    
                }
    
                override fun onPurchased(purchase: Purchase) {
                    Log.wtf("FATZ", "-- purchase: " + purchase.purchaseState)
    //                subsSharedFile.isSubscriber = (purchase.purchaseState != -1)
    //                subsSharedFile.purchaseProduct = purchase.toGson()
    //                subsSharedFile.purchaseProduct.logPurchase()
                    val isSubscriber = (purchase.purchaseState != -1)
                    isSubscribeFun(isSubscriber)
                }
    
                override fun onPurchaseFail(errorMessage: String) {
    
                }
            }) { isSubscribe ->
                ("is Here Initialized is Subscribe: $isSubscribe").log()
                runOnUiThread {
                    if (isFirstTime) {
                        isFirstTime = false
                        gotoLoadApp()
                    }
                }
            }
    
    
       private fun isSubscribeFun(isSubscribe: Boolean) {
            Log.d("FATZ", "is Subscription: $isSubscribe")
            StaticParam.purchaseListener.postValue(isSubscribe)
    
            isSubscribedForAllGlobal(this, isSubscribe)
    
    //        MySharedPreferences(this).isSubscribe = if (BuildConfig.DEBUG) true else isSubscribe
    //        isSubScribe = if (BuildConfig.DEBUG) true else isSubscribe
    //        isSubScribeFromPicker = if (BuildConfig.DEBUG) true else isSubscribe
    
            //        MySharedPreferences(this).isSubscribe = false
    //        isSubScribe = false
    //        isSubScribeFromPicker = false
        }
    
    
            --------- BillingHelper Class -------
            
               implementation 'com.revenuecat.purchases:purchases:7.0.0'
implementation "com.android.billingclient:billing-ktx:6.1.0"

 ------ Splace Screen -------

   var productIds = Arrays.asList(
    ProductBillingIDS("subscribe_weekly_kriadl", BillingClient.ProductType.SUBS),
    ProductBillingIDS("subscribe_sixly_kriadl", BillingClient.ProductType.SUBS),
    ProductBillingIDS("subscribe_yearly_kriadl", BillingClient.ProductType.SUBS),
    ProductBillingIDS("subscribe_lifetime_kriadl", BillingClient.ProductType.INAPP)
)

---------- call onCreate() -------

       BillingHelper.getBillingInit(this, productIds, object : ProductPurchaseListener {
            override fun onPurchase(message: String) {
                ("onPurchase: $message").log()
//                message.messageLog()
            }

            override fun onRevenueCatPurchased(purchases: CustomerInfo) {

            }

            override fun onPurchased(purchase: Purchase) {
                Log.wtf("FATZ", "-- purchase: " + purchase.purchaseState)
//                subsSharedFile.isSubscriber = (purchase.purchaseState != -1)
//                subsSharedFile.purchaseProduct = purchase.toGson()
//                subsSharedFile.purchaseProduct.logPurchase()
                val isSubscriber = (purchase.purchaseState != -1)
                isSubscribeFun(isSubscriber)
            }

            override fun onPurchaseFail(errorMessage: String) {

            }
        }) { isSubscribe ->
            ("is Here Initialized is Subscribe: $isSubscribe").log()
            runOnUiThread {
                if (isFirstTime) {
                    isFirstTime = false
                    gotoLoadApp()
                }
            }
        }


   private fun isSubscribeFun(isSubscribe: Boolean) {
        Log.d("FATZ", "is Subscription: $isSubscribe")
        StaticParam.purchaseListener.postValue(isSubscribe)

        isSubscribedForAllGlobal(this, isSubscribe)

//        MySharedPreferences(this).isSubscribe = if (BuildConfig.DEBUG) true else isSubscribe
//        isSubScribe = if (BuildConfig.DEBUG) true else isSubscribe
//        isSubScribeFromPicker = if (BuildConfig.DEBUG) true else isSubscribe

        //        MySharedPreferences(this).isSubscribe = false
//        isSubScribe = false
//        isSubScribeFromPicker = false
    }


        --------- BillingHelper Class -------
        
                        object BillingHelper {
            var billingClient: BillingClient? = null
            var productIds: List<ProductBillingIDS> = listOf(
                ProductBillingIDS("subscribe_weekly_kriadl", BillingClient.ProductType.SUBS),
                ProductBillingIDS("subscribe_sixly_kriadl", BillingClient.ProductType.SUBS),
                ProductBillingIDS("subscribe_yearly_kriadl", BillingClient.ProductType.SUBS),
                ProductBillingIDS("subscribe_lifetime_kriadl", BillingClient.ProductType.INAPP)
            )
        
            var myInAppProducts = ArrayList<BillingINAPPProduct>()
        
            var myProducts = ArrayList<BillingProduct>()
            var liveMyProducts = MutableLiveData<List<BillingProduct>>()
            private var isConsumable: Boolean = false
            var mySubProducts = ArrayList<BillingProduct>()
        
            fun getBillingInit(
                billContextAct: Activity,
                billingKeys: List<ProductBillingIDS>,
                productPurchaseListener: ProductPurchaseListener,
                onNextAct: (Boolean) -> Unit
            ) {
                StaticParam.productPurchaseListener = productPurchaseListener
                productIds = billingKeys
                billContext = billContextAct
                getBillingHelp(onNextAct)
            }
        
            //    var productIds = Arrays.asList("body_editor_monthly", "body_editor_yearly")
        
            fun getBillingHelp(onNextAct: (Boolean) -> Unit) {
                val purchasesUpdatedListener = PurchasesUpdatedListener { billingResult, purchases ->
                    "Client Purchase Response : ${billingResult.responseCode}".bilLog()
                }
                billingClient =
                    BillingClient.newBuilder(billContext!!).setListener(purchasesUpdatedListener)
                        .enablePendingPurchases().setListener { billingResult, purchases ->
                            when (billingResult.responseCode) {
                                BillingClient.BillingResponseCode.OK -> purchases?.forEach {
                                    handlePurchase(it)
                                }
        
                                BillingClient.BillingResponseCode.ITEM_ALREADY_OWNED -> {
                                    ("Item Already Owned").log()
                                }
        
                                else -> {
                                    when (billingResult.responseCode) {
                                        BillingClient.BillingResponseCode.USER_CANCELED -> {
                                            toastMaker(
                                                billContext!!,
                                                "You've cancelled the Google play billing process"
                                            )
                                            productPurchaseListener?.onPurchaseFail("You've cancelled the Google play billing process")
                                        }
                                    }
                                    if (billingResult.responseCode !in arrayOf(
                                            BillingClient.BillingResponseCode.USER_CANCELED,
                                            BillingClient.BillingResponseCode.OK
                                        )
                                    ) {
                                        toastMaker(
                                            billContext!!, "Item not found or Google play billing error"
                                        )
                                        productPurchaseListener?.onPurchaseFail("Item not found or Google play billing error")
                                    }
                                }
                            }
                        }.build()
        
                billingClient?.startConnection(object : BillingClientStateListener {
                    override fun onBillingSetupFinished(billingResult: BillingResult) {
        
                        "Client Setup Response : ${billingResult.responseCode == BillingClient.BillingResponseCode.OK}".bilLog()
                        ("Connection ").log("FATZ")
        
                        if (billingResult.responseCode == BillingClient.BillingResponseCode.OK) {
        
                            CoroutineScope(Dispatchers.IO).launch {
                                subSkuPurchases(billingResult)
                                inAppSkuPurchases(billingResult)
                            }
        
                            billingClient?.queryPurchasesAsync(
                                QueryPurchasesParams.newBuilder()
                                    .setProductType(BillingClient.ProductType.SUBS).build()
                            ) { _, purchases ->
                                purchases.size.toString().log()
                                if (purchases.size > 0) {
                                    ("Item Purchase").log("FATZ")
                                    isSubscribedForAllGlobal(billContext!!, true)
                                    StaticParam.purchaseListener.postValue(true)
                                    onNextAct.invoke(true)
                                } else {
                                    ("11 Item Not Purchase | Res Code: ${billingResult.responseCode}").log("FATZ")
        //                        isSubscribedForAllGlobal(billContext!!, false)
        //                        isSubscribeNewModul.value = false
                                    billingClient?.queryPurchasesAsync(
                                        QueryPurchasesParams.newBuilder()
                                            .setProductType(BillingClient.ProductType.INAPP).build()
                                    ) { _, purchases ->
                                        purchases.size.toString().log()
                                        if (purchases.size > 0) {
                                            ("11 Item Purchas in App").log("FATZ")
                                            isSubscribedForAllGlobal(billContext!!, true)
                                            StaticParam.purchaseListener.postValue(true)
                                            onNextAct.invoke(true)
                                        } else {
                                            ("11 Item Not Purchase in APP | Res Code: ${billingResult.responseCode}").log(
                                                "FATZ"
                                            )
                                            isSubscribedForAllGlobal(billContext!!, false)
                                            onNextAct.invoke(false)
                                        }
                                    }
                                }
                            }
        //                    onNextAct.invoke(isSubScribe)
                        } else {
                            isSubscribedForAllGlobal(billContext!!, false)
                            onNextAct.invoke(false)
                        }
                    }
        
                    override fun onBillingServiceDisconnected() {
                        "Client Disconnected ".bilLog()
                        CoroutineScope(Dispatchers.Main).launch {
                            isSubscribedForAllGlobal(billContext!!, false)
                            onNextAct.invoke(false)
                        }
                    }
                })
            }
        
        //    fun checkRestore(isRestoreOrNot:(Boolean) -> Unit) {
        //        billingClient.startConnection(object : BillingClientStateListener {
        //            override fun onBillingSetupFinished(billingResult: BillingResult) {
        ////                "Client Setup Response : ${billingResult.responseCode == BillingClient.BillingResponseCode.OK}".bilLog()
        //                ("Check Restore Connection ").log("FATZ")
        //                billingClient.queryPurchasesAsync(
        //                    QueryPurchasesParams.newBuilder().setProductType(BillingClient.ProductType.SUBS)
        //                        .build()
        //                ) { _, purchases ->
        //                    purchases.size.toString().log()
        //                    if (purchases.size > 0) {
        //                        ("Item Purchase").log("FATZ")
        //                        isSubscribedForAllGlobal(billContext!!, true)
        //                        StaticParam.purchaseListener.postValue(true)
        //                    } else {
        //                        ("Item Not Purchase | Res Code: ${billingResult.responseCode}").log("FATZ")
        //                        isSubscribedForAllGlobal(billContext!!, false)
        ////                        isSubscribeNewModul.value = false
        //                        if (billingResult.responseCode == BillingClient.BillingResponseCode.OK) {
        //                            CoroutineScope(Dispatchers.IO).launch {
        //                                subSkuPurchases(billingResult)
        //                                inAppSkuPurchases(billingResult)
        //                            }
        //                        }
        //                    }
        //                }
        //            }
        //
        //            override fun onBillingServiceDisconnected() {
        //                "Client Disconnected ".bilLog()
        //            }
        //        })
        //    }
        
            private suspend fun subSkuPurchases(billingResult: BillingResult) {
                billingClient?.queryPurchasesAsync(
                    QueryPurchasesParams.newBuilder().setProductType(BillingClient.ProductType.SUBS).build()
                ) { _, purchases ->
                    purchases.size.toString().log()
        
                    if (purchases.size > 0) {
                        ("Item Subscription Purchase").log("FATZ")
                    } else {
                        ("Item Subscription Not Purchase").log("FATZ")
                        if (billingResult.responseCode == BillingClient.BillingResponseCode.OK) {
                            retriveMySKU()
                        }
                    }
                }
            }
        
            fun getSkuProduct(
                queryParam: QueryProductDetailsParams, listener: ProductDetailsResponseListener
            ) {
                billingClient?.queryProductDetailsAsync(queryParam, listener)
            }
        
            fun makeProductPurchase(packageType: MyPackageType) {
                if (StaticParam.subsSharedFile?.isSubscriber == true) {
                    toastMaker(billContext!!, "Already Subscribed!")
                    return
                }
        
                if (productIds.isEmpty()) return
        
                if (packageType == MyPackageType.LIFETIME) {
                    val queryProductDetailsParams = newBuilder().setProductList(
                        listOf(
                            Product.newBuilder().setProductId(productIds[3].id)
                                .setProductType(productIds[3].billingType).build()
                        )
                    ).build()
                    getSkuProduct(queryProductDetailsParams) { billingResult, productDetails ->
                        val fetchedProduct = fetchProduct(productIds[3].id, productDetails)
                        fetchedProduct?.let {
                            val productDetailsParamsList = listOf(
                                BillingFlowParams.ProductDetailsParams.newBuilder().setProductDetails(it)
                                    .build()
                            )
        
                            val billingFlowParams = BillingFlowParams.newBuilder()
                                .setProductDetailsParamsList(productDetailsParamsList).build()
                            val resultBill =
                                billingClient?.launchBillingFlow(billContext!!, billingFlowParams)
        
                            when (resultBill?.responseCode) {
                                BillingClient.BillingResponseCode.ITEM_ALREADY_OWNED -> {
                                    "Billing result: ITEM_ALREADY_OWNED".bilLog()
                                    productPurchaseListener?.onPurchase("Product Already Purchased!")
                                }
        
                                BillingClient.BillingResponseCode.OK -> {
                                    "Billing result: OK".bilLog()
                                    productPurchaseListener?.onPurchase("Product Available.")
                                }
        
                                else -> {
                                    "No Billing result".bilLog()
                                    productPurchaseListener?.onPurchaseFail("No Purchase Made!")
                                }
                            }
                        }
                    }
        
        // Launch the billing flow
                } else {
                }
            }
        
            private suspend fun inAppSkuPurchases(billingResult: BillingResult) {
                billingClient?.queryPurchasesAsync(
                    QueryPurchasesParams.newBuilder().setProductType(BillingClient.ProductType.INAPP)
                        .build()
                ) { _, purchases ->
                    purchases.size.toString().log()
        
                    if (purchases.size > 0) {
                        ("Item Purchase {INAPP}").log("FATZ")
                    } else {
                        ("Item Not Purchase {INAPP}").log("FATZ")
                        if (billingResult.responseCode == BillingClient.BillingResponseCode.OK) {
                            retriveMySkuInApp()
                        }
                    }
                }
            }
        
            private fun retriveMySkuInApp() {
                val queryProductDetailsParams = newBuilder().setProductList(
                    listOf(
        //                        Product.newBuilder().setProductId(productIds[0].id).setProductType(productIds[0].billingType).build(),
        //                        Product.newBuilder().setProductId(productIds[1].id).setProductType(productIds[1].billingType).build(),
        //                        Product.newBuilder().setProductId(productIds[2].id).setProductType(productIds[2].billingType).build(),
                        Product.newBuilder().setProductId(productIds[3].id)
                            .setProductType(productIds[3].billingType).build()
                    )
                ).build()
                getSkuProduct(queryProductDetailsParams) { billingResult, productDetailsList ->
        //            Log.e("TAG", "retriveMySkuInApp: \n${productDetailsList.toGson()}\n")
                    productDetailsList.forEach { product ->
                        Log.e("TAG", "retriveMySkuInApp: \n${product.toGson()}\n")
        
                        val billingProduct = BillingINAPPProduct(product.productId)
                        billingProduct.id = product.productId
                        billingProduct.title = product.title
                        billingProduct.description = product.description
                        billingProduct.productType = product.productType
                        // app's Product is One time purchasable, mean it's one kind of LIFETIME For Lifetime Subscription and PRODUCT for single
                        billingProduct.packageType =
                            if (product.title.contains("Lifetime")) MyPackageType.LIFETIME else MyPackageType.PRODUCT
                        billingProduct.price = product.oneTimePurchaseOfferDetails?.formattedPrice ?: ""
                        billingProduct.amountmicros =
                            product.oneTimePurchaseOfferDetails?.priceAmountMicros ?: 0L
                        myInAppProducts.add(billingProduct)
                    }
                    SubscriptionSetter.liveMyPlan.postValue("Google Play Plan")
        
                    liveMyInAppProducts.postValue(myInAppProducts)
                    "myInAppProducts  :  $myInAppProducts".bilLog()
                }
            }
        
            private fun retriveMySKU() {
                val queryProductDetailsParams = newBuilder().setProductList(
                    listOf(
                        Product.newBuilder().setProductId(productIds[0].id)
                            .setProductType(productIds[0].billingType).build(),
                        Product.newBuilder().setProductId(productIds[1].id)
                            .setProductType(productIds[1].billingType).build(),
                        Product.newBuilder().setProductId(productIds[2].id)
                            .setProductType(productIds[2].billingType).build(),
        //                        Product.newBuilder().setProductId(productIds[3].id).setProductType(productIds[3].billingType).build()
                    )
                ).build()
        
                getSkuProduct(queryProductDetailsParams) { billingResult, productDetailsList ->
                    Log.e("TAG", "retriveMySKU: \n${productDetailsList.toGson()}\n")
                    mySubProducts.clear()
                    productDetailsList.forEach {
                        val product = it
                        product.subscriptionOfferDetails?.forEach { subsDet ->
                            subsDet.pricingPhases.pricingPhaseList.forEachIndexed { index, pricingPhase ->
                                val billingProduct = BillingProduct(it.productId)
                                pricingPhase.billingPeriod.messageLog()
                                billingProduct.title = it.title
                                if (pricingPhase.billingPeriod == "P1W") {
                                    billingProduct.packageType = MyPackageType.WEEK
                                } else if (pricingPhase.billingPeriod == "P1Y") {
                                    billingProduct.packageType = MyPackageType.ANNUAL
                                } else if (pricingPhase.billingPeriod == "P6M") {
                                    billingProduct.packageType = MyPackageType.SIX_MONTH
                                } else if (pricingPhase.billingPeriod == "P1M") {
                                    billingProduct.packageType = MyPackageType.MONTH
                                }
                                Log.e("Purchase -->", "retriveMySKU: ${it.toGson()}\n")
        
                                billingProduct.period = pricingPhase.billingPeriod
                                billingProduct.price = pricingPhase.formattedPrice
                                billingProduct.amountmicros = pricingPhase.priceAmountMicros
                                billingProduct.amountmicrostoPriceInt =
                                    pricingPhase.priceAmountMicros.div(1000000.0).toInt()
        //                        billingProduct.amountmicros = pricingPhase.priceAmountMicros
                                mySubProducts.add(billingProduct)
                            }
                        }
                    }
                    SubscriptionSetter.liveMyPlan.postValue("Google Play Plan")
                    livePackageProduct.postValue(mySubProducts)
                    "mySubProducts : $mySubProducts".bilLog()
                }
            }
        
            fun makePurchase(type: MyPackageType) {
                if (StaticParam.subsSharedFile?.isSubscriber == true) {
                    toastMaker(billContext!!, "Already Subscribed!")
                    return
                }
        
                if (productIds.isNotEmpty()) when (type) {
                    MyPackageType.WEEK -> {
                        purchaseMyProduct(productIds[0])
                    }
        
                    MyPackageType.SIX_MONTH -> {
                        purchaseMyProduct(productIds[1])
                    }
        
                    MyPackageType.ANNUAL -> {
                        purchaseMyProduct(productIds[2])
                    }
        
                    MyPackageType.LIFETIME -> {
                        purchaseMyProduct(productIds[3])
                    }
        
                    else -> {
        
                    }
                }
            }
        
            private fun purchaseMyProduct(productIDS: ProductBillingIDS) {
                val queryProductDetailsParams = newBuilder().setProductList(
                    listOf(
                        Product.newBuilder().setProductId(productIDS.id)
                            .setProductType(productIDS.billingType).build()
                    )
                ).build()
                getSkuProduct(queryProductDetailsParams) { billingResult, productDetails ->
        //            Log.e("TAG", "purchaseMyProduct: ${productDetails.toGson()}")
                    val fetchedProduct = fetchProduct(productIDS.id, productDetails)
                    fetchedProduct?.let { product ->
                        Log.e(
                            "TAG",
                            "purchaseMyProduct: 1  ${product.oneTimePurchaseOfferDetails?.toGson()}  "
                        )
        
                        val offerToken = product.subscriptionOfferDetails?.get(0)?.offerToken
                        offerToken?.let { token ->
                            Log.e("TAG", "purchaseMyProduct: 2 $token")
        
                            val productDetailsParams = BillingFlowParams.ProductDetailsParams.newBuilder()
                                .setProductDetails(product).setOfferToken(token).build()
        
                            val billingFlowParams = BillingFlowParams.newBuilder()
                                .setProductDetailsParamsList(mutableListOf(productDetailsParams)).build()
        
                            val resultBill =
                                billingClient?.launchBillingFlow(billContext!!, billingFlowParams)
        
                            when (resultBill?.responseCode) {
                                BillingClient.BillingResponseCode.ITEM_ALREADY_OWNED -> {
                                    "Billing result: ITEM_ALREADY_OWNED".bilLog()
                                    productPurchaseListener?.onPurchase("Product Already Purchased!")
                                }
        
                                BillingClient.BillingResponseCode.OK -> {
                                    "Billing result: OK".bilLog()
                                    productPurchaseListener?.onPurchase("Product Success Fully Purchased.")
                                }
        
                                else -> {
                                    "No Billing result".bilLog()
                                    productPurchaseListener?.onPurchaseFail("No Purchase Made!")
                                }
                            }
                        }
                    }
                }
            }
        
            private fun fetchProduct(key: String, productDetails: List<ProductDetails>): ProductDetails? {
                var product = productDetails.find { it.productId == key }
                return product
            }
        
            private fun handlePurchase(purchase: Purchase) {
        
                Log.e("TAG", "handlePurchase: ${purchase.toGson()}")
                if (purchase.purchaseState == Purchase.PurchaseState.PURCHASED) {
                    for (product in purchase.products) {
                        productIds.forEach {
                            if (it.id == product) {
                                productPurchaseListener?.onPurchased(purchase)
                            }
                        }
                    }
                }
                CoroutineScope(Dispatchers.IO).launch {
                    acknowledgePurchase(purchase = purchase)
                    if (isConsumable) {
                        consumePurchase(purchase = purchase)
                    }
                }
            }
        
            private suspend fun acknowledgePurchase(purchase: Purchase) {
                if (purchase.purchaseState == Purchase.PurchaseState.PURCHASED && !purchase.isAcknowledged) {
                    val acknowledgePurchaseParams =
                        AcknowledgePurchaseParams.newBuilder().setPurchaseToken(purchase.purchaseToken)
        
                    val ackPurchaseResult = withContext(Dispatchers.IO) {
                        billingClient?.acknowledgePurchase(acknowledgePurchaseParams.build(),
                            object : AcknowledgePurchaseResponseListener {
                                override fun onAcknowledgePurchaseResponse(acknowledgePurchaseResult: BillingResult) {
                                    "acknowledgePurchase: ${acknowledgePurchaseResult.responseCode}".bilLog()
                                }
                            })
                    }
        
                    if (ackPurchaseResult != null) {
                        "acknowledgePurchase: ".bilLog()
                    } else {
                        "acknowledgePurchase: =>> Not Found Any Purchase Result".bilLog()
                    }
                }
            }
        
            fun consumePurchase(purchase: Purchase) {
                val consumeParams =
                    ConsumeParams.newBuilder().setPurchaseToken(purchase.purchaseToken).build()
                billingClient?.let {
                    it.consumeAsync(consumeParams, object : ConsumeResponseListener {
                        override fun onConsumeResponse(consumeResult: BillingResult, p1: String) {
                            "consumePurchase: ${consumeResult.responseCode}".bilLog()
                        }
                    })
                }
            }
        }

            
            @Keep
            enum class MyPackageType {
                ANNUAL, MONTH, SIX_MONTH, WEEK, LIFETIME, PRODUCT
            }
    
    
    interface ProductPurchaseListener {
        fun onPurchase(message : String)
        fun onPurchased(purchase : Purchase)
        fun onRevenueCatPurchased(purchases : CustomerInfo)
        fun onPurchaseFail(errorMessage : String)
    }
    
    
        ----- SubscriptionSetter.kt
        
        object SubscriptionSetter {
            var livePackageProduct = MutableLiveData<List<BillingProduct>>()
            var liveMyInAppProducts = MutableLiveData<List<BillingINAPPProduct>>()
        
            var liveMyPlan = MutableLiveData<String>()
        
            fun makeMySubs(
                activity: Activity,
                packageType: MyPackageType,
                onSuccess: (StoreTransaction, CustomerInfo) -> Unit
            ) {
                if (StaticParam.isRevenueCate) {
                    when (packageType) {
                        MyPackageType.MONTH -> {
                            RevenueCatHelper.purchaseOnTime(activity, PackageType.MONTHLY, onSuccess)
                        }
        
                        MyPackageType.ANNUAL -> {
                            RevenueCatHelper.purchaseOnTime(activity, PackageType.ANNUAL, onSuccess)
                        }
        
                        MyPackageType.SIX_MONTH -> {
                            RevenueCatHelper.purchaseOnTime(activity, PackageType.SIX_MONTH, onSuccess)
                        }
        
                        MyPackageType.WEEK -> {
                            RevenueCatHelper.purchaseOnTime(activity, PackageType.WEEKLY, onSuccess)
                        }
        
                        else -> {
                            RevenueCatHelper.purchaseOnTime(activity, PackageType.SIX_MONTH, onSuccess)
                        }
                    }
                } else {
                    BillingHelper.makePurchase(packageType)
                }
            }
        
            fun makeMyProduct(
                activity: Activity, packageType: MyPackageType/* here packageType is Product's  */
            ) {
                if (StaticParam.isRevenueCate) {
        
                } else {
                    BillingHelper.makeProductPurchase(packageType)
                }
            }
        }
    
      ------ in Subscription Act --------
    
        purchaseListener.postValue(false)
    
            purchaseListener.observe(this@NewForYearSubAct) { purchase ->
                //finish
                ("Purchasing: $purchase").log()
    
    //                isSubscribedForAllGlobal(this@NewForYearSubAct, purchase)
    
                if (purchase) {
                    setResult(Activity.RESULT_OK)
                    onBackPressed()
                }
            }
            
        
        
           SubscriptionSetter.liveMyInAppProducts.observe(this@NewForYearSubAct) { billingInAppProduct ->
                        billingInAppProduct?.let { productsList ->
        
                            if (!NetworkHelper.isOnline(this@NewForYearSubAct)) return@observe
        
                            val lifetime =
                                productsList.find { it.packageType == MyPackageType.LIFETIME && it.productType == BillingClient.ProductType.INAPP }
                            lifetime?.let {
                                ("Sucsess INAPP Data: ${it.toGson()}").log("FATZ")
                                bind.lifetimebtn.visibility = View.VISIBLE
                                bind.lifetimepriceid.text = lifetime?.price
        
                                if (!NetworkHelper.isOnline(this@NewForYearSubAct)) {
                                    bind.lifetimepriceid.text = "₹2,000/-"
                                }
        
        //                        bind.lifetimetitalid.text = lifetime?.title
        
        //                        bind.lifetimetitalid.setEllipsize(TextUtils.TruncateAt.MARQUEE);
        //                        bind.lifetimetitalid.setSingleLine(true);
        //                        bind.lifetimetitalid.setMarqueeRepeatLimit(-1); // -1 for infinite loop
        //                        bind.lifetimetitalid.setSelected(true); // Required for the marquee effect to work
        
        //                       bind.tvpurid.text = lifetime?.description
        //                       bind.tvpurid.text = "Lifetime Purchase"
                            }
                        }
                    }
        
                    SubscriptionSetter.livePackageProduct.observe(this@NewForYearSubAct,
                        object : Observer<List<BillingProduct>?> {
                            @SuppressLint("SetTextI18n")
                            override fun onChanged(billingProducts: List<BillingProduct>?) {
                                billingProducts?.let { productsList ->
                                    try {
                                        if (!NetworkHelper.isOnline(this@NewForYearSubAct)) return
        
                                        val annualPlan =
                                            productsList.find { it.packageType == MyPackageType.ANNUAL }
                                        val weekPlan =
                                            productsList.find { it.packageType == MyPackageType.WEEK }
                                        val sixPlan =
                                            productsList.find { it.packageType == MyPackageType.SIX_MONTH }
        
        //                            bind.txtPurchaseMonth.text = monthPlan?.title
        //                            bind.priserid.text = weekPlan?.title
        //                            binding.thirdidyearly.text = sixPlan?.title
        
                                        bind.tvPriceyear.text = annualPlan?.price
                                        bind.priserid.text = weekPlan?.price
                                        bind.sixmonthjid.text = sixPlan?.price
        
        //                            ("Micro Annual: " + annualPlan!!.amountmicrostoPriceInt + " | Week: " + weekPlan!!.amountmicrostoPriceInt + " | 6 Month: " + sixPlan!!.amountmicrostoPriceInt).log(
        //                                "FATZ"
        //                            )
        
                                        val annulemicro = annualPlan?.amountmicrostoPriceInt
                                        val weekmicro = weekPlan?.amountmicrostoPriceInt
                                        val sixmonthmicro = sixPlan?.amountmicrostoPriceInt
        
                                        bind.weeklytvlongtvid.text =
                                            "• ${getString(R.string.weeksubtv)} : ${weekPlan?.price}"
                                        bind.monthlytvlongtvid.text =
                                            "• ${getString(R.string.sixmonthsubtv)} : ${sixPlan?.price}"
                                        bind.yearlytvlongtvid.text =
                                            "• ${getString(R.string.yearsubtv)} : ${annualPlan?.price}"
        
                                        if (annulemicro != null) {
                                            bind.montbottomid.text =
                                                "${getString(R.string.permonth)} \n ₹${annulemicro / 12}/-"
                                        }
                                        if (sixmonthmicro != null) {
                                            bind.sixmonthbottom.text =
                                                "${getString(R.string.perweek)} \n ₹${sixmonthmicro / 24}/-"
                                        }
        
                                        if (weekmicro != null) {
                                            bind.linabc.text = "₹${(weekmicro * 24)}/-"
                                        }
                                        if (annulemicro != null) {
                                            bind.linabcde.text = "₹${(annulemicro * 48)}/-"
                                        }
        
                                        if (sixmonthmicro != null) {
                                            val yearperchantage =
                                                ((24 * weekmicro!!) - sixmonthmicro).toDouble()
                                            val sec = (yearperchantage * 100 / (24 * weekmicro)).toDouble()
        
                                            ("Six: " + sec).log()
        
                                            bind.sixmonthtvbg.text = "${(sec).roundToInt()}${
                                                getString(
                                                    R.string.off
                                                )
                                            }"
                                        }
        
                                        if (annulemicro != null) {
        
                                            val yearperchantage =
                                                (52 * weekmicro!!) - annulemicro.toDouble()
                                            val sec = yearperchantage * 100 / (52 * weekmicro).toDouble()
        
                                            ("Annul: " + sec).log()
        
                                            bind.monthtvdid.text = "${(sec).roundToInt()}${
                                                getString(
                                                    R.string.off
                                                )
                                            }"
                                        }
        
                                        if (annulemicro != null) {
                                            ("Name: " + ((annulemicro * 100) / (annulemicro * 48))).log()
                                        }
        
                                        val isFreeTrial = productsList.any { it.price == "Free" }
        
                                        if (isFreeTrial) {
                                            App.putBoolean("isFreeTry", true)
                                            bind.startfreetrybtn.text =
                                                getSubscribeText(this@NewForYearSubAct, true)
                                        } else {
                                            App.putBoolean("isFreeTry", false)
                                            bind.startfreetrybtn.text =
                                                getSubscribeText(this@NewForYearSubAct, false)
                                        }
                                    } catch (e: Exception) {
                                        e.printStackTrace()
                                    }
                                }
                            }
                        })
    
                        

// TextView Horizently AutoScroll TextView SCroll View

          bind.lifetimetitalid.setEllipsize(TextUtils.TruncateAt.MARQUEE);
                                 bind.lifetimetitalid.setSingleLine(true);
                                 bind.lifetimetitalid.setMarqueeRepeatLimit(-1); // -1 for infinite loop
                                 bind.lifetimetitalid.setSelected(true); // Required for the marquee effect to work
         
        
                        <TextView
                            android:id="@+id/lifetimetitalid"
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_gravity="start"
                            android:layout_marginTop="@dimen/_8sdp"
                            android:fontFamily="@font/poppinsmedium"
                            android:gravity="start"
                            android:includeFontPadding="false"
                            android:maxLines="1"
                            android:ellipsize="marquee"
                            android:layout_marginHorizontal="@dimen/_10sdp"
                            android:scrollHorizontally="true"
                            android:text="Purchase once, delight indefinitely"
                            android:textColor="#1C1818"
                            android:textSize="@dimen/_12sdp"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintEnd_toStartOf="@+id/view"
                            app:layout_constraintStart_toStartOf="parent"
                            app:layout_constraintTop_toTopOf="parent"
                            app:layout_goneMarginStart="@dimen/_10sdp" />
                            

// onbackpress pr intern ad delay issues 

        android:enableOnBackInvokedCallback="true"


// Shimmer for adview adaptive banner shimmer layout

    <LinearLayout
        android:id="@+id/bannerid"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent">

        <com.facebook.shimmer.ShimmerFrameLayout
            android:layout_width="match_parent"
            android:layout_height="@dimen/_50sdp"
            app:shimmer_auto_start="true"
            app:shimmer_duration="700"
            app:shimmer_fixed_width="@dimen/_200sdp"
            app:shimmer_highlight_color="@color/white">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:background="@drawable/shimmer_layout">

            </LinearLayout>

            <TextView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_gravity="center"
                android:gravity="center"
                android:text="Load Ads"
                android:textColor="@color/white"
                android:textSize="@dimen/_22sdp" />

        </com.facebook.shimmer.ShimmerFrameLayout>
    </LinearLayout>


             <shape xmlns:android="http://schemas.android.com/apk/res/android"
             android:shape="rectangle">
         
             <solid android:color="#61FFFFFF">
         
             </solid>
         
         </shape>



// Custom Horizontal progressbar with drawable

         <ProgressBar
                 android:id="@+id/progress"
                 style="@style/Widget.AppCompat.ProgressBar.Horizontal"
                 android:layout_width="match_parent"
                 android:layout_height="@dimen/_8sdp"
                 android:layout_marginBottom="@dimen/_15sdp"
                 android:background="@color/white"
                 android:layout_marginStart="@dimen/_5sdp"
                 android:layout_marginEnd="@dimen/_5sdp"
                 android:max="100"
                 android:progress="10"
                 android:progressDrawable="@drawable/progress_bg"
                 app:layout_constraintBottom_toBottomOf="parent"
                 app:layout_constraintEnd_toEndOf="parent"
                 app:layout_constraintStart_toStartOf="parent" />

         
         <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
             <item android:id="@android:id/background">
                 <color android:color="@android:color/transparent" />
                 <corners
                     android:radius="@dimen/_6sdp"/>
             </item>
             <item android:id="@android:id/progress">
                 <clip
                     android:clipOrientation="horizontal"
                     android:gravity="left">
                     <layer-list>
                         <item>
                             <color android:color="#640000" />
                             <corners
                                 android:radius="@dimen/_6sdp"/>
                         </item>
                         <item android:right="4dp">
                             <shape>
                                 <corners
                                     android:radius="@dimen/_6sdp"/>
                                 <solid android:color="#B05DDD" />
                             </shape>
                         </item>
                     </layer-list>
                 </clip>
             </item>
         </layer-list>



// call test api callapi demo

         
           //retrofit2
             implementation 'com.squareup.retrofit2:retrofit:2.9.0'
             implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
         
             //interceptor
             implementation 'com.squareup.okhttp3:logging-interceptor:4.10.0'
         
             //gson
             implementation 'com.google.code.gson:gson:2.10.1'
         

         @Keep
         interface ApiService {
             @Multipart
             @POST("avatar/img2img")
             fun makeApiCall(
                 @Part file: MultipartBody.Part,
                 @Part("prompt") prompt: RequestBody,
                 @Part("strength") strength: Double = 0.35,
                 @Part("guidance_scale") guidance_scale: Double = 5.5,
                 @Part("num_inference_steps") num_inference_steps: Int = 100,
                 @Part("negative_prompt") negative_prompt: String = "ugly, tiling, poorly drawn hands, poorly drawn feet, poorly drawn face, out of frame, extra limbs",
             ): Call<ResModel>
         }
         
         @Keep
         interface onResponce {
             fun <T> onSuccess(res: T)
             fun onError(error: String)
         }
         
         @Keep
         class ApiCaller {
             fun makeApiCall(reqBodyFile: MultipartBody.Part, promypart: RequestBody, onNext: onResponce) {
                 val interceptor by lazy { HttpLoggingInterceptor() } //todo remove this line in production
                 interceptor.setLevel(HttpLoggingInterceptor.Level.BODY) //todo remove this line in production
         
                 val okClient by lazy {
                     OkHttpClient.Builder().addInterceptor(Interceptor { chain: Interceptor.Chain ->
                         val originalRequest: Request = chain.request()
                         val requestBuilder: Request.Builder =
                             originalRequest.newBuilder().addHeader("Cache-Control", "no-cache")
                                 .method(originalRequest.method, originalRequest.body)
                         val request: Request = requestBuilder.build()
                         chain.proceed(request)
                     }).connectTimeout(30, TimeUnit.SECONDS).addInterceptor(interceptor)
                         .readTimeout(30, TimeUnit.SECONDS).build()
                 }
         
                 val retrofit by lazy {
                     Retrofit.Builder().baseUrl(BASE_URL).addConverterFactory(GsonConverterFactory.create())
                         .client(okClient).build()
                 }
                 val myApi by lazy {
                     retrofit.create(ApiService::class.java)
                 }
                 myApi.makeApiCall(prompt = promypart, file = reqBodyFile)
                     .enqueue(object : Callback<ResModel> {
                         override fun onResponse(
                             call: Call<ResModel>, response: retrofit2.Response<ResModel>
                         ) {
                             ("Rs Code: ${response.code()}} | ${response.message()} ").log()
                             if (response.code() == 200) onNext.onSuccess(response.body())
                             else {
                                 onNext.onError("Something Went Wrong Try Again!")
                             }
                         }
         
                         override fun onFailure(call: Call<ResModel>, t: Throwable) {
                             ("OnError: ${t.message}").log()
                             onNext.onError(t.message!!)
                         }
                     })
             }
         }

// Load Facebook Ad

         facebook mediation lib

             implementation("com.google.ads.mediation:facebook:6.16.0.0")


        android:hardwareAccelerated="true"


    implementation 'com.facebook.android:audience-network-sdk:6.+'

      <activity
                 android:name="com.facebook.ads.AudienceNetworkActivity"
                 android:hardwareAccelerated="true" />
     
             <meta-data
                 android:name="com.google.android.gms.ads.AD_MANAGER_APP"
                 android:value="true" />
             <meta-data
                 android:name="com.google.android.gms.ads.APPLICATION_ID"
                 android:value="ca-app-pub-3940256099942544~3347511713" />
                 

          fun loadBigFbNativeAds(activity: Activity, viewGroup: ViewGroup) {
     
         val nativeFbAds = NativeAd(activity, Ads_ApplicationClass.getAdsdata(ADSKEY).facebook_native)
     
         val nativeAdListener = object : NativeAdListener {
             override fun onError(p0: Ad?, p1: com.facebook.ads.AdError?) {
                 ("onError").log()
             }
     
             override fun onAdLoaded(p0: Ad?) {
                 ("onAdLoaded").log()
                 val viewAttributes = NativeAdViewAttributes().setBackgroundColor(Color.WHITE)
                     .setTitleTextColor(Color.BLACK).setDescriptionTextColor(Color.BLACK)
                     .setButtonColor(Color.parseColor("#B52C01")).setButtonTextColor(Color.WHITE)
                 val adViewBigFB = com.facebook.ads.NativeAdView.render(
                     activity, nativeFbAds, viewAttributes
                 )
                 viewGroup.addView(
                     adViewBigFB, ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, 525)
                 )
             }
     
             override fun onAdClicked(p0: Ad?) {
                 ("onAdClicked").log()
             }
     
             override fun onLoggingImpression(p0: Ad?) {
                 ("onLoggingImpression").log()
             }
     
             override fun onMediaDownloaded(p0: Ad?) {
                 ("onMediaDownloaded").log()
             }
         }
         nativeFbAds.loadAd(
             nativeFbAds.buildLoadAdConfig().withAdListener(nativeAdListener).build()
         )
     }
     

     fun loadSmallNativeAds(activity: Activity, viewGroup: ViewGroup, adUnified: Int) {
         viewGroup.removeAllViews()
         if (Ads_ApplicationClass.getAdsdata(ADSKEY).ads_platform == "Addmob") loadNativeAdmobSmallAds(
             activity, viewGroup, ad_id = adUnified
         )
         else if (Ads_ApplicationClass.getAdsdata(ADSKEY).ads_platform != "NOADS") loadNativeSmallFB(
             activity, viewGroup
         )
     }
     

     fun loadFBanner(activity: Activity, viewGroup: ViewGroup) {
     //    ("FB Banner").log()
         val adView = com.facebook.ads.AdView(
             activity, Ads_ApplicationClass.getAdsdata(ADSKEY).facebook_banner.trim(), BANNER_HEIGHT_50
         )
         viewGroup.addView(adView);
         adView.loadAd();
     }
     fun loadNativeSmallFB(activity: Activity, viewGroup: ViewGroup) {
     
         val mNativeBannerAd =
             NativeBannerAd(activity, Ads_ApplicationClass.getAdsdata(ADSKEY).facebook_native)
     
         val nativeAdListener = object : NativeAdListener {
             override fun onError(p0: Ad?, p1: com.facebook.ads.AdError?) {
                 ("onError").log()
             }
     
             override fun onAdLoaded(p0: Ad?) {
                 ("onAdLoaded").log()
                 val viewAttributes = NativeAdViewAttributes().setBackgroundColor(Color.WHITE)
                     .setTitleTextColor(Color.BLACK).setDescriptionTextColor(Color.BLACK)
                     .setButtonColor(Color.parseColor("#B52C01")).setButtonTextColor(Color.WHITE);
     
                 val adViewFB = NativeBannerAdView.render(
                     activity, mNativeBannerAd, NativeBannerAdView.Type.HEIGHT_100, viewAttributes
                 )
                 viewGroup.addView(adViewFB)
             }
     
             override fun onAdClicked(p0: Ad?) {
                 ("onAdClicked").log()
             }
     
             override fun onLoggingImpression(p0: Ad?) {
                 ("onLoggingImpression").log()
             }
     
             override fun onMediaDownloaded(p0: Ad?) {
                 ("onMediaDownloaded").log()
             }
         }
         mNativeBannerAd.loadAd(
             mNativeBannerAd.buildLoadAdConfig().withAdListener(nativeAdListener).build()
         )
     }



// Poster Maker Logic

     Set Child

        frameLayout.setX(pxToDpW(child.getXPos()) - 30);
        frameLayout.setY(pxToDpH(child.getYPos()) - 30);
        

     private int pxToDpW(float px) {
          return (int) Math.round((px * posterWidthInSc) / layout.getLayoutWidth());
      }
  
      private int pxToDpH(float px) {
          return (int) Math.round((px * posterHeightInSc) / layout.getLayoutHeight());
      }
    

// Load Reward Ad

               interface RewardAdsCallback {
                   fun onAdsDismissClick()
                   fun onFullShowAds()
               }
               
               fun loadRewardAds(activity: Activity, onRewardCallBack: RewardAdsCallback) {
                   if (isSubScribe || !isShowRewardAds || !isNetworkConnected(activity)) onRewardCallBack.onAdsDismissClick()
                   else {
                       isRewardedShow = true
                       RewardAds(activity) {
                           it.log()
                           isRewardedShow = false
                           when (it) {
                               "onAdsFullShow" -> {
                                   onRewardCallBack.onFullShowAds()
                               }
               
                               "onAddDismiss" -> {
                                   onRewardCallBack.onAdsDismissClick()
                               }
               
                               "onAddFail" -> {
                                   onRewardCallBack.onAdsDismissClick()
                               }
                           }
                       }
                   }
               }
              
                 // reward class
              
                    
                    const val COUNTER_TIME = 10L
                    
                    class RewardAds(
                        var activity: Activity, var onAdsClick: (String) -> Unit
                    ) {
                        private var isEarnReward: Boolean = false
                        private val AD_UNIT_ID = REWARD_ADS
                        var progressBar: RewardAdsDialog = RewardAdsDialog(activity)
                        var timeOver = false
                    
                        private var countdownTimer: CountDownTimer? = null
                        private var isLoading = false
                        private var rewardedAd: RewardedAd? = null
                        private var timeRemaining: Long = 0L
                    
                        init {
                            if (isSubScribe) {
                                onAdsClick("onAddDismiss")
                            } else {
                                if (!activity.isFinishing) {
                                    progressBar.show()
                                    createTimer(COUNTER_TIME)
                                    loadRewardedAd()
                                }
                            }
                        }
                    
                        private fun loadRewardedAd() {
                            if (rewardedAd == null) {
                                ("Reward Ads Loading ID: $AD_UNIT_ID").log()
                                isLoading = true
                                val adRequest = AdManagerAdRequest.Builder().build()
                    
                                RewardedAd.load(activity, AD_UNIT_ID, adRequest, object : RewardedAdLoadCallback() {
                                    override fun onAdFailedToLoad(adError: LoadAdError) {
                                        ("Reward Error: " + adError.message + " |Time Over: $timeOver").log()
                                        isLoading = false
                                        rewardedAd = null
                                        timeOver = true
                                        countdownTimer!!.cancel()
                                        progressBar.onDismiss()
                                        isRewardedShow = false
                                        (activity.getString(R.string.please_try_again)).tos(activity)
                    
                    //                    timeOver = true
                    //                    if (progressBar.isShowingDialog()) {
                    //                        onAdsClick("onAddFail")
                    //                    }
                                    }
                    
                                    override fun onAdLoaded(ad: RewardedAd) {
                                        ("Ad was loaded.: $timeOver").log()
                                        rewardedAd = ad
                                        isLoading = false
                                        countdownTimer!!.cancel()
                                        if (!timeOver && !activity.isFinishing) {
                                            showRewardedVideo()
                                        }
                                    }
                                })
                            }
                        }
                    
                        private fun createTimer(time: Long) {
                            countdownTimer?.cancel()
                            countdownTimer = object : CountDownTimer(time * 1000, 50) {
                                override fun onTick(millisUnitFinished: Long) {
                                    timeRemaining = millisUnitFinished / 1000 + 1
                                }
                    
                                override fun onFinish() {
                                    ("Reward OnFinish ").log()
                                    if (!timeOver && !activity.isFinishing) {
                                        showRewardedVideo()
                                    }
                                }
                            }
                            countdownTimer?.start()
                        }
                    
                        private fun showRewardedVideo() {
                            progressBar.onDismiss()
                            timeOver = true
                            if (rewardedAd != null) {
                                rewardedAd?.fullScreenContentCallback = object : FullScreenContentCallback() {
                                    override fun onAdDismissedFullScreenContent() {
                                        ("Reward Ad was dismissed.").log()
                                        rewardedAd = null
                                        if (isEarnReward) onAdsClick("onAddDismiss")
                                        else {
                                            isRewardedShow = false
                                            (activity.getString(R.string.please_try_again)).tos(activity)
                                        }
                                    }
                    
                                    override fun onAdFailedToShowFullScreenContent(adError: AdError) {
                                        ("Ad failed to show.").log()
                                        rewardedAd = null
                                        onAdsClick("onAddFail")
                                    }
                    
                                    override fun onAdShowedFullScreenContent() {
                                        ("Ad showed fullscreen content.").log()
                                    }
                                }
                    
                                rewardedAd?.show(activity) {
                                    isEarnReward = true
                                    ("User earned the reward.").log()
                                }
                            } else {
                                isRewardedShow = false
                                (activity.getString(R.string.please_try_again)).tos(activity)
                    //            onAdsClick("onAddDismiss")
                            }
                        }
                    }
              // Load Singal ID Intern Ad
              
               const val GAME_LENGTH_MILLISECONDS: Long = 5000
               
               class InternAds(var activity: Activity, var AD_UNIT_ID: String, var onNext: () -> Unit) {
                   private var interstitialAd: AdManagerInterstitialAd? = null
                   private var countDownTimer: CountDownTimer? = null
                   private var gamePaused: Boolean = false
                   private var gameOver: Boolean = false
                   private var adIsLoading: Boolean = false
                   private var timerMilliseconds: Long = 0
                   var progressBar: RewardAdsDialog = RewardAdsDialog(activity)
               
                   init {
                       loadAd()
                       startGame()
                   }
               
                   private fun startGame() {
                       createTimer(GAME_LENGTH_MILLISECONDS)
                       gamePaused = false
                       gameOver = false
                   }
               
                   private fun createTimer(milliseconds: Long) {
                       countDownTimer?.cancel()
                       countDownTimer = object : CountDownTimer(milliseconds, 50) {
                           override fun onTick(millisUntilFinished: Long) {
                               timerMilliseconds = millisUntilFinished
                           }
               
                           override fun onFinish() {
                               if (!activity.isFinishing) {
                                   gameOver = true
                                   progressBar.onDismiss()
                                   showInterstitial()
                               }
                           }
                       }
                       if (!activity.isFinishing) {
                           progressBar.show()
                           countDownTimer?.start()
                       }
                   }
               
                   private fun showInterstitial() {
                       // Show the ad if it's ready. Otherwise restart the game.
                       if (interstitialAd != null) {
                           interstitialAd?.fullScreenContentCallback = object : FullScreenContentCallback() {
                               override fun onAdDismissedFullScreenContent() {
                                   interstitialAd = null
                                   ("Ad was dismissed.").log()
                                   isRewardedShow = false
                                   onNext()
                               }
               
                               override fun onAdFailedToShowFullScreenContent(adError: AdError) {
                                   interstitialAd = null
                                   ("Ad failed to show.").log()
                                   isRewardedShow = false
                                   onNext()
                               }
               
                               override fun onAdShowedFullScreenContent() {
                                   ("Ad showed fullscreen content.").log()
                               }
                           }
               //            if (activity is TrailActivity) activity.onBackPressed()
                           interstitialAd?.show(activity)
                       } else {
                           onNext()
               //            loadAd()
                       }
                   }
               
                   private fun loadAd() {
                       // Request a new ad if one isn't already loaded.
                       if (adIsLoading || interstitialAd != null) {
                           return
                       }
                       adIsLoading = true
                       val adRequest = AdManagerAdRequest.Builder().build()
               
                       AdManagerInterstitialAd.load(
                           activity,
                           AD_UNIT_ID,
                           adRequest,
                           object : AdManagerInterstitialAdLoadCallback() {
                               override fun onAdFailedToLoad(adError: LoadAdError) {
                                   (adError.message).log()
                                   interstitialAd = null
                                   adIsLoading = false
                                   val error =
                                       "domain: ${adError.domain}, code: ${adError.code}, " + "message: ${adError.message}"
                                   error.log()
               //                    Toast.makeText(
               //                        activity, "onAdFailedToLoad() with error $error", Toast.LENGTH_SHORT
               //                    ).show()
                               }
               
                               override fun onAdLoaded(interstitialAd: AdManagerInterstitialAd) {
                                   ("Intern Ad was loaded ID: $AD_UNIT_ID").log()
                                   this@InternAds.interstitialAd = interstitialAd
                                   adIsLoading = false
               //                    Toast.makeText(activity, "onAdLoaded()", Toast.LENGTH_SHORT).show()
                               }
                           })
                   }
               }

       
       // hide status bar and set color
       
             fun hideBottomNavigationBar() {
        val decorView = window.decorView
        var systemUiVisibilityFlags = decorView.systemUiVisibility
        systemUiVisibilityFlags = systemUiVisibilityFlags or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
        decorView.systemUiVisibility = systemUiVisibilityFlags
    }
         
             fun transparentstatusbar() {
                 window.addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
         //        window.statusBarColor = Color.TRANSPARENT
         //        val decorView = window.decorView
         //        var systemUiVisibilityFlags = decorView.systemUiVisibility
         ////        systemUiVisibilityFlags = systemUiVisibilityFlags or View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR
         //        decorView.systemUiVisibility = systemUiVisibilityFlags
             }
           

// Gradiant strok line custom drawable

       ----
       <ripple xmlns:android="http://schemas.android.com/apk/res/android"
           android:color="?colorControlHighlight">
           <item android:id="@android:id/mask">
               <shape android:shape="rectangle">
                   <corners android:radius="100dp"/>
                   <solid android:color="@color/black"/>
               </shape>
               <color android:color="@color/white"/>
           </item>
       </ripple>

       ----
       <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
           <item>
               <shape android:shape="rectangle">
                   <gradient
                       android:angle="360"
                       android:endColor="#D258D6"
                       android:gradientRadius="360"
                       android:startColor="#FBAF5F"
                       android:type="sweep" />
       
                   <corners android:radius="4dp" />
               </shape>
           </item>
           <item
               android:bottom="1.5dp"
               android:left="1.5dp"
               android:right="1.5dp"
               android:top="1.5dp">
               <shape android:shape="rectangle">
                   <solid android:color="@color/black" />
               </shape>
           </item>
       
       </layer-list>


// Ripple Relative layout ripple background
       
       class RippleBackground : RelativeLayout {
           private var rippleColor = 0
           private var rippleStrokeWidth = 0f
           private var rippleRadius = 0f
           private var rippleDurationTime = 0
           private var rippleAmount = 0
           private var rippleDelay = 0
           private var rippleScale = 0f
           private var rippleType = 0
           private var paint: Paint? = null
           private var isRippleAnimationRunning = false
               private set
           private var animatorSet: AnimatorSet? = null
           private var animatorList: ArrayList<Animator>? = null
           private var rippleParams: LayoutParams? = null
           private val rippleViewList = ArrayList<RippleView>()
       
           constructor(context: Context?) : super(context)
           constructor(context: Context, attrs: AttributeSet?) : super(context, attrs) {
               init(context, attrs)
           }
       
           constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int) : super(
               context,
               attrs,
               defStyleAttr
           ) {
               init(context, attrs)
           }
       
           private fun init(context: Context, attrs: AttributeSet?) {
               if (isInEditMode) return
               requireNotNull(attrs) { "Attributes should be provided to this view," }
               val typedArray: TypedArray =
                   context.obtainStyledAttributes(attrs, R.styleable.RippleBackground)
               rippleColor = typedArray.getColor(
                   R.styleable.RippleBackground_rb_color,
                   getCompactColor(R.color.color_gray)
               )
               rippleStrokeWidth = typedArray.getDimension(
                   R.styleable.RippleBackground_rb_strokeWidth,
                   resources.getDimension(R.dimen.rippleStrokeWidth)
               )
               rippleRadius = typedArray.getDimension(
                   R.styleable.RippleBackground_rb_radius,
                   resources.getDimension(R.dimen.rippleRadius)
               )
               rippleDurationTime =
                   typedArray.getInt(R.styleable.RippleBackground_rb_duration, DEFAULT_DURATION_TIME)
               rippleAmount =
                   typedArray.getInt(R.styleable.RippleBackground_rb_rippleAmount, DEFAULT_RIPPLE_COUNT)
               rippleScale = typedArray.getFloat(R.styleable.RippleBackground_rb_scale, DEFAULT_SCALE)
               rippleType = typedArray.getInt(R.styleable.RippleBackground_rb_type, DEFAULT_FILL_TYPE)
               typedArray.recycle()
               rippleDelay = rippleDurationTime / rippleAmount
               paint = Paint()
               paint?.isAntiAlias = true
               if (rippleType == DEFAULT_FILL_TYPE) {
                   rippleStrokeWidth = 0f
                   paint?.style = Paint.Style.FILL
               } else paint?.style = Paint.Style.STROKE
               paint?.color = rippleColor
               rippleParams = LayoutParams(
                   (1.2 * (rippleRadius + rippleStrokeWidth)).toInt(),
                   (1.2 * (rippleRadius + rippleStrokeWidth)).toInt()
               )
               rippleParams?.addRule(CENTER_IN_PARENT, TRUE)
               animatorSet = AnimatorSet()
               animatorSet?.interpolator = AccelerateDecelerateInterpolator()
               animatorList = ArrayList()
               for (i in 0 until rippleAmount) {
                   val rippleView = RippleView(getContext())
                   addView(rippleView, rippleParams)
                   rippleViewList.add(rippleView)
                   val scaleXAnimator: ObjectAnimator =
                       ObjectAnimator.ofFloat(rippleView, "ScaleX", 1.0f, rippleScale)
                   scaleXAnimator.repeatCount = ObjectAnimator.INFINITE
                   scaleXAnimator.repeatMode = ObjectAnimator.RESTART
                   scaleXAnimator.startDelay = (i * rippleDelay).toLong()
                   scaleXAnimator.duration = rippleDurationTime.toLong()
                   animatorList?.add(scaleXAnimator)
                   val scaleYAnimator: ObjectAnimator =
                       ObjectAnimator.ofFloat(rippleView, "ScaleY", 1.0f, rippleScale)
                   scaleYAnimator.repeatCount = ObjectAnimator.INFINITE
                   scaleYAnimator.repeatMode = ObjectAnimator.RESTART
                   scaleYAnimator.startDelay = (i * rippleDelay).toLong()
                   scaleYAnimator.duration = rippleDurationTime.toLong()
                   animatorList?.add(scaleYAnimator)
                   val alphaAnimator: ObjectAnimator =
                       ObjectAnimator.ofFloat(rippleView, "Alpha", 1.0f, 0f)
                   alphaAnimator.repeatCount = ObjectAnimator.INFINITE
                   alphaAnimator.repeatMode = ObjectAnimator.RESTART
                   alphaAnimator.startDelay = (i * rippleDelay).toLong()
                   alphaAnimator.duration = rippleDurationTime.toLong()
                   animatorList?.add(alphaAnimator)
               }
               animatorSet?.playTogether(animatorList)
           }
       
           private inner class RippleView(context: Context?) : View(context) {
               init {
                   this.invisible()
               }
       
               override fun onDraw(canvas: Canvas) {
                   val radius: Int = width.coerceAtMost(height) / 2
                   paint?.let {
                       canvas.drawCircle(radius.toFloat(), radius.toFloat(), radius - rippleStrokeWidth,it)
                   }
               }
           }
       
           fun startRippleAnimation() {
               if (!isRippleAnimationRunning) {
                   for (rippleView in rippleViewList) {
                       rippleView.visible()
                   }
                   animatorSet?.start()
                   isRippleAnimationRunning = true
               }
           }
       
       
       
           companion object {
               private const val DEFAULT_RIPPLE_COUNT = 3
               private const val DEFAULT_DURATION_TIME = 3500
               private const val DEFAULT_SCALE = 2.5f
               private const val DEFAULT_FILL_TYPE = 0
           }
       }

// Check Permission

       
       internal fun Context.getCompactColor(color: Int): Int = ContextCompat.getColor(this, color)
       
       internal fun View.getCompactColor(color: Int): Int = ContextCompat.getColor(this.context, color)
       
       internal fun Context.getCompactDrawable(drawable: Int): Drawable? =
           ContextCompat.getDrawable(this, drawable)
       
       internal fun View.getCompactDrawable(drawable: Int): Drawable? =
           ContextCompat.getDrawable(context, drawable)
       
       internal fun AppCompatActivity.changeStatusBarColor(color: Int) {
           window.statusBarColor = getCompactColor(color)
       }
       
       internal fun File.isImageOrGifFile(): Boolean = kotlin.run {
           name.endsWith("png") || name.endsWith("jpg") || name.endsWith("jpeg") || name.endsWith(".gif")
       }
       
       internal fun File.isVideoFile(): Boolean =
           kotlin.run { name.endsWith("mp4") || name.endsWith("mkv") || name.endsWith("mov") }
    

       internal fun Context.hasPermissions(permissionList: MutableList<String>): Boolean {
           var hasAllPermissions = true
           for (permission in permissionList) {
               if (checkCallingOrSelfPermission(permission) == PackageManager.PERMISSION_GRANTED) {
                   hasAllPermissions = false
               }
           }
           return hasAllPermissions
       }

       if (hasPermissions(permissionList)) {
                navigateToOtherScreen()
                return
            }



// Hide Navigation and status bar color 

       fun hideBottomNavigationBar() {
              window.statusBarColor = ContextCompat.getColor(this, R.color.status_bar_color)
              if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
                  // For devices running Android 11 (API level 30) and above
                  window.setDecorFitsSystemWindows(false)
                  window.insetsController?.hide(WindowInsets.Type.navigationBars())
              } else {
                  // For devices running below Android 11
                  window.decorView.systemUiVisibility =
                      (View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY)
              }
          }

// Finger Draw view

      
      public class DrawView extends View {
      
          private static final float TOUCH_TOLERANCE = 4;
          private float mX, mY;
          private Path mPath;
      
          // the Paint class encapsulates the color
          // and style information about
          // how to draw the geometries,text and bitmaps
          private final Paint mPaint;
      
          // ArrayList to store all the strokes
          // drawn by the user on the Canvas
          private final ArrayList<Stroke> paths = new ArrayList<>();
          private int currentColor;
          private int strokeWidth;
          private Bitmap mBitmap;
          private Canvas mCanvas;
          private final Paint mBitmapPaint = new Paint(Paint.DITHER_FLAG);
      
          // Constructors to initialise all the attributes
          public DrawView(Context context) {
              this(context, null);
          }
      
          public DrawView(Context context, AttributeSet attrs) {
              super(context, attrs);
              mPaint = new Paint();
      
              // the below methods smoothens
              // the drawings of the user
              mPaint.setAntiAlias(true);
              mPaint.setDither(true);
              mPaint.setColor(Color.GREEN);
              mPaint.setStyle(Paint.Style.STROKE);
              mPaint.setStrokeJoin(Paint.Join.ROUND);
              mPaint.setStrokeCap(Paint.Cap.ROUND);
      
              // 0xff=255 in decimal
              mPaint.setAlpha(0xff);
      
          }
      
          // this method instantiate the bitmap and object
          public void init(int height, int width) {
      
              mBitmap = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888);
              mCanvas = new Canvas(mBitmap);
      
              // set an initial color of the brush
              currentColor = Color.RED;
      
              // set an initial brush size
              strokeWidth = 20;
          }
      
          // sets the current color of stroke
          public void setColor(int color) {
              currentColor = color;
          }
      
          // sets the stroke width
          public void setStrokeWidth(int width) {
              strokeWidth = width;
          }
      
          public void undo() {
              if (paths.size() != 0) {
                  paths.remove(paths.size() - 1);
                  invalidate();
              }
          }
      
          public void clear() {
              if (paths.size() != 0) {
                  paths.clear();
                  invalidate();
              }
          }
      
          public Bitmap save() {
              return mBitmap;
          }
      
          @Override
          protected void onDraw(Canvas canvas) {
              // save the current state of the canvas before,
              // to draw the background of the canvas
              canvas.save();
      
              // DEFAULT color of the canvas
              int backgroundColor = Color.WHITE;
              if (mCanvas != null) mCanvas.drawColor(backgroundColor);
      
              // now, we iterate over the list of paths
              // and draw each path on the canvas
              for (Stroke fp : paths) {
                  mPaint.setColor(fp.color);
                  mPaint.setStrokeWidth(fp.strokeWidth);
                  mCanvas.drawPath(fp.path, mPaint);
              }
              canvas.drawBitmap(mBitmap, 0, 0, mBitmapPaint);
              canvas.restore();
          }
      
          // the below methods manages the touch
          // response of the user on the screen
      
          // firstly, we create a new Stroke
          // and add it to the paths list
          private void touchStart(float x, float y) {
              mPath = new Path();
              Stroke fp = new Stroke(currentColor, strokeWidth, mPath);
              paths.add(fp);
      
              // finally remove any curve
              // or line from the path
              mPath.reset();
      
              // this methods sets the starting
              // point of the line being drawn
              mPath.moveTo(x, y);
      
              // we save the current
              // coordinates of the finger
              mX = x;
              mY = y;
          }
      
          // in this method we check
          // if the move of finger on the
          // screen is greater than the
          // Tolerance we have previously defined,
          // then we call the quadTo() method which
          // actually smooths the turns we create,
          // by calculating the mean position between
          // the previous position and current position
          private void touchMove(float x, float y) {
              float dx = Math.abs(x - mX);
              float dy = Math.abs(y - mY);
      
              if (dx >= TOUCH_TOLERANCE || dy >= TOUCH_TOLERANCE) {
                  mPath.quadTo(mX, mY, (x + mX) / 2, (y + mY) / 2);
                  mX = x;
                  mY = y;
              }
          }
      
          // at the end, we call the lineTo method
          // which simply draws the line until
          // the end position
          private void touchUp() {
              mPath.lineTo(mX, mY);
          }
      
          // the onTouchEvent() method provides us with
          // the information about the type of motion
          // which has been taken place, and according
          // to that we call our desired methods
          @Override
          public boolean onTouchEvent(MotionEvent event) {
              float x = event.getX();
              float y = event.getY();
      
              switch (event.getAction()) {
                  case MotionEvent.ACTION_DOWN:
                      touchStart(x, y);
                      invalidate();
                      break;
                  case MotionEvent.ACTION_MOVE:
                      touchMove(x, y);
                      invalidate();
                      break;
                  case MotionEvent.ACTION_UP:
                      touchUp();
                      invalidate();
                      break;
              }
              return true;
          }
      }

// Vertical Seekbar

    
      public class VerticalSeekBar extends androidx.appcompat.widget.AppCompatSeekBar {
      
          public VerticalSeekBar(Context context) {
              super(context);
          }
      
          public VerticalSeekBar(Context context, AttributeSet attrs, int defStyle) {
              super(context, attrs, defStyle);
          }
      
          public VerticalSeekBar(Context context, AttributeSet attrs) {
              super(context, attrs);
          }
      
          protected void onSizeChanged(int w, int h, int oldw, int oldh) {
              super.onSizeChanged(h, w, oldh, oldw);
          }
      
          @Override
          protected synchronized void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
              super.onMeasure(heightMeasureSpec, widthMeasureSpec);
              setMeasuredDimension(getMeasuredHeight(), getMeasuredWidth());
          }
      
          protected void onDraw(Canvas c) {
              c.rotate(-90);
              c.translate(-getHeight(), 0);
      
              super.onDraw(c);
          }
      
          @SuppressLint("ClickableViewAccessibility")
          @Override
          public boolean onTouchEvent(MotionEvent event) {
              if (!isEnabled()) {
                  return false;
              }
      
              switch (event.getAction()) {
                  case MotionEvent.ACTION_DOWN:
                  case MotionEvent.ACTION_MOVE:
                  case MotionEvent.ACTION_UP:
                      int i = 0;
                      i = getMax() - (int) (getMax() * event.getY() / getHeight());
                      setProgress(i);
                      log(getProgress() + "");
                      onSizeChanged(getWidth(), getHeight(), 0, 0);
                      break;
      
                  case MotionEvent.ACTION_CANCEL:
                      break;
              }
              return true;
          }
      
      }

// PosterTextView
                
                
                public class PosterText extends androidx.appcompat.widget.AppCompatTextView {
                    private int strokeColor;
                    private float strokeWidth = 5f;
                    private int startColor;
                    private int endColor;
                
                    private boolean invalidet = true;
                
                    private boolean isStroke = false;
                    private boolean isDot = false;
                    private boolean isDash = false;
                
                    private boolean isGradient = false;
                    private boolean isPattern = false;
                    private String patternPath;
                
                    public PosterText(@NonNull Context context) {
                        super(context);
                    }
                
                    public PosterText(@NonNull Context context, @Nullable AttributeSet attrs) {
                        super(context, attrs);
                    }
                
                    public PosterText(@NonNull Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
                        super(context, attrs, defStyleAttr);
                    }
                
                    public void setDot() {
                        isDot = true;
                        isDash = false;
                        invalidate();
                    }
                
                    public void setDash() {
                        isDash = true;
                        isDot = false;
                        invalidate();
                    }
                
                    public boolean isDot() {
                        return isDot;
                    }
                
                    public boolean isDash() {
                        return isDash;
                    }
                
                    public void setLine() {
                        isDash = false;
                        isDot = false;
                        invalidate();
                    }
                
                    public void removeStroke() {
                        isStroke = false;
                        invalidate();
                    }
                
                    @Override
                    public void setTextColor(int color) {
                        isPattern = false;
                        isGradient = false;
                        super.setTextColor(color);
                    }
                
                    public void setPattern(String patternPath) {
                        invalidet = true;
                        this.patternPath = patternPath;
                        isPattern = true;
                        isGradient = false;
                        invalidate();
                    }
                
                    public void setStroke(int strokeColor) {
                        this.strokeColor = strokeColor;
                        isStroke = true;
                        invalidate();
                    }
                
                    public void setStroke(String strokeColor) {
                        this.strokeColor = toColor(strokeColor);
                        isStroke = true;
                        invalidate();
                    }
                
                    public void setStrokeWidth(float strokeWidth) {
                        this.strokeWidth = strokeWidth;
                        invalidate();
                    }
                
                    public void setGradient(int startColor, int endColor) {
                        this.startColor = startColor;
                        this.endColor = endColor;
                        isGradient = true;
                        isPattern = false;
                        invalidate();
                    }
                
                    int gradient_orientation;
                    int type;
                    Integer gradientRadius;
                
                    public void setGradient(int startColor, int endColor, int type, int gradient_orientation) {
                        this.type = type;
                        this.gradient_orientation = gradient_orientation;
                        this.startColor = startColor;
                        this.endColor = endColor;
                        isGradient = true;
                        isPattern = false;
                        invalidate();
                    }
                
                    public void setGradient(int startColor, int endColor, Integer gradientRadius) {
                        this.type = 1;
                        this.gradientRadius = gradientRadius;
                        this.startColor = startColor;
                        this.endColor = endColor;
                        isGradient = true;
                        isPattern = false;
                        invalidate();
                    }
                
                    @SuppressLint("DrawAllocation")
                    @Override
                    public void onDraw(Canvas canvas) {
                        Paint paint = this.getPaint();
                        paint.setAntiAlias(true);
                //        paint.setDither(false);
                        if (getShadowColor() != 0) {
                            paint.setShader(null);
                            paint.setShadowLayer(getShadowRadius(), getShadowDx(), getShadowDy(), getShadowColor());
                            super.onDraw(canvas);
                            paint.clearShadowLayer();
                        }
                        if (isStroke && isGradient) {
                            paint.setStyle(Paint.Style.STROKE);
                            paint.setStrokeWidth(strokeWidth);
                
                            if (isDot)
                                paint.setPathEffect(new DashPathEffect(new float[]{2f, 4f}, 0f));
                            else if (isDash)
                                paint.setPathEffect(new DashPathEffect(new float[]{10f, 6f}, 0f));
                            else
                                paint.setPathEffect(null);
                
                            paint.setShader(new LinearGradient(0f, 0f, getTextSize(), getTextSize(), strokeColor, strokeColor, Shader.TileMode.CLAMP));
                            super.onDraw(canvas);
                            /*paint.setStyle(Paint.Style.FILL);
                            Shader linearShader = new LinearGradient(0f, 0f, 0, getHeight(), startColor, endColor, Shader.TileMode.CLAMP);
                            paint.setShader(linearShader);*/
                            paint.setStyle(Paint.Style.FILL);
                
                            Shader g;
                
                            switch (type) {
                                case 0:
                                    int x0, y0, x1, y1;
                                    int w = getWidth();
                                    int h = getHeight();
                                    switch (gradient_orientation) {
                                        case 1:
                                            x0 = w / 2;
                                            y0 = 0;
                
                                            x1 = w / 2;
                                            y1 = h;
                                            break;
                
                                        case 2:
                                            x0 = w;
                                            y0 = 0;
                
                                            x1 = 0;
                                            y1 = h;
                                            break;
                
                                        case 3:
                                            x0 = w;
                                            y0 = h / 2;
                
                                            x1 = 0;
                                            y1 = h / 2;
                                            break;
                
                                        case 4:
                                            x0 = w;
                                            y0 = h;
                
                                            x1 = 0;
                                            y1 = 0;
                                            break;
                
                                        case 5:
                                            x0 = w / 2;
                                            y0 = h;
                
                                            x1 = w / 2;
                                            y1 = 0;
                                            break;
                
                                        case 6:
                                            x0 = 0;
                                            y0 = h;
                
                                            x1 = w;
                                            y1 = 0;
                                            break;
                
                                        case 7:
                                            x0 = 0;
                                            y0 = h / 2;
                
                                            x1 = w;
                                            y1 = h / 2;
                                            break;
                
                                        case 8:
                                            x0 = 0;
                                            y0 = 0;
                
                                            x1 = w;
                                            y1 = h;
                                            break;
                
                                        default:
                                            x0 = 0;
                                            y0 = 0;
                
                                            x1 = 0;
                                            y1 = h;
                                    }
                
                                    g = new LinearGradient(x0, y0, x1, y1, startColor, endColor, Shader.TileMode.CLAMP);
                                    break;
                
                                case 1:
                                    log(gradientRadius);
                                    float finalRd = (gradientRadius * getWidth()) / 500f;
                                    g = new RadialGradient(getPivotX(), getPivotY(), gradientRadius == null ? getWidth() / 2f : finalRd == 0 ? 1 : finalRd, startColor, endColor, Shader.TileMode.CLAMP);
                
                                    break;
                
                                case 2:
                                    g = new SweepGradient(getPivotX(), getPivotY(), startColor, endColor);
                                    break;
                
                                default:
                                    log("Default Of Poster Case");
                                    g = new LinearGradient(0, 0, 0, getHeight(), startColor, endColor, Shader.TileMode.CLAMP);
                            }
                
                            paint.setShader(g);
                            super.onDraw(canvas);
                
                        } else if (isStroke && isPattern) {
                            paint.setStyle(Paint.Style.STROKE);
                            paint.setStrokeWidth(strokeWidth);
                
                            if (isDot)
                                paint.setPathEffect(new DashPathEffect(new float[]{2f, 4f}, 0f));
                            else if (isDash)
                                paint.setPathEffect(new DashPathEffect(new float[]{10f, 6f}, 0f));
                            else
                                paint.setPathEffect(null);
                
                            paint.setShader(new LinearGradient(0f, 0f, getTextSize(), getTextSize(), strokeColor, strokeColor, Shader.TileMode.CLAMP));
                            super.onDraw(canvas);
                
                            paint.setStyle(Paint.Style.FILL);
                            Glide.with(this)
                                    .asBitmap()
                                    .load(BASE_ASSET + patternPath)
                                    .into(new CustomTarget<Bitmap>() {
                                        @Override
                                        public void onResourceReady(@NonNull Bitmap bitmap, @Nullable Transition<? super Bitmap> transition) {
                                            Shader ptBitmap = new BitmapShader(bitmap, Shader.TileMode.REPEAT, Shader.TileMode.REPEAT);
                                            paint.setShader(ptBitmap);
                
                                            if (invalidet) {
                                                invalidate();
                                                invalidet = false;
                                            }
                                        }
                
                                        @Override
                                        public void onLoadCleared(@Nullable Drawable placeholder) {
                                        }
                                    });
                            super.onDraw(canvas);
                
                        } else if (isStroke) {
                            int textColor = getTextColors().getDefaultColor();
                            paint.setStyle(Paint.Style.STROKE);
                            paint.setStrokeWidth(strokeWidth);
                
                            if (isDot)
                                paint.setPathEffect(new DashPathEffect(new float[]{2f, 4f}, 0f));
                            else if (isDash)
                                paint.setPathEffect(new DashPathEffect(new float[]{10f, 6f}, 0f));
                            else
                                paint.setPathEffect(null);
                
                            paint.setShader(new LinearGradient(0f, 0f, getTextSize(), getTextSize(), strokeColor, strokeColor, Shader.TileMode.CLAMP));
                            super.onDraw(canvas);
                            paint.setStyle(Paint.Style.FILL);
                            Shader linearShader = new LinearGradient(0f, 0f, 0f, 0, textColor, textColor, Shader.TileMode.CLAMP);
                            paint.setShader(linearShader);
                            super.onDraw(canvas);
                
                        } else if (isGradient) {
                //            paint.setStyle(Paint.Style.FILL);
                //            paint.setShader(new LinearGradient(0f, 0f, 0, getHeight(), startColor, endColor, Shader.TileMode.CLAMP));
                //            super.onDraw(canvas);
                
                            paint.setStyle(Paint.Style.FILL);
                
                            Shader g;
                
                            switch (type) {
                                case 0:
                                    int x0, y0, x1, y1;
                                    int w = getWidth();
                                    int h = getHeight();
                                    switch (gradient_orientation) {
                                        case 1:
                                            x0 = w / 2;
                                            y0 = 0;
                
                                            x1 = w / 2;
                                            y1 = h;
                                            break;
                
                                        case 2:
                                            x0 = w;
                                            y0 = 0;
                
                                            x1 = 0;
                                            y1 = h;
                                            break;
                
                                        case 3:
                                            x0 = w;
                                            y0 = h / 2;
                
                                            x1 = 0;
                                            y1 = h / 2;
                                            break;
                
                                        case 4:
                                            x0 = w;
                                            y0 = h;
                
                                            x1 = 0;
                                            y1 = 0;
                                            break;
                
                                        case 5:
                                            x0 = w / 2;
                                            y0 = h;
                
                                            x1 = w / 2;
                                            y1 = 0;
                                            break;
                
                                        case 6:
                                            x0 = 0;
                                            y0 = h;
                
                                            x1 = w;
                                            y1 = 0;
                                            break;
                
                                        case 7:
                                            x0 = 0;
                                            y0 = h / 2;
                
                                            x1 = w;
                                            y1 = h / 2;
                                            break;
                
                                        case 8:
                                            x0 = 0;
                                            y0 = 0;
                
                                            x1 = w;
                                            y1 = h;
                                            break;
                
                                        default:
                                            x0 = 0;
                                            y0 = 0;
                
                                            x1 = 0;
                                            y1 = h;
                                    }
                
                                    g = new LinearGradient(x0, y0, x1, y1, startColor, endColor, Shader.TileMode.CLAMP);
                                    break;
                
                                case 1:
                                    log(gradientRadius);
                
                                    float finalRd = 0;
                                    if (gradientRadius != null)
                                        finalRd = (gradientRadius * getWidth()) / 500f;
                
                                    g = new RadialGradient(getPivotX(), getPivotY(), gradientRadius == null ? getWidth() / 2f : finalRd == 0 ? 1 : finalRd, startColor, endColor, Shader.TileMode.CLAMP);
                
                                    break;
                
                                case 2:
                                    g = new SweepGradient(getPivotX(), getPivotY(), startColor, endColor);
                                    break;
                
                                default:
                                    log("Default Of Poster Case");
                                    g = new LinearGradient(0, 0, 0, getHeight(), startColor, endColor, Shader.TileMode.CLAMP);
                            }
                
                            paint.setShader(g);
                            super.onDraw(canvas);
                
                        } else if (isPattern) {
                            paint.setStyle(Paint.Style.FILL);
                            Glide.with(this)
                                    .asBitmap()
                                    .load(BASE_ASSET + patternPath)
                                    .into(new CustomTarget<Bitmap>() {
                                        @Override
                                        public void onResourceReady(@NonNull Bitmap bitmap, @Nullable Transition<? super Bitmap> transition) {
                                            Shader ptBitmap = new BitmapShader(bitmap, Shader.TileMode.REPEAT, Shader.TileMode.REPEAT);
                                            paint.setShader(ptBitmap);
                
                                            if (invalidet) {
                                                invalidate();
                                                invalidet = false;
                                            }
                                        }
                
                                        @Override
                                        public void onLoadCleared(@Nullable Drawable placeholder) {
                                        }
                                    });
                            super.onDraw(canvas);
                
                        } else {
                            paint.setStyle(Paint.Style.FILL);
                            paint.setShader(new LinearGradient(0f, 0f, 0f, 0, getCurrentTextColor(), getCurrentTextColor(), Shader.TileMode.CLAMP));
                            super.onDraw(canvas);
                        }
                    }
                }


// Notification morning start alarm start services

        <receiver
                  android:name=".MyReceiver"
                  android:exported="true">
                  <intent-filter>
                      <action android:name="android.intent.action.BOOT_COMPLETED" />
                      <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
                  </intent-filter>
              </receiver>
      
                    
      public class MyReceiver extends BroadcastReceiver {
      
          @Override
          public void onReceive(Context context, Intent intent) {
              try {
                  switch (intent.getAction()) {
                      case Intent.ACTION_BOOT_COMPLETED:
                      case Intent.ACTION_INPUT_METHOD_CHANGED:
                          if (MyManager.getInt(DRAFT_SIZE) > 0) {
                              createNotificationChannel(context);
                              Intent notificationIntent = new Intent(context, SplashActivity.class);
                              PendingIntent pendingIntent = PendingIntent.getActivity(context, 23432420, notificationIntent, PendingIntent.FLAG_IMMUTABLE);
                              Notification notification = new NotificationCompat.Builder(context, "CHANNEL_ID")
                                      .setContentTitle("It has been a few days since you have used the " + context.getString(R.string.app_name) + " app.")
                                      .setSmallIcon(R.mipmap.ic_launcher)
                                      .setContentIntent(pendingIntent)
                                      .setAutoCancel(true)
                                      .build();
      
                              NotificationManager mNotificationManager =
                                      (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
                              mNotificationManager.notify(0, notification);
      
                              Vibrator vibrator = (Vibrator) context.getSystemService(Context.VIBRATOR_SERVICE);
                              vibrator.vibrate(500);
                              log("notified");
                          }
                  }
              } catch (Exception e) {
                  print(e);
              }
          }
      
          private void createNotificationChannel(Context context) {
              if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                  NotificationChannel serviceChannel = new NotificationChannel(
                          "CHANNEL_ID",
                          context.getString(R.string.app_name) + " Notification",
                          NotificationManager.IMPORTANCE_DEFAULT
                  );
                  NotificationManager manager = context.getSystemService(NotificationManager.class);
                  manager.createNotificationChannel(serviceChannel);
              }
          }
      }


                    
      fun setAlarm(activity: Context, time: Long) {
          val pendingIntent = PendingIntent.getBroadcast(activity.applicationContext, 23432420, Intent(activity, MyReceiver::class.java), PendingIntent.FLAG_IMMUTABLE)
          val alarmManager = activity.getSystemService(Context.ALARM_SERVICE) as AlarmManager
          alarmManager[AlarmManager.RTC_WAKEUP, time] = pendingIntent
      }
      
      fun killAlarm(activity: Context, onChange: () -> Unit) {
          val intent = Intent(activity, MyReceiver::class.java)
          val pendingIntent = PendingIntent.getBroadcast(activity, 23432420, intent, PendingIntent.FLAG_IMMUTABLE)
          val alarmManager = activity.getSystemService(Context.ALARM_SERVICE) as AlarmManager
          alarmManager[AlarmManager.RTC_WAKEUP, 0] = pendingIntent
          alarmManager.cancel(pendingIntent)
          onChange()
      }


        killAlarm(this) {
            val after24hours = System.currentTimeMillis() + 86400000L
            setAlarm(this, after24hours)
        }
      
      
              fun View.toBmp(): Bitmap {
          System.gc()
          val b = Bitmap.createBitmap(if (width == 0) 1 else width, if (height == 0) 1 else height, Bitmap.Config.ARGB_8888)
          layout(left, top, right, bottom)
          draw(Canvas(b))
          return b
      }
      
      fun ImageView.ivToBmp(): Bitmap {
          System.gc()
          return drawable.toBitmap()
      }


// Box red line view

      attrs.xml
      
      <resources>
          <declare-styleable name="DividerView">
              <attr name="color" format="color" />
              <attr name="dashLength" format="dimension" />
              <attr name="dashGap" format="dimension" />
              <attr name="dashThickness" format="dimension" />
              <attr name="orientation" format="enum">
                  <enum name="horizontal" value="0" />
                  <enum name="vertical" value="1" />
              </attr>
          </declare-styleable>
      
          <declare-styleable name="StrokedTextAttrs">
              <attr name="textStrokeColor" format="color"/>
              <attr name="textStrokeWidth" format="float"/>
          </declare-styleable>
      </resources>


      public class BoxView extends View {
          static public int ORIENTATION_HORIZONTAL = 0;
          static public int ORIENTATION_VERTICAL = 1;
          private final Paint paint;
          private final Paint paintCenter;
      
      
          @SuppressLint("ResourceAsColor")
          public BoxView(Context context, AttributeSet attrs) {
              super(context, attrs);
              int dashGap, dashLength, dashThickness;
              int color;
              int centerDashGap, centerDashLength, centerDashThickness;
              int centerColor;
      
              TypedArray a = context.getTheme().obtainStyledAttributes(attrs, R.styleable.DividerView, 0, 0);
              TypedArray centerA = context.getTheme().obtainStyledAttributes(attrs, R.styleable.DividerView, 0, 0);
      
              try {
                  dashGap = a.getDimensionPixelSize(R.styleable.DividerView_dashGap, 10);
                  dashLength = a.getDimensionPixelSize(R.styleable.DividerView_dashLength, 12);
                  dashThickness = a.getDimensionPixelSize(R.styleable.DividerView_dashThickness, 1);
                  color = a.getColor(R.styleable.DividerView_color, getResources().getColor(R.color.custom_red, null));
      
                  centerDashGap = centerA.getDimensionPixelSize(R.styleable.DividerView_dashGap, 0);
                  centerDashLength = centerA.getDimensionPixelSize(R.styleable.DividerView_dashLength, 0);
                  centerDashThickness = centerA.getDimensionPixelSize(R.styleable.DividerView_dashThickness, 2);
                  centerColor = centerA.getColor(R.styleable.DividerView_color, Color.RED);
              } finally {
                  a.recycle();
                  centerA.recycle();
              }
      
              paint = new Paint();
              paint.setAntiAlias(true);
              paint.setColor(color);
              paint.setStyle(Paint.Style.STROKE);
              paint.setStrokeWidth(dashThickness);
              paint.setPathEffect(new DashPathEffect(new float[]{dashLength, dashGap,}, 0));
      
              paintCenter = new Paint();
              paintCenter.setAntiAlias(true);
              paintCenter.setColor(centerColor);
              paintCenter.setStyle(Paint.Style.STROKE);
              paintCenter.setStrokeWidth(centerDashThickness);
              paintCenter.setPathEffect(new DashPathEffect(new float[]{centerDashLength, centerDashGap,}, 0));
          }
      
          public BoxView(Context context) {
              this(context, null);
          }
      
          @Override
          protected void onDraw(Canvas canvas) {
              //center horizontal line
              float centerH = getHeight() / 2f;
              canvas.drawLine(0, centerH, getWidth(), centerH, paintCenter);
      
              //center Vertical line
              float centerV = getWidth() / 2f;
              canvas.drawLine(centerV, 0, centerV, getHeight(), paintCenter);
      
              // horizontal lines
              float thPartH = getHeight() / 12f;
              for (int i = 0; i <= 12; i++)
                  canvas.drawLine(0, thPartH * i, getWidth(), thPartH * i, paint);
      
              // Vertical lines
              float thPartV = getWidth() / 10f;
              for (int i = 0; i <= 10; i++)
                  canvas.drawLine(thPartV * i, 0, thPartV * i, getHeight(), paint);
          }
      }
            
       boxView = new BoxView(this);
              boxView.setBackgroundColor(Color.TRANSPARENT);
              boxView.setVisibility(View.GONE);
              bgFrameLayout.addView(boxView);
              

// CryptoAES encrypt & decrypt string

      class CryptoAES {
          private val KEY_SIZE = 256
          private val IV_SIZE = 128
          private val HASH_CIPHER = "AES/CBC/PKCS7Padding"
          private val AES = "AES"
          private val KDF_DIGEST = "MD5"
      
          private val APPEND = "Salted__"
      
          fun encrypt(password: String, plainText: String): String {
              val saltBytes = generateSalt(8)
              val key = ByteArray(KEY_SIZE / 8)
              val iv = ByteArray(IV_SIZE / 8)
              EvpKDF(password.toByteArray(), KEY_SIZE, IV_SIZE, saltBytes, key, iv)
              val keyS = SecretKeySpec(key, AES)
              val cipher = Cipher.getInstance(HASH_CIPHER)
              val ivSpec = IvParameterSpec(iv)
              cipher.init(Cipher.ENCRYPT_MODE, keyS, ivSpec)
              val cipherText = cipher.doFinal(plainText.toByteArray())
              val sBytes = APPEND.toByteArray()
              val b = ByteArray(sBytes.size + saltBytes.size + cipherText.size)
              System.arraycopy(sBytes, 0, b, 0, sBytes.size)
              System.arraycopy(saltBytes, 0, b, sBytes.size, saltBytes.size)
              System.arraycopy(cipherText, 0, b, sBytes.size + saltBytes.size, cipherText.size)
              val bEncode = android.util.Base64.encode(b, android.util.Base64.NO_WRAP)
              return String(bEncode)
          }
      
          fun decrypt(password: String, cipherText: String): String {
              val ctBytes =
                  android.util.Base64.decode(cipherText.toByteArray(), android.util.Base64.NO_WRAP)
              val saltBytes = Arrays.copyOfRange(ctBytes, 8, 16)
              val cipherTextBytes = Arrays.copyOfRange(ctBytes, 16, ctBytes.size)
              val key = ByteArray(KEY_SIZE / 8)
              val iv = ByteArray(IV_SIZE / 8)
              EvpKDF(password.toByteArray(), KEY_SIZE, IV_SIZE, saltBytes, key, iv)
              val cipher = Cipher.getInstance(HASH_CIPHER)
              val keyS = SecretKeySpec(key, AES)
              cipher.init(Cipher.DECRYPT_MODE, keyS, IvParameterSpec(iv))
              val plainText = cipher.doFinal(cipherTextBytes)
              return String(plainText)
          }
      
          private fun EvpKDF(
              password: ByteArray,
              keySize: Int,
              ivSize: Int,
              salt: ByteArray,
              resultKey: ByteArray,
              resultIv: ByteArray
          ): ByteArray {
              return EvpKDF(password, keySize, ivSize, salt, 1, KDF_DIGEST, resultKey, resultIv)
          }
      
          private fun EvpKDF(
              password: ByteArray,
              keySize: Int,
              ivSize: Int,
              salt: ByteArray,
              iterations: Int,
              hashAlgorithm: String,
              resultKey: ByteArray,
              resultIv: ByteArray
          ): ByteArray {
              val keySize = keySize / 32
              val ivSize = ivSize / 32
              val targetKeySize = keySize + ivSize
              val derivedBytes = ByteArray(targetKeySize * 4)
              var numberOfDerivedWords = 0
              var block: ByteArray? = null
              val hash = MessageDigest.getInstance(hashAlgorithm)
              while (numberOfDerivedWords < targetKeySize) {
                  if (block != null) {
                      hash.update(block)
                  }
                  hash.update(password)
                  block = hash.digest(salt)
                  hash.reset()
                  // Iterations
                  for (i in 1 until iterations) {
                      block = hash.digest(block!!)
                      hash.reset()
                  }
                  System.arraycopy(
                      block!!,
                      0,
                      derivedBytes,
                      numberOfDerivedWords * 4,
                      block.size.coerceAtMost((targetKeySize - numberOfDerivedWords) * 4)
                  )
                  numberOfDerivedWords += block.size / 4
              }
              System.arraycopy(derivedBytes, 0, resultKey, 0, keySize * 4)
              System.arraycopy(derivedBytes, keySize * 4, resultIv, 0, ivSize * 4)
              return derivedBytes // key + iv
          }
      
          private fun generateSalt(length: Int): ByteArray {
              return ByteArray(length).apply {
                  SecureRandom().nextBytes(this)
              }
          }
      
          private val SECRET_KEY = "===censifcneifcjnem"
          private val SECRET_KEY1 = "==cdseldxwscensifcneifcjnem"
          private val INIT_VECTOR = "dmwsakldmasfcmf==="
          private val INIT_VECTOR1 = "fcemdcfkmesdmwsa===kldmasfcmf"
      
      }


// Custom Tab for privacy policy
      
            implementation 'com.android.support:customtabs:28.0.0'
                implementation 'androidx.browser:browser:1.5.0'

            
                 val privacyPolicyUrl =
                                  "https://imagecropnwallpaperchanger.blogspot.com/2023/07/privacy-policy.html"
                              val builder = CustomTabsIntent.Builder()
                              builder.setToolbarColor(
                                  ContextCompat.getColor(
                                      this@MainActivity,
                                      android.R.color.black
                                  )
                              )
                              val customTabsIntent = builder.build()
                              customTabsIntent.launchUrl(this@MainActivity, Uri.parse(privacyPolicyUrl))

                                fun openCustomTab(link: String) {
        val builder = CustomTabsIntent.Builder()
        builder.setShareState(CustomTabsIntent.SHARE_STATE_ON)
        val color = resources.getColor(com.example.artimind.R.color.bg_color)
        val colorParams = CustomTabColorSchemeParams.Builder().setToolbarColor(color)
            .setSecondaryToolbarColor(color).build()
        builder.setDefaultColorSchemeParams(colorParams)
        val customTabsIntent = builder.build()
        customTabsIntent.launchUrl(this, Uri.parse(link))
    }
    

// Custom native Ad

              https://github.com/DevHamza090/Admob-AppLovin/tree/master/app/src/main/res/layout
                     https://github.com/googleads/googleads-mobile-android-native-templates/tree/main/nativetemplates/src/main/res/layout

                             
// Simple Exit Dialog

            fun simpleExistDialog(
                   context: Activity,
               ) {
                   val title = context.getString(R.string.exit)
                   val subTitle = context.getString(R.string.are_you_sure_want_to_exit)
                   MaterialAlertDialogBuilder(context).setTitle(title).setMessage(subTitle)
                       .setPositiveButton(context.getString(R.string.exit)) { _, _ ->
                           AppNavigator.finish(context)
                       }.setNegativeButton(context.getString(R.string.no)) { _, _ ->
                       }.show()
               }

// InApp Review & inAppReview

    implementation("com.google.android.play:review-ktx:2.0.1")

           fun Activity.inAppGoogleReview() {
               try {
                   val manager = ReviewManagerFactory.create(this)
                   val request = manager.requestReviewFlow()
                   request.addOnCompleteListener { task ->
                       if (task.isSuccessful) {
                           val reviewInfo = task.result
                           val flow = manager.launchReviewFlow(this, reviewInfo)
                           flow.addOnCompleteListener { _ ->
           
                           }
                       } else {
                           @ReviewErrorCode val reviewErrorCode =
                               (task.exception as ReviewException).errorCode
                           Timber.d("error in app review code %d ", reviewErrorCode)
                       }
                   }
               } catch (e: Exception) {
                   Timber.e(e.stackTraceToString())
               }
           }

// material lib views

            <com.google.android.material.button.MaterialButton
                   android:id="@+id/btn_allow"
                   android:layout_width="0dp"
                   android:layout_height="wrap_content"
                   android:layout_marginHorizontal="8dp"
                   android:layout_marginTop="16dp"
                   app:cornerRadius="@dimen/dimen_8x"
                   app:layout_constraintWidth_percent="0.7"
                   android:text="@string/allow"
                   android:textColor="?attr/colorTertiary"
                   app:backgroundTint="@android:color/transparent"
                   app:layout_constraintEnd_toEndOf="@+id/textView2"
                   app:layout_constraintStart_toStartOf="@+id/textView2"
                   app:layout_constraintTop_toBottomOf="@+id/textView2"
                   app:strokeColor="?attr/colorTertiary"
                   app:strokeWidth="2dp" />
        
            <com.google.android.material.materialswitch.MaterialSwitch
                   android:id="@+id/switch_select"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:layout_gravity="end"
                   android:layout_marginEnd="16dp" />
                   
             <com.google.android.material.textfield.TextInputLayout
                   android:id="@+id/textInputEditText"
                   android:layout_width="0dp"
                   android:layout_height="wrap_content"
                   android:backgroundTint="?attr/colorOnSecondary"
                   android:inputType="textWebEmailAddress"
                   app:endIconMode="clear_text"
                   app:endIconTint="?attr/colorSecondary"
                   app:boxCornerRadiusTopEnd="@dimen/dimen_16x"
                   app:boxCornerRadiusBottomEnd="@dimen/dimen_16x"
                   app:boxCornerRadiusBottomStart="@dimen/dimen_16x"
                   app:boxCornerRadiusTopStart="@dimen/dimen_16x"
                   app:endIconDrawable="@drawable/close"
                   android:padding="@dimen/dimen_8x"
                   app:layout_constraintEnd_toEndOf="parent"
                   app:layout_constraintStart_toStartOf="parent"
                   app:layout_constraintTop_toBottomOf="@id/txt_enter_current_email_label"
                   app:layout_constraintWidth_percent="0.80">
           
                   <com.google.android.material.textfield.TextInputEditText
                       android:id="@+id/editTextEmail"
                       android:layout_width="match_parent"
                       android:layout_height="wrap_content"
                       android:inputType="text"
                       android:maxLines="1" />
               </com.google.android.material.textfield.TextInputLayout>
               
            <androidx.constraintlayout.helper.widget.Grid
                   android:id="@+id/flow"
                   android:layout_width="0dp"
                   android:layout_height="0dp"
                   android:layout_margin="16dp"
                   android:padding="16dp"
                   android:textAlignment="center"
                   app:constraint_referenced_ids="key_1, key_2, key_3, key_4,key_5,key_6,key_7,key_8,key_9,key_none,key_0,key_x"
                   app:grid_columns="3"
                   app:grid_horizontalGaps="24dp"
                   app:grid_rows="4"
                   app:grid_verticalGaps="24dp"
                   app:layout_constraintBottom_toBottomOf="parent"
                   app:layout_constraintDimensionRatio="11:12"
                   app:layout_constraintLeft_toLeftOf="parent"
                   app:layout_constraintRight_toRightOf="parent" />
                   
            <com.google.android.material.floatingactionbutton.FloatingActionButton
                   android:id="@+id/floating_action_button"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:layout_margin="32dp"
                   android:contentDescription="@string/add_files"
                   app:layout_constraintBottom_toBottomOf="parent"
                   app:layout_constraintEnd_toEndOf="parent"
                   app:srcCompat="@drawable/ic_plus_vector" />
                   
              <androidx.appcompat.widget.SearchView
                           android:id="@+id/search_view"
                           android:layout_width="0dp"
                           android:layout_height="0dp"
                           android:layout_marginStart="8dp"
                           android:layout_marginEnd="16dp"
                           android:background="@drawable/search_bg"
                           android:backgroundTint="?attr/colorSurfaceVariant"
                           android:focusableInTouchMode="true"
                           android:textAppearance="?attr/textAppearanceCaption"
                           android:visibility="gone"
                           app:closeIcon="@drawable/close"
                           app:iconifiedByDefault="false"
                           app:layout_constraintBottom_toBottomOf="@+id/btn_back"
                           app:layout_constraintEnd_toEndOf="parent"
                           app:layout_constraintHeight_percent="0.65"
                           app:layout_constraintStart_toEndOf="@id/btn_back"
                           app:layout_constraintTop_toTopOf="@+id/btn_back"
                           app:queryBackground="@null"
                           app:queryHint="@string/search_hint"
                           app:searchIcon="@drawable/search"
                           tools:visibility="visible" />
           

             <com.google.android.material.progressindicator.LinearProgressIndicator
                   android:id="@+id/progress"
                   android:layout_width="0dp"
                   android:layout_height="wrap_content"
                   android:layout_marginTop="8dp"
                   android:padding="8dp"
                   app:layout_constraintLeft_toLeftOf="parent"
                   app:layout_constraintRight_toRightOf="parent"
                   app:layout_constraintTop_toBottomOf="@id/txt_progress"
                   tools:progress="30" />
                   
            <com.google.android.material.divider.MaterialDivider
                   android:layout_width="0dp"
                   android:layout_height="1dp"
                   android:layout_marginTop="@dimen/dimen_8x"
                   app:dividerColor="?attr/colorOnSurfaceVariant"
                   app:layout_constraintEnd_toEndOf="parent"
                   app:layout_constraintStart_toStartOf="parent"
                   app:layout_constraintTop_toBottomOf="@id/sort_by_group" />
                   

        <RadioGroup
            android:id="@+id/radio_group"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:paddingHorizontal="8dp"
            app:layout_constraintLeft_toLeftOf="@id/txt_header"
            app:layout_constraintRight_toRightOf="@id/txt_header"
            app:layout_constraintTop_toBottomOf="@id/txt_header">

            <com.google.android.material.radiobutton.MaterialRadioButton
                android:id="@+id/radio_default"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:paddingHorizontal="8dp"
                android:text="@string/default_lang"
                android:textAppearance="?attr/textAppearanceBody2" />

            <com.google.android.material.radiobutton.MaterialRadioButton
                android:id="@+id/radio_english"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:checked="true"
                android:paddingHorizontal="8dp"
                android:text="@string/english"
                android:textAppearance="?attr/textAppearanceBody2" />

            <com.google.android.material.radiobutton.MaterialRadioButton
                android:id="@+id/radio_portuguese"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:paddingHorizontal="8dp"
                android:text="@string/portuguese"
                android:textAppearance="?attr/textAppearanceBody2" />


            <com.google.android.material.radiobutton.MaterialRadioButton
                android:id="@+id/radio_francois"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:paddingHorizontal="8dp"
                android:text="@string/francois"
                android:textAppearance="?attr/textAppearanceBody2" />


            <com.google.android.material.radiobutton.MaterialRadioButton
                android:id="@+id/radio_hindi"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:paddingHorizontal="8dp"
                android:text="@string/hindi"
                android:textAppearance="?attr/textAppearanceBody2" />

            <com.google.android.material.radiobutton.MaterialRadioButton
                android:id="@+id/radio_turkle"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:paddingHorizontal="8dp"
                android:text="@string/turkle"
                android:textAppearance="?attr/textAppearanceBody2" />
        </RadioGroup>


        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_ok"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:text="@string/ok"
            app:cornerRadius="@dimen/dimen_8x"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@id/radio_group"
            app:layout_constraintWidth_percent="0.9" />
            
           <com.google.android.material.card.MaterialCardView xmlns:android="http://schemas.android.com/apk/res/android"
               xmlns:app="http://schemas.android.com/apk/res-auto"
               style="@style/Widget.Material3.CardView.Filled"
               android:layout_width="wrap_content"
               android:layout_marginHorizontal="@dimen/dimen_16x"
               app:cardBackgroundColor="?attr/colorSurface"
               app:cardCornerRadius="@dimen/dimen_16x"
               app:cardElevation="@dimen/dimen_8x"
               app:cardUseCompatPadding="true"
               android:layout_height="wrap_content"
               android:layout_gravity="center">


               </com.google.android.material.card.MaterialCardView>

           <com.google.android.material.appbar.MaterialToolbar
                   android:id="@+id/toolbar"
                   android:layout_width="0dp"
                   android:layout_height="wrap_content"
                   app:layout_constraintEnd_toEndOf="parent"
                   app:layout_constraintStart_toStartOf="parent"
                   app:layout_constraintTop_toTopOf="parent"
                   app:navigationIcon="@drawable/arrow_back"
                   app:navigationIconTint="?attr/colorOnBackground"
                   app:title="@string/private_files" />


                    <androidx.appcompat.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?actionBarSize"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:navigationIcon="@drawable/ic_back"
        app:title="Recovered Media"
        app:titleTextAppearance="@style/ToolbarTitleStyle" />

        binding.toolbar.setNavigationOnClickListener { onBackPressedDispatcher.onBackPressed() }


// RecyclerView BaseAdapter

            rvitemid.adapter =
                BaseAdapter(layoutResId = R.layout.rv_item, onBind = { itemView, item ->
                    // Implement logic for binding data to views
                }, onItemClickListener = { view, viewHolder ->
                    val position = viewHolder.bindingAdapterPosition
                }, diffCallback = object : DiffUtil.ItemCallback<String>() {
                    override fun areItemsTheSame(oldItem: String, newItem: String): Boolean {
                        // Implement logic to check if items are the same
                        return oldItem == newItem // Example: Assuming MyModel has an 'id' property
                    }

                    override fun areContentsTheSame(oldItem: String, newItem: String): Boolean {
                        // Implement logic to check if item contents are the same
                        return oldItem == newItem
                    }
                })
                
           class BaseAdapter<T>(
               private val layoutResId: Int,
               private val onBind: (View, T) -> Unit,
               private val onItemClickListener: (
                   View,
                   BaseAdapter<T>.BaseViewHolder,
               ) -> Unit,
               diffCallback: DiffUtil.ItemCallback<T>,
           ) : ListAdapter<T, BaseAdapter<T>.BaseViewHolder>(diffCallback) {
           
               override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BaseViewHolder {
                   val itemView = LayoutInflater.from(parent.context).inflate(layoutResId, parent, false)
                   return BaseViewHolder(itemView)
               }
           
               override fun onBindViewHolder(holder: BaseViewHolder, position: Int) {
                   val item = getItem(position)
                   onBind(holder.itemView, item)
               }
           
               inner class BaseViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
           
                   init {
                       onItemClickListener(itemView, this)
                   }
               }
           
               fun getListItem(position: Int): T = getItem(position)
           }

           
           

// EasyPathHelper


           object EasyPathHelper {
           
           fun ImageFilterView.setBorder(attr : Int) {
               val borderThickness = 8 // Set the desired border thickness in pixels
               val borderDrawable = ShapeDrawable(RectShape())
               val context = this.context
               val colorRes = EasyAndroidHelper.getColorFromThemeAttr(
                   context, attr)
               borderDrawable.paint.apply {
                   style = Paint.Style.STROKE
                   strokeWidth = borderThickness.toFloat()
                   color = colorRes
               }
               background = borderDrawable
           }
           

           
                      fun MaterialCardView.setRoundedCorners(radiusDp: Float) {
               val radiusPx = (radiusDp * Resources.getSystem().displayMetrics.density).toInt()
           
               val shapeAppearanceModel = if (radiusDp > 0f) {
                   ShapeAppearanceModel.builder()
                       .setTopLeftCorner(CornerFamily.ROUNDED, radiusPx.toFloat())
                       .setTopRightCorner(CornerFamily.ROUNDED, radiusPx.toFloat())
                       .setBottomLeftCorner(CornerFamily.ROUNDED, radiusPx.toFloat())
                       .setBottomRightCorner(CornerFamily.ROUNDED, radiusPx.toFloat())
                       .build()
               } else {
                   ShapeAppearanceModel.builder().build()
               }
           
               this.shapeAppearanceModel = shapeAppearanceModel
           }

           
                      fun ViewPager2.onPageChange(fn: (Int) -> Unit) {
               registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
                   override fun onPageSelected(position: Int) {
                       super.onPageSelected(position)
                       fn(position)
                   }
               })
           }
           

                      fun View.keyboardWithFocus() {
           
               requestFocus()
               if (hasWindowFocus()) {
                   showTheKeyboardNow()
               } else {
                   viewTreeObserver.addOnWindowFocusChangeListener(object :
                       ViewTreeObserver.OnWindowFocusChangeListener {
                       override fun onWindowFocusChanged(hasFocus: Boolean) {
                           if (hasFocus) {
                               this@keyboardWithFocus.showTheKeyboardNow()
                               viewTreeObserver.removeOnWindowFocusChangeListener(this)
                           }
                       }
                   })
               }
           }

                                 fun View.showTheKeyboardNow() {
               if (isFocused) {
                   post {
                       val imm = context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
                       imm.showSoftInput(this, InputMethodManager.SHOW_IMPLICIT)
                   }
               }
           }
           
           fun View.snackBar(resId: Any) {
               val message = if (resId is Int) context.getString(resId) else resId
               Snackbar.make(this, message.toString(), Snackbar.LENGTH_SHORT).show()
           }
           
           fun View.snackBar(message: String) {
               Snackbar.make(this, message, Snackbar.LENGTH_SHORT).show()
           }

                      fun SearchView.openKeyboard() {
               requestFocus()
               val inputMethodManager =
                   context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
               inputMethodManager.showSoftInput(this.findFocus(), InputMethodManager.SHOW_IMPLICIT)
           }
           
           
           fun SearchView.closeKeyboard() {
               clearFocus()
               val inputMethodManager =
                   context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
               inputMethodManager.hideSoftInputFromWindow(windowToken, 0)
           }

           fun Context.showBottomSheetDialog(view: View): BottomSheetDialog {
               val bottomSheetDialog = BottomSheetDialog(this)
               bottomSheetDialog.setContentView(view)
           
               // Set up BottomSheetDialog to open in full-screen mode
               val layoutParams = view.layoutParams
               val windowHeight = resources.displayMetrics.heightPixels
               layoutParams.height = windowHeight
               view.layoutParams = layoutParams
           
               // Disable dragging behavior
               val behavior = BottomSheetBehavior.from(view.parent as View)
               behavior.isDraggable = false
               bottomSheetDialog.behavior.state = BottomSheetBehavior.STATE_EXPANDED
           
           
               // Make the BottomSheetDialog not focusable
               bottomSheetDialog.window?.setFlags(
                   WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE,
                   WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE
               )
           
               // Set the window layout to match parent
               val layoutParamsForDialog = WindowManager.LayoutParams().apply {
                   copyFrom(bottomSheetDialog.window?.attributes)
                   width = WindowManager.LayoutParams.MATCH_PARENT
                   height = WindowManager.LayoutParams.MATCH_PARENT
               }
               bottomSheetDialog.window?.attributes = layoutParamsForDialog
           
               // Clear the not focusable flag to show the dialog
               bottomSheetDialog.window?.clearFlags(WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE)
               bottomSheetDialog.show()
           
               return bottomSheetDialog
           }
           

                                 
           val Context.rootPackageName: String get() = this.applicationContext.packageName
           
           val Context.fileProvider get() = this.rootPackageName + ".fileprovider"

           val String.decodeString
               get() = kotlin.run {
                   Base64.decode(this, Base64.DEFAULT).toString(charset("UTF-8"))
               }
           
           val Double.readableRound: Double get() = (this * 100.0).roundToInt() / 100.0
           
           val String.decrypt64
               get() = kotlin.run {
                   val data = Base64.decode(this, Base64.NO_WRAP); String(
                   data,
                   Charsets.UTF_8
               )
               }
           
           val String.md5
               get() = MessageDigest.getInstance("MD5").digest(this.toByteArray(Charsets.UTF_8)).toHex()
           
           fun ByteArray.toHex() = joinToString(separator = "") { byte -> "%02x".format(byte) }

               private fun getPath(context: Context, uri: Uri?): String {
                   val path: String?
                   val projection = arrayOf(MediaStore.Files.FileColumns.DATA)
                   val cursor = context.contentResolver.query(uri!!, projection, null, null, null)
                   if (cursor == null) {
                       path = uri.path
                   } else {
                       cursor.moveToFirst()
                       val columnIndex = cursor.getColumnIndexOrThrow(projection[0])
                       path = cursor.getString(columnIndex)
                       cursor.close()
                   }
                   return if (path.isNullOrEmpty()) uri.toString() else path
               }
           
               private fun getDataColumn(
                   context: Context,
                   uri: Uri?, selection: String?,
                   selectionArgs: Array<String>?,
               ): String? {
                   var cursor: Cursor? = null
                   val column = "_data"
                   val projection = arrayOf(column)
                   try {
                       if (uri == null) return null
                       cursor = context.contentResolver.query(
                           uri,
                           projection,
                           selection,
                           selectionArgs,
                           null
                       )
                       if (cursor != null && cursor.moveToFirst()) {
                           val index = cursor.getColumnIndexOrThrow(column)
                           return cursor.getString(index)
                       }
                   } finally {
                       cursor?.close()
                   }
                   return null
               }
           
               private fun isExternalStorageDocument(uri: Uri): Boolean =
                   "com.android.externalstorage.documents" == uri.authority
           
               private fun isDownloadsDocument(uri: Uri): Boolean =
                   "com.android.providers.downloads.documents" == uri.authority
           
               private fun isMediaDocument(uri: Uri): Boolean =
                   "com.android.providers.media.documents" == uri.authority
           
               private fun isGooglePhotosUri(uri: Uri): Boolean =
                   "com.google.android.apps.photos.content" == uri.authority
           
               fun getRealPathFromURI(context: Context, uri: Uri): String? {
                   when {
                       // DocumentProvider
                       DocumentsContract.isDocumentUri(context, uri) -> {
                           when {
                               // ExternalStorageProvider
                               isExternalStorageDocument(uri) -> {
                                   val docId = DocumentsContract.getDocumentId(uri)
                                   val split = docId.split(":").toTypedArray()
                                   val type = split[0]
                                   // This is for checking Main Memory
                                   return if ("primary".equals(type, ignoreCase = true)) {
                                       if (split.size > 1) Environment.getExternalStorageDirectory()
                                           .toString() + "/" + split[1]
                                       else Environment.getExternalStorageDirectory().toString() + "/"
                                       // This is for checking SD Card
                                   } else "storage" + "/" + docId.replace(":", "/")
                               }
           
                               isDownloadsDocument(uri) -> {
                                   val fileName = getPath(context, uri)
                                   if (fileName != null) {
                                       return Environment.getExternalStorageDirectory()
                                           .toString() + "/Download/" + fileName
                                   }
                                   var id = DocumentsContract.getDocumentId(uri)
                                   if (id.startsWith("raw:")) {
                                       id = id.replaceFirst("raw:".toRegex(), "")
                                       val file = File(id)
                                       if (file.exists()) return id
                                   }
                                   val contentUri = ContentUris.withAppendedId(
                                       Uri.parse("content://downloads/public_downloads"),
                                       java.lang.Long.valueOf(id)
                                   )
                                   return getDataColumn(context, contentUri, null, null)
                               }
           
                               isMediaDocument(uri) -> {
                                   val docId = DocumentsContract.getDocumentId(uri)
                                   val split = docId.split(":").toTypedArray()
                                   val type = split[0]
                                   var contentUri: Uri? = null
                                   when (type) {
                                       "image" -> {
                                           contentUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI
                                       }
           
                                       "video" -> {
                                           contentUri = MediaStore.Video.Media.EXTERNAL_CONTENT_URI
                                       }
           
                                       "audio" -> {
                                           contentUri = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI
                                       }
                                   }
                                   val selection = "_id=?"
                                   val selectionArgs = arrayOf(split[1])
                                   return getDataColumn(context, contentUri, selection, selectionArgs)
                               }
                           }
                       }
           
                       "content".equals(uri.scheme, ignoreCase = true) -> {
                           // Return the remote address
                           return if (isGooglePhotosUri(uri)) uri.lastPathSegment else getDataColumn(
                               context,
                               uri,
                               null,
                               null
                           )
                       }
           
                       "file".equals(uri.scheme, ignoreCase = true) -> {
                           return uri.path
                       }
                   }
                   return null
               }
           
               fun getNameDocUri(context: Context, docUriPath: String): String? {
                   return getRealPathFromURI(context, Uri.parse(docUriPath))
               }
           }

// Android Helper Class

                        inline fun getMediaFileFromPath(
                   applicationContext: Application,
                   filePath: String,
                   onMediaFileProcessed: (id: Long, name: String, path: String, contentUri: Uri) -> Unit,
               ) {
                   val context = applicationContext
                   val contentResolver = context.contentResolver
           
                   val projection = arrayOf(
                       MediaStore.Files.FileColumns._ID,
                       MediaStore.Files.FileColumns.DISPLAY_NAME,
                       MediaStore.Files.FileColumns.DATA
                   )
           
                   val selection = "${MediaStore.Files.FileColumns.DATA} = ?"
                   val selectionArgs = arrayOf(filePath)
           
                   val cursor = contentResolver.query(
                       MediaStore.Files.getContentUri("external"),
                       projection,
                       selection,
                       selectionArgs,
                       null
                   )
           
                   cursor?.use {
                       if (it.moveToFirst()) {
                           val id = it.getLong(it.getColumnIndexOrThrow(MediaStore.Files.FileColumns._ID))
                           val displayName =
                               it.getString(it.getColumnIndexOrThrow(MediaStore.Files.FileColumns.DISPLAY_NAME))
                           val path = it.getString(it.getColumnIndexOrThrow(MediaStore.Files.FileColumns.DATA))
                           val contentUri = ContentUris.withAppendedId(
                               MediaStore.Files.getContentUri("external"),
                               id.toLong()
                           )
                           onMediaFileProcessed(id, displayName, path, contentUri)
                       }
                   }
               }
               
           val File.isVideo
               get() = MimeTypeMap.getSingleton().getMimeTypeFromExtension(extension)
                   ?.startsWith("video", true) ?: false
           
           val File.isGif
               get() = MimeTypeMap.getSingleton().getMimeTypeFromExtension(extension)?.endsWith("gif", true)
                   ?: false
           
           val File.isAudio
               get() = MimeTypeMap.getSingleton().getMimeTypeFromExtension(extension)
                   ?.startsWith("audio", true) ?: false
           
           val File.isImage: Boolean
               get() = kotlin.run {
                   val mimeTypeFromExtension =
                       MimeTypeMap.getSingleton().getMimeTypeFromExtension(extension) ?: return@run false
                   when {
                       mimeTypeFromExtension.endsWith("jpeg") || mimeTypeFromExtension.endsWith("bmp") || mimeTypeFromExtension.endsWith(
                           "png"
                       ) || mimeTypeFromExtension.endsWith("jpg") -> true
           
                       else -> false
                   }
               }
           
           
           val File.lastModifiedFormattedString: String
               get() = kotlin.run {
           
                   val format = SimpleDateFormat("MMM dd, yyyy HH:mm", Locale.getDefault())
                   val date = Date(lastModified())
                   format.format(date)
               }
           
           
           val File.sizeFormattedString: String
               get() = kotlin.run {
                   val fileSize = this.length()
                   if (fileSize <= 0) {
                       return "0 B"
                   }
                   val units = arrayOf("B", "KB", "MB", "GB", "TB")
                   val digitGroups = (Math.log10(fileSize.toDouble()) / Math.log10(1024.0)).toInt()
           
                   val decimalFormat = DecimalFormat("#,##0.00")
                   "${
                       decimalFormat.format(
                           fileSize / Math.pow(
                               1024.0, digitGroups.toDouble()
                           )
                       )
                   } ${units[digitGroups]}"
               }
           
           val List<File>.sizeFormattedString: String
               get() = run {
                   val totalSize = sumOf { file -> file.length() }
                   if (totalSize <= 0) {
                       return "0 B"
                   }
                   val units = arrayOf("B", "KB", "MB", "GB", "TB")
                   val digitGroups = (Math.log10(totalSize.toDouble()) / Math.log10(1024.0)).toInt()
           
                   val decimalFormat = DecimalFormat("#,##0.00")
                   "${
                       decimalFormat.format(
                           totalSize / Math.pow(
                               1024.0, digitGroups.toDouble()
                           )
                       )
                   } ${units[digitGroups]}"
               }
           
           
           suspend fun File.bitmap(context: Context): Bitmap {
               return withContext(IO) {
                   Glide.with(context).asBitmap().load(this@bitmap.absolutePath)
                       .diskCacheStrategy(DiskCacheStrategy.ALL).submit().get()
               }
           }
           
           suspend fun File.bitmap(context: Context, width: Int, height: Int): Bitmap {
               return withContext(IO) {
                   Glide.with(context).asBitmap().load(this@bitmap.absolutePath)
                       .diskCacheStrategy(DiskCacheStrategy.ALL).submit().get()
               }
           }
           
           val File.videoResolution: Pair<Int, Int>
               get() = kotlin.run {
                   val retriever = MediaMetadataRetriever()
                   retriever.setDataSource(this.absolutePath)
                   val width =
                       retriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_VIDEO_WIDTH)?.toInt() ?: 0
                   val height =
                       retriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_VIDEO_HEIGHT)?.toInt()
                           ?: 0
                   return Pair(width, height)
               }
           
           suspend fun File.imageResolutionFormattedString(context: Context): String {
               val bitmap = bitmap(context)
               val width = bitmap.width
               val height = bitmap.height
               val megapixels = (width.toDouble() * height.toDouble()) / 1000000.0
               return "${width}x${height} (${String.format("%.1f", megapixels)}MP)"
           }
           
           val File.videoResolutionFormattedString
               get() : String = kotlin.run {
                   val (width, height) = videoResolution
                   val megapixels = (width.toDouble() * height.toDouble()) / 1000000.0
                   "${width}x${height} (${String.format("%.1f", megapixels)}MP)"
               }
           
           
           suspend fun File.resolutionFormattedString(context: Context): String {
               return when {
                   isImage -> imageResolutionFormattedString(context)
                   isVideo -> this.videoResolutionFormattedString
                   else -> imageResolutionFormattedString(context)
               }
           }
           
           fun File.scanStorage(context: Context) {
               return MediaScannerConnection.scanFile(context, arrayOf(this.path), null, null)
           }
           
           fun List<File>.scanStorage(context: Context) {
               val files = this.map { it.path }.toTypedArray()
               MediaScannerConnection.scanFile(context, files, null, null)
           }
           
           
           val File.contentUri: Uri
               get() = kotlin.run {
                   if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
                       when {
                           isImage -> MediaStore.Images.Media.getContentUri(MediaStore.VOLUME_EXTERNAL)
                           isVideo -> MediaStore.Video.Media.getContentUri(MediaStore.VOLUME_EXTERNAL)
                           isAudio -> MediaStore.Audio.Media.getContentUri(MediaStore.VOLUME_EXTERNAL)
                           else -> MediaStore.Files.getContentUri(MediaStore.VOLUME_EXTERNAL)
                       }
                   } else {
                       when {
                           isImage -> MediaStore.Images.Media.EXTERNAL_CONTENT_URI
                           isVideo -> MediaStore.Video.Media.EXTERNAL_CONTENT_URI
                           isAudio -> MediaStore.Audio.Media.EXTERNAL_CONTENT_URI
                           else -> MediaStore.Files.getContentUri("external")
                       }
           
                   }
               }
           
           

           enum class WallpaperType {
               HOME_SCREEN_WALLPAPER,
               LOCK_SCREEN_WALLPAPER,
               HOME_AND_LOCK_SCREEN_WALLPAPER,
           }

                      object EasyAndroidHelper {
                      
                      
                          suspend fun setWallpaper(
                              context: Context,
                              path: String,
                              wallpaperType: WallpaperType,
                          ) {
                      
                              val wallpaperManager = WallpaperManager.getInstance(context)
                              withContext(Dispatchers.IO) {
                      
                                  val bitmap = File(path).bitmap(context)
                      
                                  when (wallpaperType) {
                                      WallpaperType.HOME_SCREEN_WALLPAPER -> {
                                          wallpaperManager.setBitmap(bitmap)
                                      }
                      
                                      WallpaperType.LOCK_SCREEN_WALLPAPER -> {
                                          wallpaperManager.setBitmap(
                                              bitmap, null, false, WallpaperManager.FLAG_LOCK
                                          )
                      
                                      }
                      
                                      WallpaperType.HOME_AND_LOCK_SCREEN_WALLPAPER -> {
                                          wallpaperManager.setBitmap(bitmap)
                                          wallpaperManager.setBitmap(
                                              bitmap, null, false, WallpaperManager.FLAG_LOCK
                                          )
                                      }
                                  }
                      
                              }
                          }
                      
                          fun startFileShareIntent(
                              context: Context,
                              filePath: String,
                              title: String,
                          ) {
                              val shareIntent = Intent(Intent.ACTION_SEND).apply {
                                  type = "*/*"
                                  flags = Intent.FLAG_GRANT_READ_URI_PERMISSION
                                  putExtra(Intent.EXTRA_TEXT, title);
                                  val fileURI = FileProvider.getUriForFile(
                                      context, context.fileProvider, File(filePath)
                                  )
                                  putExtra(Intent.EXTRA_STREAM, fileURI)
                              }
                              context.startActivity(Intent.createChooser(shareIntent, title))
                          }
                      
                          fun startMultipleFilesShareIntent(
                              context: Context,
                              filePath: List<String>,
                              title: String,
                          ) {
                              val shareIntent = Intent(Intent.ACTION_SEND_MULTIPLE).apply {
                                  type = "*/*"
                                  putExtra(Intent.EXTRA_TEXT, title);
                                  val fileURI = filePath.map { path ->
                                      FileProvider.getUriForFile(
                                          context, context.fileProvider, File(path)
                                      )
                                  }
                                  flags = Intent.FLAG_GRANT_READ_URI_PERMISSION
                                  val fileArrayList = arrayListOf<Uri>()
                                  fileArrayList.addAll(fileURI)
                                  putParcelableArrayListExtra(Intent.EXTRA_STREAM, fileArrayList)
                              }
                              context.startActivity(Intent.createChooser(shareIntent, title))
                          }
                      
                          fun sendGmail(
                              context: Context,
                              subject: String,
                              body: String,
                              toEmail: String,
                              recipientEmail: List<String> = emptyList(),
                          ) {
                              try {
                                  val intent = Intent(Intent.ACTION_SEND).apply {
                                      data = Uri.parse("mailto:$toEmail")
                                      if (recipientEmail.isNotEmpty()) putExtra(
                                          Intent.EXTRA_EMAIL, recipientEmail.toTypedArray()
                                      )
                                      putExtra(Intent.EXTRA_SUBJECT, subject)
                                      putExtra(Intent.EXTRA_TEXT, body)
                                      setPackage(EasyGet.gmailPackageName)
                                  }
                                  context.startActivity(intent)
                              } catch (e: ActivityNotFoundException) {
                                  val fallbackIntent = Intent(Intent.ACTION_VIEW).apply {
                                      data =
                                          Uri.parse("mailto:$toEmail?subject=${Uri.encode(subject)}&body=${Uri.encode(body)}")
                                  }
                                  context.startActivity(fallbackIntent)
                              } catch (e: Exception) {
                                  Timber.e(e.stackTraceToString())
                              }
                          }
                      
                          fun sendGmailWIIthImages(
                              context: Context,
                              subject: String,
                              body: String,
                              toEmail: String,
                              fileURI: List<String> = emptyList(),
                          ) {
                              try {
                                  val intent = Intent(Intent.ACTION_SEND_MULTIPLE).apply {
                                      type = "message/rfc822"
                                      putExtra(Intent.EXTRA_EMAIL, arrayOf(toEmail))
                                      putExtra(Intent.EXTRA_SUBJECT, subject)
                                      putExtra(Intent.EXTRA_TEXT, body)
                                      val fileArrayList = arrayListOf<Uri>()
                                      if (fileURI.isNotEmpty()) {
                                          val uriFromPath = EasyMediaStorageHelper.getUriFromPath(context, fileURI)
                                          fileArrayList.addAll(uriFromPath)
                                          putParcelableArrayListExtra(Intent.EXTRA_STREAM, fileArrayList)
                                      }
                                      flags = Intent.FLAG_GRANT_READ_URI_PERMISSION
                                      setPackage(EasyGet.gmailPackageName)
                                  }
                                  context.startActivity(intent)
                              } catch (e: Exception) {
                                  Timber.e(e, "Failed to send email to Gmail")
                              }
                          }
                      
                          fun shareAppWithLink(context: Context, appNameResId: Int) {
                              val applicationContext = context.applicationContext
                              try {
                                  val shareIntent = Intent(Intent.ACTION_SEND)
                                  shareIntent.type = "text/plain"
                                  shareIntent.putExtra(
                                      Intent.EXTRA_SUBJECT, applicationContext.resources.getString(appNameResId)
                                  )
                                  var shareMessage = "\nLet me recommend you this application\n\n"
                                  shareMessage = """
                              ${shareMessage}https://play.google.com/store/apps/details?id=${context.rootPackageName}
                              
                              """.trimIndent()
                                  shareIntent.putExtra(Intent.EXTRA_TEXT, shareMessage)
                                  context.startActivity(Intent.createChooser(shareIntent, "choose one"))
                              } catch (e: Exception) {
                                  Timber.e(e.stackTraceToString())
                              }
                          }
                      
                          fun openMediaShowIntent(context: Context, path: String) {
                              try {
                                  val file = File(path)
                                  val intent = Intent(Intent.ACTION_VIEW) //
                                      .setDataAndType(
                                          FileProvider.getUriForFile(context, context.fileProvider, file),
                                          if (file.name.endsWith(".mp4")) "video/*" else "image/*"
                                      ).addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
                                  context.startActivity(intent)
                              } catch (e: Exception) {
                                  Timber.e(e.stackTraceToString())
                              }
                          }
                      
                          fun getDrawableUri(context: Context, drawableId: Int): Uri? {
                              return try {
                                  val resources = context.resources
                                  val uri = Uri.parse(
                                      ContentResolver.SCHEME_ANDROID_RESOURCE + "://" + resources.getResourcePackageName(
                                          drawableId
                                      ) + '/' + resources.getResourceTypeName(drawableId) + '/' + resources.getResourceEntryName(
                                          drawableId
                                      )
                                  )
                                  uri
                              } catch (e: Resources.NotFoundException) {
                                  Timber.e(e.stackTraceToString())
                                  null
                              }
                          }
                      
                          fun getColorFromThemeAttr(context: Context, themeAttr: Int): Int {
                              val typedValue = TypedValue()
                              val theme = context.theme
                              theme.resolveAttribute(themeAttr, typedValue, true)
                              return ContextCompat.getColor(context, typedValue.resourceId)
                          }
                      
                          fun setTintMenuIcon(context: Context, item: MenuItem, color: Int) {
                              val normalDrawable = item.icon
                              val wrapDrawable = DrawableCompat.wrap(normalDrawable!!)
                              val colorFromThemeAttr = EasyAndroidHelper.getColorFromThemeAttr(context, color)
                              DrawableCompat.setTint(wrapDrawable, colorFromThemeAttr)
                              item.icon = wrapDrawable
                          }
                      
                      
                          fun setSearchViewSearchIconTint(
                              searchView: SearchView,
                              context: Context,
                              colorResId: Int,
                              fromAppTheme: Boolean = true,
                          ) {
                              try {
                      //            val searchIcon =
                      //                searchView.findViewById(androidx.appcompat.R.id.search_mag_icon) as ImageView
                      //            val originalDrawable = searchIcon.drawable
                      //            val wrappedDrawable = DrawableCompat.wrap(originalDrawable).mutate()
                      //
                      //            val color = if (fromAppTheme) getColorFromThemeAttr(context, colorResId)
                      //            else ContextCompat.getColor(context, colorResId)
                      //            DrawableCompat.setTint(wrappedDrawable, color)
                      //            searchIcon.setImageDrawable(wrappedDrawable)
                      //            val collapsedSearchPlate =
                      //                searchView.findViewById(androidx.appcompat.R.id.search_plate)  as View
                      //            val collapsedSearchIcon =
                      //                collapsedSearchPlate.findViewById(androidx.appcompat.R.id.search_mag_icon) as ImageView
                      //            collapsedSearchIcon.setImageDrawable(wrappedDrawable)
                              } catch (e: Exception) {
                                   Timber.e(e.stackTraceToString())
                              }
                          }
                      
                      
                      }

// BioMetricHelper 

    implementation("androidx.biometric:biometric-ktx:1.2.0-alpha05")

           val easyBioMetricHelper =
                       EasyBioMetricHelper(requireActivity()).setTitle(getString(androidx.biometric.R.string.use_biometric_label))
                           .setSubTitle(getString(androidx.biometric.R.string.biometric_or_screen_lock_prompt_message))
                           .setDescription(getString(androidx.biometric.R.string.biometric_prompt_message))
                           .build().onSuccess {
                               viewModel.onEvent(LockerFolderViewModel.LockFolderEvent.OnBioMetricLogin(true))
                           }.onFailure {
                               toast(it)
                           }

           class EasyBioMetricHelper(
               private val activity: FragmentActivity,
           ) {
               private lateinit var promptInfo: BiometricPrompt.PromptInfo
               private lateinit var biometricPrompt: BiometricPrompt
           
               private var onSuccess: () -> Unit = { }
               private var onFaiure: (String) -> Unit = { }
           
               private var mTitle = activity.getString(androidx.biometric.R.string.use_biometric_label)
               private var mSubTitle =
                   activity.getString(androidx.biometric.R.string.biometric_or_screen_lock_prompt_message)
               private var mDescription =
                   activity.getString(androidx.biometric.R.string.biometric_prompt_message)
           
           
               fun build(): EasyBioMetricHelper {
                   bioMetricResultListener()
                   val biometricHardWareAvailable = isBiometricHardWareAvailable(activity)
                   initPrompt(mTitle, mSubTitle, mDescription, biometricHardWareAvailable)
                   return this
               }
           
               private fun initPrompt(
                   title: String,
                   subtitle: String,
                   description: String,
                   setDeviceCred: Boolean,
               ) {
                   if (setDeviceCred) {
                       if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
                           val authFlag =
                               androidx.biometric.BiometricManager.Authenticators.DEVICE_CREDENTIAL or BiometricManager.Authenticators.BIOMETRIC_STRONG
                           promptInfo =
                               BiometricPrompt.PromptInfo.Builder().setTitle(title).setSubtitle(subtitle)
                                   .setDescription(description).setAllowedAuthenticators(authFlag).build()
                       } else {
                           @Suppress("DEPRECATION") promptInfo =
                               BiometricPrompt.PromptInfo.Builder().setTitle(title).setSubtitle(subtitle)
                                   .setDescription(description).setDeviceCredentialAllowed(true).build()
                       }
                   } else {
                       promptInfo = BiometricPrompt.PromptInfo.Builder().setTitle(title).setSubtitle(subtitle)
                           .setDescription(description).setNegativeButtonText("Cancel").build()
                   }
           
               }
           
               private fun bioMetricResultListener() {
                   val executor = ContextCompat.getMainExecutor(activity)
           
                   biometricPrompt =
                       BiometricPrompt(activity, executor, object : BiometricPrompt.AuthenticationCallback() {
                           override fun onAuthenticationSucceeded(
                               result: BiometricPrompt.AuthenticationResult,
                           ) {
                               super.onAuthenticationSucceeded(result)
                               onSuccess()
                           }
           
                           override fun onAuthenticationFailed() {
                               super.onAuthenticationFailed()
                               onFaiure("Failed to unlock")
                           }
                       })
               }
           
               fun setTitle(title: String): EasyBioMetricHelper {
                   mTitle = title
                   return this
               }
           
               fun setSubTitle(subTitle: String): EasyBioMetricHelper {
                   mSubTitle = subTitle
                   return this
               }
           
               fun setDescription(description: String): EasyBioMetricHelper {
                   mDescription = description
                   return this
               }
           
               fun onSuccess(fn: () -> Unit): EasyBioMetricHelper {
                   onSuccess = fn
                   return this
               }
           
               fun onFailure(fn: (String) -> Unit): EasyBioMetricHelper {
                   onFaiure = fn
                   return this
               }

                      inline fun ComponentActivity.onBackPress(
               lifecycle: LifecycleOwner = this,
               crossinline fn: () -> Unit,
           ) {
               onBackPressedDispatcher.addCallback(lifecycle, object : OnBackPressedCallback(true) {
                   override fun handleOnBackPressed() {
                       fn()
                   }
               })
           }

           
               fun launchPrompt() {
                   biometricPrompt.authenticate(promptInfo)
               }
           
           
               inner class EasyBioMatrixBuilder {
           
               }
           
               companion object {
           
                   fun isBiometricHardWareAvailable(context: Context): Boolean {
                       var result = false
                       val biometricManager = BiometricManager.from(context)
           
                       if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
                           when (biometricManager.canAuthenticate(BiometricManager.Authenticators.DEVICE_CREDENTIAL or BiometricManager.Authenticators.BIOMETRIC_STRONG)) {
                               BiometricManager.BIOMETRIC_SUCCESS -> result = true
                               BiometricManager.BIOMETRIC_ERROR_NO_HARDWARE -> result = false
                               BiometricManager.BIOMETRIC_ERROR_HW_UNAVAILABLE -> result = false
                               BiometricManager.BIOMETRIC_ERROR_NONE_ENROLLED -> result = false
                               BiometricManager.BIOMETRIC_ERROR_SECURITY_UPDATE_REQUIRED -> result = true
           
                               BiometricManager.BIOMETRIC_ERROR_UNSUPPORTED -> result = true
           
                               BiometricManager.BIOMETRIC_STATUS_UNKNOWN -> result = false
                           }
                       } else {
                           when (biometricManager.canAuthenticate()) {
                               BiometricManager.BIOMETRIC_SUCCESS -> result = true
                               BiometricManager.BIOMETRIC_ERROR_NO_HARDWARE -> result = false
                               BiometricManager.BIOMETRIC_ERROR_HW_UNAVAILABLE -> result = false
                               BiometricManager.BIOMETRIC_ERROR_NONE_ENROLLED -> result = false
                           }
                       }
                       return result
                   }
           
                   fun deviceHasPasswordPinLock(context: Context): Boolean {
                       val keymgr =
                           context.getSystemService(AppCompatActivity.KEYGUARD_SERVICE) as KeyguardManager
                       if (keymgr.isKeyguardSecure) return true
                       return false
                   }
               }
           
           
           }

// Pattern view lock view

            implementation("io.github.itsxtt:pattern-lock:0.2.0")

                id("org.jlleitschuh.gradle.ktlint") version "11.0.0"

               <com.itsxtt.patternlock.PatternLockView
                   android:id="@+id/pattern_view"
                   android:layout_width="0dp"
                   android:layout_height="wrap_content"
                   android:alpha="0.5"
                   app:layout_constraintBottom_toBottomOf="parent"
                   app:layout_constraintEnd_toEndOf="parent"
                   app:layout_constraintStart_toStartOf="parent"
                   app:plv_regularDotColor="?attr/colorOnSurfaceVariant"
                   app:plv_regularLineColor="?attr/colorOnPrimary" />
                   

// zoomin and zomm out zomable imageview gesterimageview

           https://github.com/alexvasilkov/GestureViews


            <com.alexvasilkov.gestures.views.GestureImageView
                   android:id="@+id/imv"
                   android:layout_width="0dp"
                   android:layout_height="0dp"
                   android:scaleType="centerCrop"
                   android:transitionName="imageTransition"
                   app:layout_constraintBottom_toBottomOf="parent"
                   app:layout_constraintLeft_toLeftOf="parent"
                   app:layout_constraintRight_toRightOf="parent"
                   app:layout_constraintTop_toTopOf="parent"
                   tools:src="@tools:sample/backgrounds/scenic" />
                   
// Tack Permission

           https://github.com/guolindev/PermissionX


// Dots Indicatore


    implementation("com.tbuonomo:dotsindicator:5.0")

            <com.tbuonomo.viewpagerdotsindicator.WormDotsIndicator
                   android:id="@+id/worm_dots_indicator"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   app:dotsColor="?attr/colorPrimary"
                   app:dotsCornerRadius="8dp"
                   app:dotsSize="14dp"
                   app:layout_constraintVertical_bias="0.95"
                   app:dotsSpacing="4dp"
                   app:dotsStrokeColor="?attr/colorPrimaryContainer"
                   app:dotsStrokeWidth="2dp"
                   app:layout_constraintBottom_toBottomOf="parent"
                   app:layout_constraintEnd_toEndOf="parent"
                   app:layout_constraintStart_toStartOf="parent"
                   app:layout_constraintTop_toTopOf="parent" />
                   
// attractive Native ad layouts github online
           
           https://github.com/DevHamza090/Admob-AppLovin/tree/master/app/src/main/res/layout
           https://github.com/googleads/googleads-mobile-android-native-templates/tree/main/nativetemplates/src/main/res/layout

// set statusbar color

           fun setStatusBarGradiant(
               activity: Activity,
               NavigationBarColor: Int?,
               mSYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION: Boolean?
           ) {
               if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                   val window = activity.window
                   val background: Drawable = activity.resources.getDrawable(R.drawable.ic_top_bar)
                   window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS)
           //            window.statusBarColor = activity.resources.getColor(android.R.color.transparent)
                   window.navigationBarColor = activity.resources.getColor(NavigationBarColor!!)
                   window.setBackgroundDrawable(background)
                   if (mSYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION == true){
                       window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION;
                   }
               }
           }

// Drawable to Bitmap

           private fun drawableToBitmap(drawable: Drawable, v: View): Bitmap {
                   val bitmap: Bitmap
           
                   if (drawable is BitmapDrawable) {
                       if (drawable.bitmap != null) return drawable.bitmap
           
                   } else if (drawable is GradientDrawable) {
                       bitmap = Bitmap.createBitmap(v.width, v.height, Bitmap.Config.ARGB_8888)
                       val canvas = Canvas(bitmap)
                       drawable.setBounds(0, 0, canvas.width, canvas.height)
                       drawable.draw(canvas)
                       return bitmap
                   }
           
                   bitmap = if (drawable.intrinsicWidth <= 0 || drawable.intrinsicHeight <= 0) {
                       Bitmap.createBitmap(1, 1, Bitmap.Config.ARGB_8888) // Single color bitmap will be created of 1x1 pixel
                   } else {
                       Bitmap.createBitmap(drawable.intrinsicWidth, drawable.intrinsicHeight, Bitmap.Config.ARGB_8888)
                   }
                   val canvas = Canvas(bitmap)
                   drawable.setBounds(0, 0, canvas.width, canvas.height)
                   drawable.draw(canvas)
                   return bitmap
               }
    

// Custom Native Ad Layout

           <?xml version="1.0" encoding="utf-8"?>
           <com.google.android.gms.ads.nativead.NativeAdView xmlns:android="http://schemas.android.com/apk/res/android"
               android:layout_width="match_parent"
               android:layout_height="match_parent">
           
               <androidx.cardview.widget.CardView xmlns:app="http://schemas.android.com/apk/res-auto"
                   android:layout_width="match_parent"
                   android:layout_height="wrap_content"
                   app:cardCornerRadius="6dp"
                   app:cardElevation="0dp"
                   app:cardUseCompatPadding="false">
           
                   <LinearLayout
                       android:layout_width="match_parent"
                       android:layout_height="wrap_content"
                       android:background="@drawable/ads_bg"
                       android:orientation="vertical">
           
                       <RelativeLayout
                           android:layout_width="match_parent"
                           android:layout_height="wrap_content"
                           android:orientation="vertical">
           
                           <LinearLayout
                               android:layout_width="match_parent"
                               android:layout_height="wrap_content"
                               android:orientation="vertical">
           
                               <LinearLayout
                                   android:layout_width="match_parent"
                                   android:layout_height="0dp"
                                   android:layout_weight="0.7"
                                   android:orientation="vertical">
           
                                   <RelativeLayout
                                       android:id="@+id/layout_img"
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content">
           
                                       <androidx.appcompat.widget.AppCompatImageView
                                           android:id="@+id/ad_image_bg"
                                           android:layout_width="match_parent"
                                           android:layout_height="wrap_content"
                                           android:layout_marginLeft="1dp"
                                           android:layout_marginTop="1dp"
                                           android:layout_marginRight="1dp"
                                           android:scaleType="centerCrop" />
           
                                       <androidx.appcompat.widget.AppCompatImageView
                                           android:id="@+id/ad_image"
                                           android:layout_width="match_parent"
                                           android:layout_height="200dp"
                                           android:layout_marginLeft="1dp"
                                           android:layout_marginTop="1dp"
                                           android:layout_marginRight="1dp"
                                           android:adjustViewBounds="true" />
           
                                       <com.google.android.gms.ads.nativead.MediaView
                                           android:id="@+id/ad_media"
                                           android:layout_width="match_parent"
                                           android:layout_height="200dp"
                                           android:visibility="visible" />
                                   </RelativeLayout>
           
                                   <LinearLayout
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content"
                                       android:layout_gravity="center"
                                       android:orientation="vertical"
                                       android:paddingTop="5dp"
                                       android:visibility="gone">
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_price"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:paddingStart="5dp"
                                           android:paddingLeft="5dp"
                                           android:paddingEnd="5dp"
                                           android:paddingRight="5dp"
                                           android:text=""
                                           android:textSize="12sp"
                                           android:visibility="gone" />
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_store"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:paddingStart="5dp"
                                           android:paddingLeft="5dp"
                                           android:paddingEnd="5dp"
                                           android:paddingRight="5dp"
                                           android:text=""
                                           android:textSize="12sp"
                                           android:visibility="gone" />
                                   </LinearLayout>
                               </LinearLayout>
           
                               <LinearLayout
                                   android:layout_width="match_parent"
                                   android:layout_height="wrap_content"
                                   android:orientation="horizontal"
                                   android:padding="4dp">
           
                                   <androidx.appcompat.widget.AppCompatImageView
                                       android:id="@+id/ad_app_icon"
                                       android:layout_width="45dp"
                                       android:layout_height="45dp"
                                       android:adjustViewBounds="true" />
           
                                   <LinearLayout
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content"
                                       android:orientation="vertical"
                                       android:paddingLeft="5dp">
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_headline"
                                           android:layout_width="match_parent"
                                           android:layout_height="wrap_content"
                                           android:ellipsize="end"
           
                                           android:maxLines="2"
           
                                           android:textSize="14sp"
                                           android:textStyle="bold" />
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_body"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:ellipsize="end"
                                           android:maxLines="1"
                                           android:textColor="@android:color/darker_gray"
                                           android:textSize="10sp"
                                           android:visibility="visible" />
           
                                       <LinearLayout
                                           android:layout_width="match_parent"
                                           android:layout_height="wrap_content"
                                           android:visibility="gone">
           
                                           <androidx.appcompat.widget.AppCompatTextView
                                               android:id="@+id/ad_advertiser"
                                               android:layout_width="wrap_content"
                                               android:layout_height="match_parent"
                                               android:ellipsize="end"
                                               android:gravity="bottom"
                                               android:maxLines="1"
                                               android:text=""
                                               android:textSize="13sp"
                                               android:textStyle="bold" />
           
           
                                           <RatingBar
                                               android:id="@+id/ad_stars"
                                               style="?android:attr/ratingBarStyleSmall"
                                               android:layout_width="wrap_content"
                                               android:layout_height="wrap_content"
                                               android:isIndicator="true"
                                               android:numStars="5"
                                               android:stepSize="0.5" />
                                       </LinearLayout>
                                   </LinearLayout>
                               </LinearLayout>
           
                               <androidx.appcompat.widget.AppCompatButton
                                   android:id="@+id/ad_call_to_action"
                                   android:layout_width="match_parent"
                                   android:layout_height="wrap_content"
                                   android:layout_gravity="center_vertical"
                                   android:background="#202D62"
                                   android:gravity="center"
           
                                   android:textColor="@color/white"
                                   android:textSize="16sp" />
                           </LinearLayout>
           
                           <androidx.appcompat.widget.AppCompatTextView
                               android:layout_width="wrap_content"
                               android:layout_height="wrap_content"
                               android:layout_alignParentTop="true"
                               android:layout_gravity="bottom|left|center_vertical|center_horizontal|center"
                               android:layout_margin="1dp"
                               android:background="@drawable/ads_bg"
                               android:text=" Ad "
                               android:textColor="@color/black"
                               android:textSize="10sp"
                               android:textStyle="bold"
                               android:visibility="visible" />
                       </RelativeLayout>
           
                   </LinearLayout>
               </androidx.cardview.widget.CardView>
           </com.google.android.gms.ads.nativead.NativeAdView>
           


// Older Android Studio All versions

           https://developer.android.com/studio/emulator_archive

// File path save as a bitmap to pdf & file to bitmap

          fun File.toBmp(): Bitmap = BitmapFactory.decodeFile(absolutePath, BitmapFactory.Options())


           val photoPrinter = PrintHelper(this@SaveActivity)
                          photoPrinter.scaleMode = PrintHelper.SCALE_MODE_FIT
                          imagePath?.let {
                              photoPrinter.printBitmap(getString(R.string.app_name), bitmap!!)
                          }

// Facebook Shimmer layout

          implementation 'com.facebook.shimmer:shimmer:0.5.0'
        
              <com.facebook.shimmer.ShimmerFrameLayout
                                    android:layout_width="@dimen/_120sdp"
                                    android:layout_height="@dimen/_30sdp"
                                    app:shimmer_auto_start="true"
                                    app:shimmer_duration="2000"
                                    app:shimmer_fixed_width="@dimen/_100sdp"
                                    app:shimmer_highlight_color="@color/white">
        
                                    <LinearLayout
                                        android:layout_width="match_parent"
                                        android:layout_height="match_parent"
                                        android:background="@drawable/shimmer_layout">
        
                                    </LinearLayout>
        
                                </com.facebook.shimmer.ShimmerFrameLayout>
        
                               // shimmer_layout.xml
                               
                                        <shape
                android:shape="rectangle" xmlns:android="http://schemas.android.com/apk/res/android">
            
                <solid
                    android:color="#61FFFFFF">
            
                </solid>
            
                <corners
                    android:radius="@dimen/_12sdp">
            
                </corners>
            
            </shape>

            
            
// When Change the Language in beta

        fun setAppLocale(activity: Activity, languageCode: String) {
            App.putString(LANGUAGE_KEY, languageCode)
            val locale = Locale(languageCode)
            Locale.setDefault(locale)
            val configuration = Configuration()
            configuration.setLocale(locale)
            activity.resources.updateConfiguration(configuration, activity.resources.displayMetrics)
        }
        
        build.gradl file
        
         bundle {
                language {
                    enableSplit = false
                }
            }
        
                list.add(LanguageModel("Arabic", false, "Ar", "عربي", "#CEE3F7"))
            list.add(LanguageModel("English", false, "en", "English", "#DBF1DB"))
            list.add(LanguageModel("French", false, "fr", "Français", "#CFF6E6"))
            list.add(LanguageModel("German", false, "de", "Deutsch", "#F7DBD2"))
            list.add(LanguageModel("Japanese", false, "ja", "日本語", "#FDF2D2"))
            list.add(LanguageModel("Korean", false, "ko", "한국인", "#D7F0F4"))
            list.add(LanguageModel("Portuguese", false, "pt", "Português", "#E4DBDB"))
            list.add(LanguageModel("Swedish", false, "sv", "svenska", "#E8D2FD"))
            list.add(LanguageModel("Hindi", false, "hi", "हिन्दी", "#F7DBD2"))
        
             // for Arabic in menifest file
        
               tools:replace="android:supportsRtl"
               android:supportsRtl="false"
               
// Firebase Event Debug

        adb shell setprop debug.firebase.analytics.app package_name
        adb shell setprop debug.firebase.analytics.app       
        usage: setprop NAME VALUE


// Facebook testad id

       implementation 'com.facebook.android:audience-network-sdk:6.+'
 
         native:  "CAROUSEL_IMG_SQUARE_APP_INSTALL#YOUR_PLACEMENT_ID",
         intern:  "CAROUSEL_IMG_SQUARE_APP_INSTALL#YOUR_PLACEMENT_ID",
         banner:  "IMG_16_9_APP_INSTALL#YOUR_PLACEMENT_ID",
             

// Test AD ID ADX

        Sample ad unit ID	
        App Open	/6499/example/app-open
        Banner	/6499/example/banner
        Interstitial	/6499/example/interstitial
        Rewarded	/6499/example/rewarded
        Rewarded Interstitial	/21775744923/example/rewarded_interstitial
        Native	/6499/example/native
        Native Video	/6499/example/native-video

        implementation 'com.github.SimpleMobileTools:Simple-Commons:c31d0fda56'
        
// Firebase Notification

        implementation 'com.google.firebase:firebase-messaging:23.1.1'
        
        <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
        
         <service
                android:name=".MyFirebaseMessagingService"
                android:exported="true">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        
          
          val flags = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
              PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
          } else PendingIntent.FLAG_UPDATE_CURRENT
            
          class MyFirebaseMessagingService : FirebaseMessagingService() {
              var fcm_vaule = ""
              override fun onNewToken(token: String) {
                  super.onNewToken(token)
                  ("onNewToken: ====>$token").log()
              }
  
      override fun onMessageReceived(remoteMessage: RemoteMessage) {
          ("on Message Recive From Firebase").log()
          if (remoteMessage.data.isNotEmpty()) {
              val data = remoteMessage.data
              data.forEach {
                  val key: String = it.key
                  fcm_vaule = it.value
                  CAT_VAULE = it.value
                  ("Firebase FCM: Key: $key Value: $fcm_vaule").log()
              }
          }
  
          // Handle notification payload if present
          if (remoteMessage.notification != null) {
              // Handle notification message
              val notificationTitle = remoteMessage.notification!!.title
              val notificationBody = remoteMessage.notification!!.body
              // Process notification message
              ("Title: $notificationTitle Body: $notificationBody").log()
          }
  
          remoteMessage.notification?.let {
              sendNotification(it.title, it.body, remoteMessage)
          }
      }
  
      private fun sendNotification(
          title: String?, messageBody: String?, remoteMessage: RemoteMessage
      ) {
          val intent = Intent(this, SearchDataActivity::class.java).putExtra("keyword", fcm_vaule)
              .putExtra("isFromNotify", true)
  
          intent.flags =
              Intent.FLAG_ACTIVITY_REORDER_TO_FRONT or Intent.FLAG_ACTIVITY_CLEAR_TOP or Intent.FLAG_ACTIVITY_NEW_TASK
          intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK or Intent.FLAG_ACTIVITY_NO_HISTORY)
          intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP)
  
          val pendingIntent = PendingIntent.getActivity(
              this, 0, intent, flags
          )
  
          val channelId = "default_channel_id"
          val defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION)
          val notificationBuilder =
              NotificationCompat.Builder(this, channelId).setSmallIcon(R.mipmap.ic_launcher)
                  .setContentTitle(title).setContentText(messageBody).setAutoCancel(true).setVibrate(
                      longArrayOf(
                          1000, 1000, 1000, 1000, 1000
                      )
                  ).setSound(defaultSoundUri).setOnlyAlertOnce(true).setContentIntent(pendingIntent)
  
          val notificationManager =
              getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
  
          if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
              val channel = NotificationChannel(
                  channelId, "Important Notification", NotificationManager.IMPORTANCE_HIGH
              )
              channel.vibrationPattern = longArrayOf(1000, 1000, 1000, 1000, 1000)
              notificationManager.createNotificationChannel(channel)
          }
          notificationManager.notify(0, notificationBuilder.build())
      }
  }

        in App Background.
        SplaceAct
         if (intent.extras != null) {
                ("22222").log()
                for (firebase_key in intent.extras!!.keySet()) {
                    ("33333").log()
                    firebase_value = intent.extras!![firebase_key].toString()
                    Log.d("FATZ", "----- Fuirebase --------- Key: $firebase_key Value: $firebase_value")
                    if (firebase_key!! == "FIREBASE_KEY") {
                        isOpenSearchAct = true
                        break
                    }
                }
            }

// Custom Popup PopupManager WindowManager Dialog

        class WallatPopupDialog constructor(activity: Activity, anchorView: View) {
            private var popupWindow: PopupWindow? = null
            var bind = WallatpopupDialogBinding.inflate(activity.layoutInflater)
        
            init {
                val popupView: WallatpopupDialogBinding =
                    WallatpopupDialogBinding.inflate(activity.layoutInflater)
                LayoutInflater.from(activity).inflate(R.layout.wallatpopup_dialog, null)
        
                // Create a PopupWindow
                popupWindow = PopupWindow(
                    popupView.root, ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT
                )
        
                // Set background color to make it look like a dialog
                popupWindow!!.setBackgroundDrawable(ColorDrawable(Color.TRANSPARENT))
                popupWindow!!.isOutsideTouchable = true
                popupWindow!!.isFocusable = true
        
                val location = IntArray(2)
                anchorView.getLocationOnScreen(location)
        //        popupWindow!!.showAtLocation(anchorView, Gravity.TOP, 0, 60)
                popupWindow!!.showAsDropDown(anchorView)
                popupView.apply {
                    addcaseid.click {
                        popupWindow!!.dismiss()
                        activity.startActivity(Intent(activity, AddCaseActivity::class.java))
                    }
                    mybalanceid.click {
                        popupWindow!!.dismiss()
                        activity.startActivity(Intent(activity, MyWallatAct::class.java))
                    }
                    kyvverifyid.click {
                        popupWindow!!.dismiss()
                        activity.startActivity(Intent(activity, KycVerifyAct::class.java))
                    }
                }
        
        //        popupDialog = Dialog(activity);
        //        popupDialog!!.requestWindowFeature(Window.FEATURE_NO_TITLE);
        //        popupDialog!!.window!!.setBackgroundDrawable(ColorDrawable(Color.TRANSPARENT));
        //        popupDialog!!.setCanceledOnTouchOutside(true);
        //
        //        popupDialog!!.show()
        
        
        //        dialog.setContentView(bind.root)
        //        dialog.window!!.setLayout(
        //            WindowManager.LayoutParams.WRAP_CONTENT,
        //            WindowManager.LayoutParams.WRAP_CONTENT
        //        )
        //        dialog.window!!.setBackgroundDrawableResource(android.R.color.transparent)
        //        dialog.show()
        //
        //        bind.apply {
        //            cancelid.click { dialog.dismiss() }
        //            okid.click { dialog.dismiss() }
        //        }
            }
        }




         <androidx.cardview.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:elevation="@dimen/_2sdp"
        android:layout_marginTop="@dimen/_6sdp"
        android:layout_marginEnd="@dimen/_10sdp"
        android:layout_marginStart="@dimen/_10sdp"
        app:cardCornerRadius="@dimen/_3sdp"
        app:cardElevation="@dimen/_2sdp"
        app:cardMaxElevation="@dimen/_2sdp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:gravity="center"
            android:orientation="vertical">

            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                android:layout_marginTop="@dimen/_3sdp"
                android:fontFamily="@font/fredoka_medium"
                android:gravity="center"
                android:text="Total Cash"
                android:textColor="#94000000"
                android:textSize="@dimen/_13sdp" />

     </LinearLayout>

    </androidx.cardview.widget.CardView>




// BaseFragment


    abstract class BaseFragment<T : ViewBinding> : Fragment() {
        lateinit var bind: T
        override fun onCreateView(
            inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
        ): View? {
            bind = getActivityBinding(layoutInflater)
            if (Build.VERSION.SDK_INT > Build.VERSION_CODES.Q) {
                requireActivity().window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS)
                requireActivity().window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS)
                requireActivity().window.decorView.systemUiVisibility =
                    View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
                requireActivity().window.statusBarColor = Color.TRANSPARENT
            } else {
                requireActivity().window.setFlags(
                    WindowManager.LayoutParams.FLAG_FULLSCREEN,
                    WindowManager.LayoutParams.FLAG_FULLSCREEN
                )
            }
            return bind.root
        }
    
        override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
            super.onViewCreated(view, savedInstanceState)
            initUI()
        }
    
        abstract fun getActivityBinding(inflater: LayoutInflater): T
    
        abstract fun initUI()
    
        fun <T> T.tos() = Toast.makeText(requireActivity(), "$this", Toast.LENGTH_SHORT).show()
        fun <T> T.tosL() = Toast.makeText(requireActivity(), "$this", Toast.LENGTH_LONG).show()
    
        fun openUri(uri: String) = startActivity(Intent(Intent.ACTION_VIEW, Uri.parse(uri)))
    
        fun rateUs() {
            try {
                val marketUri = Uri.parse("market://details?id=${requireActivity().packageName}")
                val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
                startActivity(marketIntent)
            } catch (e: Exception) {
                val marketUri =
                    Uri.parse("https://play.google.com/store/apps/details?id=${requireActivity().packageName}")
                val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
                startActivity(marketIntent)
            }
        }
    }

// Custom TabLayout


    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:background="@drawable/tabbg"
        android:elevation="30dp"
        android:padding="@dimen/_3sdp"
        app:tabGravity="fill"
        app:tabIndicator="@android:color/transparent"
        android:layout_marginTop="@dimen/_3sdp"
        app:tabIndicatorColor="@android:color/transparent"
        app:tabMode="fixed"
        android:layout_marginStart="@dimen/_12sdp"
        android:layout_marginEnd="@dimen/_12sdp"
        android:layout_marginBottom="@dimen/_10sdp"
        app:tabPaddingEnd="-2dp"
        app:tabPaddingStart="-2dp">

        <com.google.android.material.tabs.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:icon="@drawable/homemaabc" />

        <com.google.android.material.tabs.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:icon="@drawable/consettrofytrofy" />

        <com.google.android.material.tabs.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:icon="@drawable/menproficon" />

        <com.google.android.material.tabs.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:icon="@drawable/more_iconicon" />

    </com.google.android.material.tabs.TabLayout>


        tabs.setOnTabSelectedListener(object : OnTabSelectedListener {
                    override fun onTabSelected(tab: TabLayout.Tab) {
                        val tabIconColor =
                            ContextCompat.getColor(this@MainActivity, R.color.tabtextselected)
                        tab.icon!!.setColorFilter(tabIconColor, PorterDuff.Mode.SRC_IN)
                        if (tab.position == 0) {
                            replaceFragment(HomeFragment())
                            head.visibility = View.VISIBLE
                        } /*else if (tab.position == 1) {
                            replaceFragment(TrofiFragment())
                            head.visibility = View.VISIBLE
                        } else if (tab.position == 2) {
                            replaceFragment(ProfileFragment())
                            head.visibility = View.GONE
                        } else {
                            replaceFragment(MenuFragment())
                            head.visibility = View.VISIBLE
                        }*/
                    }

                    override fun onTabUnselected(tab: TabLayout.Tab) {
                        val tabIconColor =
                            ContextCompat.getColor(this@MainActivity, R.color.tabtextunselected)
                        tab.icon!!.setColorFilter(tabIconColor, PorterDuff.Mode.SRC_IN)
                    }

                    override fun onTabReselected(tab: TabLayout.Tab) {}
                })



                 private fun replaceFragment(fragment: Fragment) {
        val fragmentManager = supportFragmentManager
        val transaction = fragmentManager.beginTransaction()
        transaction.replace(R.id.fragmentid, fragment)
        transaction.commit()
    }
    

// Blur ImageView

    object BlurBuilder {
        private const val BITMAP_SCALE = 0.4f
        private const val BLUR_RADIUS = 7.5f
        fun blur(context: Context?, image: Bitmap): Bitmap {
            val width = Math.round(image.width * BITMAP_SCALE)
            val height = Math.round(image.height * BITMAP_SCALE)
            val inputBitmap = Bitmap.createScaledBitmap(image, width, height, false)
            val outputBitmap = Bitmap.createBitmap(inputBitmap)
            val rs = RenderScript.create(context)
            val theIntrinsic = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs))
            val tmpIn = Allocation.createFromBitmap(rs, inputBitmap)
            val tmpOut = Allocation.createFromBitmap(rs, outputBitmap)
            theIntrinsic.setRadius(BLUR_RADIUS)
            theIntrinsic.setInput(tmpIn)
            theIntrinsic.forEach(tmpOut)
            tmpOut.copyTo(outputBitmap)
            return outputBitmap
        }
    }

// hide a device bottom navigation view

    // Check if the device has a navigation bar
          val decorView = window.decorView
          val uiOptions = (View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
                  or View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY)
          decorView.systemUiVisibility = uiOptions

// Firebase Getting a SHA1 Key
 
         keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android


// Call Multipart Api Call

       @Keep
       interface ApiService {
           @Multipart
           @POST("img2img")
           fun makeApiCall(
               @Part file: MultipartBody.Part,
               @Part("prompt") prompt: RequestBody,
               @Part("strength") strength: Double = 0.35,
               @Part("guidance_scale") guidance_scale: Double = 5.5,
               @Part("num_inference_steps") num_inference_steps: Int = 100,
               @Part("negative_prompt") negative_prompt: String = "ugly, tiling, poorly drawn hands, poorly drawn feet, poorly drawn face, out of frame, extra limbs",
           ): Call<ResModel>
       }


       @Keep
       interface onResponce {
           fun <T> onSuccess(res: T)
           fun onError(error: String)
       }
       
       @Keep
       class ApiCaller {
           fun makeApiCall(reqBodyFile: MultipartBody.Part, promypart: RequestBody, onNext: onResponce) {
               val interceptor by lazy { HttpLoggingInterceptor() } //todo remove this line in production
               interceptor.setLevel(HttpLoggingInterceptor.Level.BODY) //todo remove this line in production
       
               val okClient by lazy {
                   OkHttpClient.Builder().addInterceptor(Interceptor { chain: Interceptor.Chain ->
                       val originalRequest: Request = chain.request()
                       val requestBuilder: Request.Builder =
                           originalRequest.newBuilder().addHeader("Cache-Control", "no-cache")
                               .method(originalRequest.method, originalRequest.body)
                       val request: Request = requestBuilder.build()
                       chain.proceed(request)
                   }).connectTimeout(30, TimeUnit.SECONDS).addInterceptor(interceptor)
                       .readTimeout(30, TimeUnit.SECONDS).build()
               }
       
               val retrofit by lazy {
                   Retrofit.Builder().baseUrl(BASE_URL).addConverterFactory(GsonConverterFactory.create())
                       .client(okClient).build()
               }
               val myApi by lazy {
                   retrofit.create(ApiService::class.java)
               }
               myApi.makeApiCall(prompt = promypart, file = reqBodyFile)
                   .enqueue(object : Callback<ResModel> {
                       override fun onResponse(
                           call: Call<ResModel>, response: retrofit2.Response<ResModel>
                       ) {
                           ("Rs Code: ${response.code()}} | ${response.message()} ").log()
                           if (response.code() == 200) onNext.onSuccess(response.body())
                           else {
                               onNext.onError("Something Went Wrong Try Again!")
                           }
                       }
       
                       override fun onFailure(call: Call<ResModel>, t: Throwable) {
                           ("OnError: ${t.message}").log()
                           onNext.onError(t.message!!)
                       }
                   })
           }
       }


                        val myDir = File(cacheDir.toURI())
                        if (!myDir.exists()) myDir.mkdirs()
                        val file = File(myDir, "croped_kb.jpeg")
                        if (file.exists()) file.delete()
                        try {
                            val out = FileOutputStream(file)
                            resource.compress(
                                Bitmap.CompressFormat.JPEG, 30, out
                            )
                            out.flush()
                            out.close()
                        } catch (e: Exception) {
                            (e.message!!).log()
                        }

                        ("Size: " + getFileSize(file.length()) + " | W: ${resource.width} | H: ${resource.height}").log()

                        ("Bitmap Genrated: ${file.path}").log()

                        val requestBody =
                            RequestBody.create("multipart/form-data".toMediaTypeOrNull(), file)
                        val filePart: MultipartBody.Part =
                            MultipartBody.Part.createFormData("image", file.name, requestBody)
                        val promypart =
                            RequestBody.create("text/plain".toMediaTypeOrNull(), promtext)

                        try {
                            if (hasInternetConnect()) {
                                ApiCaller().makeApiCall(filePart, promypart, object : onResponce {
                                    override fun <T> onSuccess(res: T) {
                                        resbody = res as ResModel
                                        DataHolder.largeData = resbody.data
                                        DataHolder.selectedImg = imageUrl!!
                                        runOnUiThread {
                                            if (!isDestroyed) {
                                                bind.miraclloading.gon()
                                                bind.doneconstraint.visible()
                                            }
                                        }
                                    }

                                    override fun onError(error: String) {
                                        ("onError: $error").log()
                                        runOnUiThread {
                                            SnackbarShow(
                                                this@MiracleAct,
                                                bind.root,
                                                "Some thing went wrong Try Again !"
                                            )
                                        }
                                    }
                                })
                            } else {
                                runOnUiThread {
                                    SnackbarShow(
                                        this@MiracleAct,
                                        bind.root,
                                        "Please connect to the internet and try again."
                                    )
                                }
                            }
                        } catch (e: Exception) {
                            e.printStackTrace()
                            SnackbarShow(
                                this@MiracleAct, bind.root, "Some thing went wrong Try Again !"
                            )
                            "API call crashed with exception: ${e.message}".log()
                        }


// Remove Firebase Crash Log

       release {
                 ...
                 // Add the following line to disable Crashlytics
                 firebaseCrashlytics {
                     mappingFileUploadEnabled false
                     ndkSymbolUploadEnabled false
                     obfuscationMappingFileUploadEnabled false
                 }
             }
             

// InApp Purchases - Subscription Flow

     <uses-permission android:name="com.android.vending.BILLING" />
      <uses-permission android:name="android.permission.INTERNET" />
      
    //billing lib 
        def billing_version = "6.0.1"
        implementation "com.android.billingclient:billing:$billing_version"
        implementation "com.android.billingclient:billing-ktx:$billing_version"

     -- Application Class ---
      
         var billingClient: BillingClient? = null

     billingClient = BillingClient.newBuilder(
                applicationContext
            ).setListener { purchaseResult: BillingResult, purchases: List<Purchase>? ->
                    if (purchaseResult.responseCode == BillingClient.BillingResponseCode.OK && purchases != null) {
                        ("MyManager: OK").log()
                        for (purchase in purchases) {
                            if (purchase.purchaseState == Purchase.PurchaseState.PURCHASED && !purchase.isAcknowledged) {
                                billingClient!!.acknowledgePurchase(
                                    AcknowledgePurchaseParams.newBuilder()
                                        .setPurchaseToken(purchase.purchaseToken).build()
                                ) { result: BillingResult ->
                                    ("acknowledgePurchase : PURCHASED && Acknowledged " + result.debugMessage).log()
                                    isPurchased = true
                                }
                            }
                        }
                    } else ("MyManager: $purchaseResult $purchases").log()
                }.enablePendingPurchases().build()

        -- Splace Act ---
         checkPurchased {
                ("Here Splace").log()
                onNext()
            }

       --- const.kt ----
       
    //todo --------- InApp Subscriptions ----------
    var isPurchased: Boolean = false
    const val subId_12Month = "sub_12_month"
    const val subId_6Month = "sub_6_month"
    const val subId_Month = "sub_month"
    var subIs12Month: Boolean = false
    var subIs6Month: Boolean = false
    var subIsMonth: Boolean = false

           fun checkPurchased(block: () -> Unit) {
        billingClient!!.startConnection(object : BillingClientStateListener {
            override fun onBillingSetupFinished(billingResult: BillingResult) {
    
                if (billingResult.responseCode == BillingClient.BillingResponseCode.OK) {
                    CoroutineScope(Dispatchers.IO).launch {
                        billingClient!!.queryPurchasesAsync(
                            QueryPurchasesParams.newBuilder()
                                .setProductType(BillingClient.ProductType.SUBS).build()
                        ) { result, purchasesList ->
                            "purchasesList size : ${purchasesList.size}".log()
                            if (result.responseCode == BillingClient.BillingResponseCode.OK && purchasesList.isNotEmpty()) {
    
                                for (s in purchasesList) {
                                    "s: $s".log()
    
                                    if (s.products[0] == subId_12Month && s.purchaseState == Purchase.PurchaseState.PURCHASED) subIs12Month =
                                        true
                                    else if (s.products[0] == subId_6Month && s.purchaseState == Purchase.PurchaseState.PURCHASED) subIs6Month =
                                        true
                                    else if (s.products[0] == subId_Month && s.purchaseState == Purchase.PurchaseState.PURCHASED) subIsMonth =
                                        true
    
                                    isPurchased = true
                                }
                            }
    
                            CoroutineScope(Dispatchers.Main).launch {
                                block()
                            }
                        }
                    }
    
                } else {
                    "check failed: $billingResult".log()
                    block()
                }
            }
    
            override fun onBillingServiceDisconnected() {
                "onBillingServiceDisconnected".log()
                block()
            }
        })
    }


      --- Final Activity ------

       
        data class Subs(
            val details: ProductDetails,
            val priceDec: String,
            val discount: String? = null,
            val suggestion: String? = null,
            val tags: String? = null,
            val subscribed: Boolean? = false
        )
        
        class SubscriptionAct : BaseAct<ActivitySubscriptionBinding>() {
            var monthPrice: String? = null
            var sixPrice: String? = null
            var yearPrice: String? = null
            var plan: Subs? = null
        
            override fun getActivityBinding(inflater: LayoutInflater) =
                ActivitySubscriptionBinding.inflate(layoutInflater)
        
            override fun initUI() {
        
                checkPurchased {
                    setInAppOption()
                }
        
                bind.apply {
        
                    proid.click {
                        billingClient!!.launchBillingFlow(
                            this@SubscriptionAct,
                            BillingFlowParams.newBuilder().setProductDetailsParamsList(
                                listOf(
                                    BillingFlowParams.ProductDetailsParams.newBuilder()
                                        .setProductDetails(plan!!.details)
                                        .setOfferToken(plan!!.details.subscriptionOfferDetails!![0].offerToken)
                                        .build()
                                )
                            ).build()
                        )
                    }
                }
            }
        
            private fun setInAppOption() {
                billingClient!!.startConnection(object : BillingClientStateListener {
                    override fun onBillingSetupFinished(billingResult: BillingResult) {
        
                        if (billingResult.responseCode == BillingClient.BillingResponseCode.OK) {
        
                            billingClient!!.queryProductDetailsAsync(
                                QueryProductDetailsParams.newBuilder().setProductList(
                                    listOf(
                                        QueryProductDetailsParams.Product.newBuilder()
                                            .setProductId(subId_Month).setProductType(
                                                BillingClient.ProductType.SUBS
                                            ).build(),
                                        QueryProductDetailsParams.Product.newBuilder()
                                            .setProductId(subId_6Month).setProductType(
                                                BillingClient.ProductType.SUBS
                                            ).build(),
                                        QueryProductDetailsParams.Product.newBuilder()
                                            .setProductId(subId_12Month).setProductType(
                                                BillingClient.ProductType.SUBS
                                            ).build()
                                    )
                                ).build()
                            ) { _, productDetailsList ->
        
                                val subsList = arrayListOf<Subs>()
                                for (p in productDetailsList) {
                                    val price =
                                        p.subscriptionOfferDetails!![0].pricingPhases.pricingPhaseList[0].priceAmountMicros / 1000000
                                    val Fprice =
                                        p.subscriptionOfferDetails!![0].pricingPhases.pricingPhaseList[0].formattedPrice
        
                                    val priceString: String
                                    val monthlyPrice: Long?
        
                                    when (p.productId) {
                                        subId_12Month -> {
                                            yearPrice = Fprice
                                            priceString = "₹${price} / Yearly"
                                            monthlyPrice = price / 12
                                        }
        
                                        subId_6Month -> {
                                            sixPrice = Fprice
                                            priceString = "₹${price} / 6 Months"
                                            monthlyPrice = price / 6
                                        }
        
                                        else -> {
                                            monthPrice = Fprice
                                            priceString = "₹${price} / Monthly"
                                            monthlyPrice = null
                                        }
                                    }
        
                                    val isSubscribed = when (p.productId) {
                                        subId_12Month -> subIs12Month
                                        subId_6Month -> subIs6Month
                                        subId_Month -> subIsMonth
                                        else -> null
                                    }
        
                                    subsList.add(
                                        Subs(
                                            details = p,
                                            priceDec = if (monthlyPrice == null) "<font color=#000000> $priceString  </font>" else "<font color=#000000> $priceString  </font> <br> <font color=#969696>  <small>  $monthlyPrice per month  </small></font>",
                                            suggestion = if (p.productId == "sub_12_month") "Recommended" else if (p.productId == "sub_6_month") "Most Popular" else null,
                                            subscribed = isSubscribed
                                        )
                                    )
                                }
        
                                runOnUiThread {
                                    "setText()".log()
        //                            bind.rvSubs.adapter = SubsAdapter(this@SubscriptionActivity, subsList) {
        //                                plan = it
        //                            }
        
        //                            bind.pb.gon()
        //                            bind.nsv.visible()
                                }
                            }
        
                        } else {
                            "onBillingSetupFinished: else ${billingResult}".log()
                            runOnUiThread {
                                ("Hewre ELse").log()
                            }
                        }
                    }
        
                    override fun onBillingServiceDisconnected() {
                        "onBillingServiceDisconnected".log()
                        setInAppOption()
                    }
                })
            }
        }

                

// SpeedyAnimation LinearLayout 

    class SpeedyLinearLayoutManager : LinearLayoutManager {
        constructor(context: Context?) : super(context)
        constructor(context: Context?, orientation: Int, reverseLayout: Boolean) : super(context, orientation, reverseLayout)
        constructor(context: Context?, attrs: AttributeSet?, defStyleAttr: Int, defStyleRes: Int) : super(context, attrs, defStyleAttr, defStyleRes)
    
        override fun smoothScrollToPosition(recyclerView: RecyclerView, state: RecyclerView.State, position: Int) {
            val linearSmoothScroller: LinearSmoothScroller = object : LinearSmoothScroller(recyclerView.context) {
    
                override fun calculateSpeedPerPixel(displayMetrics: DisplayMetrics): Float {
                    return MILLISECONDS_PER_INCH / displayMetrics.densityDpi
                }
            }
            linearSmoothScroller.targetPosition = position
            startSmoothScroll(linearSmoothScroller)
        }
    
        companion object {
            private const val MILLISECONDS_PER_INCH = 5000f //default is 25f (bigger = slower)
        }
    }


// CoordinatorLayout with AppBarLayout animation top 

    <androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
    
        <com.google.android.material.appbar.AppBarLayout
            android:id="@+id/mainappbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">
    
            <com.google.android.material.appbar.CollapsingToolbarLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                app:contentScrim="?attr/colorPrimary"
                app:expandedTitleMarginEnd="64dp"
                app:expandedTitleMarginStart="48dp"
                app:layout_scrollFlags="scroll|exitUntilCollapsed"
                app:title="@string/app_name">
    
                <ImageView
                    android:id="@+id/mainbackdrop"
                    android:layout_width="match_parent"
                    android:layout_height="300dp"
                    android:scaleType="centerCrop"
                    android:src="@drawable/banner_main"
                    app:layout_collapseMode="parallax" />

                     <com.google.android.material.appbar.CollapsingToolbarLayout/>
                     <com.google.android.material.appbar.AppBarLayout/>

     <androidx.core.widget.NestedScrollView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_behavior="@string/appbar_scrolling_view_behavior">

            </androidx.core.widget.NestedScrollView>
            
// InAppUpdate Manager

    implementation 'com.google.android.play:core:1.10.3'
    
    class InAppUpdateManager(private val mActivity: Activity) {
        private val mAppUpdateManager: AppUpdateManager = AppUpdateManagerFactory.create(mActivity)
    
        fun checkForAppUpdate() {
            mAppUpdateManager.appUpdateInfo.addOnSuccessListener { appUpdateInfo: AppUpdateInfo ->
                if (appUpdateInfo.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE
                    && appUpdateInfo.isUpdateTypeAllowed(AppUpdateType.FLEXIBLE)
                ) {
                    try {
                        mAppUpdateManager.startUpdateFlowForResult(
                            appUpdateInfo,
                            AppUpdateType.FLEXIBLE,
                            mActivity,
                            REQUEST_CODE
                        )
                    } catch (e: SendIntentException) {
                        Log.e(
                            TAG,
                            "Error when starting update flow",
                            e
                        )
                    }
                }
            }
        }
    
        fun onActivityResult(requestCode: Int, resultCode: Int) {
            if (requestCode == REQUEST_CODE) {
                if (resultCode != RESULT_OK) {
                    Log.e(
                        TAG,
                        "Update failed! Result code: $resultCode"
                    )
                }
            }
        }
    
        fun onResume() {
            mAppUpdateManager.appUpdateInfo.addOnSuccessListener { appUpdateInfo: AppUpdateInfo ->
                if (appUpdateInfo.updateAvailability() == UpdateAvailability.DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS) {
                    try {
                        mAppUpdateManager.startUpdateFlowForResult(
                            appUpdateInfo,
                            AppUpdateType.FLEXIBLE,
                            mActivity,
                            REQUEST_CODE
                        )
                    } catch (e: SendIntentException) {
                        Log.e(
                            TAG,
                            "Error when starting update flow",
                            e
                        )
                    }
                }
            }
        }
    
        companion object {
            private const val TAG = "InAppUpdateManager"
            private const val REQUEST_CODE = 1000
        }
    }

    use for activity
    
      inAppUpdateManager = InAppUpdateManager(this);
      inAppUpdateManager.checkForAppUpdate();

      
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        inAppUpdateManager.onActivityResult(requestCode, resultCode);
    }

    override fun onResume() {
        super.onResume()
        inAppUpdateManager.onResume();
    }


// Dialog In dark theme

    class SaveDialog(activity: Activity) {
        val alert: AlertDialog.Builder =
            AlertDialog.Builder(activity, AlertDialog.THEME_DEVICE_DEFAULT_DARK)
    
        init {
            alert.setMessage("Photo Art saved into Gallery")
            alert.setNegativeButton("Dismiss", null)
            alert.setCancelable(false)
            alert.setPositiveButton("View", object : DialogInterface.OnClickListener {
                override fun onClick(p0: DialogInterface?, p1: Int) {
                    ("View Cclikc").log()
                    val intent = Intent(Intent.ACTION_VIEW)
                    intent.type = "image/*"
                    activity.startActivity(intent)
                }
            })
            alert.create().show()
        }
    }
            
// SnackBarView

    class SnackbarShow(activity: Activity, rootView: ViewGroup, msg: String) {
        var snack: Snackbar = Snackbar.make(rootView, msg, Snackbar.LENGTH_SHORT)
    
        init {
            snack.setBackgroundTint(activity.resources.getColor(R.color.dark_bg_color))
    //        snack.setAction("View") {
    //            val intent = Intent(Intent.ACTION_VIEW)
    //            intent.type = "image/*"
    //            activity.startActivity(intent)
    //        }
            snack.setActionTextColor(activity.resources.getColor(R.color.white));
            snack.show()
        }
    }


// Ucrop Lib

    //Ucrop
      implementation 'com.github.yalantis:ucrop:2.2.6-native'

        <activity
            android:name="com.yalantis.ucrop.UCropActivity"
            android:screenOrientation="portrait"
            android:theme="@style/Theme.AppCompat.Light.NoActionBar" />

  
              fun String.startUcrop(activity: Activity) {
      val options = UCrop.Options()
      options.setCompressionQuality(80) // Set desired compression quality
      options.setAspectRatioOptions(
          0,
          AspectRatio("3:2", 3f, 4f),
          AspectRatio("5:3", 5f, 3f),
          AspectRatio("16:9", 16f, 9f),
          AspectRatio("1:1", 1f, 1f)
      )
      options.setToolbarColor(activity.resources.getColor(R.color.white)) // Set desired toolbar color
      options.setStatusBarColor(activity.resources.getColor(R.color.bg_color)) // Set desired status bar color
      UCrop.of(
          this.toUri(), Uri.fromFile(File(activity.cacheDir, "cropped_img.png"))
      ).withOptions(options).start(activity, UCrop.REQUEST_CROP)
    }


    onActivity Result

    
    @Deprecated("Deprecated in Java")
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        ("onAct Result").log()
        if (requestCode == REQUEST_CROP && data != null) {
            val croppedImageUri = UCrop.getOutput(data)
            if (croppedImageUri != null) {
                ("Crop: $croppedImageUri").log()
                loadInterAddmobAds(this@GalleryAct) {
                    startActivityForResult(
                        Intent(this@GalleryAct, StyleAct::class.java).putExtra(
                            "uri", croppedImageUri.toString()
                        ).putExtra("selected_image", selected_image), 12
                    )
                }
            } else {
                ("Reselect Image").tos()
            }
        } else if (resultCode == UCrop.RESULT_ERROR) {
            ("On U crop Cance btn").log()
        } else if (resultCode == RESULT_OK && requestCode == 12) {
            ("Is here").log()
            if (data?.getStringExtra("from") == "edit") {
                (selected_uri).startUcrop(this@GalleryAct)
            } else if (data?.getStringExtra("from") == "back") {
                finish()
            }
        }
    }


// Foreground Service

             <service
                        android:name=".Service.ForegroundServiceManager"
                        android:exported="false" />
                        
            class ForegroundServiceManager : Service() {
                private var mNotificationManager: NotificationManager? = null
            
                override fun onDestroy() {
                    ("onDestroy").log()
                    super.onDestroy()
                }
            
                override fun onBind(p0: Intent?) = null
            
                override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
            //        ("onStart Command").log()
                    createNotificationChannel(this)
                    startForeground(ID_NOTIFI_MANAGER, showNotification(this))
            
                    return START_STICKY
                }
            
                override fun onTaskRemoved(rootIntent: Intent?) {
                    super.onTaskRemoved(rootIntent)
                    ("OnTask Removed").log()
                }
            
                private fun createNotificationChannel(mContext: Context) {
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                        val name: CharSequence = mContext.getString(R.string.app_name)
                        val importance = NotificationManager.IMPORTANCE_HIGH
                        mNotificationManager =
                            mContext.getSystemService(NOTIFICATION_SERVICE) as NotificationManager
                        val mChannel = NotificationChannel(ID_NOTIFI_MANAGER.toString(), name, importance)
                        mNotificationManager!!.createNotificationChannel(mChannel)
                    }
                }
            
                @SuppressLint("RemoteViewLayout")
                fun showNotification(mContext: Context): Notification {
                    val notificationLayout = RemoteViews(
                        mContext.packageName, R.layout.layout_notification_remote
                    )
            
                    notificationLayout.setOnClickPendingIntent(
                        R.id.root,
                        PendingIntent.getActivity(
                            mContext,
                            0,
                            Intent(
                                mContext,
                                SplaceAct::class.java
                            ).addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP or Intent.FLAG_ACTIVITY_SINGLE_TOP),
                            flags
                        )
                    )
            
                    return NotificationCompat.Builder(mContext, ID_NOTIFI_MANAGER.toString()).setOngoing(true)
                        .setCategory(NotificationCompat.CATEGORY_SOCIAL)
                        .setVisibility(NotificationCompat.VISIBILITY_PUBLIC)
                        .setCustomContentView(notificationLayout).setContent(notificationLayout)
                        .setCustomHeadsUpContentView(notificationLayout)
                        .setSound(RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION))
                        .setCustomBigContentView(notificationLayout)
                        .setStyle(NotificationCompat.BigPictureStyle())
                        .setPriority(NotificationCompat.PRIORITY_HIGH).setSmallIcon(R.mipmap.ic_launcher)
                        .build()
                }
            }
            

// Ads Loaded Class

            //Addmob Ads
               implementation 'com.google.android.gms:play-services-ads:22.3.0'
               implementation "androidx.lifecycle:lifecycle-extensions:2.2.0"
               implementation "androidx.lifecycle:lifecycle-runtime-ktx:2.6.1"
           
               //Swipe to Refresh
               implementation "androidx.swiperefreshlayout:swiperefreshlayout:1.2.0-alpha01"
           
               //Shimmer
               implementation 'io.supercharge:shimmerlayout:2.1.0'
               
           
            <uses-permission android:name="com.google.android.gms.permission.AD_ID" />
               <uses-permission android:name="android.permission.INTERNET" />


                   <meta-data
            android:name="com.google.android.gms.ads.AD_MANAGER_APP"
            android:value="true" />
        <meta-data
            android:name="com.google.android.gms.ads.APPLICATION_ID"
            android:value="ca-app-pub-3940256099942544~3347511713" />

           
                       public class AdsManager {
               private interface CallBackAds {
                   void onNext();
               }
           
               public static AdsManager mInstance;
               private InterstitialAd mInterstitialAd;
               public boolean isLoading = false;
           
               public static AdsManager getInstance() {
                   if (mInstance == null) {
                       mInstance = new AdsManager();
                       AdsManager.getInstance().loadInterstitialAd();
                   }
                   return mInstance;
               }
           
               public void loadInterstitialAd() {
                   @SuppressLint("VisibleForTests") AdRequest adRequest = new AdRequest.Builder().build();
                   if (mInterstitialAd == null && !isLoading) {
                       log("load inter req sent");
                       isLoading = true;
                       InterstitialAd.load(Objects.requireNonNull(MyApplication.Companion.getMainInstance()), INTERN_UNIT_ID, adRequest, new InterstitialAdLoadCallback() {
                           @Override
                           public void onAdLoaded(@NonNull InterstitialAd interstitialAd) {
                               log("loaded");
                               mInterstitialAd = interstitialAd;
                               isLoading = false;
                           }
           
                           @Override
                           public void onAdFailedToLoad(@NonNull LoadAdError loadAdError) {
                               log("onAdFailedToLoad");
                               mInterstitialAd = null;
                               isLoading = false;
           //                    loadInterstitialAd();
                           }
                       });
                   } else {
                       log("Internet error");
                   }
               }
           
               public void setAmInternNull() {
                   mInterstitialAd = null;
               }
           
               public boolean showAdmobInterstitialAd(Activity activity, FullScreenContentCallback contentCallback) {
                   try {
                       if (mInterstitialAd != null) {
                           log("is Show Ads");
                           mInterstitialAd.show(activity);
                           mInterstitialAd.setFullScreenContentCallback(contentCallback);
                           return false;
                       } else {
                           contentCallback.onAdDismissedFullScreenContent();
                           log("The interstitial ad wasn't ready yet.");
                           return true;
                       }
                   } catch (Exception ignored) {
                       return true;
                   }
               }
           
               public boolean loadOrShowAdmInterstial(Activity activity, FullScreenContentCallback mAdsCallBack) {
                   return showAdmobInterstitialAd(activity, mAdsCallBack);
               }
           
               @Override
               public int hashCode() {
                   return super.hashCode();
               }
           
               public void _intrestial(Activity activity, CallBackAds callBackAds) {
                   if (loadOrShowAdmInterstial(activity, new FullScreenContentCallback() {
                       @Override
                       public void onAdDismissedFullScreenContent() {
                           super.onAdDismissedFullScreenContent();
                           AdsManager.getInstance().setAmInternNull();
                           AdsManager.getInstance().loadInterstitialAd();
                           log("OnAds Diss");
                           callBackAds.onNext();
                       }
           
                       @Override
                       public void onAdClicked() {
                           super.onAdClicked();
                           log("Intern Ads Click");
                       }
           
                       @Override
                       public void onAdFailedToShowFullScreenContent(@NonNull AdError adError) {
                           super.onAdFailedToShowFullScreenContent(adError);
                           AdsManager.getInstance().setAmInternNull();
                           AdsManager.getInstance().loadInterstitialAd();
                           log("OnAds Failed");
                           callBackAds.onNext();
                       }
                   })) {
                       log("Here Main");
                   }
               }
           }



           ------

                      
           public class AppOpen implements LifecycleObserver, Application.ActivityLifecycleCallbacks {
               private static boolean isShowingAd = false;
               private AppOpenAd appOpenAd = null;
               private AppOpenAd.AppOpenAdLoadCallback loadCallback;
               private final MyApplication myApplication;
               private Activity currentActivity;
               public CallBackOpen mCallBackOpen;
               public OpenadsLoaded mOpenadsLoaded;
           
               public AppOpen(MyApplication myManager) {
                   this.myApplication = myManager;
                   myApplication.registerActivityLifecycleCallbacks(this);
                   ProcessLifecycleOwner.get().getLifecycle().addObserver(this);
               }
           
               public interface CallBackOpen {
                   void callback();
               }
           
               public interface OpenadsLoaded {
                   void callbackloaded();
               }
           
               @OnLifecycleEvent(ON_START)
               public void onStart() {
                   showAdIfAvailable();
               }
           
               public void removeAppOpen() {
                   AppOpen.this.appOpenAd = null;
                   isShowingAd = false;
               }
           
               public void fetchAd() {
                   loadCallback = new AppOpenAd.AppOpenAdLoadCallback() {
                       @Override
                       public void onAdLoaded(AppOpenAd ad) {
                           log("onAdLeoaded | Status: ");
                           AppOpen.this.appOpenAd = ad;
                           try {
                               mOpenadsLoaded.callbackloaded();
                           } catch (Exception ignored) {
           
                           }
                       }
           
                       @Override
                       public void onAdFailedToLoad(LoadAdError loadAdError) {
                           log("onAdFailedToLoad | Status: ");
                           try {
                               mCallBackOpen.callback();
                           } catch (Exception e) {
                               mCallBackOpen.callback();
                               log("Error Fetch Ads: " + e.getMessage());
                           }
                       }
           
                   };
                   AdRequest request = getAdRequest();
           
                   log("o1 Load: ");
           
                   AppOpenAd.load(myApplication, APPOPEN_UNIT_ID, request, AppOpenAd.APP_OPEN_AD_ORIENTATION_PORTRAIT, loadCallback);
               }
           
               @SuppressLint("VisibleForTests")
               private AdRequest getAdRequest() {
                   return new AdRequest.Builder().build();
               }
           
               public boolean isAdAvailable() {
                   return appOpenAd != null;
               }
           
               public void showAdIfAvailable() {
                   if (!isShowingAd && isAdAvailable()) {
                       FullScreenContentCallback fullScreenContentCallback = new FullScreenContentCallback() {
                           @Override
                           public void onAdDismissedFullScreenContent() {
                               try {
                                   mCallBackOpen.callback();
                               } catch (Exception e) {
                                   print(e);
                               }
                               AppOpen.this.appOpenAd = null;
                               isShowingAd = false;
                               fetchAd();
                           }
           
                           @Override
                           public void onAdFailedToShowFullScreenContent(@NonNull AdError adError) {
                               try {
                                   mCallBackOpen.callback();
                               } catch (Exception e) {
                                   print(e);
                               }
                           }
           
                           @Override
                           public void onAdShowedFullScreenContent() {
                               isShowingAd = true;
                           }
                       };
           
                       appOpenAd.setFullScreenContentCallback(fullScreenContentCallback);
                       appOpenAd.show(currentActivity);
           
                   } else {
                       fetchAd();
                   }
               }
           
               @Override
               public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
               }
           
               @Override
               public void onActivityStarted(Activity activity) {
           //        log("onActivityStarted");
                   if (!isShowingAd) currentActivity = activity;
               }
           
               @Override
               public void onActivityResumed(Activity activity) {
           //        log("onActivityResumed");
                   currentActivity = activity;
               }
           
               @Override
               public void onActivityStopped(Activity activity) {
           //        log("onActivityStopped");
               }
           
               @Override
               public void onActivityPaused(Activity activity) {
           //        log("onActivityPaused");
           
               }
           
               @Override
               public void onActivitySaveInstanceState(Activity activity, Bundle bundle) {
               }
           
               @Override
               public void onActivityDestroyed(Activity activity) {
                   currentActivity = null;
               }
           }


       -------------


                             
                      private var active: Boolean = true
                      
                      fun loadAppOpen(activity: Activity, onNext: () -> Unit) {
                          if (IS_ADS_NOT_SHOW) onNext()
                          else {
                              MyApplication.appOpenManager = AppOpen(MyApplication.getMainInstance() as MyApplication?)
                              MyApplication.appOpenManager?.mOpenadsLoaded = AppOpen.OpenadsLoaded {
                                  ("OnStart").log()
                                  if (!activity.isDestroyed && active) {
                                      MyApplication.appOpenManager?.onStart()
                                  }
                              }
                      
                              MyApplication.appOpenManager?.mCallBackOpen = AppOpen.CallBackOpen {
                                  active = false
                                  ("AppOpen.CallBackOpen").log()
                                  if (!activity.isDestroyed) {
                                      onNext()
                                  }
                              }
                          }
                      }
                      
                      
                      fun loadInterAddmobAds(activity: Activity, onNext: () -> Unit) {
                          clickCount++
                      
                          if (IS_ADS_NOT_SHOW) onNext()
                          else if (clickCount % 2 == 0) {
                              AdsManager.getInstance()._intrestial(activity) {
                                  onNext()
                              }
                          } else onNext()
                      }
                      
                      interface RewardAdsCallback {
                          fun onAdsDismissClick()
                          fun onFullShowAds()
                      }
                      
                      fun loadRewardAds(activity: Activity, onRewardCallBack: RewardAdsCallback) {
                          if (IS_ADS_NOT_SHOW) onRewardCallBack.onAdsDismissClick()
                          else RewardAds(activity) {
                              it.log()
                              when (it) {
                                  "onAdsFullShow" -> {
                                      onRewardCallBack.onFullShowAds()
                                  }
                      
                                  "onAddDismiss" -> {
                                      onRewardCallBack.onAdsDismissClick()
                                  }
                      
                                  "onAddFail" -> {
                                      onRewardCallBack.onAdsDismissClick()
                                  }
                              }
                          }.load()
                      }
                      
                      var adsNativesmall: NativeAd? = null
                      var bodysmall: String = ""
                      
                      var adsNativebig: NativeAd? = null
                      var bodybig: String = ""
                      
                      @SuppressLint("VisibleForTests", "InflateParams")
                      fun loadAddmobNativeBigAds(
                          activity: Activity, root: ViewGroup, layout_id: Int
                      ) {
                          if (adsNativebig?.body == bodybig && bodybig.isNotBlank()) {
                              val adView = activity.layoutInflater.inflate(layout_id, null) as NativeAdView
                              populateNativeBigAdView(adsNativebig!!, adView)
                              root.removeAllViews()
                              root.addView(adView)
                          }
                      
                          val builder = AdLoader.Builder(
                              activity, NATIVE_UNIT_ID
                          ).forNativeAd { nativeAd ->
                              adsNativebig = nativeAd
                              bodybig = adsNativebig!!.body ?: ""
                              if (root.childCount == 1) {
                                  val adView = activity.layoutInflater.inflate(layout_id, null) as NativeAdView
                                  populateNativeBigAdView(nativeAd, adView)
                                  root.removeAllViews()
                                  root.addView(adView)
                              }
                          }
                          val adLoader = builder.withAdListener(object : AdListener() {
                              override fun onAdFailedToLoad(loadAdError: LoadAdError) {
                                  ("Ad Big Native Failed").log()
                                  root.removeAllViews()
                              }
                      
                              override fun onAdClicked() {
                                  super.onAdClicked()
                                  ("Big Native Ads Click").log()
                              }
                      
                          }).build()
                          adLoader.loadAd(com.google.android.gms.ads.AdRequest.Builder().build())
                      }
                      
                      fun populateNativeBigAdView(nativeAd: NativeAd, adView: NativeAdView) {
                          adView.mediaView = adView.findViewById<View>(R.id.ad_media) as MediaView
                          adView.headlineView = adView.findViewById(R.id.ad_headline)
                          adView.bodyView = adView.findViewById(R.id.ad_body)
                          adView.callToActionView = adView.findViewById(R.id.ad_call_to_action)
                          adView.iconView = adView.findViewById(R.id.ad_app_icon)
                          adView.priceView = adView.findViewById(R.id.ad_price)
                          adView.starRatingView = adView.findViewById(R.id.ad_stars)
                          adView.storeView = adView.findViewById(R.id.ad_store)
                          adView.advertiserView = adView.findViewById(R.id.ad_advertiser)
                          (adView.headlineView as TextView).text = nativeAd.headline
                          nativeAd.mediaContent?.let { adView.mediaView?.setMediaContent(it) }
                      
                          if (nativeAd.body == null) {
                              adView.bodyView!!.visibility = View.INVISIBLE
                          } else {
                              adView.bodyView!!.visibility = View.VISIBLE
                              (adView.bodyView as TextView).text = nativeAd.body
                          }
                          if (nativeAd.callToAction == null) {
                              adView.callToActionView!!.visibility = View.INVISIBLE
                          } else {
                              adView.callToActionView!!.visibility = View.VISIBLE
                              (adView.callToActionView as Button).text = "OPEN"
                          }
                          if (nativeAd.icon == null) {
                              adView.iconView!!.visibility = View.GONE
                          } else {
                              (adView.iconView as ImageView).setImageDrawable(nativeAd.icon!!.drawable)
                              adView.iconView!!.visibility = View.VISIBLE
                          }
                          if (nativeAd.price == null) {
                              adView.priceView!!.visibility = View.INVISIBLE
                          } else {
                              adView.priceView!!.visibility = View.VISIBLE
                              (adView.priceView as TextView).text = nativeAd.price
                          }
                          if (nativeAd.store == null) {
                              adView.storeView!!.visibility = View.INVISIBLE
                          } else {
                              adView.storeView!!.visibility = View.VISIBLE
                              (adView.storeView as TextView).text = nativeAd.store
                          }
                          if (nativeAd.starRating == null) {
                              adView.starRatingView!!.visibility = View.INVISIBLE
                          } else {
                              (adView.starRatingView as RatingBar).rating = nativeAd.starRating!!.toFloat()
                              adView.starRatingView!!.visibility = View.VISIBLE
                          }
                          if (nativeAd.advertiser == null) {
                              adView.advertiserView!!.visibility = View.INVISIBLE
                          } else {
                              (adView.advertiserView as TextView).text = nativeAd.advertiser
                              adView.advertiserView!!.visibility = View.VISIBLE
                          }
                          adView.setNativeAd(nativeAd)
                          nativeAd.mediaContent?.videoController
                      }
                      
                      @SuppressLint("VisibleForTests", "InflateParams")
                      fun loadNativeAdmobSmallAds(activity: Activity, root: ViewGroup, ad_id: Int) {
                          if (adsNativesmall?.body == bodysmall && bodysmall.isNotBlank()) {
                              val adView = activity.layoutInflater.inflate(ad_id, null) as NativeAdView
                              populateNativeAdViewsmall(adsNativesmall!!, adView)
                              ("Remove Views").log()
                              root.removeAllViews()
                              root.addView(adView)
                          }
                      
                          val builder = AdLoader.Builder(
                              activity, NATIVE_UNIT_ID
                          ).forNativeAd { nativeAd ->
                              adsNativesmall = nativeAd
                              bodysmall = adsNativesmall!!.body ?: ""
                      
                              if (root.childCount == 1) {
                                  val adView = activity.layoutInflater.inflate(
                                      R.layout.ad_native_small, null
                                  ) as NativeAdView
                                  populateNativeAdViewsmall(nativeAd, adView)
                                  root.removeAllViews()
                                  ("Remove Views 1111").log()
                                  root.addView(adView)
                              }
                          }
                          val adLoader = builder.withAdListener(object : AdListener() {
                              override fun onAdFailedToLoad(loadAdError: LoadAdError) {
                      //            loadNativeAdmobSmallAds(activity, root, ad_id)
                                  root.removeAllViews()
                              }
                      
                              override fun onAdClicked() {
                                  super.onAdClicked()
                                  ("Small Native Ads Click").log()
                              }
                      
                          }).build()
                          adLoader.loadAd(AdRequest.Builder().build())
                      }
                      
                      fun populateNativeAdViewsmall(nativeAd: NativeAd, adView: NativeAdView) {
                          adView.headlineView = adView.findViewById(R.id.ad_headline)
                          adView.bodyView = adView.findViewById(R.id.ad_body)
                          adView.callToActionView = adView.findViewById(R.id.ad_call_to_action)
                          adView.iconView = adView.findViewById(R.id.ad_app_icon)
                          adView.priceView = adView.findViewById(R.id.ad_price)
                          adView.starRatingView = adView.findViewById(R.id.ad_stars)
                          adView.storeView = adView.findViewById(R.id.ad_store)
                          adView.advertiserView = adView.findViewById(R.id.ad_advertiser)
                          (adView.headlineView as TextView).text = nativeAd.headline
                          if (nativeAd.body == null) {
                              adView.bodyView!!.visibility = View.INVISIBLE
                          } else {
                              adView.bodyView!!.visibility = View.VISIBLE
                              (adView.bodyView as TextView).text = nativeAd.body
                          }
                          if (nativeAd.callToAction == null) {
                              adView.callToActionView!!.visibility = View.INVISIBLE
                          } else {
                              adView.callToActionView!!.visibility = View.VISIBLE
                              (adView.callToActionView as Button).text = "OPEN"
                          }
                          if (nativeAd.icon == null) {
                              adView.iconView!!.visibility = View.GONE
                          } else {
                              (adView.iconView as ImageView).setImageDrawable(nativeAd.icon!!.drawable)
                              adView.iconView!!.visibility = View.VISIBLE
                          }
                          if (nativeAd.price == null) {
                              adView.priceView!!.visibility = View.INVISIBLE
                          } else {
                              adView.priceView!!.visibility = View.VISIBLE
                              (adView.priceView as TextView).text = nativeAd.price
                          }
                          if (nativeAd.store == null) {
                              adView.storeView!!.visibility = View.INVISIBLE
                          } else {
                              adView.storeView!!.visibility = View.VISIBLE
                              (adView.storeView as TextView).text = nativeAd.store
                          }
                          if (nativeAd.starRating == null) {
                              adView.starRatingView!!.visibility = View.INVISIBLE
                          } else {
                              (adView.starRatingView as RatingBar).rating = nativeAd.starRating!!.toFloat()
                              adView.starRatingView!!.visibility = View.VISIBLE
                          }
                          if (nativeAd.advertiser == null) {
                              adView.advertiserView!!.visibility = View.INVISIBLE
                          } else {
                              (adView.advertiserView as TextView).text = nativeAd.advertiser
                              adView.advertiserView!!.visibility = View.VISIBLE
                          }
                      
                          adView.setNativeAd(nativeAd)
                      }
                      
                      
                      fun loadBannerAds(ctx: Context, v: ViewGroup) {
                          val adView = AdManagerAdView(ctx)
                      
                          val builder: AdRequest.Builder = AdRequest.Builder()
                          val extras = Bundle()
                      
                          adView.setAdSizes(AdSize.BANNER)
                          adView.adUnitId = BANNER_UNIT_ID
                          adView.layoutParams = ViewGroup.LayoutParams(
                              ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT
                          )
                          extras.putString("collapsible", "bottom")
                          builder.addNetworkExtrasBundle(AdMobAdapter::class.java, extras)
                          adView.loadAd(builder.build())
                      
                          adView.adListener = object : AdListener() {
                              override fun onAdFailedToLoad(adError: LoadAdError) {
                                  ("Reload Banner Ads").log()
                      //            loadBannerAds(ctx, v)
                                  v.removeAllViews()
                              }
                      
                              override fun onAdLoaded() {
                                  super.onAdLoaded()
                                  v.removeAllViews()
                                  v.addView(adView)
                                  ("AD Loaded Banner").log()
                              }
                          }
                      }


           --------------


           
           
           class RewardAds(var activity: Activity, var onAdsClick: (String) -> Unit) {
               private val AD_UNIT_ID = REWARD_ADS
               private var isLoading: Boolean = false
               private var rewardedAds: RewardedAd? = null
               var adRequest: AdRequest = AdRequest.Builder().build()
               var progressBar: RewardAdsDialog = RewardAdsDialog(activity)
           
               fun load() {
                   if (rewardedAds == null) {
                       progressBar.show()
                       isLoading = true
           
                       RewardedAd.load(activity, AD_UNIT_ID, adRequest, object : RewardedAdLoadCallback() {
                           override fun onAdFailedToLoad(loadAdError: LoadAdError) {
                               // Handle the error.
                               (("Load Reward Ads: " + loadAdError.message)).log()
                               rewardedAds = null
                               isLoading = false
                               progressBar.onDismiss()
                               onAdsClick("onAddDismiss")
           //                    load()
                           }
           
                           override fun onAdLoaded(rewardedAd: RewardedAd) {
                               rewardedAds = rewardedAd
                               ("onAdLoaded").log()
                               isLoading = false
                               show()
                           }
                       })
                   }
               }
           
               fun show() {
                   if (rewardedAds == null) return
           
                   if (!isLoading) {
                       progressBar.onDismiss()
                       rewardedAds?.fullScreenContentCallback = object : FullScreenContentCallback() {
                           override fun onAdClicked() {
                               ("Ad was clicked.").log()
                               onAdsClick("onAdsClick")
                           }
           
                           override fun onAdDismissedFullScreenContent() {
                               ("Ad dismissed fullscreen content.").log()
                               onAdsClick("onAddDismiss")
                               rewardedAds = null
                           }
           
                           override fun onAdFailedToShowFullScreenContent(adError: AdError) {
                               // Called when ad fails to show.
                               ("Ad failed to show fullscreen content.").log()
                               onAdsClick("onAddFail")
                               rewardedAds = null
                           }
           
                           override fun onAdImpression() {
                               ("Ad recorded an impression.").log()
                           }
           
                           override fun onAdShowedFullScreenContent() {
                               ("Ad showed fullscreen content.").log()
                           }
                       }
           
                       rewardedAds?.show(activity) {
                           fun onUserEarnedReward(rewardItem: RewardItem) {
                               val rewardAmount = rewardItem.amount
                               val rewardType = rewardItem.type
                               ("User earned the reward. $rewardAmount  | $rewardType").log()
                           }
                           onAdsClick("onAdsFullShow")
                           ("User earned onComplete the reward").log()
                       }
                   }
               }
           }


     ----------------------

      BIG NATIVE ADS
           
                 <com.google.android.gms.ads.nativead.NativeAdView xmlns:android="http://schemas.android.com/apk/res/android"
               xmlns:app="http://schemas.android.com/apk/res-auto"
               android:layout_width="match_parent"
               android:layout_height="match_parent"
               android:background="#1F1F1F"
               android:clipChildren="false"
               android:clipToPadding="false">
           
               <androidx.cardview.widget.CardView
                   android:layout_width="match_parent"
                   android:layout_height="wrap_content"
                   app:cardCornerRadius="6dp"
                   app:cardUseCompatPadding="false">
           
                   <LinearLayout
                       android:layout_width="match_parent"
                       android:layout_height="wrap_content"
                       android:background="@drawable/gd_home"
                       android:backgroundTint="#1F1F1F"
                       android:orientation="vertical">
           
                       <RelativeLayout
                           android:id="@+id/layout_img"
                           android:layout_width="match_parent"
                           android:layout_height="wrap_content">
           
                           <LinearLayout
                               android:id="@+id/adtop"
                               android:layout_width="match_parent"
                               android:layout_height="wrap_content"
                               android:layout_marginStart="@dimen/_10sdp"
                               android:layout_marginTop="@dimen/_5sdp"
                               android:orientation="horizontal"
                               android:padding="4dp">
           
                               <androidx.constraintlayout.utils.widget.ImageFilterView
                                   android:id="@+id/ad_app_icon"
                                   android:layout_width="45dp"
                                   android:layout_height="45dp"
                                   android:adjustViewBounds="true"
                                   android:scaleType="fitCenter"
                                   android:src="#132747"
                                   app:round="@dimen/_5sdp" />
           
                               <LinearLayout
                                   android:layout_width="match_parent"
                                   android:layout_height="match_parent"
                                   android:layout_gravity="center"
                                   android:gravity="center"
                                   android:orientation="vertical"
                                   android:paddingLeft="5dp">
           
                                   <androidx.appcompat.widget.AppCompatTextView
                                       android:id="@+id/ad_headline"
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content"
                                       android:ellipsize="end"
                                       android:maxLines="2"
                                       android:textColor="#3AE5A3"
                                       android:textSize="14sp"
                                       android:textStyle="bold" />
           
                                   <androidx.appcompat.widget.AppCompatTextView
                                       android:id="@+id/ad_body"
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content"
                                       android:layout_gravity="center"
                                       android:ellipsize="end"
                                       android:gravity="start"
                                       android:maxLines="1"
                                       android:textColor="#08C3F4"
                                       android:textSize="10sp"
                                       android:visibility="visible" />
           
                                   <LinearLayout
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content"
                                       android:visibility="gone">
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_advertiser"
                                           android:layout_width="wrap_content"
                                           android:layout_height="match_parent"
                                           android:ellipsize="end"
                                           android:gravity="bottom"
                                           android:maxLines="1"
                                           android:text=""
                                           android:textColor="@color/black"
                                           android:textSize="13sp"
                                           android:textStyle="bold" />
           
           
                                       <RatingBar
                                           android:id="@+id/ad_stars"
                                           style="?android:attr/ratingBarStyleSmall"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:isIndicator="true"
                                           android:numStars="5"
                                           android:stepSize="0.5" />
                                   </LinearLayout>
                               </LinearLayout>
                           </LinearLayout>
           
           
                           <androidx.appcompat.widget.AppCompatTextView
                               android:layout_width="wrap_content"
                               android:layout_height="wrap_content"
                               android:layout_alignParentTop="true"
                               android:layout_gravity="bottom|left|center_vertical|center_horizontal|center"
                               android:background="@drawable/ad_tv"
                               android:elevation="@dimen/_2sdp"
                               android:fontFamily="@font/mulishextrabold"
                               android:padding="@dimen/_2sdp"
                               android:text=" Ad "
                               android:textColor="@color/white"
                               android:textSize="@dimen/_8sdp"
                               android:textStyle="bold"
                               android:visibility="visible" />
           
                           <androidx.appcompat.widget.AppCompatImageView
                               android:id="@+id/ad_image_bg"
                               android:layout_width="match_parent"
                               android:layout_height="@dimen/_125sdp"
                               android:layout_below="@+id/adtop"
                               android:layout_centerInParent="true"
                               android:layout_marginLeft="1dp"
                               android:layout_marginTop="1dp"
                               android:layout_marginRight="1dp"
                               android:scaleType="centerCrop" />
           
                           <androidx.appcompat.widget.AppCompatImageView
                               android:id="@+id/ad_image"
                               android:layout_width="match_parent"
                               android:layout_height="@dimen/_125sdp"
                               android:layout_below="@+id/adtop"
                               android:layout_centerInParent="true"
                               android:layout_marginLeft="1dp"
                               android:layout_marginTop="1dp"
                               android:layout_marginRight="1dp"
                               android:adjustViewBounds="true"
                               android:visibility="gone" />
           
                           <com.google.android.gms.ads.nativead.MediaView
                               android:id="@+id/ad_media"
                               android:layout_width="match_parent"
                               android:layout_height="@dimen/_125sdp"
                               android:layout_below="@+id/adtop"
                               android:layout_centerInParent="true"
                               android:layout_marginStart="@dimen/_10sdp"
                               android:layout_marginTop="@dimen/_10sdp"
                               android:layout_marginEnd="@dimen/_10sdp"
                               android:layout_marginBottom="@dimen/_10sdp"
                               android:visibility="visible" />
                       </RelativeLayout>
           
                       <RelativeLayout
                           android:layout_width="match_parent"
                           android:layout_height="wrap_content"
                           android:orientation="vertical">
           
                           <LinearLayout
                               android:layout_width="match_parent"
                               android:layout_height="wrap_content"
                               android:orientation="vertical">
           
                               <LinearLayout
                                   android:layout_width="match_parent"
                                   android:layout_height="0dp"
                                   android:layout_weight="0.7"
                                   android:orientation="vertical">
           
                                   <LinearLayout
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content"
                                       android:layout_gravity="center"
                                       android:orientation="vertical"
                                       android:paddingTop="5dp"
                                       android:visibility="gone">
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_price"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:paddingStart="5dp"
                                           android:paddingLeft="5dp"
                                           android:paddingEnd="5dp"
                                           android:paddingRight="5dp"
                                           android:text=""
                                           android:textColor="@color/white"
                                           android:textSize="12sp"
                                           android:visibility="gone" />
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_store"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:paddingStart="5dp"
                                           android:paddingLeft="5dp"
                                           android:paddingEnd="5dp"
                                           android:paddingRight="5dp"
                                           android:text=""
                                           android:textColor="@color/white"
                                           android:textSize="12sp"
                                           android:visibility="gone" />
                                   </LinearLayout>
                               </LinearLayout>
           
           
                               <androidx.appcompat.widget.AppCompatButton
                                   android:id="@+id/ad_call_to_action"
                                   android:layout_width="match_parent"
                                   android:layout_height="@dimen/_35sdp"
                                   android:layout_gravity="center_vertical"
                                   android:layout_margin="@dimen/_8sdp"
                                   android:layout_marginStart="@dimen/_8sdp"
                                   android:layout_marginTop="@dimen/_8sdp"
                                   android:layout_marginEnd="@dimen/_8sdp"
                                   android:layout_marginBottom="@dimen/_8sdp"
                                   android:background="@drawable/tv_round_shap"
                                   android:elevation="@dimen/_2sdp"
                                   android:gravity="center"
                                   android:textAllCaps="true"
                                   android:textColor="@color/white"
                                   android:textSize="@dimen/_16sdp" />
                           </LinearLayout>
           
                       </RelativeLayout>
           
                   </LinearLayout>
               </androidx.cardview.widget.CardView>
           </com.google.android.gms.ads.nativead.NativeAdView>

     ----------- SMALL -*--------
           
                <com.google.android.gms.ads.nativead.NativeAdView xmlns:android="http://schemas.android.com/apk/res/android"
               android:layout_width="match_parent"
               android:layout_height="match_parent"
               android:background="#1F1F1F"
               android:clipChildren="false"
               android:clipToPadding="false">
           
               <androidx.cardview.widget.CardView xmlns:app="http://schemas.android.com/apk/res-auto"
                   android:layout_width="match_parent"
                   android:layout_height="wrap_content"
                   app:cardElevation="@dimen/_2sdp">
           
                   <LinearLayout
                       android:layout_width="match_parent"
                       android:layout_height="wrap_content"
                       android:background="@drawable/gd_home"
                       android:backgroundTint="#1F1F1F"
                       android:orientation="vertical">
           
                       <RelativeLayout
                           android:layout_width="match_parent"
                           android:layout_height="wrap_content"
                           android:orientation="vertical">
           
                           <LinearLayout
                               android:layout_width="match_parent"
                               android:layout_height="wrap_content"
                               android:orientation="vertical">
           
                               <LinearLayout
                                   android:layout_width="match_parent"
                                   android:layout_height="wrap_content"
                                   android:layout_weight="0.7"
                                   android:orientation="vertical">
           
                                   <LinearLayout
                                       android:layout_width="match_parent"
                                       android:layout_height="wrap_content"
                                       android:layout_gravity="center"
                                       android:orientation="vertical"
                                       android:paddingTop="5dp"
                                       android:visibility="gone">
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_price"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:paddingStart="5dp"
                                           android:paddingLeft="5dp"
                                           android:paddingEnd="5dp"
                                           android:paddingRight="5dp"
                                           android:text=""
                                           android:textColor="@color/white"
                                           android:textSize="12sp"
                                           android:visibility="gone" />
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_store"
                                           android:layout_width="wrap_content"
                                           android:layout_height="wrap_content"
                                           android:paddingStart="5dp"
                                           android:paddingLeft="5dp"
                                           android:paddingEnd="5dp"
                                           android:paddingRight="5dp"
                                           android:text=""
                                           android:textColor="@color/white"
                                           android:textSize="12sp"
                                           android:visibility="gone" />
                                   </LinearLayout>
                               </LinearLayout>
           
                               <LinearLayout
                                   android:layout_width="match_parent"
                                   android:layout_height="wrap_content"
                                   android:layout_marginStart="@dimen/_10sdp"
                                   android:layout_marginTop="@dimen/_15sdp"
                                   android:layout_marginBottom="@dimen/_30sdp"
                                   android:orientation="horizontal"
                                   android:padding="4dp">
           
                                   <androidx.constraintlayout.utils.widget.ImageFilterView
                                       android:id="@+id/ad_app_icon"
                                       android:layout_width="45dp"
                                       android:layout_height="45dp"
                                       android:adjustViewBounds="true"
                                       android:scaleType="fitCenter"
                                       android:src="#132747"
                                       app:round="@dimen/_5sdp" />
           
                                   <LinearLayout
                                       android:layout_width="match_parent"
                                       android:layout_height="match_parent"
                                       android:layout_gravity="center"
                                       android:gravity="center"
                                       android:orientation="vertical"
                                       android:paddingLeft="5dp">
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_headline"
                                           android:layout_width="match_parent"
                                           android:layout_height="wrap_content"
                                           android:ellipsize="end"
                                           android:maxLines="2"
                                           android:textColor="@color/white"
                                           android:textSize="14sp"
                                           android:textStyle="bold" />
           
                                       <androidx.appcompat.widget.AppCompatTextView
                                           android:id="@+id/ad_body"
                                           android:layout_width="match_parent"
                                           android:layout_height="wrap_content"
                                           android:layout_gravity="center"
                                           android:ellipsize="end"
                                           android:gravity="start"
                                           android:maxLines="1"
                                           android:textColor="#7A7A7A"
                                           android:textSize="10sp"
                                           android:visibility="visible" />
           
                                       <LinearLayout
                                           android:layout_width="match_parent"
                                           android:layout_height="wrap_content"
                                           android:visibility="gone">
           
                                           <androidx.appcompat.widget.AppCompatTextView
                                               android:id="@+id/ad_advertiser"
                                               android:layout_width="wrap_content"
                                               android:layout_height="match_parent"
                                               android:ellipsize="end"
                                               android:gravity="bottom"
                                               android:maxLines="1"
                                               android:text=""
                                               android:textColor="@color/black"
                                               android:textSize="13sp"
                                               android:textStyle="bold" />
           
           
                                           <RatingBar
                                               android:id="@+id/ad_stars"
                                               style="?android:attr/ratingBarStyleSmall"
                                               android:layout_width="wrap_content"
                                               android:layout_height="wrap_content"
                                               android:isIndicator="true"
                                               android:numStars="5"
                                               android:stepSize="0.5" />
                                       </LinearLayout>
                                   </LinearLayout>
                               </LinearLayout>
           
                               <androidx.appcompat.widget.AppCompatButton
                                   android:id="@+id/ad_call_to_action"
                                   android:layout_width="match_parent"
                                   android:layout_height="@dimen/_40sdp"
                                   android:layout_gravity="center_vertical"
                                   android:layout_margin="@dimen/_8sdp"
                                   android:background="@drawable/tv_round_shap"
                                   android:elevation="@dimen/_2sdp"
                                   android:gravity="center"
                                   android:textAllCaps="true"
                                   android:textColor="@color/white"
                                   android:textSize="@dimen/_16sdp" />
                           </LinearLayout>
           
                           <androidx.appcompat.widget.AppCompatTextView
                               android:layout_width="wrap_content"
                               android:layout_height="wrap_content"
                               android:layout_alignParentTop="true"
                               android:layout_gravity="bottom|left|center_vertical|center_horizontal|center"
                               android:background="@drawable/ad_tv"
                               android:elevation="@dimen/_2sdp"
                               android:fontFamily="@font/mulishextrabold"
                               android:padding="@dimen/_2sdp"
                               android:text=" Ad "
                               android:textColor="@color/white"
                               android:textSize="@dimen/_8sdp"
                               android:textStyle="bold"
                               android:visibility="visible" />
                       </RelativeLayout>
           
                   </LinearLayout>
               </androidx.cardview.widget.CardView>
           </com.google.android.gms.ads.nativead.NativeAdView>

           
// Reverse Timer CountDown

           var totalTimeInMillis = (30 * 60000).toLong()

        timerObj = object : CountDownTimer(totalTimeInMillis, 1000) {
            override fun onTick(millisUntilFinished: Long) {
                runningMilisec = millisUntilFinished
                val hours = millisUntilFinished / (60 * 60 * 1000)
                val minutesRemaining = (millisUntilFinished / 1000).toInt() / 60
                val secondsRemaining = (millisUntilFinished / 1000).toInt() % 60
                val countdownText =
                    String.format("%02d:%02d:%02d", hours, minutesRemaining, secondsRemaining)
                ("Apk Class Timer: $countdownText").log()
                timerTv = countdownText
            }

            override fun onFinish() {
                exc {
                    timerTv = "00:00:00"
                    isRunningVpnGloble = false
                    ("On Finish in Timer Counter").log()
                    cancel()
                    MessageUtil.sendMsg(
                        applicationContext,
                        AppConfig.BROADCAST_ACTION_SERVICE,
                        AppConfig.MSG_STATE_STOP,
                        ""
                    )
                }
            }
        }

// DrawerLayout & NavigationView

           <androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
               xmlns:tools="http://schemas.android.com/tools"
               android:layout_width="wrap_content"
               android:layout_height="match_parent"
               android:layout_gravity="start|top"
               android:backgroundTint="#10161F"
               tools:context=".Activity.MainActivity">
           
               <include
                   android:id="@+id/includeID"
                   layout="@layout/main_act_layout"
                   android:layout_width="match_parent"
                   android:layout_height="match_parent" />
           
               <com.google.android.material.navigation.NavigationView
                   android:id="@+id/navview"
                   android:layout_width="wrap_content"
                   android:layout_height="match_parent"
                   android:layout_gravity="start"
                   android:backgroundTint="#10161F"
                   android:fitsSystemWindows="true" />
           
           </androidx.drawerlayout.widget.DrawerLayout>


           
             val navHeaderMainBinding: NavheaderLayoutBinding = NavheaderLayoutBinding.inflate(
                       layoutInflater
                   )
                   bind.navview.addHeaderView(navHeaderMainBinding.root)
           

// comparas bitmap size 512

           Glide.with(this).asBitmap().diskCacheStrategy(DiskCacheStrategy.NONE).load(imageUrl)
                    .into(object : CustomTarget<Bitmap?>() {
                        override fun onResourceReady(
                            resource: Bitmap,
                            transition: com.bumptech.glide.request.transition.Transition<in Bitmap?>?
                        ) {
                            onBackground {
        
                                val desiredWidth = 512 // Adjust desired dimensions as needed
        
                                val desiredHeight = 512
        
                                val resizedBitmap = Bitmap.createScaledBitmap(
                                    resource, desiredWidth, desiredHeight, false
                                )
        
                                val myDir = File(cacheDir.toURI())
                                if (!myDir.exists()) myDir.mkdirs()
                                val file = File(myDir, "croped_kb.jpeg")
                                if (file.exists()) file.delete()
                                try {
                                    val out = FileOutputStream(file)
                                    resizedBitmap.compress(
                                        Bitmap.CompressFormat.JPEG, 30, out
                                    )
                                    out.flush()
                                    out.close()
                                } catch (e: Exception) {
                                    (e.message!!).log()
                                }
        
                                ("Size: " + getFileSize(file.length()) + " | W: ${resizedBitmap.width} | H: ${resizedBitmap.height}").log()
        
                                ("Bitmap Genrated: ${file.path}").log()
        
                                val requestBody =
                                    RequestBody.create("multipart/form-data".toMediaTypeOrNull(), file)
                                val filePart: MultipartBody.Part =
                                    MultipartBody.Part.createFormData("image", file.name, requestBody)
                                val promypart =
                                    RequestBody.create("text/plain".toMediaTypeOrNull(), promtext)
        
                                try {
                                    ApiCaller().makeApiCall(filePart, promypart, object : onResponce {
                                        override fun <T> onSuccess(res: T) {
                                            resbody = res as ResModel
                                            DataHolder.largeData = resbody.data
                                            runOnUiThread {
                                                if (!isDestroyed) {
                                                    bind.miraclloading.gon()
                                                    bind.doneconstraint.visible()
                                                }
                                            }
                                        }
        
                                        override fun onError(error: String) {
                                            ("onError: ").log()
                                        }
                                    })
        
                                } catch (e: Exception) {
                                    e.printStackTrace()
                                    "API call crashed with exception: ${e.message}".log()
                                }
                            }
                        }
        
                        override fun onLoadCleared(@Nullable placeholder: Drawable?) {}
                    })

// Shimmer Layout

        
         //Shimmer
            implementation 'io.supercharge:shimmerlayout:2.1.0'
            
    <androidx.appcompat.widget.LinearLayoutCompat
        android:id="@+id/bannerads"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="vertical"
        app:layout_constraintBottom_toBottomOf="parent">

        <include layout="@layout/shimmer_layout" />

    </androidx.appcompat.widget.LinearLayoutCompat>


    ------

    <io.supercharge.shimmerlayout.ShimmerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/shimmer"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@color/bg_color"
    app:shimmer_animation_duration="800"
    app:shimmer_auto_start="true"
    android:layout_gravity="bottom"
    app:shimmer_color="@color/white"
    tools:ignore="MissingDefaultResource">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#D3D3D3"
        android:backgroundTint="#1F1F1F"
        android:orientation="vertical">

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_weight="0.7"
                    android:orientation="vertical">

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center"
                        android:orientation="vertical"
                        android:paddingTop="5dp"
                        android:visibility="gone">

                        <androidx.appcompat.widget.AppCompatTextView
                            android:id="@+id/ad_price"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:paddingStart="5dp"
                            android:paddingLeft="5dp"
                            android:paddingEnd="5dp"
                            android:paddingRight="5dp"
                            android:text=""
                            android:textColor="@color/white"
                            android:textSize="12sp"
                            android:visibility="gone" />

                        <androidx.appcompat.widget.AppCompatTextView
                            android:id="@+id/ad_store"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:paddingStart="5dp"
                            android:paddingLeft="5dp"
                            android:paddingEnd="5dp"
                            android:paddingRight="5dp"
                            android:text=""
                            android:textColor="@color/white"
                            android:textSize="12sp"
                            android:visibility="gone" />
                    </LinearLayout>
                </LinearLayout>

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginStart="@dimen/_10sdp"
                    android:layout_marginTop="@dimen/_15sdp"
                    android:layout_marginBottom="@dimen/_30sdp"
                    android:orientation="horizontal"
                    android:padding="4dp">

                    <androidx.constraintlayout.utils.widget.ImageFilterView
                        android:id="@+id/ad_app_icon"
                        android:layout_width="45dp"
                        android:layout_height="45dp"
                        android:adjustViewBounds="true"
                        android:scaleType="fitCenter"
                        android:src="#D3D3D3"
                        app:round="@dimen/_5sdp" />

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:layout_gravity="center"
                        android:gravity="center"
                        android:orientation="vertical"
                        android:paddingLeft="5dp">

                        <androidx.appcompat.widget.AppCompatTextView
                            android:id="@+id/ad_headline"
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:background="#D3D3D3"
                            android:ellipsize="end"
                            android:maxLines="2"
                            android:textColor="@color/white"
                            android:textSize="14sp"
                            android:textStyle="bold" />

                        <androidx.appcompat.widget.AppCompatTextView
                            android:id="@+id/ad_body"
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:layout_gravity="center"
                            android:layout_marginTop="@dimen/_5sdp"
                            android:background="#D3D3D3"
                            android:ellipsize="end"
                            android:gravity="start"
                            android:maxLines="1"
                            android:textColor="#7A7A7A"
                            android:textSize="10sp"
                            android:visibility="visible" />

                        <LinearLayout
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:visibility="gone">

                            <androidx.appcompat.widget.AppCompatTextView
                                android:id="@+id/ad_advertiser"
                                android:layout_width="wrap_content"
                                android:layout_height="match_parent"
                                android:ellipsize="end"
                                android:gravity="bottom"
                                android:maxLines="1"
                                android:text=""
                                android:textColor="@color/black"
                                android:textSize="13sp"
                                android:textStyle="bold" />


                            <RatingBar
                                android:id="@+id/ad_stars"
                                style="?android:attr/ratingBarStyleSmall"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:isIndicator="true"
                                android:numStars="5"
                                android:stepSize="0.5" />
                        </LinearLayout>
                    </LinearLayout>
                </LinearLayout>

                <androidx.appcompat.widget.AppCompatButton
                    android:id="@+id/ad_call_to_action"
                    android:layout_width="match_parent"
                    android:layout_height="@dimen/_30sdp"
                    android:layout_gravity="center_vertical"
                    android:layout_margin="@dimen/_15sdp"
                    android:background="@drawable/tv_round_shap"
                    android:backgroundTint="#D3D3D3"
                    android:elevation="@dimen/_2sdp"
                    android:gravity="center"
                    android:textAllCaps="true"
                    android:textColor="@color/white"
                    android:textSize="@dimen/_16sdp" />
            </LinearLayout>

            <androidx.appcompat.widget.AppCompatTextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_alignParentTop="true"
                android:layout_gravity="bottom|left|center_vertical|center_horizontal|center"
                android:background="@drawable/ad_tv"
                android:backgroundTint="#D3D3D3"
                android:elevation="@dimen/_2sdp"
                android:fontFamily="@font/mulishextrabold"
                android:padding="@dimen/_2sdp"
                android:text=" Ad "
                android:textColor="@color/white"
                android:textSize="@dimen/_8sdp"
                android:textStyle="bold"
                android:visibility="visible" />
        </RelativeLayout>

    </LinearLayout>

        </io.supercharge.shimmerlayout.ShimmerLayout>
         


// Keyur bhai bottomsheeet


        class CustomBottomSheetFragment : BottomSheetDialogFragment() {
            lateinit var binding: BottomDialogBinding
            private var shouldInterceptTouch = false
            private var deviceHeight = 0f
        
            @SuppressLint("ClickableViewAccessibility")
            override fun onStart() {
                super.onStart()
                val behavior: BottomSheetBehavior<*> =
                    BottomSheetBehavior.from(requireView().parent as View)
        
                behavior.isDraggable = false;
                behavior.state = BottomSheetBehavior.STATE_EXPANDED
                var peekHeight = (resources.displayMetrics.heightPixels / 2)
                var peekHeightFull = (resources.displayMetrics.heightPixels)
        
                ("Total Height: $peekHeightFull | ${peekHeight}").log()
        
                dialog!!.window!!.setLayout(ViewGroup.LayoutParams.MATCH_PARENT, peekHeight);
                dialog!!.window!!.setGravity(Gravity.BOTTOM)
        
        
                var initialY = peekHeightFull.toFloat()
                val layoutParams = dialog!!.window!!.attributes
        
                binding.card.setOnTouchListener(object : OnTouchListener {
                    override fun onTouch(v: View?, event: MotionEvent): Boolean {
                        when (event.action) {
                            MotionEvent.ACTION_DOWN -> {
                                initialY = event.rawY
                                return true
                            }
        
                            MotionEvent.ACTION_MOVE -> {
                                val deltaY = initialY - event.rawY
        //                        ("Y: $deltaY").log()
        
                                if (deltaY > 0 && layoutParams.height <= (peekHeightFull - 60)) {
                                    // Scrolling up
                                    if (deltaY > 5) {
                                        layoutParams.height = (layoutParams.height + deltaY).toInt()
                                        dialog!!.window!!.attributes = layoutParams
                                    }
                                } else if (deltaY < 0 && layoutParams.height >= peekHeight) {
                                    // Scrolling down
                                    if (-deltaY > 5) {
                                        layoutParams.height = (layoutParams.height + deltaY).toInt()
                                        dialog!!.window!!.attributes = layoutParams
                                    } else if (-deltaY > deviceHeight / 2) {
                                        // Dismiss the dialog when scrolling down beyond half of the device's height
                                        if (((peekHeightFull / 2) + 300) >= layoutParams.height) {
                                            ("DISSMISS: ${layoutParams.height}").log()
                                            dismiss()
                                        }
                                    }
                                }
        
                                initialY = event.rawY
                                return true
                            }
        
                            MotionEvent.ACTION_UP -> {
                                initialY = 0f
                                return true
                            }
                        }
                        return false
                    }
                })
            }
        
            @SuppressLint("ResourceAsColor", "ClickableViewAccessibility")
            override fun onCreateView(
                inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
            ): View {
                binding = BottomDialogBinding.inflate(layoutInflater)
                binding.apply {
                    rv.adapter = RVItemAdapter(requireActivity())
        
                }
        
                return binding.root
            }
        
        
            fun setShouldInterceptTouch(shouldIntercept: Boolean) {
                this.shouldInterceptTouch = shouldIntercept;
            }
        }


        

// get All videos from storage
    
            @SuppressLint("Range")
            fun retrieveVideos(activity: Activity): ArrayList<String> {
                var list: ArrayList<String> = arrayListOf()
                val projection = arrayOf(MediaStore.Video.Media._ID, MediaStore.Video.Media.DATA)
                val cursor: Cursor? = activity.contentResolver.query(
                    MediaStore.Video.Media.EXTERNAL_CONTENT_URI, projection, null, null, null
                )
                if (cursor != null) {
                    while (cursor.moveToNext()) {
                        val videoPath = cursor.getString(cursor.getColumnIndex(MediaStore.Video.Media.DATA))
                        list.add(videoPath)
                    }
                    cursor.close()
                }
                return list
            }
        
        
             //xml
        
             <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@android:color/transparent"
            android:clipChildren="false"
            android:clipToPadding="false"
            android:gravity="bottom"
            android:orientation="vertical">
        
            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardCornerRadius="@dimen/_12sdp">
        
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical">
        
                    <androidx.cardview.widget.CardView
                        android:id="@+id/card"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content">
        
                        <LinearLayout
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:layout_gravity="center"
                            android:clickable="false"
                            android:gravity="center"
                            android:padding="@dimen/_5sdp">
        
                            <androidx.constraintlayout.utils.widget.ImageFilterView
                                android:id="@+id/nextbtn"
                                android:layout_width="@dimen/_40sdp"
                                android:layout_height="wrap_content"
                                android:layout_gravity="center"
                                android:layout_margin="@dimen/_10sdp"
                                android:background="@color/black"
                                android:clickable="false"
                                android:contextClickable="false"
                                android:gravity="center"
                                android:padding="@dimen/_2sdp"
                                app:layout_constraintEnd_toEndOf="parent"
                                app:layout_constraintStart_toStartOf="parent"
                                app:layout_constraintTop_toTopOf="parent"
                                app:round="@dimen/_30sdp" />
        
                        </LinearLayout>
        
                    </androidx.cardview.widget.CardView>
        
                    <androidx.recyclerview.widget.RecyclerView
                        android:id="@+id/rv"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:layout_margin="@dimen/_3sdp"
                        android:layout_weight="1"
                        android:orientation="vertical"
                        app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
                        tools:itemCount="5"
                        tools:listitem="@layout/rv_item" />
                </LinearLayout>
        
            </androidx.cardview.widget.CardView>
        
        </LinearLayout>


      // Click
       btn.setOnClickListener {
                val bottomSheetFragment = CustomBottomSheetFragment()
                bottomSheetFragment.show(supportFragmentManager, bottomSheetFragment.tag)
                bottomSheetFragment.setShouldInterceptTouch(true)

            }



// get a bitmap alpha logic 


    <com.google.android.material.card.MaterialCardView
        android:id="@+id/cardView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:cardBackgroundColor="#F4F4F4"
        app:cardCornerRadius="@dimen/_8sdp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <RelativeLayout
            android:id="@+id/relative"
            android:layout_width="@dimen/_300sdp"
            android:layout_height="@dimen/_300sdp">

            <ImageView
                android:id="@+id/img1"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:adjustViewBounds="true"
                android:scaleType="centerCrop"
                android:src="@drawable/girl_2_filter"
                android:visibility="visible" />

            <ImageView
                android:id="@+id/img2"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:adjustViewBounds="true"
                android:scaleType="centerCrop"
                android:src="@drawable/girl_2" />

            <RelativeLayout
                android:id="@+id/line"
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:elevation="@dimen/_2sdp">

                <View
                    android:layout_width="@dimen/_2sdp"
                    android:layout_height="match_parent"
                    android:layout_centerInParent="true"
                    android:background="@color/white" />

                <androidx.constraintlayout.utils.widget.ImageFilterView
                    android:layout_width="@dimen/_28sdp"
                    android:layout_height="@dimen/_28sdp"
                    android:layout_centerInParent="true"
                    android:src="@drawable/downloadline" />

            </RelativeLayout>

        </RelativeLayout>

    </com.google.android.material.card.MaterialCardView>


              val originalBitmap =
                          BitmapFactory.decodeResource(resources, com.example.artimind.R.drawable.girl_2)
          
                      val desiredWidth = relative.layoutParams.width
                      val desiredHeight = relative.layoutParams.height
          
                      val resizedBitmap =
                          Bitmap.createScaledBitmap(originalBitmap, desiredWidth, desiredHeight, true)
          
                      img2.setImageBitmap(resizedBitmap)
          
                      // ------ Sec Filter Image ---------
          
                      val originalBitmap2 = BitmapFactory.decodeResource(
                          resources, com.example.artimind.R.drawable.girl_2_filter
                      )
          
                      val desiredWidth2 = relative.layoutParams.width
                      val desiredHeight2 = relative.layoutParams.height
          
                      val resizedBitmap2 =
                          Bitmap.createScaledBitmap(originalBitmap2, desiredWidth2, desiredHeight2, true)
          
                      img1.setImageBitmap(resizedBitmap2)
          
                      ("Relative Width: " + relative.layoutParams.width + " | Height: ${relative.layoutParams.height}").log()
          
                      line.setOnTouchListener { v, event ->
                          when (event.action) {
                              MotionEvent.ACTION_DOWN -> lastX = event.rawX
                              MotionEvent.ACTION_MOVE -> {
                                  val deltaX = event.rawX - lastX
                                  var newX = line.x + deltaX
                                  // Prevent the line from going beyond the relative layout
                                  if (newX < 0) {
                                      newX = 0f
                                  } else if (newX > relative.width - line.width) {
                                      newX = (relative.width - line.width).toFloat()
                                  }
                                  line.x = newX
                                  ("x: $newX").log()
                                  resizedBitmap.makeAlpha(alphaEndWidth = (newX + 28).toInt(), img2 = img2)
                                  lastX = event.rawX
                              }
          
                              else -> {}
                          }
                          true
                      }
                  }
                  
                    
                       //same work a two split view alpha 0
                        fun Bitmap.makeAlpha(alphaStartWidth: Int = 0, alphaEndWidth: Int, img2: ImageView) {
                            val modifiedBitmap = this.copy(Bitmap.Config.ARGB_8888, true)
                    
                            val alphaRange = alphaEndWidth - alphaStartWidth
                            val alphaMultiplier = 0 / alphaRange.toFloat()
                    
                            val pixelArray = IntArray(modifiedBitmap.width * modifiedBitmap.height)
                            modifiedBitmap.getPixels(
                                pixelArray, 0, modifiedBitmap.width, 0, 0, modifiedBitmap.width, modifiedBitmap.height
                            )
                    
                            for (x in alphaStartWidth until alphaEndWidth) {
                                for (y in 0 until modifiedBitmap.height) {
                                    val index = y * modifiedBitmap.width + x
                                    val pixel = pixelArray[index]
                                    val newAlpha = (Color.alpha(pixel) * alphaMultiplier).toInt()
                                    val newPixel = Color.argb(
                                        newAlpha, Color.red(pixel), Color.green(pixel), Color.blue(pixel)
                                    )
                                    pixelArray[index] = newPixel
                                }
                            }
                    
                            modifiedBitmap.setPixels(
                                pixelArray, 0, modifiedBitmap.width, 0, 0, modifiedBitmap.width, modifiedBitmap.height
                            )
                            img2.setImageBitmap(modifiedBitmap)
                        }
                    
                    
                        //work a right and left full move view at time work
                    
                    //    fun Bitmap.makeAlpha(alphaStartWidth: Int = 0, alphaEndWidth: Int, img2: ImageView) {
                    //        val alphaRange = alphaEndWidth - alphaStartWidth
                    //        val colorMatrix = ColorMatrix()
                    //
                    //        val alphaMultiplier = 255f / alphaRange.toFloat() // Scale alpha based on the range
                    //
                    //        // Adjust the alpha channel using the color matrix
                    //        colorMatrix.setScale(1f, 1f, 1f, alphaMultiplier)
                    //
                    //        // Apply the color matrix to the image
                    //        val filteredBitmap = Bitmap.createBitmap(this.width, this.height, Bitmap.Config.ARGB_8888)
                    //        val canvas = Canvas(filteredBitmap)
                    //        val paint = Paint(Paint.ANTI_ALIAS_FLAG)
                    //        paint.colorFilter = ColorMatrixColorFilter(colorMatrix)
                    //        canvas.drawBitmap(this, 0f, 0f, paint)
                    //
                    //        img2.setImageBitmap(filteredBitmap)
                    //    }


          // with the help of onBound keyurbhai 

           imageView1 = findViewById(R.id.imageView1);
        imageView2 = findViewById(R.id.imageView2);
        separator = findViewById(R.id.separator);

        separator.post(new Runnable() {
            @Override
            public void run() {
                initialSeparatorX = separator.getX();
            }
        });

        imageView1.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()) {
                    case MotionEvent.ACTION_DOWN:
                        initialX = event.getX();
                        break;
                    case MotionEvent.ACTION_MOVE:
                        float deltaX = event.getX() - initialX;
                        float newSeparatorX = initialSeparatorX + deltaX;

                        if (newSeparatorX >= 0 && newSeparatorX <= imageView1.getWidth() - separator.getWidth()) {
                            separator.setX(newSeparatorX);
                            float ratio = newSeparatorX / (imageView1.getWidth() - separator.getWidth());
                            imageView2.setClipBounds(new Rect((int) (imageView2.getWidth() * ratio), 0, imageView2.getWidth(), imageView2.getHeight()));
                        }
                        break;
                }
                return true;
            }
        });


// get all photos from storage
           
                    
           object PhotoFolderManager {
               fun getAllImageFoldersWithFirstImage(context: Context): List<FolderMD> {
                   val folderList = mutableListOf<FolderMD>()
           
                   val projection = arrayOf(
                       MediaStore.Images.Media.BUCKET_DISPLAY_NAME,
                       MediaStore.Images.Media.BUCKET_ID,
                       MediaStore.Images.Media._ID
                   )
           
                   val selection =
                       "${MediaStore.Images.Media.MIME_TYPE} = ? OR ${MediaStore.Images.Media.MIME_TYPE} = ?"
                   val selectionArgs = arrayOf("image/jpeg", "image/png")
                   val sortOrder =
                       "${MediaStore.Images.Media.BUCKET_DISPLAY_NAME} ASC, ${MediaStore.Images.Media.DATE_ADDED} ASC"
           
                   val uri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI
                   val contentResolver = context.contentResolver
                   val cursor = contentResolver.query(uri, projection, selection, selectionArgs, sortOrder)
           
                   cursor?.use {
                       val bucketNameColumn =
                           it.getColumnIndexOrThrow(MediaStore.Images.Media.BUCKET_DISPLAY_NAME)
                       val bucketIdColumn = it.getColumnIndexOrThrow(MediaStore.Images.Media.BUCKET_ID)
                       val imageIdColumn = it.getColumnIndexOrThrow(MediaStore.Images.Media._ID)
           
                       val imageMap =
                           mutableMapOf<String, Pair<Uri?, Int>>() // Pair<FirstImageUri, ImageCount>
           
                       while (it.moveToNext()) {
                           val folderName = it.getString(bucketNameColumn)
                           val folderId = it.getString(bucketIdColumn)
                           val imageId = it.getLong(imageIdColumn)
           
                           val imageUri = Uri.withAppendedPath(
                               MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
                               imageId.toString()
                           )
           
                           val (firstImageUri, imageCount) = imageMap.getOrPut(folderId) { Pair(imageUri, 0) }
           
                           if (firstImageUri == imageUri) {
                               // Increment image count only for the first image in the folder
                               imageMap[folderId] = Pair(firstImageUri, imageCount + 1)
                           }
                       }
           
                       // Create FolderMD objects with folderName, folderId, firstImageUri, and imageCount from the map
                       for (entry in imageMap.entries) {
                           val folderId = entry.key
                           val (firstImageUri, _) = entry.value
                           val folderName = getFolderNameById(context, folderId) ?: "Unknown Folder"
                           val imageUris = getImagesInFolder(context, folderId)
                           folderList.add(FolderMD(folderId, folderName, imageUris[0], imageUris))
                       }
                   }
           
                   return folderList
               }
           
               private fun getFolderNameById(context: Context, folderId: String): String? {
                   val projection = arrayOf(MediaStore.Images.Media.BUCKET_DISPLAY_NAME)
                   val selection = "${MediaStore.Images.Media.BUCKET_ID} = ?"
                   val selectionArgs = arrayOf(folderId)
           
                   val uri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI
                   val contentResolver = context.contentResolver
                   val cursor = contentResolver.query(uri, projection, selection, selectionArgs, null)
           
                   cursor?.use {
                       if (it.moveToFirst()) {
                           return it.getString(it.getColumnIndexOrThrow(MediaStore.Images.Media.BUCKET_DISPLAY_NAME))
                       }
                   }
           
                   return null
               }
           
               private fun getImagesInFolder(context: Context, folderId: String): ArrayList<Uri> {
                   val imageList = ArrayList<Uri>()
           
                   val projection = arrayOf(MediaStore.Images.Media._ID, MediaStore.Images.Media.DATA)
           
                   val selection = "${MediaStore.Images.Media.BUCKET_ID} = ?"
                   val selectionArgs = arrayOf(folderId)
           
                   val uri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI
                   val contentResolver = context.contentResolver
                   val cursor = contentResolver.query(uri, projection, selection, selectionArgs, null)
           
                   cursor?.use {
                       val imageIdColumn = it.getColumnIndexOrThrow(MediaStore.Images.Media._ID)
           
                       while (it.moveToNext()) {
                           val imageId = it.getLong(imageIdColumn)
                           val imageUri = Uri.withAppendedPath(
                               MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
                               imageId.toString()
                           )
                           imageList.add(imageUri)
                       }
                   }
           
                   imageList.reverse()
                   return imageList
               }
           }


                      
                      @Keep
                      data class FolderMD(
                          val folderId: String,
                          val folderName: String?,
                          val firstImageUri: Uri?,
                          val imageUris: ArrayList<Uri>
                      )
                      

// restart App

     private void restartApp() {
            Intent intent = new Intent(this, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP | Intent.FLAG_ACTIVITY_NEW_TASK);
            startActivity(intent);
            finish();
        }

// Progress drawable 

    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    
        <item android:id="@android:id/background">
            <shape android:shape="rectangle">
                <corners android:radius="6dp" />
                <solid android:color="#D4F5CE" />
            </shape>
    
        </item>
    
        <item android:id="@android:id/progress">
            <scale android:scaleWidth="100%">
                <shape android:shape="rectangle">
                    <corners android:radius="6dp" />
                    <gradient
                        android:endColor="#61C24F"
                        android:startColor="#26AC71" />
                </shape>
            </scale>
    
        </item>
    
    </layer-list>

// Bottomsheet M3

    tabLayout.itemIconTintList =
                resources.getColorStateList(R.color.bottom_navigation_icon_color)
            tabLayout.itemActiveIndicatorColor =
                getColorStateList(R.color.md_theme_light_tertiaryContainer)
            tabLayout.itemTextColor = getColorStateList(R.color.md_theme_light_tertiaryContainer)

            tabLayout.setOnNavigationItemSelectedListener { item ->
                when (item.itemId) {
                    R.id.videos -> {
                        viewPager.setCurrentItem(0, true)
                        true
                    }
                    R.id.folder -> {
                        viewPager.setCurrentItem(1, true)
                        true
                    }
                    else -> false
                }
            }


            <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/tab_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#4A1D2F"
        app:itemIconSize="@dimen/_25sdp"
        app:itemTextAppearanceActive="@style/BottomNavigationView.Active"
        app:labelVisibilityMode="unlabeled"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:menu="@menu/bottom_menu" />


         <style name="BottomNavigationView.Active" parent="@style/TextAppearance.AppCompat.Caption">
        <item name="android:textSize">11sp</item>
    </style>


    <menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/videos"
        android:icon="@drawable/video_selecter"
        android:title="Videos" />
    <item
        android:id="@+id/folder"
        android:icon="@drawable/folder_selector"
        android:title="Folder" />

     </menu>

     
     <selector xmlns:android="http://schemas.android.com/apk/res/android">
         <item android:drawable="@drawable/play_circle_filled" android:state_checked="true" />
         <item android:drawable="@drawable/play_circle_stroke" android:state_checked="false" />
     </selector>

// Textview scroll markue

     <TextView
             android:id="@+id/videoTitle"
             android:layout_width="0dp"
             android:layout_height="0dp"
             android:layout_weight="1"
             android:background="#80000000"
             android:ellipsize="marquee"
             android:gravity="center"
             android:marqueeRepeatLimit="marquee_forever"
             android:singleLine="true"
             android:text="Video name"
             android:textColor="@color/white"
             android:textSize="16sp"
             android:textStyle="bold"
             app:layout_constraintBottom_toBottomOf="@+id/backBtn"
             app:layout_constraintEnd_toStartOf="@+id/moreFeaturesBtn"
             app:layout_constraintStart_toEndOf="@+id/backBtn"
             app:layout_constraintTop_toTopOf="@+id/backBtn" />

             

// get all videos from internal storage android 12+

     <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.READ_MEDIA_AUDIO" />
        <uses-permission android:name="android.permission.READ_MEDIA_VIDEO" />
        <uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
        
    class Video(
        val videoId: Long,
        val videoName: String,
        val videoPath: String,
        val videoDuration: String,
        val videoSize: Long,
        val videoDateadded: String,
        val videoResolution: String,
        val videoMime: String,
        val videoTitle: String,
        val videoDescription: String,
        val videoDatetaken: String,
        val videoArtist: String,
        val videoUri: String,
        val thumb: String,
        val videoBucket: String,
        val videoBucketId: String
    )
        
        
          fun getAllPlayableVideos() {
                videoList.clear()
                val projection = arrayOf(
                    MediaStore.Video.Media._ID,
                    MediaStore.Video.Media.DISPLAY_NAME,
                    MediaStore.Video.Media.DATA,
                    MediaStore.Video.Media.DURATION,
                    MediaStore.Video.Media.SIZE,
                    MediaStore.Video.Media.DATE_ADDED,
                    MediaStore.Video.Media.RESOLUTION,
                    MediaStore.Video.Media.MIME_TYPE,
                    MediaStore.Video.Media.TITLE,
                    MediaStore.Video.Media.DESCRIPTION,
                    MediaStore.Video.Media.DATE_TAKEN,
                    MediaStore.Video.Media.ARTIST,
                    MediaStore.Video.Media.BUCKET_DISPLAY_NAME,
                    MediaStore.Video.Media.BUCKET_ID
                )
                val sortOrder = MediaStore.Video.Media.DATE_MODIFIED + " DESC"
        
                val cursor = requireActivity().contentResolver.query(
                    MediaStore.Video.Media.EXTERNAL_CONTENT_URI, projection, null, null, sortOrder
                )
        
                cursor?.use {
                    val idColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media._ID)
                    val nameColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.DISPLAY_NAME)
                    val dataColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.DATA)
                    val durationColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.DURATION)
                    val sizeColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.SIZE)
                    val dateAddesColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.DATE_ADDED)
                    val resolutionColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.RESOLUTION)
                    val mimeColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.MIME_TYPE)
                    val titleColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.TITLE)
                    val descColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.DESCRIPTION)
                    val dateTakenColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.DATE_TAKEN)
                    val artistColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.ARTIST)
                    val bucketNameColumn =
                        it.getColumnIndexOrThrow(MediaStore.Video.Media.BUCKET_DISPLAY_NAME)
                    val bucketIdColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.BUCKET_ID)
        
                    while (it.moveToNext()) {
                        val videoId = it.getLong(idColumn)
                        val videoName = it.getString(nameColumn)
                        val videoPath = it.getString(dataColumn)
                        val videoDuration = it.getString(durationColumn)
                        val videoSize = it.getString(sizeColumn).toLong()
                        val videoDateadded = it.getString(dateAddesColumn)
                        val videoResolution = it.getString(resolutionColumn)
                        val videoMime = it.getString(mimeColumn)
                        val videoTitle = it.getString(titleColumn)
                        val videoDescription =
                            if (it.getString(descColumn) == null) "null" else it.getString(descColumn)
                        val videoDatetaken =
                            if (it.getString(dateTakenColumn) == null) "null" else it.getString(
                                dateTakenColumn
                            )
        
                        val videoArtist = it.getString(artistColumn)
                        val videoBucket = it.getString(bucketNameColumn)
                        val videoBucketId = it.getString(bucketIdColumn)
                        val videoUri =
                            ContentUris.withAppendedId(MediaStore.Video.Media.EXTERNAL_CONTENT_URI, videoId)
                                .toString()
                        val thumbnail = Uri.fromFile(File(videoPath))
        
        //                ("list size == ${videoList.size}").log()
        //                ("res == $videoResolution").log()
        
        
                        videoList.add(
                            Video(
                                videoId,
                                videoName,
                                videoPath,
                                videoDuration,
                                videoSize,
                                videoDateadded,
                                videoResolution,
                                videoMime,
                                videoTitle,
                                videoDescription,
                                videoDatetaken,
                                videoArtist,
                                videoUri,
                                thumbnail.toString(),
                                videoBucket,
                                videoBucketId
                            )
                        )
        
                    }
                    setRV(videoList)
        
        
                }
        
            }
        
            fun getFolder() {
                val tempFolder = ArrayList<String>()
        
                val projection =
                    arrayOf(MediaStore.Video.Media.BUCKET_DISPLAY_NAME, MediaStore.Video.Media.BUCKET_ID)
        
                val cursor = requireActivity().contentResolver.query(
                    MediaStore.Video.Media.EXTERNAL_CONTENT_URI, projection, null, null, null
                )
                cursor?.use {
                    val bucketNameColumn =
                        it.getColumnIndexOrThrow(MediaStore.Video.Media.BUCKET_DISPLAY_NAME)
                    val bucketIdColumn = it.getColumnIndexOrThrow(MediaStore.Video.Media.BUCKET_ID)
        
                    while (it.moveToNext()) {
                        val videoBucket = it.getString(bucketNameColumn)
                        val videoBucketId = it.getString(bucketIdColumn)
        
                        if (!tempFolder.contains(videoBucket)) {
                            tempFolder.add(videoBucket)
                            folderList.add(FolderMD(videoBucketId, videoBucket))
                            ("folderList == ${folderList.size}").log()
                        }
                    }
                }
            }

            
// Picke Image From Camera & Gallery

    private var imgCaptureUri: Uri? = null
    private lateinit var photoFile: File
    
             val gallery = Intent(Intent.ACTION_GET_CONTENT).setType("image/*")
            
                        val cam = Intent(MediaStore.ACTION_IMAGE_CAPTURE)
                        try {
                            photoFile = File.createTempFile("Img", ".png")
                        } catch (e: Exception) {
                            print(e)
                        }
            
                        if (photoFile != null) {
                            imgCaptureUri = FileProvider.getUriForFile(
                                this@MainActivity,
                                "$packageName.provider", photoFile
                            )
                            cam.putExtra(MediaStore.EXTRA_OUTPUT, imgCaptureUri)
                        }
            
                        val intentArray = arrayOf(cam)
            
                        val chooser = Intent(Intent.ACTION_CHOOSER)
            
                        chooser.putExtra(Intent.EXTRA_INTENT, gallery)
                        chooser.putExtra(Intent.EXTRA_TITLE, "Select from")
                        chooser.putExtra(Intent.EXTRA_INITIAL_INTENTS, intentArray)
                        startActivityForResult(chooser, 1)



                        //onActivity Result

                           Executors.newSingleThreadExecutor().execute(Runnable {
                val bitmap: Bitmap = if (data == null) MediaStore.Images.Media.getBitmap(
                    this.contentResolver,
                    Uri.parse(imgCaptureUri.toString())
                ) else
                    ImageRotator.getCorrectlyOrientedImage(
                        this,
                        data.data
                    )

                Log.d("FFM", "1");

                val byteArrayOutputStream = ByteArrayOutputStream()
                bitmap.compress(Bitmap.CompressFormat.PNG, 100, byteArrayOutputStream)
                val face = byteArrayOutputStream.toByteArray()

                Log.d("FFM", "2 : $face");
            })

              val bitmap: Bitmap = if (data == null) MediaStore.Images.Media.getBitmap(
                    this.contentResolver,
                    Uri.parse(imgCaptureUri.toString())
                ) else
                    ImageRotator.getCorrectlyOrientedImage(
                        this,
                        data.data
                    )
                        

// Progard

                      
                      # todo: ------- U Crop lib image cropper ----------
                      -dontwarn com.yalantis.ucrop**
                      -keep class com.yalantis.ucrop** { *; }
                      -keep interface com.yalantis.ucrop** { *; }
                      
                      # todo: -------------- Keep Model Class ----------
                      -keep class com.example.artimind.Model.* { *; }
                      -keep class com.example.artimind.Model.Req.* { *; }
                      -keep class com.example.artimind.Model.ResBody.* { *; }
                      -keep class com.example.artimind.Utils.* { *; }
                      
                      # todo: -------------- End Model Class ----------
                      
                      
                      
                      # todo -------------- Retrofit Start ----------------
                      
                      -keep class retrofit2.** { *; }
                      -keep interface retrofit2.** { *; }
                      -keepattributes Signature
                      -keepattributes Exceptions
                      
                      # Keep Retrofit annotations
                      -keepattributes *Annotation*
                      -keepattributes Retrofit
                      
                      # Keep your ApiService interface and methods
                      -keep class com.example.artimind.Utils.ApiCaller { *; }
                      
                      # Keep your response models
                      -keep class com.example.artimind.Model.** { *; }
                      
                      # todo -------------- Retrofit End ----------------
                      
                      ##---------------Begin: proguard configuration for Gson  ----------
                      # Gson uses generic type information stored in a class file when working with fields. Proguard
                      # removes such information by default, so configure it to keep all of it.
                      -keepattributes Signature
                      
                      # For using GSON @Expose annotation
                      -keepattributes *Annotation*
                      
                      # Gson specific classes
                      -dontwarn sun.misc.**
                      #-keep class com.google.gson.stream.** { *; }
                      
                      # Application classes that will be serialized/deserialized over Gson
                      -keep class com.google.gson.examples.android.model.** { <fields>; }
                      
                      # Prevent proguard from stripping interface information from TypeAdapter, TypeAdapterFactory,
                      # JsonSerializer, JsonDeserializer instances (so they can be used in @JsonAdapter)
                      -keep class * extends com.google.gson.TypeAdapter
                      -keep class * implements com.google.gson.TypeAdapterFactory
                      -keep class * implements com.google.gson.JsonSerializer
                      -keep class * implements com.google.gson.JsonDeserializer
                      
                      # Prevent R8 from leaving Data object members always null
                      -keepclassmembers,allowobfuscation class * {
                        @com.google.gson.annotations.SerializedName <fields>;
                      }
                      
                      # Retain generic signatures of TypeToken and its subclasses with R8 version 3.0 and higher.
                      -keep,allowobfuscation,allowshrinking class com.google.gson.reflect.TypeToken
                      -keep,allowobfuscation,allowshrinking class * extends com.google.gson.reflect.TypeToken
                      
                      ##---------------End: proguard configuration for Gson  ----------
                      
                      -keep public class * implements com.bumptech.glide.module.GlideModule
                      -keep public class * extends com.bumptech.glide.module.AppGlideModule
                      -keep public enum com.bumptech.glide.load.ImageHeaderParser$** {
                        **[] $VALUES;
                        public *;
                      }
                      
                      -keepclassmembers enum * {
                      public static **[] values();
                      public static ** valueOf(java.lang.String);
                      }
                      
                      -keep class com.crashlytics.* { *; }
                      -dontwarn com.crashlytics.**
                      -keepattributes SourceFile,LineNumberTable
                      -renamesourcefileattribute SourceFile
                      
                      -optimizations !class/merging/*
                      
                      -dontusemixedcaseclassnames
                      -dontskipnonpubliclibraryclasses
                      -verbose
                      
                      
                      #todo -------------- All Log gone -----------------
                      -assumenosideeffects class android.util.Log {
                          public static boolean isLoggable(java.lang.String, int);
                          public static int v(...);
                          public static int i(...);
                          public static int w(...);
                          public static int d(...);
                          public static int e(...);
                          public static int wtf(...);
                      }
                      
                      #todo -------------- End Log -----------------
                      
                      #okio
                      -dontwarn okhttp3.**
                      -dontwarn okio.**
                      -dontwarn retrofit2.PlatformJava8
                      
                      #retorofit
                      # Retrofit does reflection on generic parameters. InnerClasses is required to use Signature and
                      # EnclosingMethod is required to use InnerClasses.
                      -keepattributes Signature, InnerClasses, EnclosingMethod
                      
                      # Retrofit does reflection on method and parameter annotations.
                      -keepattributes RuntimeVisibleAnnotations, RuntimeVisibleParameterAnnotations
                      
                      # Retain service method parameters when optimizing.
                      -keepclassmembers,allowshrinking,allowobfuscation interface * {
                          @retrofit2.http.* <methods>;
                      }
                      
                      # Ignore annotation used for build tooling.
                      -dontwarn org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement
                      
                      # Ignore JSR 305 annotations for embedding nullability information.
                      -dontwarn javax.annotation.**
                      
                      # Guarded by a NoClassDefFoundError try/catch and only used when on the classpath.
                      -dontwarn kotlin.Unit
                      
                      # Top-level functions that can only be used by Kotlin.
                      -dontwarn retrofit2.KotlinExtensions
                      
                      # With R8 full mode, it sees no subtypes of Retrofit interfaces since they are created with a Proxy
                      # and replaces all potential values with null. Explicitly keeping the interfaces prevents this.
                      -if interface * { @retrofit2.http.* <methods>; }
                      -keep,allowobfuscation interface <1>
                      
                      #okhtttp
                      
                      # JSR 305 annotations are for embedding nullability information.
                      -dontwarn javax.annotation.**
                      
                      # A resource is loaded with a relative path so the package of this class must be preserved.
                      -keepnames class okhttp3.internal.publicsuffix.PublicSuffixDatabase
                      
                      # Animal Sniffer compileOnly dependency to ensure APIs are compatible with older versions of Java.
                      -dontwarn org.codehaus.mojo.animal_sniffer.*
                      
                      # OkHttp platform used only on JVM and when Conscrypt dependency is available.
                      -dontwarn okhttp3.internal.platform.ConscryptPlatform
                      
                      #todo -------------- Encrypt All Code -----------------
                      -obfuscationdictionary "/Users/fenillathiya/Desktop/Extra/encrypt.txt"
                      -packageobfuscationdictionary "/Users/fenillathiya/Desktop/Extra/encrypt.txt"
                      -classobfuscationdictionary "/Users/fenillathiya/Desktop/Extra/encrypt.txt"
                      
                      -mergeinterfacesaggressively
                      -overloadaggressively
                      -repackageclasses 'o'
                      -adaptresourcefilenames
                      
                      #todo -------------- End All Code -----------------
                      

            ##---------------Begin: proguard configuration for Gson  ----------
       # Gson uses generic type information stored in a class file when working with fields. Proguard
       # removes such information by default, so configure it to keep all of it.
       -keepattributes Signature
       
       # For using GSON @Expose annotation
       -keepattributes *Annotation*
       
       # Gson specific classes
       -dontwarn sun.misc.**
       #-keep class com.google.gson.stream.** { *; }
       
       -keep public class com.example.Model.** {
         *;
       }
       -keep public class com.example.Model.** {
         *;
       }
       # Application classes that will be serialized/deserialized over Gson
       -keep class com.google.gson.examples.android.model.** { <fields>; }
       
       # Prevent proguard from stripping interface information from TypeAdapter, TypeAdapterFactory,
       # JsonSerializer, JsonDeserializer instances (so they can be used in @JsonAdapter)
       -keep class * extends com.google.gson.TypeAdapter
       -keep class * implements com.google.gson.TypeAdapterFactory
       -keep class * implements com.google.gson.JsonSerializer
       -keep class * implements com.google.gson.JsonDeserializer
       
       # Prevent R8 from leaving Data object members always null
       -keepclassmembers,allowobfuscation class * {
         @com.google.gson.annotations.SerializedName <fields>;
       }
       
       # Retain generic signatures of TypeToken and its subclasses with R8 version 3.0 and higher.
       -keep,allowobfuscation,allowshrinking class com.google.gson.reflect.TypeToken
       -keep,allowobfuscation,allowshrinking class * extends com.google.gson.reflect.TypeToken
       
       ##---------------End: proguard configuration for Gson  ----------
       
       
       -keep public class * implements com.bumptech.glide.module.GlideModule
       -keep public class * extends com.bumptech.glide.module.AppGlideModule
       -keep public enum com.bumptech.glide.load.ImageHeaderParser$** {
         **[] $VALUES;
         public *;
       }

       
            # OkHttp platform used only on JVM and when Conscrypt dependency is available.
            -dontwarn okhttp3.internal.platform.ConscryptPlatform
            
            -obfuscationdictionary "/Users/Desktop/KeystoreAndProguard/encrypt.txt"
            -packageobfuscationdictionary "/Users/Desktop/KeystoreAndProguard/encrypt.txt"
            -classobfuscationdictionary "/Users/Desktop/KeystoreAndProguard/encrypt.txt"
            
            -mergeinterfacesaggressively
            -overloadaggressively
            -repackageclasses 'o'
            -adaptresourcefilenames
       
       -keepclassmembers enum * {
       public static **[] values();
       public static ** valueOf(java.lang.String);
       }
       
       -keep class com.crashlytics.* { *; }
       -dontwarn com.crashlytics.**
       -keepattributes SourceFile,LineNumberTable
       -renamesourcefileattribute SourceFile
       
       -optimizations !class/merging/*
       
       -dontusemixedcaseclassnames
       -dontskipnonpubliclibraryclasses
       -verbose
       
       
       -assumenosideeffects class android.util.Log {
           public static boolean isLoggable(java.lang.String, int);
           public static int v(...);
           public static int i(...);
           public static int w(...);
           public static int d(...);
           public static int e(...);
           public static int wtf(...);
       }
       #okio
       
       -dontwarn okhttp3.**
       -dontwarn okio.**
       -dontwarn retrofit2.PlatformJava8
       
       #retorofit
       # Retrofit does reflection on generic parameters. InnerClasses is required to use Signature and
       # EnclosingMethod is required to use InnerClasses.
       -keepattributes Signature, InnerClasses, EnclosingMethod
       -keepattributes Exceptions,Deprecated,SourceFile,LineNumberTable
       
       # Retrofit does reflection on method and parameter annotations.
       -keepattributes RuntimeVisibleAnnotations, RuntimeVisibleParameterAnnotations
       -keepattributes *Annotation*
       -keep public class * {
           public protected *;
       }
       # Retain service method parameters when optimizing.
       -keepclassmembers,allowshrinking,allowobfuscation interface * {
           @retrofit2.http.* <methods>;
       }
       
       -keepclassmembernames class * {
           java.lang.Class class$(java.lang.String);
           java.lang.Class class$(java.lang.String, boolean);
       }
       -keepclasseswithmembernames class * {
           native <methods>;
       }
       
       -keepclassmembers class * extends java.lang.Enum {
           public static **[] values();
           public static ** valueOf(java.lang.String);
       }
       
       -keepclassmembers class * implements java.io.Serializable {
           static final long serialVersionUID;
           static final java.io.ObjectStreamField[] serialPersistentFields;
           private void writeObject(java.io.ObjectOutputStream);
           private void readObject(java.io.ObjectInputStream);
           java.lang.Object writeReplace();
           java.lang.Object readResolve();
       }
       
       # Ignore annotation used for build tooling.
       -dontwarn org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement
       
       # Ignore JSR 305 annotations for embedding nullability information.
       -dontwarn javax.annotation.**
       
       # Guarded by a NoClassDefFoundError try/catch and only used when on the classpath.
       -dontwarn kotlin.Unit
       
       # Top-level functions that can only be used by Kotlin.
       -dontwarn retrofit2.KotlinExtensions
       
       # With R8 full mode, it sees no subtypes of Retrofit interfaces since they are created with a Proxy
       # and replaces all potential values with null. Explicitly keeping the interfaces prevents this.
       #-if interface * { @retrofit2.http.* <methods>; }
       #-keep,allowobfuscation interface <1>
       
       #okhtttp
       
       # JSR 305 annotations are for embedding nullability information.
       -dontwarn javax.annotation.**
       
       # A resource is loaded with a relative path so the package of this class must be preserved.
       -keepnames class okhttp3.internal.publicsuffix.PublicSuffixDatabase
       
       # Animal Sniffer compileOnly dependency to ensure APIs are compatible with older versions of Java.
       -dontwarn org.codehaus.mojo.animal_sniffer.*
       
       # OkHttp platform used only on JVM and when Conscrypt dependency is available.
       -dontwarn okhttp3.internal.platform.ConscryptPlatform
       
       #todo: Encryptedstring.txt file
       
       
       -mergeinterfacesaggressively
       -overloadaggressively
       -repackageclasses 'o'
       -adaptresourcefilenames
       
       
       # todo: Room
       #-keep class androidx.room.** { *; }
       #-keepattributes InnerClasses
       #-keepclassmembers class * {
       #    @androidx.room.* <fields>;
       #}
       
       -keep class com.anchorfree.proxy_bootstrap.** { *; }
       
       #todo: WebChrom
       -keep class * extends android.webkit.WebChromeClient { *; }
       
       #todo: Yandex
       -keep class com.yandex.metrica.** { *; }
       -dontwarn com.yandex.metrica.**
       
       
       # Optimizations and obfuscations
       -allowaccessmodification
       -optimizationpasses 5
       -keepattributes SourceFile,LineNumberTable
       -keepattributes *Annotation*,EnclosingMethod
       -renamesourcefileattribute SourceFile
       -keepnames class * {
           native <methods>;
       }
       
       -keep class java.nio.charset.StandardCharsets {
           public static java.nio.charset.Charset UTF_8;
       }
       
       # Ignore warnings
       -dontwarn okio.**
       -dontwarn org.conscrypt.**
       -dontwarn org.apache.commons.logging.**
       -dontwarn org.apache.http.**
       -dontwarn android.net.http.AndroidHttpClient
       -dontwarn javax.annotation.**
       -dontwarn java.nio.file.Files
       -dontwarn java.nio.file.Path
       
       # Keep classes that are used for serialization/deserialization
       -keep class com.google.gson.** { *; }
       -keep class org.joda.time.** { *; }
       -keep class org.joda.convert.** { *; }
       
       # Keep some enums
       -keepclassmembers enum * {
           public static **[] values();
           public static ** valueOf(java.lang.String);
       }
       
       # Keep the entry point to the app
       -keepclassmembers class * {
           public static void main(java.lang.String[]);
       }
       
       -keepclassmembers class * extends com.google.crypto.tink.shaded.protobuf.GeneratedMessageLite {
         <fields>;
       }
       
       -keep class com.google.crypto.tink.** { *; }
       
       # Keep the native methods and classes of some libraries
       -keep class android.support.v8.renderscript.** { *; }
       -keepclassmembers class android.support.v8.renderscript.** {
           native <methods>;
       }
       
       -keep class org.apache.http.** { *; }
       -keepclassmembers class org.apache.http.** {
           native <methods>;
       }
       -keep class okhttp3.internal.publicsuffix.PublicSuffixDatabase { public *; }
       
       -keep class io.netty.** { *; }

// FFMPAGE

       ----- Video to Audio ------
     val command = arrayOf(
                     "-i",
                     inputFilePath,
                     "-vn",
                     "-c:a",
                     "libmp3lame",
                     "-qscale:a",
                     "0",            //-qscale:a is set to 0 to get the highest quality of mp3
                     outputFilePath
                 )
                 try {
                     val s = FFmpeg.execute(command)
                     Log.d(LOG, "success = $s")
                     moveFileToDestination(outputFilePath)
                 } catch (e: IOException) {
                     updateOperationStage(Utils.FAILED)
                     updateErrorMessage(e.message.toString())
                 }
            
//Unzip foe File

       class ZipUtilsFiles {
           private val BUFFER_SIZE = 1024
       
           @Throws(IOException::class)
           fun unzip(zipFilePath: String?, destinationFolderPath: String?) {
               val destDir = File(destinationFolderPath)
               if (!destDir.exists()) {
                   destDir.mkdirs()
               }
               val fis = FileInputStream(zipFilePath)
               val zis = ZipInputStream(BufferedInputStream(fis))
               var zipEntry: ZipEntry
               val buffer = ByteArray(BUFFER_SIZE)
               try {
       
                   while (zis.nextEntry.also { zipEntry = it } != null) {
                       val entryName: String = zipEntry.name
                       val entryFile = File(destinationFolderPath, entryName)
                       if (zipEntry.isDirectory) {
                           entryFile.mkdirs()
                       } else {
                           val parentDir: File = entryFile.parentFile as File
                           if (!parentDir.exists()) {
                               parentDir.mkdirs()
                           }
                           val fos = FileOutputStream(entryFile)
                           val bos = BufferedOutputStream(fos, BUFFER_SIZE)
                           var bytesRead: Int
                           while (zis.read(buffer, 0, BUFFER_SIZE).also { bytesRead = it } != -1) {
                               bos.write(buffer, 0, bytesRead)
                           }
                           bos.flush()
                           bos.close()
                       }
                       zis.closeEntry()
                   }
                   zis.close()
               } catch (e: java.lang.Exception) {
                   e.printStackTrace()
               }
       
           }
       }

// Unzip DocumentFile

        unzip.setOnClickListener {
                val dest =
                    File("/storage/emulated/0/Android/media/com.example.pkg/files/Saves/").toUri()
                val file = File(
                    Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS),
                    "abc.zip"
                )
                val zipFileUri: Uri = FileProvider.getUriForFile(
                    this@MainActivity,
                    "$packageName.provider",
                    file
                )

                try {
                    unzip(contentResolver, zipFileUri, dest)
                    // Unzipping successful
                    ("UnZip Succ").log()
                } catch (e: IOException) {
                    // Handle IOException
                    ("Error: ${e.message}").log()
                }
            }
            
       public class ZipFileDocument {
           private static final int BUFFER_SIZE = 1024;
       
           public static void unzip(ContentResolver contentResolver, Uri zipFileUri, Uri destinationTreeUri) throws IOException {
               File destinationFolder = new File(destinationTreeUri.getPath());
               if (!destinationFolder.exists()) {
                   destinationFolder.mkdirs();
               }
       
               BufferedInputStream bis = new BufferedInputStream(contentResolver.openInputStream(zipFileUri));
               ZipInputStream zis = new ZipInputStream(bis);
               ZipEntry zipEntry;
       
               byte[] buffer = new byte[BUFFER_SIZE];
               while ((zipEntry = zis.getNextEntry()) != null) {
                   String entryName = zipEntry.getName();
                   File entryFile = new File(destinationFolder, entryName);
                   if (zipEntry.isDirectory()) {
                       entryFile.mkdirs();
                   } else {
                       entryFile.getParentFile().mkdirs();
                       FileOutputStream fos = new FileOutputStream(entryFile);
                       BufferedOutputStream bos = new BufferedOutputStream(fos, BUFFER_SIZE);
       
                       int bytesRead;
                       while ((bytesRead = zis.read(buffer, 0, BUFFER_SIZE)) != -1) {
                           bos.write(buffer, 0, bytesRead);
                       }
       
                       bos.flush();
                       bos.close();
                   }
                   zis.closeEntry();
               }
               zis.close();
           }
       }


// Corotine Work Worker Manager

       val workData = Data.Builder()
                              .putString(DownloadWorker.urlKey, item.url)
                              .putString(DownloadWorker.nameKey, itemvideo.title + item.format)
                              .putString(DownloadWorker.formatIdKey, item.formatId)
                              .putString(DownloadWorker.acodecKey, item.acodec)
                              .putString(DownloadWorker.vcodecKey, item.vcodec)
                              .putString(DownloadWorker.taskIdKey, itemvideo.id + "_" + item.format)
                              .putString(DownloadWorker.ext, item.ext)
                              .putString(DownloadWorker.link, link)
                              .build()

                    val workRequest: WorkRequest =
                        OneTimeWorkRequest.Builder(DownloadWorker::class.java)
                            .addTag(itemvideo.id)
                            .setInputData(workData)
                            .build()

                    WorkManager.getInstance(activity).enqueueUniqueWork(
                        itemvideo.id, ExistingWorkPolicy.KEEP,
                        (workRequest as OneTimeWorkRequest)
                    )

                         class DownloadWorker(appContext: Context, params: WorkerParameters) :
         CoroutineWorker(appContext, params) {
     
         private val notificationManager =
             appContext.getSystemService(Context.NOTIFICATION_SERVICE) as
                     NotificationManager?
     
         override suspend fun doWork(): Result {
     
             val url = inputData.getString(urlKey)!!
             val taskId = inputData.getString(tasid)!!
             val name = inputData.getString(name)!!
             val hasnotificationId = id.hashCode()
     
             ("Corotines Url: $url").log()
     
     
             createNotificationChannel()
             val notificationId = 1
             val notification = NotificationCompat.Builder(
                 applicationContext,
                 ""
             )
                 .setSmallIcon(R.drawable.download_icon)
                 .setContentTitle("Download")
                 .setSound(null)
                 .setOnlyAlertOnce(true)
                 .setAutoCancel(false)
                 .setContentText("Start Downloading service")
                 .build()
     
             val foregroundInfo = ForegroundInfo(notificationId, notification)
             setForeground(foregroundInfo)
     
             try {
                 // create a URL object for the video
                 val url = URL(url)
     
                 // create a connection to the server
                 val connection: HttpURLConnection =
                     withContext(Dispatchers.IO) {
                         url.openConnection()
                     } as HttpURLConnection
     
                 // set the request method to GET
                 connection.requestMethod = "GET"
     
                 // connect to the server
                 withContext(Dispatchers.IO) {
                     connection.connect()
                 }
     
                 // get the content length of the video
                 val contentLength: Int = connection.contentLength
     
                 // create a stream to read the data from the server
                 val input: InputStream = connection.inputStream
     
                 // create a stream to write the data to a file
     
                 val filePath =
                     "${Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).path}/FOLDERNAME/${System.currentTimeMillis() / 1000}.mp4"
                 val output = withContext(Dispatchers.IO) {
                     FileOutputStream(File(filePath))
                 }
     
                 // set the buffer size
                 val buffer = ByteArray(1024)
                 var bytesRead: Int
     
                 // download the video and update the progress bar
     
                 listofdownloadmodel.add(DownloadModel(filePath, 1, taskId, name, url.toString()))
     
     
                 while (withContext(Dispatchers.IO) {
                         input.read(buffer)
                     }.also { bytesRead = it } != -1) {
                     withContext(Dispatchers.IO) {
                         output.write(buffer, 0, bytesRead)
                     }
                     val progresss = ((withContext(Dispatchers.IO) {
                         output.channel.size()
                     } * 100 / contentLength).toInt())
     //                ("$progresss").log()
                     try {
                         listofdownloadmodel.find { it.id == taskId }!!.progress = progresss
                         mulaList.postValue(listofdownloadmodel)
     //                    showProgress(
     //                        hasnotificationId,
     //                        taskId,
     //                        name,
     //                        progresss,
     //                        "${progresss}% Downloading.",
     //                        File("${Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).path}/FOLDERNAME/")
     //                    )
                     } catch (e: Throwable) {
                         Log.e("FATZ", "Error: " + e.message.toString())
                         e.printStackTrace()
                     }
                 }
                 notificationManager!!.cancel(hasnotificationId)
                 ("NOw").log()
                 withContext(Dispatchers.IO) {
                     input.close()
                     output.close()
                 }
             } catch (e: Exception) {
                 ("Error: ${e.message}").log()
                 e.printStackTrace()
             }
             return Result.success()
         }
     
         private fun showProgress(
             id: Int,
             taskId: String,
             name: String,
             progress: Int,
             line: String,
             tmpFile: File
         ) {
             val text = line.replace(tmpFile.toString(), "")
     //        val intent = Intent(applicationContext, CancelReceiver::class.java)
     //            .putExtra("taskId", taskId)
     //            .putExtra("notificationId", id)
     
     //        val pendingIntent =
     //            PendingIntent.getBroadcast(
     //                applicationContext,
     //                0,
     //                intent,
     //                flags
     //            )
             val notification = NotificationCompat.Builder(
                 applicationContext,
                 myNotificationChannel
             )
                 .setPriority(NotificationCompat.PRIORITY_LOW)
                 .setSmallIcon(R.drawable.download_icon)
                 .setContentTitle(name)
                 .setStyle(
                     NotificationCompat.BigTextStyle()
                         .bigText(text)
                 )
                 .setSound(null)
                 .setOnlyAlertOnce(true)
                 .setAutoCancel(false)
                 .setProgress(100, progress, progress == -1)
     //            .addAction(
     //                R.drawable.download_icon,
     //                applicationContext.getString(R.string.cancel),
     //                pendingIntent
     //            )
                 .build()
             notificationManager?.notify(id, notification)
         }

    private fun createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            var notificationChannel =
                notificationManager?.getNotificationChannel(myNotificationChannel)
            if (notificationChannel == null) {
                val channelName = applicationContext.getString(R.string.app_name)
                notificationChannel = NotificationChannel(
                    myNotificationChannel,
                    channelName, NotificationManager.IMPORTANCE_LOW
                )
                notificationChannel.description =
                    channelName
                notificationManager?.createNotificationChannel(notificationChannel)
            }
        }
    }

    companion object {
        const val urlKey = "url"
        const val tasid = "taskid"
        const val name = "name"
    }

                    class DownloadWorker(appContext: Context, params: WorkerParameters) :
    CoroutineWorker(appContext, params) {
     override suspend fun doWork(): Result {
     }
    }

     class CancelReceiver : BroadcastReceiver() {
         private val TAG = "CancelReceiver"
     
         override fun onReceive(context: Context?, intent: Intent?) {
             Log.d(TAG, "Task Canceler called")
     
             if (intent == null) return
             val taskId = intent.getStringExtra("taskId")
             val notificationId = intent.getIntExtra("notificationId", 0)
             if (taskId.isNullOrEmpty()) return
             Log.d(TAG, "Task (id:$taskId) was killed.")
             WorkManager.getInstance(context!!).cancelAllWorkByTag(taskId)
             val notificationManager =
                 context.getSystemService(Context.NOTIFICATION_SERVICE) as
                         NotificationManager?
             Toast.makeText(context, R.string.taskcancel, Toast.LENGTH_LONG).show()
             notificationManager?.cancel(notificationId)
         }
     }

        <receiver
            android:name=".Service.CancelReceiver"
            android:exported="true" />

// conflict lib error

      android.enableJetifier=true

      plugins {
    id 'com.android.application' version '7.4.1' apply false
    id 'com.android.library' version '7.4.1' apply false
    id 'org.jetbrains.kotlin.android' version '1.7.20' apply false
      }

      plugins {
          id 'com.android.application'
          id 'org.jetbrains.kotlin.android'
      }


    constraints {
        implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk7:1.8.0") {
            because("kotlin-stdlib-jdk7 is now a part of kotlin-stdlib")
        }
        implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8:1.8.0") {
            because("kotlin-stdlib-jdk8 is now a part of kotlin-stdlib")
        }
    }

         //configurations {
     //    cleanedAnnotations
     //    implementation.exclude group: 'org.jetbrains', module: 'annotations'
     //    implementation.exclude group: 'org.jetbrains.kotlin', module: 'kotlin-stdlib-jdk8'
     ////    implementation.exclude group: 'com.google.code.gson', module: 'gson'
     //}

//Drag view touchlisioner

      class OnBoundedDragTouchListener(var mView: View, var mParent: View = mView.parent as View) : View.OnTouchListener {
          private var isDragging = false
          private var isInitialized = false
      
          private var xWhenAttached = 0f
          private var yWhenAttached = 0f
      
          private var maxLeft = 0f
          private var maxRight = 0f
          private var maxTop = 0f
          private var maxBottom = 0f
      
          private var width = 0
          private var height = 0
      
          private var dX = 0f
          private var dY = 0f
      
          private lateinit var mOnDragActionListener: OnDragActionListener
      
          fun setOnDragActionListener(mOnDragActionListener: OnDragActionListener) {
              this.mOnDragActionListener = mOnDragActionListener
          }
      
          interface OnDragActionListener {
              fun onDragStart(view: View)
              fun onDragEnd(view: View)
          }
      
          @SuppressLint("ClickableViewAccessibility")
          override fun onTouch(v: View, event: MotionEvent): Boolean {
              if (isDragging) {
                  val bounds = FloatArray(4)
                  // LEFT
                  bounds[0] = event.rawX + dX
                  if (bounds[0] < maxLeft) {
                      bounds[0] = maxLeft
                  }
                  // RIGHT
                  bounds[2] = bounds[0] + width
                  if (bounds[2] > maxRight) {
                      bounds[2] = maxRight
                      bounds[0] = bounds[2] - width
                  }
                  // TOP
                  bounds[1] = event.rawY + dY
                  if (bounds[1] < maxTop) {
                      bounds[1] = maxTop
                  }
                  // BOTTOM
                  bounds[3] = bounds[1] + height
                  if (bounds[3] > maxBottom) {
                      bounds[3] = maxBottom
                      bounds[1] = bounds[3] - height
                  }
      
                  when (event.action) {
      
                      MotionEvent.ACTION_MOVE -> {
                          mView.animate().x(bounds[0]).y(bounds[1]).setDuration(0).start()
                          mOnDragActionListener.onDragStart(mView)
                      }
                      MotionEvent.ACTION_CANCEL, MotionEvent.ACTION_UP -> onDragFinish()
      
                  }
              } else {
                  when (event.action) {
                      MotionEvent.ACTION_DOWN -> {
                          isDragging = true
                          if (!isInitialized) {
                              updateBounds()
                          }
                          dX = v.x - event.rawX
                          dY = v.y - event.rawY
                          mOnDragActionListener.onDragStart(mView)
                      }
                  }
              }
      
              return true
          }
      
          private fun onDragFinish() {
              mOnDragActionListener.onDragEnd(mView)
              dX = 0f
              dY = 0f
              isDragging = false
          }
      
          private fun updateBounds() {
              updateViewBounds()
              updateParentBounds()
              isInitialized = true
          }
      
          private fun updateViewBounds() {
              width = mView.width
              xWhenAttached = mView.x
              dX = 0f
              height = mView.height
              yWhenAttached = mView.y
              dY = 0f
          }
      
          private fun updateParentBounds() {
              maxLeft = -mView.width * 1f / 2
              maxTop = -mView.height * 1f / 2
              maxRight = mParent.width.toFloat() + mView.width * 1f / 2
              maxBottom = mParent.height.toFloat() + mView.height * 1f / 2
      
      //        maxLeft = 0f                          //old one
      //        maxTop = 0f                           //old one
      //        maxRight = maxLeft + mParent.width    //old one
      //        maxBottom = maxTop + mParent.height   //old one
          }
      }
      // after call activity
       val drag = OnBoundedDragTouchListener(imagerela, rootwebview)
                  drag.setOnDragActionListener(object : OnBoundedDragTouchListener.OnDragActionListener {
                      override fun onDragStart(view: View) {
      
                      }
      
                      override fun onDragEnd(view: View) {
                      }
                  })

      view.setOnTouchListener(drag)
      
// Make Folder

    onBackground {
       val file_v = File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).toString() + directoryInstaShoryDirectorydownload_videos_facebook)
              if (!file_v.exists()) {
                  "Make Dir".log()
                  file_v.mkdir()
              }
      }

// viewpager2 & tabs scrolling

      <androidx.viewpager2.widget.ViewPager2
              android:id="@+id/viewpager"
              android:layout_width="match_parent"
              android:layout_height="0dp"
              android:layout_marginStart="@dimen/_15sdp"
              android:layout_marginEnd="@dimen/_15sdp"
              android:layout_weight="1"
              android:background="@drawable/bg_black"
              android:orientation="horizontal"
              android:padding="@dimen/_8sdp" />

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabBanner"
        android:layout_width="match_parent"
        android:layout_height="@dimen/_10sdp"
        android:layout_marginTop="@dimen/_15sdp"
        android:layout_marginBottom="@dimen/_12sdp"
        android:elevation="@dimen/_1sdp"
        app:tabBackground="@drawable/tab_selector"
        app:tabGravity="center"
        app:tabIndicatorHeight="0dp"
        app:tabPaddingEnd="@dimen/_5sdp" />

        // tab_selecter.xml
        
              <selector xmlns:android="http://schemas.android.com/apk/res/android">
          <item android:state_selected="true">
              <layer-list>
                  <item>
                      <shape android:innerRadius="0dp" android:shape="ring" android:thickness="@dimen/_5sdp" android:useLevel="false">
                          <solid android:color="@color/white" />
                          <!--                    <stroke android:width="@dimen/_2sdp" android:color="#FFF" android:dashWidth="@dimen/_1sdp" />-->
                      </shape>
                  </item>
              </layer-list>
          </item>
      
          <item>
              <layer-list>
                  <item>
                      <shape android:innerRadius="0dp" android:shape="ring" android:thickness="@dimen/_3sdp" android:useLevel="false">
                          <solid android:color="#474545" />
                      </shape>
                  </item>
              </layer-list>
          </item>
      </selector>


    var list: ArrayList<Int> = arrayListOf()

    list.add(R.drawable.screen_one)
     list.add(R.drawable.screen_two)
     list.add(R.drawable.screen_three)
     list.add(R.drawable.screen_four)

     viewpager.adapter = ViewPagerAdapter(this, list)
      viewpager.offscreenPageLimit = 1
      viewpager.getChildAt(0).overScrollMode = 2
      val transformer = CompositePageTransformer()
      transformer.addTransformer(MarginPageTransformer(8))
      viewpager.setPageTransformer(transformer)
      val tabLayout =
          TabLayoutMediator(tabBanner, viewpager, true) { _: TabLayout.Tab?, _: Int -> }
      tabLayout.attach()
      tabBanner.addOnTabSelectedListener(object : TabLayout.OnTabSelectedListener {
          override fun onTabSelected(tab: TabLayout.Tab) {
              ("Tab Selected: ${tab.position}").log()
              this@HowUseAct.tab = tab.position
              tvnext.text = if (tab.position == 3) "Finish" else "Next"
          }
          override fun onTabUnselected(tab: TabLayout.Tab) {}
          override fun onTabReselected(tab: TabLayout.Tab) {}
      })

    //viewpager2 adapter

    class ViewPagerAdapter(var activity: Activity, var list: ArrayList<Int>) :
    RecyclerView.Adapter<ViewPagerAdapter.VH>() {
    class VH(var binding: ViewpagerItemBinding) : RecyclerView.ViewHolder(binding.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = VH(
        ViewpagerItemBinding.inflate(
            LayoutInflater.from(activity), parent, false
        )
    )

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = list[position]

        holder.binding.apply {
            image.setImageResource(item)
        }
    }

    override fun getItemCount() = list.size
      }

      //viewpager_item.xml

      <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/image"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:adjustViewBounds="true"
        android:scaleType="fitCenter"
        android:src="@color/black" />

      </LinearLayout>

// get Video duration

      val mediaPlayer = MediaPlayer()
       mediaPlayer.setDataSource(video_url)
        mediaPlayer.setOnPreparedListener {
                        val minutes: Long =
                            TimeUnit.MILLISECONDS.toMinutes(mediaPlayer.duration.toLong())
                        val seconds: Long =
                            TimeUnit.MILLISECONDS.toSeconds(mediaPlayer.duration.toLong()) - TimeUnit.MINUTES.toSeconds(
                                minutes
                            )
                        val duration = String.format("%02d:%02d", minutes, seconds)
            
                    }
                    ("Vide: ${duration}").log()
                    mediaPlayer.prepareAsync()
                    mediaPlayer.release()

         get Video Size from link

         val url by lazy { URL(video_url) }
         val urlConnection by lazy { url.openConnection() as HttpURLConnection }
         urlConnection.requestMethod = "HEAD"
         urlConnection.connect()
         val contentLength: Int = urlConnection.contentLength
         item.length = contentLength.toLong()
         it.size = contentLength.toLong().toString()
         urlConnection.disconnect()

      //  get video pixel from url

            val retriever by lazy { MediaMetadataRetriever() }
    retriever.setDataSource(item.video_url_list!![0].video_url, HashMap())
    val videoWidth =
        retriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_VIDEO_WIDTH)
     val videoHeight =
         retriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_VIDEO_HEIGHT)
     val videoBitrate =
         retriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_BITRATE)
       item.video_url_list!![0].quality = "$videoWidth p"
    
//Exo Player

    implementation 'com.google.android.exoplayer:exoplayer:2.15.0'
    implementation 'com.github.HamidrezaAmz:MagicalExoPlayer:2.0.7'


    <com.potyvideo.library.AndExoPlayerView
           android:id="@+id/videoview"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           android:background="@color/black"/>

      val link = intent.extras!!.getString("link", "")
      val extraHeaders: HashMap<String, String> = HashMap()
      extraHeaders["foo"] = "bar"
      videoview.setBackgroundColor(Color.BLACK)
      videoview.setSource(link, extraHeaders)

       <activity
            android:name=".Activity.VideoAct"
            android:configChanges="uiMode|keyboard|keyboardHidden|orientation|screenSize|screenLayout|smallestScreenSize|uiMode"
            android:exported="false"
            android:theme="@style/Theme.WindowFullscreen" />

     <style name="Theme.WindowFullscreen" parent="Theme.WebScraping">
        <item name="android:windowNoTitle">true</item>
        <item name="android:windowFullscreen">true</item>
    </style>
    
// Room Database
        
        val room_version = "2.6.1"
         implementation("androidx.room:room-runtime:$room_version")
            ksp("androidx.room:room-compiler:$room_version")
            implementation("androidx.room:room-ktx:$room_version")
            annotationProcessor("androidx.room:room-compiler:$room_version")
        
                plugins {
            alias(libs.plugins.android.application)
            alias(libs.plugins.kotlin.android)
            alias(libs.plugins.ksp)
        }
        
            plugins {
            alias(libs.plugins.android.application) apply false
            alias(libs.plugins.kotlin.android) apply false
            alias(libs.plugins.ksp).apply(false)
        }
        
        ksp = "1.9.10-1.0.13"
        ksp = { id = "com.google.devtools.ksp", version.ref = "ksp" }

        // Old Version
        
      plugins {
          id 'com.android.application' version '7.3.1' apply false
          id 'com.android.library' version '7.3.1' apply false
          id 'org.jetbrains.kotlin.android' version '1.7.20' apply false
      }

      plugins {
          id 'com.android.application'
          id 'org.jetbrains.kotlin.android'
          id 'kotlin-android'
      }
      apply plugin: 'kotlin-kapt'

    implementation "androidx.room:room-runtime:2.5.0"
    kapt "androidx.room:room-compiler:2.5.0"

    //Kotlin Extensions and Coroutines support for Room
    implementation "androidx.room:room-ktx:2.5.0"
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.1"

      
          @Database(
          entities = [SearchData::class, TabsModel::class],
          version = BuildConfig.VERSION_CODE,
          exportSchema = true
      )
      abstract class MyAppDatabase : RoomDatabase() {
          abstract fun userDao(): UserDao
      
          abstract fun tabsDao(): TabsDao
      }

      @Dao
      interface UserDao {
          @Query("SELECT * FROM SearchData")
          fun getAllUsers(): LiveData<List<SearchData>>
      
          @Insert(onConflict = OnConflictStrategy.REPLACE)
          suspend fun insertUser(user: SearchData)
      
          @Update
          suspend fun updateUser(user: SearchData)
      
          @Delete
          suspend fun deleteUser(user: SearchData)
      
          @Query("DELETE FROM SearchData")
          suspend fun deleteAll()
      }
      
      @Dao
      interface TabsDao {
          @Query("SELECT * FROM TabsModel")
          fun getAllTabs(): LiveData<List<TabsModel>>
      
          @Insert
          suspend fun insertTabs(tabs: TabsModel)
      
          @Update
          suspend fun updateTabs(tabs: TabsModel)
      
          @Delete
          suspend fun deleteTabs(tabs: TabsModel)
      
          @Query("SELECT * FROM TabsModel WHERE isSelected = 1")
          suspend fun getSelectedEntities(): TabsModel?
      
          @Query("UPDATE TabsModel SET isSelected = 0")
          suspend fun updateMyColumnToFalse()
      
          @Query("SELECT COUNT(*) FROM TabsModel")
          suspend fun getCount(): Int
      
          @Query("DELETE FROM TabsModel")
          suspend fun deleteAll()
      }

      @Entity
      data class SearchData(
          @PrimaryKey(autoGenerate = true) val id: Int = 0,
          val logo: String = "",
          val name: String = "",
          val link: String = ""
      )
      
      @Entity
      data class TabsModel(
          @PrimaryKey(autoGenerate = true) val id: Int = 0,
          var logo: String = "",
          var name: String = "",
          var link: String = "",
          var filepath: String = "",
          var isSelected: Boolean = false
      )

      private var myDatabaseInstance: MyAppDatabase? = null

      fun getDatabaseInstance(context: Activity): MyAppDatabase {
          if (myDatabaseInstance == null) {
              myDatabaseInstance = Room.databaseBuilder(
                  context.applicationContext, MyAppDatabase::class.java, "MyXDB"
              ).build()
          }
          return myDatabaseInstance as MyAppDatabase
      }

       GlobalScope.launch {
            getDatabaseInstance(this).tabsDao().apply {
               
            }
        }
                  

// get a all whatsapp number contacnts

   <uses-permission android:name="android.permission.READ_CONTACTS" />
   
    fun getWhatsAppContactDatas(contentResolver: ContentResolver): ArrayList<ContactData> {
        whatsappcontacts = ArrayList<ContactData>()
        val projection = arrayOf(
            ContactsContract.Data.DISPLAY_NAME,
            ContactsContract.CommonDataKinds.Phone.NUMBER
        )
    
        val selection = (
                ContactsContract.Data.MIMETYPE + " = ? AND " +
                        ContactsContract.CommonDataKinds.Phone.NUMBER + " LIKE ?"
                )
        val selectionArgs = arrayOf(
            "vnd.android.cursor.item/vnd.com.whatsapp.profile",
            "%@%"
        )
    
        val cursor = contentResolver.query(
            ContactsContract.Data.CONTENT_URI,
            projection,
            selection,
            selectionArgs,
            null
        )
    
        cursor?.use {
            val nameColumnIndex = it.getColumnIndex(ContactsContract.Data.DISPLAY_NAME)
            val numberColumnIndex = it.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER)
    
            while (cursor.moveToNext()) {
                name = cursor.getString(nameColumnIndex)
                val number1 = cursor.getString(numberColumnIndex)
                number = number1.replace("@s.whatsapp.net", "")
                s1 = ContactData(name.toString(), number.toString())
                whatsappcontacts.add(s1)
            }
        }
    
        cursor?.close()
    
        return whatsappcontacts
    }

//WebView Multiple Tabs Logic

    private fun encodeWebViewBundle(webView: WebView): String {
        val bundle = Bundle()
        webView.saveState(bundle)
        val parcel = Parcel.obtain()
        bundle.writeToParcel(parcel, 0)
        val bytes = parcel.marshall()
        parcel.recycle()
        return Base64.encodeToString(bytes, Base64.DEFAULT)
    }

    private fun decodeWebViewBundle(encodedBundle: String): Bundle {
        val bytes: ByteArray = Base64.decode(encodedBundle, Base64.DEFAULT)
        val parcel = Parcel.obtain()
        parcel.unmarshall(bytes, 0, bytes.size)
        parcel.setDataPosition(0)
        return parcel.readBundle(javaClass.classLoader)!!
    }

    @OptIn(DelicateCoroutinesApi::class)
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
           item.bundel = encodeWebViewBundle(bind.webView)
           ("Item: ").log()
    }

    webView.restoreState(decodeWebViewBundle(item.bundel))

    @SuppressLint("SetJavaScriptEnabled")
    fun WebView.allsettings() {
        this.settings.javaScriptEnabled = true
        this.settings.domStorageEnabled = false
        this.setNetworkAvailable(true)
        this.isSaveEnabled = true
        this.settings.javaScriptCanOpenWindowsAutomatically = true
        this.settings.loadsImagesAutomatically = true;
        this.setLayerType(View.LAYER_TYPE_HARDWARE, null)
        this.webChromeClient = WebChromeClient()
        this.webViewClient = WebViewClient()
    }

// Current Location with broadcast 


    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

      <receiver
                  android:name=".Reciver.LocationReceiver"
                  android:enabled="true"
                  android:exported="true">
                  <intent-filter>
                      <action android:name="android.location.PROVIDERS_CHANGED" />

                  </intent-filter>
              </receiver>
              
      class LocationReceiver(var onClick: (String) -> Unit) : BroadcastReceiver() {
          private var fusedLocationProviderClient: FusedLocationProviderClient? = null
          private var locationCallback: LocationCallback? = null

          @SuppressLint("MissingPermission")
          override fun onReceive(context: Context, intent: Intent) {
              val action: String? = intent.action
              if (action != null && action == LocationManager.PROVIDERS_CHANGED_ACTION) {

                  val locationManager =
                      context.getSystemService(Context.LOCATION_SERVICE) as LocationManager
                  val isLocationEnabled = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER)
                  val networkEnabled = locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER)


                  if (isLocationEnabled && networkEnabled) {
                      ("Location Start").log()
                      fusedLocationProviderClient =
                          LocationServices.getFusedLocationProviderClient(context)
                      locationCallback = object : LocationCallback() {
                          override fun onLocationResult(locationResult: LocationResult) {
                              onClick("On")
                              ("Location Update: ${locationResult.lastLocation?.latitude}").log()
                          }
                      }
                      fusedLocationProviderClient!!.requestLocationUpdates(
                          getLocationRequest()!!,
                          locationCallback as LocationCallback,
                          null
                      )
                  } else {
                      ("Location Off").log()
                      if (fusedLocationProviderClient != null) {
                          onClick("off")
                          fusedLocationProviderClient!!.removeLocationUpdates(locationCallback!!)
                      }
                  }
              }
          }

          private fun getLocationRequest(): LocationRequest? {
              val locationRequest = LocationRequest()
              locationRequest.interval = 1000 // 10 seconds
              locationRequest.priority = LocationRequest.PRIORITY_HIGH_ACCURACY
              locationRequest.numUpdates = 1
              return locationRequest
          }
     }
      
      class LocationAct : BaseAct<LocatonLayoutBinding>() {
          private var locationReceiver: LocationReceiver? = null

          override fun initView() {
              activityLoc = this
              fetchLastLocation()

              ("OnCreate").log()

              locationReceiver = LocationReceiver {

              }

              val intentFilter = IntentFilter(LocationManager.PROVIDERS_CHANGED_ACTION)
              registerReceiver(locationReceiver, intentFilter)

          }

          override fun onDestroy() {
              super.onDestroy()
              unregisterReceiver(locationReceiver)
          }

          private fun fetchLastLocation() {
              if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                  if (checkSelfPermission(ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED && checkSelfPermission(
                          ACCESS_COARSE_LOCATION
                      ) != PackageManager.PERMISSION_GRANTED
                  ) {
                      showPermissionAlert()
                      return
                  }
              }
              fusedLocationProviderClient!!.lastLocation
                  .addOnSuccessListener(
                      this
                  ) { location ->
                      if (location != null) {
                          ("LAST LOCATION: ${location.latitude}").log()

                      } else {
                          ("location is Null").log()
                      }
                  }
          }

          override fun onRequestPermissionsResult(
              requestCode: Int,
              permissions: Array<out String>,
              grantResults: IntArray
          ) {
              super.onRequestPermissionsResult(requestCode, permissions, grantResults)
              when (requestCode) {
                  123 -> {
                      // If request is cancelled, the result arrays are empty.
                      if (grantResults[0] == PackageManager.PERMISSION_DENIED) {
                          // permission was denied, show alert to explain permission
                          showPermissionAlert()
                      } else {
                          //permission is granted now start a background service
                          if (ActivityCompat.checkSelfPermission(
                                  applicationContext,
                                  ACCESS_FINE_LOCATION
                              ) === PackageManager.PERMISSION_GRANTED
                              && ActivityCompat.checkSelfPermission(
                                  applicationContext,
                                  ACCESS_COARSE_LOCATION
                              ) === PackageManager.PERMISSION_GRANTED
                          ) {
                              fetchLastLocation()
                          }
                      }
                  }
              }
          }

          private fun showPermissionAlert() {
              if (ActivityCompat.checkSelfPermission(
                      this@LocationAct,
                      ACCESS_FINE_LOCATION
                  ) !== PackageManager.PERMISSION_GRANTED
                  && ActivityCompat.checkSelfPermission(
                      this@LocationAct,
                      ACCESS_COARSE_LOCATION
                  ) !== PackageManager.PERMISSION_GRANTED
              ) {
                  ActivityCompat.requestPermissions(
                      this@LocationAct,
                      arrayOf<String>(
                          ACCESS_COARSE_LOCATION,
                          ACCESS_FINE_LOCATION
                      ),
                      123
                  )
              }
          }

          override fun getActivityBinding(layoutInflater: LayoutInflater) =
              LocatonLayoutBinding.inflate(layoutInflater)
      }



// Counter Up

      //Application Class
      lateinit var timerCounter: CountUpTimer

       timerCounter = object : CountUpTimer(1000) {
            override fun onTick(elapsedTime: Long) {
                elapsedTimeMM += elapsedTime
                time = formatDurationTv(elapsedTimeMM)

                val rxBytes = TrafficStats.getTotalRxBytes()
                val txBytes = TrafficStats.getTotalTxBytes()

                val totalBytes = TrafficStats.getTotalRxBytes() - rxBytes
                val bytesPerSecond: Long = TrafficStats.getTotalTxBytes() - txBytes

                val dkbps = bytesPerSecond * 8 / 1024
                val ukbps = totalBytes * 8 / 1024

                uploadspeed = "$ukbps kb/s"
                downloadspeed = "$dkbps kb/s"

                ("Elaps: $time | Spped:  $dkbps | $ukbps").log()
            }
        }
 
         abstract class CountUpTimer(private val interval: Long) {
             var previousTime: Long

             companion object {
                 var runnable: Runnable? = null
                 var handler: Handler? = null
             }

             init {
                 previousTime = System.currentTimeMillis()
                 handler = Handler(Looper.getMainLooper())

                 runnable = object : Runnable {
                     override fun run() {
                         val currentTime = System.currentTimeMillis()
                         val elapsedTime = currentTime - previousTime
                         previousTime = currentTime
                         onTick(elapsedTime)
                         handler!!.postDelayed(this, interval)
                     }
                 }
             }

             fun stop() {
                 isTimerRunning = false
              
                 handler!!.removeCallbacks(runnable!!)
             }

             fun start() {
                 isTimerRunning = true
                 previousTime = System.currentTimeMillis()
                 handler!!.post(runnable!!)
             }

             abstract fun onTick(elapsedTime: Long)
         }

// .gitignore File

         # These are some examples of commonly ignored file patterns.
         # You should customize this list as applicable to your project.
         # Learn more about .gitignore:
         #     https://www.atlassian.com/git/tutorials/saving-changes/gitignore

         # Node artifact files
         node_modules/
         dist/

         # Compiled Java class files
         *.class

         # Compiled Python bytecode
         *.py[cod]

         # Log files
         *.log

         # Package files
         *.jar

         # Maven
         target/
         dist/

         # JetBrains IDE
         .idea/

         # Unit test reports
         TEST*.xml

         # Generated by MacOS
         .DS_Store

         # Generated by Windows
         Thumbs.db

         # Applications
         *.app
         *.exe
         *.war

         # Large media files
         *.mp4
         *.tiff
         *.avi
         *.flv
         *.mov
         *.wmv


         #env file
         .env

         #certificate file
         *.crt

// onActivity Result Updated

         private val luanch = registerForActivityResult(VpnContent()) {
              if (it) {
                  startService(Intent(this, VpnConnected::class.java))
              }
          }

          class VpnContent : ActivityResultContract<Intent, Boolean>() {
              override fun createIntent(context: Context, input: Intent): Intent {
                  return input
              }

              override fun parseResult(resultCode: Int, intent: Intent?): Boolean {
                  return resultCode == RESULT_OK
              }
          }
          
            luanch.launch(instnace)

// get WhatsApp Status android 11+

      <queries>
            <package android:name="com.whatsapp" />
            <package android:name="com.whatsapp.w4b" />
        </queries>

     private void grantAndroid11StorageAccessPermission() {
            Intent intent = null;
            StorageManager storageManager = (StorageManager) getSystemService(STORAGE_SERVICE);

            String whatsappfolderdir;
            if (new File(
                    Environment.getExternalStorageDirectory()
                            .toString() + "/Android/media/com.whatsapp/WhatsApp/Media/.Statuses"
            ).isDirectory()
            ) {
                whatsappfolderdir = "Android%2Fmedia%2Fcom.whatsapp%2FWhatsApp%2FMedia%2F.Statuses";
            } else {
                whatsappfolderdir = "WhatsApp%2FMedia%2F.Statuses";
            }

            if (Build.VERSION.SDK_INT >= 29) {
                intent = storageManager.getPrimaryStorageVolume().createOpenDocumentTreeIntent();
                String scheme = (intent.getParcelableExtra("android.provider.extra.INITIAL_URI").toString()
                        .replace("/root/", "/document/"));

                String stringBuilder = "$scheme%3A$whatsappfolderdir";
                intent.putExtra("android.provider.extra.INITIAL_URI", Uri.parse(stringBuilder));
            } else {
                intent = new Intent("android.intent.action.OPEN_DOCUMENT_TREE");
                intent.putExtra("android.provider.extra.INITIAL_URI", Uri.parse(whatsappfolderdir));
            }
            intent.addFlags(Intent.FLAG_GRANT_WRITE_URI_PERMISSION);
            intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
            intent.addFlags(Intent.FLAG_GRANT_PREFIX_URI_PERMISSION);
            intent.addFlags(Intent.FLAG_GRANT_PERSISTABLE_URI_PERMISSION);
            startActivityForResult(intent, 10);
            return;
        }


        @Override
        protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (requestCode == 10 && resultCode == -1) {
                Uri uri = data.getData();
                try {
                    getContentResolver().takePersistableUriPermission(uri, Intent.FLAG_GRANT_READ_URI_PERMISSION);
                } catch (Exception e) {
                    e.printStackTrace();
                }

                ApplicationClass.Companion.putString("whatsapp", uri.toString());
                setUpRv();
            }
        }
        
               private void setUpRv() {
            ArrayList<DocumentFile> allFiles = new ArrayList<>();
            DocumentFile[] allFileswhatsapp = getDataFromWhatsAppFolder();
            allFiles.addAll(Arrays.asList(allFileswhatsapp));
            log(String.valueOf(allFiles.size()));
        }

        private DocumentFile[] getDataFromWhatsAppFolder() {
            DocumentFile fromTreeUri = DocumentFile.fromTreeUri(
                    getApplicationContext(),
                    Uri.parse(ApplicationClass.Companion.getString("whatsapp"))
            );
            return fromTreeUri.listFiles();
        }


// Set a Proxy 

       private fun setProxy() {
              if (WebViewFeature.isFeatureSupported(WebViewFeature.PROXY_OVERRIDE)) {
                  val proxyConfig = ProxyConfig.Builder().addProxyRule("$host:$port")
                      .addDirect() //when proxy is not working, use direct connect, maybe?
                      .build()
                  ProxyController.getInstance().setProxyOverride(proxyConfig, {
                      ("proxy execute").log()
                      bind.webView.webViewClient = LightningWebClient(this@WebAct, bind.tital) {
                          ("Proxy Execute!!").log()
                          
                      }
                      bind.webView.webChromeClient = LightningChromeClient(this@WebAct)
                      webSettings()
                      bind.webView.loadUrl(
                          webUrl,
                          getAllHeaders()
                      )
                  }) { ("WebView proxy").log() }
              } else {
                  ("Proxy is not Set").log()
              }
          }

    //proxy lib
    final def netCipherVersion = '2.0.0-alpha1'
    implementation "info.guardianproject.netcipher:netcipher:2.1.0"
    implementation "info.guardianproject.netcipher:netcipher-webkit:$netCipherVersion"
    
    implementation 'androidx.webkit:webkit:1.6.1'
    implementation 'androidx.browser:browser:1.5.0'
 
       fun openCustomTab(activity: Activity, link: String) {
          val builder: CustomTabsIntent.Builder = CustomTabsIntent.Builder()
          builder.setToolbarColor(Color.BLACK)
          builder.setShowTitle(true)
          val customTabsIntent = builder.build()
          customTabsIntent.launchUrl(activity, Uri.parse(link))
      }
    
    gradlePluginPortal()
        maven {
            url 'https://jitpack.io'
        }
        
      WebkitProxy.setProxy(
            "",
            applicationContext,
            null,
            host,
            port!!.toInt()
        )
        

// WebView Config for robust work

          fun getAllHeaders(): HashMap<String, String> {
              val headers: HashMap<String, String> = HashMap()
              headers["Cache-Control"] = "max-age=3600"
              headers["User-Agent"] =
                  "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36"
              headers["Accept-Encoding"] = "br"

              return headers
          }


          fun getjs(activity: Activity, str: String?, canAdd: Boolean = false): String {
              val open: InputStream
              try {
                  open = activity.assets.open(str.toString())
                  val bArr = ByteArray(open.available())
                  open.read(bArr)
                  val str2 = String(bArr)
                  return try {
                      open.close()
                      str2
          //            val final = str2.replaceFirstChar { "" }
          //            if (canAdd)
          //                decodeB64("$final==")
          //            else
          //                decodeB64(final)
                  } catch (unused: IOException) {
                      return str2
          //            decodeB64(str2)
                  }
              } catch (unused2: IOException) {
                  return ""
              } catch (th: Throwable) {
                  th.addSuppressed(th)
              }
              return ""
          }

            webView.settings.javaScriptEnabled = true
            webView.settings.domStorageEnabled = false
            webView.setNetworkAvailable(true)
            webView.isSaveEnabled = true
            webView.settings.javaScriptCanOpenWindowsAutomatically = true
            webView.settings.loadsImagesAutomatically = true;
            webView.setLayerType(View.LAYER_TYPE_HARDWARE, null)
            
            
// ApplicationClass

                  implementation "androidx.security:security-crypto:1.1.0-alpha06"
         
                 class ApplicationClass : Application() {
           companion object {
               var sharedPreferences: SharedPreferences? = null
       
               fun putString(key: String, value: String) {
                   sharedPreferences!!.edit()?.putString(key, value)!!.apply()
               }
       
               fun putBoolean(key: String, value: Boolean) {
                   sharedPreferences!!.edit()?.putBoolean(key, value)!!.apply()
               }
       
               fun getString(key: String) =
                   sharedPreferences?.getString(key, "")
       
               fun getBoolean(key: String) = sharedPreferences?.getBoolean(key, false)
           }
       
           override fun onCreate() {
               super.onCreate()
       
               val masterKey = MasterKey.Builder(this)
                   .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
                   .build()
       
               sharedPreferences = EncryptedSharedPreferences.create(
                   this,
                   "myDemo",
                   masterKey,
                   EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
                   EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
               )
       
           }
       }

// Set Rington

          if (Arrays.stream((direct.listFiles())).anyMatch(it -> it.getName().startsWith(Title))) {

                for (File f : Objects.requireNonNull(direct.listFiles())) {

                    if (f.getName().startsWith(Title)) {
                        name = f.getName();
                        path = f.getPath();
                    }
                }

                ContentValues values = new ContentValues();
                values.put(MediaStore.Audio.Media.IS_RINGTONE, true);

                try {
                    getExternalMp3Files(name);
                    File file = new File(audioFilePath);

                    Uri uri;
                    if (file.exists()) {
                        Uri contentUriForPath = MediaStore.Audio.Media.getContentUriForPath(file.getAbsolutePath());
                        String[] strArr = {file.getAbsolutePath()};

                        Uri contentUriForPath2 = MediaStore.Audio.Media.getContentUriForPath(file.getAbsolutePath());

                        Cursor query = getContentResolver().query(contentUriForPath2, new String[]{"_id"}, "_data=? ", strArr, (String) null);

                        if (query == null || !query.moveToFirst()) {
                            uri = getContentResolver().insert(contentUriForPath, values);
                            Log.d("TAG", "onCreate: Ringtone Set1 ========= " + uri);

                        } else {
                            @SuppressLint("Range")
     //                        int valueOf = query.getInt(query.getColumnIndex("_id"));
                            Integer valueOf = Integer.valueOf(query.getInt(query.getColumnIndexOrThrow(MediaStore.Audio.Media._ID)));
                            uri = Uri.withAppendedPath(contentUriForPath, "" + valueOf);
                            Log.d("TAG", "onCreate: Ringtone Set2 ========= " + uri);

                        }
                        RingtoneManager.setActualDefaultRingtoneUri(this, RingtoneManager.TYPE_RINGTONE, uri);
                        Toast.makeText(this, "Ringtone Successfully Set", Toast.LENGTH_SHORT).show();
                    }
                } catch (Exception e2) {
                    Toast.makeText(this, "Unable To Set Ringtone", Toast.LENGTH_SHORT).show();
                }
                Log.d("TAG", "onCreate: Ringtone Set2 =========  Already Downloaded");

            } else {
                Log.d("TAG", "is Not Match");
                if (Settings.System.canWrite(this)) {
                    DownloadFile asyncTask = new DownloadFile();
                    asyncTask.execute();
                } else {
                    openAndroidPermissionsMenu();

                }
            }

                      private void openAndroidPermissionsMenu() {
                          Intent intent = new Intent(Settings.ACTION_MANAGE_WRITE_SETTINGS);
                          intent.setData(Uri.parse("package:" + this.getPackageName()));
                          startActivity(intent);
                      }

                         public Void getExternalMp3Files(String name) {

                 String selection = MediaStore.Audio.Media.DISPLAY_NAME + "=?";
                 String[] selectionArgs = new String[]{name};

         // Get the columns to retrieve
                 String[] projection = new String[]{
                         MediaStore.Audio.Media._ID,     // ID of the audio file
                         MediaStore.Audio.Media.DATA     // File path of the audio file
                 };
         // Query the media store for the audio file
                 Cursor cursor = getContentResolver().query(
                         MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, // The content URI for audio media files
                         projection,                                 // The columns to retrieve
                         selection,                                  // The selection criteria
                         selectionArgs,                              // The value of the selection criteria
                         null                                        // The sort order for the results
                 );
         // Check if the cursor returned any results
                 if (cursor != null && cursor.moveToFirst()) {
                     // Get the ID and file path of the audio file from the cursor

                     audioFileId = cursor.getLong(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media._ID));
                     audioFilePath = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.DATA));

                     // Log the ID and file path of the audio file
                     Log.d("TAG", "Audio file ID: " + audioFileId);
                     Log.d("TAG", "Audio file path: " + audioFilePath);
                     // Close the cursor
                     cursor.close();
                 }
                 return null;
             }
             
                 <uses-permission android:name="android.permission.WRITE_SETTINGS"
        tools:ignore="ProtectedPermissions" />
             

// RecyclerView Pagination

         rvmedia.addOnScrollListener(object : RecyclerView.OnScrollListener() {
                        override fun onScrolled(rv: RecyclerView, dx: Int, dy: Int) {
                            super.onScrolled(rv, dx, dy)
                            pb.gon()
                            if (!pb.isVisible && (rv.layoutManager as GridLayoutManager).findLastVisibleItemPosition() == rv.adapter!!.itemCount - 1) {
                                pb.visible()
                                loadAllPostsData()
                            }
                        }
                    })


// Download From Server with Progressbar

           <RelativeLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="vertical">

                    <ProgressBar
                        android:id="@+id/progressBar"
                        style="?android:attr/progressBarStyleHorizontal"
                        android:layout_width="150dp"
                        android:layout_height="150dp"
                        android:max="100"
                        android:progress="0"
                        android:progressDrawable="@drawable/round_shape"
                        android:visibility="visible" />

                    <TextView
                        android:id="@+id/tvnumber"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_centerInParent="true"
                        android:elevation="@dimen/_1sdp"
                        android:fontFamily="@font/publicasans"
                        android:gravity="center"
                        android:text="0%"
                        android:textColor="#EA214B"
                        android:textSize="@dimen/_18sdp" />

                </RelativeLayout>

        <layer-list xmlns:android="http://schemas.android.com/apk/res/android"
            android:fromDegrees="120"
            android:pivotX="50%"
            android:pivotY="50%"
            android:toDegrees="140">
            <item android:id="@android:id/background">
                <shape
                    android:angle="0"
                    android:innerRadiusRatio="3"
                    android:shape="ring"
                    android:thicknessRatio="18.0"
                    android:type="sweep"
                    android:useLevel="false">
                    <solid android:color="#D2D2D2" />

                </shape>

            </item>
            <item android:id="@android:id/progress">
                <rotate
                    android:fromDegrees="120"
                    android:toDegrees="120">
                    <shape
                        android:angle="0"
                        android:innerRadiusRatio="3"
                        android:shape="ring"
                        android:thicknessRatio="18.0"
                        android:type="sweep">
                        <gradient
                            android:centerColor="#EA214B"
                            android:endColor="#FBA24D"
                            android:startColor="#5D3ED1" />

                    </shape>
                </rotate>
            </item>
        </layer-list>

        class MyProgressDialog(var activity: Activity, var videoUrl: String, nametitle: String, from: String, onClick: (String) -> Unit) {

            var bind = ProgressDialogBinding.inflate(activity.layoutInflater)
            var dialog = Dialog(activity)

            init {
                dialog.setContentView(bind.root)
                dialog.window!!.setLayout(WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams.MATCH_PARENT)
                dialog.window!!.setBackgroundDrawableResource(android.R.color.transparent)
                dialog.setCancelable(false)
                dialog.show()

                ("Name: $nametitle | From: $from | Video: $videoUrl").log()

                onBackground {
                    try {
                        // create a URL object for the video
                        val url = URL(videoUrl)

                        // create a connection to the server
                        val connection: HttpURLConnection = url.openConnection() as HttpURLConnection

                        // set the request method to GET
                        connection.requestMethod = "GET"

                        // connect to the server
                        connection.connect()

                        // get the content length of the video
                        val contentLength: Int = connection.contentLength

                        // create a stream to read the data from the server
                        val input: InputStream = connection.inputStream

                        // create a stream to write the data to a file

                        val filePath: String = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS)
                        val output = FileOutputStream(File(filePath))

                        //
                        // set the buffer size
                        val buffer = ByteArray(1024)
                        var bytesRead: Int

                        // download the video and update the progress bar

                        while (input.read(buffer).also { bytesRead = it } != -1) {
                            output.write(buffer, 0, bytesRead)
                            val progresss = ((output.channel.size() * 100 / contentLength).toInt())
                            ("$progresss").log()

        //                    if (progresss == 98)
        //                        break
                            activity.runOnUiThread {
                                bind.progressBar.progress = progresss
                                bind.tvnumber.text = "$progresss%"
                            }
                        }

                        ("NOw").log()
                        activity.runOnUiThread {
                            bind.progressBar.progress = 100
                            bind.tvnumber.text = "100%"
                            onClick.invoke(filePath)
                            dismiss()
                        }
                        // close the input and output streams
                        output.close()
                        input.close()
                    } catch (e: Exception) {
                        e.printStackTrace()
                    }
                }

            }

            fun dismiss() = dialog.dismiss()
        }
        
        

// Funcations

        <queries>
            <package android:name="com.whatsapp" />
            <package android:name="com.whatsapp.w4b" />
            <package android:name="com.facebook.katana" />
            <package android:name="com.instagram.android" />
            <package android:name="com.twitter.android" />
        </queries>



        public void shareImageWhatsApp(String type, String file, String packageName, String apkname) {

            Intent share = new Intent(Intent.ACTION_SEND);
            share.setType("image/" + type);

            share.putExtra(Intent.EXTRA_STREAM,
                    FileProvider.getUriForFile(this, getPackageName() + ".provider",
                            new File(file), file.toLowerCase()));
            share.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);

            if (appInstalledOrNot(packageName)) {
                share.setPackage(packageName);
                startActivity(Intent.createChooser(share, "Share Image"));

            } else {
                tos("Please Install " + apkname);
            }

        }

           fun setAppLocale(activity: Context, languageCode: String) {
            val locale = Locale(languageCode)
            Locale.setDefault(locale)
            val configuration = Configuration()
            configuration.setLocale(locale)
            activity.resources.updateConfiguration(configuration, activity.resources.displayMetrics)
        }
        
        fun bitmapToBase64(bitmap: Bitmap): String {
            val byteArrayOutputStream = ByteArrayOutputStream()
            bitmap.compress(Bitmap.CompressFormat.PNG, 100, byteArrayOutputStream)
            val byteArray = byteArrayOutputStream.toByteArray()
            return Base64.encodeToString(byteArray, Base64.DEFAULT)
        }
        
        fun base64ToBitmap(base64String: String): Bitmap {
            val decodedBytes = Base64.decode(base64String, Base64.DEFAULT)
            return BitmapFactory.decodeByteArray(decodedBytes, 0, decodedBytes.size)
        }

        private boolean appInstalledOrNot(String uri) {
            PackageManager pm = getPackageManager();
            try {
                pm.getPackageInfo(uri, PackageManager.GET_ACTIVITIES);
                return true;
            } catch (PackageManager.NameNotFoundException e) {
                print(e);
            }

            return false;
        }
        
          fun instagramformatNumber(number: Int): String {
            val suffixes = listOf("", "k", "M", "B", "T", "P", "E")
            var value = number.toFloat()
            var suffixIndex = 0
            while (value >= 1000 && suffixIndex < suffixes.size - 1) {
                value /= 1000
                suffixIndex++
            }
            val formattedValue = String.format("%.0f", value)
            val suffix = suffixes[suffixIndex]
            return "$formattedValue$suffix"
        }

// Download Manager File

        public class DownloadFileMain {
            public static DownloadManager downloadManager;
            public static long downloadID;

            public static void startDownloading(final Context context, String url, String title, String ext) {
                try {
                    String cutTitle = "";
        //            DownloadBroadcastReceiver.isFirstTimeDownload = false;
                    cutTitle = title;

                    String characterFilter = "[^\\p{L}\\p{M}\\p{N}\\p{P}\\p{Z}\\p{Cf}\\p{Cs}\\s]";
                    cutTitle = cutTitle.replaceAll(characterFilter, "");
                    cutTitle = cutTitle.replaceAll("['+.^:,#\"]", "");
                    cutTitle = cutTitle.replace(" ", "-").replace("!", "").replace(":", "") + ext;
                    if (cutTitle.length() > 100) {
                        cutTitle = cutTitle.substring(0, 100) + ext;
                    }

                    downloadManager = (DownloadManager) context.getSystemService(Context.DOWNLOAD_SERVICE);

                    url = url.replace("\"", "");

                    DownloadManager.Request request = new DownloadManager.Request(Uri.parse(url));
                    request.setTitle(title);
                    request.setDescription("Downloading..");
                    request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);

                    try {
                        File file_v = new File(Environment.getExternalStoragePublicDirectory(DIRECTORY_DOWNLOADS) + "/X_Downloader/");
                        if (!file_v.exists()) {
                            log("Make Dir");
                            file_v.mkdir();
                        }

                    } catch (IllegalStateException e) {
                        e.printStackTrace();
                    }

                    switch (ext) {
                        case ".png":
                        case ".jpg":
                        case ".gif":
                        case ".jpeg":
                        case ".mp4":
                        case ".webm":
                        case ".mp3":
                        case ".m4a":
                        case ".wav":
                            request.setDestinationInExternalPublicDir(DIRECTORY_DOWNLOADS, "/X_Downloader/" + cutTitle);
                            break;
                    }

                    request.allowScanningByMediaScanner();
                    downloadID = downloadManager.enqueue(request);
                    Log.e("downloadFileName", cutTitle);

                    expandNotificationBar(context);
                    ((Activity) context).runOnUiThread(() -> Toast.makeText(context, "Downloading Start", Toast.LENGTH_LONG).show());


                } catch (Exception e) {
                    e.printStackTrace();
                    try {
                        ((Activity) context).runOnUiThread(() -> Toast.makeText(context, "Error occurred!", Toast.LENGTH_LONG).show());
                    } catch (Exception e1) {
                        e1.printStackTrace();
                    }
                }
            }

            static void expandNotificationBar(Context context) {
                @SuppressLint("WrongConstant") Object service = context.getSystemService("statusbar");
                if (service != null) {
                    try {
                        Class<?> statusBarManager = Class.forName("android.app.StatusBarManager");
                        Method expandMethod = statusBarManager.getMethod("expandNotificationsPanel");
                        expandMethod.invoke(service);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }

// Bottom Sheet Dialog

        class UpdateAppDialog(var activity: Activity) {
            var dialog: BottomSheetDialog = BottomSheetDialog(activity)
            var binding: UpdateDialogBinding = UpdateDialogBinding.inflate(LayoutInflater.from(activity))

            init {
                dialog.setContentView(binding.root)
                dialog.setCancelable(false)
                dialog.window?.setBackgroundDrawableResource(android.R.color.transparent)
                dialog.window!!.findViewById<View>(com.google.android.material.R.id.design_bottom_sheet).setBackgroundResource(R.color.transparent)
                binding.updatebtn.setOnClickListener {
                    dialog.dismiss()
                    rateUs();
                }
                dialog.show()
            }

            private fun rateUs() {
                try {
                    val marketUri = Uri.parse("market://details?id=" + activity.packageName)
                    val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
                    activity.startActivity(marketIntent)
                } catch (e: Exception) {
                    e.print()
                    val marketUri = Uri.parse("https://play.google.com/store/apps/details?id=" + activity.packageName)
                    val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
                    activity.startActivity(marketIntent)
                }
            }
        }

        <?xml version="1.0" encoding="utf-8"?>
        <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom"
            android:background="@android:color/transparent"
            android:clipChildren="false"
            android:clipToPadding="false">

            <com.google.android.material.card.MaterialCardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardCornerRadius="@dimen/_16sdp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="@dimen/_minus24sdp"
                    android:background="@color/white"
                    android:orientation="vertical"
                    android:paddingStart="@dimen/_14sdp"
                    android:paddingTop="@dimen/_14sdp"
                    android:clipToPadding="false"
                    android:clipChildren="false"
                    android:paddingEnd="@dimen/_14sdp"
                    android:paddingBottom="@dimen/_50sdp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center"
                        android:gravity="center"
                        android:text="Update Now"
                        android:textColor="#FF002E"
                        android:textSize="@dimen/_16sdp" />

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center"
                        android:layout_marginTop="@dimen/_10sdp"
                        android:gravity="center"
                        android:text="New version available"
                        android:textColor="#6A6A6A"
                        android:textSize="@dimen/_12sdp"
                        android:textStyle="bold" />

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center"
                        android:layout_marginStart="@dimen/_10sdp"
                        android:layout_marginTop="@dimen/_10sdp"
                        android:layout_marginEnd="@dimen/_10sdp"
                        android:gravity="center"
                        android:text="recommends that \nyou update to the latest version. You can’t \nkeep using this app while downloading the \nupdate        version from play store. Please\n update it."
                        android:textColor="#6A6A6A"
                        android:textSize="@dimen/_10sdp" />

                    <TextView
                        android:id="@+id/updatebtn"
                        android:layout_width="@dimen/_150sdp"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center"
                        android:layout_marginTop="@dimen/_15sdp"
                        android:elevation="@dimen/_3sdp"
                        android:gravity="center"
                        android:padding="@dimen/_8sdp"
                        android:text="Update Now"
                        android:textColor="@color/white"
                        android:textSize="@dimen/_14sdp" />

                </LinearLayout>
            </com.google.android.material.card.MaterialCardView>
        </RelativeLayout>

// const class

    fun createLog(txt: String) {
        try {
            val file = File(
                Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS),
                "proxylog.txt"
            )
            val writer = FileWriter(file, true)
            writer.write(txt)
            writer.close()
        } catch (e: IOException) {
            e.printStackTrace()
        }
    }
    
        fun longtoDataTime(): String {
        val currentTimeMillis = System.currentTimeMillis()
        val date = Date(currentTimeMillis)
        val dateFormat: DateFormat = DateFormat.getDateTimeInstance()
        val formattedDateTime: String = dateFormat.format(date)
      //    ("Current date and time: $formattedDateTime").log()
        return formattedDateTime
      }


    fun callapi() {


    interface Api {
     @GET("{filename}")
        fun downloadPdf(@Path("filename") fullUrl: String): Call<ResponseBody?>?
        
    @GET(".")
    fun getInstaStory(
            @Header("Cookie") Cookie: String,
            @Header("User-Agent") userAgent: String)
            : Call<ResClass>

     }
    
     interface onResponce {
         fun <T> onSuccess(res: T)
         fun onError(error: String)
     }
     
     fun callApi(videoid: String, onResponce: onResponce) {
         val interceptor by lazy { HttpLoggingInterceptor() } //todo remove this line in production
         interceptor.setLevel(HttpLoggingInterceptor.Level.BODY) //todo remove this line in production
     
         val okClient by lazy {
             OkHttpClient.Builder()
                 .addInterceptor(Interceptor { chain: Interceptor.Chain ->
                     val originalRequest: Request = chain.request()
                     val requestBuilder: Request.Builder = originalRequest.newBuilder()
                         .addHeader("Cache-Control", "no-cache")
                         .method(originalRequest.method, originalRequest.body)
                     val request: Request = requestBuilder.build()
                     chain.proceed(request)
                 })
                 .addInterceptor(interceptor) //todo remove this line in production
                 .connectTimeout(10, TimeUnit.SECONDS)
                 .readTimeout(10, TimeUnit.SECONDS)
                 .build()
         }
     
     
         val retrofit by lazy {
             Retrofit.Builder().baseUrl("https://youtube-downloader.deno.dev/")
                 .addConverterFactory(GsonConverterFactory.create())
                 .client(okClient)
                 .build()
         }
         val myApi by lazy {
             retrofit.create(Api::class.java)
         }
         myApi.getYTdatabyID(videoid).enqueue(object : Callback<YTube> {
             override fun onResponse(call: retrofit2.Call<YTube>, response: Response<YTube>) {
                 if (response.code() == 200) {
                     onResponce.onSuccess(response.body()!!)
                 } else {
                     onResponce.onError("some thing went wrong: ${response.code()}")
                 }
             }
     
             override fun onFailure(call: retrofit2.Call<YTube>, t: Throwable) {
                 onResponce.onError(t.localizedMessage!!)
             }
         })
     }


        <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
        <uses-permission android:name="android.permission.USE_EXACT_ALARM" />
        <uses-permission android:name="com.android.alarm.permission.SET_ALARM" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM" />

    @SuppressLint("ServiceCast")
    fun startAlert(activity: Context) {
        val second = 8
        (activity.getSystemService(ALARM_SERVICE) as AlarmManager?)!!.set(
            AlarmManager.RTC_WAKEUP,
            System.currentTimeMillis() + second * 1000L,
            PendingIntent.getBroadcast(
                activity,
                111,
                Intent(activity, MyBroadcastReceiver::class.java),
                flags
            )
        )
        ("Alarm set in $second seconds").log()
    }


    fun stopAlertservice(activity: Context) {
        val alarmManager = activity.getSystemService(ALARM_SERVICE) as AlarmManager
        val pendingIntent = PendingIntent.getBroadcast(
            activity, 111, Intent(
                activity,
                MyBroadcastReceiver::class.java
            ), flags
        )
        alarmManager.cancel(pendingIntent)
        ("Alarm Stop").log()
    }

        val flags = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
            PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
        } else PendingIntent.FLAG_UPDATE_CURRENT


        fun hideSoftKeyBord(context: Activity) {
            val inputMethodManager: InputMethodManager? =
                context.getSystemService(INPUT_METHOD_SERVICE) as InputMethodManager?
            if (inputMethodManager != null && context.currentFocus != null) {
                inputMethodManager.hideSoftInputFromWindow(context.currentFocus!!.windowToken, 0)
            }
        }
        

        fun isMyServiceRunning(activity: AppEvent, serviceClass: Class<*>): Boolean {
            val manager = activity.getSystemService(AppCompatActivity.ACTIVITY_SERVICE) as ActivityManager
            for (service in manager.getRunningServices(Int.MAX_VALUE)) {
                if (serviceClass.name == service.service.className) {
                    return true
                }
            }
            return false
        }
        
                fun hasInternetConnect(activity: Activity): Boolean {
            var isWifiConnected = false
            var isMobileConnected = false
            val cm = activity.getSystemService(AppCompatActivity.CONNECTIVITY_SERVICE) as ConnectivityManager

            if (cm.defaultProxy != null) return false

            for (ni in cm.allNetworkInfo) {
                if (ni.typeName.equals("WIFI", ignoreCase = true)) if (ni.isConnected) isWifiConnected = true
                if (ni.typeName.equals("MOBILE", ignoreCase = true)) if (ni.isConnected) isMobileConnected = true
            }

            return isWifiConnected || isMobileConnected
        }

         fun rateUs(activity: Activity) {
            try {
                val marketUri = Uri.parse("market://details?id=" + activity.packageName)
                val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
                activity.startActivity(marketIntent)
            } catch (e: Exception) {
                e.print()
                val marketUri = Uri.parse("https://play.google.com/store/apps/details?id=" + activity.packageName)
                val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
                activity.startActivity(marketIntent)
            }
        }

        @SuppressLint("SimpleDateFormat")
        fun getOutputMediaFile(node: String): File? {
            val mediaStorageDir = File(
                Environment.getExternalStorageDirectory()
                    .absolutePath + SAVE_FOLDER_NAME_FOR_INSTA
            )

            if (!mediaStorageDir.exists()) {
                if (!mediaStorageDir.mkdirs()) {
                    return null
                }
            }
            // Create a media file name
            val mediaFile: File
            val mImageName = "${"x_video_" + (System.currentTimeMillis() / 1000)}.png"
            mediaFile = File(mediaStorageDir.path + File.separator + mImageName)
            return mediaFile
        }

        fun storeImage(image: Bitmap, node: String) {
            val pictureFile: File = getOutputMediaFile(node)!!
            try {
                val fos = FileOutputStream(pictureFile)
                image.compress(Bitmap.CompressFormat.PNG, 100, fos)
                fos.close()
            } catch (e: FileNotFoundException) {
                ("Error File Not Found: " + e.message).log()
            } catch (e: IOException) {
                ("Error accessing file: " + e.message).log()
            }
        }       


// Exit Dialog

        class ExitAppDialog(var activity: Context, var onClick: () -> Unit) {
            var dialog: Dialog = Dialog(activity)
            var binding: ExitDialogBinding = ExitDialogBinding.inflate(LayoutInflater.from(activity))

            init {
                dialog.setContentView(binding.root)
                dialog.setCancelable(false)
                dialog.window!!.setBackgroundDrawableResource(android.R.color.transparent)

                binding.cancel.setOnClickListener {
                    dialog.dismiss()
                }

                binding.ok.setOnClickListener {
                    dialog.dismiss()
                    onClick.invoke()
                }
                dialog.show()
            }
        }

// Package Add & Remove

    <uses-permission android:name="android.permission.BROADCAST_STICKY" />

    <uses-permission
        android:name="android.permission.BROADCAST_CLOSE_SYSTEM_DIALOGS"
        tools:ignore="ProtectedPermissions" />
        
        <receiver
                    android:name=".Service.PackageRec"
                    android:excludeFromRecents="true"
                    android:exported="false">
                    <intent-filter>
                        <action android:name="android.intent.action.PACKAGE_INSTALL" />
                        <action android:name="android.intent.action.PACKAGE_REMOVED" />
                    </intent-filter>

                </receiver>

        class PackageRec : BroadcastReceiver() {
            @RequiresApi(Build.VERSION_CODES.O)
            @SuppressLint("ShowToast", "RestrictedApi", "RemoteViewLayout")
            override fun onReceive(context: Context, intent: Intent) {
                (" --- Package Event onRecive --- ${context.packageName} | Class Name: ${PkgInstallAct::class.java.name} ").log()
                if (intent.action == Intent.ACTION_PACKAGE_ADDED)
                  
            }

            fun OnCreate(context: Context) {
                ("Package Added onCreate").log()

                val filter = IntentFilter()
                filter.addAction(Intent.ACTION_PACKAGE_ADDED)
                filter.addAction(Intent.ACTION_PACKAGE_REMOVED)
                filter.addDataScheme("package")
                context.registerReceiver(this, filter)
            }

            fun OnDestroy(context: Context?) {
                ("onDestroy a Package").log()
                context?.unregisterReceiver(this)
            }
        }
        

// get battery leval

        public static int getBatteryTemp(Context context) {
        try {
            Intent intent = context.getApplicationContext().registerReceiver(null, new IntentFilter("android.intent.action.BATTERY_CHANGED"));
            int temperature = intent.getIntExtra(BatteryManager.EXTRA_TEMPERATURE, -1);
            if (temperature != -1) {
                return getTempC(temperature);
            }
        } catch (Exception e) {
        }
        return 20;
    }
    
    fun getTempC(i: Int): Int {
    return (Math.ceil((i / 10f * 100).toDouble()) / 100).toInt()
        }
         public static int getBatteryLevel(Context context) {
                try {
                    Intent intent = context.getApplicationContext().registerReceiver(null, new IntentFilter("android.intent.action.BATTERY_CHANGED"));
                    int intExtra = intent.getIntExtra(BatteryManager.EXTRA_LEVEL, -1);
                    int scale = intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1);
                    if (intExtra != -1) {
                        if (scale != -1) {
                            return (int) ((((float) intExtra) / ((float) scale)) * 100.0f);
                        }
                    }
                } catch (Exception e) {
                }
                return 50;
            }
    
// get available ram

        fun getTotalRAM(): Long {
            var reader: RandomAccessFile? = null
            var load: String? = null
            val twoDecimalForm = DecimalFormat("#.##")
            var totRam: Long = 0
            try {
                reader = RandomAccessFile("/proc/meminfo", "r")
                load = reader.readLine()

                // Get the Number value from the string
                val p = Pattern.compile("(\\d+)")
                val m = p.matcher(load)
                var value: String? = ""
                while (m.find()) {
                    value = m.group(1)
                    // System.out.println("Ram : " + value);
                }
                reader.close()
                totRam = Integer.valueOf(value).toLong()
                // totRam = totRam / 1024;
            } catch (ex: IOException) {
                ex.printStackTrace()
            } finally {
                // Streams.close(reader);
            }
            return totRam * 1024
        }

          fun getTotalMemoryStorage(): Long {
            val statFs = StatFs(Environment.getExternalStorageDirectory().path)
            return statFs.blockSize.toLong() * statFs.blockCount.toLong()
        }

        fun getMemoryStorageAvailable(): Long {
            val stat = StatFs(Environment.getExternalStorageDirectory().path)
            return stat.blockSize.toLong() * stat.availableBlocks.toLong()
        }

          private long getAvailableRam(Context context) {
                ActivityManager.MemoryInfo mi = new ActivityManager.MemoryInfo();
                ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
                if (activityManager != null) {
                    activityManager.getMemoryInfo(mi);
                }
                return mi.availMem;
            }

// Foreground Notification Manager

        startForeground(ID_NOTIFI_MANAGER, showNotification(this))

            private var mNotificationManager: NotificationManager? = null
         private fun createNotificationChannel(mContext: Context) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                    val name: CharSequence = mContext.getString(com.example.cleanmaster.R.string.app_name)
                    val importance = NotificationManager.IMPORTANCE_HIGH
                    mNotificationManager =
                        mContext.getSystemService(NOTIFICATION_SERVICE) as NotificationManager
                    val mChannel = NotificationChannel(ID_NOTIFI_MANAGER.toString(), name, importance)
                    mNotificationManager!!.createNotificationChannel(mChannel)
                }
            }
            
          fun showNotification(mContext: Context): Notification {
                val notificationLayout = RemoteViews(
                    mContext.packageName,
                    R.layout.layout_notification_remote
                )
                notificationLayout.setOnClickPendingIntent(
                    R.id.ll_cleanup,
                    onButtonNotificationClick(mContext, R.id.ll_cleanup)
                )

                notificationLayout.setOnClickPendingIntent(
                    R.id.phone_boost,
                    onButtonNotificationClick(mContext, R.id.phone_boost)
                )
                notificationLayout.setOnClickPendingIntent(
                    R.id.ll_cooldown,
                    onButtonNotificationClick(mContext, R.id.ll_cooldown)
                )
                notificationLayout.setOnClickPendingIntent(
                    R.id.ll_battery,
                    onButtonNotificationClick(mContext, R.id.ll_battery)
                )

         try {
                val notification = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION)
                val r = RingtoneManager.getRingtone(mContext, notification)
                r.play()
            } catch (e: Exception) {
                e.printStackTrace()
            }

                return NotificationCompat.Builder(mContext, ID_NOTIFI_MANAGER.toString())
                    .setOngoing(true)
                    .setCategory(NotificationCompat.CATEGORY_SERVICE)
                    .setVisibility(NotificationCompat.VISIBILITY_PUBLIC)
                    .setCustomContentView(notificationLayout)
                    .setContent(notificationLayout)
                    .setCustomHeadsUpContentView(notificationLayout)
                    .setSound(RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION))
                    .setCustomBigContentView(notificationLayout)
                    .setStyle(NotificationCompat.BigPictureStyle())
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(R.drawable.applogo)
                    .build()
            }
            
                manager.notify(notificationID, notification)

    

// get App from device 
  
          fun isUserApp(ai: ApplicationInfo): Boolean {
                val mask = ApplicationInfo.FLAG_SYSTEM or ApplicationInfo.FLAG_UPDATED_SYSTEM_APP
                return ai.flags and mask == 0
            }
    
         onBackground {
                    val apps = packageManager.getInstalledPackages(0)
                    val res: ArrayList<AppInfo> = ArrayList<AppInfo>()
                    res.clear()

                    Thread.sleep(2000)

                    for (i in 0 until apps.size) {
                        val p = apps[i]
                        val newInfo = AppInfo()
                        newInfo.appname = p.applicationInfo.loadLabel(packageManager).toString()
                        newInfo.pname = p.packageName
                        newInfo.versionName = p.versionName
                        newInfo.versionCode = p.versionCode
                        newInfo.icon = p.applicationInfo.loadIcon(packageManager)

                        if (!isDestroyed) {
                            runOnUiThread {
                                bind.scanning.text = newInfo.pname
                                bind.centerimg.setImageDrawable(newInfo.icon)
                            }

                            res.add(newInfo)
                            Thread.sleep(100)
                        } else {
                            break
                        }
                    }

// Gson Convert ArrayList

        val data: ArrayList<WsModel> = Gson().fromJson(intent.extras!!.getString("data"), object : TypeToken<ArrayList<WsModel?>?>() {}.type)

// onActivity Result

        var settingpermission: ActivityResultLauncher<Intent> =
              registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->

                  if (result.resultCode == RESULT_OK) {

                  }
              }


// updated onBackpress

      onBackPressedDispatcher.addCallback(this, object : OnBackPressedCallback(true) {
                override fun handleOnBackPressed() {                   
                        finish()
                }
            })  
            
// Bitmap Save Local File

        @SuppressLint("SimpleDateFormat")
        fun getOutputMediaFile(node: String): File? {
            // To be safe, you should check that the SDCard is mounted
            // using Environment.getExternalStorageState() before doing this.
            val mediaStorageDir = File(Environment.getExternalStorageDirectory()
                    .absolutePath + if (node == "Insta") SAVE_FOLDER_NAME_FOR_INSTA else SAVE_FOLDER_NAME_FOR_FACEBOOK)

            if (!mediaStorageDir.exists()) {
                if (!mediaStorageDir.mkdirs()) {
                    return null
                }
            }
            // Create a media file name
            val mediaFile: File
            val mImageName = "${if (node == "Insta") "insta_" else if (node == "FB") "fb_" else "saver_"}${(System.currentTimeMillis() / 1000)}.png"
            mediaFile = File(mediaStorageDir.path + File.separator + mImageName)
            return mediaFile
        }

        fun storeImage(image: Bitmap, node: String) {
            val pictureFile: File = getOutputMediaFile(node)!!
            try {
                val fos = FileOutputStream(pictureFile)
                image.compress(Bitmap.CompressFormat.PNG, 100, fos)
                fos.close()
            } catch (e: FileNotFoundException) {
                ("Error File Not Found: " + e.message).log()
            } catch (e: IOException) {
                ("Error accessing file: " + e.message).log()
            }
        }

// FilePathUtility

     fun deleteFile() {
            val directory = File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), "/Social_Video_Downloader/$from")
            try {
                val hasDeleted = directory.deleteRecursively()
                ("TANCOLO===> hasDeleted: $hasDeleted").log()
            } catch (exp: Exception) {
                ("TANCOLO===> exception: ${exp.message}").log()
            }
        }

        fun copyFolder(source: File, destination: File) {
            if (source.isDirectory) {
                if (!destination.exists()) {
                    destination.mkdirs()
                }
                val files = source.list()
                for (file in files) {
                    val srcFile = File(source, file)
                    val destFile = File(destination, file)
                    copyFolder(srcFile, destFile)
                }
            } else {
                var `in`: InputStream? = null
                var out: OutputStream? = null
                try {
                    `in` = FileInputStream(source)
                    out = FileOutputStream(destination)
                    val buffer = ByteArray(1024)
                    var length: Int
                    while (`in`.read(buffer).also { length = it } > 0) {
                        out.write(buffer, 0, length)
                    }
                } catch (e: java.lang.Exception) {
                    try {
                        `in`?.close()
                    } catch (e1: IOException) {
                        e1.printStackTrace()
                    }
                    try {
                        out?.close()
                    } catch (e1: IOException) {
                        e1.printStackTrace()
                    }
                }
            }
        }


        private fun checkFolder() {
            val direct = File(Environment.getExternalStorageDirectory().toString() + "/Download/Social_Video_Downloader/whatsapp/")
            ("is Exits: ${!direct.exists()}").log()
            if (!direct.exists()) {
                direct.mkdirs()

            }
        public static boolean moveFile(Context context, String sourceFile, String foldername) {
            String despath = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS) + "/Social_Video_Downloader/whatsapp/" + new        File(sourceFile).getName();
            Uri destUri = Uri.fromFile(new File(despath));
            try {
                InputStream is = context.getContentResolver().openInputStream(Uri.parse(sourceFile));
                OutputStream os = context.getContentResolver().openOutputStream(destUri, "w");
                byte[] buffer = new byte[1024];
                while (true) {
                    int read = is.read(buffer);
                    int length = read;
                    if (read > 0) {
                        os.write(buffer, 0, length);
                    } else {
                        is.close();
                        os.flush();
                        os.close();
                        Intent intent = new Intent("android.intent.action.MEDIA_SCANNER_SCAN_FILE");
                        intent.setData(destUri);
                        context.sendBroadcast(intent);
                        return true;
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
                return false;
            }
        }


        @SuppressLint("NewApi")
        public static String getPath(final Context context, final Uri uri) {

            // check here to KITKAT or new version
            final boolean isKitKatorUp = Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT;

            // DocumentProvider
            if (isKitKatorUp && DocumentsContract.isDocumentUri(context, uri)) {

                // ExternalStorageProvider
                if (isExternalStorageDocument(uri)) {
                    final String docId = DocumentsContract.getDocumentId(uri);
                    final String[] split = docId.split(":");
                    final String type = split[0];

                    if ("primary".equalsIgnoreCase(type)) {
                        return Environment.getExternalStorageDirectory() + "/"
                                + split[1];
                    }
                }
                // DownloadsProvider
                else if (isDownloadsDocument(uri)) {

                    final String id = DocumentsContract.getDocumentId(uri);
                    final Uri contentUri = ContentUris.withAppendedId(
                            Uri.parse("content://downloads/public_downloads"),
                            Long.valueOf(id));

                    return getDataColumn(context, contentUri, null, null);
                }
                // MediaProvider
                else if (isMediaDocument(uri)) {
                    final String docId = DocumentsContract.getDocumentId(uri);
                    final String[] split = docId.split(":");
                    final String type = split[0];

                    Uri contentUri = null;
                    if ("image".equals(type)) {
                        contentUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
                    } else if ("video".equals(type)) {
                        contentUri = MediaStore.Video.Media.EXTERNAL_CONTENT_URI;
                    } else if ("audio".equals(type)) {
                        contentUri = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI;
                    }

                    final String selection = "_id=?";
                    final String[] selectionArgs = new String[]{split[1]};

                    return getDataColumn(context, contentUri, selection,
                            selectionArgs);
                }
            }

            // MediaStore (and general)
            else if ("content".equalsIgnoreCase(uri.getScheme())) {

                // Return the remote address
                if (isGooglePhotosUri(uri))
                    return uri.getLastPathSegment();

                return getDataColumn(context, uri, null, null);
            }
            // File
            else if ("file".equalsIgnoreCase(uri.getScheme())) {
                return uri.getPath();
            }

            return null;
        }

        /**
         * Get the value of the data column for this Uri. This is useful for
         * MediaStore Uris, and other file-based ContentProviders.
         *
         * @param context       The context.
         * @param uri           The Uri to query.
         * @param selection     (Optional) Filter used in the query.
         * @param selectionArgs (Optional) Selection arguments used in the query.
         * @return The value of the _data column, which is typically a file path.
         */
        public static String getDataColumn(Context context, Uri uri,
                                           String selection, String[] selectionArgs) {

            Cursor cursor = null;
            final String column = "_data";
            final String[] projection = {column};

            try {
                cursor = context.getContentResolver().query(uri, projection,
                        selection, selectionArgs, null);
                if (cursor != null && cursor.moveToFirst()) {
                    final int index = cursor.getColumnIndexOrThrow(column);
                    return cursor.getString(index);
                }
            } catch (Exception e) {

            } finally {
                if (cursor != null)
                    cursor.close();
            }
            return null;
        }

        /**
         * @param uri The Uri to check.
         * @return Whether the Uri authority is ExternalStorageProvider.
         */
        public static boolean isExternalStorageDocument(Uri uri) {
            return "com.android.externalstorage.documents".equals(uri
                    .getAuthority());
        }

        /**
         * @param uri The Uri to check.
         * @return Whether the Uri authority is DownloadsProvider.
         */
        public static boolean isDownloadsDocument(Uri uri) {
            return "com.android.providers.downloads.documents".equals(uri
                    .getAuthority());
        }

        /**
         * @param uri The Uri to check.
         * @return Whether the Uri authority is MediaProvider.
         */
        public static boolean isMediaDocument(Uri uri) {
            return "com.android.providers.media.documents".equals(uri
                    .getAuthority());
        }

        /**
         * @param uri The Uri to check.
         * @return Whether the Uri authority is Google Photos.
         */
        public static boolean isGooglePhotosUri(Uri uri) {
            return "com.google.android.apps.photos.content".equals(uri
                    .getAuthority());
        }


        public static FilePathUtility getInstance() {
            return new FilePathUtility();
        }

        public FilePathUtility with(Context context) {
            this.context = context;
            return this;
        }

        /**
         * Create new media uri.
         */
        public Uri create(String directory, String filename, String mimetype) {

            ContentResolver contentResolver = context.getContentResolver();

            ContentValues contentValues = new ContentValues();

            //Set filename, if you don't system automatically use current timestamp as name
            contentValues.put(MediaStore.MediaColumns.DISPLAY_NAME, filename);

            //Set mimetype if you want
            contentValues.put(MediaStore.MediaColumns.MIME_TYPE, mimetype);

            //To create folder in Android directories use below code
            //pass your folder path here, it will create new folder inside directory
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
                contentValues.put(MediaStore.MediaColumns.RELATIVE_PATH, directory);
            }

            //pass new ContentValues() for no values.
            //Specified uri will save object automatically in android specified directories.
            //ex. MediaStore.Images.Media.EXTERNAL_CONTENT_URI will save object into android Pictures directory.
            //ex. MediaStore.Videos.Media.EXTERNAL_CONTENT_URI will save object into android Movies directory.
            //if content values not provided, system will automatically add values after object was written.
            return contentResolver.insert(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, contentValues);
        }

        /**
         * Delete file.
         * <p>
         * If {@link ContentResolver} failed to delete the file, use trick,
         * SDK version is >= 29(Q)? use {@link SecurityException} and again request for delete.
         * SDK version is >= 30(R)? use .
         */
        public void delete(ActivityResultLauncher<IntentSenderRequest> launcher, Uri uri) {

            ContentResolver contentResolver = context.getContentResolver();

            try {

                //delete object using resolver
                contentResolver.delete(uri, null, null);

            } catch (SecurityException e) {

                PendingIntent pendingIntent = null;

                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {

                    ArrayList<Uri> collection = new ArrayList<>();
                    collection.add(uri);
                    pendingIntent = MediaStore.createDeleteRequest(contentResolver, collection);

                } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {

                    //if exception is recoverable then again send delete request using intent
                    if (e instanceof RecoverableSecurityException) {
                        RecoverableSecurityException exception = (RecoverableSecurityException) e;
                        pendingIntent = exception.getUserAction().getActionIntent();
                    }
                }

                if (pendingIntent != null) {
                    IntentSender sender = pendingIntent.getIntentSender();
                    IntentSenderRequest request = new IntentSenderRequest.Builder(sender).build();
                    launcher.launch(request);
                }
            }
        }

        /**
         * Rename file.
         *
         * @param uri    - filepath.
         * @param rename - the name you want to replace with original.
         */
        public void rename(Uri uri, String rename) {

            //create content values with new name and update
            ContentValues contentValues = new ContentValues();
            contentValues.put(MediaStore.MediaColumns.DISPLAY_NAME, rename);

            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
                context.getContentResolver().update(uri, contentValues, null);
            }
        }

        /**
         * Duplicate file.
         *
         * @param uri - filepath.
         */
        public Uri duplicate(Uri uri) {
            ContentResolver contentResolver = context.getContentResolver();

            Uri output = contentResolver.insert(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, new ContentValues());

            String input = getPathFromUri(uri);

            try (InputStream inputStream = new FileInputStream(input)) { //input stream

                OutputStream out = contentResolver.openOutputStream(output); //output stream

                byte[] buf = new byte[1024];
                int len;
                while ((len = inputStream.read(buf)) > 0) {
                    out.write(buf, 0, len); //write input file data to output file
                }

            } catch (IOException e) {
                e.printStackTrace();
            }

            return output;
        }

        /**
         * Get file path from uri.
         */
        @SuppressLint("Range")
        private String getPathFromUri(Uri uri) {
            Cursor cursor = context.getContentResolver().query(uri, null, null, null, null);

            String text = null;

            if (cursor.moveToNext()) {
                text = cursor.getString(cursor.getColumnIndex(MediaStore.MediaColumns.DATA));
            }

            cursor.close();

            return text;
        }

        public static long getFilePathToMediaID(String songPath, Context context) {
            long id = 0;
            ContentResolver cr = context.getContentResolver();

            Uri uri = MediaStore.Files.getContentUri("external");
            String selection = MediaStore.Audio.Media.DATA;
            String[] selectionArgs = {songPath};
            String[] projection = {MediaStore.Audio.Media._ID};
            String sortOrder = MediaStore.Audio.Media.TITLE + " ASC";

            Cursor cursor = cr.query(uri, projection, selection + "=?", selectionArgs, null);

            if (cursor != null) {
                while (cursor.moveToNext()) {
                    int idIndex = cursor.getColumnIndex(MediaStore.Audio.Media._ID);
                    id = Long.parseLong(cursor.getString(idIndex));
                }
            }

            return id;
        }

        public static void deletefileAndroid10andABOVE(Activity activity, String path, boolean isvideo) {
            if (isvideo) {
                File tempFile = new File(path);
                long mediaID = getFilePathToMediaID(tempFile.getAbsolutePath(), activity);
                Uri Uri_one = ContentUris.withAppendedId(MediaStore.Video.Media.getContentUri("external"), mediaID);
                List<Uri> uris = new ArrayList<>();
                uris.add(Uri_one);

                requestDeletePermission(activity, uris);
            } else {
                File tempFile = new File(path);
                long mediaID = getFilePathToMediaID(tempFile.getAbsolutePath(), activity);
                Uri Uri_one = ContentUris.withAppendedId(MediaStore.Images.Media.getContentUri("external"), mediaID);
                List<Uri> uris = new ArrayList<>();
                uris.add(Uri_one);


                requestDeletePermission(activity, uris);
            }
        }

        private static void requestDeletePermission(Activity activity, List<Uri> uriList) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
                PendingIntent pi = MediaStore.createDeleteRequest(activity.getContentResolver(), uriList);

                try {
                    activity.startIntentSenderForResult(pi.getIntentSender(), 123, null, 0, 0,
                            0);
                } catch (IntentSender.SendIntentException e) {
                    Log.d("error", "" + e.getLocalizedMessage());

                }
            }
        }

// Read & Write Permisstion

     private val requiredPermissions = arrayOf(
                android.Manifest.permission.WRITE_EXTERNAL_STORAGE,
                android.Manifest.permission.READ_EXTERNAL_STORAGE,
        )

        var requestPermissionsLauncher =
                registerForActivityResult(ActivityResultContracts.RequestMultiplePermissions()) {
                    if (Build.VERSION.SDK_INT < 33) {
                        //  restartApp()
                    }
                }

          if (Build.VERSION.SDK_INT >= 33) {
                   
                    finish()
                }
            
                  setOnClickListener {
                ("is tiramisu: " + (Build.VERSION.SDK_INT == Build.VERSION_CODES.TIRAMISU)).log()
                if (Build.VERSION.SDK_INT == Build.VERSION_CODES.TIRAMISU) {
                    finish()
                } else
                    if (requiredPermissions.all { isPermissionGranted(it) } || Build.VERSION.SDK_INT >= 33) {
                        finish()
                    } else {
                        requestPermissionsLauncher.launch(requiredPermissions)
                    }
            }
        }
    
         private fun isPermissionGranted(permission: String): Boolean {
            return ContextCompat.checkSelfPermission(
                    this,
                    permission,
            ) == PackageManager.PERMISSION_GRANTED
        }

        override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<out String>, grantResults: IntArray) {
            super.onRequestPermissionsResult(requestCode, permissions, grantResults)
            if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                finish()
            } else {
                ("is Here Not Granted").tos()
            }
        }
        
// get File Size

    open fun getFileSize(size: Long): String? {
        if (size <= 0) return "0"
        val units = arrayOf("B", "KB", "MB", "GB", "TB")
        val digitGroups = (log10(size.toDouble()) / log10(1024.0)).toInt()
        return DecimalFormat("#,##0.#").format(size / 1024.0.pow(digitGroups.toDouble())) + " " + units[digitGroups]
    }

// Firebase Rules

         rules_version = '2';
        service cloud.firestore {

        match /databases/{database}/documents {

            // match /abc/{document=**} {
            //   allow read: if true;   
            //   allow write: if false;
            // }

           match /base/{document=**} {
              allow read: if request.auth!=null;   
              allow write: if false;
            }
          }

        }


// Android Menifest

        android:largeHeap="true"
              android:requestLegacyExternalStorage="true"
               android:usesCleartextTraffic="true"

               <activity
                  android:name="<Act Name>"
                  android:configChanges="uiMode"
                              android:launchMode="singleInstance"
                  android:exported="false"
                  android:screenOrientation="portrait">

              </activity>


              <provider
                  android:name="androidx.core.content.FileProvider"
                  android:authorities="${applicationId}.provider"
                  android:exported="false"
                  android:grantUriPermissions="true">
                  <meta-data
                      android:name="android.support.FILE_PROVIDER_PATHS"
                      android:resource="@xml/provider_paths" />
              </provider>

              --- XML Provider ---
              <paths>
          <external-path
              name="external"
              path="." />
          <external-files-path
              name="external_files"
              path="." />
          <cache-path
              name="cache"
              path="." />
          <external-cache-path
              name="external_cache"
              path="." />
          <files-path
              name="files"
              path="." />
      </paths>

        
// Important Theme
 
    <item name="colorPrimary">@color/purple_500</item>
          <item name="colorPrimaryVariant">@color/purple_700</item>
          <item name="colorOnPrimary">@color/white</item>
          <!-- Secondary brand color. -->
          <item name="colorSecondary">@color/teal_200</item>
          <item name="colorSecondaryVariant">@color/teal_700</item>
          <item name="colorOnSecondary">@color/black</item>
          <!-- Status bar color. -->
          <item name="android:statusBarColor">@color/status_bar</item>
          <item name="android:windowLightStatusBar">false</item>

          <!-- Customize your theme here. -->
          <item name="materialCalendarFullscreenTheme">@style/ThemeOverlay.MaterialComponents.MaterialCalendar.Fullscreen</item>

          <item name="android:windowSplashScreenAnimatedIcon" tools:targetApi="s">@android:color/transparent</item>
          <item name="android:windowSplashScreenIconBackgroundColor" tools:targetApi="s">@android:color/transparent</item>
          <!--        <item name="android:windowSplashScreenAnimationDuration" tools:targetApi="s">100</item>-->
          <item name="android:forceDarkAllowed" tools:targetApi="q">false</item>


      <style name="Theme.WindowFullscreen" parent="Theme.Name">
          <item name="android:windowNoTitle">true</item>
          <item name="android:windowFullscreen">true</item>
      </style>

// Custom popup dialog popupdialog popupmanager popupwindow manager          
                
                  class CustomPopupDialog(
                        activity: Activity, anchorView: View, listofcat: ArrayList<String>, callBack: (String) -> Unit
                    ) {
                        private var popupWindow: PopupWindow? = null
                    
                        init {
                            val popupView: PopupdialogsBinding = PopupdialogsBinding.inflate(activity.layoutInflater)
                            LayoutInflater.from(activity).inflate(R.layout.popupdialogs, null)
                    
                            popupWindow = PopupWindow(
                                popupView.root, ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT
                            )
                    
                            popupWindow!!.animationStyle = R.style.PopupAnimation
                    
                            popupView.root.setCardBackgroundColor(android.graphics.Color.WHITE)
                    //        popupWindow!!.setBackgroundDrawable(ColorDrawable(android.graphics.Color.WHITE))
                    
                            popupWindow!!.isOutsideTouchable = true
                            popupWindow!!.isFocusable = true
                    
                            val marginRightDp = 10
                            val marginRightPx = TypedValue.applyDimension(
                                TypedValue.COMPLEX_UNIT_DIP, marginRightDp.toFloat(), activity.resources.displayMetrics
                            ).toInt()
                    
                            // Get screen dimensions and anchor view location
                            val location = IntArray(2)
                            anchorView.getLocationOnScreen(location)
                    
                            val displayMetrics = DisplayMetrics()
                            activity.windowManager.defaultDisplay.getMetrics(displayMetrics)
                            val screenWidth = displayMetrics.widthPixels
                    
                            // Calculate the exact x and y positions
                            val anchorX = location[0]
                            val anchorY = location[1] + anchorView.height
                    
                            // Determine the x position for the popup with a right margin
                            val popupWidth = popupWindow!!.contentView.measuredWidth
                            val xPosition = screenWidth - popupWidth - marginRightPx
                    
                            // Show the popup at the calculated position
                            popupWindow!!.showAtLocation(
                                anchorView, Gravity.NO_GRAVITY, anchorX, anchorY
                            )
                    
                            popupView.apply {
                                rvcatid.adapter = RVDragDropAdapter(activity, listofcat) {
                                    callBack.invoke(it)
                                    popupWindow!!.dismiss()
                                }
                            }
                        }
                    }
                
                <com.google.android.material.card.MaterialCardView xmlns:android="http://schemas.android.com/apk/res/android"
                    xmlns:app="http://schemas.android.com/apk/res-auto"
                    xmlns:tools="http://schemas.android.com/tools"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_centerInParent="true"
                    android:layout_margin="@dimen/_15sdp"
                    android:background="@android:color/transparent"
                    android:clipChildren="false"
                    android:clipToPadding="false"
                    android:elevation="0dp"
                    app:cardCornerRadius="@dimen/_10sdp"
                    app:cardElevation="0dp"
                    app:strokeColor="#299E9E9E">
                
                    <LinearLayout
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content">
                
                        <androidx.recyclerview.widget.RecyclerView
                            android:id="@+id/rvcatid"
                            android:layout_width="wrap_content"
                            android:layout_height="@dimen/_300sdp"
                            android:orientation="vertical"
                            app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
                            tools:itemCount="10"
                            tools:listitem="@layout/rv_dragdrop_layout" />
                
                    </LinearLayout>
                </com.google.android.material.card.MaterialCardView>

// Base Fragment BaseFragment
      
        abstract class BaseFragment<VB : ViewBinding> : Fragment() {
            lateinit var bind: VB
        
            override fun onCreateView(
                inflater: LayoutInflater,
                container: ViewGroup?,
                savedInstanceState: Bundle?
            ): View {
                bind = getActivityBinding(layoutInflater)
                return bind.root
            }
        
            override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
                super.onViewCreated(view, savedInstanceState)
                initUI()
            }
        
            abstract fun getActivityBinding(inflater: LayoutInflater): VB
        
            abstract fun initUI()
        
        }

// TypewriterTextView chatgpt responce typewriter printertext printtextview
                             
                class TypewriterTextView @JvmOverloads constructor(
                    context: Context,
                    attrs: AttributeSet? = null,
                    defStyle: Int = 0
                ) : AppCompatTextView(context, attrs, defStyle) {
                
                    private var textToShow: CharSequence = ""
                    private var index = 0
                    private var delay: Long = 5 // Delay in ms for each character
                    private var onTypingCompleted: (() -> Unit)? = null
                
                    private val handler = Handler(Looper.getMainLooper())
                
                    private val characterAdder = object : Runnable {
                        override fun run() {
                            if (index < textToShow.length) {
                                text = textToShow.subSequence(0, index + 1) // Update text with next character
                                index++
                                handler.postDelayed(this, delay)
                            } else {
                                onTypingCompleted?.invoke() // Notify when typing is complete
                            }
                        }
                    }
                
                    fun setTextWithTypingEffect(text: CharSequence, typingDelay: Long = 5, onComplete: (() -> Unit)? = null) {
                        textToShow = text
                        index = 0
                        onTypingCompleted = onComplete
                        this.text = "" // Clear the current text
                        handler.removeCallbacks(characterAdder)
                        handler.post(characterAdder) // Start typing effect
                    }
                    }
                
                  bind.tvresponceid.setTextWithTypingEffect(
                                            itemdata.Content.text, typingDelay = 20
                                        ) {
                                            Log.d("Typewriter", "Typing completed!")
                                        }
                                        
// Important Lib

       
          //tab-layout
           implementation("com.github.crimson0829:AdvancedTabLayout:1.6")

                         <?xml version="1.0" encoding="utf-8"?>
              <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:app="http://schemas.android.com/apk/res-auto"
                  xmlns:tools="http://schemas.android.com/tools"
                  android:layout_width="match_parent"
                  android:layout_height="match_parent"
                  tools:context=".ui.recovered_media.activity.RecoveredFileActivity">
              
                  <androidx.appcompat.widget.Toolbar
                      android:id="@+id/toolbar"
                      android:layout_width="match_parent"
                      android:layout_height="?actionBarSize"
                      app:layout_constraintEnd_toEndOf="parent"
                      app:layout_constraintStart_toStartOf="parent"
                      app:layout_constraintTop_toTopOf="parent"
                      app:navigationIcon="@drawable/ic_back"
                      app:title="Recovered Media"
                      app:titleTextAppearance="@style/ToolbarTitleStyle" />
              
                  <androidx.viewpager2.widget.ViewPager2
                      android:id="@+id/pager"
                      android:layout_width="0dp"
                      android:layout_height="0dp"
                      app:layout_constraintBottom_toTopOf="@+id/tabLayout"
                      app:layout_constraintEnd_toEndOf="parent"
                      app:layout_constraintHorizontal_bias="1.0"
                      app:layout_constraintStart_toStartOf="parent"
                      app:layout_constraintTop_toBottomOf="@+id/toolbar" />
              
                  <com.crimson.library.tab.AdvancedTabLayout
                      android:id="@+id/tabLayout"
                      android:layout_width="match_parent"
                      android:layout_height="@dimen/_42sdp"
                      app:layout_constraintBottom_toBottomOf="parent"
                      app:layout_constraintEnd_toEndOf="parent"
                      app:layout_constraintStart_toStartOf="parent"
                      app:tl_indicator_color="@color/color_active"
                      app:tl_indicator_corner_radius="100dp"
                      app:tl_indicator_height="5dp"
                      app:tl_indicator_width="35dp"
                      app:tl_smoothScroll_enable="true"
                      app:tl_textBold="NONE"
                      app:tl_textSelectColor="@color/white"
                      app:tl_textSelectSize="@dimen/_16ssp"
                      app:tl_textUnselectColor="@color/color_gray_800"
                      app:tl_textsize="@dimen/_13ssp"
                      app:tl_underline_color="@android:color/transparent"
                      app:tl_underline_height="5dp" />
              
              
              
              </androidx.constraintlayout.widget.ConstraintLayout>

          //ticker timer animation textview
           implementation("com.robinhood.ticker:ticker:2.0.4")


              <com.robinhood.ticker.TickerView
        android:id="@+id/tvTapOnStartScan"
        android:layout_width="wrap_content"
        android:layout_height="@dimen/_35sdp"
        android:layout_marginTop="@dimen/_15sdp"
        android:fontFamily="@font/nunito_reguler"
        android:gravity="center"
        android:includeFontPadding="false"
        android:text="@string/tap_to_start_scanning"
        android:textColor="@color/white"
        android:textSize="@dimen/_15ssp"
        app:layout_constraintEnd_toEndOf="@+id/animationView"
        app:layout_constraintStart_toStartOf="@+id/animationView"
        app:layout_constraintTop_toBottomOf="@+id/animationView"
        app:ticker_animationDuration="500" />


                binding.tvTapOnStartScan.setCharacterLists(TickerUtils.provideNumberList())

    
    //mvvm,coroutine,lifecycle
    implementation("androidx.lifecycle:lifecycle-extensions:2.2.0")
    implementation("androidx.lifecycle:lifecycle-livedata-ktx:2.6.2")
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.6.2")
    implementation("androidx.lifecycle:lifecycle-runtime:2.6.2")
    implementation("androidx.activity:activity-ktx:1.7.2")
    implementation("androidx.fragment:fragment-ktx:1.6.1")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.1")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.1")

    
     //bounce animation
        implementation("io.github.hariprasanths:bounceview-android:0.2.0")

        BounceView.addAnimTo(binding.tvVideo)
        

      //splash-API
        implementation("androidx.core:core-splashscreen:1.0.1")

       -- in splace screen
          private lateinit var splashScreen: SplashScreen
              private val viewModel: SplashViewModel by viewModels()


              override fun onCreate(savedInstanceState: Bundle?) {
            splashScreen = installSplashScreen()
            splashScreen.setKeepOnScreenCondition { viewModel.isReadyForExit.value }
            splashScreen.setOnExitAnimationListener { splashScreenView ->
                val animation = splashScreenView.alphaAnimation()
    
                val animatorSet = AnimatorSet()
                animatorSet.duration = 1500
                animatorSet.interpolator = AnticipateInterpolator()
                animatorSet.playTogether(animation)
    
                animatorSet.doOnEnd {
                    splashScreenView.remove()
                    startActivity<DashboardActivity> { }
                    finish()
                }
                animatorSet.start()
            }
    
            super.onCreate(savedInstanceState)
        }

                class SplashViewModel :ViewModel() {
            val isReadyForExit = MutableStateFlow(true)
            init {
                viewModelScope.launch {
                    delay(1000)
                    isReadyForExit.tryEmit(false)
                }
            }
        }

     <style name="Theme.App.Starting" parent="Theme.SplashScreen">
            <item name="windowSplashScreenBackground">@color/black</item>
            <item name="windowSplashScreenAnimatedIcon">@drawable/ic_splash_logo</item>
            <item name="windowSplashScreenAnimationDuration">2000</item>
            <item name="postSplashScreenTheme">@style/Theme.Main</item>
        </style>

            
    fun SplashScreenViewProvider.slideUpAnimation(): ObjectAnimator {
        return ObjectAnimator.ofFloat(view, View.TRANSLATION_Y, 0f, -view.height.toFloat())
    }
    
    fun SplashScreenViewProvider.slideLeftAnimation(): ObjectAnimator {
        return ObjectAnimator.ofFloat(view, View.TRANSLATION_X, 0f, -view.width.toFloat())
    }
    
    fun SplashScreenViewProvider.scaleXAnimation(): ObjectAnimator {
        return ObjectAnimator.ofFloat(view, View.SCALE_X, 1.0f, 0f)
    }
    
    fun SplashScreenViewProvider.scaleXYAnimation(): ObjectAnimator {
        val path = Path()
        path.moveTo(1.0f, 1.0f)
        path.lineTo(0f, 0f)
    
        return ObjectAnimator.ofFloat(view, View.SCALE_X, View.SCALE_Y, path)
    }
    
    internal fun SplashScreenViewProvider.alphaAnimation(): ObjectAnimator {
        return ObjectAnimator.ofFloat(view, View.ALPHA, 1f, 0f)
    }

        
            implementation 'com.yandex.android:mobmetricalib:5.0.1'

            implementation 'com.android.installreferrer:installreferrer:2.2'

            implementation 'com.facebook.android:facebook-android-sdk:latest.release'

            //in-App Review
            implementation 'com.google.android.play:review:2.0.0'
            implementation 'com.google.android.play:review-ktx:2.0.0'

            //OneSignal
            implementation 'com.onesignal:OneSignal:[4.0.0, 4.99.99]'

            //firebase-auth
            implementation 'com.google.firebase:firebase-auth-ktx:21.0.7'
            implementation 'com.google.android.gms:play-services-analytics-impl:18.0.2'

            //billing
            def billing_version = "5.0.0"
            implementation "com.android.billingclient:billing:$billing_version"
            implementation "com.android.billingclient:billing-ktx:$billing_version"

            //Move File Lib

            implementation 'org.apache.commons:commons-lang3:3.10'
            implementation 'commons-io:commons-io:2.6'


            val file = File(item.absolutePath)
                        val destPath = Environment.getExternalStorageDirectory().absolutePath + SAVE_FOLDER_NAME_FOR_WHATSAPP
                        val destFile = File(destPath)
                        try {
                            org.apache.commons.io.FileUtils.copyFileToDirectory(file, destFile)
                        } catch (e: IOException) {
                            activity.runOnUiThread {
                                e.message.log()
                                e.printStackTrace().log()
                            }
                        }
                        
    
            //lib
            implementation "androidx.lifecycle:lifecycle-extensions:2.2.0"
            implementation "androidx.lifecycle:lifecycle-runtime-ktx:2.5.0"
            annotationProcessor "androidx.lifecycle:lifecycle-compiler:2.5.0"
            //ads
            implementation 'com.google.android.gms:play-services-ads:20.6.0'

            implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.6.1'
            implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.1'

            implementation 'com.airbnb.android:lottie:5.2.0'

            //blure effect
            implementation 'jp.wasabeef:glide-transformations:4.3.0'
            // If you want to use the GPU Filters
            implementation 'jp.co.cyberagent.android:gpuimage:2.1.0'

            //ucrop
            implementation 'com.github.yalantis:ucrop:2.2.6'

            //sdp
            implementation 'com.intuit.sdp:sdp-android:1.1.0'

            //retrofit2
            implementation 'com.squareup.retrofit2:retrofit:2.9.0'
            implementation 'com.squareup.retrofit2:converter-gson:2.9.0'

            //interceptor
            implementation 'com.squareup.okhttp3:logging-interceptor:4.9.1'

            //gson
            implementation 'com.google.code.gson:gson:2.8.9'

            //Glide
            implementation 'com.github.bumptech.glide:glide:4.13.0'
            annotationProcessor 'com.github.bumptech.glide:compiler:4.13.0'

            //firebase-crashlytics & analytics
            implementation 'com.google.firebase:firebase-crashlytics:18.2.6'
            implementation 'com.google.firebase:firebase-analytics:20.0.2'
            //firestore
            implementation 'com.google.firebase:firebase-firestore-ktx:24.2.1'

            // Recycler view animation lib
            https://github.com/wasabeef/recyclerview-animators

          //YoYo Animation..
          YoYo.with(Techniques.Pulse).duration(1200).repeat(Animation.INFINITE)
                .playOn(bind.handbtn)
                
            implementation 'com.daimajia.easing:library:2.0@aar'
            implementation 'com.daimajia.androidanimations:library:2.3@aar'
    
            //multidex
            implementation 'androidx.multidex:multidex:2.0.1'

            //kotlin
            implementation 'androidx.core:core-ktx:1.8.0'

            implementation "androidx.activity:activity-ktx:1.5.0"

            implementation 'androidx.legacy:legacy-support-v4:1.0.0'
            implementation 'androidx.appcompat:appcompat:1.4.2'
            implementation 'com.google.android.material:material:1.6.1'
            implementation 'androidx.constraintlayout:constraintlayout:2.1.4'

// ViewBinding
               
         buildFeatures {
            viewBinding true
         }
    

// Extenstion Funcation Ext.kt funcation extention funcation 
                
                    fun getTimeAgo(pastTimeMillis: Long): String {
                        val currentTimeMillis = System.currentTimeMillis()
                        val differenceMillis = currentTimeMillis - pastTimeMillis
                    
                        val seconds = differenceMillis / 1000
                        val minutes = seconds / 60
                        val hours = minutes / 60
                        val days = hours / 24
                    
                        return when {
                            seconds < 60 -> "$seconds seconds ago"
                            minutes < 60 -> "$minutes minutes ago"
                            hours < 24 -> "$hours hours ago"
                            else -> "$days days ago"
                        }
                    }
                
                //Save image saveBitmap sharebitmap 
                
                    fun shareImageFromUrl(context: Context, imageUrl: String) {
                        CoroutineScope(Dispatchers.IO).launch {
                            try {
                                // Download the image as Bitmap
                                val bitmap = Glide.with(context).asBitmap().load(imageUrl).submit().get()
                    
                                // Save the Bitmap locally
                                val file = saveBitmapToFile(context, bitmap)
                    
                                // Share the image using FileProvider
                                file?.let { shareImage(context, it) }
                            } catch (e: Exception) {
                                e.printStackTrace()
                            }
                        }
                    }
                    
                    private fun saveBitmapToFile(context: Context, bitmap: Bitmap): File? {
                        return try {
                            // Create a directory in the cache folder
                            val cachePath = File(context.cacheDir, "images")
                            cachePath.mkdirs() // Ensure the directory exists
                    
                            // Save the image to a file
                            val file = File(cachePath, "shared_image.png")
                            val fileOutputStream = FileOutputStream(file)
                            bitmap.compress(Bitmap.CompressFormat.PNG, 100, fileOutputStream)
                            fileOutputStream.close()
                            file
                        } catch (e: Exception) {
                            e.printStackTrace()
                            null
                        }
                    }
                    
                    private fun shareImage(context: Context, file: File) {
                        // Get the content URI using FileProvider
                        val uri: Uri = FileProvider.getUriForFile(context, "${context.packageName}.fileprovider", file)
                    
                        // Create a sharing intent
                        val shareIntent = Intent(Intent.ACTION_SEND).apply {
                            type = "image/png"
                            putExtra(Intent.EXTRA_STREAM, uri)
                            addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION) // Grant permission to the receiving app
                        }
                    
                        // Start the sharing activity
                        context.startActivity(Intent.createChooser(shareIntent, "Share Image"))
                    }
                    
                    
                    @SuppressLint("NewApi")
                    fun saveBitmapToDownloads(context: Context, bitmap: Bitmap, fileName: String) {
                        val downloadsDir = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
                            // For Android 10 and above
                            MediaStore.Downloads.EXTERNAL_CONTENT_URI
                        } else {
                            Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS)
                        }
                    
                        try {
                            val contentValues = ContentValues().apply {
                                put(MediaStore.MediaColumns.DISPLAY_NAME, "$fileName.png") // Add extension
                                put(MediaStore.MediaColumns.MIME_TYPE, "image/png")
                                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
                                    put(MediaStore.MediaColumns.RELATIVE_PATH, Environment.DIRECTORY_DOWNLOADS)
                                }
                            }
                    
                            val uri =
                                context.contentResolver.insert(MediaStore.Downloads.EXTERNAL_CONTENT_URI, contentValues)
                            uri?.let {
                                val outputStream: OutputStream? = context.contentResolver.openOutputStream(it)
                                outputStream?.use { stream ->
                                    bitmap.compress(Bitmap.CompressFormat.PNG, 100, stream) // Save as PNG
                                    Toast.makeText(context, "Saved to Downloads folder", Toast.LENGTH_SHORT).show()
                                }
                            }
                        } catch (e: Exception) {
                            e.printStackTrace()
                            Toast.makeText(context, "Failed to save image: ${e.message}", Toast.LENGTH_LONG).show()
                        }
                    }
                    
                    fun downloadAndSaveBitmap(context: Context, imageUrl: String, fileName: String) {
                        Glide.with(context).asBitmap().load(imageUrl)
                            .into(object : com.bumptech.glide.request.target.CustomTarget<Bitmap>() {
                                override fun onResourceReady(
                                    resource: Bitmap,
                                    transition: com.bumptech.glide.request.transition.Transition<in Bitmap>?
                                ) {
                                    saveBitmapToDownloads(context, resource, fileName)
                                }
                    
                                override fun onLoadCleared(placeholder: android.graphics.drawable.Drawable?) {
                                    // Handle cleanup if needed
                                }
                    
                                override fun onLoadFailed(errorDrawable: android.graphics.drawable.Drawable?) {
                                    Toast.makeText(context, "Failed to download image", Toast.LENGTH_SHORT).show()
                                }
                            })
                    }
                
                
                // share text
                //copytext
                
                fun shareText(context: Context, textToShare: String) {
                    val intent = Intent(Intent.ACTION_SEND).apply {
                        type = "text/plain"
                        putExtra(Intent.EXTRA_TEXT, textToShare)
                    }
                
                    // Start the share intent
                    val chooser = Intent.createChooser(intent, "Share using")
                    context.startActivity(chooser)
                }
                
                fun copyTextToClipboard(context: Context, textToCopy: String) {
                    val clipboardManager = context.getSystemService(Context.CLIPBOARD_SERVICE) as ClipboardManager
                    val clipData = ClipData.newPlainText("Copied Text", textToCopy)
                    clipboardManager.setPrimaryClip(clipData)
                
                    // Notify the user
                    Toast.makeText(context, "Text copied to clipboard", Toast.LENGTH_SHORT).show()
                }
                
                fun Activity.onBackground(onNext: () -> Unit) {
                    CoroutineScope(Dispatchers.IO).launch {
                        onNext.invoke()
                    }
                }
                

        // statusbar color change statusbar 
        
           private fun applyStatusBarColor() {
                    val isDarkMode = AppCompatDelegate.getDefaultNightMode() == AppCompatDelegate.MODE_NIGHT_YES
            
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
                        val controller = window.insetsController
                        if (controller != null) {
                            if (isDarkMode) {
                                controller.setSystemBarsAppearance(
                                    0, WindowInsetsController.APPEARANCE_LIGHT_STATUS_BARS
                                )
                            } else {
                                controller.setSystemBarsAppearance(
                                    WindowInsetsController.APPEARANCE_LIGHT_STATUS_BARS,
                                    WindowInsetsController.APPEARANCE_LIGHT_STATUS_BARS
                                )
                            }
                        }
                    } else {
                        @Suppress("DEPRECATION") if (isDarkMode) {
                            window.decorView.systemUiVisibility = 0
                        } else {
                            window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR
                        }
                    }
            // Set the status bar color
                    window.statusBarColor = ContextCompat.getColor(this, R.color.white)
                }
                
      @JvmField
      val gson = Gson()

      fun Exception.print() {
    if (BuildConfig.DEBUG) {
        printStackTrace()
        "$message | ${javaClass.name}".log()
    }
              }
              
           fun AppCompatActivity.onBack() = onBackPressedDispatcher.onBackPressed()
           
           inline fun View.click(crossinline click: () -> Unit) {
               this.setOnClickListener {
                   click()
               }
           }

           
           fun View.handAnim() {
               val zoomIn: Animation = ScaleAnimation(
                   1f, 1.2f,  // Start and end values for the X axis scaling
                   1f, 1.2f,  // Start and end values for the Y axis scaling
                   Animation.RELATIVE_TO_SELF, 0.5f,  // Pivot point of X scaling
                   Animation.RELATIVE_TO_SELF, 0.5f
               ) // Pivot point of Y scaling
           
               zoomIn.duration = 200
           
               val zoomOut: Animation = ScaleAnimation(
                   1.2f, 1f,  // Start and end values for the X axis scaling
                   1.2f, 1f,  // Start and end values for the Y axis scaling
                   Animation.RELATIVE_TO_SELF, 0.5f,  // Pivot point of X scaling
                   Animation.RELATIVE_TO_SELF, 0.5f
               ) // Pivot point of Y scaling
           
               zoomOut.duration = 200
               zoomOut.startOffset = 200 // Delay the zoom out animation
               zoomOut.repeatCount = 10000
           
               zoomIn.setAnimationListener(object : Animation.AnimationListener {
                   override fun onAnimationStart(animation: Animation) {}
                   override fun onAnimationEnd(animation: Animation) {
                       startAnimation(zoomOut) // Start zoom out animation after zoom in
                   }
           
                   override fun onAnimationRepeat(animation: Animation) {}
               })
           
               startAnimation(zoomIn)
           }
           
           
           fun View.clearAnim() {
               clearAnimation()
           }


                      fun View.flipanimdelay(delay: Long) {
    val animator: ObjectAnimator = ObjectAnimator.ofPropertyValuesHolder(
        this,
        PropertyValuesHolder.ofFloat(View.ROTATION_Y, 0f, 360f)
    )
    animator.duration = delay
               animator.interpolator = AccelerateDecelerateInterpolator()
               animator.start()
           }


           inline fun <reified T> String.fromJSON() = Gson().fromJson<T>(this, object : TypeToken<T>() {}.type)

      fun <T> T.toGson(): String = gson.toJson(this)

        fun <T> AppCompatActivity.toAc(java: Class<T>) = startActivity(Intent(this, java))
        
        fun htmlTagVertical(txt: String, value: Any): Spanned = "<font color=#064974><small>$txt</small></font> <br> <font>$value</font>".html()


        inline fun exc(block: () -> Unit) {
            try {
                block()
            } catch (e: Exception) {
                e.print()
            }
        }
        
        fun View.roundAnim(activity: Activity) {
            val animation = AnimationUtils.loadAnimation(activity, com.example.cleanmaster.R.anim.rotate)
            animation.interpolator = LinearInterpolator()
            animation.repeatCount = Animation.INFINITE
            animation.duration = 1000
            this.startAnimation(animation)
        }
        
                fun ImageView.load(any: Any?) {
            Glide.with(this).load(any)
                .placeholder(com.example.cleanmaster.R.drawable.ic_launcher_background).into(this@load)
            this.visible()
        }

        fun ImageView.tint(any: Any?) {
            this.imageTintList =
                ColorStateList.valueOf(if (any is Int) any else Color.parseColor(any as String))
        }

        fun shareFile(activity: Activity, item: String) {
            ("Item: $item").log()
            val share = Intent(Intent.ACTION_SEND)
            share.type = "${if (item.endsWith(".png") || item.endsWith(".jpg")) "image" else "video"}/*"
            share.putExtra(Intent.EXTRA_STREAM,
                    FileProvider.getUriForFile(activity, activity.packageName + ".provider",
                            File(item), item.lowercase(Locale.getDefault())))
            share.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
            activity.startActivity(Intent.createChooser(share, "Share ${if (item.endsWith(".png") || item.endsWith(".jpg")) "Image" else "Video"}"))
        }

        fun File.calculateSizeRecursively(): Long {
            return walkBottomUp().fold(0L) { acc, file -> acc + file.length() }
        }
         fun TextView.underline() {
            this.paintFlags = this.paintFlags or Paint.UNDERLINE_TEXT_FLAG
        }

            <rotate
                android:duration="1400"
                android:fromDegrees="0"
                android:interpolator="@android:anim/linear_interpolator"
                android:pivotX="50%"
                android:pivotY="50%"
                android:repeatCount="infinite"
                android:repeatMode="restart"
                android:toDegrees="360" />
            />
        </set>

        fun Any?.log() = exc { Log.wtf("FATZ", "$this") }
        
        fun Exception.print() {
            printStackTrace()
            "$message | ${javaClass.name}".log()
            FirebaseCrashlytics.getInstance().recordException(this)
        }

            fun Activity.imagetobitmap(any: Any, onBitmap: (Bitmap) -> Unit) {
        Glide.with(this).asBitmap().load(any).into(object : BitmapImageViewTarget(ImageView(this)) {
            override fun onResourceReady(resource: Bitmap, transition: Transition<in Bitmap>?) {
                onBitmap.invoke(resource)
            }
        })
    }

        fun Context.showAlertDialog(positionBtnText: String, title: String, subTitle: String, onOk: () -> Unit) {
            val dialog = MaterialAlertDialogBuilder(this)
                    .setTitle(title)
                    .setMessage(subTitle)
                    .setBackground(ContextCompat.getDrawable(this, R.drawable.bg_alert))
                    .setCancelable(true)
                    .setNegativeButton("No") { dialog, _ ->
                        dialog.dismiss()
                    }
                    .setPositiveButton("Yes") { dialog, _ ->
                        onOk()
                        dialog.dismiss()
                    }
                    .show()

            dialog.getButton(AlertDialog.BUTTON_NEGATIVE).setTextColor(ContextCompat.getColorStateList(this, R.color.black))
            dialog.getButton(AlertDialog.BUTTON_NEGATIVE).setTypeface(null, Typeface.BOLD)

            dialog.getButton(AlertDialog.BUTTON_POSITIVE).setTextColor(ContextCompat.getColorStateList(this, R.color.black))
            dialog.getButton(AlertDialog.BUTTON_POSITIVE).setTypeface(null, Typeface.BOLD)
        }
        
        fun htmlTagHorizontal(txt: String, value: Any): Spanned = "<font color=#969696><small>$txt</small></font>${value}".html()

          fun View.gon() {
              this.visibility = GONE
          }

          fun View.invisible() {
              this.visibility = INVISIBLE
          }

          fun <T> T.tos(ctx: Context) = Toast.makeText(ctx, "$this", Toast.LENGTH_SHORT).show()
          fun <T> T.tosL(ctx: Context) = Toast.makeText(ctx, "$this", Toast.LENGTH_LONG).show()

        fun dayDiff(startDate: Long, endDate: Long = System.currentTimeMillis()): Int {

         val dateFormat = "dd/MM/yyyy"
         val df = SimpleDateFormat(dateFormat)

         val startDay = df.parse(DateFormat.format(dateFormat, Date(startDate)).toString())
         val endDay = df.parse(DateFormat.format(dateFormat, Date(endDate)).toString())

         return (abs(startDay.time - endDay.time) / (24 * 60 * 60 * 1000)).toInt()
     }

     fun View.animateViewXtrigger() {
    val animation: Animation = TranslateAnimation(this.x - 5, this.x + 5.toFloat(), 0f, 0f)
    animation.duration = 50 // Set the duration of the animation in milliseconds
    animation.repeatCount = 6
    this.startAnimation(animation)
    }

     fun longToDate(millis: Long) = DateFormat.format("dd/MM/yyyy", Date(millis)).toString()

     fun dateToDate(date: Date) = DateFormat.format("dd/MM/yyyy", date).toString()

     fun longToDayOfMonth(millis: Long) = DateFormat.format("dd", Date(millis)).toString().toInt()

     fun String.html() = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) Html.fromHtml(this, Html.FROM_HTML_MODE_LEGACY) else HtmlCompat.fromHtml(this, HtmlCompat.FROM_HTML_MODE_LEGACY)

     fun disableBtn(v: View) {
         v.isEnabled = false
         Handler(Looper.getMainLooper()).postDelayed({ v.isEnabled = true }, 2000)
     }

     fun View.removeFromParent() {
         if (parent != null) (parent as ViewGroup).removeView(this)
     }

     fun onBackground(block: () -> Unit) {
         Executors.newSingleThreadExecutor().execute {
             block()
         }
     }
     
     fun delayInMillis(millis: Long, block: () -> Unit) {
    Handler(Looper.getMainLooper()).postDelayed({
        block()
    }, millis)
    }

     inline fun exc(block: () -> Unit) {
         try {
             block()
         } catch (e: Exception) {
             e.print()
         }
     }

     fun openUri(ctx: Context, uri: String) = ctx.startActivity(Intent(Intent.ACTION_VIEW, Uri.parse(uri)))

     fun isServiceRunning(ctx: Context, serviceClass: Class<*>): Boolean {
         for (service in (ctx.getSystemService(AppCompatActivity.ACTIVITY_SERVICE) as ActivityManager).getRunningServices(Int.MAX_VALUE)) {
             if (serviceClass.name == service.service.className) return true
         }
         return false
     }

// Download From notification 

     private fun enqueueDownload(context: Context) {
        val fileName = "${System.currentTimeMillis()}.png"
        val destination = "${Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DCIM).path}/$fileName"

        val file = File(destination)
        if (file.exists()) file.delete()

        val request = DownloadManager.Request(Uri.parse(url))
                .setMimeType("*/*")
                .setTitle(fileName)
                .setDescription("downloading")
                .setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE)
                .setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED)
                .setDestinationUri(Uri.parse("file://$destination"))

        (getSystemService(Context.DOWNLOAD_SERVICE) as DownloadManager).enqueue(request)
        "downloading".tosL()
    }

//BaseAct

    abstract class BaseAct<T : ViewBinding> : AppCompatActivity() {
    lateinit var bind: T
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        bind = getActivityBinding(layoutInflater)
        setContentView(bind.root)
                 if (Build.VERSION.SDK_INT > Build.VERSION_CODES.Q) {
            window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS)
            window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS)
            window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
            window.statusBarColor = Color.TRANSPARENT
        } else {
            window.setFlags(
                WindowManager.LayoutParams.FLAG_FULLSCREEN,
                WindowManager.LayoutParams.FLAG_FULLSCREEN
            )
        }
           //        window.setFlags(
         //            WindowManager.LayoutParams.FLAG_SECURE,
         //            WindowManager.LayoutParams.FLAG_SECURE
         //        )

        exc { initUI() }
    }


    abstract fun getActivityBinding(inflater: LayoutInflater): T

    abstract fun initUI()
    
    fun <T> T.tos() = Toast.makeText(this@BaseAct, "$this", Toast.LENGTH_SHORT).show()
    fun <T> T.tosL() = Toast.makeText(this@BaseAct, "$this", Toast.LENGTH_LONG).show()

    fun openUri(uri: String) = startActivity(Intent(Intent.ACTION_VIEW, Uri.parse(uri)))

    fun rateUs() {
        try {
            val marketUri = Uri.parse("market://details?id=$packageName")
            val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
            startActivity(marketIntent)
        } catch (e: Exception) {
            e.print()
            val marketUri = Uri.parse("https://play.google.com/store/apps/details?id=$packageName")
            val marketIntent = Intent(Intent.ACTION_VIEW, marketUri)
            startActivity(marketIntent)
        }
    }

    fun shareUs() {
        val i = Intent(Intent.ACTION_SEND)
                .putExtra(
                        Intent.EXTRA_TEXT,
                        "I'm using ${getString(R.string.app_name)}! Get the app for free at http://play.google.com/store/apps/details?id=${packageName}"
                )
        i.type = "text/plain"
        startActivity(Intent.createChooser(i, "Share"))
    }

    fun canWrite(): Boolean {
        if (ContextCompat.checkSelfPermission(
                        this,
                        Manifest.permission.WRITE_EXTERNAL_STORAGE
                ) != PackageManager.PERMISSION_GRANTED
        ) {
            ActivityCompat.requestPermissions(
                    this,
                    arrayOf(Manifest.permission.WRITE_EXTERNAL_STORAGE),
                    111
            )
            return false
        }
        return true
    }

     fun openApp(pkg: String) {
            if (hasInternetConnect())
                try {
                    startActivity(Intent(packageManager.getLaunchIntentForPackage(pkg)));
                } catch (e: java.lang.Exception) {
                    "App is not install".tos()
                    try {
                        startActivity(
                                Intent(
                                        Intent.ACTION_VIEW,
                                        Uri.parse("market://details?id=$pkg")
                                )
                        )
                    } catch (anfe: ActivityNotFoundException) {
                        startActivity(
                                Intent(
                                        Intent.ACTION_VIEW,
                                        Uri.parse("https://play.google.com/store/apps/details?id=$pkg")
                                )
                        )
                    }
                    e.print().log()
                }
            else
                ("Internet connection error").tos()
        }

        fun openUri(uri: String) = startActivity(Intent(Intent.ACTION_VIEW, Uri.parse(uri)))

     fun hasInternetConnect(): Boolean {
            var isWifiConnected = false
            var isMobileConnected = false
            val cm = getSystemService(CONNECTIVITY_SERVICE) as ConnectivityManager

            if (cm.defaultProxy != null) return false

            for (ni in cm.allNetworkInfo) {
                if (ni.typeName.equals("WIFI", ignoreCase = true)) if (ni.isConnected) isWifiConnected = true
                if (ni.typeName.equals("MOBILE", ignoreCase = true)) if (ni.isConnected) isMobileConnected = true
            }

            return isWifiConnected || isMobileConnected
        }

        fun isVPN(context: Context): Boolean {
            val cm = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
            cm.getNetworkCapabilities(cm.activeNetwork)?.let {
                if (it.hasTransport(NetworkCapabilities.TRANSPORT_VPN)) return true
            }
            return false
        }


        open fun isCertificateInstall(): Boolean {
            var iface = ""
            try {
                for (networkInterface in Collections.list(NetworkInterface.getNetworkInterfaces())) {
                    if (networkInterface.isUp) iface = networkInterface.name
                    if (iface.contains("tun") || iface.contains("ppp") || iface.contains("pptp")) {
                        return true
                    }
                }
            } catch (e1: SocketException) {
                e1.printStackTrace()
                return false
            }
            return false
        }

        fun shareUs() {
            val i = Intent(Intent.ACTION_SEND)
                    .putExtra(
                            Intent.EXTRA_TEXT,
                                "I'm using ${getString(com.example.socialvideodownloader.R.string.app_name)}! Get the app for free at                http://play.google.com/store/apps/details?id=${packageName}"
                        )
                i.type = "text/plain"
                startActivity(Intent.createChooser(i, "Share"))
            }


        fun isMyPackedgeInstalled(packageName: String?): Boolean {
            return try {
                packageManager.getPackageInfo(packageName!!, 0)
                true
            } catch (e: PackageManager.NameNotFoundException) {
                false
            }
          }

        fun getRandomNumber(bound: Int) = Random().nextInt(bound)

        open fun getFilenameFromURL(str: String?): String? {
            return try {
                val stringBuilder = StringBuilder()
                stringBuilder.append(File(URL(str).path).name)
                stringBuilder.append("")
                stringBuilder.toString()
            } catch (str2: java.lang.Exception) {
                str2.printStackTrace()
                System.currentTimeMillis().toString()
            }
        }

        open fun getImageFilenameFromURL(url: String?): String? {
            return try {
                File(URL(url).path).name
            } catch (e: java.lang.Exception) {
                e.printStackTrace()
                System.currentTimeMillis().toString() + ".png"
            }
        }

        open fun getVideoFilenameFromURL(url: String?): String? {
            return try {
                File(URL(url).path).name
            } catch (e: java.lang.Exception) {
                e.printStackTrace().log()
                System.currentTimeMillis().toString() + ".mp4"
            }
        }


 //Genrate Token

    const genrateToken = (username) => {
    const token = jwt.sign({ username: username },
        process.env.jwt_sec_key, {
        expiresIn: '3y'
    })
    return token
    }


//Multer: 

     const imageUpload = multer({
    storage: multer.diskStorage({
        // Destination to store image     
        destination: 'images',
        filename: (req, file, cb) => {
            cb(null, file.fieldname + '_' + Date.now()
                + ".png")
            console.log("Field Name: " + file.fieldname + '_' + Date.now() + ".png")

        }
    }),

    limits: {
        fileSize: 5000000 // 5000000 Bytes = 5 MB
    },
    fileFilter(req, file, cb) {
        if (!file.originalname.match(/\.(png|jpg|jpeg)$/)) {
            // upload only png and jpg format
            return cb(new Error('Please upload valide Image'))
        }
        cb(undefined, true)
    }
})


//Check Auth Token

    const cheackUserAuth = async (req, res, next) => {
    const token = req.headers
    let token_verify
    try {
        token_verify = JSON.stringify(token).split(" ")[1].split(`"`)[0].trim();
    } catch (error) {
        console.log("Error: " + error);
    }

    console.log("Verify Token: " + token_verify);

    if (token_verify) {
        try {
            // verify token 
            const muserdata = await userdata.findOne({ token: token_verify });

            if (muserdata == null) {
                res.status(TOKEN_UNATHORIZE_CODE).send({ status: false, message: "Token not found" });
            } else {
                req.query.tokendata = muserdata
                next();
            }

        } catch (err) {
            console.log(err);
            res.status(TOKEN_UNATHORIZE_CODE).send({ message: "Token UnAthorized User" })
        }
    }
    else {
        res.status(TOKEN_UNATHORIZE_CODE).send({ status: false, message: "Token Not Add" })
    }
    }


//Validation Mongoos

    "type": "String",
        required: 'Email address is required',
        "unique": true,
        lowercase: true,
        trim: true,
        validate: [validateEmail, 'Please fill a valid email address'],
        match: [/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/, 'Please fill a valid email address']

   var validateEmail = function (email) {
    var re = /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/;
    return re.test(email)
   };


// Connect MongoDB

  mongoose.connect(DATA_BASE_PATH).then(() => {
    console.log(`MongoDB connection Don`);
  }).catch((error) => {
    console.log(`Error`);
  })



//check if srvice is running or not

        fun isServiceRunning(ctx: Context, serviceClass: Class<*>): Boolean {
            for (service in (ctx.getSystemService(AppCompatActivity.ACTIVITY_SERVICE) as ActivityManager).getRunningServices(Int.MAX_VALUE)) {
                if (serviceClass.name == service.service.className) return true
            }
            return false
        }
        
        

//check net vpn proxy

        public boolean hasInternetConnect() {
        boolean haveConnectedWifi = false;
        boolean haveConnectedMobile = false;

        ConnectivityManager cm = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);

        if (cm.getNetworkCapabilities(cm.getActiveNetwork()).hasTransport(NetworkCapabilities.TRANSPORT_VPN)) return false;

        if (cm.getDefaultProxy() != null) return false;

        NetworkInfo[] netInfo = cm.getAllNetworkInfo();
        for (NetworkInfo ni : netInfo) {
            if (ni.getTypeName().equalsIgnoreCase("WIFI"))
                if (ni.isConnected())
                    haveConnectedWifi = true;
            if (ni.getTypeName().equalsIgnoreCase("MOBILE"))
                if (ni.isConnected())
                    haveConnectedMobile = true;
        }

        if (isCertificateInstall()) return false;

        if (haveConnectedWifi || haveConnectedMobile) {

        } else {
            tos("Network Not Available");
        }

        return haveConnectedWifi || haveConnectedMobile;
        }
    
        public boolean isCertificateInstall() {
        String iface = "";
        try {
            for (NetworkInterface networkInterface : Collections.list(NetworkInterface.getNetworkInterfaces())) {
                if (networkInterface.isUp())
                    iface = networkInterface.getName();
                if (iface.contains("tun") || iface.contains("ppp") || iface.contains("pptp")) {
                    return true;
                }
            }
        } catch (SocketException e1) {
            e1.printStackTrace();
            return false;
        }

        return false;
        }
        

//emulator detection

    private fun checkBuildConfig(): Boolean {
    var isEmulator = (Build.MANUFACTURER.contains("Genymotion") 
            || Build.MODEL.contains("google_sdk")
            || Build.MODEL.toLowerCase().contains("droid4x")
            || Build.MODEL.contains("Emulator")
            || Build.MODEL.contains("Android SDK built for x86")
            || Build.HARDWARE == "goldfish"
            || Build.HARDWARE == "vbox86"
            || Build.HARDWARE.toLowerCase().contains("nox")
            || Build.FINGERPRINT.startsWith("generic")
            || Build.PRODUCT == "sdk"
            || Build.PRODUCT == "google_sdk"
            || Build.PRODUCT == "sdk_x86"
            || Build.PRODUCT == "vbox86p"
            || Build.PRODUCT.toLowerCase().contains("nox")
            || Build.BOARD.toLowerCase().contains("nox")
            || (Build.BRAND.startsWith("generic") &&    Build.DEVICE.startsWith("generic")))
    return isEmulator
          }

    public Boolean isEmulator() {
        @SuppressLint("HardwareIds") String androidId = Settings.Secure.getString(getContentResolver(), "android_id");
        return Build.PRODUCT.contains("sdk") || Build.HARDWARE.contains("goldfish") || Build.HARDWARE.contains("ranchu") || androidId == null;
    }
    
    //by facebook
    @JvmStatic
    fun isEmulator(): Boolean {
      return Build.FINGERPRINT.startsWith("generic") ||
          Build.FINGERPRINT.startsWith("unknown") ||
          Build.MODEL.contains("google_sdk") ||
          Build.MODEL.contains("Emulator") ||
          Build.MODEL.contains("Android SDK built for x86") ||
          Build.MANUFACTURER.contains("Genymotion") ||
          Build.BRAND.startsWith("generic") && Build.DEVICE.startsWith("generic") ||
          "google_sdk" == Build.PRODUCT
    }

//root detedction

    //by oneSignal
    static boolean isRooted() {
        String[] places = new String[]{"/sbin/", "/system/bin/", "/system/xbin/", "/data/local/xbin/", "/data/local/bin/", "/system/sd/xbin/",                                                  "/system/bin/failsafe/", "/data/local/"};

        try {
            String[] var1 = places;
            int var2 = places.length;

            for(int var3 = 0; var3 < var2; ++var3) {
                String where = var1[var3];
                if ((new File(where + "su")).exists()) {
                    return true;
                }
            }
        } catch (Throwable var5) {
        }
        return false;
    }
    
    //by crashlytics
    public static boolean isRooted(Context context) {
        boolean isEmulator = isEmulator(context);
        String buildTags = Build.TAGS;
        if (!isEmulator && buildTags != null && buildTags.contains("test-keys")) {
            return true;
        } else {
            File file = new File("/system/app/Superuser.apk");
            if (file.exists()) {
                return true;
            } else {
                file = new File("/system/xbin/su");
                return !isEmulator && file.exists();
            }
        }
    }
    
    

//image loading with progressbasr or gif

            val cpd = CircularProgressDrawable(this)
            cpd.strokeWidth = 5f
            cpd.centerRadius = 30f
            cpd.setColorSchemeColors(Color.GREEN,Color.CYAN,Color.LTGRAY)
            cpd.start()

            Glide.with(this)
                    .load("https://preview.redd.it/c3uhsgo1vx541.jpg?auto=webp&s=a45b583ebf921d3ad1649e77ad05e55226140120")
                    .skipMemoryCache(true)
                    .diskCacheStrategy(DiskCacheStrategy.NONE)
                    .placeholder(cpd)                                    // comment when applying gif
    //              .thumbnail(Glide.with(activity).load("https://i.gifer.com/ZKZx.gif"))   //for gif
    //              .fitCenter()                                         //may be optional
                    .error(R.drawable.liked)
                    .into(bind.iv)



//pick font and copy to catch dir

                         val fontLauncher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->
                                if (result.resultCode == Activity.RESULT_OK) {

                                    val uri: Uri = result.data!!.data!!
                                   // val source = File(FileUtils.getPath(requireActivity(), uri))
                                    val destination = File(requireActivity().cacheDir.absolutePath + "/font/" + uri.path!!.split("/").last())

                                    if (destination.exists() && destination.length() != 0L)
                                        "File exists".tos()

                                    else {
                                        File(destination.parent!!).let { if (!it.exists()) it.mkdir() }
                                        destination.createNewFile()

                                        requireActivity().contentResolver.openInputStream(uri).use { inputStream ->
                                            destination.outputStream().use { output ->
                                                inputStream!!.copyTo(output)
                                            }
                                        }
                                    }
                                }
                            }

                            bind.btnAddFont.setOnClickListener {
                                fontLauncher.launch(
                                        Intent(Intent.ACTION_GET_CONTENT)
                                                .setType("*/*")
                                                .putExtra(Intent.EXTRA_MIME_TYPES, arrayOf("font/ttf", "font/otf", "application/vnd.ms-fontobject",   "font/woff", "font/woff2"))
                                )
                            }
                

//make list of time interval

            val sdf = SimpleDateFormat("yyyy-MM-dd HH:mm", Locale.ENGLISH)

            val dateStart = sdf.parse("${startDate()} ${startHm()}") as Date
            val dateEnd = sdf.parse("${startDate()} 24:00") as Date

            ("Date Start: $dateStart").log()
            ("Date End: $dateEnd").log()

            var list = arrayListOf<String>()

            var dif: Long = dateStart.time
            while (dif < dateEnd.time) {
                val slot = SimpleDateFormat("HH:mm", Locale.ENGLISH).format(Date(dif))
                "Hour Slot --> $slot".log()
                list.add(slot)
                dif += 1800000 //for 1 hour make it 3600000
            }
            
            // currentDate
            fun startDate(): String = SimpleDateFormat("yyyy-MM-dd", Locale.ENGLISH).format(Date())

            //gives HH:00 or HH:30 only (Ex. 01:30, 02:00 etc..)
            fun startHm(): String {
                val h = SimpleDateFormat("HH", Locale.ENGLISH).format(Date())

                SimpleDateFormat("mm", Locale.ENGLISH).format(Date()).let {
                    return if (it.toInt() > 30) (h+1)+":00" else "$h:30"
                }
            }
            
            //extras
            fun getSpecificTimeInMillis() : Long = SimpleDateFormat("yyyy-MM-dd hh:mm:ss a", Locale.ENGLISH).parse(startDate() + " 11:30:00 pm")!!.time


// AlarmManager

    public void startAlert() {
        int second = Integer.parseInt(binding.et.getText().toString());

        ((AlarmManager) getSystemService(ALARM_SERVICE))
                .set(
                        AlarmManager.RTC_WAKEUP,
                        System.currentTimeMillis() + (second * 1000L),
                        PendingIntent.getBroadcast(
                                getApplicationContext(),
                                111,
                                new Intent(this, MyBroadcastReceiver.class),
                                FLAG_IMMUTABLE)
                );
        Toast.makeText(this, "Alarm set in " + second + " seconds", Toast.LENGTH_SHORT).show();
    }

      public class MyBroadcastReceiver extends BroadcastReceiver {

          @Override
          public void onReceive(Context context, Intent intent) {
              rcv();
          }

          private void rcv() {
              new Handler(Looper.getMainLooper()).postDelayed(() -> {
                  Log.i("TAG", "run: ");
                  rcv();
              },2000);
          }
      }

      in AndroidManifest.xml

      <receiver android:name="MyBroadcastReceiver" />



//App link assitant steps

      upload .well-known/assetlinks.json folder in website
      iside assetlinks.json put following	
      [{
        "relation": ["delegate_permission/common.handle_all_urls"],
        "target": {
          "namespace": "android_app",
          "package_name": "com.example.yourapp",
          "sha256_cert_fingerprints":
          ["98:56:DB:F3:C0:FC:BA: -------------  sha 256 ------------ :B5:13:73:36:4E:53:A6:F3"]
        }
      }]

      now in android studio tools>App Link Assistant> click

      1 Add URL intent filters -> put path of assetlinks.json in Check URL Mapping
            Ex. = https://YourHost.com/.well-known/assetlinks.json

            add activity that will onpen on link click

            and make sure below is in menifst of added activity

            <intent-filter>
                  <action android:name="android.intent.action.VIEW" />

                      <category android:name="android.intent.category.DEFAULT" />
                      <category android:name="android.intent.category.BROWSABLE" />

                      <data
                          android:host="YourHost.com"
                          android:scheme="https" />
               </intent-filter>

      2 Add logica t handle the intent -> put below in selected activity

            // ATTENTION: This was auto-generated to handle app links.
              Intent appLinkIntent = getIntent();
              String appLinkAction = appLinkIntent.getAction();
              Uri appLinkData = appLinkIntent.getData();

      3 Associate website -> you will know what to do

      4 Test on device or emulator -> in URL must start with "YourHost.com" after put whatever you want
            Ex. https://YourHost.com/whatever you want

            That's It!!
      
      
//to not change comfiguration
      
      in activity tag of manifest file
      android:configChanges="uiMode"

//for light mode only

      //in theme
     <item name="android:forceDarkAllowed" tools:targetApi="q">false</item>

//pager adapter

     class FGPagerAdapter(fm: FragmentManager) : FragmentPagerAdapter(fm) {
              private var fragmentList: MutableList<Fragment> = ArrayList()

              fun add(fragment: Fragment) {
                  fragmentList.add(fragment)
              }

              override fun getItem(position: Int): Fragment {
                  return fragmentList[position]
              }

              override fun getCount() = fragmentList.size
          }



//save vide from tree uri

          private void saveVideoFromTreeUri(Uri uri) {
        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.Q) {
            String videoName = System.currentTimeMillis() + ".mp4";

            ContentValues valuesvideos = new ContentValues();
            valuesvideos.put(MediaStore.Video.Media.RELATIVE_PATH, Environment.DIRECTORY_DOWNLOADS);
            valuesvideos.put(MediaStore.Video.Media.TITLE, videoName);
            valuesvideos.put(MediaStore.Video.Media.DISPLAY_NAME, videoName);
            valuesvideos.put(MediaStore.Video.Media.MIME_TYPE, "video/mp4");
            valuesvideos.put(MediaStore.Video.Media.DATE_ADDED, System.currentTimeMillis() / 1000);
            valuesvideos.put(MediaStore.Video.Media.DATE_TAKEN, System.currentTimeMillis());
            valuesvideos.put(MediaStore.Video.Media.IS_PENDING, 1);
            ContentResolver resolver = getContentResolver();
            //for  [DCIM, Movies, Pictures] use MediaStore.Video.Media.getContentUri(MediaStore.VOLUME_EXTERNAL_PRIMARY) as insert uri
            Uri uriSavedVideo = resolver.insert(MediaStore.Downloads.EXTERNAL_CONTENT_URI, valuesvideos);

            ParcelFileDescriptor pfd;

            try {
                pfd = getContentResolver().openFileDescriptor(uriSavedVideo, "w");
                assert pfd != null;
                FileOutputStream out = new FileOutputStream(pfd.getFileDescriptor());
                // Get the already saved video as fileinputstream from here
                InputStream in = getContentResolver().openInputStream(uri);
                byte[] buf = new byte[8192];
                int len;
                int progress = 0;
                while ((len = in.read(buf)) > 0) {
                    progress = progress + len;
                    out.write(buf, 0, len);
                }
                out.close();
                in.close();
                pfd.close();
                valuesvideos.clear();
                valuesvideos.put(MediaStore.Video.Media.IS_PENDING, 0);
                valuesvideos.put(MediaStore.Video.Media.IS_PENDING, 0); //only your app can see the files until pending is turned into 0
                getContentResolver().update(uriSavedVideo, valuesvideos, null, null);
                File file = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS) + "/" + videoName);

                MediaScannerConnection.scanFile(this, new String[]{file.getAbsolutePath()}, new String[]{"video/*"}, null);

            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }


//open Dir with Doc Tree

          if (android.os.Build.VERSION.SDK_INT > Build.VERSION_CODES.Q) {
                if (DocumentFile.fromTreeUri(StatusActivity.this, Uri.parse(pref.getString("uri"))).canRead()) {

                } else {
                    Uri finalUri = DocumentFile
                            .fromTreeUri(
                                    StatusActivity.this,
                            Uri.parse("content://com.android.externalstorage.documents/tree/primary%3AAndroid%2Fmedia%2Fcom.whatsapp%2FWhatsApp%2FMedia%2F.Statuses")
                            ).getUri();

                    launcher.launch(
                            new Intent(Intent.ACTION_OPEN_DOCUMENT_TREE)
                                    .putExtra(DocumentsContract.EXTRA_INITIAL_URI, finalUri)
                                    .addFlags(Intent.FLAG_GRANT_PERSISTABLE_URI_PERMISSION | Intent.FLAG_GRANT_READ_URI_PERMISSION | Intent.FLAG_GRANT_WRITE_URI_PERMISSION)
                    );
                }
            }
                      
                      

//Api Client In Test Be Carefull

          public class ApiClient<T> {

              Activity activity;
              Dialog progress;
              DialogNoNet dialogNoNet;

              public ServiceGenerator<T> serviceGenerator;

              public interface ServiceGenerator<T> {
                  void OnSuccess(T obj);
              }

              public void callApiWithDialog(Activity activity, View root, Call<T> apiCall, ServiceGenerator<T> serviceGenerator) {
                  this.activity = activity;
                  this.serviceGenerator = serviceGenerator;

                  if (hasInternetConnect()) {
                      dismissNoNet();
                      showProgress();
                      apiCall.clone().enqueue(new Callback<T>() {
                          @Override
                          public void onResponse(@NonNull Call<T> call, @NonNull Response<T> response) {
                              new Handler(Looper.getMainLooper()).postDelayed(() -> dismissProgress(), 200);

                              if (response.isSuccessful() && response.body() != null) {
                                  serviceGenerator.OnSuccess(response.body());

                              } else {
                                  Snackbar.make(activity.findViewById(android.R.id.content), somethingWentWrong, Snackbar.LENGTH_INDEFINITE)
                                          .setAction("Try Again", view -> callApiWithDialog(activity, root, apiCall, serviceGenerator)).show();
                              }
                          }

                          @Override
                          public void onFailure(@NonNull Call<T> call, @NonNull Throwable t) {
                              dismissProgress();
                              t.printStackTrace();

                              Snackbar.make(activity.findViewById(android.R.id.content), somethingWentWrong, Snackbar.LENGTH_INDEFINITE)
                                      .setAction("Try Again", view -> callApiWithDialog(activity, root, apiCall, serviceGenerator)).show();

                          }
                      });

                  } else {
                      showNoNet();
                      dialogNoNet.setOnRetryClickListener(new DialogNoNet.RootClickEvent() {
                          @Override
                          public void onRetryClick(@NonNull Dialog dialog) {
                              callApiWithDialog(activity, root, apiCall, serviceGenerator);
                          }
                      });
                  }
              }

              public void callApiWithBar(Activity activity, View root, ProgressBar pb, Call<T> apiCall, ServiceGenerator<T> serviceGenerator) {
                  this.activity = activity;
                  this.serviceGenerator = serviceGenerator;

                  if (hasInternetConnect()) {
                      dismissNoNet();
                      pb.setVisibility(View.VISIBLE);
                      apiCall.clone().enqueue(new Callback<T>() {
                          @Override
                          public void onResponse(@NonNull Call<T> call, @NonNull Response<T> response) {
                              pb.setVisibility(View.GONE);

                              if (response.isSuccessful() && response.body() != null) {
                                  serviceGenerator.OnSuccess(response.body());

                              } else {
                                  Snackbar.make(activity.findViewById(android.R.id.content), somethingWentWrong, Snackbar.LENGTH_INDEFINITE)
                                          .setAction("Try Again", view -> callApiWithBar(activity, root, pb, apiCall, serviceGenerator)).show();
                              }
                          }

                          @Override
                          public void onFailure(@NonNull Call<T> call, @NonNull Throwable t) {
                              pb.setVisibility(View.GONE);
                              t.printStackTrace();

                             Snackbar.make(
                              root, t instanceof SocketTimeoutException ? "Time out" : somethingWentWrong, Snackbar.LENGTH_INDEFINITE
                              ).setAction(
                                   t instanceof SocketTimeoutException ? "Try again" : "dismiss", view -> {
                                if (t instanceof SocketTimeoutException) {
                                    callApiWithBar(activity, root, pb, apiCall, serviceGenerator);
                                }
                            }).show();
                              Snackbar.make(activity.findViewById(android.R.id.content), somethingWentWrong, Snackbar.LENGTH_INDEFINITE)
                                      .setAction("Try Again", view -> callApiWithBar(activity, root, pb, apiCall, serviceGenerator)).show();

                          }
                      });

                  } else {
                      showNoNet();
                      dialogNoNet.setOnRetryClickListener(new DialogNoNet.RootClickEvent() {
                          @Override
                          public void onRetryClick(@NonNull Dialog dialog) {
                              callApiWithBar(activity, root, pb, apiCall, serviceGenerator);
                          }
                      });
                  }
              }


              public static Api getService() {
                  HttpLoggingInterceptor interceptor = new HttpLoggingInterceptor();     //todo remove this line in production
                  interceptor.setLevel(HttpLoggingInterceptor.Level.BODY);               //todo remove this line in production

                  OkHttpClient okClient = new OkHttpClient.Builder()
                          .addInterceptor(chain -> {
                              Request originalRequest = chain.request();
                              Request.Builder requestBuilder = originalRequest.newBuilder()
                                      .addHeader("Cache-Control", "no-cache")
                                      .method(originalRequest.method(), originalRequest.body());
                              Request request = requestBuilder.build();
                              return chain.proceed(request);
                          })
                          .addInterceptor(interceptor)                                    //todo remove this line in production
                          .connectTimeout(10, TimeUnit.SECONDS)
                          .readTimeout(10, TimeUnit.SECONDS)
                          .build();


                  return new Retrofit.Builder()
                          .baseUrl(cons.BASE_URL)
                          .addConverterFactory(GsonConverterFactory.create())
                          .client(okClient)
                          .build()
                          .create(Api.class);
              }

              private void initNoNetDialog() {
                  dialogNoNet = new DialogNoNet(activity);
              }

              private void showNoNet() {
                  if (dialogNoNet == null) initNoNetDialog();
                  dialogNoNet.show();
              }

              private void dismissNoNet() {
                  if (dialogNoNet != null) dialogNoNet.dismiss();
              }

              private void initProgressDialog() {
                  progress = new Dialog(activity);
                  progress.setContentView(DialogProgressBinding.inflate(activity.getLayoutInflater()).getRoot());
                  int widthAndHeight = WindowManager.LayoutParams.MATCH_PARENT;
                  progress.getWindow().setLayout(widthAndHeight, widthAndHeight);
                  progress.getWindow().setBackgroundDrawableResource(android.R.color.transparent);
                  progress.setCancelable(false);
              }

              public void showProgress() {
                  if (progress == null) initProgressDialog();
                  if (!progress.isShowing()) progress.show();
              }

              public void dismissProgress() {
                  if (progress != null && progress.isShowing()) progress.dismiss();
              }

              public boolean hasInternetConnect() {
                  boolean isWifiConnected = false;
                  boolean isMobileConnected = false;

                  NetworkInfo[] netInfo = ((ConnectivityManager) activity.getSystemService(Context.CONNECTIVITY_SERVICE)).getAllNetworkInfo();

                  for (NetworkInfo ni : netInfo) {
                      if (ni.getTypeName().equalsIgnoreCase("WIFI"))
                          if (ni.isConnected()) isWifiConnected = true;
                      if (ni.getTypeName().equalsIgnoreCase("MOBILE"))
                          if (ni.isConnected()) isMobileConnected = true;
                  }
                  return isWifiConnected || isMobileConnected;
              }
          }



//view to bitmap 

     /**
     *if dose not work call this method inside View.post() method
     **/
    public Bitmap bitmapFromView(View v) {
        Bitmap b = Bitmap.createBitmap(v.getWidth(), v.getHeight(), Bitmap.Config.ARGB_8888);
        v.layout(v.getLeft(), v.getTop(), v.getRight(), v.getBottom());
        v.draw(new Canvas(b));
        return b;
    }



//simple animation

            public void slideUpFad(View v) {
                    v.setVisibility(View.VISIBLE);
                    v.setTranslationY(v.getHeight());
                    v.setAlpha(0f);
                    v.animate().setDuration(600)
                            .translationY(0)
                            .setListener(new AnimatorListenerAdapter() {
                                @Override
                                public void onAnimationEnd(Animator animation) {
                                    super.onAnimationEnd(animation);
                                }
                            }).alpha(1f).start();
                }

//Unsplash Api Calling

            private const val BASE_URL = "https://api.unsplash.com/"

            const val AccessKey = "akYdbrQ-RcwiLMcEkHuunsplash_2FUTsHJ25k42aaaO67N4"

            interface Api {
            @GET("search/photos")
            suspend fun getSearchPhotos(@Query("client_id") clientId: String,
                                        @Query("orientation") orientation: String,
                                        @Query("query") searchItem: String?,
                                        @Query("per_page") itemPerPage: Int): Response<SearchPhoto>


            companion object {
                private var okClient: OkHttpClient = OkHttpClient.Builder().addInterceptor(
                        Interceptor { chain: Interceptor.Chain ->
                            val originalRequest = chain.request()
                            chain.proceed(
                                    originalRequest.newBuilder()
                                            .addHeader("Cache-Control", "no-cache")
                                            .method(originalRequest.method, originalRequest.body).build()
                            )
                        })
                        .addInterceptor(HttpLoggingInterceptor().setLevel(HttpLoggingInterceptor.Level.BODY)) //todo remove this line in production
                        .connectTimeout(12, TimeUnit.SECONDS)
                        .readTimeout(12, TimeUnit.SECONDS)
                        .build()

                val service: Api = Retrofit.Builder()
                        .baseUrl(BASE_URL)
                        .addConverterFactory(GsonConverterFactory.create())
                        .client(okClient)
                        .build().create(Api::class.java)
                }
            }



//read asset file from asset file path

            public String readAssetsFile(String assetFilePath) {
                    String jsonString = "";
                    try {
                        InputStream is = getAssets().open(assetFilePath);
                        byte[] buffer = new byte[is.available()];
                        is.read(buffer);
                        is.close();
                        jsonString = new String(buffer, StandardCharsets.UTF_8);
                    } catch (Exception e) {
                        Log.e(TAG, e.getMessage());
                    }
                    return jsonString;
                }



//save pdf from bitmap

            public File savePdf(Bitmap bitmap) {
                    PdfDocument document = new PdfDocument();
                    PdfDocument.PageInfo pageInfo = new PdfDocument.PageInfo.Builder(bitmap.getWidth(), bitmap.getHeight(), 1).create();
                    PdfDocument.Page page = document.startPage(pageInfo);

                    Canvas canvas = page.getCanvas();
                    Paint paint = new Paint();
                    paint.setColor(Color.parseColor("#ffffff"));
                    canvas.drawPaint(paint);

                    bitmap = Bitmap.createScaledBitmap(bitmap, bitmap.getWidth(), bitmap.getHeight(), true);

                    paint.setColor(Color.BLUE);
                    canvas.drawBitmap(bitmap, 0, 0, null);
                    document.finishPage(page);

                    File myDir = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOCUMENTS), getString(R.string.app_name));

                    if (!myDir.exists()) myDir.mkdirs();

                    String targetPdf = System.currentTimeMillis() + ".pdf";

                    File filePath = new File(myDir, targetPdf);

                    if (filePath.exists()) filePath.delete();

                    try {
                        document.writeTo(new FileOutputStream(filePath));
                    } catch (Exception e) {
                        Log.e(TAG, e.getMessage());
                    }

                    document.close();

                    return filePath;
                }






//To Get Pic From Camera Full Size

            manifest
            <application...
             <provider
                        android:name="androidx.core.content.FileProvider"
                        android:authorities="${applicationId}.provider"
                        android:exported="false"
                        android:grantUriPermissions="true">
                        <meta-data
                            android:name="android.support.FILE_PROVIDER_PATHS"
                            android:resource="@xml/file_paths" />
                    </provider>
             </application>

           MainActivity
           private static final int REQUEST_IMAGE_CAPTURE = 111;
           String currentPhotoPath;

           private void dispatchTakePictureIntent() {
                    Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
                    File photoFile = null;
                    try {
                        photoFile = File.createTempFile("Img"/*File Name prefix */, ".jpg"/*Extension suffix */);
                        currentPhotoPath = photoFile.getAbsolutePath();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    if (photoFile != null) {
                        Uri photoURI = FileProvider.getUriForFile(this, getPackageName() + ".provider", photoFile);
                        takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, photoURI);
                        startActivityForResult(takePictureIntent, REQUEST_IMAGE_CAPTURE);
                    }
            }

            @Override
            protected void onActivityResult(int requestCode, int resultCode, Intent data) {
                super.onActivityResult(requestCode, resultCode, data);
                if (requestCode == REQUEST_IMAGE_CAPTURE && resultCode == RESULT_OK) {
                    Log.i(TAG, currentPhotoPath); //  /data/user/0/com.example.xyz/cache/cam_img**some_auto_genreted_numers**.jpg
                    ((ImageView) findViewById(R.id.iv)).setImageURI(Uri.fromFile(new File(currentPhotoPath)));
                }
            }

        res>xml>file_paths.xml
        <?xml version="1.0" encoding="utf-8"?>
        <paths xmlns:android="http://schemas.android.com/apk/res/android">
            <external-files-path name="my_images" path="Pictures" />
        </paths>


//Dialog with Snackbar


    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:color/transparent"
        android:orientation="vertical">

        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_margin="@dimen/_16sdp"
            app:cardCornerRadius="@dimen/_4sdp"
            app:cardElevation="@dimen/_2sdp"
            app:cardUseCompatPadding="true">

            <RelativeLayout

                android:layout_width="match_parent"
                android:layout_height="wrap_content">

                <ProgressBar
                    android:id="@+id/pb"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_centerHorizontal="true"
                    android:layout_centerVertical="true"
                    android:visibility="gone"
                    tools:visibility="visible" />

                <LinearLayout
                    android:id="@+id/ll"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:clipToPadding="false"
                    android:orientation="vertical"
                    android:paddingStart="@dimen/_12sdp"
                    android:paddingTop="@dimen/_8sdp"
                    android:paddingEnd="@dimen/_12sdp"
                    android:paddingBottom="@dimen/_8sdp"
                    android:visibility="invisible"
                    tools:visibility="visible">          

                    <ImageView
                        android:id="@+id/ivClose"
                        android:layout_width="@dimen/_40sdp"
                        android:layout_height="@dimen/_24sdp"
                        android:layout_gravity="center_horizontal"
                        android:layout_marginBottom="@dimen/_minus8sdp"
                        android:src="@drawable/ic_close_wallet"
                        app:tint="@color/gray_d" />
                </LinearLayout>

                <androidx.coordinatorlayout.widget.CoordinatorLayout
                    android:id="@+id/clVisibleRoot"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_alignBottom="@id/ll" />
            </RelativeLayout>
        </com.google.android.material.card.MaterialCardView>

    </LinearLayout>

    private void showWallet() {
        dialog = new Dialog(this);
        bind = WalletDialogBinding.inflate(getLayoutInflater());
        dialog.setContentView(bind.getRoot());

        int width = WindowManager.LayoutParams.MATCH_PARENT;
        int height = WindowManager.LayoutParams.WRAP_CONTENT;
        dialog.getWindow().setLayout(width, height);

        dialog.getWindow().setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));

        dialog.setCancelable(true);

        bind.ivClose.setOnClickListener(v -> {
            dialog.dismiss();
        });

        dialog.show();

        //network call if needed
    }
    
    //if network all failed show option to try again
    public void tryAgain(String s, String method) {
        Snackbar snackbar = Snackbar.make(bind.clVisibleRoot, s, Snackbar.LENGTH_INDEFINITE);
        snackbar.setAction("Try Again", view -> {
            switch (method) {
                case method:
                    method();
                    break;
            }
        });
        snackbar.show();
    }
    
    



//Bitmap to File
        
      public File saveBitmap(Bitmap bitmap, Bitmap.CompressFormat format, int quality) {
        File myDir = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DCIM), getString(R.string.app_name));

        if (!myDir.exists()) myDir.mkdirs();

        String fileName;
        if (format == Bitmap.CompressFormat.JPEG)
            fileName = System.currentTimeMillis() + ".jpeg";
        else
            fileName = System.currentTimeMillis() + ".png";

        File file = new File(myDir, fileName);
        if (file.exists()) file.delete();

        try {
            FileOutputStream out = new FileOutputStream(file);
            bitmap.compress(format, quality, out);
            out.flush();
            out.close();

            MediaScannerConnection.scanFile(this, new String[]{file.getPath()}, new String[]{"image/jpeg", "image/png"}, null);
        } catch (Exception e) {
            Log.e(TAG, e.getMessage());
        }
        return file;
    }
    

//todo AsyncTask Alternative

            Executors.newSingleThreadExecutor().execute(new Runnable() {
                public void run() {
                    //do in background

                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            //post Execute
                        }
                    });
                }
            });
            
//selector for Rg

        <?xml version="1.0" encoding="utf-8"?>
        <selector xmlns:android="http://schemas.android.com/apk/res/android">

            <item android:state_checked="true">
                <shape>
                    <solid android:color="@color/blue" />
                    <corners android:radius="@dimen/_12sdp" />
                    <padding android:bottom="@dimen/_8sdp" android:left="@dimen/_18sdp" android:right="@dimen/_18sdp" android:top="@dimen/_8sdp" />
                </shape>
            </item>

            <item android:state_pressed="true">
                <shape>
                    <solid android:color="#200C1947" />
                    <corners android:radius="@dimen/_12sdp" />
                    <padding android:bottom="@dimen/_8sdp" android:left="@dimen/_18sdp" android:right="@dimen/_18sdp" android:top="@dimen/_8sdp" />
                </shape>

            </item>

            <item android:state_checked="false">
                <shape>
                    <solid android:color="@color/btn_def" />
                    <corners android:radius="@dimen/_12sdp" />
                    <padding android:bottom="@dimen/_8sdp" android:left="@dimen/_18sdp" android:right="@dimen/_18sdp" android:top="@dimen/_8sdp" />
                </shape>
            </item>
        </selector>
            
//Image Blure With Picasso

        implementation 'com.squareup.picasso:picasso:2.71828'
        
       
        import android.content.Context;
        import android.graphics.Bitmap;
        import android.renderscript.Allocation;
        import android.renderscript.Element;
        import android.renderscript.RenderScript;
        import android.renderscript.ScriptIntrinsicBlur;
        import com.squareup.picasso.Transformation;

        public class BlurTransformation implements Transformation {

            RenderScript rs;

            public BlurTransformation(Context context) {
                super();
                rs = RenderScript.create(context);
            }

            @Override
            public Bitmap transform(Bitmap bitmap) {
                // Create another bitmap that will hold the results of the filter.
                Bitmap blurredBitmap = bitmap.copy(Bitmap.Config.ARGB_8888, true);

                // Allocate memory for Renderscript to work with
                Allocation input = Allocation.createFromBitmap(rs, blurredBitmap, Allocation.MipmapControl.MIPMAP_FULL, Allocation.USAGE_SHARED);
                Allocation output = Allocation.createTyped(rs, input.getType());

                // Load up an instance of the specific script that we want to use.
                ScriptIntrinsicBlur script = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs));
                script.setInput(input);

                // Set the blur radius
                script.setRadius(10);

                // Start the ScriptIntrinisicBlur
                script.forEach(output);

                // Copy the output to the blurred bitmap
                output.copyTo(blurredBitmap);

                bitmap.recycle();

                return blurredBitmap;
            }

            @Override
            public String key() {
                return "blur";
            }
        }
        
        Picasso.get().load(R.drawable.black_background)
        .transform(new BlurTransformation(context))
        .into(bind.blureView);


//Backround Blur

        implementation 'com.eightbitlab:blurview:1.6.6'

         <eightbitlab.com.blurview.BlurView
                        android:id="@+id/blureView"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent" />

        View decorView = activity.getWindow().getDecorView();
        //ViewGroup you want to start blur from. Choose root as close to BlurView in hierarchy as possible.
        ViewGroup rootView = (ViewGroup) decorView.findViewById(android.R.id.content);
        //Set drawable to draw in the beginning of each blurred frame (Optional).
        //Can be used in case your layout has a lot of transparent space and your content
        //gets kinda lost after after blur is applied.
        Drawable windowBackground = decorView.getBackground();

        bind.blureView.setupWith(bind.getRoot())
                 .setFrameClearDrawable(windowBackground)
                 .setBlurAlgorithm(new RenderScriptBlur(activity))
                 .setBlurRadius(10f) //radius
                 .setBlurAutoUpdate(true)
                 .setHasFixedTransformationMatrix(false); // Or false if it's in a scrolling container or might be animated
                 
                 
//Full Screen
             
     //in values/themes.xml
     <style name="Theme.App" parent="Theme.MaterialComponents.Light.NoActionBar.Bridge">
        <!--        status bar-->
        <item name="colorPrimaryDark">@color/st</item>
        <!--        button-->
        <item name="colorPrimary">@color/btn</item>
        <!--        courser  switch  checkBox-->
        <item name="colorAccent">@color/accent</item>
     </style>

    <style name="Theme.WindowFullscreen" parent="Theme.App">
        <item name="android:windowNoTitle">true</item>
        <item name="android:windowFullscreen">true</item>
    </style>
    
    //in AndroidManifest.xml
    <application
        ...
        android:theme="@style/Theme.App">
                 
        <activity
            android:name=".SplashActivity"
            android:exported="true"
            android:theme="@style/Theme.WindowFullscreen">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".LogInActivity"
            android:exported="true"
            android:theme="@style/Theme.WindowFullscreen">
        </activity>
      
    </application>
