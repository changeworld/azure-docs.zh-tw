---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 458925dd9f7f7386a9159256fdb024d027f7016c
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929310"
---
# <a name="release-notes"></a>版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>總結

Azure HDInsight 是 Azure 上的開放原始碼分析企業客戶中最受歡迎的其中一項服務。

## <a name="release-date-01092020"></a>發行日期：01/09/2020

此版本適用于 HDInsight 3.6 和4.0。 在數天內，所有區域都可以使用 HDInsight 版本。 此處的 [發行日期] 表示第一個區域發行日期。 如果您沒有看到下列變更，請在您的區域中等候幾天的版本。

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本控制文件](hdinsight-component-versioning.md)。

## <a name="new-features"></a>新功能
### <a name="tls-12-enforcement"></a>TLS 1.2 強制執行
傳輸層安全性 (TLS) 和安全通訊端層 (SSL) 是密碼編譯通訊協定，可透過電腦網路提供通訊安全性。 深入瞭解[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)。 HDInsight 在公用 HTTPs 端點上使用 TLS 1.2，但仍支援 TLS 1.1 以提供回溯相容性。 

在此版本中，客戶只能針對透過公用叢集端點的所有連線加入宣告 TLS 1.2。 為了支援這種情況，會引進新的屬性**minSupportedTlsVersion** ，並可在叢集建立期間指定。 如果未設定此屬性，叢集仍然支援 TLS 1.0、1.1 和1.2，這與今天的行為相同。 客戶可以將此屬性的值設定為 "1.2"，這表示叢集僅支援 TLS 1.2 和更新版本。 如需詳細資訊，請參閱[傳輸層安全性](./transport-layer-security.md)。

### <a name="bring-your-own-key-for-disk-encryption"></a>攜帶您自己的金鑰進行磁片加密
HDInsight 中的所有受控磁碟都會使用 Azure 儲存體服務加密 (SSE) 來加以保護。 根據預設，這些磁片上的資料會由受 Microsoft 管理的金鑰加密。 從這個版本開始，您可以攜帶您自己的金鑰（BYOK）進行磁片加密，並使用 Azure Key Vault 來管理它。 BYOK 加密是叢集建立期間的單一步驟設定，不需額外費用。 只要使用 Azure Key Vault 將 HDInsight 註冊為受控識別，並在建立叢集時新增加密金鑰。 如需詳細資訊，請參閱[客戶管理的金鑰磁片加密](https://docs.microsoft.com/azure/hdinsight/disk-encryption)。

## <a name="deprecation"></a>淘汰
此版本沒有任何棄用功能。 若要準備開始進行棄用功能，請參閱即將進行的[變更](#upcoming-changes)。

## <a name="behavior-changes"></a>行為變更
此版本沒有任何行為變更。 若要準備進行即將進行的變更，請參閱即將進行的[變更](#upcoming-changes)。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。 

### <a name="deprecate-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 中的取代 Spark 2.1 和 2.2 3.6 Spark 叢集
從 1 2020 年7月開始，客戶將無法在 HDInsight 3.6 上使用 Spark 2.1 和2.2 來建立新的 Spark 叢集。 現有的叢集將會以不受 Microsoft 支援的方式執行。 請考慮在6月 30 2020 于 HDInsight 3.6 上移至 Spark 2.3，以避免潛在的系統/支援中斷。

### <a name="deprecate-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark 叢集中的取代 Spark 2。3
從 1 2020 年7月開始，客戶將無法使用 Spark 2.3 on HDInsight 4.0 來建立新的 Spark 叢集。 現有的叢集將會以不受 Microsoft 支援的方式執行。 請考慮移至6月 30 2020 的 HDInsight 4.0 上的 Spark 2.4，以避免潛在的系統/支援中斷。

### <a name="deprecate-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka 叢集中的取代 Kafka 1。1
從 1 2020 年7月開始，客戶將無法使用 Kafka 1.1 on HDInsight 4.0 來建立新的 Kafka 叢集。 現有的叢集將會以不受 Microsoft 支援的方式執行。 請考慮移至6月 30 2020 的 HDInsight 4.0 上的 Kafka 2.1，以避免潛在的系統/支援中斷。

### <a name="hbase-20-to-216"></a>HBase 2.0 至2.1。6
在即將推出的 HDInsight 4.0 版本中，HBase 版本將從2.0 版升級至2.1。6

### <a name="spark-240-to-244"></a>Spark 2.4.0 至2.4。4
在即將推出的 HDInsight 4.0 版本中，Spark 版本將從版本2.4.0 升級至2.4。4

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>前端節點必須至少有4核心 VM 
前端節點必須至少有4核心 VM，才能確保 HDInsight 叢集的高可用性和可靠性。 自 6 2020 年4月起，客戶只能選擇4核心或以上的 VM 作為新 HDInsight 叢集的前端節點。 現有的叢集將會繼續如預期般執行。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 叢集節點大小變更 
在即將發行的版本中，ESP Spark 叢集所允許的最小節點大小會變更為 Standard_D13_V2。 A 系列 Vm 可能會造成 ESP 叢集問題，因為 CPU 和記憶體容量相對較低。 A 系列 Vm 將會被取代，以建立新的 ESP 叢集。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 現在會使用 Azure 虛擬機器來布建叢集。 在即將推出的版本中，HDInsight 會改為使用 Azure 虛擬機器擴展集。 深入瞭解 Azure 虛擬機器擴展集。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會繼續改善叢集的可靠性和效能。 

## <a name="component-version-change"></a>元件版本變更
此版本沒有任何元件版本變更。 您可以在這裡找到 HDInsight 4.0 ad HDInsight 3.6 的目前元件版本。

