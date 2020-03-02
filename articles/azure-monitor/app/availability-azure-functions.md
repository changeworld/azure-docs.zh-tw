---
title: 使用 Azure Functions 建立及執行自訂可用性測試
description: 本檔將說明如何使用 TrackAvailability （）建立 Azure 函式，此函式會根據 TimerTrigger 函式中指定的設定定期執行。 這項測試的結果將會傳送至您的 Application Insights 資源，您可以在其中查詢可用性結果資料併發出警示。 自訂測試可讓您撰寫比使用入口網站 UI、監視 Azure VNET 內的應用程式、變更端點位址，或建立可用性測試（如果您的區域中未提供）時更複雜的可用性測試。
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665794"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure Functions 建立及執行自訂可用性測試

本文將說明如何使用 TrackAvailability （）建立 Azure 函式，該函數會根據 TimerTrigger 函式中提供的設定，以您自己的商務邏輯定期執行。 這項測試的結果將會傳送至您的 Application Insights 資源，您可以在其中查詢可用性結果資料併發出警示。 這可讓您建立自訂的測試，就像您可以透過入口網站中的[可用性監視](../../azure-monitor/app/monitor-web-app-availability.md)來執行此動作。 自訂測試可讓您撰寫更複雜的可用性測試，而不是使用入口網站 UI、監視 Azure VNET 內的應用程式、變更端點位址，或建立可用性測試，即使您的區域無法使用這項功能。

> [!NOTE]
> 此範例是專為示範 TrackAvailability （） API 呼叫在 Azure 函式中的運作方式所設計。 不是如何撰寫基本的 HTTP 測試程式碼/商務邏輯，將它轉換成功能完整的可用性測試。 根據預設，如果您逐步執行此範例，將會建立一律會產生失敗的可用性測試。

## <a name="create-timer-triggered-function"></a>建立計時器觸發函式

- 如果您有 Application Insights 資源：
    - 根據預設，Azure Functions 會建立 Application Insights 資源，但如果您想要使用其中一個已建立的資源，則必須在建立期間指定它。
    - 請遵循如何[建立 Azure Functions 資源和計時器觸發函式](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（在清除前停止）的指示，並使用下列選項。
        -  選取靠近頂端的 [**監視**] 索引標籤。

            ![ 使用您自己的 App Insights 資源建立 Azure Functions 應用程式](media/availability-azure-functions/create-function-app.png)

        - 選取 [Application Insights] 下拉式清單方塊，然後輸入或選取您的資源名稱。

            ![選取現有的 Application Insights 資源](media/availability-azure-functions/app-insights-resource.png)

        - 選取 [**審核] + [建立**]
- 如果您還沒有為計時器觸發的函式建立 Application Insights 資源：
    - 根據預設，當您建立 Azure Functions 應用程式時，它會為您建立 Application Insights 資源。
    - 請遵循如何[建立 Azure Functions 資源和計時器觸發](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)函式（在清除前停止）的指示。

## <a name="sample-code"></a>範例程式碼

將下列程式碼複製到 .csx 檔案中（這會取代既有的程式碼）。 若要這麼做，請移至您的 Azure Functions 應用程式，並選取左側的計時器觸發程式函式。

>[!div class="mx-imgBorder"]
>![Azure 入口網站](media/availability-azure-functions/runcsx.png) 中的 Azure function .csx

> [!NOTE]
> 針對您要使用的端點位址： `EndpointAddress= https://dc.services.visualstudio.com/v2/track`。 除非您的資源位於 Azure Government 或 Azure 中國之類的區域，否則請參閱這篇文章，以瞭解如何覆[寫預設端點](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification)，並為您的區域選取適當的遙測通道端點。

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

在右側的 [查看檔案] 底下，選取 [**新增**]。 使用下列設定呼叫新的 file **function。**

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
>![在右側選取 [新增]。 將檔案命名為 proj](media/availability-azure-functions/addfile.png)

在右側的 [查看檔案] 底下，選取 [**新增**]。 使用下列設定來呼叫新的檔案**runAvailabilityTest. .csx** 。

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>檢查可用性

為確保所有專案都正常運作，您可以在 Application Insights 資源的 [可用性] 索引標籤中查看圖形。

> [!NOTE]
> 如果您在 runAvailabilityTest 中執行自己的商務邏輯，則會看到如下列螢幕擷取畫面所示的成功結果，如果您沒有這樣做，就會看到失敗的結果。

>[!div class="mx-imgBorder"]
>成功的結果 ![[可用性] 索引標籤](media/availability-azure-functions/availtab.png)

當您使用 Azure Functions 設定測試時，您會注意到，不同于在 [可用性] 索引標籤中使用 [**加入測試**]，您的測試名稱將不會出現，而且您將無法與其互動。 結果會視覺化，但您會看到摘要視圖，而不是您透過入口網站建立可用性測試時所取得的相同詳細觀點。

若要查看端對端交易詳細資料，請在 [深入探索] 下選取 [**成功**] 或 [**失敗**]，然後選取一個範例。 您也可以選取圖表上的資料點，以取得端對端交易詳細資料。

>[!div class="mx-imgBorder"]
>![選取範例可用性測試](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![端對端交易詳細資料](media/availability-azure-functions/end-to-end.png)

如果您依原樣執行所有專案（但不新增商務邏輯），則您會看到測試失敗。

## <a name="query-in-logs-analytics"></a>記錄中的查詢（分析）

您可以使用記錄（分析）來查看可用性結果、相依性等。 若要深入瞭解記錄，請造訪[記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。

>[!div class="mx-imgBorder"]
>![可用性結果](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Dependencies](media/availability-azure-functions/dependencies.png) (相依性)

## <a name="next-steps"></a>後續步驟

- [應用程式對應](../../azure-monitor/app/app-map.md)
- [交易診斷](../../azure-monitor/app/transaction-diagnostics.md)
