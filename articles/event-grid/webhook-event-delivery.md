---
title: WebHook 事件傳遞
description: 本文說明使用 Webhook 時的 WebHook 事件傳遞和端點驗證。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b616c82092ad896da82d0f98f37e3c45f3ba1014
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118948"
---
# <a name="webhook-event-delivery"></a>WebHook 事件傳遞
Webhook 是從 Azure 事件方格接收事件的眾多方法之一。 當新事件準備就緒時，「事件方格」服務就會透過 POST 對所設定的端點發佈 HTTP 要求，並在要求本文內夾帶該事件。

和許多其他支援 Webhook 的服務一樣，「事件方格」也會先要求您證明具有 Webhook 端點的「擁有權」，然後才開始將事件傳遞給該端點。 此需求可避免惡意使用者利用事件癱瘓您的端點。 當您使用下列三種 Azure 服務的任一種時，Azure 基礎結構將會自動處理這項驗證：

- Azure Logic Apps，使用[事件格線連接器](https://docs.microsoft.com/connectors/azureeventgrid/)進行驗證
- Azure 自動化，透過 [Webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) 進行驗證
- Azure Functions，使用[事件格線觸發程序](../azure-functions/functions-bindings-event-grid.md)進行驗證

## <a name="endpoint-validation-with-event-grid-events"></a>使用事件方格事件進行端點驗證
如果您使用任何其他類型的端點 (例如以 HTTP 觸發程序為基礎的 Azure 函式)，則您的端點程式碼必須參與和「事件方格」的驗證交握。 「事件方格」支援兩種驗證訂用帳戶的方式。

1. **同步交握**：建立事件訂閱時，「事件方格」會傳送一個訂閱驗證事件給您的端點。 此事件的結構描述類似於任何其他「事件方格」事件。 此事件的資料部分包含 `validationCode` 屬性。 您的應用程式會確認該驗證要求是針對預期的事件訂閱，然後以同步方式在回應中傳回驗證碼。 所有「事件方格」版本都支援此交握機制。

2. **非同步交握**：在某些情況下，您無法在回應中同步傳回 ValidationCode。 例如，如果您使用第三方服務 (例如 [`Zapier`](https://zapier.com) 或 [IFTTT](https://ifttt.com/))，就無法透過程式設計方式以驗證碼回應。

   從 2018-05-01-preview 版開始，「事件方格」支援手動驗證交握。 如果您是以採用 API 2018-05-01-preview 版或更新版本的 SDK 或工具來建立事件訂閱，「事件方格」就會在訂閱驗證事件的資料部分中一併傳送 `validationUrl` 屬性。 若要完成交握，請在事件資料中找出該 URL，然後對其提出 GET 要求。 您可以使用 REST 用戶端或您的網頁瀏覽器。

   所提供 URL 的有效時間為 **5 分鐘**。 在那段時間，事件訂閱的佈建狀態為 `AwaitingManualAction`。 如果您未在 5 分鐘內完成手動驗證，則會將佈建狀態設為 `Failed`。 您必須再次建立事件訂閱，才能開始進行手動驗證。

   此驗證機制也需要 Webhook 端點傳回 HTTP 狀態碼 200，以便其得知已先接受驗證事件的 POST，才能進入手動驗證模式。 換句話說，如果端點傳回 200，但並未同步傳回驗證回應，則模式會轉換成手動驗證模式。 如果在 5 分鐘內驗證 URL 上出現 GET，則會將驗證交握視為成功。

> [!NOTE]
> 不支援使用自我簽署憑證進行驗證。 請改用商業憑證授權單位 (CA) 所簽署的憑證。

### <a name="validation-details"></a>驗證詳細資料

- 建立/更新事件訂閱時，「事件方格」會將訂閱驗證事件發佈至目標端點。
- 此事件會包含一個標頭值 "aeg-event-type:SubscriptionValidation"。
- 此事件主體之結構描述與其他 Event Grid 事件相同。
- 事件的 eventType 屬性是 `Microsoft.EventGrid.SubscriptionValidationEvent`。
- 事件的資料屬性包含 `validationCode` 屬性，內含隨機產生的字串。 例如，"validationCode: acb13…"。
- 事件資料也包含具有 URL 的 `validationUrl` 屬性，可供手動驗證訂用帳戶。
- 此陣列只包含驗證事件。 在您回應驗證程式碼之後，其他事件會在不同的要求中傳送。
- EventGrid DataPlane SDK 具有對應於訂閱驗證事件資料和訂閱驗證回應的類別。

下列範例顯示 SubscriptionValidationEvent 的範例：

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

如需證明端點擁有權，請在 validationResponse 內容中回應驗證代碼，如下列範例所示：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

您必須傳回「HTTP 200 正常」回應狀態碼。 「HTTP 202 已接受」無法辨識為有效的「事件方格」訂閱驗證回應。 此 http 要求必須在 30 秒內完成。 如果作業未在 30 秒內完成，則會取消作業，而且可能會在 5 秒後重新嘗試。 如果所有嘗試都失敗，則會將其視為驗證交握錯誤。

或者，您可以藉由將 GET 要求傳送至驗證 URL，以手動方式驗證訂用帳戶。 事件訂用帳戶會保持擱置狀態，直到通過驗證為止。 驗證 Url 會使用連接埠 553。 如果防火牆規則封鎖了連接埠 553，則可能需要更新規則，才能進行成功的手動交握。

如需有關處理訂閱驗證交握的範例，請參閱 [C# 範例](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs) \(英文\)。

## <a name="endpoint-validation-with-cloudevents-v10"></a>使用 CloudEvents v1.0 進行端點驗證
如果您已經熟悉事件方格，則可能會注意到事件方格的端點驗證交握可防止濫用。 CloudEvents v1.0 會使用 HTTP OPTIONS 方法，實作自己的[濫用保護語義](webhook-event-delivery.md)。 您可以在 [此處](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)閱讀相關資訊。 使用 CloudEvents 結構描述進行輸出時，事件方格會使用 CloudEvents v1.0 濫用保護來取代事件方格驗證事件機制。

## <a name="next-steps"></a>後續步驟
請參閱下列文章，了解如何針對事件訂用帳戶驗證進行疑難排解： 

[針對事件訂用帳戶驗證進行疑難排解](troubleshoot-subscription-validation.md)
