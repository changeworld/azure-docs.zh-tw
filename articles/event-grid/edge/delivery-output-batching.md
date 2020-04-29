---
title: Azure 事件方格中的輸出批次處理 IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的輸出批次處理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841742"
---
# <a name="output-batching"></a>輸出批次處理

事件方格支援在單一傳遞要求中傳遞一個以上的事件。 這項功能可讓您增加整體傳遞輸送量，而不需支付 HTTP 每個要求的負荷。 批次處理預設為關閉，而且可以針對每個訂用帳戶開啟。

> [!WARNING]
> 即使訂閱者程式碼可能必須針對每個批次要求執行更多工作，但處理每個傳遞要求的最大持續期間也不會變更。 傳遞超時預設為60秒。

## <a name="batching-policy"></a>批次處理原則

藉由調整下列兩項設定，可以針對每個訂閱者自訂事件方格的批次處理行為：

* 每批次的事件數上限

  此設定會設定可新增至批次傳遞要求的事件數目上限。

* 慣用的批次大小（以 Kb 為單位）

  此旋鈕可用來進一步控制每個傳遞要求可以傳送的最大 kb 數

## <a name="batching-behavior"></a>批次處理行為

* 全部或無

  事件方格會使用 all 或-none 語義來運作。 它不支援批次傳遞的部分成功。 訂閱者應該謹慎地要求每個批次有多個事件，因為它們可以在60秒內合理地處理。

* 開放式批次處理

  批次處理原則設定不會嚴格限制批次處理行為，並會盡力遵循。 在事件速率較低的情況下，您通常會發現批次大小小於每個批次要求的最大事件數。

* 預設值設定為 OFF

  根據預設，事件方格只會將一個事件新增至每個傳遞要求。 開啟批次處理的方式是在事件訂用帳戶 JSON 中，設定前述文章中所述的其中一項設定。

* 預設值

  建立事件訂用帳戶時，不需要指定這兩個設定（每個批次的最大事件和大約個位元組的批次大小）。 如果只設定一個設定，事件方格會使用（可設定）的預設值。 請參閱下列各節中的預設值，以及如何覆寫它們。

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

下列部署時間設定會控制建立事件訂閱時允許的最大值。

| 屬性名稱 | 描述 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes`旋鈕允許的最大值。 預設`1033`值。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch`旋鈕允許的最大值。 預設`50`值。

## <a name="configuring-runtime-default-values"></a>設定執行時間預設值

下列部署時間設定會控制每個旋鈕在事件訂用帳戶中未指定時的預設值。 重申一次，必須在事件訂用帳戶上設定至少一個旋鈕，才能開啟批次行為。

| 屬性名稱 | 描述 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 只有`MaxEventsPerBatch`在指定時，傳遞要求大小上限。 預設`1_058_576`值。
| `broker__defaultMaxEventsPerBatch` | 只有在指定時，才`MaxBatchSizeInBytes`會新增至批次的事件數目上限。 預設`10`值。
