# KYCClientSDK
Last released version: 3.0.0
Versions 2.5.x and below now only for support (Latest 2.5.5)
if your project doesn't use AndroidX, you may stay on version 2.5.5.

### Changes
* 2.5.5 - Improved handler when a token is expired
* 3.0.0 
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
implementation 'com.sumsub:kyc-liveness3d:{last-version}'
implementation 'com.sumsub:kyc-client:{last_version}'
```
 
For liveness right now we have two different activities:
```
KYCLivenessFaceAuthActivity  // For Face Auth
KYCLivenessFaceDetectionActivity // Liveness detection, old way
```
In onActivityResult you can check two codes:
```
KYCLiveness3D.REQUEST_CODE_ID_FACE_DETECTION
KYCLiveness3D.REQUEST_CODE_ID_FACE_AUTH
```
 
### Usage
Start check by:
```kotlin
startActivityForResult(
    KYCLivenessFaceAuthActivity.newIntent(
        this@StartActivity,
        BuildConfig.BASE_URL,
        applicantId,
        token,
        Locale.getDefault(),
        customization
    ),
    KYCLiveness3D.REQUEST_CODE_ID_FACE_AUTH
)
Or
```kotlin
startActivityForResult(
    KYCLivenessFaceDetectionActivity.newIntent(
        this@StartActivity,
        BuildConfig.BASE_URL,
        applicantId,
        token,
        Locale.getDefault(),
        customization
    ),
    KYCLiveness3D.REQUEST_CODE_ID_FACE_AUTH
)
```
You can use KYCLivenessFaceDetectionActivity as earlier when applicantId == null instead of KYCLivenessFaceAuthActivity needs correct applicantId.
 
And you can get results in onActivityResult callback.
For KYCLivenessFaceAuthActivity:
```kotlin
data?.getParcelableExtra<KYCLivenessResult.FaceAuth>(KYCLiveness3D.EXTRA_RESULT)
```
results will be in KYCLivenessResult.FaceAuth.
KYCLivenessResult.FaceAuth fields:
```
id
type
rejectLabels
reason
trailData
```
 
For KYCLivenessFaceDetectionActivity:
```kotlin
data?.getParcelableExtra<KYCLivenessResult.FaceDetection>(KYCLiveness3D.EXTRA_RESULT)
```
results will be in KYCLivenessResult.FaceDetection.
 
# Possible reasons
```kotlin
sealed class KYCLivenessReason(val message: String): Serializable {
 
    // Liveness Check
    object UserCancelled: KYCLivenessReason("User cancelled before completing session.")
    object VeritifcationSuccessfully: KYCLivenessReason("The Liveness session was performed successfully ")
    object CameraPermissionDenied: KYCLivenessReason("Camera is required but access prevented by user settings.")
    object PortraitRequired: KYCLivenessReason("Portrait mode is required.")
    object Timeout: KYCLivenessReason("Session cancelled due to timeout.")
    object ContextSwitch: KYCLivenessReason("Session cancelled because a Context Switch occurred during session.")
    object CameraInitializationIssue: KYCLivenessReason("Session failed because of an unexpected camera error.")
    object UnknownInternalError: KYCLivenessReason("Session failed because of an unhandled internal error.")
    object LockedOut: KYCLivenessReason("ZoOm is in a lockout state.")
    object MissingGuidanceImages: KYCLivenessReason("Session cancelled because guidance images were not provided.")
 
    // Zoom SDK Status
    object VersionDeprecated: KYCLivenessReason("Current version of SDK is deprecated.")
    object LicenseExpiredOrInvalid: KYCLivenessReason("License was expired, contained invalid text, or you are attempting to initialize in an App that is not specified in your license.")
    object InvalideDeviceLicenseKeyIndetifier: KYCLivenessReason("The Device License Key Identifier provided was invalid.")
 
    data class InitializationError(val exception: Exception): KYCLivenessReason("Liveness initialization is failed")
    data class NetworkError(val exception: Exception? = null): KYCLivenessReason("Network connectivity issue encountered.")
}
```
 
# Localization
 
Override these strings in your client app for localization:
```
<string name="liveness_retry">Retry</string>
<string name="liveness_cancel">Cancel</string>
<string name="liveness_check_failed_please_try_again">Please check the internet connection then try again.</string>
<string name="liveness_initialization_please_try_again">Initialization failed, please try again.</string>
<string name="liveness_liveness_detection">Liveness Detection</string>
<string name="liveness_initializing">Initializing…</string>
 
<string name="zoom_accessibility_cancel_button">Cancel</string>
<string name="zoom_accessibility_torch_button">Toggle Torch</string>
<string name="zoom_action_accept_photo">ACCEPT</string>
<string name="zoom_action_continue">CONTINUE</string>
<string name="zoom_action_im_ready">I\'M READY</string>
<string name="zoom_action_ok">OK</string>
<string name="zoom_action_skip_guidance">SKIP</string>
<string name="zoom_action_try_again">TRY AGAIN</string>
<string name="zoom_camera_permission_enable_camera">ENABLE CAMERA</string>
<string name="zoom_camera_permission_header">Enable Camera</string>
<string name="zoom_camera_permission_launch_settings">LAUNCH SETTINGS</string>
<string name="zoom_camera_permission_message_auth">ZoOm has detected your camera permissions are disabled. Tap the button below to edit your OS settings.</string>
<string name="zoom_camera_permission_message_enroll">Please enable your selfie camera.</string>
<string name="zoom_feedback_center_face">Center Your Face</string>
<string name="zoom_feedback_face_not_found">Frame Your Face</string>
<string name="zoom_feedback_face_not_looking_straight_ahead">Look Straight Ahead</string>
<string name="zoom_feedback_face_not_upright">Hold Your Head Straight</string>
<string name="zoom_feedback_hold_steady">Hold Steady</string>
<string name="zoom_feedback_move_phone_away">Move Away</string>
<string name="zoom_feedback_move_phone_closer">Move Closer</string>
<string name="zoom_feedback_move_phone_even_closer">Even Closer</string>
<string name="zoom_feedback_move_phone_to_eye_level">Raise Camera Up To Eye Level</string>
<string name="zoom_feedback_use_even_lighting">Light Face More Evenly</string>
<string name="zoom_instructions_header_face_angle">Hold Camera\nAt Eye Level</string>
<string name="zoom_instructions_header_lighting">Light Face Evenly</string>
<string name="zoom_instructions_header_ready">Get Ready For\nYour Video Selfie!</string>
<string name="zoom_instructions_message_ready">Please Frame Your Face In\nThe Small Oval, Then The Big Oval</string>
<string name="zoom_locked_header">ZoOm is Locked</string>
<string name="zoom_locked_header_ready">ZoOm is Ready</string>
<string name="zoom_locked_try_again_in">Please try again in:</string>
<string name="zoom_result_facemap_upload_message">Uploading\nEncrypted\n3D FaceMap</string>
<string name="zoom_retry_header">Let\'s try that again</string>
<string name="zoom_retry_ideal_image_label">Ideal ZoOm</string>
<string name="zoom_retry_instruction_message_1">Neutral Expression, No Smiling</string>
<string name="zoom_retry_instruction_message_2">No Glare or Extreme Lighting</string>
<string name="zoom_retry_subheader_message">But first, please take a look at your images and correct your environment.</string>
<string name="zoom_retry_your_image_label">Your ZoOm</string>
```
 
### Liveness customizatiom
 
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
 
```kotlin
KYCLivenessCustomization:
var exitAnimationSuccessResourceID = -1
var exitAnimationUnsuccessResourceID = -1
var guidance = Guidance()
var frame = Frame()
var feedback = Feedback()
var oval = Oval()
var overlay = Overlay()
var resultsScreen = ResultsScreen()
 
KYCLivenessCustomization.Guidance
var backgroundColors = -1
var foregroundColor = -1
var readyScreenOvalFillColor = -1
var readyScreenTextBackgroundColor = -1
var readyScreenTextBackgroundCornerRadius = -1
var showIntroScreenBrandingImage = false
 
KYCLivenessCustomization.Guidance.Button
var textNormalColor = -1
var backgroundNormalColor = -1
var textHighlightColor = -1
var backgroundHighlightColor = -1
var cornerRadius = -1
var border = Button.Border()
 
KYCLivenessCustomization.Guidance.Button.Border
var width = -1
var color = -1
 
KYCLivenessCustomization.Guidance.Image
var goodFaceAngleImage = -1
var badFaceAngleImage = -1
var goodLightingImage = -1
var badLightingImage = -1
var idealZoomImage = -1
var cameraPermissionsScreenImage = -1
var lockoutScreenLockedImage = -1
var lockoutScreenUnlockedImage = -1
var skipGuidanceButtonImage = -1
var introScreenBrandingImage = -1
 
KYCLivenessCustomization.Frame
var ratio = -1.0f
var topMargin = -1
var backgroundColor = -1
var border: Border = Border()
 
KYCLivenessCustomization.Frame.Border
var width = -1
var radius = -1
var color = -1
 
KYCLivenessCustomization.ResultsScreen
var foregroundColor = -1
var backgroundColors = -1
var activityIndicatorColor = -1
var uploadProgressFillColor = -1
var uploadProgressTrackColor = -1
var resultAnimationBackgroundColor = -1
var resultAnimationForegroundColor = -1
 
KYCLivenessCustomization.Feedback
var cornerRadius = -1
var backgroundColors = -1
var topMargin = -1
var textColor = -1
var textSpacing = -1.0f
var enablePulsatingText: Boolean = false
var size: Size? = null
 
KYCLivenessCustomization.Feedback.Size
var width = -1
var height = -1
 
KYCLivenessCustomization.Oval
var strokeWidth = -1
var strokeColor = -1
var progress: Progress = Progress()
 
KYCLivenessCustomization.Oval.Progress
var strokeWidth = -1
var color1 = -1
var color2 = -1
var radialOffset = -1
 
KYCLivenessCustomization.Overlay
var backgroundColor = -1
var brandingImage = -1
```
 
See demo for more examples: https://github.com/SumSubstance/KYC-Android-Demo
