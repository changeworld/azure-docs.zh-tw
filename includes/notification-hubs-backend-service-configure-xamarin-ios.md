---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c13b7ee8c5c0a0d302e4822047ea60f9df120bf8
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112084"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>設定 Info.plist 和 Entitlements.plist

1. 確定您已登入 **Apple 開發人員帳戶** (位於 [Visual Studio] > [喜好設定...] > [發佈] > [Apple 開發人員帳戶])，並已下載適當的*憑證*和*配置文件*。 您應該已在先前的步驟中建立這些資產。

1. 在 **PushDemo.iOS** 中開啟 **Info.plist**，並確定 **BundleIdentifier** 符合 [Apple 開發人員入口網站](https://developer.apple.com)中個別配置文件所使用的值。 **BundleIdentifier** 的格式為 ``com.<organization>.PushDemo``。

1. 在相同檔案中，將 [最低系統版本] 設定為 [13.0]。

    > [!NOTE]
    > 本教學課程僅支援執行 **iOS 13.0 和更新版本**的裝置，但您可以將其延伸而能夠支援執行較舊版本的裝置。

1. 開啟 **PushDemo.iOS** 的 [專案選項] (按兩下專案)。

1. 在 [專案選項] 的 [組建] > [iOS 套件組合簽署] 底下，確定 [小組] 底下已選取您的開發人員帳戶。 然後，確定已選取 [自動管理簽署]，且系統已自動選取您的簽署憑證和配置文件。

    > [!NOTE]
    > 如果系統尚未自動選取 [簽署憑證] 和 [配置文件]，請選擇 [手動佈建]，然後按一下 [套件組合簽署選項]。 確定已針對 [簽署身分識別] 選取您的 [小組]，並已針對 [偵錯] 和 [發行] 設定的 [配置文件] 選取您的 *PushDemo* 特有配置文件，以確保在這兩種情況下都會針對 [平台] 選取 [iPhone]。

1. 在 **PushDemo.iOS** 中開啟 **Entitlements.plist**，並確定在 [權利] 索引標籤中檢視時有核取 [啟用推播通知]。然後，確定在 [來源] 索引標籤中檢視時，[APS 環境] 設定已設定為 [開發]。

### <a name="handle-push-notifications-for-ios"></a>處理 iOS 的推播通知

1. **Control** + **按一下** **PushDemo.iOS** 專案，從 [新增] 功能表選擇 [新增資料夾]，然後按一下 [新增]，並以 *Services* 作為**資料夾名稱**。

1. **Control** + **按一下** **Services** 資料夾，然後從 [新增] 功能表中選擇 [新增檔案...]。

1. 選取 [一般] > [空白類別]，並輸入 *DeviceInstallationService.cs* 作為 [名稱]，然後按一下 [新增] 來加入下列實作。

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > 這個類別會提供唯一識別碼 (使用 [UIDevice.IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) 值) 和通知中樞註冊承載。

1. 使用下列實作，將新資料夾新增至稱為 *Extensions* 的 **PushDemo.iOS** 專案，然後將 [空白類別] 新增至稱為 *NSDataExtensions.cs* 的該資料夾。

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. 在 **AppDelegate.cs** 中，確定已將下列命名空間新增至檔案頂端。

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. 新增裝置權杖快取金鑰的常數。

    ```csharp
    const string CachedDeviceToken = "cached_device_token";
    ```

1. 新增私有屬性及其各自的支援欄位，以將參考儲存至 **IPushDemoNotificationActionService**、**INotificationRegistrationService** 和 **IDeviceInstallationService** 實作。

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    INotificationRegistrationService _notificationRegistrationService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    INotificationRegistrationService NotificationRegistrationService
        => _notificationRegistrationService ??
            (_notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. 新增 **RegisterForRemoteNotifications** 方法以註冊使用者通知設定，然後針對遠端通知，請使用 **APNS**。

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. 新增 **CompleteRegistrationAsync** 方法以設定 `IDeviceInstallationService.Token` 屬性值。 重新整理註冊並快取裝置權杖 (如果自上次儲存後已有所更新)。

    ```csharp
    async Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();

        var cachedToken = await SecureStorage.GetAsync(CachedDeviceToken)
            .ConfigureAwait(false);

        if (!string.IsNullOrWhiteSpace(cachedToken) &&
            cachedToken.Equals(DeviceInstallationService.Token))
            return;

        await NotificationRegistrationService.RefreshRegistrationAsync()
            .ConfigureAwait(false);

        await SecureStorage.SetAsync(CachedDeviceToken, DeviceInstallationService.Token)
            .ConfigureAwait(false);
    }
    ```

1. 新增 **ProcessNotificationActions** 方法來處理 **NSDictionary** 通知資料，並依情況呼叫 **NotificationActionService.TriggerAction**。

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. 覆寫 **RegisteredForRemoteNotifications** 方法，將 **deviceToken** 引數傳遞至 **CompleteRegistrationAsync** 方法。

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. 覆寫 **ReceivedRemoteNotification** 方法，將 **userInfo** 引數傳遞至 **ProcessNotificationActions** 方法。

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. 覆寫 **FailedToRegisterForRemoteNotifications** 方法以記錄錯誤。

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > 這就是預留位置。 您可以針對生產案例實作適當的記錄和錯誤處理機制。

1. 更新 **FinishedLaunching** 方法，以便在呼叫 `Forms.Init` 之後立即呼叫 `Bootstrap.Begin`，而傳入 **IDeviceInstallationService** 的平台特有實作。

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 在相同方法中，依情況要求授權，並在 `Bootstrap.Begin` 之後立即註冊遠端通知。

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. 仍然在 **FinishedLaunching** 中，如果 **options** 引數包含 **UIApplication.LaunchOptionsRemoteNotificationKey**，則在呼叫 `LoadApplication` 之後立即呼叫 **ProcessNotificationActions**，以傳入產生的 **userInfo** 物件。

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
