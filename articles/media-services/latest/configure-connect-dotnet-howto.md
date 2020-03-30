---
title: 連接到 Azure 媒體服務 v3 API - .NET
description: 本文演示如何使用 .NET 連接到媒體服務 v3 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269805"
---
# <a name="connect-to-media-services-v3-api---net"></a>連接到媒體服務 v3 API - .NET

本文介紹如何使用服務主體登錄方法連接到 Azure 媒體服務 v3 .NET SDK。

## <a name="prerequisites"></a>Prerequisites

- [創建媒體服務帳戶](create-account-cli-how-to.md)。 請確保記住資源組名稱和媒體服務帳戶名稱
- 安裝要用於 .NET 開發的工具。 本文中的步驟演示如何使用[Visual Studio 2019 社區版](https://www.visualstudio.com/downloads/)。 您可以使用視覺化工作室代碼，請參閱使用[C#](https://code.visualstudio.com/docs/languages/csharp)。 或者，您可以使用其他代碼編輯器。

> [!IMPORTANT]
> 查看[命名約定](media-services-apis-overview.md#naming-conventions)。

## <a name="create-a-console-application"></a>建立主控台應用程式

1. 啟動 Visual Studio。 
1. 在 **"檔"** 功能表中，按一下 **"新專案** > **Project**"。 
1. 創建 **.NET 核心**主控台應用程式。

本主題中的示例應用以 為目標`netcoreapp2.0`。 代碼使用"非同步主"，從 C# 7.1 開始可用。 有關詳細資訊，請參閱此[博客](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/)。

## <a name="add-required-nuget-packages"></a>新增必要的 NuGet 封裝

1. 在視覺化工作室中，選擇**工具** > **NuGet 包管理器** > **Nuget 管理器主控台**。
2. 在 **"包管理器主控台"** 視窗中，`Install-Package`使用命令添加以下 NuGet 包。 例如： `Install-Package Microsoft.Azure.Management.Media` 。

|Package|描述|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure 媒體服務 SDK。 <br/>要確保您使用的是最新的 Azure 媒體服務包，請查看[Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)。|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|用於 NET 的 Azure SDK 的 ADAL 身份驗證庫|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|從環境變數和本地 JSON 檔中讀取配置值|
|`Microsoft.Extensions.Configuration.Json`|從環境變數和本地 JSON 檔中讀取配置值
|`WindowsAzure.Storage`|存儲 SDK|

## <a name="create-and-configure-the-app-settings-file"></a>創建和配置應用設置檔

### <a name="create-appsettingsjson"></a>創建應用設置.json

1. 轉到**常規** > **文字檔**。
1. 將其命名為"appsettings.json"。
1. 將 .json 檔的"複製到輸出目錄"屬性設置為"如果更新，則複製"（以便應用程式在發佈時可以訪問它）。

### <a name="set-values-in-appsettingsjson"></a>在應用設置中設置值.

運行訪問`az ams account sp create` [API](access-api-cli-how-to.md)中所述的命令。 該命令返回應複製到"appsettings.json"的 json。
 
## <a name="add-configuration-file"></a>新增組態檔

為方便起見，添加一個設定檔，負責從"appsettings.json"讀取值。

1. 向專案添加新的 .cs 類。 將它命名為 `ConfigWrapper` 
1. 在此檔中粘貼以下代碼（本示例假定命名空間為`ConsoleApp1`）。

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>連接到 .NET 用戶端

若要開始搭配使用媒體服務 API 與 .NET，您需要建立 **AzureMediaServicesClient** 物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在下面的代碼中，GetCredentialsAsync 函數基於本地設定檔中提供的憑據創建服務用戶端憑據物件。

1. 開啟 `Program.cs`。
1. 貼上下列程式碼：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>後續步驟

- [教程：上傳、編碼和資料流視頻 - .NET](stream-files-tutorial-with-api.md) 
- [教程： 流即時與媒體服務 v3 - .NET](stream-live-tutorial-with-api.md)
- [教程：使用媒體服務 v3 分析視頻 - .NET](analyze-videos-tutorial-with-api.md)
- [從本機檔案建立作業輸入 - .NET](job-input-from-local-file-how-to.md)
- [從 HTTPS URL 建立作業輸入 - .NET](job-input-from-http-how-to.md)
- [使用自訂轉換進行編碼 - .NET](customize-encoder-presets-how-to.md)
- [使用 AES-128 動態加密和金鑰傳遞服務 - .NET](protect-with-aes128.md)
- [使用 DRM 動態加密與授權傳遞服務 - .NET](protect-with-drm.md)
- [從現有原則取得簽署金鑰 - .NET](get-content-key-policy-dotnet-howto.md)
- [使用媒體服務建立篩選 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [搭配使用 Azure Functions v2 與媒體服務 v3 的進階影片隨選範例](https://aka.ms/ams3functions)

## <a name="see-also"></a>另請參閱

* [.NET 參考](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* 有關更多代碼示例，請參閱[.NET SDK 示例](https://github.com/Azure-Samples/media-services-v3-dotnet)回購。
