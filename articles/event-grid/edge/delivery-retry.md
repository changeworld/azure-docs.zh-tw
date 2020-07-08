---
title: 傳遞和重試-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 IoT Edge 的事件方格中傳遞和重試。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841707"
---
# <a name="delivery-and-retry"></a>傳遞和重試

Event Grid 提供持久的傳遞。 它會嘗試立即為每個相符的訂用帳戶傳遞每個訊息一次。 如果訂閱者的端點未確認收到事件或發生失敗，事件方格會根據固定的**重試排程**和**重試原則**來重試傳遞。  根據預設，事件方格模組會一次傳遞一個事件給訂閱者。 但裝載是具有單一事件的陣列。 您可以藉由啟用輸出批次處理功能，讓模組一次傳遞一個以上的事件。 如需這項功能的詳細資訊，請參閱[輸出批次處理](delivery-output-batching.md)。  

> [!IMPORTANT]
>事件資料沒有持續性支援。 這表示重新部署或重新開機事件方格模組，將會導致您遺失尚未傳遞的任何事件。

## <a name="retry-schedule"></a>重試排程

事件方格在傳遞訊息之後，會等待最多60秒的回應。 如果訂閱者的端點未通知回應，則會將訊息加入其中一個後置佇列中，以供後續重試。

有兩個預先設定的反向佇列，可決定嘗試重試的排程。 其中包括：

| 排程 | Description |
| ---------| ------------ |
| 1 分鐘 | 每分鐘會嘗試在這裡結束的訊息。
| 10 分鐘 | 每隔10分鐘就會嘗試在這裡結束的訊息。

### <a name="how-it-works"></a>運作方式

1. 訊息抵達事件方格模組。 嘗試立即傳遞。
1. 如果傳遞失敗，則訊息會加入1分鐘的佇列，並在一分鐘後重試。
1. 如果傳遞持續失敗，則訊息會加入10分鐘的佇列中，並每隔10分鐘重試一次。
1. 會嘗試傳遞，直到達到成功或重試原則限制為止。

## <a name="retry-policy-limits"></a>重試原則限制

有兩個設定可決定重試原則。 其中包括：

* 嘗試次數上限
* 事件存留時間（TTL）

如果達到重試原則的其中一個限制，則會捨棄事件。 重試排程本身已在重試排程一節中說明。 這些限制的設定可以針對所有訂閱者或每個訂用帳戶來進行。 下一節會進一步詳細說明每一個。

## <a name="configuring-defaults-for-all-subscribers"></a>設定所有訂閱者的預設值

有兩個屬性： `brokers__defaultMaxDeliveryAttempts` 和 `broker__defaultEventTimeToLiveInSeconds` ，可以設定為事件方格部署的一部分，以控制所有訂閱者的重試原則預設值。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 傳遞事件的嘗試次數上限。 預設值：30。
| `broker__defaultEventTimeToLiveInSeconds` | 事件 TTL （以秒為單位），在此時間之後，如果未傳遞，就會捨棄事件。 預設值： **7200**秒

## <a name="configuring-defaults-per-subscriber"></a>設定每個訂閱者的預設值

您也可以根據每個訂用帳戶來指定重試原則限制。
如需如何為每個訂閱者設定預設值的相關資訊，請參閱我們的[API 檔](api.md)。 訂用帳戶層級的預設值會覆寫模組層級設定。

## <a name="examples"></a>範例

下列範例會在事件方格模組中設定重試原則，其 maxNumberOfAttempts = 3，事件 TTL 為30分鐘

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

下列範例會設定 maxNumberOfAttempts = 3 且事件 TTL 為30分鐘的 Web 勾點訂閱

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
