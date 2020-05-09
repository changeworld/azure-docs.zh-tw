---
title: Azure 事件方格-疑難排解訂閱驗證
description: 本文會說明如何針對訂閱驗證進行疑難排解。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630183"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>針對 Azure 事件方格錯誤進行疑難排解
本疑難排解指南提供疑難排解事件訂閱驗證的相關資訊。 


## <a name="troubleshoot-event-subscription-validation"></a>針對事件訂閱驗證進行疑難排解

在事件訂閱建立期間，如果您看到如的錯誤訊息`The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`，則表示驗證交握失敗。 若要解決此錯誤，請執行以下幾方面的驗證：

- 透過使用 Postman 或捲曲或類似工具的[範例 SubscriptionValidationEvent](webhook-event-delivery.md#validation-details)要求本文，對您的 webhook URL 執行 HTTP POST。
- 如果您的 webhook 正在執行同步驗證交握機制，請確認 ValidationCode 會當做回應的一部分傳回。
- 如果您的 webhook 正在執行非同步驗證交握機制，請確認您是 HTTP POST 傳回200正常。
- 如果您的 webhook 在回應中傳回403（禁止），請檢查您的 webhook 是否位於 Azure 應用程式閘道或 Web 應用程式防火牆後方。 如果是，則您需要停用這些防火牆規則，然後再次執行 HTTP POST：

  920300（要求遺失 Accept 標頭，我們可以修正此問題）

  942430（受限制的 SQL 字元異常偵測（args）：已超過的特殊字元數（12））

  920230（偵測到多個 URL 編碼）

  942130（SQL 插入式攻擊：偵測到 SQL Tautology）。

  931130（可能是遠端檔案包含（RFI）攻擊 = Off-網域參考/連結）

> [!IMPORTANT]
> 如需 webhook 端點驗證的詳細資訊，請參閱[Webhook 事件傳遞](webhook-event-delivery.md)。

## <a name="validate-event-grid-event-subscription-using-postman"></a>使用 Postman 驗證事件方格事件訂用帳戶
以下是使用 Postman 來驗證事件方格事件之 webhook 訂用帳戶的範例： 

![使用 Postman 的事件方格事件訂用帳戶驗證](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

以下是 SubscriptionValidationEvent JSON 範例：

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

若要深入瞭解 webhook 的事件方格事件驗證，請參閱[使用事件方格事件的端點驗證](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="validate-cloud-event-subscription-using-postman"></a>使用 Postman 驗證雲端事件訂用帳戶
以下是使用 Postman 來驗證雲端事件 webhook 訂用帳戶的範例： 

![使用 Postman 進行雲端事件訂用帳戶驗證](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

使用**HTTP OPTIONS**方法進行雲端事件驗證。 若要深入瞭解 webhook 的雲端事件驗證，請參閱[使用雲端事件的端點驗證](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="event-grid-event-subscription-validation-using-curl"></a>使用捲曲的事件方格事件訂用帳戶驗證 
以下是用來驗證事件方格事件 webhook 訂用帳戶的範例捲曲命令： 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>後續步驟
如果您需要更多協助，請在[Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)中張貼您的問題，或開啟[支援票證](https://azure.microsoft.com/support/options/)。 
