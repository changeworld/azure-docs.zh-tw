---
title: 串流處理內嵌輸送量限制-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 深入瞭解 Azure 時間序列深入解析 Gen2 中的輸入輸送量限制。
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: fb10effce8b94a6443e1daa8dadaa99111da0d4e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097311"
---
# <a name="streaming-ingestion-throughput-limits"></a>串流處理內嵌輸送量限制

Azure 時間序列深入解析 Gen2 串流資料輸入限制的說明如下。

> [!TIP]
> 如需所有限制的完整清單，請參閱[規劃您的 Azure 時間序列深入解析 Gen2 環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)。

## <a name="per-environment-limitations"></a>每個環境限制

一般來說，輸入速率會被視為您組織中的裝置數目、事件發出頻率，以及每個事件大小的因素：

*  **裝置數目** × **事件發出頻率** × **每個事件的大小**。

根據預設，Azure 時間序列深入解析 Gen2 可在**每個 Azure 時間序列深入解析 Gen2 環境中，以最多每秒 1 mb （MBps）** 的速率內嵌傳入資料。 [每個中樞分割區](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)都有額外的限制。

> [!TIP]
>
> * 要求可以提供擷取速度最高可達 16 MBps 的環境支援。
> * 如果您需要更高的輸送量，請透過 Azure 入口網站提交支援票證來連絡我們。
 
* **範例 1：**

    Contoso Shipping 有 100,000 部裝置每分鐘發出事件三次。 事件的大小為 200 個位元組。 它們會使用具有四個磁碟分割的 IoT 中樞做為 Azure 時間序列深入解析 Gen2 事件來源。

    * 其 Azure 時間序列深入解析 Gen2 環境的內嵌速率為： **100000 裝置 * 200 位元組/事件 * （3/60 事件/秒） = 1 MBps**。
    * 每個分割區的擷取速率將為 0.25 MBps。
    * Contoso 運送的內嵌費率會在規模限制範圍內。

* **範例 2：**

    Contoso Fleet Analytics 有 60,000 部裝置每秒發出一個事件。 它們使用的事件中樞分割區計數為4，做為 Azure 時間序列深入解析 Gen2 事件來源。 事件的大小為 200 個位元組。

    * 環境擷取速率將為：**60,000 部裝置 * 200 個位元組/事件 * 1 個事件/秒 = 12 MBps**。
    * 每個分割區速率將為 3 MBps。
    * Contoso Fleet Analytics 的擷取速率超過環境和分割區限制。 他們可以透過 Azure 入口網站將要求提交至 Azure 時間序列深入解析 Gen2，以增加其環境的內送速率，並建立一個事件中樞，其中具有更多分割區可在限制範圍內。

## <a name="hub-partitions-and-per-partition-limits"></a>中樞分割區和每個分割區限制

在規劃您的 Azure 時間序列深入解析 Gen2 環境時，請務必考慮您將連接到 Azure 時間序列深入解析 Gen2 的事件來源設定。 Azure IoT 中樞和事件中樞都會利用分割區來啟用事件處理的水平調整。 

*「分割區」* 是經過排序且保存在中樞內的事件序列。 分割區計數是在中樞建立階段期間設定的，而且無法變更。

如需事件中樞分割最佳做法，請參閱[我需要多少個分割區？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 大部分與 Azure 時間序列深入解析 Gen2 搭配使用的 IoT 中樞只需要四個磁碟分割。

無論您是為 Azure 時間序列深入解析 Gen2 環境建立新的中樞，還是要使用現有的集線器，都需要計算每個分割區的內嵌速率，以判斷它是否在限制內。 

Azure 時間序列深入解析 Gen2 目前有**每個分割區限制 0.5 MBps 的**一般。

### <a name="iot-hub-specific-considerations"></a>IoT 中樞特定考量

在 IoT 中樞中建立裝置時，其會永久指派給分割區。 這樣做之後，IoT 中樞就能夠保證事件排序 (因為指派永遠不會變更)。

固定的分割區指派也會影響內嵌從下游傳送 IoT 中樞之資料的 Azure 時間序列深入解析 Gen2 實例。 當使用相同的閘道裝置識別碼將來自多個裝置的訊息轉送到中樞時，其可能會同時抵達相同的分割區，因而可能會超過每個分割區的規模限制。

**影響**：

* 如果單一分割區發生超過限制的持續速率，則在超過 IoT 中樞的資料保留期間之前，Azure 時間序列深入解析 Gen2 可能不會同步所有裝置遙測。 因此，如果一致地超過擷取限制，傳送的資料可能會遺失。

為了減緩這種情況，建議您採用下列最佳做法：

* 在部署您的解決方案之前，請先計算每個環境和每個分割區的擷取速率。
* 確定您的 IoT 中樞裝置已負載平衡到最大的可能程度。

> [!IMPORTANT]
> 針對使用 IoT 中樞作為事件來源的環境，使用使用中的中樞裝置數目來計算內建速率，以確保速率低於 0.5 MBps 的每個分割區限制。
>
> * 即使有數個事件同時抵達，也不會超過限制。

  ![IoT 中樞分割區圖表](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要深入瞭解如何最佳化中樞輸送量和分割區，請參閱下列資源：

* [IoT 中樞規模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中樞規模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中樞分割區](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>後續步驟

* 閱讀資料[儲存體](./concepts-storage.md)的相關資訊