---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095181"
---
### <a name="create-a-web-project"></a>建立 Web 專案

1. 在 **Visual Studio** 中，選取 [檔案] > [新增解決方案]。

1. 選取 [.NET Core] > [應用程式] > [ASP.NET Core] > [API] > [下一步]。
  
1. 在 [設定新的 ASP.NET Core Web API] 對話方塊中，選取 [.NET Core 3.1] 的**目標 Framework**。

1. 在 [專案名稱] 中，輸入 *PushDemoApi*，然後選取 [建立]。

1. 開始偵錯 (**Command** + **Enter**)，以測試樣板化應用程式。

    > [!NOTE]
    > 樣板化應用程式會設定為使用 **WeatherForecastController** 作為 *launchUrl*。 這會在 [屬性] > [launchSettings.json] 中設定。  
    >
    > 如果系統出現「**發現無效開發憑證**」的訊息提示：
    >
    > 1. 按一下 [是]，以同意執行 'dotnet dev-certs https' 工具來修正此問題。 然後，'dotnet dev-certs https' 工具會提示您輸入憑證的密碼和金鑰鏈的密碼。
    >
    > 1. 當系統提示您**安裝並信任新憑證**時，請按一下 [是]，然後輸入您金鑰鏈的密碼。

1. 展開 [Controllers] 資料夾，然後刪除 **WeatherForecastController.cs**。

1. 刪除 **WeatherForecast.cs**。

1. **Control** + **按一下** **PushDemoApi** 專案，然後從 [新增] 功能表中選擇 [新增檔案...]。

1. 使用[祕密管理員工具](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager)設定本機組態值。 將秘密與解決方案分離，以確保祕密不會出現在原始檔控制中。 開啟**終端機**，然後移至專案檔的目錄，並執行下列命令：

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    使用您自己的通知中樞名稱和連接字串值來取代預留位置值。 您已在[建立通知中樞](#create-a-notification-hub)一節中記下這些資訊。 或者，您可以在 [Azure](https://portal.azure.com) 中查閱這些資訊。

    **NotificationsHub:Name**：  
    請參閱 [概觀] 頂端 [基本資訊] 摘要中的 [名稱]。  

    **NotificationHub:ConnectionString**：  
    請參閱 [存取原則] 中的 *DefaultFullSharedAccessSignature*

    > [!NOTE]
    > 針對生產案例，您可以查看 [Azure KeyVault](https://azure.microsoft.com/services/key-vault) 之類的選項，以安全地儲存連接字串。 為了簡單起見，密碼會新增至 [Azure App Service](https://azure.microsoft.com/services/app-service/) 應用程式設定中。

### <a name="authenticate-clients-using-an-api-key-optional"></a>使用 API 金鑰驗證用戶端 (選擇性)

API 金鑰的安全性並不如權杖，但對於本教學課程的目的來說已足夠。 您可以透過 [ASP.NET 中介軟體](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1)輕鬆地設定 API 金鑰。

1. 將 **API 金鑰** 新增至本機設定值。

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > 您應該以自己的值來取代預留位置，並記下該值。

1. **Control** + **按一下** **PushDemoApi** 專案，從 [新增] 功能表選擇 [新增資料夾]，然後按一下 [新增]，並以 *Authentication* 作為**資料夾名稱**。

1. **Control** + **按一下** **Authentication** 資料夾，然後從 [新增] 功能表中選擇 [新增檔案...]。

1. 選取 [一般] > [空白類別]，並輸入 *ApiKeyAuthOptions.cs* 作為**名稱**，然後按一下 [新增] 來加入下列實作。

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. 將另一個名為 *ApiKeyAuthHandler.cs* 的**空白類別**新增至 **Authentication** 資料夾，然後新增下列實作。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > [驗證處理常式](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler)是實作為配置行為的類型 (在此案例中是自訂 API 金鑰配置)。

1. 將另一個名為 *ApiKeyAuthenticationBuilderExtensions.cs* 的**空白類別**新增至 **Authentication** 資料夾，然後新增下列實作。

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > 此擴充方法會簡化 **Startup.cs** 中的中介軟體設定程式碼，使其更容易閱讀，而且通常更易於遵循。

1. 在 **Startup.cs**中，更新 **ConfigureServices** 方法，以在 **services.AddControllers**方法的呼叫下設定 API 金鑰驗證。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. 繼續在 **Startup.cs** 中，更新**設定**方法以呼叫應用程式中 **IApplicationBuilder** 上的 **UseAuthentication** 和 **UseAuthorization** 擴充方法。 請務必在 **UseRouting** 之後和 **app.UseEndpoints** 之前，呼叫這些方法。

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > 呼叫 **UseAuthentication** 來註冊的中介軟體會使用先前註冊的驗證配置 (來自 **ConfigureServices**)。 必須先執行此呼叫，才能驗證相依於使用者的中介軟體。

### <a name="add-dependencies-and-configure-services"></a>新增相依性並設定服務

ASP.NET Core 支援[相依性插入 (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。  

服務內的封裝內容可讓您使用通知中樞和[適用於後端作業的通知中樞 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。 該服務已註冊，並可透過適當的擷取方式來取得。

1. **Control** + **按一下** **Dependencies** 資料夾，然後選擇 [管理 NuGet 套件...]。

1. 搜尋 **Microsoft.Azure.NotificationHubs** 並確認此項目已核取。

1. 按一下 [新增套件]，然後在出現接受授權條款的提示時，按一下 [接受]。

1. **Control** + **按一下** **PushDemoApi** 專案，從 [新增] 功能表選擇 [新增資料夾]，然後按一下 [新增]，並以 *Models* 作為**資料夾名稱**。

1. **Control** + **按一下** **Models** 資料夾，然後從 [新增] 功能表中選擇 [新增檔案...]。

1. 選取 [一般] > [空白類別]，並輸入 *PushTemplates.cs* 作為**名稱**，然後按一下 [新增] 來加入下列實作。

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > 此類別包含 Token 化的通知承載，將用於此案例所需的一般和無訊息通知。 該承載會在[安裝](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet)外部加以定義，以允許進行實驗，而不需要透過服務來更新現有安裝。 以此方式處理安裝的變更已超出本教學課程的範圍。 針對生產環境，請考慮使用[自訂範本](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。

1. 選取 [一般] > [空白類別]，並輸入 *DeviceInstallation.cs* 作為**名稱**，然後按一下 [新增] 來加入下列實作。

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. 將另一個名為 *NotificationRequest.cs* 的**空白類別**新增至 **Models** 資料夾，然後新增下列實作。

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. 將另一個名為 *NotificationHubOptions.cs* 的**空白類別**新增至 **Models** 資料夾，然後新增下列實作。

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. 將名為 *Services* 的新資料夾新增至 **PushDemoApi** 專案。

1. 將名為 *INotificationService.cs* 的**空白介面**新增至 **Services** 資料夾，然後新增下列實作。

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. 將名為 *NotificationHubsService.cs* 的**空白類別**新增至 **Services** 資料夾，然後新增下列程式碼來實作 **INotificationService** 介面：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > 提供給 **SendTemplateNotificationAsync** 的標記運算式限制為 20 個標記。 大部分運算子的限制為 6 個，但在此案例中，運算式僅包含 OR (||)。 如果要求中的標記超過 20 個，則必須將標記分割到多個要求中。 如需詳細資訊，請參閱[路由和標記運算式](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396)文件。

1. 在 **Startup.cs**中，更新 **ConfigureServices** 方法，以將 **NotificationHubsService** 新增為 **INotificationService**的單一實作。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>建立通知 API

1. **Control** + **按一下** **Controllers** 資料夾，然後從 [新增] 功能表中選擇 [新增檔案...]。

1. 選取 [ASP.NET Core] > [Web API 控制器類別]，輸入 *NotificationsController* 作為**名稱**，然後按一下 [新增]。

1. 將下列命名空間新增至檔案的頂端。

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. 更新樣板化控制器，使其衍生自 **ControllerBase**，並使用 **ApiController** 屬性裝飾。

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > **控制器**基底類別提供檢視支援，但在此案例中不需要這麼做，因此可以改用 **ControllerBase**。

1. 如果您選擇完成[使用 API 金鑰驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節，您應該也使用 **Authorize** 屬性來裝飾 **NotificationsController**。

    ```cs
    [Authorize]
    ```

1. 更新建構函式，以接受 **INotificationService** 的已註冊執行個體作為引數，並將其指派給唯讀成員。

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. 在 **launchSettings.json** 中 (**Properties** 資料夾內)，將 **launchUrl** 從 `weatherforecast` 變更為 *api/notifications*，以符合 **RegistrationsController** **Route** 屬性中指定的 URL。

1. 開始偵錯 (**Command** + **Enter**)，以驗證應用程式是否正在使用新的 **NotificationsController**，並且傳回「**401 未授權**」狀態。

    > [!NOTE]
    > Visual Studio 可能不會在瀏覽器中自動啟動應用程式。 您將使用 [Postman](https://www.postman.com/downloads) 從這裡開始測試 API。

1. 在新的 [ **[Postman](https://www.postman.com/downloads)** ] 索引標籤上，將要求設定為 **GET**，然後輸入下列位址。

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > localhost 位址應符合 [Properties] > [launchSettings.json] 中找到的 **applicationUrl** 值。 預設值應該是 `https://localhost:5001;http://localhost:5000`，不過，如果您收到 404 回應，這就是要確認的部分。

1. 如果您選擇完成[使用 API 金鑰驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節，請務必將要求標頭設定為包含 **apikey** 值。

   | Key                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. 按一下 [傳送] 按鈕。

    > [!NOTE]
    > 您應該會收到「**200 正常**」狀態，其中包含一些 **JSON** 內容。
    >
    > 如果您收到 **SSL 憑證驗證**警告，您可以在 [設定] 中，將要求 SSL 憑證驗證 **[Postman](https://www.postman.com/downloads)** 設定切換為 [關閉]。

1. 使用下列程式碼取代樣板化類別方法。

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>建立 API 應用程式

您現在會在用來裝載後端服務的 [Azure App Service](https://docs.microsoft.com/azure/app-service/) 中建立 [API 應用程式](https://azure.microsoft.com/services/app-service/api/)。  

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [建立資源]，然後搜尋並選擇 [API 應用程式]，然後按一下 [建立]。

1. 更新下列欄位，然後按一下 [建立]。

    **應用程式名稱：**  
    輸入 **API 應用程式**的全域唯一名稱

    **訂用帳戶︰**  
    選擇您在其中建立通知中樞的相同目標**訂用帳戶**。

    **資源群組：**  
    選擇您在其中建立通知中樞的相同**資源群組**。

    **App Service 方案/位置：**  
    建立新的 **App Service 方案**  

    > [!NOTE]
    > 從預設選項變更為包含 **SSL** 支援的方案。 否則，您必須在使用行動應用程式時採取適當的步驟，以防止 **HTTP** 要求遭到封鎖。

    **Application Insights：**  
    保留建議選項 (將使用該名稱建立新的資源)，或挑選現有的資源。

1. 佈建 **API 應用程式**之後，請瀏覽至該資源。

1. 記下 [概觀] 頂端 [基本資訊] 摘要中的 [URL] 屬性。 此 URL 是您稍後將在本教學課程中使用的「後端端點」。

    > [!NOTE]
    > URL 會使用您稍早指定的 API 應用程式名稱，其格式為 `https://<app_name>.azurewebsites.net`。

1. 從 [設定] 底下的清單中選取 [組態]。  

1. 針對下列每個設定，按一下 [新增應用程式設定] 來輸入**名稱**和**值**，然後按一下 [確定]。

   | 名稱                               | 值                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > 這些設定與您先前在 [使用者設定] 中所定義的設定相同。 您應該能夠複製這些設定。 只有在您選擇完成[使用 API 金鑰來驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節時，才需要 **Authentication:ApiKey** 設定。 針對生產案例，您可以查看 [Azure KeyVault](https://azure.microsoft.com/services/key-vault) 之類的選項。 為了簡單起見，本案例已將這些這些設定新增為應用程式設定。

1. 新增所有應用程式設定之後，請按一下 [儲存]，然後**繼續**。

### <a name="publish-the-backend-service"></a>發行後端服務

接下來，您可將應用程式部署到 API 應用程式，使其得以從所有裝置存取。  

1. 將您的設定從 [偵錯] 變更為 [發行] (如果您尚未這麼做)。

1. **Control** + **按一下** **PushDemoApi** 專案，然後從 [發佈] 功能表中選擇 [發佈至 Azure...]。

1. 遵循驗證流程 (如果系統提示您執行此動作)。 使用您先前在[建立 API 應用程式](#create-the-api-app)一節中所使用的帳戶。

1. 選取您先前從清單中建立的 **Azure App Service API 應用程式**作為發佈目標，然後按一下 [發佈]。

精靈會在完成之後，將應用程式發佈至 Azure，然後開啟應用程式。 記下 **URL** (如果您尚未這麼做)。 此 URL 是您稍後將在本教學課程中使用的「後端端點」。

### <a name="validating-the-published-api"></a>驗證已發佈的 API

1. 在 **[Postman](https://www.postman.com/downloads)** 中開啟新的索引標籤，將要求設定為 **POST**，然後輸入以下的位址。 將預留位置取代為您先前在[發佈後端服務](#publish-the-backend-service)一節中所記下的基底位址。

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > 基底位址的格式應為 ``https://<app_name>.azurewebsites.net/``

1. 如果您選擇完成[使用 API 金鑰驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節，請務必將要求標頭設定為包含 **apikey** 值。

   | Key                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. 選擇 [主體] 的**原始**選項，然後從格式選項清單中選擇 [JSON]，然後包含一些 **JSON** 內容的預留位置：

    ```json
    {}
    ```

1. 按一下 [ **傳送**]。

    > [!NOTE]
    > 您應該會收到來自服務的「**400 不正確的要求**」狀態。

1. 再次執行步驟 1-4，但這次請指定要求端點來驗證您是否會收到相同的「**400 不正確的要求**」回應。

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> 您尚無法使用有效的要求資料來測試 API，因為這將需要用戶端行動應用程式的平台專屬資訊。
