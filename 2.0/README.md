# KYCClientSDK

### Installation
* Supports Android SDK 17+
* Put `kyc_client.aar` into `libs` folder
* Add gradle dependencies

```
implementation fileTree(dir: 'libs', include: ['*.jar', '*.aar'])

implementation 'com.android.support:appcompat-v7:28.0.0'
implementation 'com.android.support:exifinterface:28.0.0'
implementation 'com.android.support:recyclerview-v7:28.0.0'
implementation 'com.android.support:cardview-v7:28.0.0'
implementation 'com.android.support.constraint:constraint-layout:1.1.3'
implementation 'com.android.support:support-v4:28.0.0'

implementation 'android.arch.persistence.room:runtime:1.1.1'
implementation 'android.arch.lifecycle:extensions:1.1.1'
annotationProcessor 'android.arch.persistence.room:compiler:1.1.1'

implementation 'com.makeramen:roundedimageview:2.3.0'
implementation 'com.squareup.picasso:picasso:2.71828'
implementation 'pl.droidsonroids.gif:android-gif-drawable:1.2.16'

```
* Start KYC Module like this

```
public void startKYCModule() {

    String kycAPIPath = "msdk.sumsub.com";
//    String kycAPIPath = "test-msdk.sumsub.com";
    
    KYCClientData clientData = new KYCClientData(
        kycAPIPath,
        getPackageName(),
        "1.0",
        TestManager.getInstance().getLocale(),
        TestManager.getInstance().getApplicant(),
        "support@sumsub.com",
        new KYCColorConfig(),
        new KYCStringConfig(),
        new KYCIconConfig()
    );
    
    KYCManager.init(getApplicationContext(), clientData, TestManager.getInstance().getKYCTokenUpdater());

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
* Customize almost any color and robot icons by overriding properties in KYCColorConfig and KYCIconConfig instances

* Receive verification result callback like this

```
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
