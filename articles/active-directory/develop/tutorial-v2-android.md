---
title: 教學課程：建立 Android 應用程式，使用 Microsoft 身分識別平台進行驗證 | Azure
titleSuffix: Microsoft identity platform
description: 在本教學課程中，您會建置 Android 應用程式，使用 Microsoft 身分識別平台來登入使用者，並取得存取權杖來代表他們呼叫 Microsoft Graph API。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7ba15e66cca7baefdf8cca5cabd5e5d5b1e2c7f7
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507807"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>教學課程：從 Android 應用程式登入使用者並呼叫 Microsoft Graph API

在本教學課程中，您會建置與 Microsoft 身分識別平台整合的 Android 應用程式，將使用者登入，並取得存取權杖以呼叫 Microsoft Graph API。

完成本教學課程後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。

在本教學課程中： 

> [!div class="checklist"]
> * 在「Android Studio」中建立 Android 應用程式專案
> * 在 Azure 入口網站中註冊應用程式
> * 新增程式碼以支援使用者登入和登出
> * 新增程式碼以呼叫 Microsoft Graph API
> * 測試應用程式

## <a name="prerequisites"></a>必要條件

* Android Studio 3.5+

## <a name="how-this-tutorial-works"></a>本教學課程的運作方式

![示範本教學課程所產生的應用程式範例如何運作](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

此教學課程中的應用程式將會登入使用者，並且代表他們取得資料。 此資料會透過需要授權的受保護 API 來存取 (Microsoft Graph API)，並會受到 Microsoft 身分識別平台保護。

具體而言：

* 您的應用程式會透過瀏覽器或 Microsoft Authenticator 及 Intune 公司入口網站來登入使用者。
* 終端使用者會接受您應用程式已要求的權限。
* 您的應用程式會針對 Microsoft Graph API 發出存取權杖。
* 此存取權杖會包含在 Web API 的 HTTP 要求中。
* 處理 Microsoft Graph 回應。

此範例會使用適用於 Android 的 Microsoft 驗證程式庫 (MSAL) 來實作驗證：[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)。

MSAL 會自動更新權杖、提供裝置上其他應用程式之間的 SSO，以及管理帳戶。

> [!NOTE]
> 本教學課程示範使用「適用於 Android 的 MSAL」的簡化範例。 為了簡單起見，本教學課程僅使用單一帳戶模式。 若要探索更複雜的案例，請參閱 GitHub 上完整的[工作程式碼範例](https://github.com/Azure-Samples/ms-identity-android-java/)。

## <a name="create-a-project"></a>建立專案
如果您還沒有 Android 應用程式，請依照下列步驟設定新的專案。

1. 開啟 Android Studio，然後選取 [開始新的 Android Studio 專案]。
2. 選取 [基本活動]，然後選取 [下一步]。
3. 為您的應用程式命名。
4. 儲存套件名稱。 您稍後會在 Azure 入口網站中加以輸入。
5. 將語言從 [Kotlin] 變更為 [Java]。
6. 將 [最低 API 層級] 設為 [API 19] 或更高，然後按一下 [完成]。
7. 在專案檢視中，從下拉式清單中選擇 [專案] 以顯示來源和非來源專案檔，然後開啟 **app/build.gradle**，並將 `targetSdkVersion` 設為 `28`。

## <a name="integrate-with-microsoft-authentication-library"></a>與 Microsoft 驗證程式庫整合

### <a name="register-your-application"></a>註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
1. 輸入應用程式的 **名稱**。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
1. 選取 [註冊]。
1. 在 [管理] 底下，選取 [驗證] > [新增平台] > [Android]。
1. 輸入您專案的套件名稱。 如果您已下載程式碼，此值會是 `com.azuresamples.msalandroidapp`。
1. 在 [設定 Android 應用程式] 頁面的 [簽章雜湊] 區段中，選取 [產生開發簽章雜湊]。 然後，複製要用於平台的 KeyTool 命令。

   > [!Note]
   > KeyTool.exe 會安裝為 Java 開發套件 (JDK) 的一部分。 您也必須安裝 OpenSSL 工具來執行 KeyTool 命令。 如需詳細資訊，請參閱[關於產生金鑰的 Android 文件](https://developer.android.com/studio/publish/app-signing#generate-key)。

1. 輸入 KeyTool 所產生的 **簽章雜湊**。
1. 選取 [設定] 並儲存出現在 [Android 組態] 頁面中的 [MSAL 組態]，以便稍後在設定應用程式時可加以輸入。  
1. 選取 [完成]  。

### <a name="configure-your-application"></a>設定您的應用程式

1. 在 Android Studio 的專案窗格中，瀏覽至 **app\src\main\res**。
1. 以滑鼠右鍵按一下 [res]，然後選擇 [新增] > [目錄]。 輸入 `raw` 作為新的目錄名稱，然後按一下 [確定]。
1. 在 app > src > main > res > raw 中，建立名為 `auth_config_single_account.json` 的新 JSON 檔案，並貼上您先前儲存的 MSAL 設定。

    在 [重新導向 URI] 下方，貼上：
    ```json
      "account_mode" : "SINGLE",
    ```
    您的組態檔應會與下列範例類似：
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```

   >[!NOTE]
   >本教學課程僅示範如何以單一帳戶模式設定應用程式。 如需關於[單一與多重帳戶模式的比較](./single-multi-account.md)和[設定應用程式](./msal-configuration.md)的詳細資訊，請參閱文件

4. 在 [app] > [src] > [main] > [AndroidManifest.xml] 中，於應用程式主體下方新增 `BrowserTabActivity` 活動。 此輸入可讓 Microsoft 在完成驗證後回呼您的應用程式：

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    將 `android:host=` 值取代為您在 Azure 入口網站中註冊的套件名稱。
    將 `android:path=` 值取代為您在 Azure 入口網站中註冊的索引鍵雜湊。 簽章雜湊 **不應** 進行 URL 編碼。 請確定簽章雜湊的開頭處有前置的 `/`。
    >[!NOTE]
    >將取代 `android:host` 值的「套件名稱」應顯示如下："com.azuresamples.msalandroidapp"。將取代 `android:path` 值的「簽章雜湊」應顯示如下："/1wIqXSqBj7w+h11ZifsnqwgyKrY="。您也可以在應用程式註冊的 [驗證] 刀鋒視窗中找到這些值。 請注意，您的重新導向 URI 會顯示如下："msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D"。 當簽章雜湊在此值的結尾處進行 URL 編碼時，即 **不應** 在您的 `android:path` 值中進行該簽章雜湊的 URL 編碼。

## <a name="use-msal"></a>使用 MSAL

### <a name="add-msal-to-your-project"></a>將 MSAL 新增至您的專案

1. 在 Android Studio 專案視窗中，瀏覽至 [app] > [src] > [build.gradle]，並新增下列項目：

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Microsoft Graph SDK 的詳細資訊](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>必要的匯入項目

將下列項目新增至 **app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java** 頂端

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>具現化 PublicClientApplication
#### <a name="initialize-variables"></a>初始化變數
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
在 `MainActivity` 類別中，請參照下列 onCreate() 方法，使用 `SingleAccountPublicClientApplication` 來具現化 MSAL。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
接聽按鈕，並據以呼叫方法或記錄錯誤。
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> 使用 MSAL 登出會從應用程式中移除關於使用者的所有已知資訊，但使用者仍可在其裝置上擁有作用中的工作階段。 如果使用者嘗試再次登入，他們可能會看到登入 UI，但可能不需要重新輸入其認證，因為裝置工作階段仍為作用中。

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
用於互動式要求的回呼。

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
用於無訊息要求的回呼
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>呼叫 Microsoft Graph API

下列程式碼示範如何使用 Graph SDK 來呼叫 GraphAPI。

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>新增 UI
### <a name="activity"></a>活動
如果您想要在本教學課程中建立 UI 的模型，下列方法將可指引您更新文字和接聽按鈕。

#### <a name="updateui"></a>updateUI
根據登入狀態和設定文字來啟用/停用按鈕。
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
用來更新 UI 中的文字以反映登出的方法。

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>配置

顯示按鈕和文字方塊的範例 `activity_main.xml` 檔案。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>測試應用程式

### <a name="run-locally"></a>在本機執行

將應用程式建置及部署至測試裝置或模擬器。 您應該已能夠登入 Azure AD 或個人 Microsoft 帳戶，並取得權杖。

在您登入之後，應用程式會顯示從 Microsoft Graph `/me` 端點傳回的資料。
PR 4
### <a name="consent"></a>同意

任何使用者在初次登入您的應用程式時，Microsoft 身分識別都會提示他們同意所要求的權限。 某些 Azure AD 租用戶已停用使用者同意，而需要系統管理員代表所有使用者表示同意。 若要支援此案例，您必須建立自己的租用戶或取得管理員同意。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除您在[註冊應用程式](#register-your-application)步驟中所建立的應用程式物件。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>下一步

深入了解如何建置行動應用程式，以在我們的多部分案例系列中呼叫受保護的 Web API。

> [!div class="nextstepaction"]
> [案例：呼叫 Web API 的行動應用程式](scenario-mobile-overview.md)
