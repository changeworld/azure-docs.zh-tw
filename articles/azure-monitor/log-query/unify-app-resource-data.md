---
title: 整合多個 Azure 監視器 Application Insights 資源 | Microsoft Docs
description: 本文提供有關如何使用 Azure 監視器記錄中的函數來查詢多個 Application Insights 資源，並將該資料視覺化的詳細資訊。
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137488"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>整合多個 Azure 監視器 Application Insights 資源 
本文說明如何在同一個位置查詢和查看所有 Application Insights 記錄資料，即使它們位於不同的 Azure 訂用帳戶中，也可以取代 Application Insights Connector。 您可以包含在單一查詢中的 Application Insights 資源數目限制為100。

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>查詢多個 Application Insights 資源的建議方法 
在查詢中列出多個 Application Insights 資源可能很麻煩且難以維護。 相反地，您可以利用函數將查詢邏輯與應用程序範圍分開。  

此範例示範如何監視多個 Application Insights 資源，並將失敗的要求計數依應用程式名稱視覺化。

使用聯集運算子搭配應用程式清單來建立函式，然後使用 *applicationsScoping* 別名將查詢在您的工作區中儲存成函式。 

您可以隨時修改所列出的應用程式，方法是在入口網站中瀏覽至您工作區中的 [查詢總管] 並選取函式來進行編輯後再儲存，或是使用 `SavedSearch` PowerShell Cmdlet。 

>[!NOTE]
>這個方法無法搭配記錄警示使用，因為警示規則資源（包括工作區和應用程式）的存取驗證是在警示建立期間執行。 不支援在建立警示之後，將新資源新增至函式。 如果您想要針對記錄警示中的資源範圍使用函式，您必須在入口網站中編輯警示規則，或使用 Resource Manager 範本來更新已設定範圍的資源。 或者，您可以在記錄警示查詢中包含資源的清單。

`withsource= SourceApp` 命令在結果中新增一個資料行，可指定傳送記錄的應用程式。 在此範例中，parse 運算子是選擇性的，並使用從 SourceApp 屬性中解壓縮應用程式名稱。 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

您現在可以在跨資源查詢中使用 applicationsScoping 函數：  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

該查詢使用 Application Insights 資料結構，雖然查詢是在工作區中執行的，因為 applicationsScoping 函數會傳回 Application Insights 資料結構。 函式別名會從所有定義的應用程式傳送要求的聯集。 接著，查詢會篩選失敗的要求，並依應用程式將趨勢視覺化。

![跨查詢結果範例](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>新的 [scheduledQueryRules API](../log-query/cross-workspace-query.md) 支援記錄警示中的[跨資源查詢](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 根據預設，Azure 監視器會使用[舊版 Log Analytics 警示 API](../platform/api-alerts.md) 從 Azure 入口網站建立新的記錄警示規則，除非您從[舊版記錄警示 API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) 切換。 切換之後，新的 API 將成為 Azure 入口網站中新警示規則的預設值，並允許您建立跨資源查詢記錄警示規則。 您可以使用[適用於 scheduledQueryRules API 的 ARM 範本](../log-query/cross-workspace-query.md)建立[跨資源查詢](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)記錄警示規則而無需進行切換 – 但此警示規則可透過 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 進行管理，而不是 Azure 入口網站。

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights 和 Log Analytics 工作區結構描述差異
下表顯示 Log Analytics 和 Application Insights 之間的結構描述差異。  

| Log Analytics 工作區屬性| Application Insights 資源屬性|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | NAME |
| AvailabilityTimestamp | timestamp |
| 瀏覽器 | client_browser |
| City | client_city |
| ClientIP | client_IP |
| 電腦 | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | NAME | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | NAME | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | NAME | 
| RequestSuccess | 成功 | 
| ResponseCode | ResultCode | 
| 角色 | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>後續步驟

使用[記錄搜尋](../../azure-monitor/log-query/log-query-overview.md)來檢視 Application Insights 應用程式的詳細資訊。
