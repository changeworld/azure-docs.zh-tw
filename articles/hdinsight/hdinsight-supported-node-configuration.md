---
title: Azure HDInsight 支援的節點設定
description: 瞭解 HDInsight 叢集節點的最低和建議設定。
keywords: vm 大小，叢集大小，叢集設定
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77484781"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight 的預設和建議節點設定為何？

本文討論 Azure HDInsight 叢集的預設和建議節點設定。

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>叢集的預設和最低建議節點設定和虛擬機器大小

下表列出 HDInsight 叢集的預設和建議的虛擬機器（VM）大小。  當您建立 PowerShell 或 Azure CLI 腳本來部署 HDInsight 叢集時，必須要有此資訊，才能瞭解要使用的 VM 大小。

如果您在叢集中需要超過32個背景工作節點，請選取具有至少8個核心和 14 GB RAM 的前端節點大小。 

具有資料磁片的唯一叢集類型是 Kafka，以及已啟用加速寫入功能的 HBase 叢集。 在這些情況下，HDInsight 支援 P30 和 S30 磁片大小。

下表摘要說明本檔中所使用之所有最低建議 VM 類型的規格。

| Size              | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟 / 輸送量︰IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

如需每個 VM 類型規格的詳細資訊，請參閱下列檔：

* [一般用途的虛擬機器大小： Dv2 系列1-5](../virtual-machines/dv2-dsv2-series.md)
* [記憶體優化的虛擬機器大小： Dv2 系列11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [一般用途的虛擬機器大小： Av2 系列1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>除了巴西南部和日本西部以外的所有支援區域

> [!Note]
> 若要取得用於 powershell 和其他腳本的 SKU 識別碼，請將`Standard_`新增至下表中所有 VM sku 的開頭。 例如， `D12_v2`會變成`Standard_D12_v2`。

| 叢集類型 | Hadoop | hbase | 互動式查詢 | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 前端：預設 VM 大小 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2、 <br/>D13_v2 * | D12_v2 | D3_v2 |
| Head：建議的最低 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2、 <br/>D13_v2 * | D12_v2 | D3_v2 |
| 背景工作：預設 VM 大小 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 每個訊息代理程式有2個 S30 磁片 |
| 背景工作：建議的最低 VM 大小 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper：預設 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper：建議的最低 VM 大小 |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML 服務：預設 VM 大小 |  |  |  |  |  | D4_v2 |  |
| ML 服務：建議的最低 VM 大小 |  |  |  |  |  | D4_v2 |  |

\*= Spark 企業安全性套件（ESP）叢集的 VM 大小

### <a name="brazil-south-and-japan-west-only"></a>僅限巴西南部和日本西部

| 叢集類型 | Hadoop | hbase | 互動式查詢 | Storm | Spark | ML 服務 |
|---|---|---|---|---|---|---|
| 前端：預設 VM 大小 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head：建議的最低 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| 背景工作：預設 VM 大小 | D4 | D4 | D14 | D3 | D13 | D4 |
| 背景工作：建議的最低 VM 大小 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper：預設 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper：建議的最低 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML 服務：預設 VM 大小 |  |  |  |  |  | D4 |
| ML 服務：建議的最低 VM 大小 |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - 前端稱為 Storm 叢集類型的 Nimbus**。
> - 背景工作角色稱為 Storm 叢集類型的「監督員」**。
> - 背景工作角色稱為 HBase 叢集類型的「區域」**。

## <a name="next-steps"></a>後續步驟

* [可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？](hdinsight-component-versioning.md)
