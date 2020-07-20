---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156274"
---
### <a name="add-the-google-services-json-file"></a>新增 Google Services JSON 檔案

1. **Control** + **按一下** [android] 資料夾，然後選擇 [在 Android Studio 中開啟]。 然後，切換至 [專案] 檢視 (如果還未進入此檢視的話)。

1. 找到您稍早在 [Firebase 主控台](https://console.firebase.google.com)中設定 **PushDemo** 專案時下載的 *google-services.json* 檔案。 然後，將其拖曳到 **app** 模組根目錄 (**android** > **android** > **app**)。

### <a name="configure-build-settings-and-permissions"></a>設定組建設定和權限

1. 將 [專案] 檢視切換為 [Android]。

1. 開啟 **AndroidManifest.xml**，然後將 **INTERNET** 和 **READ_PHONE_STATE** 權限新增至 **application** 元素之後，結尾 **</manifest>** 標籤之前。

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>新增 Firebase SDK

1. 在 **Android Studio** 中，開啟專案層級 **build.gradle** 檔案 (**Gradle Scripts** > **build.gradle (Project: android)** )。 並確定 ``buildscript`` > **相依性**節點中有「com.google.gms:google-services」類別路徑。

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > 務必要按照建立 **Android 專案**時 [Firebase 主控台](https://console.firebase.google.com)中所提供的指示來參照最新版本。

1. 在應用程式層級 **build.gradle** 檔案 (**Gradle Scripts** > **build.gradle (Module: app)** ) 中，套用 **Google Services Gradle 外掛程式**。 將外掛程式套用在 [android] 節點正上方。

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. 在相同檔案中，於 [相依性] 節點中新增**雲端傳訊** Android 程式庫的相依性。

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > 務必按照[雲端傳訊 Android 用戶端文件](https://firebase.google.com/docs/cloud-messaging/android/client)來參照最新版本。

1. 儲存變更，然後按一下 [立即同步] 按鈕 (從工具列提示) 或 [同步處理專案與 Gradle 檔案]。

### <a name="handle-push-notifications-for-android"></a>處理 Android 的推播通知

1. 在 **Android Studio** 中，**Control** + **按一下** **com.<your_organization>.pushdemo** 套件資料夾 (**app** > **src** > **main** > **kotlin**)，然後從 [新增] 功能表中選擇 [套件]。 輸入 **services** 作為名稱，然後按 **Return**。

1. **Control** + **按一下** [services] 資料夾，然後從 [新增] 功能表中選擇 [Kotlin 檔案/類別]。 輸入 **DeviceInstallationService** 作為名稱，然後按 **Return**。

1. 使用下列程式碼實作 **DeviceInstallationService**。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > 此類別會針對 `com.<your_organization>.pushdemo/deviceinstallation` 通道執行平台專屬的對應項目。 這已在應用程式 Flutter 部分的 **DeviceInstallationService.dart** 中定義。 在此情況下，呼叫會從通用程式碼對原生主機發出。 無論使用哪種方式，請務必將 **<your_organization>** 取代為您自己的組織。
    >
    > 此類別會提供唯一識別碼 (使用 [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) 作為通知中樞註冊承載的一部分。

1. 將另一個名為 *NotificationRegistrationService* 的 **Kotlin 檔案/類別**新增至 **Services** 資料夾，然後新增下列程式碼。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > 此類別會針對 `com.<your_organization>.pushdemo/notificationregistration` 通道執行平台專屬的對應項目。 這已在應用程式 Flutter 部分的 **NotificationRegistrationService.dart** 中定義。 在此情況下，呼叫會從原生主機對通用程式碼發出。 同樣地，無論使用哪種方式，請務必將 **<your_organization>** 取代為您自己的組織。

1. 將另一個名為 *NotificationActionService* 的 **Kotlin 檔案/類別**新增至 **Services** 資料夾，然後新增下列程式碼。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 此類別會針對 `com.<your_organization>.pushdemo/notificationaction` 通道執行平台專屬的對應項目。 其已在應用程式 Flutter 部分的 **NotificationActionService.dart** 中定義。 在此情況下，呼叫可以在這兩個方向中進行。 無論使用哪種方式，請務必將 **<your_organization>** 取代為您自己的組織。

1. 將新的 **Kotlin 檔案/類別**新增至 **com.<your_organization>.pushdemo** 套件 (名稱為 *PushNotificationsFirebaseMessagingService*)，然後使用下列程式碼來實作。

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 當應用程式在前景中執行時，此類別會負責處理通知。 如果動作包含在 **onMessageReceived** 所收到的通知承載中，此類別會有條件地呼叫 **NotificationActionService** 上的 **triggerAction**。 這也會在藉由覆寫 **onNewToken** 函式來重新產生 **Firebase** 權杖時，於 **NotificationRegistrationService** 上呼叫 **refreshRegistration**。
    >
    > 同樣地，無論是在哪裡使用，請務必將 **<your_organization>** 取代為您自己的組織。

1. 在 **AndroidManifest.xml** (**app** > **src** > **main**) 中，使用 `com.google.firebase.MESSAGING_EVENT` 意圖篩選將 **PushNotificationsFirebaseMessagingService** 新增至 **application** 元素的底部。

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. 回到 **DeviceInstallationService**，確定檔案頂端有下列匯入。

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > 以您自己的組織值取代 **<your_organization>** 。

1. 更新預留位置文字 *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation*，以從 **PushNotificationFirebaseMessagingService** 取得權杖值。

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. 在 **MainActivity** 中，確定檔案頂端有下列匯入。

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > 以您自己的組織值取代 **<your_organization>** 。

1. 新增變數以儲存 **DeviceInstallationService** 的參考。

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. 新增名為 **processNotificationActions** 的函式，以檢查 [意圖] 是否有名為 **action** 的額外值。 有條件地觸發該動作，或在應用程式啟動期間處理動作時將其儲存，以供稍後使用。

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. 覆寫 **onNewIntent** 函式以呼叫 **ProcessNotificationActions**。

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > 由於 **MainActivity** 的 **LaunchMode** 設定為 **SingleTop**，因此**意圖**將會透過 **onNewIntent** 函式來傳送至現有 [活動] 執行個體，而不是透過 **onCreate** 函式，因此您必須處理 **onCreate** 和 **onNewIntent** 函式中的傳入**意圖**。

1. 覆寫 **onCreate** 函式，將 **deviceInstallationService** 設定為 **DeviceInstallationService** 的新執行個體。

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. 在 **PushNotificationFirebaseMessagingServices** 上設定 **notificationActionService** 和 **notificationRegistrationService** 屬性。

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. 在相同函式中，有條件地呼叫 **FirebaseInstanceId.getInstance().instanceId**。 在呼叫 **refreshRegistration** 之前，先實作 **OnCompleteListener** 以在 **PushNotificationFirebaseMessagingService** 上設定產生的 [權杖] 值。

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. 仍然在 **onCreate** 中，請在函式結尾呼叫 **processNotificationActions**。 針對 *launchAction* 引數使用 *true*，以指出會在應用程式啟動期間處理此動作。

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> 您必須在每次執行應用程式時重新註冊，並從偵錯工作階段停止應用程式，才能繼續接收推播通知。
