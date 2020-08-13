---
title: Microsoft 身分識別平台 Android 快速入門 | Azure
description: 了解 Android 應用程式如何呼叫需要來自 Microsoft 身分識別平台端點存取權杖的 API。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: a46cd1b916edeae8a24fb997db46e5a0651567cb
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115266"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>快速入門：從 Android 應用程式登入使用者並呼叫 Microsoft Graph API

此快速入門將使用程式碼範例示範 Android 應用程式如何使用 Microsoft 身分識別平台來登入個人、公司或學校帳戶，然後取得存取權杖並呼叫 Microsoft Graph API。 (如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。)

應用程式必須以 Azure Active Directory 中的應用程式物件表示，讓 Microsoft 身分識別平台能夠為您的應用程式提供權杖。

> [!div renderon="docs"]
> 為了方便起見，`AndroidManifest.xml` 檔案中的程式碼範例會隨附預先設定的預設 `redirect_uri`，因此您不需要先註冊自己的應用程式物件。 `redirect_uri` 中有某些部分是以您應用程式的簽署金鑰為基礎。 範例專案會使用簽署金鑰預先設定，讓提供的 `redirect_uri` 可以使用。 若要深入了解如何註冊應用程式物件，並將其與您的應用程式整合，請參閱[從 Android 應用程式登入使用者並呼叫 Microsoft Graph](tutorial-v2-android.md) 教學課程。


> [!NOTE]
> **先決條件**
> * Android Studio 
> * Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式 
>  若要讓此快速入門中的程式碼範例正確運作，您必須新增與 Auth 訊息代理程式相容的重導 URI。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-android/green-check.png) 您的應用程式已設定了這些屬性
>
> ### <a name="step-2-download-the-project"></a>步驟 2:下載專案 
> [!div class="sxs-lookup" renderon="portal"]
> 使用 Android Studio 執行專案。
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步驟 3：您的應用程式已設定並準備好執行
> 我們已使用您的應用程式屬性值來設定您的專案，且專案已可供執行。 
> 範例應用程式會在 [單一帳戶模式]  畫面上啟動。 依預設會提供預設範圍 **user.read**，這是在 Microsoft Graph API 呼叫期間讀取您自己的設定檔資料時所使用的範圍。 依預設會提供 Microsoft Graph API 呼叫的 URL。 您可以視需要變更這兩項設定。
>
> ![顯示單一和多個帳戶使用量的 MSAL 範例應用程式](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> 使用應用程式功能表，在單一和多重帳戶模式之間進行變更。
>
> 在單一帳戶模式中，使用工作或主帳戶登入：
>
> 1. 選取 [以互動方式取得圖形資料]  ，以提示使用者輸入其認證。 您會在畫面底部看到 Microsoft Graph API 呼叫的輸出。
> 2. 登入之後，選取 [以無訊息方式取得圖形資料]  以呼叫 Microsoft Graph API，而不再次提示使用者提供認證。 您會在畫面底部看到 Microsoft Graph API 呼叫的輸出。
>
> 在多重帳戶模式中，您可以重複相同的步驟。  此外，您也可以移除已登入的帳戶，這也會移除該帳戶的快取權杖。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>步驟 1:取得範例應用程式
>
> [下載程式碼](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)。
>
> ## <a name="step-2-run-the-sample-app"></a>步驟 2:執行範例應用程式
>
> 從 Android Studio 的**可用裝置**下拉式清單中選取您的模擬器或實體裝置，然後執行應用程式。
>
> 範例應用程式會在 [單一帳戶模式]  畫面上啟動。 依預設會提供預設範圍 **user.read**，這是在 Microsoft Graph API 呼叫期間讀取您自己的設定檔資料時所使用的範圍。 依預設會提供 Microsoft Graph API 呼叫的 URL。 您可以視需要變更這兩項設定。
>
> ![顯示單一和多個帳戶使用量的 MSAL 範例應用程式](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> 使用應用程式功能表，在單一和多重帳戶模式之間進行變更。
>
> 在單一帳戶模式中，使用工作或主帳戶登入：
>
> 1. 選取 [以互動方式取得圖形資料]  ，以提示使用者輸入其認證。 您會在畫面底部看到 Microsoft Graph API 呼叫的輸出。
> 2. 登入之後，選取 [以無訊息方式取得圖形資料]  以呼叫 Microsoft Graph API，而不再次提示使用者提供認證。 您會在畫面底部看到 Microsoft Graph API 呼叫的輸出。
>
> 在多重帳戶模式中，您可以重複相同的步驟。  此外，您也可以移除已登入的帳戶，這也會移除該帳戶的快取權杖。

## <a name="how-the-sample-works"></a>此範例的運作方式
![範例應用程式的螢幕擷取畫面](media/quickstart-v2-android/android-intro.svg)


程式碼會組織成片段，說明如何撰寫單一和多重帳戶 MSAL 應用程式。 程式碼檔案的組織方式如下：

| 檔案  | 示範  |
|---------|---------|
| MainActivity | 管理 UI |
| MSGraphRequestWrapper  | 使用 MSAL 所提供的權杖來呼叫 Microsoft Graph API |
| MultipleAccountModeFragment  | 初始化多重帳戶應用程式、載入使用者帳戶，並取得權杖以呼叫 Microsoft Graph API |
| SingleAccountModeFragment | 初始化單一帳戶應用程式、載入使用者帳戶，並取得權杖以呼叫 Microsoft Graph API |
| res/auth_config_multiple_account.json  | 多重帳戶組態檔 |
| res/auth_config_single_account.json  | 單一帳戶組態檔 |
| Gradle Scripts/build.grade (Module:app) | 在此會新增 MSAL 程式庫相依性 |

現在我們將更詳細地探討這些檔案，並呼叫在每個檔案中 MSAL 特有的程式碼。

### <a name="adding-msal-to-the-app"></a>將 MSAL 新增至應用程式

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) 這個程式庫是用來登入使用者並要求權杖，該權杖會用來存取受 Microsoft 身分識別平台保護的 API。 當您在 [Gradle Scripts]   > [build.gradle (Module: app)]  中的 [相依性]  底下新增以下內容時，Gradle 3.0+ 就會安裝該程式庫：

```gradle  
implementation 'com.microsoft.identity.client:msal:1.+'
```

您可以在 build.gradle (Module: app) 的範例專案中看到此內容：

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

這會指示 Gradle 從 Maven 中央存放庫下載並建置 MSAL。

### <a name="msal-imports"></a>MSAL 匯入

與 MSAL 程式庫相關的匯入為 `com.microsoft.identity.client.*`。  例如，您會看到 `import com.microsoft.identity.client.PublicClientApplication;`，這是 `PublicClientApplication` 類別的命名空間，代表您的公用用戶端應用程式。

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

此檔案示範如何建立單一帳戶 MSAL 應用程式，並呼叫 Microsoft Graph API。

單一帳戶應用程式僅供單一使用者使用。  例如，您可能只會有一個用來登入對應應用程式的帳戶。

#### <a name="single-account-msal-initialization"></a>單一帳戶 MSAL 初始化

在 `auth_config_single_account.json` 的 `onCreateView()` 中，儲存在 `auth_config_single_account.json` 檔案中的組態資訊會用來建立單一帳戶 `PublicClientApplication`。  這就是您初始化 MSAL 程式庫以供單一帳戶 MSAL 應用程式使用的方式：

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>登入使用者

在 `SingleAccountModeFragment.java` 中，用來登入使用者的程式碼位於 `initializeUI()` 的 `signInButton` 點擊處理常式中。

在嘗試取得權杖前，請先呼叫 `signIn()`。 `signIn()` 的行為會如同呼叫了 `acquireToken()`，而對登入的使用者產生互動式提示。

使用者登入是非同步作業。 在使用者登入後，會傳遞呼叫 Microsoft Graph API 的回呼，並更新 UI：

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>登出使用者

在 `SingleAccountModeFragment.java` 中，用來登出使用者的程式碼位於 `initializeUI()` 的 `signOutButton` 點擊處理常式中。  將使用者登出是非同步作業。 將使用者登出後，也會清除該帳戶的權杖快取。 當使用者帳戶登出後，會建立一個更新 UI 的回呼：

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>以互動或無訊息方式取得權杖

若要向使用者呈現最少的提示數，您通常會以無訊息方式取得權杖。 然後，如果發生錯誤，請嘗試以互動方式取得權杖。 應用程式第一次呼叫 `signIn()` 時，會有效地作為 `acquireToken()` 的呼叫，這會提示使用者提供認證。

在某些情況下，可能會提示使用者選取其帳戶、輸入認證，或同意應用程式要求的權限，這些情況包括：

* 使用者第一次登入應用程式時
* 如果使用者重設自己的密碼，就必須輸入自己的認證
* 如果已撤銷同意
* 如果您的應用程式明確要求同意
* 您的應用程式第一次要求存取資源時
* 需要 MFA 或其他條件式存取原則時

在 `SingleAccountModeFragment.java` 中，以互動方式 (也就是使用者必須使用 UI 的方式) 取得權杖的程式碼，位於 `initializeUI()` 的 `callGraphApiInteractiveButton` 點擊處理常式中：

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

如果使用者已經登入，`acquireTokenSilentAsync()` 可讓應用程式以無訊息方式在 `callGraphApiSilentButton` 點擊處理常式中要求權杖，如 `initializeUI()` 中所說明：

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>載入帳戶

載入帳戶的程式碼位於 `SingleAccountModeFragment.java` 的 `loadAccount()` 中。  載入使用者的帳戶是非同步作業，因此會將在帳戶載入、變更或發生錯誤時予以處理的回呼傳至 MSAL。  下列程式碼也會處理 `onAccountChanged()`，此情況會在移除帳戶、使用者變更為另一個帳戶時發生。

```java
private void loadAccount() {
    ...

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
```

#### <a name="call-microsoft-graph"></a>呼叫 Microsoft Graph

當使用者登入時，對 Microsoft Graph 的呼叫會透過 `callGraphAPI()` 所要求的 HTTP 來進行，其定義位於 `SingleAccountModeFragment.java` 中。 此函式是一個包裝函式，可藉由執行如下的工作來簡化範例：從 `authenticationResult` 取得存取權杖，並將呼叫封裝至 MSGraphRequestWrapper，以及顯示呼叫的結果。

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

這是使用單一帳戶的 MSAL 應用程式的組態檔。

如需這些欄位的說明，請參閱[了解 Android MSAL 組態檔](msal-configuration.md)。

請注意 `"account_mode" : "SINGLE"` 的存在，這會將此應用程式設定為使用單一帳戶。

`"client_id"` 已預先設定為使用 Microsoft 維護的應用程式物件註冊。
`"redirect_uri"` 已預先設定為使用程式碼範例所提供的簽署金鑰。

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

此檔案示範如何建立多重帳戶 MSAL 應用程式，並呼叫 Microsoft Graph API。

舉例來說，可讓您使用多個使用者帳戶 (例如工作帳戶和個人帳戶) 的電子郵件應用程式，即為多重帳戶應用程式。

#### <a name="multiple-account-msal-initialization"></a>多重帳戶 MSAL 初始化

在 `MultipleAccountModeFragment.java` 檔案的 `onCreateView()` 中，儲存在 `auth_config_multiple_account.json file` 中的組態資訊會用來建立多重帳戶應用程式物件 (`IMultipleAccountPublicClientApplication`)：

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

建立的 `MultipleAccountPublicClientApplication` 物件會儲存在類別成員變數中，以便用來與 MSAL 程式庫互動，而取得權杖以及載入和移除使用者帳戶。

#### <a name="load-an-account"></a>載入帳戶

多重帳戶應用程式通常會呼叫 `getAccounts()`，以選取要用於 MSAL 作業的帳戶。 載入帳戶的程式碼位於 `MultipleAccountModeFragment.java` 檔案的 `loadAccounts()` 中。  載入使用者帳戶是非同步作業。 因此，會以回呼處理帳戶載入、變更或發生錯誤時的情況。

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>以互動或無訊息方式取得權杖

在某些情況下，可能會提示使用者選取其帳戶、輸入認證，或同意應用程式要求的權限，這些情況包括：

* 使用者首次登入應用程式
* 如果使用者重設自己的密碼，就必須輸入自己的認證 
* 如果已撤銷同意 
* 如果您的應用程式明確要求同意 
* 您的應用程式第一次要求存取資源時
* 需要 MFA 或其他條件式存取原則時

多重帳戶應用程式通常應以互動方式取得權杖，也就是使用者必須使用 UI，並呼叫 `acquireToken()`。  在 `MultipleAccountModeFragment.java` 檔案中，以互動方式取得權杖的程式碼位於 `initializeUI()` 的 `callGraphApiInteractiveButton` 點擊處理常式中：

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

應用程式應該不需要使用者在每次要求權杖時都必須登入。 如果使用者已經登入，`acquireTokenSilentAsync()` 可讓應用程式要求權杖，而不會對使用者發出提示，如 `MultipleAccountModeFragment.java` 中 `initializeUI()` 的 `callGraphApiSilentButton` 點擊處理常式所示：

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>移除帳戶

在 `MultipleAccountModeFragment.java` 檔案的 `initializeUI()` 中，移除帳戶 (包含帳戶的任何快取權杖) 所用的程式碼位在用於移除帳戶按鈕的處理常式中。 您必須要有從 MSAL 方法 (如 `getAccounts()` 和 `acquireToken()`) 取得的帳戶物件，才能移除帳戶。 由於移除帳戶是非同步作業，因此會提供 `onRemoved` 回呼以更新 UI。

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

這是使用多個帳戶的 MSAL 應用程式的組態檔。

如需各個欄位的說明，請參閱[了解 Android MSAL 組態檔](msal-configuration.md)。

不同於 [auth_config_single_account.json](#auth_config_single_accountjson) 組態檔，此組態檔具有 `"account_mode" : "MULTIPLE"` (而非 `"account_mode" : "SINGLE"`)，因為這是多重帳戶應用程式。

`"client_id"` 已預先設定為使用 Microsoft 維護的應用程式物件註冊。
`"redirect_uri"` 已預先設定為使用程式碼範例所提供的簽署金鑰。

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
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

## <a name="next-steps"></a>後續步驟

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>了解建立本快速入門中所使用之應用程式的步驟

試著遵循＜[從 Android 應用程式登入使用者並呼叫 Microsoft Graph](tutorial-v2-android.md)＞教學課程，此教學課程會逐步引導您建立 Android 應用程式來取得存取權杖，並使用該權杖呼叫 Microsoft Graph API。

> [!div class="nextstepaction"]
> [呼叫 Graph API Android 教學課程](./tutorial-v2-android.md)

### <a name="msal-for-android-library-wiki"></a>適用於 Android 的 MSAL 程式庫的 Wiki

閱讀適用於 Android 的 MSAL 程式庫的詳細資訊：

> [!div class="nextstepaction"]
> [適用於 Android 的 MSAL 程式庫的 Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]