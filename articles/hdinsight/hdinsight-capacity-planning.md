---
title: Azure HDInsight 中的叢集容量規劃
description: 識別規劃 Azure HDInsight 叢集容量與效能的重要問題。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 8e76f767470b9052b25cd2b2958f3f9e9780881b
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714741"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 叢集的容量規劃

在部署 HDInsight 叢集之前，請判斷所需的效能與規模，以規劃預定的叢集容量。 此計劃可協助將可用性和成本最佳化。 部署之後，將無法變更某些叢集容量決策。 如果效能參數變更，就可以拆卸並重新建立叢集，而不會遺失預存的資料。

容量規劃的主要問題是：

* 您應該在哪些地理區域中部署叢集？
* 您需要多少儲存空間？
* 您應該部署哪種叢集類型？
* 您的叢集節點應該使用什麼尺寸和類型的虛擬機器 (VM)？
* 您的叢集應該有幾個背景工作角色？

## <a name="choose-an-azure-region"></a>選擇 Azure 區域

Azure 地區決定您叢集實際佈建的位置。 若要將讀取和寫入的延遲降至最低，叢集就應該接近您的資料。

HDInsight 可在多個 Azure 區域中使用。 若要尋找最接近的區域，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)。

## <a name="choose-storage-location-and-size"></a>選擇存放位置和大小

### <a name="location-of-default-storage"></a>預設儲存體的位置

預設儲存體 (無論是 Azure 儲存體帳戶或 Azure Data Lake Storage) 必須與您的叢集在相同的位置。 Azure 儲存體可在所有位置使用。 有些區域提供 Data Lake Storage Gen1，請參閱最新的 [Data Lake Storage 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

### <a name="location-of-existing-data"></a>現有資料的位置

若想要使用現有的儲存體帳戶或 Data Lake Storage 作為叢集的預設儲存體，您就必須在相同的位置部署您的叢集。

### <a name="storage-size"></a>儲存體大小

在部署的叢集上，您可以連接其他 Azure 儲存體帳戶，或存取其他 Data Lake Storage。 所有的儲存體帳戶都必須與您的叢集位在同一位置。 儘管遠距離可能會導致某些延遲，Data Lake Storage 仍可位於不同的位置。

Azure 儲存體有某些[容量限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)，但 Data Lake Storage Gen1 幾乎是毫無限制。

叢集可以存取不同儲存體帳戶的組合。 典型的範例包括：

* 當資料量很可能超過單一 blob 儲存體容器的儲存體容量。
* 當 blob 容器的存取速率可能會超過節流發生的閾值。
* 當您想要製作資料時，就已經上傳了可供叢集使用的 Blob 容器。
* 當您基於安全性的原因或要簡化系統管理而需要將儲存體的不同部分進行隔離。

為提升效能，每個儲存體帳戶僅使用一個容器。

## <a name="choose-a-cluster-type"></a>選擇叢集類型

叢集類型決定您 HDInsight 叢集設定要執行的工作負載。 這些類型包括 [Apache Hadoop](./hadoop/apache-hadoop-introduction.md)、[Apache Storm](./storm/apache-storm-overview.md)、[Apache Kafka](./kafka/apache-kafka-introduction.md) 或 [Apache Spark](./spark/apache-spark-overview.md)。 如需可用叢集類型的詳細說明，請參閱 [Azure HDInsight 簡介](hdinsight-overview.md#cluster-types-in-hdinsight)。 每個叢集類型都有特定的部署拓撲，其中包含節點數目和大小的需求。

## <a name="choose-the-vm-size-and-type"></a>選擇 VM 大小與類型

每個叢集類型都具有一組節點類型，且每個節點類型都有其 VM 大小和類型的特定選項。

若要判斷您應用程式的最佳叢集大小，您可以效能評定叢集容量，並依指示增加大小。 例如，您可以使用模擬的工作負載或 Canary 查詢。 在不同大小的叢集上執行模擬的工作負載。 逐漸增加大小，直到達到預定效能為止。 您可以在其他生產查詢間定期插入 Canary 查詢，以顯示叢集是否有足夠的資源。

如需如何為工作負載選擇正確 VM 系列的詳細資訊，請參閱[為您的叢集選取正確的 VM 大小](hdinsight-selecting-vm-size.md)。

## <a name="choose-the-cluster-scale"></a>選擇叢集縮放比例

叢集的縮放比例取決於其 VM 節點的數量。 針對所有的叢集類型，有些叢集類型具有特定的縮放比例，以及支援相應放大的節點類型。例如，叢集可能需要正好三個 [Apache ZooKeeper](https://zookeeper.apache.org/) 節點或兩個前端節點。 以分散方式處理資料的背景工作節點可自其他背景工作節點獲益。

依據您的叢集類型，增加背景工作節點的數目會可新增額外的計算容量 (例如更多核心)。 擁有更多節點會增加整個叢集所需的記憶體總量，以支援正在處理資料的記憶體內部儲存體。 如同選擇 VM 大小與類型一樣，累積的經驗最終也會讓您選到正確的叢集規模。 請使用模擬的工作負載或 Canary 查詢。

您可以擴增叢集，以符合尖峰的負載需求。 然後，當不再需要這些額外的節點時，再縮小叢集。 [自動調整功能](hdinsight-autoscale-clusters.md)可讓您依據預先決定的計量與時間，自動調整您的叢集。 如需手動調整叢集的詳細資訊，請參閱[調整 HDInsight 叢集](hdinsight-scaling-best-practices.md)。

### <a name="cluster-lifecycle"></a>叢集生命週期

叢集的存留期需要收費。 若只在特定時間需要叢集，請[使用 Azure Data Factory 來建立隨需叢集](hdinsight-hadoop-create-linux-clusters-adf.md)。 您也可以建立 PowerShell 指令碼來佈建和刪除您的叢集，並使用 [Azure 自動化](https://azure.microsoft.com/services/automation/)來排程這些指令碼。

> [!NOTE]  
> 刪除叢集時，也會刪除其預設的 Hive 中繼存放區。 若要保存中繼存放區以進行下一次重新建立叢集，請使用外部中繼資料存放區，例如 Azure 資料庫或 [Apache Oozie](https://oozie.apache.org/)。

### <a name="isolate-cluster-job-errors"></a>找出叢集作業錯誤

有時會因平行執行多個對應，以及減少多節點叢集上的元件，而發生錯誤。 為協助隔離問題，請嘗試分散式測試。 在單一背景工作節點叢集上執行多個並行作業。 然後展開此方法，以在包含多個節點的叢集上同時執行多個作業。 若要在 Azure 中建立單一節點的 HDInsight 叢集，請使用 [ *`Custom(size, settings, apps)`* ] 選項，並在入口網站佈建新叢集時，於 [叢集大小] 區段的 [背景工作節點數目] 使用值 1。

## <a name="quotas"></a>配額

如需管理訂閱配額的詳細資訊，請參閱[要求增加配額](quota-increase-request.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Hadoop、Spark、Kafka 及其他工具在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何在 HDInsight 中設定及配置叢集。
* [監視叢集效能](hdinsight-key-scenarios-to-monitor.md)：了解重要情節，以監視可能會影響叢集容量的 HDInsight 叢集。
