---
title: 自動調整 Azure HDInsight 叢集規模
description: 使用 Azure HDInsight 自動調整功能自動執行 Apache Hadoop 擴充叢集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: cc294eb1bdfd4a6a8c6ad001c007f83a10983644
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185803"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自動調整 Azure HDInsight 叢集規模

Azure HDInsight 的免費自動調整功能可以根據先前設定的準則，自動增加或減少叢集中的背景工作節點數目。 您可以在叢集建立期間設定最小和最大節點數目、使用日期時間排程或特定效能計量來建立調整準則，而 HDInsight 平臺會執行其餘工作。

## <a name="how-it-works"></a>運作方式

自動調整功能使用兩種類型的條件來觸發調整事件：各種叢集效能標準的臨界值 (稱為*載入式調整*) 和以時間為基礎的觸發程式 (稱為以*排程為基礎的調整*) 。 以負載為基礎的調整會在您設定的範圍內變更叢集中的節點數目，以確保最佳的 CPU 使用量並將執行成本降至最低。 以排程為基礎的調整會根據您與特定日期和時間相關聯的作業，變更叢集中的節點數目。

下列影片概述自動調整可解決的挑戰，以及它如何協助您使用 HDInsight 控制成本。


> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>選擇以負載為基礎或以排程為基礎的調整

選擇調整類型時，請考慮下列因素：

* 負載變異數：叢集的負載是否會在特定時間的特定時間遵循一致的模式？ 如果沒有，以負載為基礎的排程是較好的選擇。
* SLA 需求：自動調整規模是回應性，而不是預測。 負載開始增加的時間和叢集必須處於其目標大小之間是否有足夠的延遲？ 如果有嚴格的 SLA 需求，而負載是固定的已知模式，則 [以排程為基礎] 是較好的選擇。

### <a name="cluster-metrics"></a>叢集計量

自動調整會持續監視叢集，並收集下列計量：

|計量|描述|
|---|---|
|擱置中的 CPU 總計|開始執行所有擱置中容器時所需的核心總數。|
|擱置中的記憶體總計|開始執行所有擱置中容器時所需的記憶體總計 (MB)。|
|可用的 CPU 總計|作用中背景工作節點上所有未使用核心的總和。|
|可用的記憶體總計|作用中背景工作節點上所有未使用記憶體的總和 (MB)。|
|每個節點的已使用記憶體|背景工作節點上的負載。 已使用 10 GB 記憶體的背景工作節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。|
|每個節點的應用程式主機數目|背景工作節點上執行的應用程式主機 (AM) 容器數目。 裝載兩個 AM 容器的背景工作節點會被視為比裝載 zero AM 容器的背景工作節點更重要。|

系統每隔 60 秒就會檢查上述計量。 您可以使用任何一種計量來設定叢集的調整作業。

### <a name="load-based-scale-conditions"></a>以負載為基礎的調整條件

當偵測到下列情況時，自動調整將會發出調整要求：

|相應增加|縮小|
|---|---|
|擱置中的 CPU 總計大於可用的 CPU 總計超過3分鐘。|擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。|
|暫止的記憶體總計大於可用的記憶體總計超過3分鐘。|擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。|

針對相應增加，自動調整會發出相應增加要求，以新增所需的節點數目。 相應增加是根據所需的新背景工作節點數目，以符合目前的 CPU 和記憶體需求。

針對相應減少，自動調整會發出移除特定節點數目的要求。 相應減少是以每個節點的 AM 容器數目為依據。 以及目前的 CPU 和記憶體需求。 服務也會根據目前的作業執行狀況，偵測哪些節點是移除的候選項目。 縮小作業會先會將節點解除委任，再將其從叢集中移除。

### <a name="cluster-compatibility"></a>叢集相容性

> [!Important]
> Azure HDInsight 自動調整功能已於 2019 年 11 月 7 日正式發行，適用於 Spark 和 Hadoop 叢集，並包含功能預覽版本中未提供的改善項目。 如果您在 2019 年 11 月 7 日之前已建立 Spark 叢集，並想要在叢集上使用自動調整功能，建議的路徑是建立新叢集，並在新叢集上啟用自動調整。
>
> 適用於 Interactive Query (LLAP) 和 HBase 叢集的自動調整仍處於預覽狀態。 自動調整僅適用於 Spark、Hadoop、Interactive Query 以及 HBase 叢集。

下表描述與自動調整功能相容的叢集類型和版本。

| 版本 | Spark | Hive | LLAP | HBase | Kafka | 暴風雨 | ML |
|---|---|---|---|---|---|---|---|
| 不含 ESP 的 HDInsight 3。6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 不含 ESP 的 HDInsight 4。0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 使用 ESP 的 HDInsight 3。6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 使用 ESP 的 HDInsight 4。0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |

\*HBase 叢集只能針對以排程為基礎的調整進行設定，而非以載入為基礎。

## <a name="get-started"></a>開始使用

### <a name="create-a-cluster-with-load-based-autoscaling"></a>建立具有以負載為基礎之自動調整的叢集

若要啟用以負載為基礎的自動調整功能，請在一般叢集建立程式中完成下列步驟：

1. 在 [設定 **+ 定價**] 索引標籤上，選取 [**啟用自動**調整] 核取方塊。
1. 選取 [**自動調整類型**] 下**的 [載入型**]。
1. 輸入下列屬性的預期值：  

    * 背景**工作節點**的初始**節點數目**。
    * 背景工作節點的**最小**數目。
    * 背景工作節點的**最大**數目。

    ![啟用背景工作節點以負載為基礎的自動調整](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值會定義叢集建立時的初始大小。 背景工作節點的最小數目應該設定為三個或多個。 將您的叢集調整至少於三個節點，可能會導致它因為檔案複寫不完整而停滯于安全模式。  如需詳細資訊，請參閱[在安全模式中停滯](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>建立具有以排程為基礎之自動調整的叢集

若要啟用以排程為基礎的自動調整功能，請在一般叢集建立程式中完成下列步驟：

1. 在 [設定 **+ 定價**] 索引標籤上，選取 [**啟用自動**調整] 核取方塊。
1. 輸入背景**工作節點**的**節點數目**，以控制相應增加叢集的限制。
1. 選取 [**自動調整類型**] 下的 [**排程依據**] 選項。
1. 選取 [**設定**] 以開啟 [**自動**調整規模設定] 視窗。
1. 選取您的時區，然後按一下 [ **+ 新增條件**]
1. 選取應套用新條件的周間日。
1. 編輯條件應生效的時間，以及應調整叢集的節點數目。
1. 視需要新增更多條件。

    ![啟用背景工作節點以排程為基礎的建立](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

節點數目必須介於3到您在新增條件之前輸入的背景工作節點數目上限。

### <a name="final-creation-steps"></a>最後的建立步驟

從 [**節點大小**] 下的下拉式清單中選取 vm，以選取背景工作角色節點的 vm 類型。 為每個節點類型選擇 VM 類型之後，您可以看到整個叢集的預估成本範圍。 調整 VM 類型以符合您的預算。

![啟用背景工作節點以排程為基礎的自動調整節點大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

您的訂用帳戶擁有每個區域的容量配額。 前端節點和最大背景工作節點的核心總數不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]  
> 如果您超過總核心配額限制，將會收到錯誤訊息，指出「最大節點超過此區域中的可用核心數，請選擇另一個區域，或聯絡支援人員增加配額」。

如需使用 Azure 入口網站建立 HDInsight 叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

#### <a name="load-based-autoscaling"></a>以負載為基礎的自動調整

您可以透過將節點加入至具有屬性的區段，以使用載入型自動調整 Azure Resource Manager 範本來建立 HDInsight 叢集， `autoscale` `computeProfile`  >  `workernode` `minInstanceCount` `maxInstanceCount` 如下列 json 程式碼片段所示。 如需完整的 resource manager 範本，請參閱[快速入門範本：部署已啟用 Loadbased 自動調整的 Spark](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased)叢集。

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

您可以藉由將節點新增至區段，以排程為基礎的自動調整 Azure Resource Manager 範本來建立 HDInsight 叢集 `autoscale` `computeProfile`  >  `workernode` 。 `autoscale`節點包含 `recurrence` 具有 `timezone` 和 `schedule` 的，可描述何時會進行變更。 如需完整的 resource manager 範本，請參閱[部署 Spark 叢集並啟用以排程為基礎的自動](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased)調整。

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

若要在執行中的叢集上啟用自動調整，請選取 [**設定**] 底下的 [叢集**大小** 然後選取 [**啟用自動**調整]。 選取您想要的自動調整類型，並輸入以負載為基礎或以排程為基礎的縮放選項。 最後，選取 [儲存]****。

![啟用以背景工作節點排程為基礎的自動調整執行叢集](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>使用 REST API

若要使用 REST API 在執行中的叢集上啟用或停用自動調整，請對自動調整端點提出 POST 要求：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

在要求承載中使用適當的參數。 下列 json 承載可用來啟用自動調整。 使用承載 `{autoscale: null}` 來停用自動調整。

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

請參閱上一節關於[啟用以負載為基礎的自動](#load-based-autoscaling)調整，以取得所有裝載參數的完整描述。

## <a name="monitoring-autoscale-activities"></a>監視自動調整活動

### <a name="cluster-status"></a>叢集狀態

Azure 入口網站中所列的叢集狀態可協助您監視自動調整活動。

![啟用背景工作節點負載型自動調整叢集狀態](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

下列清單會說明您可能會看到的所有叢集狀態訊息。

| 叢集狀態 | 描述 |
|---|---|
| 執行中 | 叢集正常運作。 所有先前的自動調整活動都已順利完成。 |
| 更新  | 正在更新叢集自動調整設定。  |
| HDInsight 設定  | 叢集擴充或相應減少作業進行中。  |
| 更新錯誤  | HDInsight 在自動調整設定更新期間遇到問題。 客戶可以選擇重試更新或停用自動調整。  |
| 錯誤  | 叢集發生問題，因此無法使用。 刪除此叢集並建立一個新叢集。  |

若要查看您叢集中目前的節點數目，請移至您叢集的 [**總覽**] 頁面上的 [叢集**大小**] 圖表。 或選取 [**設定**] 底下的 [叢集**大小**]。

### <a name="operation-history"></a>作業歷程記錄

您可以將叢集相應增加和相應減少歷程記錄視為叢集計量的一部分來查看。 您也可以列出過去一天、一周或其他一段時間的所有調整動作。

選取 [**監視**] 下的 [**計量**]。 然後**從 [計量**] 下拉式清單方塊中選取 [**新增**計量和作用中背景**工作的數目**]。 選取右上方的按鈕以變更時間範圍。

![啟用以背景工作節點排程為基礎的自動調整度量](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="other-considerations"></a>其他考量

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考量擴大或縮小作業的延遲

調整作業可能需要10到20分鐘的時間才能完成。 設定自訂排程時，請為此延遲進行規劃。 例如，如果您需要在上午9:00 的叢集大小為20，請將排程觸發程式設定為較早的時間，例如 8:30 AM，讓調整作業已完成 9:00 AM。

### <a name="preparation-for-scaling-down"></a>縮小的準備

在叢集相應減少程式期間，自動調整會將節點解除委任以符合目標大小。 如果工作是在這些節點上執行，自動調整將會等候，直到作業完成為止。 由於每個背景工作節點也會在 HDFS 中提供角色，因此暫存資料將會移至其餘的節點。 因此，您應該確定其餘節點上有足夠的空間來裝載所有的暫存資料。

執行中的作業將會繼續。 暫止的工作將會等待較少的可用背景工作節點進行排程。

### <a name="minimum-cluster-size"></a>最小叢集大小

請不要將叢集調整為少於三個節點。 將您的叢集調整至少於三個節點，可能會導致它因為檔案複寫不完整而停滯于安全模式。  如需詳細資訊，請參閱[在安全模式中停滯](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

## <a name="next-steps"></a>後續步驟

閱讀[調整指導方針](hdinsight-scaling-best-practices.md)中的手動調整叢集的指導方針
