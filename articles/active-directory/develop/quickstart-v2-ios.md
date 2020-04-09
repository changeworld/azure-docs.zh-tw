---
title: Microsoft 身分識別平台 iOS 和 macOS 快速入門 | Azure
description: 了解如何在 iOS 或 macOS 應用程式中登入使用者及查詢 Microsoft Graph。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 3b0d8c8aa73903572a6a310e07aae57ec71f9cb7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473641"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>快速入門：從 iOS 或 macOS 應用程式登入使用者並呼叫 Microsoft Graph API

本快速入門包含的程式碼範例，會示範原生 iOS 或 macOS 應用程式如何使用 Microsoft 身分識別平台來登入個人、公司與學校帳戶、取得存取權杖，以及呼叫 Microsoft Graph API。

本快速入門適用於 iOS 和 macOS 應用程式。 只有 iOS 應用程式才需要某些步驟。 這些步驟會標注其僅適用於 iOS。

![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **先決條件**
> * XCode 10+
> * iOS 10+ 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
> 有兩個選項可用來啟動快速入門應用程式：
> * [快速] [選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [手動] [選項 2：註冊並手動設定您的應用程式和程式碼範例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要註冊您的應用程式，
> 1. 移至新的 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs)窗格。
> 1. 輸入應用程式的名稱，並選取 [註冊]  。
> 1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://aka.ms/MobileAppReg)頁面。
> 1. 選取 [新增註冊]  。
> 1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
>      - 在 [名稱]  區段中，輸入當應用程式的使用者登入或同意使用您的應用程式時，將向使用者顯示且有意義的應用程式名稱。
>      - 略過此頁面上的其他設定。
>      - 選取 `Register`。
> 1. 在 [管理]  區段中，選取 `Authentication` > `Add Platform` > `iOS`。
>      - 為您的應用程式輸入***套件組合識別碼***。 套件組合識別碼只是可唯一識別應用程式的唯一字串，例如 `com.<yourname>.identitysample.MSALMacOS`。 記下您所使用的值。
>      - 請注意，iOS 組態也適用於 macOS 應用程式。
> 1. 選取 `Configure` 並儲存 [MSAL 組態] 詳細資料，以供稍後在本快速入門中使用。
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>步驟 1:設定您的應用程式
> 若要讓此快速入門中的程式碼範例正確運作，您必須新增與 Auth 訊息代理程式相容的重導 URI。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這項變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-ios/green-check.png) 您的應用程式已設定了這些屬性

#### <a name="step-2-download-the-sample-project"></a>步驟 2:下載範例專案

- [下載 iOS 的程式碼範例](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [下載 macOS 的程式碼範例](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>步驟 3：安裝相依性

在終端視窗中，瀏覽至包含所下載程式碼範例的資料夾，然後執行 `pod install` 來安裝最新的 MSAL 程式庫。

#### <a name="step-4-configure-your-project"></a>步驟 4：設定您的專案

> [!div renderon="docs"]
> 如果您已選取上面的選項 1，則可以略過這些步驟。 

> [!div renderon="portal" class="sxs-lookup"]
> 1. 將 ZIP 檔案解壓縮並在 XCode 中開啟專案。
> 1. 編輯 **ViewController.swift**，並使用下列程式碼片段來取代開頭為 'let kClientID' 的那一行。 請記得使用您在本快速入門中稍早於入口網站註冊應用程式時所儲存的用戶端識別碼來更新 `kClientID` 的值：
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. 編輯 **ViewController.swift**，並使用下列程式碼片段來取代開頭為 'let kAuthority' 的那一行：
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. 編輯 **ViewController.swift**，並使用下列程式碼片段來取代開頭為 'let kGraphEndpoint' 的那一行：
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. 開啟專案設定。 在 [身分識別]  區段中，輸入您在入口網站中輸入的 [套件組合識別碼]  。
> 1. (僅限於 iOS) 以滑鼠右鍵按一下 **Info.plist**，然後選取 [開啟為]   > [原始程式碼]  。
> 1. (僅限於 iOS) 在 dict 根節點下，使用您在入口網站中輸入的 [套件組合識別碼] 取代 `CFBundleURLSchemes`。
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. 建置並執行應用程式！
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
>
> 1. 將 ZIP 檔案解壓縮並在 XCode 中開啟專案。
> 1. 編輯 **ViewController.swift**，並使用下列程式碼片段來取代開頭為 'let kClientID' 的那一行。 請記得使用您在本快速入門中稍早於入口網站註冊應用程式時所儲存的用戶端識別碼來更新 `kClientID` 的值：
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. 如果您要為 [Azure AD 國家雲端](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)建置應用程式，請使用正確端點來取代開頭為 'let kGraphEndpoint' 的那一行。 在 [全域存取] 中，請使用預設值：
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. 其他端點則記載在[這裡](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)。 例如，若要執行 Azure AD Germany 的快速入門，請使用下列內容：
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. 開啟專案設定。 在 [身分識別]  區段中，輸入您在入口網站中輸入的 [套件組合識別碼]  。
> 1. (僅限於 iOS) 以滑鼠右鍵按一下 **Info.plist**，然後選取 [開啟為]   > [原始程式碼]  。
> 1. (僅限於 iOS) 在 dict 根節點下，使用您在入口網站中使用的 [套件組合識別碼] 取代 `Enter_the_bundle_Id_Here`。
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. 建置並執行應用程式！ 

## <a name="more-information"></a>相關資訊

請閱讀這些小節以深入了解本快速入門。

### <a name="get-msal"></a>取得 MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc) \(英文\)) 是用來登入使用者並要求權杖的程式庫，該權杖可用來存取受 Microsoft 身分識別平台保護的 API。 您可以使用下列程序將 MSAL 新增至您的應用程式：

```
$ vi Podfile

```
將下列內容新增至此 podfile (其中含有您的專案目標)：

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

執行 CocoaPods 安裝命令：

```pod install```

### <a name="initialize-msal"></a>將 MSAL 初始化

您可以透過加入下列程式碼來新增 MSAL 的參考：

```swift
import MSAL
```

接著，使用下列程式碼將 MSAL 初始化：

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |其中： ||
> |---------|---------|
> | `clientId` | 來自在 *portal.azure.com* 中註冊之應用程式的應用程式識別碼 |
> | `authority` | Microsoft 身分識別平台端點。 在大部分情況下，這會是 *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | 應用程式的重新導向 URI。 您可以傳遞 'nil' 來使用預設值或您自訂的重新導向 URI。 |

### <a name="for-ios-only-additional-app-requirements"></a>其他應用程式需求 (僅限於 iOS)

您的應用程式在 `AppDelegate` 中也必須具有下列內容。 這讓 MSAL SDK 能夠在您執行驗證時，處理來自驗證訊息代理程式應用程式的權杖回應。

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> 在 iOS 13+ 上，如果您採用 `UISceneDelegate`而非 `UIApplicationDelegate`，請改為將此程式碼放入 `scene:openURLContexts:` 回呼中 (請參閱 [Apple 文件](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc))。
> 如果您同時支援 UISceneDelegate 和 UIApplicationDelegate 以便與舊版 iOS 相容，則必須將 MSAL 回呼放在這兩個地方。

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

最後，您的應用程式在 ***Info.plist*** 中的 `CFBundleURLTypes` 旁邊必須有 `LSApplicationQueriesSchemes` 項目。 此範例會包含此項目。 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>登入使用者與要求權杖

MSAL 有兩種取得權杖的方法：`acquireToken` 與 `acquireTokenSilent`。

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken：以互動方式取得權杖

某些情況需要使用者與 Microsoft 身分識別平台互動。 在這些情況下，終端使用者可能需要選取自己的帳戶、輸入認證，或同意您應用程式的權限。 例如， 

* 使用者首次登入應用程式
* 如果使用者重設自己的密碼，就必須輸入自己的認證 
* 您的應用程式第一次要求存取資源時
* 需要 MFA 或其他條件式存取原則時

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |其中：||
> |---------|---------|
> | `scopes` | 包含所要求的範圍 (即適用於 Microsoft Graph 的 `[ "user.read" ]` 或適用於自訂 Web API 的 `[ "<Application ID URL>/scope" ]` (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent：以無訊息方式取得存取權杖

應用程式應該不需要使用者在每次要求權杖時都必須登入。 如果使用者已經登入，這個方法可允許應用程式以無訊息方式要求權杖。 

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in
            
   guard let account = currentAccount else {
      return
   }
            
   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |其中： ||
> |---------|---------|
> | `scopes` | 包含所要求的範圍 (即適用於 Microsoft Graph 的 `[ "user.read" ]` 或適用於自訂 Web API 的 `[ "<Application ID URL>/scope" ]` (`api://<Application ID>/access_as_user`) |
> | `account` | 要求權杖的帳戶。 本快速入門關於單一帳戶應用程式。 如果您想要建置多帳戶的應用程式，則必須使用 `accountsFromDeviceForParameters:completionBlock:` 定義邏輯來識別要針對權杖要求使用哪一個帳戶，並傳遞正確的 `accountIdentifier` |

## <a name="next-steps"></a>後續步驟

嘗試用於 iOS 和 macOS 的教學課程以取得建置應用程式的完整逐步指南，包括本快速入門的完整說明。

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>了解如何建立本快速入門中所使用的應用程式

> [!div class="nextstepaction"]
> [針對 iOS 和 macOS 呼叫圖形 API 教學課程](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

協助我們改善 Microsoft 身分識別平台。 完成問卷調查簡短的兩個問題，告訴我們您的想法。

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台問卷調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
