---
title: 串流內嵌輸送量限制-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Gen2 中的輸入輸送量限制。
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: seodec18
ms.openlocfilehash: c8be18049e6ae74a198f5885a46b70df581e0cd7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187454"
---
# <a name="streaming-ingestion-throughput-limits"></a>串流內嵌輸送量限制

Azure 時間序列深入解析 Gen2 串流資料輸入限制如下所述。

> [!TIP]
> 如需所有限制的完整清單，請參閱 [Azure 時間序列深入解析 Gen2 環境的方案](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) 。

## <a name="per-environment-limitations"></a>每個環境限制

一般來說，輸入速率會被視為您組織中的裝置數目、事件發出頻率，以及每個事件大小的因素：

* **裝置數目** × **事件發出頻率** × **每個事件的大小**。

根據預設，Azure 時間序列深入解析 Gen2 可以用每 **秒最多 1 mb 的速率來內嵌傳入資料 (MBps) 每個 Azure 時間序列深入解析 Gen2 環境**。 [每個中樞分割區](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)都有額外的限制。

> [!TIP]
>
> * 要求可以提供擷取速度最高可達 8 MBps 的環境支援。
> * 如果您需要透過 Azure 入口網站提交支援票證，請與我們聯絡。

* **範例 1：**

    Contoso Shipping 有 100,000 部裝置每分鐘發出事件三次。 事件的大小為 200 個位元組。 他們使用具有四個磁碟分割的 IoT 中樞做為 Azure 時間序列深入解析 Gen2 事件來源。

  * 其 Azure 時間序列深入解析 Gen2 環境的內嵌速率為： **100000 裝置 * 200 bytes/event * (3/60 事件/秒) = 1 MBps**。
    * 假設有平衡的資料分割，每個分割區的內嵌速率會是 0.25 MBps。
    * Contoso 運送的內嵌費率會在規模限制內。

* **範例 2：**

    Contoso 車隊分析有每秒發出事件的40000裝置。 它們會使用分割區計數為2的事件中樞做為 Azure 時間序列深入解析 Gen2 事件來源。 事件的大小為 200 個位元組。

  * 環境的內嵌速率為： **40000 裝置 * 200 bytes/event * 1 event/sec = 8 MBps**。
    * 假設有平衡的資料分割，則其每個資料分割速率會是 4 MBps。
    * Contoso Fleet Analytics 的擷取速率超過環境和分割區限制。 他們可以透過 Azure 入口網站提交要求 Azure 時間序列深入解析 Gen2，以增加其環境的內嵌速率，以及建立具有更多資料分割的事件中樞，以在限制範圍內。

## <a name="hub-partitions-and-per-partition-limits"></a>中樞分割區和每個分割區限制

規劃 Azure 時間序列深入解析 Gen2 環境時，請務必考慮您將連線到 Azure 時間序列深入解析 Gen2 的事件來源 (s) 。 Azure IoT 中樞和事件中樞都會利用分割區來啟用事件處理的水平調整。

*「分割區」* 是經過排序且保存在中樞內的事件序列。 分割區計數是在中樞建立階段期間設定的，而且無法變更。

如需事件中樞分割最佳做法，請參閱[我需要多少個分割區？](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

> [!NOTE]
> 使用 Azure 時間序列深入解析 Gen2 的大部分 IoT 中樞只需要四個磁碟分割。

無論您是針對 Azure 時間序列深入解析 Gen2 環境建立新的中樞，或使用現有的環境，都需要計算每個分割區的內嵌速率，以判斷其是否在限制範圍內。

Azure 時間序列深入解析 Gen2 目前有 **每個資料分割限制 0.5 MBps 的一般值**。

### <a name="iot-hub-specific-considerations"></a>IoT 中樞特定考量

在 IoT 中樞中建立裝置時，其會永久指派給分割區。 這樣做之後，IoT 中樞就能夠保證事件排序 (因為指派永遠不會變更)。

固定的資料分割指派也會影響擷取從 IoT 中樞下游傳送之資料的 Azure 時間序列深入解析 Gen2 實例。 當使用相同的閘道裝置識別碼將來自多個裝置的訊息轉送到中樞時，其可能會同時抵達相同的分割區，因而可能會超過每個分割區的規模限制。

**影響**：

* 如果單一分割區在超過限制的情況下有持續的速率，則在超過 IoT 中樞資料保留期限之前，Azure 時間序列深入解析 Gen2 可能不會同步所有裝置遙測。 因此，如果一致地超過擷取限制，傳送的資料可能會遺失。

為了減緩這種情況，建議您採用下列最佳做法：

* 在部署您的解決方案之前，請先計算每個環境和每個分割區的擷取速率。
* 確定您的 IoT 中樞裝置已負載平衡到最大的可能程度。

> [!IMPORTANT]
> 針對使用 IoT 中樞作為事件來源的環境，請使用使用中的中樞裝置數目來計算內建速率，以確定每個分割區限制的速率低於 0.5 MBps。
>
> * 即使有多個事件同時抵達，也不會超過此限制。

  ![IoT 中樞分割區圖表](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要深入瞭解如何最佳化中樞輸送量和分割區，請參閱下列資源：

* [IoT 中樞規模](../iot-hub/iot-hub-scaling.md)
* [事件中樞規模](../event-hubs/event-hubs-scalability.md#throughput-units)
* [事件中樞分割區](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>後續步驟

* 深入瞭解資料 [儲存體](./concepts-storage.md)