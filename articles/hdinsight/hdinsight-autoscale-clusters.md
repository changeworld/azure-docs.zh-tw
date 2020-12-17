---
title: 自動調整 Azure HDInsight 叢集規模
description: 使用自動調整功能，根據排程或效能計量自動調整 Azure HDInsight 叢集規模。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 2b23b4256e79723ce0b5edafd59186dc345eb791
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629250"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自動調整 Azure HDInsight 叢集規模

Azure HDInsight 的免費自動調整功能可根據先前設定的準則，自動增加或減少叢集中的背景工作節點數目。 自動調整功能的運作方式是根據效能度量或相應增加和相應減少作業的排程，在預設限制內調整節點數目。

## <a name="how-it-works"></a>運作方式

自動調整功能會使用兩種類型的條件來觸發調整事件：各種叢集效能計量的臨界值 (稱為以 *負載為* 基礎的調整) 和以時間為基礎的觸發程式 (稱為以 *排程為基礎的調整*) 。 以負載為基礎的調整會變更您叢集中的節點數目（在您設定的範圍內），以確保最佳的 CPU 使用率，並將執行成本降至最低。 以排程為基礎的規模調整會根據向上延展和相應減少作業的排程，變更叢集中的節點數目。

下列影片概述自動調整所能解決的挑戰，以及它如何協助您使用 HDInsight 來控制成本。

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>選擇以負載為基礎或以排程為基礎的調整

選擇調整類型時，請考慮下列因素：

* 負載變異數：叢集的負載是否會在特定的時間在特定時間遵循一致的模式？ 如果沒有，以負載為基礎的排程是較佳的選項。
* SLA 需求：自動調整規模調整是被動的，而不是預測性。 當負載開始增加時，以及當叢集必須達到目標大小時，是否有足夠的延遲？ 如果有嚴格的 SLA 需求，且負載是固定的已知模式，則 [排程依據] 是較佳的選項。

### <a name="cluster-metrics"></a>群集計量

自動調整會持續監視叢集，並收集下列計量：

|計量|描述|
|---|---|
|擱置中的 CPU 總計|開始執行所有擱置中容器時所需的核心總數。|
|擱置中的記憶體總計|開始執行所有擱置中容器時所需的記憶體總計 (MB)。|
|可用的 CPU 總計|作用中背景工作節點上所有未使用核心的總和。|
|可用的記憶體總計|作用中背景工作節點上所有未使用記憶體的總和 (MB)。|
|每個節點的已使用記憶體|背景工作節點上的負載。 已使用 10 GB 記憶體的背景工作節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。|
|每個節點的應用程式主機數目|背景工作節點上執行的應用程式主機 (AM) 容器數目。 裝載兩個 AM 容器的背景工作節點會被視為比裝載零 AM 容器的背景工作節點更重要。|

系統每隔 60 秒就會檢查上述計量。 您可以使用任何一種計量來設定叢集的調整作業。

### <a name="load-based-scale-conditions"></a>以負載為基礎的調整條件

當偵測到下列情況時，自動調整將會發出調整要求：

|相應增加|縮小|
|---|---|
|總等待 CPU 大於3分鐘的可用 CPU 總計。|擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。|
|暫止記憶體總計大於可用記憶體總計超過3分鐘。|擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。|

相應增加時，自動調整會發出相應增加要求，以新增所需的節點數目。 相應增加是根據所需的新背景工作節點數目，以符合目前的 CPU 和記憶體需求。

針對相應減少，自動調整會發出要求以移除特定數目的節點。 相應減少是根據每個節點的 AM 容器數目來調整。 以及目前的 CPU 和記憶體需求。 服務也會根據目前的作業執行狀況，偵測哪些節點是移除的候選項目。 縮小作業會先會將節點解除委任，再將其從叢集中移除。

### <a name="cluster-compatibility"></a>叢集相容性

> [!Important]
> Azure HDInsight 自動調整功能已於 2019 年 11 月 7 日正式發行，適用於 Spark 和 Hadoop 叢集，並包含功能預覽版本中未提供的改善項目。 如果您在 2019 年 11 月 7 日之前已建立 Spark 叢集，並想要在叢集上使用自動調整功能，建議的路徑是建立新叢集，並在新叢集上啟用自動調整。
>
> Interactive Query (LLAP) 的自動調整已于2020年8月27日，于 HDI 4.0 正式推出。 HBase 叢集仍處於預覽狀態。 自動調整僅適用於 Spark、Hadoop、Interactive Query 以及 HBase 叢集。

下表說明與自動調整功能相容的叢集類型和版本。

| 版本 | Spark | Hive | 互動式查詢 | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 沒有 ESP 的 HDInsight 3。6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 沒有 ESP 的 HDInsight 4。0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 使用 ESP 的 HDInsight 3。6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 使用 ESP 的 HDInsight 4。0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |

\* HBase 叢集只能針對以排程為基礎的調整進行設定，而不是以負載為基礎。

## <a name="get-started"></a>開始使用

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用以負載為基礎的自動調整來建立叢集

若要啟用以負載為基礎的自動調整功能，請在一般叢集建立程式中完成下列步驟：

1. 在 [設定 **+ 定價** ] 索引標籤上，選取 [ **啟用自動** 調整] 核取方塊。
1. 選取 [**自動調整類型**] 下 **的 [載入**]。
1. 輸入下列屬性的預期值：  

    * 背景 **工作節點** 的初始 **節點數目**。
    * 背景工作節點的 **最小** 數目。
    * 背景工作節點的數目 **上限**。

    ![啟用背景工作節點以負載為基礎的自動調整](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值會定義建立叢集時的初始大小。 背景工作節點的最小數目應設定為三個以上。 將您的叢集調整至少於三個節點可能會導致因為檔案複寫不足而導致它卡在安全模式中。  如需詳細資訊，請參閱 [進入安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用以排程為基礎的自動調整來建立叢集

若要啟用以排程為基礎的自動調整功能，請在一般叢集建立程式中完成下列步驟：

1. 在 [設定 **+ 定價** ] 索引標籤上，選取 [ **啟用自動** 調整] 核取方塊。
1. 輸入背景 **工作節點** 的 **節點數目**，以控制擴充叢集的限制。
1. 在 [**自動調整類型**] 下，選取 [**排程依據**] 選項。
1. 選取 [設定] 以開啟 [**自動** 調整 **設定**] 視窗。
1. 選取您的時區，然後按一下 [ **+ 新增條件**]
1. 選取要套用新條件的星期幾。
1. 編輯條件應生效的時間，以及應調整叢集的節點數目。
1. 視需要新增更多條件。

    ![啟用背景工作節點以排程為基礎的建立](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

節點數目必須介於3到您輸入的背景工作角色節點數目上限之間，才能新增條件。

### <a name="final-creation-steps"></a>最後的建立步驟

從 [ **節點大小**] 下的下拉式清單中選取 vm，以選取背景工作節點的 vm 類型。 選擇每個節點類型的 VM 類型之後，您就可以看到整個叢集的預估成本範圍。 調整 VM 類型以符合您的預算。

![啟用背景工作角色節點以排程為基礎的自動調整節點大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

您的訂用帳戶擁有每個區域的容量配額。 前端節點的核心總數和最大背景工作節點數不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]  
> 如果您超過總核心配額限制，您會收到錯誤訊息，指出「最大節點超過此區域中的可用核心數，請選擇其他區域或聯繫支援人員以增加配額」。

如需使用 Azure 入口網站建立 HDInsight 叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

#### <a name="load-based-autoscaling"></a>以負載為基礎的自動調整

您可以 `autoscale` 使用屬性將節點新增至 `computeProfile`  >  `workernode` 區段， `minInstanceCount` 並使用 `maxInstanceCount` 下列 json 程式碼片段，建立具有負載型自動調整 Azure Resource Manager 範本的 HDInsight 叢集。 如需完整的 Resource Manager 範本，請參閱 [快速入門範本：部署 Spark 叢集，並啟用以負載為基礎的自動](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased)調整。

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>以排程為基礎的自動調整

您可以藉由將節點加入至區段，以排程為基礎的自動調整 Azure Resource Manager 範本來建立 HDInsight 叢集 `autoscale` `computeProfile`  >  `workernode` 。 `autoscale`節點包含， `recurrence` 其中具有 `timezone` 和，可 `schedule` 描述何時會進行變更。 如需完整的 Resource Manager 範本，請參閱 [使用以排程為基礎的自動調整功能來部署 Spark](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased)叢集。

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>針對正在執行的叢集啟用和停用自動調整

#### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

若要在執行中叢集上啟用自動 **調整** ，請在 [ **設定**] 底下選取 [叢集大小] 然後選取 [ **啟用自動** 調整]。 選取您想要的自動調整類型，並輸入以負載為基礎或以排程為基礎的調整選項。 最後，選取 [儲存]。

![啟用執行叢集中的背景工作節點排程型自動調整](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>使用 REST API

若要使用 REST API 在執行中的叢集上啟用或停用自動調整，請對自動調整端點提出 POST 要求：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

在要求承載中使用適當的參數。 以下的 json 承載可以用來啟用自動調整。 使用裝載 `{autoscale: null}` 來停用自動調整。

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

請參閱上一節，以瞭解如何 [啟用以負載為基礎的自動](#load-based-autoscaling) 調整，以取得所有承載參數的完整描述。

## <a name="monitoring-autoscale-activities"></a>監視自動調整活動

### <a name="cluster-status"></a>叢集狀態

Azure 入口網站中列出的叢集狀態可協助您監視自動調整活動。

![啟用背景工作節點以負載為基礎的自動調整叢集狀態](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

下列清單會說明您可能會看到的所有叢集狀態訊息。

| 叢集狀態 | 描述 |
|---|---|
| 執行中 | 叢集正常運作。 所有先前的自動調整活動都已順利完成。 |
| 更新  | 正在更新叢集自動調整設定。  |
| HDInsight 設定  | 叢集擴大或縮小作業正在進行中。  |
| 更新錯誤  | HDInsight 在自動調整設定更新期間發生問題。 客戶可以選擇重試更新或停用自動調整。  |
| 錯誤  | 叢集發生問題，因此無法使用。 刪除此叢集，並建立一個新叢集。  |

若要查看叢集中目前的節點數目，請移至叢集的 [**總覽**] 頁面上的 [叢集 **大小**] 圖表。 或選取 [**設定**] 下的 [叢集 **大小**]。

### <a name="operation-history"></a>作業歷程記錄

您可以在叢集計量中查看叢集的相應增加和相應減少歷程記錄。 您也可以列出過去一天、一周或其他期間的所有規模調整動作。

選取 [**監視** 中的 **計量**]。 然後，**從 [計量**] 下拉式方塊中選取 [**新增** 計量和作用中背景 **工作角色數目**]。 選取右上方的按鈕來變更時間範圍。

![啟用背景工作角色節點以排程為基礎的自動調整度量](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>最佳作法

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>考慮擴大和縮小作業的延遲

調整作業可能需要10到20分鐘的時間才能完成。 設定自訂排程時，請規劃此延遲。 例如，如果您需要在上午9:00 將叢集大小設定為20，請將排程觸發程式設定為較早的時間（例如 8:30 AM），以便調整作業已完成 9:00 AM。

### <a name="prepare-for-scaling-down"></a>準備相應減少

在叢集相應減少進程期間，自動調整會解除節點以符合目標大小。 如果工作在這些節點上執行，自動調整會等候，直到 Spark 和 Hadoop 叢集的工作完成為止。 因為每個背景工作節點也會在 HDFS 中擔任角色，所以暫存資料會移至其餘的節點。 請確定剩餘的節點有足夠的空間來裝載所有暫存資料。

正在執行的作業將會繼續。 暫止的工作會等候排程的可用背景工作節點較少。

### <a name="be-aware-of-the-minimum-cluster-size"></a>請留意最小的叢集大小

請勿將您的叢集調整到少於三個節點。 將您的叢集調整至少於三個節點可能會導致因為檔案複寫不足而導致它卡在安全模式中。 如需詳細資訊，請參閱 [進入安全模式](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="increase-the-number-of-mappers-and-reducers"></a>增加對應程式數目和歸納器的數目

Hadoop 叢集的自動調整也會監視 HDFS 的使用量。 如果 HDFS 忙碌中，則會假設叢集仍然需要目前的資源。 如果查詢中包含大量資料，您可以增加對應程式數目和歸納器的數目，以增加平行處理原則並加速 HDFS 作業。 如此一來，當有額外的資源時，將會觸發適當的相應縮小。 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>針對尖峰使用量案例，設定 Hive 設定的並行查詢總數上限

自動調整事件不會在 Ambari 中變更 Hive 設定的 *並行查詢總數上限* 。 這表示在任何時間點，Hive Server 2 互動式服務只能處理指定數目的並行查詢，即使 Interactive Query 的守護程式計數是根據負載和排程相應增加和減少。 一般建議是針對尖峰使用量案例設定此設定，以避免手動介入。

但是，如果只有少量的背景工作節點，而且並行查詢總數的值設定太高，您可能會遇到 Hive Server 2 重新開機失敗。 您至少需要可以容納指定 Tez Ams 數量的背景工作節點數目下限， (等於最大並行查詢設定) 總數。 

## <a name="limitations"></a>限制

### <a name="node-label-file-missing"></a>遺漏節點標籤檔案

HDInsight 自動調整會使用節點標籤檔案來判斷節點是否已準備好執行工作。 節點標籤檔案儲存在具有三個複本的 HDFS 上。 如果叢集大小大幅縮減，而且有大量的暫存資料，則可能會有很少的三個複本卸載。 如果發生這種情況，叢集會進入錯誤狀態。

### <a name="interactive-query-daemons-count"></a>Interactive Query 的守護程式計數

在啟用自動調整的 Interactive Query 叢集的情況下，自動調整/減少事件也會將 Interactive Query 的守護程式數目相應增加/減少為作用中背景工作節點的數目。 守護程式數目的變更不會保存在 Ambari 的設定中 `num_llap_nodes` 。 如果 Hive 服務是以手動方式重新開機，則會根據 Ambari 中的設定來重設 Interactive Query 的守護程式數目。

如果以手動方式重新開機 Interactive Query 服務，您需要以手動方式將設定變更 `num_llap_node` (在 *Advanced hive-Interactive-env* 下執行 Hive Interactive Query 背景工作角色，以符合目前作用中背景工作角色節點計數所需的節點 (數目) 。

## <a name="next-steps"></a>下一步

閱讀[調整指導方針](hdinsight-scaling-best-practices.md)以手動調整叢集的相關指導方針
