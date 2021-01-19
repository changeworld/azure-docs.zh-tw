---
title: 教學課程：在 Azure Functions 應用程式中使用 Azure 應用程式組態的動態組態 | Microsoft Docs
description: 在本教學課程，您將了解如何以動態方式更新 Azure Functions 應用程式的組態資料
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963550"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>教學課程：在 Azure Functions 應用程式中使用動態組態

應用程式組態 .NET 組態提供者支援由應用程式活動動態驅動的組態快取和重新整理。 本教學課程會示範如何在程式碼中實作動態設定更新。 本文以快速入門中介紹的 Azure Functions 應用程式為基礎。 繼續進行之前，請先完成[使用 Azure 應用程式組態建立 Azure Functions 應用程式](./quickstart-azure-functions-csharp.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定您的 Azure Functions 應用程式，使其在應用程式組態存放區發生變更時更新其組態。
> * 將最新的組態插入您的 Azure Functions 呼叫中。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- 包含 **Azure 開發** 工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- 完成[使用 Azure 應用程式組態建立 Azure Functions 應用程式](./quickstart-azure-functions-csharp.md)快速入門

## <a name="reload-data-from-app-configuration"></a>從應用程式設定重新載入資料

1. 開啟 Startup.cs，並更新 `ConfigureAppConfiguration` 方法。 

   `ConfigureRefresh` 方法會在應用程式內觸發重新整理時，註冊要檢查是否有變更的設定；在稍後的步驟中，您會在新增 `_configurationRefresher.TryRefreshAsync()` 時執行此動作。 `refreshAll` 參數會指示應用程式組態提供者在每次已註冊的設定中偵測到變更時，都要重新載入整個設定。

    註冊進行重新整理的所有設定都有 30 秒的預設快取到期時間。 您可以呼叫 `AzureAppConfigurationRefreshOptions.SetCacheExpiration` 方法來更新。

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > 更新應用程式組態中的多個索引鍵/值時，通常不希望應用程式在進行所有變更之前重新載入設定。 您可以註冊 **Sentinel** 金鑰，並只在所有其他設定變更完成時加以更新。 這有助於確保應用程式中的設定一致性。

2. 更新 `Configure` 方法，使 Azure 應用程式組態服務可透過相依性插入來使用。

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. 開啟 Function1.cs，並新增下列命名空間。

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   更新此函式，透過相依性插入取得 `IConfigurationRefresherProvider` 的執行個體，您可以從中取得 `IConfigurationRefresher` 的執行個體。

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. 在 Functions 呼叫開始時使用 `TryRefreshAsync` 方法，以更新重新整理組態的 `Run` 方法和信號。 如果未達到快取到期時間範圍，這就不會有任何作用。 如果您想要在不封鎖目前函式呼叫的情況下重新整理組態，請移除 `await` 運算子。 在此情況下，較新的函式呼叫將會取得更新的值。

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>在本機測試函式

1. 設定名為 **ConnectionString** 的環境變數，並將其設定為應用程式設定存放區的存取金鑰。 如果您使用 Windows 命令提示字元，請執行下列命令，然後重新啟動命令提示字元以讓變更生效：

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 Windows PowerShell，請執行下列命令：

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 macOS 或 Linux，請執行下列命令：

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. 若要測試您的函式，請按 F5。 如果出現提示，請接受 Visual Studio 所發出要下載及安裝 **Azure Functions Core (CLI)** 工具的要求。 您可能也需要啟用防火牆例外狀況，工具才能處理 HTTP 要求。

3. 從 Azure Functions 執行階段輸出複製函式的 URL。

    ![VS 中的函式偵錯快速入門](./media/quickstarts/function-visual-studio-debugging.png)

4. 將 HTTP 要求的 URL 貼到瀏覽器的網址列。 下圖顯示瀏覽器中對於函式傳回之本機 GET 要求所做出的回應。

    ![快速入門函式啟動本機](./media/quickstarts/dotnet-core-function-launch-local.png)

5. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]，然後選取您在快速入門中建立的應用程式組態存放區。

6. 選取 [組態總管]，然後更新下列索引鍵的值：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 - 已更新 |

   然後建立 Sentinel 索引鍵；如果值已存在，請修改其值，例如

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. 重新整理瀏覽器數次。 當快取設定在 30 秒後過期時，此頁面將會以更新的值顯示 Functions 呼叫的回應。

    ![本機函式重新整理快速入門](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> 本教學課程中使用的範例程式碼，可從[應用程式組態 GitHub 存放庫](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)下載取得。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用 Azure Functions 應用程式，以動態方式從應用程式組態重新整理組態設定。 若要了解如何使用 Azure 受控服務識別來簡化對應用程式組態的存取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
