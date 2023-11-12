# testfenil.github.io


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


// Important Lib

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
    

// Extenstion Funcation 

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
