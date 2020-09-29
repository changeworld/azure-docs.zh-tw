---
title: 內嵌總覽-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解如何將資料內嵌至 Azure 時間序列深入解析 Gen2。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: 57c23ba4acdbde1a5dfac39d89a09dfcef6b25a1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460895"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure 時間序列深入解析 Gen2 資料擷取概觀

您的 Azure 時間序列深入解析 Gen2 環境包含可收集、處理和儲存串流時間序列資料的內嵌 *引擎* 。 當資料抵達您的事件來源 (s) 時，Azure 時間序列深入解析 Gen2 會近乎即時地取用和儲存您的資料。

[![擷取概觀](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>內嵌主題

下列文章涵蓋深入的資料處理，包括要遵循的最佳作法：

* 閱讀有關 [事件來源](./concepts-streaming-ingestion-event-sources.md) 的資訊，以及有關選取事件來源時間戳記的指引。

* 檢查支援的 [資料類型](./concepts-supported-data-types.md)

* 瞭解內嵌引擎會如何將一組 [規則](./concepts-json-flattening-escaping-rules.md) 套用至您的 JSON 屬性，以建立您的儲存體帳戶資料行。

* 請審查您的環境 [輸送量限制](./concepts-streaming-ingress-throughput-limits.md) ，以規劃您的調整需求。

## <a name="next-steps"></a>後續步驟

* 繼續閱讀以深入瞭解 Azure 時間序列深入解析 Gen2 環境的 [事件來源](./concepts-streaming-ingestion-event-sources.md) 。
