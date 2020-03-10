---
title: Azure HDInsight 中的叢集容量規劃
description: 識別 Azure HDInsight 叢集的容量和效能規劃的重要問題。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 69627c961d9224a124fda09f40901f837d627281
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2020
ms.locfileid: "78932628"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 叢集的容量規劃

在部署 HDInsight 叢集之前，請規劃所需的叢集容量，方法為判斷所需的效能和縮放比例。 此計劃可協助將可用性和成本最佳化。 無法在部署之後變更某些叢集容量決策。 如果效能參數變更，就可以拆卸並重新建立叢集，而不會遺失預存的資料。

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

預設儲存體 (無論是 Azure 儲存體帳戶或 Azure Data Lake Storage) 必須與您的叢集在相同的位置。 Azure 儲存體可在所有位置使用。 Data Lake Storage Gen1 可在某些區域中使用-請參閱目前的[Data Lake Storage 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

### <a name="location-of-existing-data"></a>現有資料的位置

如果您已經有包含資料的儲存體帳戶或 Data Lake Storage，並需要使用這個儲存體作為叢集的預設儲存體，就必須在該相同的位置部署您的叢集。

### <a name="storage-size"></a>儲存體大小

部署 HDInsight 叢集之後，您可以連接額外的 Azure 儲存體帳戶，或存取其他 Data Lake Storage。 所有的儲存體帳戶都必須與您的叢集位於相同的位置。 Data Lake Storage 可位於不同的位置，雖然這樣可能會導致某些資料的讀取/寫入延遲。

Azure 儲存體有某些[容量限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)，但 Data Lake Storage Gen1 幾乎不受限制。

叢集可以存取不同儲存體帳戶的組合。 典型的範例包括：

* 當資料量很可能超過單一 blob 儲存體容器的儲存體容量。
* 當 blob 容器的存取速率可能會超過節流發生的閾值。
* 當您想要建立資料時，您已上傳至可供叢集使用的 blob 容器。
* 當您基於安全性的原因或要簡化系統管理而需要將儲存體的不同部分進行隔離。

為提升效能，每個儲存體帳戶僅使用一個容器。

## <a name="choose-a-cluster-type"></a>選擇叢集類型

叢集類型會決定您 HDInsight 叢集設定要執行的工作負載，例如 [Apache Hadoop](https://hadoop.apache.org/)、[Apache Storm](https://storm.apache.org/)、[Apache Kafka](https://kafka.apache.org/) 或 [Apache Spark](https://spark.apache.org/)。 如需可用叢集類型的詳細說明，請參閱 [Azure HDInsight 簡介](hdinsight-overview.md#cluster-types-in-hdinsight)。 每個叢集類型都有特定的部署拓撲，其中包含節點數目和大小的需求。

## <a name="choose-the-vm-size-and-type"></a>選擇 VM 大小與類型

每個叢集類型都具有一組節點類型，且每個節點類型都有其 VM 大小和類型的特定選項。

若要判斷您應用程式的最佳叢集大小，您可以效能評定叢集容量，並依指示增加大小。 例如，您可以使用模擬的工作負載或 Canary 查詢。 使用模擬工作負載時，您要在不同大小的叢集上執行您預期的工作負載，逐漸增加大小直到觸達所需的效能為止。 未分類的查詢可以在其他生產查詢中定期插入，以顯示叢集是否有足夠的資源。

如需有關如何為您的工作負載選擇正確 VM 系列的詳細資訊，請參閱為您的叢集[選取正確的 vm 大小](hdinsight-selecting-vm-size.md)。

## <a name="choose-the-cluster-scale"></a>選擇叢集縮放比例

叢集的縮放比例取決於其 VM 節點的數量。 所有叢集類型的節點類型都有特定的規模，以及支援相應放大的節點類型。例如，叢集可能只需要三個[Apache ZooKeeper](https://zookeeper.apache.org/)節點或兩個前端節點。 以分散式方式進行資料處理的背景工作節點可受益於相應放大，方法是新增其他背景工作節點。

根據您的叢集類型，增加背景工作節點數可新增額外的計算容量 (例如更多核心)，但也可以新增至整個叢集所需的記憶體總數，以支援記憶體內正在進行處理的資料儲存體。 如同 VM 大小和類型的選擇，通常會使用模擬的工作負載或 Canary 查詢，以實證方式達到選取正確的叢集縮放比例。

您可以擴增叢集以符合尖峰負載需求，然後當不再需要這些額外的節點時，將其縮減。 自動調整[功能](hdinsight-autoscale-clusters.md)可讓您根據預先決定的計量和時間，自動擴充您的叢集。 如需手動調整叢集的詳細資訊，請參閱[調整 HDInsight 叢集規模](hdinsight-scaling-best-practices.md)。

### <a name="cluster-lifecycle"></a>叢集生命週期

叢集的存留期需要收費。 如果您只需要叢集在特定時間開機及執行，可以[使用 Azure Data Factory 來建立隨需叢集](hdinsight-hadoop-create-linux-clusters-adf.md)。 您也可以建立 PowerShell 指令碼來佈建和刪除您的叢集，並使用 [Azure 自動化](https://azure.microsoft.com/services/automation/)來排程這些指令碼。

> [!NOTE]  
> 刪除叢集時，也會刪除其預設的 Hive 中繼存放區。 若要保存中繼存放區以進行下一次重新建立叢集，請使用外部中繼資料存放區，例如 Azure 資料庫或 [Apache Oozie](https://oozie.apache.org/)。
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>找出叢集作業錯誤

有時會因多個對應平行執行而發生錯誤，從而減少多節點叢集上的元件。 若要協助找出問題，請在單一背景工作節點叢集上執行並行多個作業來嘗試進行分散式測試，然後展開此方法，在包含多個節點的叢集上同時執行多個作業。 若要在 Azure 中建立單一節點的 HDInsight 叢集，請使用 [*自訂（大小、設定、應用程式）* ] 選項，並在入口網站中布建新叢集時，針對 [叢集**大小**] 區段中的背景*工作節點數目*使用1的值。

## <a name="quotas"></a>配額

決定您的目標叢集 VM 大小、縮放比例和類型之後，請檢查您訂用帳戶的目前配額容量限制。 當您觸達配額限制時，可能無法部署新的叢集，或新增更多背景工作節點來將現有的叢集擴增。 唯一的配額限制是 CPU 核心配額，其存在於每個訂用帳戶區域層級。 例如，您的訂用帳戶在美國東部區域可能有 30 個核心限制。 

若要檢查可用的核心，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 流覽至 HDInsight 叢集的 [**總覽**] 頁面。 
3. 在左側功能表上，按一下 [**配額限制**]。

   此頁面會顯示使用中的核心數目、可用的核心數目，以及核心總數。

如果您需要要求增加配額，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頁面左下方的 [說明 **+ 支援**]。
1. 選取 [新增支援要求]。
1. 在 [新增支援要求] 頁面的 [基本] 索引標籤上，選取下列選項：

   - **問題類型**：**服務和訂用帳戶限制（配額）**
   - **訂用帳戶**：您需要修改的訂用帳戶
   - **配額類型**： **HDInsight**

     ![建立支援要求以提高 HDInsight 核心配額](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. 選取 **[下一步]：方案 > >** 。
1. 在 [**詳細資料**] 頁面上，輸入問題的描述、選取問題的嚴重性、您偏好的連絡人方法，以及其他必要的欄位。
1. 選取 **[下一步]： [檢查 + 建立 > >]** 。
1. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。

> [!NOTE]  
> 如果需要在私人區域中新增 HDInsight 核心配額，請[提交允許清單要求](https://aka.ms/canaryintwhitelist)。

您可以[連絡支援人員以要求增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

不過，有一些固定的配額限制，例如單一 Azure 訂用帳戶最多可以有 10,000 個核心。 如需這些限制的詳細資料，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何在 HDInsight 中使用 Apache Hadoop、Spark、Kafka、Interactive Hive、HBase、ML 服務或 Storm 安裝並設定叢集。
* [監視叢集效能](hdinsight-key-scenarios-to-monitor.md)：了解重要情節，以監視可能會影響叢集容量的 HDInsight 叢集。
