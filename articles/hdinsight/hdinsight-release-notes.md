---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535605"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼分析使用。

## <a name="release-date-09282020"></a>發行日期：09/28/2020

此版本適用于 HDInsight 3.6 和 HDInsight 4.0。 在數天內，所有區域都可以使用 HDInsight 發行版本。 此處的發行日期為第一個區域發行日期。 如果您沒有看到以下變更，請在數天內等待發行在您的區域中。

## <a name="new-features"></a>新功能
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>HDInsight 4.0 的 Interactive Query 自動調整現已正式推出
適用于 Interactive Query 叢集類型的自動調整現已正式推出 (適用于 HDInsight 4.0 的 GA) 。 在2020年8月27日之後建立的所有 Interactive Query 4.0 叢集將擁有自動調整的 GA 支援。

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>HBase 叢集支援 Premium ADLS Gen2
HDInsight 現在支援 Premium ADLS Gen2 作為 HDInsight HBase 3.6 和4.0 叢集的主要儲存體帳戶。 搭配 [加速寫入](./hbase/apache-hbase-accelerated-writes.md)，您可以為您的 HBase 叢集取得較佳的效能。

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Azure 容錯網域上的 Kafka 資料分割分佈
容錯網域是 Azure 資料中心內基礎硬體的邏輯群組。 每個容錯網域會共用通用電源和網路交換器。 HDInsight Kafka 可能會將所有磁碟分割複本儲存在相同的容錯網域中。 從這個版本開始，HDInsight 現在支援根據 Azure 容錯網域自動散發 Kafka 分割區。 

### <a name="encryption-in-transit"></a>傳輸中加密
客戶可以使用 IPSec 加密與平臺管理的金鑰，在叢集節點之間啟用傳輸加密。 您可以在建立叢集時啟用這個選項。 查看更多有關 [如何啟用傳輸中加密的](./domain-joined/encryption-in-transit.md)詳細資料。

### <a name="encryption-at-host"></a>主機上的加密
當您在主機上啟用加密時，儲存在 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 在此版本中，您可以在建立叢集時，于 **暫存資料磁片上的主機啟用加密** 。 只有 [有限區域中的特定 VM sku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal)才支援在主機上進行加密。 HDInsight 支援 [下列節點設定和 sku](./hdinsight-supported-node-configuration.md)。 請參閱有關 [如何在主機上啟用加密的](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys)詳細資料。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 現在會使用 Azure 虛擬機器來佈建叢集。 從這個版本開始，服務會逐漸遷移至 [Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。 整個過程可能需要數個月的時間。 遷移您的區域和訂用帳戶之後，新建立的 HDInsight 叢集將會在沒有客戶動作的虛擬機器擴展集上執行。 不需要中斷變更。

## <a name="deprecation"></a>淘汰
此版本沒有淘汰。

## <a name="behavior-changes"></a>行為變更
此版本沒有任何行為變更。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>能夠為 Spark、Hadoop 和 ML 服務選取不同的 Zookeeper SKU
HDInsight 今日不支援變更 Spark、Hadoop 和 ML 服務叢集類型的 Zookeeper SKU。 它使用適用于 Zookeeper 節點 A2_v2/A2 SKU，而不會向客戶收取費用。 在即將推出的版本中，客戶可以視需要變更 Spark、Hadoop 和 ML 服務的 Zookeeper SKU。 具有 A2_v2/A2 以外 SKU 的 Zookeeper 節點將會收取費用。 預設 SKU 仍會 A2_V2/A2，而且不會收費。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會持續改善叢集的可靠性和效能。 

## <a name="component-version-change"></a>元件版本變更
此發行版本沒有任何元件版本變更。 您可以在 [本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)檔中找到 hdinsight 4.0 和 hdinsight 3.6 目前的元件版本。