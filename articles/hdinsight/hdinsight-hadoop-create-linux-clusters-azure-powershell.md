---
title: 使用 PowerShell 建立 Apache Hadoop 叢集 - Azure HDInsight
description: 了解如何在 Linux 上使用 Azure PowerShell 為 HDInsight 建立 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 23ce0f83e4233837d56ba9045a9a356ebd1cc33f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541805"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Microsoft Azure 中控制和自動化工作量的部署與管理。 本文件提供如何使用 Azure PowerShell 建立以 Linux 為基礎之 HDInsight 叢集的相關資訊。 其中也包含一個範例指令碼。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) Az 模組。

## <a name="create-cluster"></a>建立叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要使用 Azure PowerShell 建立 HDInsight 叢集，您必須完成下列程序：

* 建立 Azure 資源群組
* 建立 Azure 儲存體帳戶
* 建立 Azure Blob 容器
* 建立 HDInsight 叢集

> [!NOTE]
> 目前不支援使用 PowerShell 建立具有 Azure Data Lake Storage Gen2 的 HDInsight 叢集。

下列指令碼示範如何建立新叢集：

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

您為叢集登入指定的值會用來建立叢集的 Hadoop 使用者帳戶。 請使用此帳戶來連線叢集上裝載的服務，像是 web UI 或 REST API。

您為 SSH 使用者指定的值會用來建立叢集的 SSH 使用者。 使用此帳戶在叢集上啟動遠端 SSH 工作階段並執行工作。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md) 文件。

> [!IMPORTANT]  
> 如果您規劃使用 32 個以上的背景工作角色節點 (在建立叢集時或在建立後調整叢集時)，則您也必須指定具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

建立叢集可能需要花費 20 分鐘的時間。

## <a name="create-cluster-configuration-object"></a>建立叢集：設定物件

您也可以使用 Cmdlet 來建立 HDInsight 設定物件 [`New-AzHDInsightClusterConfig`](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) 。 然後，您可以修改此設定物件來啟用叢集的其他設定選項。 最後，使用 `-Config` Cmdlet 的參數 [`New-AzHDInsightCluster`](/powershell/module/az.hdinsight/new-azhdinsightcluster) 來使用設定。

下列指令碼會建立可在 HDInsight 叢集類型上設定 R 伺服器的設定物件。 此設定可啟用邊緣節點、RStudio 和其他儲存體帳戶。

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用儲存體帳戶。 使用此範例時，請在與伺服器相同的位置建立其他儲存體帳戶。

## <a name="customize-clusters"></a>自訂叢集

* 請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
* 請參閱[使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="delete-the-cluster"></a>選取叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>下一步

既然您已成功建立 HDInsight 叢集，請使用下列資源來瞭解如何使用您的叢集。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集

* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache HBase 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](storm/apache-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](storm/apache-storm-develop-python-topology.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 叢集

* [使用 Scala 建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)