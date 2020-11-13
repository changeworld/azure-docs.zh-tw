---
title: Azure 事件方格 - 針對訂閱驗證進行疑難排解
description: 本文說明如何針對訂閱驗證進行疑難排解。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592935"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>針對 Azure 事件方格的訂閱驗證進行疑難排解
在事件訂閱建立期間，如果您看到錯誤訊息（例如 `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` ），則表示驗證交握失敗。 若要解決此錯誤，請執行以下幾方面的驗證：

- 使用 Postman 或捲曲或類似的工具，以 [範例 SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) 要求本文進行 HTTP POST 至 webhook url。
- 如果您的 webhook 正在執行同步驗證交握機制，請確認 ValidationCode 會作為回應的一部分傳回。
- 如果您的 webhook 正在執行非同步驗證交握機制，請確認您是 HTTP POST 傳回的是 200 OK。
- 如果您 `403 (Forbidden)` 的 webhook 在回應中傳回，請檢查您的 webhook 是否位於 Azure 應用程式閘道或 Web 應用程式防火牆後方。 如果是，則您需要停用這些防火牆規則，並再次執行 HTTP POST：
    - 920300 (要求遺漏 accept 標頭) 
    - 942430 (受限制的 SQL 字元異常偵測 (args) ：超過 (12) # A5 的特殊字元數
    - 920230 (偵測到多個 URL 編碼) 
    - 942130 (SQL 插入式攻擊：偵測到 SQL tautology。 ) 
    - 931130 (可能的遠端檔案包含 (RFI) 攻擊 = Off 網域參考/連結) 

> [!IMPORTANT]
> 如需 Webhook 端點驗證的詳細資訊，請參閱 [Webhook 事件傳遞](webhook-event-delivery.md)。

下列各節會示範如何使用 Postman 和捲曲驗證事件訂閱。  

## <a name="validate-event-grid-event-subscription-using-postman"></a>使用 Postman 驗證事件方格的事件訂閱
以下範例使用 Postman 來驗證事件方格事件的 Webhook 訂閱： 

![使用 Postman 進行事件方格的事件訂閱驗證](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

以下是 **SubscriptionValidationEvent** 範例 JSON：

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

以下是成功回應的範例：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

若要深入瞭解 Webhook 的事件方格事件驗證，請參閱[事件方格事件的端點驗證](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。


## <a name="validate-event-grid-event-subscription-using-curl"></a>使用 Curl 驗證事件方格的事件訂閱 
以下範例 Curl 命令是用來驗證事件方格事件的 Webhook 訂閱： 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>使用 Postman 驗證雲端事件訂閱
以下範例使用 Postman 來驗證雲端事件的 Webhook 訂閱： 

![使用 Postman 進行雲端事件訂閱驗證](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

使用 **HTTP 選項** 方法進行雲端事件驗證。 若要深入瞭解 Webhook 的雲端事件驗證，請參閱[雲端事件的端點驗證](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="troubleshoot-event-subscription-validation"></a>針對事件訂閱驗證進行疑難排解

## <a name="next-steps"></a>後續步驟
如果您需要更多協助，請將您的問題張貼在 [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)，或開啟[支援票證](https://azure.microsoft.com/support/options/)。 
