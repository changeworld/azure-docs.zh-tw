---
title: Azure HDInsight 支援的節點配置
description: 瞭解 HDInsight 叢集節點的最小和推薦配置。
keywords: vm 大小、群集大小、群集配置
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484781"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight 的預設和推薦的節點配置是什麼？

本文討論 Azure HDInsight 群集的預設和推薦的節點配置。

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>群集的預設和最小建議的節點配置和虛擬機器大小

下表列出了 HDInsight 群集的預設和建議的虛擬機器 （VM） 大小。  此資訊對於瞭解在創建 PowerShell 或 Azure CLI 腳本以部署 HDInsight 群集時要使用的 VM 大小是必需的。

如果群集中需要 32 個以上輔助節點，請選擇具有至少 8 個內核和 14 GB RAM 的頭節點大小。 

只有具有資料磁片的群集類型是啟用了加速寫入功能的 Kafka 和 HBase 群集。 HDInsight 在這些情況下支援 P30 和 S30 磁片大小。

本文檔中使用的所有最小建議 VM 類型的規範都匯總在下表中。

| 大小              | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟 / 輸送量︰IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16 x 500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32 x 500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64 x 500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

有關每種 VM 類型的規範的更多詳細資訊，請參閱以下文檔：

* [通用虛擬機器尺寸：Dv2 系列 1-5](../virtual-machines/dv2-dsv2-series.md)
* [記憶體優化的虛擬機器尺寸：Dv2 系列 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [通用虛擬機器尺寸：Av2 系列 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>除巴西南部和日本西部外，所有受支援的地區

> [!Note]
> 要獲取用於電源殼和其他腳本的 SKU 識別碼，請使用`Standard_`下表中的所有 VM SKU 的開頭。 例如，`D12_v2`將變為`Standard_D12_v2`。

| 叢集類型 | Hadoop | hbase | 互動式查詢 | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 前端：預設 VM 大小 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 <br/>D13_v2* | D12_v2 | D3_v2 |
| 頭：最小推薦的 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 <br/>D13_v2* | D12_v2 | D3_v2 |
| 背景工作：預設 VM 大小 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2每個代理有 2 個 S30 磁片 |
| 輔助角色：最小建議的 VM 大小 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper：預設 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| 動物園管理員：最小推薦的 VM 大小 |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML 服務：預設 VM 大小 |  |  |  |  |  | D4_v2 |  |
| ML 服務：最小建議的 VM 大小 |  |  |  |  |  | D4_v2 |  |

\*• Spark 企業安全包 （ESP） 群集的 VM 大小

### <a name="brazil-south-and-japan-west-only"></a>巴西南部和日本僅西

| 叢集類型 | Hadoop | hbase | 互動式查詢 | Storm | Spark | ML 服務 |
|---|---|---|---|---|---|---|
| 前端：預設 VM 大小 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| 頭：最小推薦的 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| 背景工作：預設 VM 大小 | D4 | D4 | D14 | D3 | D13 | D4 |
| 輔助角色：最小建議的 VM 大小 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper：預設 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| 動物園管理員：最小推薦的 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML 服務：預設 VM 大小 |  |  |  |  |  | D4 |
| ML 服務：最小推薦的 VM 大小 |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - 前端稱為 Storm 叢集類型的 Nimbus**。
> - 背景工作角色稱為 Storm 叢集類型的「監督員」**。
> - 背景工作角色稱為 HBase 叢集類型的「區域」**。

## <a name="next-steps"></a>後續步驟

* [可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？](hdinsight-component-versioning.md)
