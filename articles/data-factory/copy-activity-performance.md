---
title: 複製活動性能和可伸縮性指南
description: 瞭解在使用複製活動時影響 Azure 資料工廠中資料移動性能的關鍵因素。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261394"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>複製活動性能和可伸縮性指南

> [!div class="op_single_selector" title1="選擇正在使用的 Azure 資料工廠版本："]
> * [版本 1](v1/data-factory-copy-activity-performance.md)
> * [當前版本](copy-activity-performance.md)

無論您是要執行從資料湖或企業資料倉儲 （EDW） 到 Azure 的大規模資料移轉，還是希望將資料從不同源大規模引入 Azure 以進行大資料分析，實現最佳性能和可 伸縮 性。  Azure 資料工廠提供了一種性能、彈性且經濟高效的機制，用於大規模引入資料，非常適合希望構建性能高且可擴展的資料引入管道的資料工程師。

閱讀本文後，您將能夠回答下列問題：

- 使用 ADF 複製活動進行資料移轉和資料引入方案，我可以達到什麼級別的性能和可擴充性？

- 我應該採取哪些步驟來調整 ADF 複製活動的性能？
- 我可以利用什麼 ADF perf 優化旋鈕來優化單個複製活動運行的性能？
- 在優化複製性能時，除 ADF 之外需要考慮哪些其他因素？

> [!NOTE]
> 如果您通常不熟悉複製活動，請參閱閱讀本文之前[的副本活動概述](copy-activity-overview.md)。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>使用 ADF 可實現的複製性能和可擴充性

ADF 提供一種無伺服器架構，允許在不同級別上並行化，允許開發人員構建管道，以充分利用您的網路頻寬以及存儲 IOPS 和頻寬，從而最大限度地提高環境的資料移動輸送量。  這意味著可以通過測量來源資料存儲、目標資料存儲和源和目標之間的網路頻寬提供的最低輸送量來估計您所能實現的輸送量。  下表根據資料大小和環境的頻寬限制計算複製持續時間。 

| 資料大小 / <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分鐘    | 1.4 分鐘   | 0.3 分鐘   | 0.1 分鐘  | 0.03 分鐘 | 0.01 分 | 0.0 分鐘   |
| **10 GB**                   | 27.3 分鐘   | 13.7 分鐘  | 2.7 分鐘   | 1.3 分鐘  | 0.3 分鐘  | 0.1 分鐘  | 0.03 分鐘  |
| **100 GB**                  | 4.6 小時    | 2.3 小時   | 0.5 小時   | 0.2 小時  | 0.05 小時 | 0.02 小時 | 0.0 小時   |
| **1 TB**                    | 46.6 小時   | 23.3 小時  | 4.7 小時   | 2.3 小時  | 0.5 小時  | 0.2 小時  | 0.05 小時  |
| **10 TB**                   | 19.4 天  | 9.7 天  | 1.9 天  | 0.9 天 | 0.2 天 | 0.1 天 | 0.02 天 |
| **100 TB**                  | 194.2 天 | 97.1 天 | 19.4 天 | 9.7 天 | 1.9 天 | 1 日    | 0.2 天  |
| **1 PB**                    | 64.7 月    | 32.4 月   | 6.5 月    | 3.2 月   | 0.6 月   | 0.3 月   | 0.06 月   |
| **10 PB**                   | 647.3 月   | 323.6 月  | 64.7 月   | 31.6 月  | 6.5 月   | 3.2 月   | 0.6 月    |

ADF 副本可擴展到不同級別：

![ADF 副本如何縮放](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 控制流可以並行啟動多個複製活動，例如使用[For each 迴圈](control-flow-for-each-activity.md)。
- 單個複製活動可以利用可擴展的計算資源：在使用 Azure 集成運行時時，您可以以無伺服器方式為每個複製活動指定[多達 256 個 DIA;](#data-integration-units)使用自託管集成運行時時，可以手動擴展電腦或擴展到多台電腦（[最多 4 個節點](create-self-hosted-integration-runtime.md#high-availability-and-scalability)），單個複製活動將跨所有節點對其檔集進行分區。
- 單個複製活動使用[並行的](#parallel-copy)多個執行緒從和寫入資料存儲。

## <a name="performance-tuning-steps"></a>效能微調步驟

執行這些步驟以使用複製活動調整 Azure 資料工廠服務的性能。

1. **拾取測試資料集並建立基線。** 在開發階段，使用具有代表性的資料示例的複製活動來測試管道。 您選擇的資料集應表示典型的資料模式（資料夾結構、檔案模式、資料架構等），並且足夠大，足以評估複製性能，例如，完成複製活動需要 10 分鐘或更長時間。 在[複製活動監視](copy-activity-monitoring.md)後收集執行詳細資訊和性能特徵。

2. **如何最大限度地提高單個複製活動的性能**：

   首先，我們建議您首先使用單個複製活動最大限度地提高性能。

   - **如果在 Azure 集成運行時執行複製活動：** 從[資料整合單元 （DIU）](#data-integration-units)和[並行複製](#parallel-copy)設置的預設值開始。 

   - **如果在自託管的集成運行時執行複製活動：** 我們建議您使用獨立于託管資料存儲的伺服器的專用電腦來承載集成運行時。 從[並行複製](#parallel-copy)設置的預設值開始，並使用自承載 IR 的單個節點。  

   執行效能測試運行，並記下已實現的性能以及使用的實際值（如 DIA 和並行副本）。 有關如何收集使用的運行結果和性能設置的[複製活動監視](copy-activity-monitoring.md)，並瞭解如何[對複製活動性能進行故障排除](copy-activity-performance-troubleshooting.md)，以識別和解決瓶頸。 

   在故障排除和調優指導之後，反覆運算以執行其他效能測試運行。 一旦單個複製活動運行無法實現更好的輸送量，請考慮通過同時引用步驟 3 運行多個副本來最大化聚合輸送量。


3. **如何通過同時運行多個副本來最大化聚合輸送量：**

   現在，您已經最大化了單個複製活動的性能，如果您尚未達到環境的輸送量上限（網路、來源資料存儲和目標資料存儲），則可以使用 ADF 控制流構造（如[For Each 迴圈](control-flow-for-each-activity.md)）並行運行多個複製活動。 請參閱[從多個容器複製檔](solution-template-copy-files-multiple-containers.md)、[將資料從 Amazon S3 遷移到 ADLS Gen2，](solution-template-migration-s3-azure.md)或[使用控制表](solution-template-bulk-copy-with-control-table.md)解決方案範本進行批量複製作為一般示例。

5. **將配置擴展到整個資料集。** 當您對執行結果和性能感到滿意時，可以展開定義和管道以覆蓋整個資料集。

## <a name="troubleshoot-copy-activity-performance"></a>排除複製活動性能的故障

按照[性能調優步驟](#performance-tuning-steps)為方案規劃和執行效能測試。 瞭解如何從["疑難排解複製活動性能"](copy-activity-performance-troubleshooting.md)中解決 Azure 資料工廠中每個複製活動運行的性能問題。

## <a name="copy-performance-optimization-features"></a>複製性能優化功能

Azure 資料工廠提供以下性能優化功能：

- [資料整合單位](#data-integration-units)
- [自託管集成運行時可擴充性](#self-hosted-integration-runtime-scalability)
- [平行複製](#parallel-copy)
- [分段複製](#staged-copy)

### <a name="data-integration-units"></a>資料整合單位

資料整合單元是表示 Azure 資料工廠中單個單元的功率（CPU、記憶體和網路資源分配的組合）的度量值。 資料整合單元僅適用于[Azure 集成運行時](concepts-integration-runtime.md#azure-integration-runtime)，但不應用於[自託管的集成運行時](concepts-integration-runtime.md#self-hosted-integration-runtime)。 [深入了解](copy-activity-performance-features.md#data-integration-units)。

### <a name="self-hosted-integration-runtime-scalability"></a>自託管集成運行時可擴充性

要承載不斷增加的併發工作負載或實現更高的性能，可以向上擴展或擴展自託管的集成運行時。 [深入了解](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)。

### <a name="parallel-copy"></a>平行複製

您可以設置並行複製以指示您希望複製活動使用的並行性。 可以將此屬性視為複製活動中從源讀取或並行寫入接收器資料存儲的最大執行緒數。 [深入了解](copy-activity-performance-features.md#parallel-copy)。

### <a name="staged-copy"></a>分段複製

從來源資料存放區將資料複製到接收資料存放區時，您可以選擇使用 Blob 儲存體做為過渡暫存存放區。 [深入了解](copy-activity-performance-features.md#staged-copy)。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概述](copy-activity-overview.md)
- [排除複製活動性能的故障](copy-activity-performance-troubleshooting.md)
- [複製活動性能優化功能](copy-activity-performance-features.md)
- [使用 Azure 資料工廠將資料從資料湖或資料倉儲遷移到 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)
