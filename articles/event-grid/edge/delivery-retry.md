---
title: 傳遞和重試 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 在 IoT 邊緣的事件網格中傳遞和重試。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841707"
---
# <a name="delivery-and-retry"></a>傳遞和重試

Event Grid 提供持久的傳遞。 它嘗試立即為每個匹配的訂閱至少傳遞一次每條消息。 如果訂戶的終結點不承認收到事件或失敗，事件網格會基於固定**重試計畫和****重試策略**重試傳遞。  預設情況下，事件網格模組一次向訂閱者提供一個事件。 但是，有效負載是具有單個事件的陣列。 通過啟用輸出批次處理功能，可以使模組一次提供多個事件。 有關此功能的詳細資訊，請參閱[輸出批次處理](delivery-output-batching.md)。  

> [!IMPORTANT]
>事件資料沒有持久性支援。 這意味著重新部署或重新開機事件網格模組將導致您丟失尚未傳遞的任何事件。

## <a name="retry-schedule"></a>重試計畫

事件網格在發送消息後最多等待 60 秒以等待回應。 如果訂閱者的終結點不確認回應，則消息將排隊在我們的一個 backoff 佇列中進行後續重試。

有兩個預配置的退退佇列，確定將嘗試重試的計畫。 其中包括：

| 排程 | 描述 |
| ---------| ------------ |
| 1 分鐘 | 最終在這裡的消息每分鐘都會嘗試。
| 10 分鐘 | 結束在這裡的消息每隔 10 分鐘嘗試一次。

### <a name="how-it-works"></a>運作方式

1. 消息到達事件網格模組。 嘗試立即交付它。
1. 如果傳遞失敗，則消息將排隊到 1 分鐘的佇列中，並在一分鐘後重試。
1. 如果傳遞繼續失敗，則消息將排隊到 10 分鐘的佇列中，每 10 分鐘重試一次。
1. 在成功或重試策略限制之前，將嘗試交付。

## <a name="retry-policy-limits"></a>重試策略限制

有兩種配置決定了重試策略。 其中包括：

* 最大嘗試次數
* 活動存留時間 （TTL）

如果達到重試策略的任一限制，將刪除事件。 重試計畫本身在"重試計畫"部分仲介紹。 可以對所有訂閱者或基於訂閱執行這些限制的配置。 以下部分將介紹每個部分的進一步詳細資訊。

## <a name="configuring-defaults-for-all-subscribers"></a>配置所有訂閱者的預設值

有兩個屬性：`brokers__defaultMaxDeliveryAttempts``broker__defaultEventTimeToLiveInSeconds`可以配置為事件網格部署的一部分，該部署控制所有訂閱者的重試策略預設值。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 傳遞事件的最大嘗試次數。 預設值：30。
| `broker__defaultEventTimeToLiveInSeconds` | 事件 TTL，在幾秒鐘內，如果未傳遞事件，將丟棄事件。 預設值： **7200**秒

## <a name="configuring-defaults-per-subscriber"></a>配置每個訂閱者的預設值

您還可以根據每個訂閱指定重試策略限制。
有關如何為每個訂閱者配置預設值的資訊，請參閱我們的[API 文檔](api.md)。 訂閱級預設值覆蓋模組層級別配置。

## <a name="examples"></a>範例

以下示例在事件網格模組中設置重試策略，最大嘗試次數 = 3 和事件 TTL 為 30 分鐘

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

以下示例設置最大嘗試次數 = 3 和事件 TTL 30 分鐘的 Web 掛鉤訂閱

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
