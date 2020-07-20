---
title: 內嵌總覽-Azure 時間序列深入解析 |Microsoft Docs
description: 深入瞭解 Azure 時間序列深入解析中的資料內嵌。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049839"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Azure 時間序列深入解析資料內嵌總覽

您的 Azure 時間序列深入解析環境包含可收集、處理和儲存串流時間序列資料的內嵌*引擎*。 當資料抵達您的事件來源時，Azure 時間序列深入解析會以近乎即時的方式取用並儲存您的資料。

[![內嵌總覽](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>內嵌主題

下列文章涵蓋深入資料處理，包括要遵循的最佳作法：

* 閱讀有關[事件來源](concepts-streaming-ingestion-event-sources.md)的資訊，以及有關選取事件來源時間戳記的指引。

* 檢查支援的[資料類型](concepts-supported-data-types.md)

* 瞭解內嵌引擎如何將一組[規則](./concepts-json-flattening-escaping-rules.md)套用至您的 JSON 屬性，以建立您的儲存體帳戶資料行。

* 請檢查您的環境[輸送量限制](concepts-streaming-throughput-limitations.md)，以規劃您的調整需求。

## <a name="next-steps"></a>後續步驟

* 繼續閱讀以深入瞭解 Azure 時間序列深入解析環境的[事件來源](concepts-streaming-ingestion-event-sources.md)。 
