---
title: 在 Azure API for FHIR®中啟用診斷記錄
description: 本文說明如何在 Azure API for FHIR 中啟用診斷記錄功能®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 948ca03b5bf503c884df5df56c61951b381874a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870868"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>在 Azure API for FHIR®中啟用診斷記錄

在本文中，您將瞭解如何在 Azure API for FHIR®中啟用診斷記錄，並能夠查看這些記錄的一些範例查詢。 對於符合法規需求（例如 HIPAA）的任何醫療保健服務，都必須具備診斷記錄的存取權。 Azure API for FHIR®中啟用診斷記錄的功能，就是 Azure 入口網站中的[**診斷設定**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)。 

## <a name="enable-audit-logs"></a>啟用 audit 記錄
1. 若要在 Azure API for FHIR®中啟用診斷記錄，請在 Azure 入口網站中選取您的 Azure API for FHIR®服務 
2. 流覽至 [**診斷設定**] [  
 ![ 診斷設定]](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. 選取 [ **+ 新增診斷設定**]

4. 輸入設定的名稱

5. 選取您想要用來存取診斷記錄的方法：

    1. 封存**至儲存體帳戶**以進行審核或手動檢查。 您想要使用的儲存體帳戶必須已經建立。
    2. **串流至事件中樞**，以供協力廠商服務或自訂分析解決方案進行內嵌。 您需要先建立事件中樞命名空間和事件中樞原則，才能設定此步驟。
    3. **串流至 Azure 監視器中的 Log Analytics**工作區。 您必須先建立您的記錄分析工作區，才能選取此選項。

6. 選取**AuditLogs**和您想要捕獲的任何計量

7. 按一下 [Save] \(儲存)。

> [!Note] 
> 最多可能需要15分鐘的時間，才會在 Log Analytics 中顯示第一個記錄。  
 
如需有關如何使用診斷記錄的詳細資訊，請參閱[Azure 資源記錄檔](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview)

## <a name="audit-log-details"></a>Audit 記錄詳細資料
此時，Azure API for FHIR®服務會傳回 audit 記錄檔中的下欄欄位： 

|欄位名稱  |類型  |注意  |
|---------|---------|---------|
|CallerIdentity|動態|包含身分識別資訊的泛型屬性包
|CallerIdentityIssuer|String|Issuer 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|呼叫者的 IP 位址 
|CorrelationId|String| 相互關連識別碼
|FhirResourceType|String|執行作業的資源類型
|LogCategory|String|記錄類別（我們目前傳回 ' AuditLogs ' LogCategory）
|位置|String|處理要求之伺服器的位置（例如美國中南部）
|OperationDuration|Int|完成此要求所花費的時間（以秒為單位）
|OperationName|String| 描述作業的類型（例如更新、搜尋類型）
|RequestUri|String|要求 URI 
|ResultType|String|目前可用的值為 [**已啟動**]、[**成功**] 或 [**失敗**]
|StatusCode|Int|HTTP 狀態碼。 （例如，200） 
|TimeGenerated|Datetime|事件的日期和時間|
|屬性|String| 描述 fhirResourceType 的屬性
|SourceSystem|String| 來源系統（在此案例中一律為 Azure）
|TenantId|String|租用戶識別碼
|類型|String|記錄類型（在此案例中一律為 MicrosoftHealthcareApisAuditLog）
|_ResourceId|String|資源的詳細資料

## <a name="sample-queries"></a>範例查詢

以下是您可以用來流覽記錄資料的幾個基本 Application Insights 查詢。

執行此查詢以查看**100 的最新**記錄：

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

執行此查詢以依據**FHIR 資源類型**來分組作業：

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

執行此查詢以取得所有**失敗的結果**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>結論 
若要監視服務和提供合規性報告，必須有診斷記錄的存取權。 Azure API for FHIR®可讓您透過診斷記錄來執行這些動作。 
 
FHIR®是 HL7 的注冊商標，並與 HL7 的許可權搭配使用。

## <a name="next-steps"></a>後續步驟
在本文中，您已瞭解如何針對 Azure API for FHIR®啟用 Audit 記錄。 接下來，請瞭解您可以在 Azure API for FHIR 中設定的其他設定
 
>[!div class="nextstepaction"]
>[其他設定](azure-api-for-fhir-additional-settings.md)