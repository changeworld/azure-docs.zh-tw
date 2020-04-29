---
title: 將推播通知傳送至使用 Azure 通知中樞的 Swift iOS 應用程式 |Microsoft Docs
description: 瞭解如何將通知推送至使用 Azure 通知中樞的 Swift iOS 應用程式。
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336643"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>教學課程：使用通知中樞 REST API 將推播通知傳送至 Swift iOS 應用程式

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

在本教學課程中，您會使用 Azure 通知中樞，透過[REST API](/rest/api/notificationhubs/)將通知推送至 Swift 型 iOS 應用程式。 您也會使用[Apple Push Notification service （APNs）](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)，建立可接收推播通知的空白 iOS 應用程式。

本教學課程會引導您完成下列步驟：

> [!div class="checklist"]
> * 產生憑證簽署要求檔案。
> * 要求您的應用程式進行推播通知。
> * 建立應用程式的布建設定檔。
> * 建立通知中樞。
> * 使用 APNs 資訊來設定通知中樞。
> * 將您的 iOS 應用程式連線至通知中樞。
> * 測試方案。

您可以在 [GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest) 上找到本教學課程的完整程式碼。

## <a name="prerequisites"></a>先決條件

若要繼續，您需要：

- 如果您不熟悉此服務，請流覽[Azure 通知中樞的總覽](notification-hubs-push-notification-overview.md)。
- 以瞭解[註冊和安裝](notification-hubs-push-notification-registration-management.md)。
- 有效的[Apple 開發人員帳戶](https://developer.apple.com)。
- 執行 Xcode 的 Mac 以及安裝在您 Keychain 中的有效開發人員憑證。
- 您可以使用執行和進行 debug 的實體 iPhone 裝置，因為您無法使用模擬器測試推播通知。
- 您在[Apple 入口網站](https://developer.apple.com)中註冊的實體 iPhone 裝置，並與您的憑證相關聯。
- [Azure 訂](https://portal.azure.com)用帳戶，您可以在其中建立及管理資源。

即使您先前沒有使用 iOS 開發的經驗，您還是應該能夠遵循建立此第一個原則範例的步驟。 不過，您將受益于熟悉下列概念：

- 使用 Xcode 和 Swift 建立 iOS 應用程式。
- 設定適用于 iOS 的[Azure 通知中樞](notification-hubs-ios-apple-push-notification-apns-get-started.md)。
- [Apple 開發人員入口網站](https://developer.apple.com)和[Azure 入口網站](https://portal.azure.com)。

> [!NOTE]
> 通知中樞將設定為只使用**沙箱**驗證模式。 您不應該將此驗證模式用於生產工作負載。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>將您的 iOS 應用程式連線至通知中樞

在本節中，您會建立將連接到通知中樞的 iOS 應用程式。  

### <a name="create-an-ios-project"></a>建立 iOS 專案

1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式] **** 範本。

1. 設定新專案的選項時：

   1. 指定在 Apple 開發人員入口網站中設定套件組合`com.<organization>`**識別碼**時，所使用的**產品名稱**（PushDemo）和**組織識別碼**（）。

   1. 選擇要為其設定**應用程式識別碼**的**小組**。

   1. 將**語言**設定為**Swift**。

   1. 選取 [下一步]  。

1. 建立名為**SupportingFiles**的新資料夾。

1. 在**SupportingFiles**資料夾中建立名為**devsettings plist**的新 p 清單檔案。 請務必將此資料夾新增至您的 **.gitignore**檔案，使其在使用 git 存放庫時不會被認可。 在生產應用程式中，您可能會有條件地將這些秘密設定為自動化組建流程的一部分。 此逐步解說不涵蓋這類設定。

1. 使用您布建的通知中樞中您自己的值，更新**devsettings plist**以包含下列設定專案：

   | Key                            | 類型                     | 值                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | 字串                   | \<hubKey>                  |
   | notificationHubKeyName         | 字串                   | \<hubKeyName>              |
   | notificationHubName            | 字串                   | \<hubName>                 |
   | notificationHubNamespace       | 字串                   | \<hubNamespace>            |

   流覽至 [Azure 入口網站中的 [通知中樞] 資源，即可找到所需的值。 特別是， **notificationHubName**和**notificationHubNamespace**值位於 [**總覽**] 頁面中 [**基本**] 摘要的右上角。

   ![通知中樞 Essentials 摘要](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   您也可以流覽至 [**存取原則**]，然後選取個別的**存取原則**（例如），以尋找`DefaultFullSharedAccessSignature` **notificationHubKeyName**和**notificationHubKey**值。 之後，從**主要連接字串** `SharedAccessKeyName=`複製前置詞為`notificationHubKeyName`的值，並在前面`SharedAccessKey=`加上的值。 `notificationHubKey`

   連接字串應採用下列格式：

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   若要讓它保持簡單`DefaultFullSharedAccessSignature` ，請指定，讓您可以使用權杖來傳送通知。 實際上，在`DefaultListenSharedAccessSignature`您只想要接收通知的情況下，會是較佳的選擇。

1. 在 [**專案導覽器**] 底下，選取**專案名稱**，然後選取 [**一般**] 索引標籤。

1. 尋找 [身分**識別**]，然後設定 [套件組合**識別碼**] `com.<organization>.PushDemo`值，使其符合，也就是上一個步驟中用於**應用程式識別碼**的值。

1. 尋找**簽署 & 功能**，然後為您的**Apple 開發人員帳戶**選取適當的**小組**。 小組值應符合您用來建立憑證和設定檔的**專案**。

1. Xcode 應該根據套件組合**識別碼**，自動拉出適當的布建**設定檔**值。 如果您沒有看到新的布建**設定檔**值，請嘗試藉由選取 [ **Xcode** > **喜好** > **帳戶**] 來重新整理**簽署識別**的設定檔，然後選取 [**下載手動設定檔**] 按鈕以下載設定檔。

1. 仍在 [**簽署 & 功能**] 索引標籤上，按一下 [ **+ 功能**] 按鈕，然後按兩下清單中的 [**推播通知**]，以確保已啟用**推播通知**。

1. 開啟您的**AppDelegate**檔案以執行**UNUserNotificationCenterDelegate**通訊協定，並將下列程式碼新增至類別的頂端：

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    您稍後將會用到這些成員。 具體而言，您會使用「**標記**」成員做為使用**自訂範本**註冊的一部分。 如需標記的詳細資訊，請參閱註冊和[範本註冊](notification-hubs-templates-cross-platform-push-messages.md)[的標記](notification-hubs-tags-segment-push-message.md)。

1. 在相同的檔案中，將下列程式碼新增至**didFinishLaunchingWithOptions**函式：

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    此程式碼會從**devsettings**中抓取設定值，將**AppDelegate**類別設定為**UNUserNotificationCenter**委派，要求授權進行推播通知，然後呼叫**registerForRemoteNotifications**。

    為了簡單起見，此程式碼*僅支援 iOS 10 和更新版本*。 您可以有條件地使用個別的 Api 和方法來新增對舊版 OS 版本的支援，就像平常一樣。

1. 在相同的檔案中，新增下列函數：

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    程式碼會使用**installationId**和**pushChannel**值向通知中樞註冊。 在此情況下，您會使用**UIDevice**來提供唯一值來識別裝置，然後將**deviceToken**格式化以提供所需的**pushChannel**值。 **ShowAlert**函式只是為了示範而顯示一些郵件內文。

1. 仍在**AppDelegate swift**檔案中，將**willPresent**和**DidReceive**函式新增至**UNUserNotificationCenterDelegate**。 這些函式會在通知應用程式分別在前景或背景中執行時顯示警示。

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. 將 print 語句加入至**didRegisterForRemoteNotificationsWithDeviceToken**函式的底部，以確認**installationId**和**pushChannel**已指派給值。

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. 針對您稍後將新增至專案的基礎元件，建立**模型**、**服務**和**公用程式**資料夾。

1. 檢查項目是否在實體裝置上建立並執行。 您無法使用模擬器來測試推播通知。

### <a name="create-models"></a>建立模型

在此步驟中，您將建立一組模型來代表[通知中樞 REST API](/rest/api/notificationhubs/)裝載，並儲存所需的共用存取簽章（SAS）權杖資料。

1. 將名為**PushTemplate**的新 Swift 檔案新增至 [**模型**] 資料夾。 此模型提供的結構代表個別範本的**主體**，做為**DeviceInstallation**裝載的一部分。

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. 將名為**DeviceInstallation**的新 Swift 檔案新增至 [**模型**] 資料夾。 此檔案會定義代表用於建立或更新**裝置安裝**之承載的結構。 將下列程式碼新增至該檔案：

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. 將名為**TokenData**的新 Swift 檔案新增至 [**模型**] 資料夾。 此模型將用來儲存 SAS 權杖及其到期日。

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>產生 SAS 權杖

通知中樞使用與 Azure 服務匯流排相同的安全性基礎結構。 若要呼叫 REST API，您必須以程式設計方式產生可用於要求之**Authorization**標頭中的[SAS 權杖](/rest/api/eventhub/generate-sas-token)。  

產生的權杖會採用下列格式：

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

進程本身牽涉到相同的六個主要步驟：  

1. 以[UNIX Epoch 時間](https://en.wikipedia.org/wiki/Unix_time)格式計算到期日，這表示從國際標準時間（1970年1月1日午夜）起經過的秒數。
1. 將代表您嘗試存取之資源的**ResourceUrl**格式化，使其具有百分比編碼和小寫。 **ResourceUrl**的格式`'https://<namespace>.servicebus.windows.net/<hubName>'`為。
1. 準備**StringToSign**，其格式為`'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`。
1. 使用**StringToSign**值的 HMAC-SHA256 哈**希來計算**和 Base64 編碼簽章。 雜湊值會與個別**授權規則**之**連接字串**的索引**鍵**部分搭配使用。
1. 格式化 Base64 編碼簽章 **，使其**以百分比編碼。
1. 使用**UrlEncodedSignature**、 **ExpiryEpoch**、 **KeyName**和**UrlEncodedResourceUrl**值，以預期的格式來建立權杖。

請參閱[Azure 服務匯流排檔](../service-bus-messaging/service-bus-sas.md)，以深入瞭解共用存取簽章，以及 Azure 服務匯流排和通知中樞使用它的方式。

基於這個 Swift 範例的目的，您將使用 Apple 的開放原始碼**CommonCrypto**程式庫，協助簽章的雜湊。 因為它是 C 程式庫，所以無法在現成可用的情況下存取。 您可以使用橋接標頭讓程式庫可用。

若要新增和設定橋接標頭：

1. 在 Xcode 中，**選取** > [檔案] [**新增** > **File** > ] [檔案**標頭檔**]。 將標頭檔命名為**BridgingHeader**。

1. 編輯檔案以匯入**CommonHMAC。 h**：

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. 更新目標的**組建設定**以參考橋接標頭：

   1. 請在**PushDemo**專案上按一下，並向下卷到 [ **Swift 編譯器**] 區段。

   1. 確定 [**安裝目標-C 相容性標頭**] 選項設定為 **[是]**。

   1. 在 [ `'<ProjectName>/BridgingHeader.h'` **目標-C 橋接標頭**] 選項中輸入檔案路徑。 這是我們橋接標頭的檔案路徑。

   如果您找不到這些選項，請確定您已選取 [ **All** ]，而不是 [**基本**] 或 [**自訂**]。

   有許多協力廠商開放原始碼包裝函式程式庫可讓您更輕鬆地使用**CommonCrypto** 。 不過，這類程式庫的討論已超出本文的範圍。

1. 在 [**公用程式**] 資料夾內新增名為**TokenUtility**的 swift 檔案，然後新增下列程式碼：

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   這個公用程式會封裝負責產生 SAS 權杖的邏輯。

   如先前所述， **getSasToken**函數會協調準備權杖所需的高層級步驟。 稍後在本教學課程中，將會由安裝服務呼叫此函式。

   **GetSasToken**函式會呼叫其他兩個函式： **sha256HMac**來計算簽章，並使用**urlEncodedString**來編碼相關聯的 URL 字串。 **UrlEncodedString**函式是必要的，因為無法使用內建的**addingPercentEncoding**函數來達到所需的輸出。

   [Azure 儲存體 IOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)是如何在目標-C 中進行這些作業的絕佳範例。 如需 Azure 服務匯流排 SAS 權杖的進一步資訊，請參閱[Azure 服務匯流排檔](../service-bus-messaging/service-bus-sas.md)。

1. 在**AppDelegate**中，將下列程式碼新增至*didRegisterForRemoteNotificationsWithDeviceToken*函式，以確認**TokenUtility getSasToken**正在產生有效的權杖
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    請務必以您自己的來取代**baseAddress**字串中的預留位置值。

### <a name="verify-the-sas-token"></a>驗證 SAS 權杖

在用戶端中執行安裝服務之前，請先使用您選擇的 HTTP 公用程式，檢查我們的應用程式是否已正確產生 SAS 權杖。 基於本教學課程的目的，我們的選擇工具將會**Postman**。

記下應用程式所產生的**installationId**和**token**值。

請遵循下列步驟來呼叫**安裝**API：

1. 在 [ **Postman**] 中，開啟新的索引標籤。

1. 設定要**取得**的要求，並指定下列位址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 設定要求標頭，如下所示：

   | Key           | 值            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | 授權 | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. 選取出現在 [**儲存**] 按鈕右下方的 [程式**代碼**] 按鈕。 要求看起來應該類似下列範例：

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. 選取 [Send]**** \(傳送\) 按鈕。

此時，指定的**installationId**不存在任何註冊。 驗證應該會產生「404找不到」回應，而不是「401未經授權」回應。 此結果應確認已接受 SAS 權杖。

### <a name="implement-the-installation-service-class"></a>執行安裝服務類別

接下來，您將在[REST API 安裝](/rest/api/notificationhubs/create-overwrite-installation)時，執行基本的包裝函式。  

在 [**服務**] 資料夾底下，新增名為**NotificationRegistrationService**的 swift 檔案，然後將下列程式碼新增至此檔案：

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

初始化過程中會提供必要的詳細資料。 標記和範本會選擇性地傳遞至**register**函式，以形成**裝置安裝**JSON 承載的一部分。  

**Register**函數會呼叫其他私用函式來準備要求。 收到回應之後，就會呼叫完成，並指出註冊是否成功。  

要求端點是由**getBaseAddress**函式所構成。 此結構會使用在初始化期間所提供的通知中樞參數*命名空間*和*名稱*。  

**GetSasToken**函數會檢查目前儲存的權杖是否有效。 如果權杖無效，則函式會呼叫**TokenUtility**來產生新的權杖，然後在傳回值之前加以儲存。

最後， **encodeToJson**會將個別的模型物件轉換成 JSON，以作為要求主體的一部分使用。

### <a name="invoke-the-notification-hubs-rest-api"></a>叫用通知中樞 REST API

最後一個步驟是更新**AppDelegate** ，以使用**NotificationRegistrationService**向我們的**NotificationHub**註冊。

1. 開啟**AppDelegate** ，並新增類別層級變數來儲存**NoficiationRegistrationService**和一般**PushTemplate**的參考：

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. 在相同的檔案中，更新**didRegisterForRemoteNotificationsWithDeviceToken**函式以初始化具有必要參數的**NotificationRegistrationService** ，然後呼叫**register**函式。

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    為了簡單起見，您將使用幾個 print 語句，以**註冊**作業的結果來更新 [輸出] 視窗。

1. 現在，在實體裝置上建立並執行應用程式。 您應該會在 [輸出] 視窗中看到「已註冊」。

## <a name="test-the-solution"></a>測試方案

此階段的應用程式會向**NotificationHub**註冊，並可接收推播通知。 在 Xcode 中，停止偵錯工具並關閉應用程式（如果它目前正在執行）。 接下來，請檢查**裝置安裝**詳細資料是否如預期般運作，而且我們的應用程式現在可以接收推播通知。  

### <a name="verify-the-device-installation"></a>驗證裝置安裝

您現在可以使用**Postman**來[驗證 SAS 權杖](#verify-the-sas-token)，以建立與先前相同的要求。 假設 SAS 權杖尚未過期，回應現在應該會包含您所提供的安裝詳細資料，例如範本和標記。

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

如果您先前的**SAS 權杖**已過期，您可以將**中斷點**新增至**TokenUtility**類別的**第24行**，以取得新的**SAS 權杖**，並以該新的值更新**授權**標頭。

### <a name="send-a-test-notification-azure-portal"></a>傳送測試通知（Azure 入口網站）

測試您現在可以接收通知的最快方法，就是流覽至 Azure 入口網站中的通知中樞：

1. 在 [Azure 入口網站中，流覽至通知中樞的 [**總覽**] 索引標籤。

1. 選取 [**測試傳送**]，其位於入口網站視窗左上角的 [**基本**功能摘要] 上方：

    ![通知中樞 Essentials 摘要測試傳送按鈕](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. 從 [**平臺**] 清單中選擇 [**自訂範本**]。

1. 針對 [**傳送至標記] 運算式**輸入**12345** 。 您先前已在安裝中指定此標記。

1. 選擇性地編輯 JSON 承載中的**訊息**：

    ![通知中樞測試傳送](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. 選取 [**傳送**]。 入口網站應該會指出是否已成功將通知傳送至裝置：

    ![通知中樞測試傳送結果](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    假設應用程式未在前景中執行，您也應該會在裝置上的**通知中心**看到通知。 點擊通知應該會開啟應用程式，並顯示警示。

    ![收到的通知範例](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>傳送測試通知（郵件承運商）

您可以使用**Postman**透過[REST API](/rest/api/notificationhubs/)傳送通知，這可能是更方便的測試方式。

1. 在 [ **Postman**] 中開啟新的索引標籤。

1. 將 [要求] 設定為 [**張貼**]，然後輸入下列位址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 設定要求標頭，如下所示：

   | Key                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json; 字元集 = utf-8 |
   | 授權                  | \<sasToken>                     |
   | ServiceBusNotification-格式  | template                       |
   | Tags                           | "12345"                        |

1. 將**要求本文**設定為使用具有下列 JSON 承載的**原始 json （application. json）** ：

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. 選取 [程式**代碼**] 按鈕，這會在視窗右上方的 [**儲存**] 按鈕底下。 要求看起來應該類似下列範例：

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. 選取 [Send]**** \(傳送\) 按鈕。

您應該會收到**201 建立**的成功狀態碼，並在用戶端裝置上收到通知。

## <a name="next-steps"></a>後續步驟
您現在已透過[REST API](/rest/api/notificationhubs/)將基本 iOS Swift 應用程式連線至通知中樞，而且可以傳送和接收通知。 如需詳細資訊，請參閱下列文章：

- [Azure 通知中樞總覽](notification-hubs-push-notification-overview.md)
- [通知中樞 REST API](/rest/api/notificationhubs/)
- [適用于後端作業的通知中樞 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 上的通知中樞 SDK](https://github.com/Azure/azure-notificationhubs)
- [向應用程式後端註冊](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [註冊管理](notification-hubs-push-notification-registration-management.md)
- [使用標記](notification-hubs-tags-segment-push-message.md) 
- [使用自訂範本](notification-hubs-templates-cross-platform-push-messages.md)
- [使用共用存取簽章服務匯流排存取控制](../service-bus-messaging/service-bus-sas.md)
- [以程式設計方式產生 SAS 權杖](/rest/api/eventhub/generate-sas-token)
- [Apple security：一般加密](https://developer.apple.com/security/)
- [UNIX Epoch 時間](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
