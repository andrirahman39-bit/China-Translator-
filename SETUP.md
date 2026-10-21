# Setup

Put the 3 `.kt` files in `app/src/main/java/<your/package>/` and change the `package` line at the top of each.

## 1. app/build.gradle(.kts) — add dependency

```
implementation("com.google.mlkit:text-recognition-chinese:16.0.1")
```
(minSdk must be 21+; 26+ recommended)

## 2. AndroidManifest.xml

Inside `<manifest>`:
```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
<uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PROJECTION"/>
<uses-permission android:name="android.permission.POST_NOTIFICATIONS"/>
```

Inside `<application>`:
```xml
<service
    android:name=".TranslateService"
    android:exported="false"
    android:foregroundServiceType="mediaProjection"/>
```

## 3. MainActivity — start button

```kotlin
private val capture = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { r ->
    val d = r.data
    if (r.resultCode == RESULT_OK && d != null) TranslateService.start(this, r.resultCode, d)
}

fun startTranslator() {
    if (!Settings.canDrawOverlays(this)) {
        startActivity(Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION,
            Uri.parse("package:$packageName")))
        return
    }
    val mpm = getSystemService(Context.MEDIA_PROJECTION_SERVICE) as MediaProjectionManager
    capture.launch(mpm.createScreenCaptureIntent())
}
```
Also request the POST_NOTIFICATIONS permission at runtime on Android 13+.

## 4. API key

`TranslateService` reads the key from SharedPreferences `"settings"` / `"gemini_key"`.
Change that one line to match how your app already stores it.

## Usage
1. Open your app, tap start, allow overlay + screen capture.
2. Switch to the game (start it in landscape BEFORE tapping start, since capture size is fixed).
3. Stop from the notification's Stop button.
4. 
