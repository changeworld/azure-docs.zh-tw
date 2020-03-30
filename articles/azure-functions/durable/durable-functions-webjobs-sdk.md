---
title: 如何以 Web 作業方式運行持久函數 - Azure
description: 了解如何使用 WebJobs SDK，對 Durable Functions 進行編碼並設定為在 WebJobs 中執行。
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232731"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>如何以 Web 作業方式運行持久函數

預設情況下，持久函數使用 Azure 函數運行時來承載業務流程。 但是，在某些情況下，可能需要對偵聽事件的代碼進行更多控制。 本文介紹如何使用 WebJobs SDK 實現業務流程。 要查看函數和 Web 作業之間的更詳細的比較，請參閱[比較函數和 Web 作業](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

[Azure Functions](../functions-overview.md) 和 [Durable Functions](durable-functions-overview.md) 擴充功能是以 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) 為基礎所建置的。 Web作業 SDK 中的作業主機是 Azure 函數中的運行時。 如果需要以 Azure 函數中不可能的方式控制行為，可以使用 WebJobs SDK 自己開發和運行持久函數。

在 WebJobs SDK 的 3.x 版本中，主機是`IHost`的實現，在版本 2.x`JobHost`中，您使用該物件。

鏈式持久函數示例在 WebJobs SDK 2.x 版本中可用：下載或克隆[持久函數存儲庫](https://github.com/azure/azure-functions-durable-extension/)，然後轉到*示例\\\\Webjobssdk 連結*資料夾。

## <a name="prerequisites"></a>Prerequisites

本文假設您已熟悉 WebJobs SDK、Azure Functions 的C# 類別庫開發和 Durable Functions 等項目的基本概念。 如果您需要這些主題的簡介，請參閱下列資源：

* [開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [使用 Visual Studio 建立第一個函式](../functions-create-your-first-function-visual-studio.md)
* [長期函式](durable-functions-sequence.md)

若要完成本文中的步驟：

* 使用**Azure 開發**工作負荷安裝 Visual [Studio 2019。](https://docs.microsoft.com/visualstudio/install/)

  如果您已有 Visual Studio，但沒有該工作負荷，則通過選擇 **"工具** > **獲取工具和功能**"來添加工作負荷。

  (您可以改為使用 [Visual Studio Code](https://code.visualstudio.com/)，但某些指示是 Visual Studio 特有的。)

* 安裝並執行 [Azure 儲存體模擬器](../../storage/common/storage-use-emulator.md) 5.2 版或更新版本。 替代方法是使用 Azure 儲存體連接字串來更新 App.config** 檔案。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

本文說明如何開發 WebJobs SDK 2.x 專案 (相當於 Azure Functions 1.x 版)。 如需 3.x 版的資訊，請參閱本文稍後的 [WebJobs SDK 3.x](#webjobs-sdk-3x)。

## <a name="create-a-console-app"></a>建立主控台應用程式

要將持久函數作為 Web 作業運行，必須首先創建主控台應用。 WebJobs SDK 專案只是安裝了適當 NuGet 套件的主控台應用程式專案。

在視覺化工作室**新專案**對話方塊中，選擇**Windows 經典桌面** > **主控台應用 （.NET 框架）。** 在專案檔中，`TargetFrameworkVersion` 應該是 `v4.6.1`。

Visual Studio 還具有 WebJob 專案範本，您可以通過選擇**雲** > **Azure WebJob （.NET 框架）** 來使用它。 此範本會安裝許多套件，但您可能不需要其中的某些套件。

## <a name="install-nuget-packages"></a>安裝 NuGet 套件

您需要 NuGet 套件中的 WebJobs SDK、核心繫結、記錄架構和長期工作擴充功能。 以下是這些**包的包管理器主控台**命令，截至本文撰寫之日，最新的穩定版本號如下：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

您也需要記錄提供者。 以下命令安裝 Azure 應用程式見解提供程式和`ConfigurationManager`。 `ConfigurationManager` 可讓您從應用程式設定中取得 Application Insights 檢測金鑰。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

下列命令會安裝主控台提供者：

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 程式碼

創建主控台應用並安裝所需的 NuGet 包後，即可使用持久功能。 通過使用 JobHost 代碼執行此操作。

若要使用 Durable Functions 擴充功能，請在 `Main` 方法中的 `JobHostConfiguration` 物件上呼叫 `UseDurableTask`：

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

如需可在 `DurableTaskExtension` 物件中設定的屬性清單，請參閱 [host.json](../functions-host-json.md#durabletask)。

`Main` 方法也是用來設定記錄提供者的位置。 下面的示例配置主控台和應用程式見解提供程式。

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>函式

Web作業上下文中的持久函數與 Azure 函數上下文中的持久函數略有不同。 在編寫代碼時，瞭解差異非常重要。

WebJobs SDK 不支援下列 Azure Functions 功能：

* [FunctionName 屬性](#functionname-attribute)
* [HTTP 觸發程序](#http-trigger)
* [Durable Functions HTTP 管理 API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName 屬性

在 WebJobs SDK 專案中，函式的方法名稱即為函式名稱。 `FunctionName` 屬性只會在 Azure Functions 中使用。

### <a name="http-trigger"></a>HTTP 觸發程序

WebJobs SDK 沒有 HTTP 觸發程序。 範例專案的協調流程用戶端會使用計時器觸發程序：

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP 管理 API

由於沒有 HTTP 觸發程序，WebJobs SDK 沒有 [HTTP 管理 API](durable-functions-http-api.md)。

在 WebJobs SDK 專案中，可以在業務流程用戶端物件上調用方法，而不是通過發送 HTTP 要求。 下列方法對應至三個可使用 HTTP 管理 API 來進行的工作：

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

示例專案中的業務流程用戶端函數啟動協調器函數，然後進入每 2 秒調用`GetStatusAsync`一次的迴圈：

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>執行範例

您已設置持久函數以作為 WebJob 運行，現在您已經瞭解了與作為獨立 Azure 函數運行持久函數有何不同。 此時，在示例中看到它工作可能會有所説明。

本節概述如何執行[範例專案](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)。 如需會說明如何在本機執行 WebJobs SDK 專案並將其部署至 Azure WebJob 的詳細指示，請參閱[開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)。

### <a name="run-locally"></a>在本機執行

1. 確定儲存體模擬器正在執行 (請參閱[先決條件](#prerequisites))。

1. 如果要在本地運行專案時在應用程式見解中看到日誌：

    a. 創建應用程式見解資源，並為其使用 **"常規**應用類型"。

    b. 將檢測金鑰儲存在 App.config** 檔案。

1. 執行專案。

### <a name="run-in-azure"></a>在 Azure 中執行

1. 建立 Web 應用程式和儲存體帳戶。

1. 在 Web 應用中，將存儲連接字串保存在名為 的應用`AzureWebJobsStorage`設置中。

1. 創建應用程式見解資源，並為其使用 **"常規**應用類型"。

1. 將檢測金鑰保存在名為 的應用`APPINSIGHTS_INSTRUMENTATIONKEY`設置中。

1. 部署為 WebJob。

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

本文介紹如何開發 WebJobs SDK 2.x 專案。 如果您正在開發[WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md)專案，本節可説明您瞭解差異。

引入的主要更改是使用 .NET Core 而不是 .NET 框架。 要創建 WebJobs SDK 3.x 專案，說明相同，但以下例外情況除外：

1. 建立 .NET Core 主控台應用程式。 在視覺化工作室**新專案**對話方塊中，選擇 **.NET 核心** > **主控台應用 （.NET Core）。** 專案檔會將 `TargetFramework` 指定為 `netcoreapp2.x`。

1. 選擇以下套裝軟體的發佈版本 WebJobs SDK 3.x：

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. 通過使用 .NET Core 組態架構，在*appset.json*檔中設置存儲連接字串和應用程式見解檢測金鑰。 以下是範例：

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. 更改`Main`方法代碼以執行此操作。 以下是範例：

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>後續步驟

若要深入了解 WebJobs SDK，請參閱[如何使用 WebJobs SDK](../../app-service/webjobs-sdk-how-to.md)。
