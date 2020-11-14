---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 1ae4e2a1e0d67a0a09c19b517245ffc6d92d17df
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629915"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 版本資訊

本文提供有關 **最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼分析使用。

如果您想要訂閱版本資訊，請觀賞 [此 GitHub 存放庫](https://github.com/hdinsight/release-notes/releases)上的版本。

## <a name="release-date-11092020"></a>發行日期：11/09/2020

此版本適用于 HDInsight 3.6 和 HDInsight 4.0。 在數天內，所有區域都可以使用 HDInsight 發行版本。 此處的發行日期為第一個區域發行日期。 如果您沒有看到以下變更，請在數天內等待發行在您的區域中。

## <a name="new-features"></a>新功能
### <a name="hdinsight-identity-broker-hib-is-now-ga"></a>HDInsight Identity Broker (HIB) 現已正式推出
啟用 ESP 叢集 OAuth 驗證的 HDInsight Identity Broker (HIB) 現已在此版本中正式推出。 此版本之後所建立的 HIB 叢集將具有最新的 HIB 功能：
- 高可用性 (HA) 
- 支援 Multi-Factor Authentication (MFA) 
- 同盟使用者登入，而不使用密碼雜湊同步處理至 AAD-DS 如需詳細資訊，請參閱 [HIB 檔](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker)。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 現在會使用 Azure 虛擬機器來佈建叢集。 從這個版本開始，服務會逐漸遷移至 [Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。 整個過程可能需要數個月的時間。 遷移您的區域和訂用帳戶之後，新建立的 HDInsight 叢集將會在沒有客戶動作的虛擬機器擴展集上執行。 不需要中斷變更。

## <a name="deprecation"></a>淘汰
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>淘汰 HDInsight 3.6 ML 服務叢集
HDInsight 3.6 ML 服務叢集類型將于 31 2020 年12月結束支援。 客戶將不會在 31 2020 年12月之後建立新的 3.6 ML 服務叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請在 [此](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)檢查 HDInsight 版本和叢集類型的支援期限。

### <a name="disabled-vm-sizes"></a>停用的 VM 大小
從 16 2020 年11月開始，HDInsight 將會封鎖新客戶使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小來建立叢集。 過去三個月內使用這些 VM 大小的現有客戶將不會受到影響。 從 9 2021 年1月開始，HDInsight 將會封鎖所有使用 standand_A8、standand_A9 standand_A10 和 standand_A11 VM 大小來建立叢集的客戶。 現有的叢集將會依原樣執行。 請考慮移至 HDInsight 4.0，以避免潛在的系統/支援中斷。

## <a name="behavior-changes"></a>行為變更
此版本沒有任何行為變更。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>能夠為 Spark、Hadoop 和 ML 服務選取不同的 Zookeeper 虛擬機器大小
HDInsight today 不支援針對 Spark、Hadoop 和 ML 服務叢集類型自訂 Zookeeper 節點大小。 它會預設為 A2_v2/A2 的虛擬機器大小，並提供免費的費用。 在即將推出的版本中，您可以選取最適合您案例的 Zookeeper 虛擬機器大小。 A2_v2/A2 以外的虛擬機器大小的 Zookeeper 節點將會收取費用。 A2_v2 和 A2 虛擬機器仍提供免費的費用。

### <a name="default-cluster-version-will-be-changed-to-40"></a>預設叢集版本將會變更為4。0
自2021年2月起，HDInsight 叢集的預設版本將自3.6 變更為4.0。 如需可用版本的詳細資訊，請參閱 [可用的版本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)。 深入瞭解[HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release)的新功能

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 年 6 30 2021 月終止支援
HDInsight 3.6 即將結束支援。 自 30 2021 年6月起，客戶無法建立新的 HDInsight 3.6 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮移至 HDInsight 4.0，以避免潛在的系統/支援中斷。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會持續改善叢集的可靠性和效能。 
### <a name="fix-issue-for-restarting-vms-in-cluster"></a>修正在叢集中重新開機 Vm 的問題
已修正在叢集中重新開機 Vm 的問題，您可以使用 [PowerShell 或 REST API 重新開機叢集中的節點](https://docs.microsoft.com/azure/hdinsight/cluster-reboot-vm) 。

## <a name="component-version-change"></a>元件版本變更
此發行版本沒有任何元件版本變更。 您可以在 [本](./hdinsight-component-versioning.md)檔中找到 hdinsight 4.0 和 hdinsight 3.6 目前的元件版本。
