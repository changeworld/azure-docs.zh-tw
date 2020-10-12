---
title: 使用 Azure Functions 建立和執行自訂可用性測試
description: '本檔將說明如何使用 TrackAvailability ( # A1 來建立 Azure 函式，該函式會根據 TimerTrigger 函式中指定的設定定期執行。 這項測試的結果會傳送到您的 Application Insights 資源，您可以在其中查詢可用性結果資料併發出警示。 自訂測試可讓您撰寫更複雜的可用性測試，而非使用入口網站 UI、監視 Azure VNET 內的應用程式、變更端點位址，或建立可用性測試（如果您的區域中沒有提供的話）。'
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 3553b212d1b63d4bd239893ba90aa3465d98df60
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945646"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure Functions 建立和執行自訂可用性測試

本文將說明如何使用 TrackAvailability ( # A1 來建立 Azure 函式，此函式會根據您自己的商務邏輯 TimerTrigger 函式中提供的設定定期執行。 這項測試的結果會傳送到您的 Application Insights 資源，您可以在其中查詢可用性結果資料併發出警示。 這可讓您建立自訂的測試，類似于在入口網站中透過 [可用性監視](./monitor-web-app-availability.md) 所能進行的作業。 自訂測試可讓您撰寫比使用入口網站 UI 更複雜的可用性測試、監視 Azure VNET 內的應用程式、變更端點位址，或建立可用性測試，即使您的區域無法使用這項功能。

> [!NOTE]
> 此範例僅供示範 TrackAvailability ( # A1 API 呼叫在 Azure 函式中的運作方式。 不是如何撰寫基礎 HTTP 測試程式碼/商務邏輯，將其轉換成功能完整的可用性測試。 依預設，如果您逐步完成此範例，您將會建立永遠會產生失敗的可用性測試。

## <a name="create-timer-triggered-function"></a>建立計時器觸發函式

- 如果您有 Application Insights 資源：
    - 依預設 Azure Functions 會建立 Application Insights 資源，但如果您想要使用其中一個已建立的資源，您必須在建立期間指定該資源。
    - 依照下列選項，以瞭解如何 [建立 Azure Functions 資源和計時器觸發](../../azure-functions/functions-create-scheduled-function.md) 的函式 (在清除) 之前停止。
        -  選取靠近頂端的 [ **監視** ] 索引標籤。

            ![ 使用您自己的 App Insights 資源建立 Azure Functions 應用程式](media/availability-azure-functions/create-function-app.png)

        - 選取 [Application Insights] 下拉式清單方塊，然後輸入或選取資源的名稱。

            ![選取現有的 Application Insights 資源](media/availability-azure-functions/app-insights-resource.png)

        - 選取 [檢閱 + 建立]
- 如果您還沒有為計時器觸發的函式建立 Application Insights 資源：
    - 依預設，當您建立 Azure Functions 應用程式時，它會為您建立 Application Insights 資源。
    - 請依照有關如何 [建立 Azure Functions 資源和計時器觸發](../../azure-functions/functions-create-scheduled-function.md) 的函式， (在清除) 之前停止的指示。

## <a name="sample-code"></a>範例程式碼

將下列程式碼複製到 .csx 檔案 (這會取代預先存在的程式碼) 。 若要這樣做，請移至您的 Azure Functions 應用程式，然後在左側選取您的計時器觸發程式函式。

>[!div class="mx-imgBorder"]
>![Azure 入口網站中的 Azure 函數 run. .csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 針對您要使用的端點位址： `EndpointAddress= https://dc.services.visualstudio.com/v2/track` 。 除非您的資源位於 Azure Government 或 Azure 中國等區域中，否則請參閱這篇文章以瞭解如何覆 [寫預設端點](./custom-endpoints.md#regions-that-require-endpoint-modification) ，並為您的區域選取適當的遙測通道端點。

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

在右側的 [view files] 下，選取 [ **新增**]。 使用下列設定呼叫新的檔案**函數。**

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![在右側選取 [加入]。 將檔案命名為 proj](media/availability-azure-functions/addfile.png)

在右側的 [view files] 下，選取 [ **新增**]。 使用下列設定呼叫新的檔案 **runAvailabilityTest. .csx** 。

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>檢查可用性

為了確保一切運作正常，您可以在 Application Insights 資源的 [可用性] 索引標籤中查看圖形。

> [!NOTE]
> 如果您在 runAvailabilityTest 中執行自己的商務邏輯，則您會看到如下列螢幕擷取畫面所示的成功結果，如果您沒有這樣做，則會看到失敗的結果。 使用建立的測試 `TrackAvailability()` 會在測試名稱旁邊顯示為 [ **自訂** ]。

>[!div class="mx-imgBorder"]
>![具有成功結果的 [可用性] 索引標籤](media/availability-azure-functions/availability-custom.png)

若要查看端對端交易詳細資料，請選取 [切入] 下的 [ **成功** ] 或 [ **失敗** ]，然後選取範例。 您也可以選取圖形上的資料點，以取得端對端交易詳細資料。

>[!div class="mx-imgBorder"]
>![選取範例可用性測試](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![端對端交易詳細資料](media/availability-azure-functions/end-to-end.png)

如果您以 (的方式執行所有工作，而不) 新增商務邏輯，則您會看到測試失敗。

## <a name="query-in-logs-analytics"></a>在記錄中查詢 (分析) 

您可以使用記錄 (分析) 來查看可用性結果、相依性等。 若要深入瞭解記錄，請造訪 [記錄查詢總覽](../log-query/log-query-overview.md)。

>[!div class="mx-imgBorder"]
>![可用性結果](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![螢幕擷取畫面：顯示 [新查詢] 索引標籤，其相依性限制為50。](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>後續步驟

- [應用程式對應](./app-map.md)
- [交易診斷](./transaction-diagnostics.md)

