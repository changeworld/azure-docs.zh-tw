---
title: Azure 事件方格中的輸出批次處理 IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的輸出批次處理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171630"
---
# <a name="output-batching"></a>輸出批次處理

事件方格支援在單一傳遞要求中傳遞一個以上的事件。 這項功能可提高整體傳遞輸送量，而不需支付 HTTP 每個要求的負荷。 預設會關閉批次處理，每個訂用帳戶可以開啟批次處理。

> [!WARNING]
> 雖然訂閱者程式碼可能必須針對每個批次要求執行更多工作，但每個傳遞要求的最大允許持續時間不會變更。 傳遞超時的預設值為60秒。

## <a name="batching-policy"></a>批次處理原則

您可以調整下列兩個設定，以針對每個訂閱者自訂事件方格的批次處理行為：

* 每個批次的最大事件數

  這項設定會設定可新增至批次傳遞要求的事件數目上限。

* 慣用的批次大小（以 Kb 為單位）

  此旋鈕可用來進一步控制每個傳遞要求可以傳送的最大 kb 數

## <a name="batching-behavior"></a>批次處理行為

* 全部或無

  事件方格會以所有或無的語義運作。 它不支援批次傳遞的部分成功。 訂閱者應該小心只要求每個批次有多個事件，因為它們可在60秒內適當地處理。

* 開放式批次處理

  批次處理原則設定不是批次行為的嚴格界限，而且會以最佳方式進行。 以較低的事件費率，您通常會觀察到批次大小小於每個批次要求的最大事件數目。

* 預設值設為 OFF

  根據預設，事件方格只會在每個傳遞要求中新增一個事件。 開啟批次處理的方法是在事件訂用帳戶 JSON 的文章中，設定先前所述的其中一個設定。

* 預設值

  建立事件訂閱時，並不需要指定每個批次的最大事件數目，以及大約的批次大小（以 kb 為單位）) 的 (。 如果只設定一個設定，事件方格會使用 (可設定) 的預設值。 請參閱下列各節中的預設值，以及如何加以覆寫。

## <a name="turn-on-output-batching"></a>開啟輸出批次處理

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>設定允許的最大值

下列部署時間設定可控制建立事件訂閱時允許的最大值。

| 屬性名稱 | 描述 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | 旋鈕允許的最大值 `PreferredBatchSizeInKilobytes` 。 預設值 `1033` 。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | 旋鈕允許的最大值 `MaxEventsPerBatch` 。 預設值 `50` 。

## <a name="configuring-runtime-default-values"></a>設定執行時間預設值

當事件訂用帳戶未指定時，下列部署時間設定會控制每個旋鈕的執行時間預設值。 再次重申，事件訂用帳戶上至少必須設定一個旋鈕來開啟批次處理行為。

| 屬性名稱 | 描述 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 只有指定時的傳遞要求大小上限 `MaxEventsPerBatch` 。 預設值 `1_058_576` 。
| `broker__defaultMaxEventsPerBatch` | 只有在指定時要加入至批次的最大事件數目 `MaxBatchSizeInBytes` 。 預設值 `10` 。
