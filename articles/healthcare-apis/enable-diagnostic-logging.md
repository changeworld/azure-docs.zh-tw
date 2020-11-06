---
title: 啟用 Azure API for FHIR®中的診斷記錄
description: 本文說明如何在 Azure API for FHIR 中啟用診斷記錄®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 262509df98b93c7902d83f90756872a16d84198f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398125"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>啟用 Azure API for FHIR®中的診斷記錄

在本文中，您將瞭解如何在 Azure API for FHIR®中啟用診斷記錄，並能夠查看這些記錄的一些範例查詢。 對於符合法規需求 (例如 HIPAA) 必須符合規範的醫療保健服務而言，存取診斷記錄是不可或缺的。 Azure API for FHIR®中啟用診斷記錄的功能是 Azure 入口網站中的 [**診斷設定**](../azure-monitor/platform/diagnostic-settings.md) 。 

## <a name="enable-audit-logs"></a>啟用 audit 記錄
1. 若要在 Azure API for FHIR®中啟用診斷記錄，請在 Azure 入口網站中選取您的 Azure API for FHIR®服務 
2. 流覽至 **診斷設定**  
 ![ 診斷設定](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. 選取 [ **+ 新增診斷設定** ]

4. 輸入設定的名稱

5. 選取您要用來存取診斷記錄的方法：

    1. 封存 **至儲存體帳戶** 進行審核或手動檢查。 您要使用的儲存體帳戶必須已經建立。
    2. **串流至事件中樞** ，以供協力廠商服務或自訂分析解決方案進行內嵌。 您將需要先建立事件中樞命名空間和事件中樞原則，才能設定此步驟。
    3. 在 Azure 監視器中 **串流至 Log Analytics** 工作區。 您將需要先建立記錄分析工作區，才能選取此選項。

6. 選取 **AuditLogs** 和您想要捕捉的任何計量

7. 按一下 [Save] \(儲存)。

> [!Note] 
> 最多可能需要15分鐘的時間，才會在 Log Analytics 中顯示第一個記錄。  
 
如需如何使用診斷記錄的詳細資訊，請參閱 [Azure 資源記錄檔](../azure-monitor/platform/platform-logs-overview.md)

## <a name="audit-log-details"></a>Audit 記錄檔詳細資料
目前，Azure API for FHIR®服務會在審核記錄中傳回下欄欄位： 

|欄位名稱  |類型  |注意  |
|---------|---------|---------|
|CallerIdentity|動態|包含身分識別資訊的泛型屬性包
|CallerIdentityIssuer|字串|Issuer 
|CallerIdentityObjectId|字串|Object_Id 
|CallerIPAddress|字串|呼叫端的 IP 位址 
|CorrelationId|字串| 相互關連識別碼
|FhirResourceType|字串|執行作業的資源類型
|LogCategory|字串|記錄類別 (我們目前傳回 ' AuditLogs ' LogCategory) 
|Location|字串|處理要求的伺服器位置 (例如美國中南部) 
|OperationDuration|Int|完成此要求所花費的時間（秒）
|OperationName|字串| 描述作業的類型 (例如 update、搜尋類型) 
|RequestUri|字串|要求 URI 
|ResultType|字串|目前的可用值是 [ **已啟動** ]、[ **成功** ] 或 [ **失敗** ]
|StatusCode|Int|HTTP 狀態碼。  (例如 200)  
|TimeGenerated|Datetime|事件的日期和時間|
|屬性|字串| 描述 fhirResourceType 的屬性
|SourceSystem|字串| 在此情況下，來源系統 (一律是 Azure) 
|TenantId|字串|租用戶識別碼
|類型|String|在此情況下，一律會 MicrosoftHealthcareApisAuditLog 的記錄類型 () 
|_ResourceId|字串|資源的詳細資料

## <a name="sample-queries"></a>範例查詢

以下是一些您可以用來探索記錄資料的基本 Application Insights 查詢。

執行此查詢以查看 **100 的最新** 記錄：

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

執行此查詢以依 **FHIR 資源類型** 將作業分組：

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

執行此查詢以取得所有 **失敗的結果**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>結論 
存取診斷記錄對於監視服務和提供合規性報告非常重要。 Azure API for FHIR®可讓您透過診斷記錄執行這些動作。 
 
FHIR®是 HL7 的注冊商標，並搭配 HL7 的許可權使用。

## <a name="next-steps"></a>後續步驟
在本文中，您已瞭解如何啟用 Azure API for FHIR®的 Audit 記錄檔。 接下來，請瞭解您可以在 Azure API for FHIR 中設定的其他其他設定
 
>[!div class="nextstepaction"]
>[其他設定](azure-api-for-fhir-additional-settings.md)