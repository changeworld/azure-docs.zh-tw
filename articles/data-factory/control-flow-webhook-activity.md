---
title: Azure Data Factory 中的 Webhook 活動
description: Webhook 活動不會繼續執行管線，直到它以使用者指定的特定準則驗證連接的資料集為止。
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
ms.openlocfilehash: 1ce41a5928d5b8a7c7df439ce5321cd15f0cc1d5
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634975"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Webhook 活動

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Webhook 活動可以透過您的自訂程式碼來控制管線的執行。 使用 webhook 活動時，客戶的程式碼可以呼叫端點，並將回呼 URL 傳遞給它。 管線執行會在繼續進行下一個活動之前等候回呼調用。

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
**方法** | 目標端點的 REST API 方法。 | 字串。 支援的類型為 "POST"。 | 是 |
**url** | 目標端點和路徑。 | 字串或含有字串之 **resultType** 值的運算式。 | 是 |
**頭** | 傳送至要求的標頭。 以下範例會在要求上設定語言和類型： `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` 。 | 字串或含有字串之 **resultType** 值的運算式。 | 是。 您 `Content-Type` 必須使用類似的標頭 `"headers":{ "Content-Type":"application/json"}` 。 |
**body** | 代表傳送至端點的承載。 | 有效的 JSON 或具有 JSON 之 **resultType** 值的運算式。 請參閱要求承載架構的 [要求承載架構](./control-flow-web-activity.md#request-payload-schema) 。 | 是 |
**認證** | 用來呼叫端點的驗證方法。 支援的類型為「基本」和「ClientCertificate」。 如需詳細資訊，請參閱[驗證](./control-flow-web-activity.md#authentication)。 如果不需要驗證，請排除此屬性。 | 字串或含有字串之 **resultType** 值的運算式。 | 否 |
**timeout** | 活動等候叫用 **callBackUri** 所指定回呼的時間長度。 預設值為10分鐘 ( "00:10:00" ) 。 值的 TimeSpan 格式為 *d* 。 *hh* ： *mm* ： *ss* 。 | String | 否 |
**報告回呼的狀態** | 讓使用者報告 webhook 活動的失敗狀態。 | Boolean | 否 |

## <a name="authentication"></a>驗證

Webhook 活動支援下列驗證類型。

### <a name="none"></a>None

如果不需要驗證，請不要包含 **驗證** 屬性。

### <a name="basic"></a>基本

指定要搭配基本驗證使用的使用者名稱和密碼。

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

使用 data factory 的受控識別來指定要求存取權杖的資源 URI。 若要呼叫 Azure 資源管理 API，請使用 `https://management.azure.com/`。 如需受控識別如何運作的詳細資訊，請參閱適用 [于 Azure 資源的受控識別總覽](../active-directory/managed-identities-azure-resources/overview.md)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 如果您的 data factory 是以 Git 存放庫設定，則您必須將認證儲存在 Azure Key Vault 中，才能使用基本或用戶端憑證驗證。 Azure Data Factory 不會在 Git 中儲存密碼。

## <a name="additional-notes"></a>其他注意事項

Data Factory 會將主體中的額外屬性 **callBackUri** 傳遞至 URL 端點。 Data Factory 預期此 URI 必須在指定的超時值之前叫用。 如果未叫用 URI，活動會失敗，並出現「逾時」狀態。

當自訂端點的呼叫失敗時，webhook 活動會失敗。 任何錯誤訊息都可以新增至回呼主體，並在稍後的活動中使用。

針對每個 REST API 呼叫，如果端點未在一分鐘內回應，用戶端會超時。 此行為是標準 HTTP 最佳做法。 若要修正此問題，請執行202模式。 在目前的案例中，端點會傳回 202 (已接受的) 且用戶端會進行輪詢。

要求的一分鐘超時與活動超時無關。 後者可用來等候 **callbackUri** 所指定的回呼。

傳遞回回呼 URI 的主體必須是有效的 JSON。 將 `Content-Type` 標頭設定為 `application/json`。

當您使用回呼屬性的 **報表狀態** 時，您必須在進行回呼時，將下列程式碼加入主體：

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

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)