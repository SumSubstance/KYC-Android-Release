# KYCClientSDK
Last released version: 3.0.0
### Versions 2.5.x and below now only for support (Latest 2.5.5)
Note: if your project doesn't use AndroidX, you may stay on version 2.5.5.

### Changes
* 2.5.5 - Improved handler when a token is expired
* 3.0.0 -
  Migrate to AndroidX
  Zoom8 is available

More info about version 2.5.x see by the link: https://github.com/SumSubstance/KYC-Android-Release/tree/2.5.x

More info about Liveness you can see by the link: https://github.com/SumSubstance/KYC-Android-Release/blob/master/LIVENESS.md


### Installation
* Supports Android SDK 18+
* Add maven repo to project's level build.gradle (in allProjects { repositories { section
```
maven {
  url  "https://dl.bintray.com/sumsub/maven"
}
```
* Add gradle dependencies
```
implementation 'com.sumsub:kyc-core:{last_version}'
implementation 'com.sumsub:kyc-liveness3d:{last-version}' //add this line if you need Liveness module
implementation 'com.sumsub:kyc-client:{last_version}'
```
* Start KYC Module like this
```java
public void startKYCModule() {
    String kycAPIPath = "msdk.sumsub.com"; //prod
    //String kycAPIPath = "test-msdk.sumsub.com"; //dev
    
    KYCColorConfig config = new KYCColorConfig();
    //config.setChatButtonBackgroundColor(Color.parseColor("#aaaaaa"));
    //config.setChatButtonTextColor(Color.BLACK);

    KYCClientData clientData = new KYCClientData(
        kycAPIPath,
        getPackageName(),
        "2.0",
        TestManager.getInstance().getLocale(),
        TestManager.getInstance().getApplicant(),
        "support@sumsub.com",
        config,
        new KYCStringConfig(),
        new KYCIconConfig()
    );

    KYCManager.init(
        this, 
        clientData, 
        TestManager.getInstance().getKYCTokenUpdater(), 
        Collections.singletonList(new Liveness3DModule()) //if you need Liveness module or empty list instead
    );
    Intent intent = new Intent(this, KYCChatActivity.class);
    startActivityForResult(intent, KYC_REQUEST_CODE);
}

public KYCTokenUpdater getKYCTokenUpdater() {
    return new KYCTokenUpdater() {

        @Override
        public void getInitialToken(ParamCallback<String> callback) {
            callback.onResult(getToken());
        }

        @Override
        public void updateExpiredToken(final String expiredToken, final ParamCallback<String> callback) {
            TestNetworkManager.getInstance().addToRequestQueue(new TestAuthRequest(getLogin(), getPasssword(), new TestRequestListener<String>() {

                @Override
                public void onResult(String result) {
                    setToken(result);
                    callback.onResult(result);
                }

                @Override
                public void onError(Exception e) {
                    new Handler().postDelayed(new Runnable() {
                        @Override
                        public void run() {
                            updateExpiredToken(expiredToken, callback);
                            }
                    }, 4000);
                }
            }));
        }
    };
}

```
* Receive verification result callback like this

```java
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (data != null && requestCode == KYC_REQUEST_CODE) {
        KYCReviewResult kycReviewResult = (KYCReviewResult) data.getSerializableExtra(KYCChatActivity.KYC_VERIFICATION_KEY);
        if (kycReviewResult != null) {
            // do smth
        }
    }
}

```
In version 2.4.1 we added new optional callback for FaceAuth request. You can use it like:
```kotlin
//first, create callback
val receiver = Liveness3DResultReceiver(Handler()).apply {
    setReceiver(object : Liveness3DResultReceiver.Receiver {
       override fun onReceiveResult(data: Bundle) {
          Log.d(TAG, "Face Auth result: $data")
       }
    })
 }
 //second, pass it to KYCLivenessFaceAuthActivity. 
 //Remember! this callback nullable and optional.
startActivityForResult(KYCLivenessFaceAuthActivity.newIntent(this@StartActivity, BuildConfig.BASE_URL, applicantId, token, Locale.getDefault(), customization, receiver), KYCLiveness3D.REQUEST_CODE_ID_FACE_AUTH)
```
Than after request will completed you recieve Bundle object like:
```
Bundle[
  {
     EXTRA_ACTION_ID=5ddd3fa80a975a2eecb241ba, 
     EXTRA_CREATED_AT=2019-11-26 15:07:20, 
     EXTRA_APPLICANT_ID=5ddd3f930a975a2eecb240a9, 
     EXTRA_TYPE=selfieAuth, 
     EXTRA_REJECTED_LABS=[BAD_SELFIE], 
     EXTRA_RESULT=Error
  }
]
```

* See demo project for more infoSee demo for more examples: https://github.com/SumSubstance/KYC-Android-Demo
