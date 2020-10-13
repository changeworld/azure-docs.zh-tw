---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974561"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼分析使用。

## <a name="release-date-10082020"></a>發行日期：10/08/2020

此版本適用于 HDInsight 3.6 和 HDInsight 4.0。 在數天內，所有區域都可以使用 HDInsight 發行版本。 此處的發行日期為第一個區域發行日期。 如果您沒有看到以下變更，請在數天內等待發行在您的區域中。

## <a name="new-features"></a>新功能
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>沒有公用 IP 和私人連結的 HDInsight 私人叢集 (預覽) 
HDInsight 現在支援在預覽版中建立叢集，而不使用公用 IP 和私人連結來存取叢集。 客戶可使用新的 advanced 網路設定來建立完全隔離的叢集，而不使用公用 IP，並使用自己的私人端點來存取叢集。 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 現在會使用 Azure 虛擬機器來佈建叢集。 從這個版本開始，服務會逐漸遷移至 [Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。 整個過程可能需要數個月的時間。 遷移您的區域和訂用帳戶之後，新建立的 HDInsight 叢集將會在沒有客戶動作的虛擬機器擴展集上執行。 不需要中斷變更。

## <a name="deprecation"></a>淘汰
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>淘汰 HDInsight 3.6 ML 服務叢集
HDInsight 3.6 ML 服務叢集類型將于12月 31 2020 日結束支援。 客戶之後將無法建立新的 3.6 ML 服務叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請在 [此](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)檢查 HDInsight 版本和叢集類型的支援期限。

## <a name="behavior-changes"></a>行為變更
此版本沒有任何行為變更。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>能夠為 Spark、Hadoop 和 ML 服務選取不同的 Zookeeper 虛擬機器大小
HDInsight today 不支援針對 Spark、Hadoop 和 ML 服務叢集類型自訂 Zookeeper 節點大小。 它會預設為 A2_v2/A2 的虛擬機器大小，並提供免費的費用。 在即將推出的版本中，您可以選取最適合您案例的 Zookeeper 虛擬機器大小。 A2_v2/A2 以外的虛擬機器大小的 Zookeeper 節點將會收取費用。 A2_v2 和 A2 虛擬機器仍提供免費的費用。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會持續改善叢集的可靠性和效能。 

## <a name="component-version-change"></a>元件版本變更
此發行版本沒有任何元件版本變更。 您可以在 [本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)檔中找到 hdinsight 4.0 和 hdinsight 3.6 目前的元件版本。