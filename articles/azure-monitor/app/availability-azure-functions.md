---
title: 使用 Azure 函數創建和運行自訂可用性測試
description: 此文檔將介紹如何使用 Track 可用性（）創建 Azure 函數，該函數將根據 TimerTrigger 函數中給出的配置定期運行。 此測試的結果將發送到您的應用程式見解資源，您可以在其中查詢和警報可用性結果資料。 自訂測試將允許您編寫比使用門戶 UI 更複雜的可用性測試、監視 Azure VNET 內部的應用、更改終結點位址或創建可用性測試（如果區域不可用）。
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665794"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure 函數創建和運行自訂可用性測試

本文將介紹如何使用 Track 可用性（）創建 Azure 函數，該函數將根據 TimerTrigger 函數中提供的配置使用您自己的業務邏輯定期運行。 此測試的結果將發送到您的應用程式見解資源，您可以在其中查詢和警報可用性結果資料。 這允許您創建自訂測試，類似于通過門戶中[的可用性監視](../../azure-monitor/app/monitor-web-app-availability.md)可以執行的測試。 自訂測試將允許您編寫比使用門戶 UI 更複雜的可用性測試、監視 Azure VNET 內部的應用、更改終結點位址或創建可用性測試（即使此功能在您所在區域中不可用）。

> [!NOTE]
> 此示例僅用於向您展示 Track 可用性（） API 呼叫在 Azure 函數中的工作方式。 不是如何編寫基礎 HTTP 測試代碼/業務邏輯，該邏輯需要將其轉換為功能齊全的可用性測試。 預設情況下，如果您遍歷此示例，您將創建一個可用性測試，該測試將始終生成失敗。

## <a name="create-timer-triggered-function"></a>創建計時器觸發函數

- 如果您有應用程式見解資源：
    - 預設情況下，Azure 函數創建應用程式見解資源，但如果要使用已創建的資源之一，則需要在創建期間指定該資源。
    - 按照以下選項，按照有關如何[創建 Azure 函數資源和計時器觸發函數](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（清理前停止）的說明進行操作。
        -  選擇頂部附近的 **"監視"** 選項卡。

            ![ 使用自己的應用見解資源創建 Azure 函數應用](media/availability-azure-functions/create-function-app.png)

        - 選擇"應用程式見解"下拉清單，鍵入或選擇資源的名稱。

            ![選擇現有的應用程式見解資源](media/availability-azure-functions/app-insights-resource.png)

        - 選擇 **"審閱" 創建**
- 如果尚未為計時器觸發函數創建應用程式見解資源：
    - 預設情況下，在創建 Azure 函數應用程式時，它將為您創建應用程式見解資源。
    - 按照有關如何[創建 Azure 函數資源和計時器觸發函數](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)的說明（清理前停止）。

## <a name="sample-code"></a>範例程式碼

將下面的代碼複製到 run.csx 檔中（這將替換預先存在的代碼）。 為此，請進入 Azure 函數應用程式，然後選擇左側的計時器觸發器函數。

>[!div class="mx-imgBorder"]
>![Azure 函數在 Azure 門戶中的 run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 對於要使用的終結點位址： `EndpointAddress= https://dc.services.visualstudio.com/v2/track`。 除非您的資源位於 Azure 政府或 Azure 中國這樣的區域，在這種情況下，請參閱本文，[介紹重寫預設終結點](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification)，並為區域選擇相應的遙測通道終結點。

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

在視圖檔右側，選擇 **"添加**"。 使用以下配置調用新的檔**函數.proj。**

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![在右側選擇中，添加。 命名檔函數.](media/availability-azure-functions/addfile.png)

在視圖檔右側，選擇 **"添加**"。 使用以下配置調用新檔**run 可用性測試.csx。**

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>檢查可用性

為了確保一切工作，您可以在應用程式見解資源的"可用性"選項卡中查看圖形。

> [!NOTE]
> 如果您在 run 可用性Test.csx 中實現了自己的業務邏輯，那麼您將看到成功的結果，如下面的螢幕截圖所示，如果沒有，則您將看到失敗的結果。

>[!div class="mx-imgBorder"]
>![具有成功結果的可用性選項卡](media/availability-azure-functions/availtab.png)

使用 Azure 函數設置測試時，您會注意到，與在"可用性"選項卡中使用 **"添加測試**"不同，測試的名稱不會顯示，並且您將無法與它進行交互。 結果是視覺化的，但您將獲得摘要視圖，而不是通過門戶創建可用性測試時獲得的相同詳細視圖。

要查看端到端事務詳細資訊，請選擇"鑽入下**成功**"或 **"失敗"，** 然後選擇示例。 您還可以通過選擇圖形上的資料點來獲取端到端事務詳細資訊。

>[!div class="mx-imgBorder"]
>![選擇示例可用性測試](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![端到端事務詳細資訊](media/availability-azure-functions/end-to-end.png)

如果運行的所有內容（不添加業務邏輯），則您將看到測試失敗。

## <a name="query-in-logs-analytics"></a>日誌中的查詢（分析）

您可以使用 Logs（分析）來查看可用性結果、依賴項等。 要瞭解有關日誌的詳細資訊，請訪問[日誌查詢概述](../../azure-monitor/log-query/log-query-overview.md)。

>[!div class="mx-imgBorder"]
>![可用性結果](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Dependencies](media/availability-azure-functions/dependencies.png) (相依性)

## <a name="next-steps"></a>後續步驟

- [應用程式映射](../../azure-monitor/app/app-map.md)
- [交易診斷](../../azure-monitor/app/transaction-diagnostics.md)
