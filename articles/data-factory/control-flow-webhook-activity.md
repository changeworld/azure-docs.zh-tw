---
title: Azure Data Factory 中的 Webhook 活動
description: Webhook 活動不會繼續執行管線，直到它以使用者指定的特定準則驗證附加的資料集為止。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: ced2279878ee2eb361ec7338647418658e411513
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79213009"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Webhook 活動

Webhook 活動可以透過您的自訂程式碼來控制管線的執行。 使用 webhook 活動，客戶的程式碼可以呼叫端點，並將回呼 URL 傳遞給它。 管線執行會等待回呼調用，再繼續進行下一個活動。

## <a name="syntax"></a>語法

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
**name** | Webhook 活動的名稱。 | String | 是 |
**type** | 必須設定為 "WebHook"。 | String | 是 |
**method** | 目標端點的 REST API 方法。 | 字串。 支援的類型為 "POST"。 | 是 |
**url** | 目標端點和路徑。 | 字串或具有字串之**resultType**值的運算式。 | 是 |
**headers** | 傳送至要求的標頭。 以下範例會設定要求的語言和類型： `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`。 | 字串或具有字串之**resultType**值的運算式。 | 是。 需要 `Content-Type` 標頭（例如 `"headers":{ "Content-Type":"application/json"}`）。 |
**body** | 代表傳送至端點的承載。 | 有效的 JSON，**或值為 JSON 的運算式**。 如需要求裝載的架構，請參閱[要求裝載架構](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema)。 | 是 |
**驗證** | 用來呼叫端點的驗證方法。 支援的類型為「基本」和「ClientCertificate」。 如需詳細資訊，請參閱[驗證](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication)。 如果不需要驗證，請排除此屬性。 | 字串或具有字串之**resultType**值的運算式。 | 否 |
**timeout** | 活動等候**callBackUri**指定之回呼的時間長度。 預設值為10分鐘（"00:10:00"）。 值具有 TimeSpan 格式*d*。*hh*：*mm*：*ss*。 | String | 否 |
**回撥時報告狀態** | 讓使用者報告 webhook 活動的失敗狀態。 | Boolean | 否 |

## <a name="authentication"></a>驗證

Webhook 活動支援下列驗證類型。

### <a name="none"></a>None

如果不需要驗證，請不要包含**驗證**屬性。

### <a name="basic"></a>基本

指定要與基本驗證搭配使用的使用者名稱和密碼。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>用戶端憑證

指定以 Base64 編碼的 PFX 檔案內容和密碼。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>受控識別

使用 data factory 的受控識別來指定要求存取權杖的資源 URI。 若要呼叫 Azure 資源管理 API，請使用 `https://management.azure.com/`。 如需受控識別如何工作的詳細資訊，請參閱[適用于 Azure 資源的受控識別總覽](/azure/active-directory/managed-identities-azure-resources/overview)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 如果您的 data factory 設定了 Git 存放庫，您必須將認證儲存在 Azure Key Vault 中，才能使用基本或用戶端憑證驗證。 Azure Data Factory 不會將密碼儲存在 Git 中。

## <a name="additional-notes"></a>其他注意事項

Data Factory 會在傳送至 URL 端點的主體中傳遞額外的屬性**callBackUri** 。 Data Factory 預期在指定的超時值之前叫用此 URI。 如果未叫用 URI，活動就會失敗，並出現狀態 "TimedOut"。

當自訂端點的呼叫失敗時，webhook 活動就會失敗。 任何錯誤訊息都可以加入至回呼主體，並用於後續的活動中。

針對每個 REST API 呼叫，如果端點在一分鐘內沒有回應，用戶端就會超時。 這種行為是標準的 HTTP 最佳作法。 若要修正此問題，請執行202模式。 在目前的案例中，端點會傳回202（已接受），而用戶端會輪詢。

要求的一分鐘超時與活動超時無關。 後者是用來等候**callbackUri**所指定的回呼。

傳回回撥 URI 的主體必須是有效的 JSON。 將 `Content-Type` 標頭設定為 `application/json`。

當您使用回呼屬性的**報告狀態**時，您必須在進行回呼時，將下列程式碼新增至本文：

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>後續步驟

請參閱下列 Data Factory 支援的控制流程活動：

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
