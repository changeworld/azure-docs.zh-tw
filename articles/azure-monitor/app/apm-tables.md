---
title: Azure 監視器 Application Insights 以工作區為基礎的資源結構描述
description: 瞭解 Azure 監視器 Application Insights 工作區資源的新資料表結構和架構。
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 8f0bee64d74cfd5b6abef5c918c023974fda3fcf
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931048"
---
# <a name="workspace-based-resource-changes-preview"></a>以工作區為基礎的資源變更 (預覽) 

在引進以 [工作區為基礎的 Application Insights 資源](create-workspace-resource.md)之前，Application Insights 的資料會與 Azure 監視器中的其他記錄資料分開儲存。 兩者都是以 Azure 資料總管為基礎，且使用相同的 Kusto 查詢語言 (KQL) 。 這在 Azure 監視器的 [記錄](../platform/data-platform-logs.md)中有所說明。

使用以工作區為基礎的 Application Insights 資源資料會儲存在 Log Analytics 工作區中，並包含其他監視資料和應用程式資料。 這可讓您更輕鬆地分析多個解決方案之間的資料，並利用工作區的功能，來簡化您的設定。

## <a name="table-structure"></a>資料表結構

| 舊版資料表名稱 | 新的資料表名稱 | 描述 |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  可用性測試的摘要資料。|
| browserTimings | AppBrowserTimings | 用戶端效能的相關資料，例如處理傳入資料所花費的時間。|
| 相依性 | AppDependencies | 從應用程式到其他元件的呼叫 (包括) 透過 TrackDependency 錄製的外部元件 ( # A3 –例如，呼叫 REST API、資料庫或檔案系統。  |
| customEvents | AppEvents | 您的應用程式所建立的自訂事件。 |
| customMetrics | AppMetrics | 您的應用程式所建立的自訂計量。 |
| pageViews | AppPageViews| 具有瀏覽器資訊的每個網站視圖的相關資料。 |
| performanceCounters | AppPerformanceCounters | 支援應用程式之計算資源的效能測量，例如 Windows 效能計數器。 |
| requests | AppRequests | 您的應用程式所收到的要求。 例如，系統會針對 web 應用程式收到的每個 HTTP 要求記錄個別的要求記錄。  |
| 例外狀況 | AppSystemEvents | 應用程式執行時間擲回的例外狀況會將伺服器端和用戶端 (瀏覽器) 例外狀況。 |
| traces | AppTraces | 詳細記錄 (追蹤) 透過 TrackTrace ( # A3 所記錄的應用程式程式碼/記錄架構發出。 |

## <a name="table-schemas"></a>資料表架構

下列各節顯示傳統屬性名稱與新的工作區 Application Insights 屬性名稱之間的對應。  使用此資訊來轉換使用舊版資料表的任何查詢。

大部分的資料行都具有具有不同大小寫的相同名稱。 由於 KQL 區分大小寫，因此您必須變更每個資料行名稱，以及常設查詢中的資料表名稱。 除了大小寫之外，還會反白顯示具有變更的資料行。 即使是以工作區為基礎的資源，您仍然可以在 Application Insights 資源的 [ **記錄** ] 窗格中使用傳統的 Application Insights 查詢。 從 Log Analytics 工作區體驗的內容中查詢時，需要新的屬性名稱。

### <a name="appavailabilityresults"></a>AppAvailabilityResults

舊版資料表：可用性

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|duration|real|DurationMs|real|
|`id`|字串|`Id`|字串|
|iKey|字串|IKey|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|String|
|location|字串|Location|字串|
|message|字串|訊息|字串|
|NAME|字串|名稱|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|performanceBucket|字串|PerformanceBucket|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|size|real|大小|real|
|成功|字串|成功|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

### <a name="appbrowsertimings"></a>AppBrowserTimings

舊版資料表： browserTimings

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|iKey|字串|IKey|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|字串|
|NAME|字串|名稱|Datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|performanceBucket|字串|PerformanceBucket|字串|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|字串|SdkVersion|字串|
|sendDuration|real|SendDurationMs|real|
|session_Id|字串|SessionId|字串|
|timestamp|Datetime|TimeGenerated|Datetime|
|totalDuration|real|TotalDurationMs|real|
|url|字串|Url|字串|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

### <a name="appdependencies"></a>AppDependencies

舊版資料表：相依性

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|資料|字串|資料|字串|
|duration|real|DurationMs|real|
|`id`|字串|`Id`|字串|
|iKey|字串|IKey|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|String|
|NAME|字串|名稱|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|performanceBucket|字串|PerformanceBucket|字串|
|ResultCode|字串|ResultCode|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|成功|字串|成功|Bool|
|目標|字串|目標|字串|
|timestamp|Datetime|TimeGenerated|Datetime|
|type|字串|DependencyType|字串|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

### <a name="appevents"></a>AppEvents

舊版資料表： customEvents

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|iKey|字串|IKey|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|字串|
|NAME|字串|名稱|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

### <a name="appmetrics"></a>AppMetrics

舊版資料表： customMetrics

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|iKey|字串|IKey|字串|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|字串|
|NAME|字串|名稱|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|
|value|real| (移除) ||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

舊版資料表： pageViews

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|duration|real|DurationMs|real|
|`id`|字串|`Id`|字串|
|iKey|字串|IKey|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|String|
|NAME|字串|名稱|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|performanceBucket|字串|PerformanceBucket|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|字串|Url|字串|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

### <a name="appperformancecounters"></a>AppPerformanceCounters

舊版資料表： performanceCounters

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|category|字串|類別|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|counter|字串| (移除) ||
|customDimensions|動態|屬性|動態|
|iKey|字串|IKey|字串|
|instance|字串|執行個體|字串|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|字串|
|NAME|字串|名稱|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|
|value|real|值|real|

### <a name="apprequests"></a>AppRequests

舊版資料表：要求

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|duration|real|DurationMs|Real|
|`id`|字串|`Id`|字串|
|iKey|字串|IKey|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|String|
|NAME|字串|Name|String|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|performanceBucket|字串|PerformanceBucket|字串|
|ResultCode|字串|ResultCode|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|source|字串|來源|字串|
|成功|字串|成功|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|字串|Url|字串|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

### <a name="appsystemevents"></a>AppSystemEvents

舊版資料表：例外狀況

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|組件 (assembly)|字串|組件|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|詳細資料|動態|詳細資料|動態|
|handledAt|字串|HandledAt|字串|
|iKey|字串|IKey|字串|
|innermostAssembly|字串|InnermostAssembly|字串|
|innermostMessage|字串|InnermostMessage|字串|
|innermostMethod|字串|InnermostMethod|字串|
|innermostType|字串|InnermostType|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|字串|
|message|字串|訊息|字串|
|method|字串|方法|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|outerAssembly|字串|OuterAssembly|字串|
|outerMessage|字串|OuterMessage|字串|
|outerMethod|字串|OuterMethod|字串|
|outerType|字串|OuterType|字串|
|problemId|字串|ProblemId|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|severityLevel|int|SeverityLevel|int|
|timestamp|Datetime|TimeGenerated|Datetime|
|type|字串|ExceptionType|字串|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

### <a name="apptraces"></a>AppTraces

舊版資料表：追蹤

|ApplicationInsights|類型|LogAnalytics|類型|
|:---|:---|:---|:---|
|appId|字串|\_ResourceGUID|字串|
|application_Version|字串|AppVersion|字串|
|appName|字串|\_ResourceId|字串|
|client_Browser|字串|ClientBrowser|字串|
|client_City|字串|ClientCity|字串|
|client_CountryOrRegion|字串|ClientCountryOrRegion|字串|
|client_IP|字串|ClientIP|字串|
|client_Model|字串|ClientModel|字串|
|client_OS|字串|ClientOS|字串|
|client_StateOrProvince|字串|ClientStateOrProvince|字串|
|client_Type|字串|ClientType|字串|
|cloud_RoleInstance|字串|AppRoleInstance|字串|
|cloud_RoleName|字串|AppRoleName|字串|
|customDimensions|動態|屬性|動態|
|customMeasurements|動態|量測|動態|
|iKey|字串|IKey|字串|
|itemCount|int|ItemCount|int|
|itemId|字串|\_ItemId|字串|
|itemType|字串|類型|字串|
|message|字串|訊息|字串|
|operation_Id|字串|OperationId|字串|
|operation_Name|字串|OperationName|字串|
|operation_ParentId|字串|OperationParentId|字串|
|operation_SyntheticSource|字串|OperationSyntheticSource|字串|
|sdkVersion|字串|SdkVersion|字串|
|session_Id|字串|SessionId|字串|
|severityLevel|int|SeverityLevel|int|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|字串|UserAccountId|字串|
|user_AuthenticatedId|字串|UserAuthenticatedId|字串|
|user_Id|字串|UserId|字串|

## <a name="next-steps"></a>後續步驟

* [探索度量](../platform/metrics-charts.md)
* [撰寫分析查詢](../log-query/log-query-overview.md)

