---
title: 快速入門 - 使用 Azure 通訊服務將 VOIP 通話新增至 Android 應用程式
description: 在本教學課程中，您將了解如何使用適用於 Android 的 Azure 通訊服務通話用戶端程式庫
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 02cf175fc0a29795428ce1b3651469532ff3867c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92438928"
---
在本快速入門中，您將了解如何使用適用於 Android 的 Azure 通訊服務通話用戶端程式庫開始進行通話。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Android Studio](https://developer.android.com/studio)，用於建立 Android 應用程式。
- 已部署通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- 針對您的 Azure 通訊服務的[使用者存取權杖](../../access-tokens.md)。

## <a name="setting-up"></a>設定

### <a name="create-an-android-app-with-an-empty-activity"></a>建立具有空白活動的 Android 應用程式

在 Android Studio 中，選取 [啟動新的 Android Studio 專案]。

:::image type="content" source="../media/android/studio-new-project.png" alt-text="顯示在 Android Studio 中選取 [啟動新的 Android Studio 專案] 按鈕的螢幕擷取畫面。":::

選取 [手機和平板電腦] 底下的 [空白活動] 專案範本。

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="顯示在 Android Studio 中選取 [啟動新的 Android Studio 專案] 按鈕的螢幕擷取畫面。" 或更新版本。

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="顯示在 Android Studio 中選取 [啟動新的 Android Studio 專案] 按鈕的螢幕擷取畫面。":::


### <a name="install-the-package"></a>安裝套件

<!-- TODO: update with instructions on how to download, install and add package to project -->
找出您的專案層級 build.gradle，並務必將 `mavenCentral()` 新增至 `buildscript` 和 `allprojects` 下的存放庫清單
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
然後，在您的模組層級 build.gradle 中，將以下幾行新增至相依性和 android 區段

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.2'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>將權限新增至應用程式資訊清單

為了要求進行通話所需的權限，必須先在應用程式資訊清單 (`app/src/main/AndroidManifest.xml`) 中宣告。 以下列內容取代檔案的內容：

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>設定應用程式的配置

需要兩個輸入：被呼叫者識別碼的文字輸入，以及用來進行呼叫的按鈕。 這些可以透過設計工具或藉由編輯版面配置 xml 來新增。 建立識別碼為 `call_button`、文字輸入為 `callee_id` 的按鈕。 瀏覽至 `app/src/main/res/layout/activity_main.xml` 並且以下列內容取代檔案的內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>建立主要活動 Scaffolding 和繫結

建立版面配置之後，即可新增繫結，以及活動的基本 Scaffolding。 活動會處理要求執行階段權限、建立呼叫代理程式，並且在按下按鈕時進行呼叫。 每個項目會涵蓋在各自的區段中。 系統將會覆寫 `onCreate` 方法，以叫用 `getAllPermissions` 和 `createAgent`，以及新增呼叫按鈕的繫結。 只有在建立活動時，才會發生這種情況。 如需 `onCreate` 的詳細資訊，請參閱[了解活動生命週期](https://developer.android.com/guide/components/activities/activity-lifecycle)指南。

瀏覽至 **MainActivity.java** ，然後以下列程式碼取代內容：

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUser;
import com.azure.android.communication.common.CommunicationUserCredential;
import com.azure.communication.calling.CallAgent;
import com.azure.communication.calling.CallClient;
import com.azure.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }
}

```

### <a name="request-permissions-at-runtime"></a>要求執行階段時的權限

針對 Android 6.0 和更新版本 (API 層級 23) 和 `targetSdkVersion` 23 或更新版本，權限會在執行階段授與，而不是在安裝應用程式時。 為了支援此功能，`getAllPermissions` 可以實作為呼叫 `ActivityCompat.checkSelfPermission` 和 `ActivityCompat.requestPermissions`，以取得每個必要的權限。

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> 在設計您的應用程式時，請考量何時應要求這些權限。 您應該視需要要求權限，而不是提前要求。 如需詳細資訊，請參閱 [Android 權限指南](https://developer.android.com/training/permissions/requesting)。

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務通話用戶端程式庫的一些主要功能：

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient 是通話用戶端程式庫的主要進入點。|
| CallAgent | CallAgent 是用來啟動和管理通話。 |
| CommunicationUserCredential | CommunicationUserCredential 會當做權杖認證使用，以具現化 CallAgent。|

## <a name="create-an-agent-from-the-user-access-token"></a>從使用者存取權杖建立代理程式

使用使用者權杖時，可以將已驗證的呼叫代理程式具現化。 一般來說，此權杖會從具有應用程式特定驗證的服務產生。 如需使用者存取權杖的詳細資訊，請參閱[使用者存取權杖](../../access-tokens.md)指南。 在快速入門中，將 `<User_Access_Token>` 取代為針對您的 Azure 通訊服務資源所產生的使用者存取權杖。

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationUserCredential credential = new CommunicationUserCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>使用呼叫代理程式開始通話

您可以透過呼叫代理程式來進行呼叫，只需要提供被呼叫者識別碼和呼叫選項的清單。 在快速入門中，會使用沒有視訊的預設呼叫選項，以及文字輸入中的單一被呼叫者識別碼。

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUser[] {new CommunicationUser(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>啟動應用程式並呼叫 Echo Bot

應用程式現在可以使用工具列上的 [執行應用程式] 按鈕 (Shift+F10) 來啟動。 確認您可以藉由呼叫 `8:echo123` 來進行呼叫。 接著會播放預先錄製的訊息，為您重複訊息。

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="顯示在 Android Studio 中選取 [啟動新的 Android Studio 專案] 按鈕的螢幕擷取畫面。":::

## <a name="sample-code"></a>範例程式碼

您可以從 [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling) 下載範例應用程式
