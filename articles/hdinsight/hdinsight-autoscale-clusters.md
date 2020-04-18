---
title: 自動調整 Azure HDInsight 叢集規模
description: 使用 Azure HDInsight 自動縮放功能自動使用 Apache Hadoop 調整叢集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4f9b43b6f800bb47942ccc00fee0fac4536d2ec0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640587"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自動調整 Azure HDInsight 叢集規模

> [!Important]
> Azure HDInsight 自動縮放功能於 2019 年 11 月 7 日發布,適用於 Spark 和 Hadoop 群集,並包含該功能預覽版本中未提供的改進。 如果您在 2019 年 11 月 7 日之前創建了 Spark 群集,並希望在群集上使用自動縮放功能,則建議的路徑是創建新群集,並在新群集上啟用自動縮放。
>
> 互動式查詢 (LLAP) 和 HBase 群集的自動縮放仍處於預覽階段。 自動縮放僅在 Spark、Hadoop、互動式查詢和 HBase 叢集上可用。

Azure HDInsight 的群集自動縮放功能可自動縮放群集中輔助節點的數量。 群集中的其他類型的節點當前無法縮放。  在建立新的 HDInsight 叢集時，可以設定最小和最大的背景工作節點數目。 然後,自動縮放可監視分析負載的資源需求,並向上或向下縮放輔助節點的數量。 此功能不收取額外費用。

## <a name="cluster-compatibility"></a>叢集相容性

下表描述了與自動縮放功能相容的群集類型和版本。

| 版本 | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 無 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| HDInsight 4.0 無 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| HDInsight 3.6 與 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| HDInsight 4.0 與 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |

\*HBase 群集只能配置為基於計劃的縮放,不能基於負載。

## <a name="how-it-works"></a>運作方式

您可以為 HDInsight 群集選擇基於負載的縮放或基於計畫的縮放。 基於負載的擴展會更改群集中設置範圍內的節點數,以確保最佳 CPU 使用率並最大限度降低運行成本。

基於計劃的擴展會根據在特定時間生效的條件更改群集中的節點數。 這些條件將群集縮放為預期的節點數。

### <a name="metrics-monitoring"></a>計量監視

自動調整會持續監視叢集，並收集下列計量：

|計量|描述|
|---|---|
|總掛起 CPU|開始執行所有擱置中容器時所需的核心總數。|
|總掛起記憶體|開始執行所有擱置中容器時所需的記憶體總計 (MB)。|
|總免費 CPU|作用中背景工作節點上所有未使用核心的總和。|
|總可用記憶體|作用中背景工作節點上所有未使用記憶體的總和 (MB)。|
|每個節點使用的記憶體|背景工作節點上的負載。 已使用 10 GB 記憶體的背景工作節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。|
|每個節點的應用程式母版數|背景工作節點上執行的應用程式主機 (AM) 容器數目。 承載兩個 AM 容器的工作節點被認為比承載零 AM 容器的工作節點更重要。|

系統每隔 60 秒就會檢查上述計量。 自動縮放根據這些指標做出決策。

### <a name="load-based-scale-conditions"></a>基於負載的縮放條件

當偵測到以下條件時,自動縮放將發出縮放請求:

|相應增加|縮小|
|---|---|
|總掛起的 CPU 大於總可用 CPU 超過 3 分鐘。|擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。|
|總掛起記憶體大於總可用記憶體超過3分鐘。|擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。|

對於放大,自動縮放發出放大請求以添加所需的節點數。 擴展基於滿足當前 CPU 和記憶體要求所需的新工作節點數。

對於縮減,自動縮放發出刪除一定數量的節點的請求。 縮減基於每個節點的 AM 容器數。 以及當前的 CPU 和記憶體要求。 該服務還會根據當前作業執行檢測哪些節點是要刪除的候選節點。 縮減操作首先將節點分離,然後從群集中刪除它們。

## <a name="get-started"></a>開始使用

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基於負載的自動縮放建立叢集

要啟用基於負載的縮放自動縮放功能,在常規群集建立過程中完成以下步驟:

1. 在「**設定 + 定價**」 選項卡上,選擇「**啟用自動縮放**」複選框。
1. 在**自動縮放型態**下選擇**以負載**。
1. 輸入以下屬性的預期值:  

    * **次要節點**的初始**節點數**。
    * 輔助節點的**最小**數量。
    * **輔助**節點的最大數量。

    ![開啟基於工作節點負載的自動縮放](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值定義創建群集時的初始大小。 輔助節點的最小數量應設置為三個或更多。 將群集擴展到少於三個節點可能會導致群集由於檔複製不足而陷入安全模式。  有關詳細資訊,請參閱[卡在安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基於計畫的自動縮放建立叢集

要啟用基於計畫的縮放的自動縮放功能,在常規群集建立過程中完成以下步驟:

1. 在 **「設定 + 定價**」選項卡上,選擇 **「啟用自動縮放**」複選框。
1. 輸入**輔助角色節點****的節點數**,該節點控制向上擴展群集的限制。
1. 選擇「**自動縮放類型**」**的選項**。
1. 選擇 **「設定**」以開啟**自動縮放設定**視窗。
1. 選擇您的時區,然後按下 **+ 新增條件**
1. 選擇新條件應應用於的一周中的天數。
1. 編輯條件應生效的時間以及群集應縮放到的節點數。
1. 如果需要,添加更多條件。

    ![開啟以工作節點的計劃建立](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

節點數必須介於添加條件之前輸入的最大輔助節點數 3 和最大輔助節點數之間。

### <a name="final-creation-steps"></a>最終建立步驟

通過在 **'節點大小**'下的下拉清單中選擇 VM,為輔助節點選擇 VM 類型。 為每個節點類型選擇 VM 類型後,可以看到整個群集的估計成本範圍。 調整 VM 類型以適合您的預算。

![開啟基於工作節點的自動縮放節點大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

您的訂用帳戶擁有每個區域的容量配額。 頭節點和最大輔助節點的內核總數不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]  
> 如果超過總內核配額限制,您將收到一條錯誤消息,指出「最大節點超出了該區域中的可用內核,請選擇其他區域或與支援部門聯繫以增加配額。

如需使用 Azure 入口網站建立 HDInsight 叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

#### <a name="load-based-autoscaling"></a>基於負載的自動縮放

`autoscale`您可以透過將節點添加`computeProfile` > `workernode`到具有`minInstanceCount`屬性的部分`maxInstanceCount`, 並如下面的 json 代碼段所示,使用基於負載的 Azure 資源管理器樣本創建 HDInsight 群集。

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

#### <a name="schedule-based-autoscaling"></a>以計畫的自動縮放

通過將`autoscale`節點添加到`computeProfile` > `workernode`分區,可以使用基於計劃的 Azure 資源管理器範本創建 HDInsight 叢集。 節點`autoscale`包含`recurrence`具有`timezone``schedule`和的 , 描述何時發生變更。

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
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>針對正在執行的叢集啟用和停用自動調整

#### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

要在執行的叢集上開啟自動縮放,請在 **「設定」** 選擇**叢集大小**。 然後選擇 **「啟用自動縮放**」。 選擇所需的自動縮放類型,並輸入基於負載或基於計劃的縮放選項。 最後，選取 [儲存]****。

![開啟基於工作節點計劃的自動縮放執行叢集](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>使用 REST API

要使用 REST API 在執行的叢集上啟用或關閉自動縮放,請向自動縮放終結點發出 POST 請求:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

在請求負載中使用適當的參數。 下面的 json 有效負載可用於啟用自動縮放。 使用有效負載`{autoscale: null}`禁用自動縮放。

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

有關所有有效負載參數的完整說明,請參閱上一節關於[啟用基於負載的自動縮放](#load-based-autoscaling)。

## <a name="guidelines"></a>指導方針

### <a name="choosing-load-based-or-schedule-based-scaling"></a>選擇基於負載或基於排程的縮放

在決定選擇哪種模式之前,請考慮以下因素:

* 在群集創建期間啟用自動縮放。
* 節點的最小數量應至少為三個。
* 負載方差:群集的負載在特定時間、特定日期是否遵循一致的模式。 如果不是,基於負載的調度是一個更好的選擇。
* SLA 要求:自動縮放是被動的,而不是預測性的。 負載開始增加和群集需要達到其目標大小之間是否有足夠的延遲? 如果有嚴格的 SLA 要求,並且負載是固定的已知模式,則"基於計劃"是一個更好的選擇。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考慮延伸或縮減操作的延遲

完成縮放操作可能需要 10 到 20 分鐘。 設置自定義計劃時,計劃此延遲。 例如,如果需要在上午 9:00 時群集大小為 20,則將計劃觸發器設置為較早的時間(如上午 8:30,以便縮放操作在上午 9:00 之前完成)。

### <a name="preparation-for-scaling-down"></a>準備縮小規模

在群集縮減過程中,自動縮放將停用節點以滿足目標大小。 如果這些節點上運行任務,自動縮放將等待任務完成。 由於每個輔助節點在 HDFS 中也充當了一個角色,因此臨時數據將轉移到其餘節點。 因此,應確保其餘節點上有足夠的空間來承載所有臨時數據。

正在運行的作業將繼續。 掛起的作業將等待具有較少可用輔助角色節點的計劃。

### <a name="minimum-cluster-size"></a>最小叢集大小

不要將群集縮減到少於三個節點。 將群集擴展到少於三個節點可能會導致群集由於檔複製不足而陷入安全模式。  有關詳細資訊,請參閱[卡在安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

## <a name="monitoring"></a>監視

### <a name="cluster-status"></a>叢集狀態

Azure 門戶中列出的群集狀態可説明您監視自動縮放活動。

![開啟基於工作節點的自動縮放叢集狀態](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

下面清單中將解釋您可能看到的所有群集狀態消息。

| 叢集狀態 | 描述 |
|---|---|
| 執行中 | 群集工作正常。 所有以前的自動縮放活動都已成功完成。 |
| Updating  | 正在更新群集自動縮放配置。  |
| HDInsight 設定  | 群集向上擴展或縮減操作正在進行中。  |
| 更新錯誤  | HDInsight 在自動縮放配置更新期間遇到了問題。 客戶可以選擇重試更新或禁用自動縮放。  |
| 錯誤  | 群集出現問題,並且不可用。 刪除此群集並創建新群集。  |

要查看群集中的當前節點數,請轉到群集**的「概述」** 頁上的 **「群集大小**圖表」。。 或者選擇 **「設定」** 下的 **「群集大小**」。

### <a name="operation-history"></a>動作記錄

您可以查看群集擴展和縮減歷史記錄,作為群集指標的一部分。 您還可以列出過去一天、一周或其他時間段內的所有縮放操作。

在**監視**下選擇**指標**。 然後從 **「指標」** 下拉框中選擇 **「添加指標**」和 **「活動工作人員數**」。 選擇右上角的按鈕以更改時間範圍。

![開啟基於工作節點計劃的自動縮放指標](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>後續步驟

在縮放指南中閱讀有關手動縮放群集[的指南](hdinsight-scaling-best-practices.md)
