---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060413"
---
### <a name="configure-required-android-packages"></a>設定必要的 Android 套件

在建置應用程式時，會[自動連結](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)套件。 您有下列幾個額外步驟，以完成設定程序。

### <a name="configure-android-manifest"></a>設定 Android 資訊清單

在您的「android/app/src/main/Androidmanifest.xml」中，驗證套件名稱、權限和必要服務。 確定您已註冊 `RNPushNotificationPublisher` 及 `RNPushNotificationBootEventReceiver` 接收器，並已註冊 `RNPushNotificationListenerService` 服務。 通知中繼資料可以用來自訂您的推播通知外觀。

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>設定 Google 服務

在 「android/app/build.gradle」中註冊 Google 服務：

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

將您在 FCM 設定期間下載的「google-services.json」檔案複製到專案資料夾「android/app/」。

### <a name="handle-push-notifications-for-android"></a>處理 Android 的推播通知

您已設定現有的 `RNPushNotificationListenerService` 服務來處理傳入的 Android 推播通知。 此服務先前已在應用程式資訊清單中註冊。 其會處理傳入通知，並將其 Proxy 至跨平台的 React Native 部分。 不需要任何額外步驟。
