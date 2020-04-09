---
title: Azure HDInsight 中的叢集容量規劃
description: 確定 Azure HDInsight 群集的容量和性能規劃的關鍵問題。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886988"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 叢集的容量規劃

在部署 HDInsight 群集之前,通過確定所需的性能和規模來規劃預期的群集容量。 此計劃可協助將可用性和成本最佳化。 部署後無法更改某些群集容量決策。 如果效能參數變更，就可以拆卸並重新建立叢集，而不會遺失預存的資料。

容量規劃的主要問題是：

* 您應該在哪些地理區域中部署叢集？
* 您需要多少儲存空間？
* 您應該部署哪種叢集類型？
* 您的叢集節點應該使用什麼尺寸和類型的虛擬機器 (VM)？
* 您的叢集應該有幾個背景工作角色？

## <a name="choose-an-azure-region"></a>選擇 Azure 區域

Azure 地區決定您叢集實際佈建的位置。 若要將讀取和寫入的延遲降至最低，叢集就應該接近您的資料。

HDInsight 可在多個 Azure 區域中使用。 要尋找最近的區域,請參閱[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)。

## <a name="choose-storage-location-and-size"></a>選擇存放位置和大小

### <a name="location-of-default-storage"></a>預設儲存體的位置

預設儲存體 (無論是 Azure 儲存體帳戶或 Azure Data Lake Storage) 必須與您的叢集在相同的位置。 Azure 儲存體可在所有位置使用。 資料儲存的資料儲存 Gen1 在某些地區可用 - 請參考目前[資料儲存可用性](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

### <a name="location-of-existing-data"></a>現有資料的位置

如果要使用現有存儲帳戶或數據湖儲存作為群集的預設存儲,則必須將群集部署在同一位置。

### <a name="storage-size"></a>儲存體大小

在已部署的群集上,可以附加其他 Azure 儲存帳戶或訪問其他數據湖存儲。 所有存儲帳戶必須與群集位於同一位置。 數據湖存儲可能位於其他位置,但距離遙遠可能會帶來一些延遲。

Azure 儲存有一些[容量限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits),而數據存儲第 1 代幾乎是無限的。

叢集可以存取不同儲存體帳戶的組合。 典型的範例包括：

* 當資料量很可能超過單一 blob 儲存體容器的儲存體容量。
* 當 blob 容器的存取速率可能會超過節流發生的閾值。
* 如果要創建數據,已上傳到群集可用的 Blob 容器。
* 當您基於安全性的原因或要簡化系統管理而需要將儲存體的不同部分進行隔離。

為提升效能，每個儲存體帳戶僅使用一個容器。

## <a name="choose-a-cluster-type"></a>選擇叢集類型

叢集類型確定 HDInsight 群集配置為運行的工作負載。 類型包括[阿帕奇哈多普](./hadoop/apache-hadoop-introduction.md),[阿帕奇風暴](./storm/apache-storm-overview.md),[阿帕奇卡夫卡](./kafka/apache-kafka-introduction.md),或[阿帕奇火花](./spark/apache-spark-overview.md)。 如需可用叢集類型的詳細說明，請參閱 [Azure HDInsight 簡介](hdinsight-overview.md#cluster-types-in-hdinsight)。 每個叢集類型都有特定的部署拓撲，其中包含節點數目和大小的需求。

## <a name="choose-the-vm-size-and-type"></a>選擇 VM 大小與類型

每個叢集類型都具有一組節點類型，且每個節點類型都有其 VM 大小和類型的特定選項。

若要判斷您應用程式的最佳叢集大小，您可以效能評定叢集容量，並依指示增加大小。 例如，您可以使用模擬的工作負載或 Canary 查詢**。 在不同的大小群集上運行類比工作負載。 逐漸增加大小,直到達到預期性能。 金絲雀查詢可以定期插入其他生產查詢中,以顯示群集是否有足夠的資源。

有關如何為工作負荷選擇正確的 VM 系列的詳細資訊,請參閱[為群集選擇正確的 VM 大小](hdinsight-selecting-vm-size.md)。

## <a name="choose-the-cluster-scale"></a>選擇叢集縮放比例

叢集的縮放比例取決於其 VM 節點的數量。 對於所有群集類型,有具有特定比例的節點類型,以及支援橫向擴展的節點類型。例如,群集可能只需要三個[Apache ZooKeeper](https://zookeeper.apache.org/)節點或兩個頭節點。 以分散式方式進行數據處理的工作節點受益於其他輔助角色節點。

根據群集類型,增加輔助節點的數量會增加額外的計算容量(如更多內核)。 更多的節點將增加整個群集所需的總記憶體,以支援正在處理的數據的記憶體中存儲。 與選擇 VM 大小和類型一樣,通常根據經驗選擇正確的群集比例。 使用類比工作負載或金絲雀查詢。

您可以橫向擴展群集以滿足峰值負載需求。 然後,當不再需要這些額外的節點時,將其縮減為縮減。 [自動縮放功能](hdinsight-autoscale-clusters.md)允許您根據預先確定的指標和時間自動縮放群集。 有關手動延伸叢集的詳細資訊,請參閱[縮放 HDInsight 叢集](hdinsight-scaling-best-practices.md)。

### <a name="cluster-lifecycle"></a>叢集生命週期

群集的存留期需要付費。 如果只有特定時間需要群組,[請使用 Azure 資料工廠建立此一個需要叢集](hdinsight-hadoop-create-linux-clusters-adf.md)。 您也可以建立 PowerShell 指令碼來佈建和刪除您的叢集，並使用 [Azure 自動化](https://azure.microsoft.com/services/automation/)來排程這些指令碼。

> [!NOTE]  
> 刪除叢集時，也會刪除其預設的 Hive 中繼存放區。 若要保存中繼存放區以進行下一次重新建立叢集，請使用外部中繼資料存放區，例如 Azure 資料庫或 [Apache Oozie](https://oozie.apache.org/)。
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>找出叢集作業錯誤

有時,由於並行執行多個映射並減少多節點群集上的元件,可能會發生錯誤。 為了幫助隔離問題,請嘗試分散式測試。 在單個輔助節點群集上運行併發多個作業。 然後展開此方法,在包含多個節點的群集上同時運行多個作業。 要在 Azure 中建立單節點 HDInsight 群集*`Custom(size, settings, apps)`*,在門戶中預配新群集時,請使用 選項,並在**群集大小**部分中對*輔助節點數*使用值 1。

## <a name="quotas"></a>配額

決定您的目標叢集 VM 大小、縮放比例和類型之後，請檢查您訂用帳戶的目前配額容量限制。 達到配額限制時,無法部署新群集。 或者通過添加更多輔助節點來擴展現有群集。 唯一的配額限制是 CPU 核心配額，其存在於每個訂用帳戶區域層級。 例如，您的訂用帳戶在美國東部區域可能有 30 個核心限制。

要檢查可用的內核,請執行以下步驟:

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 導航到 HDInsight 群集的 **「概述」** 頁面。
3. 在左側功能表上,選擇 **「配額限制**」。。

   該頁顯示正在使用的內核數、可用內核數和總內核數。

如果您需要要求增加配額，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在頁面左下角選擇 **「説明 + 支援**」。
1. 選取 [新增支援要求]****。
1. 在 [新增支援要求]**** 頁面的 [基本]**** 索引標籤上，選取下列選項：

   - **問題型態**:**服務和訂閱限制(配額)**
   - **訂閱**:要修改的訂閱
   - **配額型態**: **HDInsight**

     ![建立支援要求以提高 HDInsight 核心配額](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. 選擇**下一步:解決方案 >>**。
1. 在 **「詳細資訊」** 頁上,輸入問題說明,選擇問題的嚴重性、首選的聯繫人方法和其他必填字段。
1. 選擇 **「下一步」:查看 = 建立 >>**。
1. 在 [檢閱 + 建立]**** 索引標籤中，選取 [建立]****。

> [!NOTE]  
> 如果需要在私人區域中新增 HDInsight 核心配額，請[提交允許清單要求](https://aka.ms/canaryintwhitelist)。

您可以[連絡支援人員以要求增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

有一些固定的配額限制。 例如,單個 Azure 訂閱最多只能有 10,000 個內核。 如需這些限制的詳細資料，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Hadoop、Spark、Kafka 等在 HDInsight 中設置群集](hdinsight-hadoop-provision-linux-clusters.md):瞭解如何在 HDInsight 中設置和配置群集。
* [監視叢集效能](hdinsight-key-scenarios-to-monitor.md)：了解重要情節，以監視可能會影響叢集容量的 HDInsight 叢集。
