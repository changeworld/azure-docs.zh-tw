---
title: MSAL for iOS 和 macOS 教學課程 - Microsoft 身分識別平台 | Azure
description: 了解 iOS 和 macOS (Swift) 應用程式如何使用 Microsoft 身分識別平台呼叫需要存取權杖的 API
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1dd0bc589b8290172d18482b36baf30c24d099b4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83640559"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>從 iOS 或 macOS 應用程式登入使用者並呼叫 Microsoft Graph

在本教學課程中，您將了解如何整合 iOS 或 macOS 應用程式與 Microsoft 身分識別平台。 應用程式會讓使用者登入、取得存取權杖來呼叫 Microsoft Graph API，以及對 Microsoft Graph API 提出要求。  

完成本指南後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。

>[!NOTE]
> 如果您不熟悉 Microsoft 身分識別平台，建議您從[從 iOS 或 macOS 應用程式登入使用者並呼叫 Microsoft Graph API](quickstart-v2-ios.md) 開始了解。

## <a name="how-this-tutorial-works"></a>本教學課程的運作方式

![示範本教學課程所產生的應用程式範例如何運作](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

此教學課程中的應用程式將會登入使用者，並且代表他們取得資料。  此資料會透過需要授權的受保護 API 來存取 (在此案例中為 Microsoft Graph API)，並會受到 Microsoft 身分識別平台保護。

具體而言：

* 您的應用程式會透過瀏覽器或 Microsoft Authenticator 來登入使用者。
* 終端使用者會接受您應用程式已要求的權限。
* 您的應用程式會針對 Microsoft Graph API 發出存取權杖。
* 此存取權杖會包含在 Web API 的 HTTP 要求中。
* 處理 Microsoft Graph 回應。

此範例會使用 Microsoft 驗證程式庫 (MSAL) 來實作驗證。 MSAL 會自動更新權杖、提供裝置上其他應用程式之間的 SSO，以及管理帳戶。

本教學課程適用於 iOS 和 macOS 應用程式。 請注意，這兩個平台間有一些的不同步驟。 

## <a name="prerequisites"></a>Prerequisites

- 您需要 XCode 11.x 版或更高版本，才能在本指南中建置應用程式。 您可以從 [iTunes 網站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下載 URL") 下載 XCode。
- Microsoft 驗證程式庫 ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 您可以使用相依性管理員或以手動方式新增程式庫。 以下指示會示範作法。

此教學課程會建立新的專案。 如果您想改為下載完整的教學課程，請下載程式碼：
- [iOS 範例程式碼](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS 範例程式碼](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>建立新專案

1. 開啟 Xcode，然後選取 [Create a new Xcode project] \(建立新的 Xcode 專案\)。
2. 針對 iOS 應用程式，選取 [iOS] > [單一檢視應用程式]，然後選取 [下一步]。
3. 針對 macOS 應用程式，選取 [macOS] > [Cocoa 應用程式]，然後選取 [下一步]。
4. 提供產品名稱。
5. 將 [語言] 設定為 [Swift]，然後選取 [下一步]。
6. 選取用來建立應用程式的資料夾，然後按一下 [建立]。

## <a name="register-your-application"></a>註冊您的應用程式

1. 移至 [Azure 入口網站](https://aka.ms/MobileAppReg)
2. 開啟 應用程式註冊刀鋒視窗，然後按一下 [+新增註冊]。
3. 輸入應用程式的**名稱**，無需設定重新導向 URI。
4. 在 [支援的帳戶類型] 下，選取**任何組織目錄中的帳戶 (任何 Azure AD 目錄 - 多租用戶) 和個人 Microsoft 帳戶 (例如 Skype、Xbox)**
5. 按一下 [註冊]
6. 在顯示的窗格中，從 [管理] 區段選取 [驗證]。

7. 在靠近畫面頂端的地方，按一下 [試用新體驗] 來開啟新的應用程式註冊體驗，然後按一下 [+ 新增註冊] > [+ 新增平台] > [iOS/macOS]。
    - 輸入您專案的組合識別碼。 如果您已下載程式碼，這會是 `com.microsoft.identitysample.MSALiOS`。 如果您要建立自己的專案，請在 Xcode 中選取您的專案，然後開啟 [一般] 索引標籤。[身分識別] 區段中會出現組合識別碼。
8. 按一下 `Configure` 並儲存出現在 [MSAL 組態] 頁面中的 [MSAL 組態]，以便稍後在設定應用程式時可加以輸入。  按一下 [完成] 。

## <a name="add-msal"></a>新增 MSAL

選擇下列其中一種方式，在您的應用程式中安裝 MSAL 程式庫：

### <a name="cocoapods"></a>CocoaPods

1. 如果您使用 [CocoaPods](https://cocoapods.org/)，請先在專案中 `.xcodeproj` 檔案所在的資料夾內，建立名為 `podfile` 的空白檔案，然後再安裝 `MSAL`。 將下面內容新增至 `podfile`：

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. 將 `<your-target-here>` 取代為您的專案名稱。
3. 在終端視窗中，流覽至包含所建立 `podfile` 的資料夾，然後執行 `pod install` 來安裝 MSAL 程式庫。
4. 關閉 Xcode 並開啟 `<your project name>.xcworkspace` 來重新載入 Xcode 中的專案。

### <a name="carthage"></a>Carthage

如果您使用 [Carthage](https://github.com/Carthage/Carthage) 來安裝 `MSAL`，可將其新增到 `Cartfile`：

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

從終端機視窗中，在與更新後的 `Cartfile` 相同的目錄中執行下列命令，讓 Carthage 更新專案中的相依性。

iOS：

```bash
carthage update --platform iOS
```

macOS：

```bash
carthage update --platform macOS
```

### <a name="manually"></a>手動

您也可以使用 Git 子模組或簽出最新版本來作為應用程式中的架構使用。

## <a name="add-your-app-registration"></a>新增您的應用程式註冊

接下來，我們會將您的應用程式註冊新增到您的程式碼。 

首先，在 `ViewController.swift` 和 `AppDelegate.swift` 或 `SceneDelegate.swift` 檔案頂端，新增下列 import 陳述式：

```swift
import MSAL
```

然後，將下列程式碼新增到 `viewDidLoad()` 前面的 `ViewController.swift`：

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
    
let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user
    
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

上方唯一需要修改的值是指派給 `kClientID` 的值，應修改為您的[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id)。 此值是 MSAL 設定資料的一部分，也就是本教學課程一開始以步驟引導您在 Azure 入口網站中註冊應用程式時，您所儲存的資料。

## <a name="configure-xcode-project-settings"></a>進行 Xcode 專案設定

將新的金鑰鏈群組新增至您的專案**簽署和功能**。 在 iOS 上金鑰鏈群組應為 `com.microsoft.adalcache`，而在 macOS 上應為 `com.microsoft.identity.universalstorage`。

![顯示應如何設定金鑰鏈群組的 Xcode UI](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>(僅適用於 iOS) 設定 URL 配置

您將在此步驟中註冊 `CFBundleURLSchemes`，讓使用者可以在登入之後重新導回應用程式。 順便一提，`LSApplicationQueriesSchemes` 也可讓您的應用程式使用 Microsoft Authenticator。

在 Xcode 中，以原始檔的形式開啟 `Info.plist`，並在 `<dict>` 區段內新增下列程式碼。 將 `[BUNDLE_ID]` 取代為您在 Azure 入口網站中使用的值，如果您已下載程式代碼，該值就會是 `com.microsoft.identitysample.MSALiOS`。 如果您要建立自己的專案，請在 Xcode 中選取您的專案，然後開啟 [一般] 索引標籤。[身分識別] 區段中會出現組合識別碼。

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>(僅適用於 macOS) 設定應用程式沙箱

1. 移至您的 Xcode 專案設定 > [功能] 索引標籤 > [應用程式沙箱]
2. 選取 [傳出連線 (用戶端)] 核取方塊。 

## <a name="create-your-apps-ui"></a>建立您應用程式的 UI

現在，請建立 UI 來包含一個可呼叫 Microsoft Graph API 的按鈕，以及另一個用來登出的按鈕，並且藉由將下列程式碼新增至 `ViewController` 類別來包含可查看部分輸出的文字檢視：

### <a name="ios-ui"></a>iOS UI

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {
        
    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right
        
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)
        
    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)
        
    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false
        
    self.view.addSubview(loggingText)
        
    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {
                
    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)
        
}
    
@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS UI

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!
    
var usernameLabel: NSTextField!

func initUI() {
        
    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)
        
    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false
        
    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false
        
    self.view.addSubview(loggingText)
        
    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

接下來，也是在 `ViewController` 類別內，將 `viewDidLoad()` 方法取代為：

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()
        
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
        
        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>使用 MSAL

### <a name="initialize-msal"></a>將 MSAL 初始化

將下列 `initMSAL` 方法新增至 `ViewController` 類別：

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

將 `initMSAL` 方法之後的下列內容新增至 `ViewController` 類別。

### <a name="ios-code"></a>iOS 程式碼：

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS 程式碼：

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>(僅適用於 iOS) 處理登入回呼

開啟 `AppDelegate.swift` 檔案。 若要在登入之後處理回呼，請在 `appDelegate` 類別中新增 `MSALPublicClientApplication.handleMSALResponse`，如下所示：

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**如果您使用的是 Xcode 11**，則應該改為將 MSAL 回呼放入 `SceneDelegate.swift`。
如果您同時支援 UISceneDelegate 和 UIApplicationDelegate 以便與舊版 iOS 相容，則必須將 MSAL 回呼放入這兩個檔案。

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

#### <a name="acquire-tokens"></a>取得權杖

現在，我們可以實作應用程式的 UI 處理邏輯，並以互動方式透過 MSAL 取得權杖。

MSAL 會公開兩個主要方法來取得權杖：`acquireTokenSilently()` 和 `acquireTokenInteractively()`： 

- 只要帳戶存在，`acquireTokenSilently()` 就會嘗試登入使用者並取得權杖，並且不會與使用者有任何互動。 `acquireTokenSilently()` 需要提供有效的 `MSALAccount`，可以使用其中一個 MSAL 帳戶列舉 API 來擷取。 這個範例會使用 `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` 來擷取目前的帳戶。 

- `acquireTokenInteractively()` 在嘗試登入使用者時，一律會顯示 UI。 它可能會使用瀏覽器中的工作階段 Cookie 或 Microsoft 驗證器中的帳戶，以提供互動式 SSO 體驗。

將下列程式碼新增至 `ViewController` 類別：

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {
        
        self.loadCurrentAccount { (account) in
            
            guard let currentAccount = account else {
                
                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }
            
            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main
                
        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in
            
            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }
            
            if let currentAccount = currentAccount {
                
                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")
                
                self.updateCurrentAccount(account: currentAccount)
                
                if let completion = completion {
                    completion(self.currentAccount)
                }
                
                return
            }
            
            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)
            
            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>以互動方式取得權杖

以下程式碼第一次取得權杖的方式是建立 `MSALInteractiveTokenParameters` 物件並呼叫 `acquireToken`。 接下來，您將新增程式碼來執行下列作業：

1. 以範圍建立 `MSALInteractiveTokenParameters`。
2. 使用已建立的參數呼叫 `acquireToken()`。
3. 處理錯誤。 如需詳細資訊，請參閱[適用於 iOS 和 macOS 的 MSAL 錯誤處理指南](msal-handling-exceptions.md)。
4. 處理成功的情況。

將下列程式碼新增至 `ViewController` 類別。

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>以無訊息方式取得權杖

若要以無訊息方式取得更新的權杖，請將下列程式碼新增至 `ViewController` 類別。 此動作會建立 `MSALSilentTokenParameters` 物件並呼叫 `acquireTokenSilent()`：

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        /**
         
         Acquire a token for an existing account silently
         
         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */
        
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
            
            if let error = error {
                
                let nsError = error as NSError
                
                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                
                if (nsError.domain == MSALErrorDomain) {
                    
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>呼叫 Microsoft Graph API 

一旦您擁有權杖後，您的應用程式就可以在 HTTP 標頭中使用此權杖，進而對 Microsoft Graph 提出已授權的要求：

| 標頭索引鍵    | value                 |
| ------------- | --------------------- |
| 授權 | 持有人 \<access-token> |

將下列程式碼新增至 `ViewController` 類別：

```swift
    func getContentWithToken() {
        
        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
        
        URLSession.shared.dataTask(with: request) { data, response, error in
            
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }
            
            self.updateLogging(text: "Result from Graph: \(result))")
            
            }.resume()
    }
```

請參閱 [Microsoft Graph API](https://graph.microsoft.com) 來深入了解 Microsoft Graph API。

### <a name="use-msal-for-sign-out"></a>使用 MSAL 登出

接著，新增登出的支援。

> [!Important]
> 使用 MSAL 登出會從應用程式中移除關於使用者的所有已知資訊，也會移除裝置上的裝置組態允許的作用中工作階段。 您也可以從瀏覽器選擇性地將使用者登出。

若要新增登出能力，請在 `ViewController` 類別中新增下列程式碼。 

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview
            
            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in
                
                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }
                
                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })
            
        }
    }
```

### <a name="enable-token-caching"></a>啟用權杖快取

根據預設，MSAL 會快取 iOS 或 macOS 金鑰鏈中的應用程式權杖。 

啟用權杖快取：
1. 確定您的應用程式已正確簽署
2. 移至您的 Xcode 專案設定 > [功能] 索引標籤  >  [啟用 Keychain 共用]
3. 按一下 **+** ，然後輸入下列 [金鑰鏈群組] 項目：3.a 針對 iOS 輸入 `com.microsoft.adalcache` 3.b 針對 macOS 輸入 `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>新增協助程式方法
將下列協助程式方法新增至 `ViewController` 類別以完成範例。

### <a name="ios-ui"></a>iOS UI：

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateAccountLabel() {
        
        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }
        
        self.usernameLabel.text = currentAccount.username
    }
    
    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS UI：

```swift
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>僅適用於 iOS，取得額外的裝置資訊

使用下列程式碼來讀取目前的裝置組態，包括裝置是否設定為共用：

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {
        
        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in
                
                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }
                
                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>多重帳戶應用程式

此應用程式專為單一帳戶案例所建置。 MSAL 也支援多重帳戶案例，但需要從應用程式執行一些額外的工作。 您必須建立 UI，以協助使用者針對每個需要權杖的動作，選取想要使用的帳戶。 或者，您的應用程式可以實作啟發學習法，從 MSAL 查詢所有帳戶來選取要使用的帳戶。 例如，請參閱 `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>測試應用程式

### <a name="run-locally"></a>在本機執行

將應用程式建置及部署至測試裝置或模擬器。 您應該已能夠登入 Azure AD 或個人 Microsoft 帳戶，並取得權杖。

使用者在初次登入您的應用程式時，Microsoft 身分識別都會提示他們同意所要求的權限。  大部分使用者都能夠親自同意，但某些 Azure AD 租用戶已停用使用者同意，而需要系統管理員代表所有使用者表示同意。 若要支援此案例，請在 Azure 入口網站中註冊您的應用程式範圍。

在您登入之後，應用程式會顯示從 Microsoft Graph `/me` 端點傳回的資料。

## <a name="get-help"></a>取得說明

對於本教學課程或 Microsoft 身分識別平台若有任何問題，請瀏覽[說明與支援](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)。
