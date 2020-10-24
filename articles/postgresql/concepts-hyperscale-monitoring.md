---
title: 監視和微調-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: '本文說明適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus 中的監視和微調功能) '
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 30be9cbba7fe702d12285fe13794290c4820fd53
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487988"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>監視和微調適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus) 

監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 超大規模 (Citus) 提供各種監視選項，可讓您深入瞭解伺服器群組中節點的行為。

## <a name="metrics"></a>計量

超大規模 (Citus) 為伺服器群組中的每個節點提供度量。 這些計量讓您深入瞭解支援資源的行為。 每個計量都會以一分鐘的頻率發出，而且可保留最多 30 天的歷程記錄。

除了查看計量的圖表之外，您還可以設定警示。 如需逐步指引，請參閱[如何設定警示](howto-hyperscale-alert-on-metric.md)。  其他工作包括設定自動化動作、執行 advanced analytics，以及封存歷程記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../azure-monitor/platform/data-platform.md)。

### <a name="list-of-metrics"></a>計量清單

這些計量適用于超大規模 (Citus) 節點：

|計量|計量顯示名稱|Unit|說明|
|---|---|---|---|
|active_connections|作用中的連線|Count|伺服器的使用中連線數量。|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|iops|IOPS|Count|請參閱 [IOPS 定義](../virtual-machines/premium-storage-performance.md#iops) 和 [超大規模 (Citus) 輸送量](concepts-hyperscale-configuration-options.md)|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|network_bytes_ingress|Network In|位元組|跨作用中連線的網路輸入。|
|network_bytes_egress|Network Out|位元組|跨作用中連線的網路輸出。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|已使用儲存體|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|

Azure 不提供整個叢集的匯總計量，但多個節點的計量可放置在相同的圖表上。

## <a name="next-steps"></a>後續步驟

- 瞭解 [如何設定警示](howto-hyperscale-alert-on-metric.md) ，以取得有關在計量上建立警示的指引。