---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: de961aa36d690cf03e42707758bc70e5495f692e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448738"
---
### <a name="validate-package-name-and-permissions"></a>驗證套件名稱和權限

1. 在 **[PushDemo]** 中，開啟 [專案選項]，然後從 [組建] 區段選取 [Android 應用程式]。

1. 檢查**套件名稱**是否符合您在 [Firebase 主控台](https://console.firebase.google.com) **PushDemo** 專案中使用的值。 **套件名稱**的格式為 ``com.<organization>.pushdemo``。

1. 將**最低 Android 版本**設定為 **Android 8.0 (API 層級 26)** ，並將**目標 Android 版本**設定為最新的 **API 層級**。

    > [!NOTE]
    > 本教學課程僅支援執行 **API 層級 26 和更新版本**的裝置，但您可以將其延伸而能夠支援執行較舊版本的裝置。

1. 請確定您已在 [必要權限] 底下，啟用 **INTERNET** 和 **READ_PHONE_STATE** 權限。

1. 按一下 [檔案] &gt; [新增] &gt; [專案] 

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>新增 Xamarin Google Play Services 基底和 Xamarin.Firebase.Messaging 套件

1. 在 **PushDemo.Android** 中，**Control** + **按一下** **Packages** 資料夾，然後選擇 [管理 NuGet 套件...]。

1. 搜尋 **Xamarin.GooglePlayServices.Base** (不是 **Basement**)，並確定該項目已核取。

1. 搜尋 **Xamarin.Firebase.Messaging**，並確定該項目已核取。

1. 按一下 [新增套件]，然後在出現接受**授權條款**的提示時，按一下 [接受]。

### <a name="add-the-google-services-json-file"></a>新增 Google Services JSON 檔案

1. **Control** + **按一下** `PushDemo.Android` 專案，然後從 [新增] 功能表中選擇 [現有檔案...]。

1. 選擇您稍早在 [Firebase 主控台](https://console.firebase.google.com)中設定 **PushDemo** 專案時下載的 *google-services.json* 檔案，然後按一下 [開啟]。

1. 出現提示時，請選擇 [將檔案複製到目錄]。

1. **Control** + **按一下**  `PushDemo.Android` 中的 *google-services.json* 檔案，然後確定 **GoogleServicesJson** 已設定為 [建置動作]。

### <a name="handle-push-notifications-for-android"></a>處理 Android 的推播通知

1. **Control** + **按一下** `PushDemo.Android` 專案，從 [新增] 功能表選擇 [新增資料夾]，然後按一下 [新增]，並以 *Services* 作為**資料夾名稱**。

1. **Control** + **按一下** **Services** 資料夾，然後從 [新增] 功能表中選擇 [新增檔案...]。

1. 選取 [一般] > [空白類別]，並輸入 *DeviceInstallationService.cs* 作為 [名稱]，然後按一下 [新增] 來加入下列實作。

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for FCM");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > 此類別會提供唯一識別碼 (使用 [Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) 作為通知中樞註冊承載的一部分。

1. 將另一個名為 *PushNotificationFirebaseMessagingService.cs*的**空白類別**新增至 **Services** 資料夾，然後新增下列實作。

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
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

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. 在 **MainActivity.cs** 中，確定已將下列命名空間新增至檔案頂端。

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. 在 **MainActivity.cs** 中，將 **LaunchMode** 設定為 [SingleTop]，使 **MainActivity** 不會在開啟時再次建立。

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. 加入私人屬性和對應的支援欄位，以儲存 **IPushNotificationActionService** 和 **IDeviceInstallationService** 實作的參考。

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. 實作 **IOnSuccessListener** 介面，以擷取並儲存 **Firebase** 權杖。

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. 新增名為 **ProcessNotificationActions** 的新方法，以檢查指定的**意圖**具有名為 *action* 的額外值。 使用 **IPushDemoNotificationActionService** 實作有條件地觸發該動作。

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. 覆寫 **OnNewIntent** 方法以呼叫 **ProcessNotificationActions** 方法。

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > 由於 [活動] 的 **LaunchMode** 設定為 [SingleTop]，因此**意圖**將會透過 **OnNewIntent** 方法來傳送至現有 [活動] 執行個體，而不是透過 **OnCreate** 方法，因此您必須處理 **OnCreate** 和 **OnNewIntent** 方法中的傳入意圖。

1. 更新 **OnCreate** 方法，以便在呼叫 `base.OnCreate` 之後立即呼叫 `Bootstrap.Begin`，而傳入 **IDeviceInstallationService** 的平台特有實作。

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 在相同的方法中，呼叫 `Bootstrap.Begin` 之後，有條件地在 **FirebaseApp** 執行個體上呼叫 **GetInstanceId**，並新增 **MainActivity** 作為 **IOnSuccessListener**。

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. 繼續在 **OnCreate** 中，在呼叫 `LoadApplication` 之後立即呼叫 **ProcessNotificationActions**，以傳入目前的**意圖**。

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> 您必須在每次執行應用程式時重新註冊，並從偵錯工作階段停止應用程式，才能繼續接收推播通知。
