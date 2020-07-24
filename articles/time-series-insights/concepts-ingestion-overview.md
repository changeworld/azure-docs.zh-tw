---
title: 內嵌總覽-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 深入瞭解 Azure 時間序列深入解析 Gen2 中的資料內嵌。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: 33cafd058e55951f7da4e925a603c2c442d4aed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077668"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure 時間序列深入解析 Gen2 資料內嵌總覽

您的 Azure 時間序列深入解析 Gen2 環境包含可收集、處理和儲存串流時間序列資料的內嵌*引擎*。 當資料抵達您的事件來源時，Azure 時間序列深入解析 Gen2 會以近乎即時的方式取用並儲存您的資料。

[![內嵌總覽](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>內嵌主題

下列文章涵蓋深入資料處理，包括要遵循的最佳作法：

* 閱讀有關[事件來源](./concepts-streaming-ingestion-event-sources.md)的資訊，以及有關選取事件來源時間戳記的指引。

* 檢查支援的[資料類型](./concepts-supported-data-types.md)

* 瞭解內嵌引擎如何將一組[規則](./concepts-json-flattening-escaping-rules.md)套用至您的 JSON 屬性，以建立您的儲存體帳戶資料行。

* 請檢查您的環境[輸送量限制](./concepts-streaming-ingress-throughput-limits.md)，以規劃您的調整需求。

## <a name="next-steps"></a>後續步驟

* 繼續閱讀以深入瞭解 Azure 時間序列深入解析 Gen2 環境的[事件來源](./concepts-streaming-ingestion-event-sources.md)。 
