# KYCClientSDK
Last released version: 2.3.0

### Changes
- Added new activity for Face Auth
- Fixed issues



### Installation
* Supports Android SDK 16+
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
* See demo project for more info
