---
title: 使用 Azure 通知中樞和 REST API 將推播通知傳送至 Swift iOS 應用程式
description: 在本教學課程中，您將了解如何使用 Azure 通知中樞和 REST API 將推播通知傳送至 iOS 裝置。
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127103"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>教學課程：使用通知中樞 REST API 將推播通知傳送至 Swift iOS 應用程式

本教學課程將說明如何搭配使用 Azure 通知中樞和 REST API 將推播通知傳送至 iOS 應用程式。

本教學課程涵蓋下列步驟：

- 建立 iOS 應用程式範例。
- 將您的 iOS 應用程式連線至 Azure 通知中樞。
- 傳送測試推播通知。
- 確認您的應用程式可接收通知。

您可以從 [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples) 下載本教學課程的完整程式碼。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- 執行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及安裝在您 Keychain 中的有效開發人員憑證。

- 執行 iOS 10 或更新版本的 iPhone 或 iPad。

- 在 [Apple 入口網站](https://developer.apple.com/)中註冊且與您憑證相關聯的實體裝置。

在繼續之前，請務必先完成上一個教學課程，以了解如何開始使用[適用於 iOS 應用程式的 Azure 通知中樞](https://go.microsoft.com/fwlink/?linkid=2129801)，以在您的通知中樞內設定推送認證。 即使您先前沒有 iOS 開發的經驗，您應該仍然可以遵循這些步驟。

> [!NOTE]
> 基於推播通知的組態需求，您必須在實體 iOS 裝置 (iPhone 或 iPad)，而不是在 iOS 模擬器上部署和測試推播通知。

在下列各節中，您會建立連線到通知中樞的 iOS 應用程式。

## <a name="create-an-ios-project"></a>建立 iOS 專案

1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式] ****  範本。

2. 為新專案設定選項時：
   - 指定 **產品名稱** (PushDemo) 和 **組織識別碼** (`com.<organization>`)，這是您在 Apple 開發人員入口網站設定 [套件組合識別碼] 時所使用的資訊。
   - 選擇已設定 **[應用程式識別碼]**  的 **小組** 。
   - 將 **[語言]**  設定為 **Swift**。
   - 選取 [下一步] **** 。

3. 建立名為 **SupportingFiles** 的新資料夾。

4. 在 **SupportingFiles** 資料夾中，建立名為 **devsettings.plist** 的新 p-list 檔案。 請務必將此資料夾新增至您的 **gitignore** 檔案，讓其在使用 git 存放庫時不會受到認可。 在生產應用程式中，您可以有條件地將這些祕密設定為自動化建置程序的一部分。 本教學課程未涵蓋這些設定。

5. 從您佈建的通知中樞內，使用您自己的值來更新 **devsettings.plist** ，以包含下列設定項目：

   | **索引鍵**                  | **型別** | **ReplTest1**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | String   | `<hubKey>`       |
   | notificationHubKeyName   | String   | `<hubKeyName>`   |
   | notificationHubName      | String   | `<hubName>`      |
   | notificationHubNamespace | String   | `<hubNamespace>` |

   瀏覽至 Azure 入口網站中的通知中樞資源，即可找到所需的值。 特別的是， **notificationHubName** 和 **notificationHubNamespace** 的值位於 [概觀] ****  頁面中 [基本資訊] ****  摘要的右上角。

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="必要值":::

   您也可以瀏覽至 **[存取原則]**  並選取個別的存取原則 **** (例如 **DefaultFullSharedAccessSignature**)，來尋找 **notificationHubKeyName** 和 **notificationHubKey** 的值。 之後，請從  **[主要連接字串]**   複製前置詞為  `SharedAccessKeyName=`  ** 的 notificationHubKeyName** 值，以及前置詞為  `SharedAccessKey=`  的  **notificationHubKey** 值。 連接字串的格式應如下所示：

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   為求簡化，請指定 **DefaultFullSharedAccessSignature**，讓您可以使用權杖來傳送通知。 在實務上，如果您只想要接收通知，則 **DefaultListenSharedAccessSignature** 在是較佳的選擇。

6. 在 **[專案導覽器]** 底下，選取 **[專案名稱]**  ，然後選取 **[一般]**  索引標籤。

7. 尋找 **[身分識別]**  ，然後設定 **[套件組合識別碼]**  的值，使其符合 `com.<organization>.PushDemo`，這是上一個步驟中，用來作為 **[應用程式識別碼]**  的值。

8. 尋找 **[簽署與功能]** ，然後為您的 **[Apple 開發人員帳戶]**  選取適當的 **[小組]** 。  **[小組]**  值應符合您用來建立憑證和設定檔的值。

9. Xcode 應該會根據 **[組合套件識別碼]** ，自動下載適當的 **[佈建設定檔]**  值。 如果您沒有看到新的 **[佈建設定檔]**  值，請嘗試重新整理 **[簽署身分識別]**  的設定檔，方法是依序選取 **[Xcode]** 、 **[喜好設定]** 、 **[帳戶]** ，然後選取 **[下載手動設定檔]**  按鈕來下載設定檔。

10. 繼續在 **[簽署與功能]**  索引標籤上，按一下 **[+ 功能]**  按鈕，然後從清單中點選 **** [推播通知] ，以確保已啟用 **[推播通知]**  。

11. 開啟您的 **AppDelegate.swift** 檔案，以執行 **UNUserNotificationCenterDelegate** 通訊協定，並將下列程式碼新增至類別的頂端：

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

    您稍後將會用到這些成員。 具體而言，您將在使用 **[自訂範本]** 進行註冊時使用 **[標記]**  成員。 如需有關標記的詳細資訊，請參閱 [＜用於註冊的標記](notification-hubs-tags-segment-push-message.md)＞ 和＜ [範本註冊](notification-hubs-templates-cross-platform-push-messages.md)＞。

12. 在相同的檔案中，將下列程式碼新增至**didFinishLaunchingWithOptions** 函式：

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

    此程式碼會從 **devsettings.plist**中擷取設定、將 **AppDelegate** 類別設定為 **UNUserNotificationCenter** 委派、要求授權來進行推播通知，然後呼叫 **registerForRemoteNotifications**。

    為了簡單起見，此程式碼只支援 iOS 10 和更新版本。 您可以有條件地使用個別 API 和方法來新增舊版 iOS 的支援，方法就跟平常所做的一樣。

13. 在相同檔案中新增下列程式碼：

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

    此程式碼會使用 **installationId** 和 **pushChannel** 值向通知中樞註冊。 在此案例中，您會使用 **UIDevice.current.identifierForVendor** 提供用來識別裝置的唯一值，然後格式化 **deviceToken** 來提供所需的 **pushChannel** 值。  **showAlert** 函式的存在，只是為了顯示一些示範用的訊息文字。

14. 繼續在 **AppDelegate.swift** 檔案中，將 **willPresent** 和 **didReceive** 函式新增至 **UNUserNotificationCenterDelegate**。 這些函式會在收到應用程式在前景或背景中執行的通知時顯示警示。

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

15. 將 `print` 陳述式加入 **didRegisterForRemoteNotificationsWithDeviceToken** 函式的底部，以確認 **installationId** 和 **pushChannel** 會收到指派的值：

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. 建立 **[模型]** 、 **[服務]** 和 **[公用程式]**  資料夾，以供您稍後要新增至專案的基礎元件使用。

17. 檢查專案是否在實體裝置上建立並執行。 您無法使用模擬器來測試推播通知。

## <a name="create-models"></a>建立模型

在此步驟中，您會建立一組模型來代表 [通知中樞 REST API](/rest/api/notificationhubs/) 承載，並儲存所需的共用存取簽章 (SAS) 權杖資料。

1. 將名為 **PushTemplate.swift** 的新 Swift 檔案新增至 **[模型]**  資料夾。 此模型會定義一個結構，表示個別範本的 **BODY** 是 **DeviceInstallation** 承載的一部分。

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. 將名為 **DeviceInstallation.swift** 的新 Swift 檔案新增至 **[模型]**  資料夾。 此檔案會定義結構，以代表用來建立或更新 **[裝置安裝]** 的承載。 將下列程式碼新增至該檔案：

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

3. 將名為 **TokenData.swift** 的新 Swift 檔案新增至 **[模型]**  資料夾。 此模型會用來儲存 SAS 權杖及其到期日。 將下列程式碼新增至該檔案：

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

## <a name="generate-a-sas-token"></a>產生 SAS 權杖

通知中樞會使用與 Azure 服務匯流排相同的安全性基礎結構。 若要呼叫 REST API，請 [以程式設計方式產生 SAS 權杖](/rest/api/eventhub/generate-sas-token)，其可用在要求的 **[授權]**  標頭中。

產生的權杖會採用下列格式：

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

程序本身牽涉到相同的六個步驟：

1. 計算 [UNIX Epoch 時間](https://en.wikipedia.org/wiki/Unix_time) 格式的到期日，這表示從 1970 年 1 月 1 日 (國際標準時間) 午夜起經過的秒數。

2. 將 **ResourceUrl** (代表您要嘗試存取的資源) 格式化，使其以百分比編碼和小寫呈現。  **ResourceUrl** 的格式為 `https://<namespace>.servicebus.windows.net/<hubName>`。

3. 準備 **StringToSign**，其格式為 `<UrlEncodedResourceUrl>\n<ExpiryEpoch>`。

4. 使用 **StringToSign** 值的 HMAC-SHA256 雜湊，計算並以 Base64 編碼 **[簽章]**  。 針對個別 **[授權規則]** ，雜湊值會與 **[連接字串]**  的 **[金鑰]**  部分搭配使用。

5. 將 Base64 編碼的 **[簽章]**  格式化，使其以百分比編碼呈現。

6. 使用 **UrlEncodedSignature**、 **ExpiryEpoch**、 **KeyName** 和 **UrlEncodedResourceUrl** 值，以預期的格式來建立權杖。

如需共用存取簽章的完整概觀，以及 Azure 服務匯流排和通知中樞使用簽章的方式，請參閱 [Azure 服務匯流排文件](../service-bus-messaging/service-bus-sas.md) 。

基於此 Swift 範例的目的，您會使用 Apple 開放原始碼 **CommonCrypto** 程式庫來協助完成簽章雜湊。 由於這是 C 程式庫，所以無法在 Swift 中直接存取。 您可以使用橋接標頭來讓程式庫可供使用。

若要新增和設定橋接標頭：

1. 在 Xcode 中，依序選取 **[檔案]** 、 **[新增]** 、 **[檔案]** ，然後選取 **[標頭檔案]** 。 將標頭檔命名為 **BridgingHeader.h**。

2. 編輯要匯入 **CommonHMAC.h**的檔案：

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. 更新 **[組建設定]**  目標以參考橋接標頭：

   1. 選取 **[PushDemo]**  專案，並向下捲動至 **[Swift 編譯器]**  區段。

   2. 請確定 **[安裝 Objective-C 相容性標頭]**  選項已設定為 **[是]** 。

   3. 在 **[Objective-C 橋接標頭]**  選項中，輸入檔案路徑 `<ProjectName>/BridgingHeader.h` 。 這是橋接標頭的檔案路徑。

   如果您找不到這些選項，請確定您已選取 **[所有]**  視圖，而不是 **[基本]**  或 **[自訂]** 。

   有許多第三方開放原始碼包裝函式程式庫可讓您更輕鬆地使用 **CommonCrypto** 。 不過，這些程式庫的討論已超出本文的範圍。

4. 在 **[公用程式]**  資料夾內，新增名為 **TokenUtility** 的 Swift 檔案，然後新增下列程式碼：

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

   此公用程式會封裝負責產生 SAS 權杖的邏輯。

   如先前所述， **getSasToken** 函式會協調準備權杖所需的高階步驟。 稍後在本教學課程中，安裝服務將會呼叫此函式。

   其他兩個函式會由 **getSasToken** 函式所呼叫： **sha256HMac** 用於計算簽章，而 **urlEncodedString** 用於編碼相關聯的 URL 字串。  **urlEncodedString** 函式是必要的，因為無法使用內建的 **addingPercentEncoding** 函式來達到所需的輸出。

    [Azure 儲存體 iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) 是在 Objective-C 中進行這些作業的絕佳範例。 如需有關 Azure 服務匯流排 SAS 權杖的深入資訊，請參閱 [Azure 服務匯流排文件](../service-bus-messaging/service-bus-sas.md)。

5. 在 **AppDelegate.swift**中，將下列程式碼新增至 `didRegisterForRemoteNotificationsWithDeviceToken` 函式，以確認 **TokenUtility.getSasToken** 將產生有效的權杖

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   請務必以您自己的值取代 **baseAddress** 字串中的預留位置。

## <a name="verify-the-sas-token"></a>驗證 SAS 權杖

在用戶端中執行安裝服務之前，請先檢查您的應用程式是否使用 HTTP 公用程式正確地產生 SAS 權杖。 基於本教學課程的目的，我們選擇的工具是 **Postman**。

記下應用程式所產生的 **installationId** 和 **token** 值。

請遵循下列步驟來呼叫 **[安裝]**  API：

1. 在 **Postman** 中，開啟新的索引標籤。
2. 將要求設定為 **GET** 並指定下列位址：

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. 設定要求標頭，如下所示：

   | **索引鍵**       | **ReplTest1**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | 授權 | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. 選取出現在 **[儲存]**  按鈕右下方的 **[程式碼]**  按鈕。 要求看起來應該會類似以下的範例：

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. 選取 **[傳送]**  按鈕。

此時，指定的 **installationId** 並沒有註冊。 驗證應該會產生「404找不到」回應，而不是「401未經授權」回應。 此結果應是確認已接受 SAS 權杖。

## <a name="implement-the-installation-service-class"></a>執行安裝服務類別

接下來，在安裝 [REST API](/rest/api/notificationhubs/create-overwrite-installation)周圍執行基本包裝函式。

在 **[服務]**  資料夾底下，新增名為 **NotificationRegistrationService.swift** 的新 Swift 檔案，然後將下列程式碼新增至此檔案：

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

初始化過程中會提供必要的詳細資料。 標記和範本會選擇性地傳遞至 **register** 函式，以形成 **裝置安裝** JSON 承載的一部分。

 **register** 函式會呼叫其他私人函式來準備要求。 然後在收到回應之後呼叫完成，並指出註冊是否成功。

要求端點是由 **getBaseAddress** 函式所構成。 此結構會使用初始化期間提供的通知中樞參數 *namespace* 和 *name* 。

 **getSasToken** 函式會檢查目前儲存的權杖是否有效。 如果權杖無效，函式會呼叫 **TokenUtility** 來產生新的權杖，然後在傳回值之前加以儲存。

最後， **encodeToJson** 會將個別的模型物件轉換成 JSON，以作為要求主體的一部分使用。

## <a name="invoke-the-notification-hubs-rest-api"></a>叫用通知中樞 REST API

最後一個步驟是更新 **AppDelegate** ，以使用 **NotificationRegistrationService** 向 **NotificationHub**註冊。

1. 開啟 **AppDelegate** 並新增類別層級變數，以儲存 **NoficiationRegistrationService** 和泛型 **PushTemplate**的參考：

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. 在相同的檔案中，更新 **didRegisterForRemoteNotificationsWithDeviceToken** 函式，以初始化具有必要參數的 **NotificationRegistrationService** ，然後呼叫 **register** 函式。

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

   為了簡單起見，程式碼會使用 `print` 陳述式，以 **register** 作業的結果來更新輸出視窗。

3. 在實體裝置上建立並執行應用程式。 您應該會在輸出視窗中看到**已註冊**。

## <a name="test-the-solution"></a>測試解決方案

此時，應用程式會向 **NotificationHub** 註冊，並可接收推播通知。 在 Xcode 中，停止偵錯工具並關閉應用程式 (如果其正在執行)。 接下來，請檢查 **[裝置安裝]**  詳細資料是否會如預期般出現，而且應用程式現在可以接收推播通知。

### <a name="verify-the-device-installation"></a>驗證裝置安裝

您現在可以使用 **Postman** 來 [驗證 SAS 權杖](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token)，以提出與先前相同的要求。 假設 SAS 權杖尚未過期，則回應現在應該會包含您所提供的安裝詳細資料，例如範本和標記。

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

如果先前的 SAS 權杖已過期，您可以將中斷點新增至 **TokenUtility** 類別的第 24 行，以取得新的 SAS 權杖，並以此新值更新 **[授權]**  標頭。

### <a name="send-a-test-notification-azure-portal"></a>傳送測試通知 (Azure 入口網站)

測試您現在是否可以接收通知的最快方法，就是瀏覽至 Azure 入口網站中的通知中樞：

1. 在 Azure 入口網站中，瀏覽至通知中樞上的 **[概觀]**  索引標籤。

2. 選取 **[測試傳送]** ，其位於入口網站視窗左上方的 **[基礎資訊]**  摘要上方：

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="通知中樞基礎資訊摘要的測試傳送":::

3. 從 **[平台]**  清單中選擇 **[自訂範本]**  。

4. 針對 **[傳送至標記運算式]** 輸入 **12345** 。 您先前已在安裝中指定此標記。

5. (選擇性) 在 JSON 承載中編輯 **[訊息]**  ：

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Azure 通知中樞的測試傳送":::

6. 選取 **** [傳送]。 入口網站應該會指出是否已成功將通知傳送至裝置：

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="測試傳送結果":::

   假設應用程式未在前景中執行，您也應該會在裝置上的 **[通知中心]**  中看到通知。 點選通知應該會開啟應用程式，並顯示警示。

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="測試通知":::

### <a name="send-a-test-notification-mail-carrier"></a>傳送測試通知 (郵件載體)

您可以透過 [REST API](/rest/api/notificationhubs/) 使用 **Postman**傳送通知，這可能是較方便的測試方式。

1. 在 **Postman** 中開啟新的索引標籤。

2. 將要求設定為 **POST**，然後輸入下列位址：

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. 設定要求標頭，如下所示：

   | Key                           | 值                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | application/json;charset=utf-8 |
   | 授權                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | template                       |
   | Tags                          | "12345"                        |

4. 將要求 **BODY** 設定為使用 **RAW - JSON (application.json)**  ，並搭配下列 JSON 承載：

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. 選取 **[程式碼]**  按鈕，其位於視窗右上方的 **[儲存]**  按鈕底下。 要求看起來應該會類似以下的範例：

   ```xml
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

6. 選取 **[傳送]**  按鈕。

您應該會收到 **[201 已建立]**  的成功狀態碼，並在用戶端裝置上收到通知。

## <a name="next-steps"></a>後續步驟

您現在已透過 [通知中樞 REST API](/rest/api/notificationhubs/)，將基本 iOS Swift 應用程式連線至通知中樞，而且可以傳送和接收通知。 若要深入了解如何將通知傳送至特定裝置，請繼續進行下列教學課程：

- [教學課程：將推播通知傳送至特定裝置](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

如需詳細資訊，請參閱下列文章：

- [Azure 通知中樞概觀](notification-hubs-push-notification-overview.md)
- [通知中樞 REST API](/rest/api/notificationhubs/)
- [適用於後端作業的通知中樞 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 上的通知中樞 SDK](https://github.com/Azure/azure-notificationhubs)
- [向應用程式後端註冊](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [註冊管理](notification-hubs-push-notification-registration-management.md)
- [使用標記](notification-hubs-tags-segment-push-message.md)
- [使用自訂範本](notification-hubs-templates-cross-platform-push-messages.md)
- [使用共用存取簽章的服務匯流排存取控制](../service-bus-messaging/service-bus-sas.md)
- [以程式設計方式產生 SAS 權杖](/rest/api/eventhub/generate-sas-token)
- [Apple 安全性：一般加密](https://developer.apple.com/security/)
- [Unix Epoch 時間](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
