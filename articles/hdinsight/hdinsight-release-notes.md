---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: b7489c49b7469feacfd5b982615419741d286998
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849700"
---
# <a name="release-notes"></a>版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼分析使用。

## <a name="release-date-01092020"></a>發行日期：2020/1/09

此發行適用於 HDInsight 3.6 和 4.0。 在數天內，所有區域都可以使用 HDInsight 發行版本。 此處的發行日期為第一個區域發行日期。 如果您沒有看到下列變更，請稍作等待，幾天後將在您的區域發行。

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本控制文件](hdinsight-component-versioning.md)。

## <a name="new-features"></a>新功能
### <a name="tls-12-enforcement"></a>TLS 1.2 強制執行
傳輸層安全性 (TLS) 和安全通訊端層 (SSL) 是密碼編譯通訊協定，可透過電腦網路提供通訊安全性。 進一步了解 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)。 HDInsight 在公用 HTTPS 端點上使用 TLS 1.2，但仍支援 TLS 1.1 以提供回溯相容性。 

在此發行版本中，客戶只能對所有透過公用叢集端點的連線加入宣告 TLS 1.2。 為了支援這一點，引進 了新的屬性 **minSupportedTlsVersion**，並可在叢集建立期間指定。 如未設定此屬性，叢集仍然支援 TLS 1.0、1.1 和 1.2，如同目前的行為。 客戶可以將此屬性的值設定為 "1.2"，這表示叢集僅支援 TLS 1.2 和更新版本。 如需詳細資訊，請參閱[傳輸層安全性](./transport-layer-security.md)。

### <a name="bring-your-own-key-for-disk-encryption"></a>攜帶您自己的金鑰進行磁碟加密
HDInsight 中的所有受控磁碟都會使用 Azure 儲存體服務加密 (SSE) 來加以保護。 根據預設，這些磁碟上的資料會使用 Microsoft 所管理的金鑰來加密。 從這個發行版本開始，您可以攜帶您自己的金鑰 (BYOK) 進行磁碟加密，並使用 Azure Key Vault 管理它。 BYOK 加密是單一步驟的設定，可在叢集建立期間免費進行。 只要使用 Azure Key Vault 將 HDInsight 註冊為受控識別，並在建立叢集時新增加密金鑰即可。 如需詳細資訊，請參閱[客戶管理的金鑰磁碟加密](https://docs.microsoft.com/azure/hdinsight/disk-encryption)。

## <a name="deprecation"></a>淘汰
此發行版本沒有淘汰任何功能。 若要為近期將發生的淘汰做準備，請參閱[即將推出的變更](#upcoming-changes)。

## <a name="behavior-changes"></a>行為變更
此發行版本沒有任何行為變更。 若要為近期將發生的變更做準備，請參閱[即將推出的變更](#upcoming-changes)。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。 

### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>淘汰 HDInsight 3.6 Spark 叢集中的 Spark 2.1 和 2.2
自 2020 年 7 月 1 日開始，客戶將無法在 HDInsight 3.6 上使用 Spark 2.1 和 2.2 建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 3.6 上移至 Spark 2.3，以避免潛在的系統/支援中斷。

### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>淘汰 HDInsight 4.0 Spark 叢集中的 Spark 2.3
自 2020 年 7 月 1 日開始，客戶將無法在 HDInsight 4.0 上使用 Spark 2.3 建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Spark 2.4，以避免潛在的系統/支援中斷。

### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>淘汰 HDInsight 4.0 Kafka 叢集中的 Kafka 1.1
自 2020 年 7 月 1 日開始，客戶將無法在 HDInsight 4.0 上使用 Kafka 1.1 建立新的 Kafka 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Kafka 2.1，以避免潛在的系統/支援中斷。

### <a name="hbase-20-to-216"></a>HBase 2.0 升級至 2.1.6
在即將推出的 HDInsight 4.0 發行版本中，HBase 版本將從 2.0 版升級至2.1.6。

### <a name="spark-240-to-244"></a>Spark 2.4.0 升級至 2.4.4
在即將推出的 HDInsight 4.0 發行版本中，Spark 版本將從 2.4.0 版升級至2.4.4。

### <a name="kafka-210-to-211"></a>Kafka 2.1.0 升級至 2.1.1
在即將推出的 HDInsight 4.0 發行版本中，Kafka 版本將從 2.1.0 版升級至2.1.1。

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>前端節點必須至少有 4 核心 VM 
前端節點必須至少有 4 核心 VM，才能確保 HDInsight 叢集的高可用性和可靠性。 自 2020 年 4 月 6 日起，客戶只能選擇 4 核心或以上的 VM 作為新 HDInsight 叢集的前端節點。 現有的叢集將會繼續如預期般執行。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 叢集節點大小變更 
在即將推出的發行版本中，ESP Spark 叢集允許的最小節點大小會變更為 Standard_D13_V2。 A 系列 VM 因為 CPU 和記憶體容量相對較低，可能會造成 ESP 叢集問題。 未來將無法再使用 A 系列 VM建立新的 ESP 叢集。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 現在會使用 Azure 虛擬機器來佈建叢集。 在即將推出的發行版本中，HDInsight 會改為使用 Azure 虛擬機器擴展集。 請深入了解 Azure 虛擬機器擴展集。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會持續改善叢集的可靠性和效能。 

## <a name="component-version-change"></a>元件版本變更
此發行版本沒有任何元件版本變更。 您可以在這裡找到 HDInsight 4.0 和 HDInsight 3.6 的目前元件版本。

