---
title: Azure 事件方格 - 針對訂閱驗證進行疑難排解
description: 本文說明如何針對訂閱驗證進行疑難排解。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48844859013507ab684ef8879b7b85dd6b6fe8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118982"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>針對 Azure 事件方格的訂閱驗證進行疑難排解
本文提供針對事件訂閱驗證進行疑難排解的相關資訊。 

> [!IMPORTANT]
> 如需 Webhook 端點驗證的詳細資訊，請參閱 [Webhook 事件傳遞](webhook-event-delivery.md)。

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

使用 **HTTP 選項**方法進行雲端事件驗證。 若要深入瞭解 Webhook 的雲端事件驗證，請參閱[雲端事件的端點驗證](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="error-code-403"></a>錯誤碼：403
如果您的 Webhook 在回應中傳回 403 (禁止)，請檢查您的 Webhook 是否位於 Azure 應用程式閘道或 Web 應用程式防火牆後方。 如果是，您必須停用下列防火牆規則，然後再次執行 HTTP POST：

  - 920300 (要求中遺漏 Accept 標頭，我們可以修正此問題)
  - 942430 (受限制的 SQL 字元異常偵測 (args): 超出的特殊字元數目 (12))
  - 920230 (偵測到多個 URL 編碼)
  - 942130 (SQL 插入式攻擊:檢測到 SQL 恆真式。)
  - 931130 (可能的遠端檔案包含 (RFI) 攻擊 = 關閉網域參考/連結)

## <a name="next-steps"></a>後續步驟
如果您需要更多協助，請將您的問題張貼在 [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)，或開啟[支援票證](https://azure.microsoft.com/support/options/)。 
