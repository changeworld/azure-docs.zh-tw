---
title: 向使用 Azure 通知中心的 Swift iOS 應用發送推送通知 |微軟文檔
description: 瞭解如何將通知推送到使用 Azure 通知中心的 Swift iOS 應用。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336643"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>教程：使用通知中心 REST API 向 Swift iOS 應用發送推送通知

> [!div class="op_single_selector"]
> * [目標C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

在本教程中，您可以使用 Azure 通知中心使用[REST API](/rest/api/notificationhubs/)將通知推送到基於 Swift 的 iOS 應用程式。 您還可以創建一個空白的 iOS 應用，該應用程式使用[Apple 推送通知服務 （APN）](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)接收推送通知。

本教程將引導您完成以下步驟：

> [!div class="checklist"]
> * 生成證書簽名請求檔。
> * 請求應用推送通知。
> * 為應用創建預配設定檔。
> * 建立通知中樞。
> * 使用 APN 資訊配置通知中心。
> * 將 iOS 應用連接到通知中心。
> * 測試解決方案。

您可以在 [GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest) 上找到本教學課程的完整程式碼。

## <a name="prerequisites"></a>Prerequisites

若要繼續，您需要：

- 如果您不熟悉該服務，則流覽[Azure 通知中心概述](notification-hubs-push-notification-overview.md)。
- 瞭解[註冊和安裝](notification-hubs-push-notification-registration-management.md)。
- 一個活躍的[蘋果開發者帳戶](https://developer.apple.com)。
- 運行 Xcode 的 Mac 以及安裝到鑰匙串的有效開發人員證書。
- 物理 iPhone 設備，您可以運行和調試，因為您無法使用模擬器測試推送通知。
- 您在[Apple 門戶](https://developer.apple.com)註冊並與您的證書關聯的物理 iPhone 設備。
- 可以創建和管理資源的[Azure 訂閱](https://portal.azure.com)。

即使您以前沒有 iOS 開發經驗，您也應該能夠按照創建此第一原則示例的步驟進行操作。 但是，您將受益于熟悉以下概念：

- 使用 Xcode 和 Swift 構建 iOS 應用程式。
- 為 iOS 配置[Azure 通知中心](notification-hubs-ios-apple-push-notification-apns-get-started.md)。
- [蘋果開發人員門戶](https://developer.apple.com)和[Azure 門戶](https://portal.azure.com)。

> [!NOTE]
> 通知中心將配置為僅使用**沙箱**身份驗證模式。 不應為此身份驗證模式用於生產工作負載。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>將 iOS 應用連接到通知中心

在本節中，您將構建將連接到通知中心的 iOS 應用。  

### <a name="create-an-ios-project"></a>創建 iOS 專案

1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式] **** 範本。

1. 設置新專案的選項時：

   1. 指定在 Apple 開發人員門戶中設置**捆綁識別碼**時`com.<organization>`使用**的產品名稱**（PushDemo）**和組織識別碼**（ ）。

   1. **選擇****為應用 ID**設置的團隊。

   1. 將**語言**設置為**Swift**。

   1. 選取 [下一步]****。

1. 創建名為 **"支援檔**"的新資料夾。

1. 在 **"支援檔**"資料夾中創建新的 p 清單檔，稱為**devsettings.plist。** 請務必將此資料夾添加到**gitignore**檔中，以便在使用 git 存儲庫時不會提交該檔。 在生產應用中，您可能會有條件地將這些機密設置為自動生成過程的一部分。 本演練中未介紹此類設置。

1. 使用您預配的通知中心中的您自己的值，更新**devvalue.plist**以包括以下配置條目：

   | Key                            | 類型                     | 值                     |
   |--------------------------------| -------------------------| --------------------------|
   | 通知HubKey             | String                   | \<輪轂鍵>                  |
   | 通知HubKeyName         | String                   | \<鍵名稱>              |
   | 通知HubName            | String                   | \<>                 |
   | 通知 Hub 命名空間       | String                   | \<中心命名空間>            |

   您可以通過導航到 Azure 門戶中的通知中心資源來查找所需的值。 特別是，通知**HubName**和**通知 HubNamespace**值位於 **"概述"** 頁中 **"基本"** 摘要的右上角。

   ![通知中心要點摘要](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   您還可以通過導航到**訪問策略**並選擇相應的**訪問策略**（如`DefaultFullSharedAccessSignature`）來查找**通知 HubKeyName**和通知**HubKey**值。 之後，從**主連接字串**複製值`SharedAccessKeyName=`預綴 的 和`notificationHubKeyName`的值預綴為`SharedAccessKey=`。 `notificationHubKey`

   連接字串應採用以下格式：

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   要保持簡單，請指定`DefaultFullSharedAccessSignature`，以便可以使用權杖發送通知。 實際上，`DefaultListenSharedAccessSignature`對於只想接收通知的情況，這將是一個更好的選擇。

1. 在 **"專案導航器"** 下，選擇 **"專案名稱**"，然後選擇"**常規**"選項卡。

1. 查找**標識**，然後設置**捆綁識別碼**值，以便它匹配`com.<organization>.PushDemo`，這是上一步中**用於應用 ID**的值。

1. 查找**簽名&功能**，然後為您的**Apple 開發人員帳戶**選擇相應的**團隊**。 **Team**值應與您創建證書和設定檔的值匹配。

1. Xcode 應基於**捆綁識別碼**自動拉下相應的**預配設定檔**值。 如果看不到新的**預配設定檔**值，請嘗試通過選擇**Xcode** > **首選項** > **帳戶**刷新**簽名標識**的設定檔，然後選擇 **"下載手動設定檔"** 按鈕下載設定檔。

1. 仍然在 **"簽名&功能**"選項卡上，**按一下"+ 功能"** 按鈕，然後按兩下清單中的**推送通知**，以確保啟用**推送通知**。

1. 打開**AppDelegate.swift**檔以實現**UNUser通知中心委託**協定，並將以下代碼添加到類的頂部：

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

    稍後您將使用這些成員。 具體來說，您將使用**自訂範本**將**標記**成員用作註冊的一部分。 有關標記的詳細資訊，請參閱[註冊](notification-hubs-tags-segment-push-message.md)和[範本註冊標記](notification-hubs-templates-cross-platform-push-messages.md)。

1. 在同一檔中，將以下代碼添加到**didFinish 啟動選項**函數：

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

    此代碼從**devset.plist**檢索設置值 ，將**AppDelegate**類設置為**UNUser通知中心**委託，請求推送通知的授權，然後調用**寄存器ForRemote通知**。

    為了簡單，代碼僅支援*iOS 10 及更高版本*。 您可以像通常一樣，有條件地使用相應的 API 和方法來添加對早期作業系統版本的支援。

1. 在同一檔中，添加以下函數：

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

    代碼使用安裝**Id**和**pushChannel**值向通知中心註冊。 在這種情況下，您使用的是**UIDevice.current.識別碼forVendor**提供唯一值來標識設備，然後格式化**設備權杖**以提供所需的**push pushChannel**值。 **showAlert**功能的存在只是為了顯示一些消息文本，以便進行演示。

1. 仍在**AppDelegate.swift**檔中，將 **"將"將函數"** 和 **"已接收"** 函數添加到**UNUser 通知中心委託**。 當通知應用分別在前臺或後臺運行時，這些函數將顯示警報。

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

1. 將列印語句添加到**已註冊為遠端通知與設備權杖**函數的底部，以驗證是否分配**了安裝 Id**和**pushChannel**的值。

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. 為稍後將添加到專案的基礎元件創建**模型**、**服務和****實用程式**資料夾。

1. 檢查項目在物理設備上生成和運行。 無法使用模擬器測試推送通知。

### <a name="create-models"></a>建立模型

在此步驟中，您將創建一組模型來表示[通知中心 REST API](/rest/api/notificationhubs/)負載並存儲所需的共用訪問簽名 （SAS） 權杖資料。

1. 向 **"模型"** 資料夾添加新的 Swift 檔，稱為**PushTemplate.swift。** 此模型提供一個結構，表示單個範本的**BODY**作為**設備安裝**負載的一部分。

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. 向 **"模型"** 資料夾添加新的 Swift 檔，稱為**設備安裝.swift。** 此檔定義表示用於創建或更新**設備安裝**的有效負載的結構。 將下列程式碼新增至該檔案：

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

1. 向 **"模型"** 資料夾添加新的 Swift 檔，稱為**權杖資料.swift。** 此模型將用於存儲 SAS 權杖及其過期。

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

通知中心使用與 Azure 服務匯流排相同的安全基礎結構。 要調用 REST API，您需要[以程式設計方式生成](/rest/api/eventhub/generate-sas-token)可在請求**的授權**標頭中使用的 SAS 權杖。  

生成的權杖將採用以下格式：

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

流程本身涉及相同的六個關鍵步驟：  

1. 以[UNIX Epoch 時間](https://en.wikipedia.org/wiki/Unix_time)格式計算到期時間，這意味著自 1970 年 1 月 1 日午夜協調通用時間午夜以來經過的秒數。
1. 格式化表示您嘗試訪問的資源的**ResourceUrl，** 以便將其編碼百分比小寫小寫。 **資源 Url**具有`'https://<namespace>.servicebus.windows.net/<hubName>'`表單 。
1. 準備**StringtoSign**，其格式為`'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`。
1. 使用**StringToSign**值的 HMAC-SHA256 雜湊值計算和 Base64 編碼**簽名**。 雜湊值與相應**授權規則的****連接字串****的鍵**部分一起使用。
1. 格式化 Base64 編碼**簽名**，使其編碼百分比。
1. 使用**UrlEncoded 簽名**、**過期、****金鑰名稱**和**UrlEncodedResourceUrl**值以預期格式構造權杖。

有關共用訪問簽名以及 Azure 服務匯流排和通知中心如何使用共用訪問簽名的更全面概述，請參閱[Azure 服務匯流排文檔](../service-bus-messaging/service-bus-sas.md)。

為了此 Swift 示例，您將使用 Apple 的開源**通用加密庫**來説明對簽名進行雜湊處理。 因為它是一個 C 庫，因此在 Swift 中無法訪問它。 您可以使用橋接標頭使庫可用。

要添加和配置橋接標頭，

1. 在 Xcode 中 > ，選擇 **"檔新** > **檔** > **標頭檔**"。 **File** 命名標頭檔**橋接標題.h**。

1. 編輯檔以導入**通用HMAC.h**：

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. 更新目標的**生成設置**以引用橋接標頭：

   1. 點擊**PushDemo**專案，向下滾動到 **"快速編譯器"** 部分。

   1. 確保**安裝目標-C 相容性標頭**選項設置為 **"是**"。

   1. 將檔路徑`'<ProjectName>/BridgingHeader.h'`輸入**到目標 C 橋接標頭**選項中。 這是橋接標頭的檔路徑。

   如果找不到這些選項，請確保選擇了 **"全部"** 視圖，而不是 **"基本"** 或 **"自訂**"

   有許多協力廠商開源包裝庫可用，這可能使使用**通用加密**更容易一些。 但是，對此類庫的討論超出了本文的範圍。

1. 在**實用程式**資料夾中添加新的 Swift 檔，名為**權杖實用程式.swift，** 並添加以下代碼：

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

   此實用程式封裝了負責生成 SAS 權杖的邏輯。

   如前所述 **，getSasToken**函數協調準備權杖所需的高級步驟。 本教程稍後將由安裝服務調用該函數。

   其他兩個函數由**getSasToken**函式呼叫 **：sha256HMac**用於計算用於編碼關聯的 URL 字串的簽名和**urlEncodedString。** **URLEncodedString**函數是必需的，因為無法使用內置**的加百分比編碼**函數實現所需的輸出。

   [Azure 存儲 iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)是如何在目標 C 中處理這些操作的一個極好的示例。 有關 Azure 服務匯流排 SAS 權杖的詳細資訊，請參閱[Azure 服務匯流排文檔](../service-bus-messaging/service-bus-sas.md)。

1. 在**AppDelegate.swift**中，將以下代碼添加到*t寄存器ForRemote通知與設備權杖*函數中，以驗證**權杖實用程式.getSasToken**正在生成有效的權杖
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    請確保將**基底位址**字串中的預留位置值替換為您自己的

### <a name="verify-the-sas-token"></a>驗證 SAS 權杖

在用戶端中實現安裝服務之前，請檢查我們的應用是否使用您選擇的 HTTP 實用程式正確生成 SAS 權杖。 在本教程的目的，我們選擇的工具將是**郵遞員**。

記下應用生成的安裝**Id**和**權杖**值。

按照以下步驟調用**安裝**API：

1. 在**郵遞員**中，打開一個新選項卡。

1. 將請求設置為**GET**並指定以下位址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 按如下方式配置請求標頭：

   | Key           | 值            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | 授權 | \<薩塞權杖>       |
   | x-ms-version  | 2015-01          |

1. 選擇"**保存**"按鈕右上方顯示**的代碼**按鈕。 請求應類似于以下示例：

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

此時，不存在指定安裝**Id**的註冊。 驗證應導致"404 未找到"回應，而不是"401 未授權"回應。 此結果應確認 SAS 權杖已被接受。

### <a name="implement-the-installation-service-class"></a>實現安裝服務類

接下來，您將圍繞[安裝 REST API](/rest/api/notificationhubs/create-overwrite-installation)實現我們的基本包裝。  

在**服務**資料夾下添加新的 Swift 檔稱為**通知註冊服務.swift，** 然後將以下代碼添加到此檔：

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

作為初始化的一部分，提供了必要的詳細資訊。 標記和範本可選地傳遞到**寄存器**函數中，以構成**設備安裝**JSON 負載的一部分。  

**寄存器**函式呼叫其他私有函數來準備請求。 收到回應後，將調用完成，並指示註冊是否成功。  

請求終結點由**getBase位址**函數構造。 構造使用初始化期間提供的通知中心參數*命名空間和**名稱*。  

**getSasToken**函數檢查當前存儲的權杖是否有效。 如果權杖無效，該函數將調用 Token**實用程式**來生成新權杖，然後在傳回值之前存儲它。

最後 **，encodeToJson**將各自的模型物件轉換為JSON，作為請求正文的一部分使用。

### <a name="invoke-the-notification-hubs-rest-api"></a>調用通知中心 REST API

最後一步是更新**AppDelegate**以使用**通知註冊服務**向通知**Hub**註冊。

1. 打開**AppDelegate.swift**並添加類級變數以存儲對**引用的引用，即使用附加註冊服務和**通用**PushTemplate**：

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. 在同一檔中，更新**已註冊為遠端通知與設備權杖**函數以使用必要的參數初始化**通知註冊服務**，然後調用**寄存器**函數。

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

    為簡單，您將使用幾個列印語句使用**寄存器**操作的結果更新輸出視窗。

1. 現在，在物理設備上生成並運行應用。 您應該在輸出視窗中看到"已註冊"。

## <a name="test-the-solution"></a>測試方案

我們的應用程式在此階段註冊到**通知Hub，** 並可以接收推送通知。 在 Xcode 中，停止調試器並關閉應用（如果當前正在運行）。 接下來，檢查**設備安裝**詳細資訊是否按預期，並且我們的應用現在是否可以接收推送通知。  

### <a name="verify-the-device-installation"></a>驗證設備安裝

現在，您可以使用**Postman** [驗證 SAS 權杖](#verify-the-sas-token)，從而發出與之前相同的請求。 假設 SAS 權杖尚未過期，回應現在應包括您提供的安裝詳細資訊，如範本和標記。

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

如果以前的**SAS 權杖**已過期，則可以向**權杖實用程式**類**的第 24 行**添加**中斷點**，以獲得新的**SAS 權杖**，並使用該新值更新**授權**標頭。

### <a name="send-a-test-notification-azure-portal"></a>發送測試通知（Azure 門戶）

測試現在可以接收通知的最快方法是流覽到 Azure 門戶中的通知中心：

1. 在 Azure 門戶中，流覽到通知中心的 **"概述"** 選項卡。

1. 選擇 **"測試發送**"，該摘要位於門戶視窗左上角的 **"基本"** 摘要之上：

    ![通知中心要點摘要測試發送按鈕](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. 從 **"平臺**"清單中選擇**自訂範本**。

1. 輸入**12345**的 **"發送到標記運算式**"。 您之前在我們的安裝中指定了此標記。

1. 可以選擇在 JSON 負載中編輯**消息**：

    ![通知中心測試發送](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. 選擇 **"發送**"。 門戶應指示通知是否已成功發送到設備：

    ![通知中心測試發送結果](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    假設應用未在前臺運行，則還應在設備上**的通知中心**中看到通知。 點擊通知應打開應用並顯示警報。

    ![收到的通知示例](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>發送測試通知（郵件運營商）

您可以使用**Postman**通過[REST API](/rest/api/notificationhubs/)發送通知，這可能是一種更方便的測試方法。

1. 在**郵遞員**中打開一個新選項卡。

1. 將請求設置為**POST**，並輸入以下位址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 按如下方式配置請求標頭：

   | Key                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | 應用程式/json;字元\utf-8 |
   | 授權                  | \<薩塞權杖>                     |
   | 服務匯流排通知-格式  | template                       |
   | Tags                           | "12345"                        |

1. 將請求**BODY**配置為使用具有以下 JSON 負載的**RAW - JSON（應用程式.json）：**

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. 選擇 **"代碼"** 按鈕，該按鈕位於視窗右上角的 **"保存**"按鈕下。 請求應類似于以下示例：

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

您應該獲得**201 創建**成功狀態碼，並在用戶端設備上接收通知。

## <a name="next-steps"></a>後續步驟
現在，您擁有了通過[REST API](/rest/api/notificationhubs/)連接到通知中心的基本 iOS Swift 應用，並可以發送和接收通知。 如需詳細資訊，請參閱下列文章：

- [Azure 通知中心概述](notification-hubs-push-notification-overview.md)
- [通知中樞 REST API](/rest/api/notificationhubs/)
- [用於後端操作的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 上的通知中心 SDK](https://github.com/Azure/azure-notificationhubs)
- [使用應用程式後端註冊](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [註冊管理](notification-hubs-push-notification-registration-management.md)
- [使用標記](notification-hubs-tags-segment-push-message.md) 
- [使用自訂範本](notification-hubs-templates-cross-platform-push-messages.md)
- [具有共用訪問簽名的服務匯流排存取控制](../service-bus-messaging/service-bus-sas.md)
- [以程式設計方式生成 SAS 權杖](/rest/api/eventhub/generate-sas-token)
- [蘋果安全：通用加密](https://developer.apple.com/security/)
- [UNIX 紀元時間](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
