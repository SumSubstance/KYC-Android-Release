## Liveness3D module

Last released version: 2.2.0-Beta2

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
implementation 'com.sumsub:kyc-core:2.2.0-Beta3'
implementation 'com.sumsub:kyc-liveness3d:2.2.0-Beta3'
implementation 'com.sumsub:kyc-client:2.2.0-Beta3'
```

### Usage
Start liveness check by:
```
String token = TestManager.getInstance().getToken();
String applicant = TestManager.getInstance().getApplicant();
startActivityForResult(
	KYCLiveness3DActivity.Companion.newIntent(
		requireContext(), 
		apiUrl, 
		applicant, 
		token, 
		Locale.getDefault()
	), 
	KYCLiveness3DActivity.REQUEST_RESULT_CODE_ID
);
```
And here you can get results in onActivityResult callback:
```
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == KYCLiveness3DActivity.REQUEST_RESULT_CODE_ID) {
        Toast.makeText(
        	requireContext(), 
        	"Liveness3D status is " + data.getSerializableExtra(KYCLiveness3DActivity.EXTRA_STATUS), Toast.LENGTH_SHORT
        ).show();
    }  
}
```

# Possible result statuses
enum class Liveness3DResult {
    Cancelled,
    InitializationFailed,
    CameraPermissionDenied,
    TokenIsInvalid,
    VerificationPassedSuccessfully
}

See demo for more examples: https://github.com/SumSubstance/KYC-Android-Demo

