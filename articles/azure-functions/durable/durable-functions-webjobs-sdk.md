---
title: 如何以 Webjob 的形式執行 Durable Functions-Azure
description: 了解如何使用 WebJobs SDK，對 Durable Functions 進行編碼並設定為在 WebJobs 中執行。
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540293"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>如何以 Webjob 的形式執行 Durable Functions

根據預設，Durable Functions 會使用 Azure Functions 執行時間來裝載協調流程。 不過，在某些情況下，您可能需要更充分掌控接聽事件的程式碼。 本文說明如何使用 Webjob SDK 來執行協調流程。 若要查看函式和 Webjob 之間更詳細的比較，請參閱[比較函數和 webjob](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

[Azure Functions](../functions-overview.md) 和 [Durable Functions](durable-functions-overview.md) 擴充功能是以 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) 為基礎所建置的。 Webjob SDK 中的作業主機是 Azure Functions 中的執行時間。 如果您需要在 Azure Functions 中控制不可行的行為，您可以自行使用 Webjob SDK 來開發及執行 Durable Functions。

在2.x 版的 Webjob SDK 中，主機是的執行 `IHost` ，而在2.x 版中，您使用 `JobHost` 物件。

您可以在 Webjob SDK 2.x 版中取得連結 Durable Functions 範例：下載或複製[Durable Functions 存放庫](https://github.com/azure/azure-functions-durable-extension/)，並簽出*v1*分支並移至*範例 \\ webjobssdk \\ 連結*資料夾。

## <a name="prerequisites"></a>先決條件

本文假設您已熟悉 WebJobs SDK、Azure Functions 的C# 類別庫開發和 Durable Functions 等項目的基本概念。 如果您需要這些主題的簡介，請參閱下列資源：

* [開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [使用 Visual Studio 建立第一個函式](../functions-create-your-first-function-visual-studio.md)
* [長期函式](durable-functions-sequence.md)

若要完成本文中的步驟：

* 透過 **Azure 開發**工作負載[安裝 Visual Studio 2019](/visualstudio/install/)。

  如果您已經有 Visual Studio，但沒有該工作負載，請選取 [**工具**] [  >  **取得工具和功能**] 來新增工作負載。

  (您可以改為使用 [Visual Studio Code](https://code.visualstudio.com/)，但某些指示是 Visual Studio 特有的。)

* 安裝並執行 [Azure 儲存體模擬器](../../storage/common/storage-use-emulator.md) 5.2 版或更新版本。 替代方法是使用 Azure 儲存體連接字串來更新 App.config** 檔案。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

本文說明如何開發 WebJobs SDK 2.x 專案 (相當於 Azure Functions 1.x 版)。 如需 3.x 版的資訊，請參閱本文稍後的 [WebJobs SDK 3.x](#webjobs-sdk-3x)。

## <a name="create-a-console-app"></a>建立主控台應用程式

若要以 Webjob 的形式執行 Durable Functions，您必須先建立主控台應用程式。 WebJobs SDK 專案只是安裝了適當 NuGet 套件的主控台應用程式專案。

在 [Visual Studio**新增專案**] 對話方塊中，選取 [ **Windows 傳統桌面**  >  **主控台應用程式（.NET Framework）**]。 在專案檔中，`TargetFrameworkVersion` 應該是 `v4.6.1`。

Visual Studio 也有 WebJob 專案範本，您可以藉由選取 [**雲端**]  >  **[Azure WebJob （.NET Framework）**] 來使用它。 此範本會安裝許多套件，但您可能不需要其中的某些套件。

## <a name="install-nuget-packages"></a>安裝 NuGet 套件

您需要 NuGet 套件中的 WebJobs SDK、核心繫結、記錄架構和長期工作擴充功能。 以下是這些套件的**套件管理員主控台**命令，以及本文撰寫日期為止的最新穩定版本號碼：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

您也需要記錄提供者。 下列命令會安裝 Azure 應用程式 Insights 提供者和 `ConfigurationManager` 。 `ConfigurationManager` 可讓您從應用程式設定中取得 Application Insights 檢測金鑰。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

下列命令會安裝主控台提供者：

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 程式碼

建立主控台應用程式並安裝您所需的 NuGet 套件之後，您就可以開始使用 Durable Functions。 您可以使用 JobHost 程式碼來執行此動作。

若要使用 Durable Functions 擴充功能，請在 `Main` 方法中的 `JobHostConfiguration` 物件上呼叫 `UseDurableTask`：

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

如需可在 `DurableTaskExtension` 物件中設定的屬性清單，請參閱 [host.json](../functions-host-json.md#durabletask)。

`Main` 方法也是用來設定記錄提供者的位置。 下列範例會設定主控台和 Application Insights 提供者。

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

Webjob 內容中的 Durable Functions 與 Azure Functions 內容中的 Durable Functions 有些不同。 當您撰寫程式碼時，請務必留意差異。

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

在 Webjob SDK 專案中，您可以在協調流程用戶端物件上呼叫方法，而不是傳送 HTTP 要求。 下列方法對應至三個可使用 HTTP 管理 API 來進行的工作：

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

範例專案中的協調流程用戶端函式會啟動協調器函式，然後進入 `GetStatusAsync` 每2秒呼叫一次的迴圈：

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

您已 Durable Functions 設定成以 WebJob 的形式執行，而且您現在已經瞭解這與以獨立 Azure Functions 的形式執行 Durable Functions 有何不同。 此時，查看範例中的工作可能會很有説明。

本節概述如何執行[範例專案](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining)。 如需會說明如何在本機執行 WebJobs SDK 專案並將其部署至 Azure WebJob 的詳細指示，請參閱[開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)。

### <a name="run-locally"></a>在本機執行

1. 確定儲存體模擬器正在執行 (請參閱[先決條件](#prerequisites))。

1. 如果您想要在本機執行專案時查看 Application Insights 中的記錄：

    a. 建立 Application Insights 資源，並對其使用**一般**應用程式類型。

    b. 將檢測金鑰儲存在 App.config** 檔案。

1. 執行專案。

### <a name="run-in-azure"></a>在 Azure 中執行

1. 建立 Web 應用程式和儲存體帳戶。

1. 在 web 應用程式中，將儲存體連接字串儲存在名為的應用程式設定中 `AzureWebJobsStorage` 。

1. 建立 Application Insights 資源，並對其使用**一般**應用程式類型。

1. 將檢測金鑰儲存在名為的應用程式設定中 `APPINSIGHTS_INSTRUMENTATIONKEY` 。

1. 部署為 WebJob。

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

本文說明如何開發 Webjob SDK 2.x 專案。 如果您正在開發[WEBJOB SDK](../../app-service/webjobs-sdk-get-started.md) 3.x 專案，本節可協助您瞭解差異。

所引進的主要變更是使用 .NET Core，而不是 .NET Framework。 若要建立 Webjob SDK 3.x 專案，指示相同，但有下列例外狀況：

1. 建立 .NET Core 主控台應用程式。 在 [Visual Studio**新增專案**] 對話方塊中，選取 [ **.net core**  >  **主控台應用程式（.net core）**]。 專案檔會將 `TargetFramework` 指定為 `netcoreapp2.x`。

1. 選擇下列套件的發行版本 Webjob SDK 3.x：

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. 使用 .NET Core 設定架構，在檔案的*appsettings.js*中設定儲存體連接字串和 Application Insights 檢測金鑰。 以下為範例：

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. 變更 `Main` 方法程式碼以執行此動作。 以下是範例：

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
