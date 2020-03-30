---
title: Azure 資料工廠中的 Webhook 活動
description: Webhook 活動不會繼續執行管道，直到它使用使用者指定的特定條件驗證附加資料集。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213009"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure 資料工廠中的 Webhook 活動

Webhook 活動可以通過自訂代碼控制管道的執行。 使用 Webhook 活動時，客戶代碼可以調用終結點並將其傳遞回檔 URL。 管道運行等待回檔調用，然後再繼續下一個活動。

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
**名稱** | 網鉤活動的名稱。 | String | 是 |
**型別** | 必須設置為"網路掛"。 | String | 是 |
**方法** | 目標終結點的 REST API 方法。 | 字串。 支援的類型為"POST"。 | 是 |
**URL** | 目標終結點和路徑。 | 具有字串**結果類型**值的字串或運算式。 | 是 |
**頭** | 傳送至要求的標頭。 下面是一個在請求上設置語言和類型的示例： `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`。 | 具有字串**結果類型**值的字串或運算式。 | 是。 需要`Content-Type`這樣的`"headers":{ "Content-Type":"application/json"}`標頭。 |
**身體** | 代表傳送至端點的承載。 | 有效的 JSON 或具有 JSON**的結果類型**值的運算式。 有關請求有效負載的架構，請參閱[請求有效負載架構](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema)。 | 是 |
**認證** | 用於調用終結點的身份驗證方法。 支援的類型是"基本"和"用戶端憑證"。 如需詳細資訊，請參閱[驗證](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication)。 如果不需要身份驗證，則排除此屬性。 | 具有字串**結果類型**值的字串或運算式。 | 否 |
**超時** | 活動等待**調用 callBackUri**指定的回檔的時間。 預設值為 10 分鐘（"00：10：00"）。 值具有時間跨度格式*d*。*hh*：*毫米*：*s.* | String | 否 |
**回檔時報告狀態** | 允許使用者報告 Webhook 活動的失敗狀態。 | Boolean | 否 |

## <a name="authentication"></a>驗證

Webhook 活動支援以下身份驗證類型。

### <a name="none"></a>None

如果不需要身份驗證，請不要包含**身份驗證**屬性。

### <a name="basic"></a>基本

指定用於基本驗證的使用者名和密碼。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>用戶端憑證

指定 PFX 檔和密碼的 Base64 編碼內容。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>受控識別

使用資料工廠的託管標識指定為其請求訪問權杖的資源 URI。 若要呼叫 Azure 資源管理 API，請使用 `https://management.azure.com/`。 有關託管標識的工作原理的詳細資訊，請參閱[Azure 資源概述的託管標識](/azure/active-directory/managed-identities-azure-resources/overview)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 如果資料工廠配置了 Git 存儲庫，則必須在 Azure 金鑰保存庫中存儲憑據才能使用基本驗證或用戶端憑證身份驗證。 Azure 資料工廠不將密碼存儲在 Git 中。

## <a name="additional-notes"></a>其他注意事項

資料工廠傳遞發送到 URL 終結點的正文中的附加屬性**調用 BackUri。** 資料工廠希望此 URI 在指定的超時值之前調用。 如果未調用 URI，則活動將失敗，狀態為"超時"。

當對自訂終結點的調用失敗時，Webhook 活動將失敗。 任何錯誤訊息都可以添加到回檔正文，並在以後的活動中使用。

對於每個 REST API 呼叫，如果終結點在一分鐘內未回應，用戶端都會超時。 此行為是標準 HTTP 最佳做法。 要解決此問題，實現 202 模式。 在當前情況下，終結點返回 202（已接受）和用戶端輪詢。

請求上的一分鐘超時與活動超時無關。 後者用於等待**回檔 Uri**指定的回檔。

傳遞到回檔 URI 的正文必須有效的 JSON。 將 `Content-Type` 標頭設定為 `application/json`。

在**回檔屬性上使用"報表"狀態**時，在進行回檔時，必須向正文添加以下代碼：

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

請參閱資料工廠支援的以下控制流活動：

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [對於每個活動](control-flow-for-each-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
