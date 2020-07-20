---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156273"
---
### <a name="configure-the-runner-target-and-infoplist"></a>設定執行器目標和 Info.plist

1. 在 **Visual Studio Code** 中，按 **Control** + **按一下** **ios** 資料夾，然後選擇 [在 Xcode 中開啟]。

1. 在 **Xcode** 中，按一下**執行器** (最上方的 **xcodeproj**，而不是資料夾)，然後選取**執行器**目標，接著選取 [簽署與功能]。 選取 [所有] 組建組態後，選擇您**小組**的開發人員帳戶。 確定已核取 [自動管理簽署] 選項，且系統已自動選取您的簽署憑證和配置文件。

    > [!NOTE]
    > 如果您沒有看到新的[配置文件]值，請嘗試重新整理簽署身分識別的設定檔，方法是選取 [Xcode] > [喜好設定] > [帳戶]，然後選取 [下載手動設定檔] 按鈕來下載設定檔。

1. 按一下 [+ 功能]，然後搜尋 [推播通知]。 **按兩下** [推播通知]，以新增這項功能。

1. 開啟 **Info plist**，並將 [最低系統版本] 設定為 **13.0**。

    > [!NOTE]
    > 本教學課程僅支援執行 **iOS 13.0 和更新版本**的裝置，但您可以將其延伸而能夠支援執行較舊版本的裝置。

1. 開啟 **Runner.entitlements**，並確保 [APS 環境]  的設定已設為 [開發]。

### <a name="handle-push-notifications-for-ios"></a>處理 iOS 的推播通知

1. **Control** + **按一下** **Runner** 資料夾 (在 [執行器] 專案中)，然後選擇 [新增群組]，並以 **Services** 作為名稱。

1. **Control** + **按一下** **Services** 資料夾，然後選擇 [新增檔案...]。然後，選擇 [Swift 檔案] 並按一下 [下一步]。 指定 **DeviceInstallationService** 作為名稱，然後按一下 [建立]。

1. 使用下列程式碼實作 **DeviceInstallationService.swift**。

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > 此類別會針對 `com.<your_organization>.pushdemo/deviceinstallation` 通道執行平台專屬的對應項目。 這已在應用程式 Flutter 部分的 **DeviceInstallationService.dart** 中定義。 在此情況下，呼叫會從通用程式碼對原生主機發出。 無論使用哪種方式，請務必將 **<your_organization>** 取代為您自己的組織。
    >
    > 這個類別會提供唯一識別碼 (使用 [UIDevice.IdentifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) 值) 作為通知中樞註冊承載的一部分。

1. 將另一個名為 *NotificationRegistrationService* 的 **Swift 檔案**新增至 **Services** 資料夾，然後新增下列程式碼。

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > 此類別會針對 `com.<your_organization>.pushdemo/notificationregistration` 通道執行平台專屬的對應項目。 這已在應用程式 Flutter 部分的 **NotificationRegistrationService.dart** 中定義。 在此情況下，呼叫會從原生主機對通用程式碼發出。 同樣地，無論使用哪種方式，請務必將 **<your_organization>** 取代為您自己的組織。

1. 將另一個名為 *NotificationActionService* 的 **Swift 檔案**新增至 **Services** 資料夾，然後新增下列程式碼。

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > 此類別會針對 `com.<your_organization>.pushdemo/notificationaction` 通道執行平台專屬的對應項目。 其已在應用程式 Flutter 部分的 **NotificationActionService.dart** 中定義。 在此情況下，呼叫可以在這兩個方向中進行。 無論使用哪種方式，請務必將 **<your_organization>** 取代為您自己的組織。

1. 在 **AppDelegate.swift** 中，新增變數以儲存您先前所建服務的參考。

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. 新增名為 **processNotificationActions** 的函式，以處理通知資料。 有條件地觸發該動作，或在應用程式啟動期間處理動作時將其儲存，以供稍後使用。

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. 覆寫 **didRegisterForRemoteNotificationsWithDeviceToken** 函式，設定 **DeviceInstallationService** 的**權杖**值。 然後，在 **NotificationRegistrationService** 上呼叫 **refreshRegistration**。

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. 覆寫 **didReceiveRemoteNotification** 函式，將 **userInfo** 引數傳遞至 **processNotificationActions** 函式。

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. 覆寫 **didFailToRegisterForRemoteNotificationsWithError** 函式來記錄錯誤。

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > 這就是預留位置。 您可以針對生產案例實作適當的記錄和錯誤處理機制。

1. 在 **didFinishLaunchingWithOptions** 中，將 **deviceInstallationService**、**notificationRegistrationService** 和 **notificationActionService** 變數具現化。

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. 在相同函式中，依情況要求授權，並註冊遠端通知。

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. 如果 **launchOptions** 包含 **remoteNotification** 索引鍵，請在 **didFinishLaunchingWithOptions** 函式的結尾呼叫 **processNotificationActions**。 傳入產生的 **userInfo** 物件，並針對 *launchAction* 引數使用 *true*。 *true* 值表示動作會在應用程式啟動期間進行處理。

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
