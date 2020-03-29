---
title: 在 Azure 事件網格 IoT 邊緣中的輸出批次處理 |微軟文檔
description: 在 IoT 邊緣的事件網格中輸出批次處理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841742"
---
# <a name="output-batching"></a>輸出批次處理

事件網格支援在單個傳遞請求中傳遞多個事件。 此功能無需支付每個請求的 HTTP 開銷即可提高總體交付輸送量。 批次處理預設情況下處于關閉狀態，並且可以按訂閱打開。

> [!WARNING]
> 處理每個傳遞請求的最長允許持續時間不會更改，即使訂閱者代碼可能必須執行每個批次處理請求的更多工作。 交貨超時預設為 60 秒。

## <a name="batching-policy"></a>批次處理策略

通過調整以下兩個設置，可以自訂每個訂閱者的事件網格的批次處理行為：

* 每個批次的最大事件數

  此設置設置可添加到批次處理傳遞請求的事件數的上限。

* 首選批次大小（以千位元組為單位）

  此旋鈕用於進一步控制每個傳遞請求可以發送的最大千位元組數

## <a name="batching-behavior"></a>批次處理行為

* 全部或無

  事件網格使用全或無語義運行。 它不支援批次處理交付的部分成功。 訂閱者應小心，每批只要求盡可能多的事件，因為他們可以在 60 秒內合理處理。

* 樂觀批次處理

  批次處理策略設置對批次處理行為不是嚴格限制，並且會盡力而為地遵守。 在低事件速率下，您通常會觀察到批次處理大小小於每個批次請求的最大事件。

* 預設值設置為"關閉"

  預設情況下，事件網格僅為每個傳遞請求添加一個事件。 打開批次處理的方法是在事件訂閱 JSON 中設置本文前面提到的任一設置。

* 預設值

  創建事件訂閱時，無需同時指定設置（每個批次處理的最大事件和以千位元組為單位的近似批次處理大小）。 如果僅設置一個設置，事件網格將使用（可配置）預設值。 有關預設值以及如何重寫這些值，請參閱以下部分。

## <a name="turn-on-output-batching"></a>打開輸出批次處理

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

## <a name="configuring-maximum-allowed-values"></a>配置允許的最大值

以下部署時間設置控制創建事件訂閱時允許的最大值。

| 屬性名稱 | 描述 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes`旋鈕允許的最大值。 預設值`1033`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch`旋鈕允許的最大值。 預設值`50`。

## <a name="configuring-runtime-default-values"></a>配置運行時預設值

在事件訂閱中未指定每個旋鈕的運行時預設值時，以下部署時間設置將控制該按鈕的運行時預設值。 要重申，必須在事件訂閱上設置至少一個旋鈕才能打開批次處理行為。

| 屬性名稱 | 描述 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 僅`MaxEventsPerBatch`指定時的最大傳遞請求大小。 預設值`1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 僅`MaxBatchSizeInBytes`指定時要添加到批次處理的最大事件數。 預設值`10`。
