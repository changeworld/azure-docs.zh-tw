---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448709"
---
### <a name="create-the-xamarinforms-solution"></a>建立 Xamarin.Forms 解決方案

1. 在 **Visual Studio** 中，使用**空白表單應用程式**作為範本，並輸入 *PushDemo* 作為**專案名稱**來建立新的 **Xamarin.Forms** 解決方案。

    > [!NOTE]
    > 在 [設定您的空白表單應用程式] 對話方塊中，確定 [組織識別碼] 符合您先前使用的值，而且已核取 [Android] 和 [iOS] 目標。

1. **Control** + **按一下** *PushDemo* 解決方案，然後選擇 [更新 NuGet 套件]。
1. **Control** + **按一下** *PushDemo* 解決方案，然後選擇 [管理 NuGet 套件...]。
1. 搜尋 **Newtonsoft.Json**，並確定該項目已核取。
1. 按一下 [新增套件]，然後在出現接受授權條款的提示時，按一下 [接受]。
1. 在每個目標平台上建立並執行應用程式 (**Command** + **Enter**)，以測試您裝置上執行的樣板化應用程式。

### <a name="implement-the-cross-platform-components"></a>實作跨平台元件

1. **Control** + **按一下** **PushDemo** 專案，從 [新增] 功能表選擇 [新增資料夾]，然後按一下 [新增]，並以 *Models* 作為**資料夾名稱**。

1. **Control** + **按一下** **Models** 資料夾，然後從 [新增] 功能表中選擇 [新增檔案...]。

1. 選取 [一般] > [空白類別]，並輸入 *DeviceInstallation.cs*，然後加入下列實作。

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. 使用下列實作，將名為 *PushDemoAction.cs* 的**空白列舉**新增至 **Models** 資料夾。

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. 使用下列實作，將稱為 *Services* 的新資料夾新增至 **PushDemo** 專案，然後將 [空白類別] 新增至稱為 *ServiceContainer.cs* 的資料夾。

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 這是 [XamCAT](https://github.com/xamcat/mobcat-library) 存放庫中 [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) 類別的修減版版。 其會用來作為輕量的 IoC (控制反轉) 容器。

1. 將名為 *IDeviceInstallationService.cs* 的**空白介面**新增至 **Services** 資料夾，然後新增下列程式碼。

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > 每個目標稍後都會實作並啟動此介面，以提供平台特定功能和後端服務所需的 **DeviceInstallation** 資訊。

1. 將另一個名為 *INotificationRegistrationService.cs* 的**空白介面**新增至 **Services** 資料夾，然後新增下列程式碼。  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > 這會處理用戶端和後端服務之間的互動。

1. 將另一個名為 *INotificationActionService.cs* 的**空白介面**新增至 **Services** 資料夾，然後新增下列程式碼。  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > 這是用來集中處理通知動作的簡單機制。

1. 使用下列實作，將衍生自 *INotificationActionService* 且名為 *IPushDemoNotificationActionService.cs* 的**空白介面** 新增至 **Services** 資料夾。  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > 這是 **PushDemo** 應用程式特有的類型，並使用 **PushDemoAction** 列舉來識別以強型別方式觸發的動作。

1. 將名為 *NotificationRegistrationService.cs* 的**空白類別**新增至 **Services** 資料夾，並以下列程式碼來實作 *INotificationRegistrationService*。

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > 只有在您選擇完成[使用 API 金鑰來驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節時，才需要 **apiKey** 引數。

1. 將名為 *PushDemoNotificationActionService.cs* 的**空白類別**新增至 **Services** 資料夾，並以下列程式碼來實作 *IPushDemoNotificationActionService*。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. 使用下列實作，將名為 *Config.cs* 的**空白類別**新增至 **PushDemo** 專案。  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > 這是將祕密與原始檔控制分開的簡單方式。 您可以將這些值取代為自動化組建的一部分，或使用本機部分類別來將其覆寫。 您將在下一個步驟中進行此動作。
    >
    > 只有在您選擇完成[使用 API 金鑰來驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節時，才需要 **ApiKey** 欄位。

1. 使用下列實作，將另一個名為 *Config.local_secrets.cs* 的**空白類別**新增至 **PushDemo** 專案。  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > 以您自己的值取代預留位置值。 您應該在建立後端服務時，記下這些值。 **API 應用程式** URL 應為 ``https://<api_app_name>.azurewebsites.net/``。 請記得將 ``*.local_secrets.*`` 新增至 gitignore 檔案，以避免認可此檔案。
    >
    > 只有在您選擇完成[使用 API 金鑰來驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節時，才需要 **ApiKey** 欄位。

1. 使用下列實作，將名為 *Bootstrap.cs* 的**空白類別**新增至 **PushDemo** 專案。  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > 當應用程式啟動時，每個平台都會呼叫 **Begin** 方法，以傳遞平台專屬的 **IDeviceInstallationService** 實作。
    >
    > 只有在您選擇完成[使用 API 金鑰來驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節時，才需要 **NotificationRegistrationService** **apiKey** 建構函式引數。

### <a name="implement-the-cross-platform-ui"></a>實作跨平台 UI

1. 在 **PushDemo** 專案中，開啟 **MainPage.xaml**，並以下列項目取代 **StackLayout** 控制項。

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. 現在在 **MainPage.xaml.cs**中，新增 **readonly** 支援欄位，以儲存 **INotificationRegistrationService** 實作的參考。

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. 在**MainPage** 建構函式中，使用 **ServiceContainer** 解析 **INotificationRegistrationService**實作，並將其指派給 *_notificationRegistrationService_* 支援欄位。

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. 針對 **RegisterButton** 和 **DeregisterButton** 按鈕**點按**事件實作事件處理常式，以呼叫相對應的 **Register**/**Deregister** 方法。

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. 現在在 **App.xaml.cs** 中，請確定已參考下列命名空間。

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. 針對 **IPushDemoNotificationActionService** **ActionTriggered**事件實作事件處理常式。

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. 在 **App** 建構函式中，使用 **ServiceContainer** 解析 **IPushNotificationActionService**實作，並訂閱 **IPushDemoNotificationActionService** **ActionTriggered** 事件。

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > 這只是為了示範推播通知動作的接收和傳播。 一般而言，這些作業會以無訊息方式處理，例如瀏覽至特定的檢視或重新整理某些資料，而不是透過根**頁面** (此案例中的 **MainPage**) 顯示警示。
