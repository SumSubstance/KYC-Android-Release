## Liveness3D module

Last released version: 2.2.0-RC1

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
implementation 'com.sumsub:kyc-liveness3d:{last-version}'
implementation 'com.sumsub:kyc-client:{last_version}'
```

### Usage
Start liveness check by:
```java
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
```java
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
```kotlin
enum class Liveness3DResult {
    Cancelled,
    InitializationFailed,
    CameraPermissionDenied,
    TokenIsInvalid,
    VerificationPassedSuccessfully
}
```

# Localization

Override these strings in your client app for localization:
```
 <string name="liveness_let_s_try_that_again">Let\'s try that again</string>
 <string name="liveness_first_please_make_sure_your_environment">But first, please make sure your environment meets the requirements</string>
 <string name="liveness_check_lighting_and_camera">• Check Lighting and Camera Position</string>
 <string name="liveness_try_again">Try again</string>
 <string name="liveness_ideal_zoom">Ideal ZoOm</string>
 <string name="liveness_neutral_expression">• Neutral Expression, No Smiling\n• No Glare or Extreme Lighting</string>
 <string name="liveness_no_glare">• No Glare or Extreme Lighting</string>
 <string name="liveness_your_zoom">Your ZoOm</string>
 <string name="liveness_first_please_take_a_look_at_your_images_and_correct">But first, please take a look at your images and correct your environment.</string>
 <string name="liveness_retry">Retry</string>
 <string name="liveness_cancel">Cancel</string>
 <string name="liveness_check_failed_please_try_again">Check failed, please try again.</string>
 <string name="liveness_liveness_detection">Liveness Detection</string>
 <string name="liveness_initializing">Initializing…</string>
 <string name="liveness_processing">Processing…</string>
 <string name="liveness_success">Success!</string>
 <string name="liveness_continue">Continue</string>
```

# Liveness customizatiom

Use class KYCLivenessCustomization. For example:
```java
//set background color and screen ratio (1.0 - fullscreen)
KYCLivenessCustomization customization = new KYCLivenessCustomization();
customization.getFrame().setBackgroundColor(ContextCompat.getColor(requireContext(), R.color.blueDark));
customization.getFrame().setRatio(0.98f);
//and send parameters to KYCLiveness3DActivity
startActivityForResult(KYCLiveness3DActivity.Companion.newIntent(
    requireContext(), 
    apiUrl, 
    applicant, 
    token, 
    Locale.getDefault(), 
    customization), 
    KYCLiveness3DActivity.REQUEST_RESULT_CODE_ID);
```

Other fields for customization. Base fields:

```kotlin
@ColorInt
var backgroundColor
@ColorInt
var foregroundColor
@DrawableRes
var brandingLogo
var frame: Frame
var feedback: Feedback
var oval: Oval
var button: Button
```

Frame fields (frame variable):
```kotlin
var ratio
@ColorInt
var backgroundColor
var border: Border
```
Also you can create your own frame.

Border fields:
```kotlin
var width
var radius
@ColorInt
var color
```

Feedback filds:
```kotlin
var cornerRadius
@ColorInt
var backgroundColor
@ColorInt
var textColor
var enablePulsatingText: Boolean = true
var size: Size? = null
```

Size fields:
```kotlin
var width
var height
```

Oval fields:
```kotlin
var strokeWidth
@ColorInt
var strokeColor
var progress: Progress
```

Progress fields:
```kotlin
var strokeWidth
@ColorInt
var color1
@ColorInt
var color2
var radialOffset
```

Button fields:
```kotlin
@ColorInt
var textNormalColor
@ColorInt
var textHighlightColor
@ColorInt
var backgroundNormalColor
@ColorInt
var backgroundHighlightColor
```

See demo for more examples: https://github.com/SumSubstance/KYC-Android-Demo

