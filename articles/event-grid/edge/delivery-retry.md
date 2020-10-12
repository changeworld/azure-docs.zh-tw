---
title: 傳遞和重試-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件方格中傳遞和重試。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171613"
---
# <a name="delivery-and-retry"></a>傳遞和重試

Event Grid 提供持久的傳遞。 它會嘗試立即針對每個相符的訂用帳戶傳遞每則訊息一次。 如果訂閱者的端點未認可事件的接收或發生錯誤，事件方格會根據固定的 **重試排程** 和 **重試原則**來重試傳遞。  根據預設，事件方格模組會將一個事件一次傳遞給「訂閱者」。 但承載是具有單一事件的陣列。 您可以藉由啟用輸出批次處理功能，讓模組一次傳遞一個以上的事件。 如需這項功能的詳細資訊，請參閱 [輸出批次處理](delivery-output-batching.md)。  

> [!IMPORTANT]
>沒有事件資料的持續性支援。 這表示重新部署或重新開機事件方格模組會導致您遺失任何尚未傳遞的事件。

## <a name="retry-schedule"></a>重試排程

事件方格會在傳遞訊息之後等候最多60秒的回應。 如果訂閱者的端點未通知回應，則會將訊息排入其中一個回復佇列中，以供後續重試。

有兩個預先設定的後端佇列，可決定將嘗試重試的排程。 其中包括：

| 排程 | 說明 |
| ---------| ------------ |
| 1 分鐘 | 每分鐘會嘗試一次結束的訊息。
| 10 分鐘 | 每隔10分鐘會嘗試一次結束的訊息。

### <a name="how-it-works"></a>運作方式

1. 訊息抵達事件方格模組。 嘗試立即進行傳遞。
1. 如果傳遞失敗，訊息會排入1分鐘的佇列，並在一分鐘後重試。
1. 如果傳遞持續失敗，訊息會排入10分鐘的佇列，並每隔10分鐘重試一次。
1. 在達到成功或重試原則限制之前，會嘗試傳遞。

## <a name="retry-policy-limits"></a>重試原則限制

有兩個設定會決定重試原則。 其中包括：

* 嘗試次數上限
* 事件存留時間 (TTL) 

如果達到重試原則的任何限制，就會卸載事件。 重試排程本身會在 [重試排程] 區段中描述。 您可以針對所有訂閱者或依訂用帳戶來設定這些限制。 下一節會詳細說明每一個。

## <a name="configuring-defaults-for-all-subscribers"></a>設定所有訂閱者的預設值

有兩個屬性： `brokers__defaultMaxDeliveryAttempts` 和 `broker__defaultEventTimeToLiveInSeconds` 可以設定為事件方格部署的一部分，以控制所有訂閱者的重試原則預設值。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 嘗試傳遞事件的次數上限。 預設值：30。
| `broker__defaultEventTimeToLiveInSeconds` | 事件 TTL （以秒為單位），如果未傳遞，就會捨棄事件。 預設值： **7200** 秒

## <a name="configuring-defaults-per-subscriber"></a>設定每個訂閱者的預設值

您也可以針對每個訂用帳戶指定重試原則限制。
如需如何針對每個訂閱者設定預設值的詳細資訊，請參閱我們的 [API 檔](api.md) 。 訂用帳戶層級預設值會覆寫模組層級設定。

## <a name="examples"></a>範例

下列範例會在事件方格模組中設定 maxNumberOfAttempts = 3 的重試原則和30分鐘的事件 TTL

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

下列範例會設定 maxNumberOfAttempts = 3 的 webhook 訂用帳戶，以及30分鐘的事件 TTL

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
