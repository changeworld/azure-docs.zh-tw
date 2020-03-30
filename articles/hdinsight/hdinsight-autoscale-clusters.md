---
title: 自動調整 Azure HDInsight 叢集規模
description: 使用 Azure HDInsight 自動縮放功能自動使用 Apache Hadoop 縮放群集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 68bc30d08d95fe8e3d20a8ecb7af6c9710951921
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399708"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自動調整 Azure HDInsight 叢集規模

> [!Important]
> Azure HDInsight 自動縮放功能于 2019 年 11 月 7 日發佈，適用于 Spark 和 Hadoop 群集，並包含該功能預覽版本中未提供的改進。 如果您在 2019 年 11 月 7 日之前創建了 Spark 群集，並希望在群集上使用自動縮放功能，則建議的路徑是創建新群集，並在新群集上啟用自動縮放。
>
> 互動式查詢 （LLAP） 和 HBase 群集的自動縮放仍處於預覽階段。 自動縮放僅在 Spark、Hadoop、互動式查詢和 HBase 群集上可用。

Azure HDInsight 的群集自動縮放功能可自動縮放群集中輔助節點的數量。 群集中的其他類型的節點當前無法縮放。  在建立新的 HDInsight 叢集時，可以設定最小和最大的背景工作節點數目。 然後，自動縮放可監視分析負載的資源需求，並向上或向下縮放輔助節點的數量。 此功能不收取額外費用。

## <a name="cluster-compatibility"></a>群集相容性

下表描述了與自動縮放功能相容的群集類型和版本。

| 版本 | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 無 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| HDInsight 4.0 無 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| HDInsight 3.6 與 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| HDInsight 4.0 與 ESP | 是 | 是 | 是 | 是* | 否 | 否 | 否 |

\*HBase 群集只能配置為基於計畫的縮放，不能基於負載。

## <a name="how-it-works"></a>運作方式

您可以為 HDInsight 群集選擇基於負載的縮放或基於計畫的縮放。 基於負載的擴展會更改群集中設置範圍內的節點數，以確保最佳 CPU 利用率並最大限度降低運行成本。

基於計畫的擴展會根據在特定時間生效的條件更改群集中的節點數。 這些條件將群集縮放到所需的節點數。

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

系統每隔 60 秒就會檢查上述計量。 自動縮放基於這些指標做出擴展和縮減決策。

### <a name="load-based-scale-conditions"></a>基於負載的縮放條件

當檢測到以下條件時，自動縮放將發出縮放請求：

|相應增加|縮小|
|---|---|
|總掛起的 CPU 大於總可用 CPU 超過 3 分鐘。|擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。|
|總掛起記憶體大於總可用記憶體超過 3 分鐘。|擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。|

對於擴展，HDInsight 服務計算滿足當前 CPU 和記憶體要求所需的新工作節點數，然後發出擴展請求以添加所需的節點數。

對於橫向擴展，根據每個節點的 AM 容器數以及當前的 CPU 和記憶體要求，自動縮放會發出刪除一定數量的節點的請求。 該服務還會根據當前作業執行檢測哪些節點是要刪除的候選節點。 縮減操作首先將節點分離，然後從群集中刪除它們。

## <a name="get-started"></a>開始使用

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基於負載的自動縮放創建群集

要啟用基於負載的縮放自動縮放功能，在常規群集創建過程中完成以下步驟：

1. 在"**配置 + 定價**"選項卡上，選擇"**啟用自動縮放**"核取方塊。
1. 在**自動縮放類型**下選擇**基於負載**。
1. 輸入以下屬性的所需值：  

    * **輔助節點**的初始**節點數**。
    * 輔助節點的**最小**數量。
    * **輔助**節點的最大數量。

    ![啟用基於工作節點負載的自動縮放](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值定義創建群集時的初始大小。 輔助節點的最小數量應設置為三個或更多。 將群集擴展到少於三個節點可能會導致群集由於檔案複製不足而陷入安全模式。  有關詳細資訊，請參閱[卡在安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基於計畫的自動縮放創建群集

要啟用基於計畫的縮放的自動縮放功能，在常規群集創建過程中完成以下步驟：

1. 在 **"配置 + 定價**"選項卡上，選中 **"啟用自動縮放**"核取方塊。
1. 輸入**輔助角色節點****的節點數**，該節點控制向上擴展群集的限制。
1. 選擇"**自動縮放類型**"下**基於計畫**的選項。
1. 選擇 **"配置**"以打開**自動縮放配置**視窗。
1. 選擇您的時區，然後按一下 **+ 添加條件**
1. 選擇新條件應應用於的一周中的天數。
1. 編輯條件應生效的時間以及群集應縮放到的節點數。
1. 如果需要，添加更多條件。

    ![啟用基於工作節點的計畫創建](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

節點數必須介於添加條件之前輸入的最大輔助節點數 3 和最大輔助節點數之間。

### <a name="final-creation-steps"></a>最終創建步驟

對於基於負載的擴展和基於計畫的擴展，通過在**Node 大小**下的下拉清單中選擇 VM 來選擇輔助節點的 VM 類型。 為每個節點類型選擇 VM 類型後，可以看到整個群集的估計成本範圍。 調整 VM 類型以適合您的預算。

![啟用基於工作節點計畫的自動縮放節點大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

您的訂用帳戶擁有每個區域的容量配額。 頭節點的內核總數加上工作節點的最大數量不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]  
> 如果超過總內核配額限制，您將收到一條錯誤訊息，指出"最大節點超出了該區域中的可用內核，請選擇其他區域或與支援部門聯繫以增加配額。

如需使用 Azure 入口網站建立 HDInsight 叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

#### <a name="load-based-autoscaling"></a>基於負載的自動縮放

`autoscale`您可以通過將節點添加到`computeProfile` > `workernode`具有屬性`minInstanceCount`的部分，`maxInstanceCount`並如下面的 json 程式碼片段所示，使用基於負載的 Azure 資源管理器範本創建 HDInsight 群集。

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

如需使用 Resource Manager 範本建立叢集的詳細資訊，請參閱[使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。  

#### <a name="schedule-based-autoscaling"></a>基於計畫的自動縮放

通過將`autoscale`節點添加到`computeProfile` > `workernode`分區，可以使用基於計畫的 Azure 資源管理器範本創建 HDInsight 群集。 節點`autoscale`包含 具有`recurrence`和`timezone``schedule`的 ， 描述何時發生更改。

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

要在正在運行的群集上啟用自動縮放，請在 **"設置"** 下選擇**群集大小**。 然後選擇 **"啟用自動縮放**"。 選擇所需的自動縮放類型，並輸入基於負載或基於計畫的縮放選項。 最後，選取 [儲存]****。

![啟用基於工作節點計畫的自動縮放運行群集](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>使用 REST API

要使用 REST API 在正在運行的群集上啟用或禁用自動縮放，請向自動縮放終結點發出 POST 請求，如下所示：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

在請求負載中使用適當的參數。 下面的 json 有效負載可用於啟用自動縮放。 使用有效負載`{autoscale: null}`禁用自動縮放。

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

有關所有有效負載參數的完整說明，請參閱上一節關於[啟用基於負載的自動縮放](#load-based-autoscaling)。

## <a name="best-practices"></a>最佳作法

### <a name="choosing-load-based-or-schedule-based-scaling"></a>選擇基於負載或基於計畫的縮放

在決定選擇哪種模式之前，請考慮以下因素：

* 在群集創建期間啟用自動縮放。
* 節點的最小數量應至少為三個。
* 負載方差：群集的負載在特定時間、特定日期是否遵循一致的模式。 如果不是，基於負載的調度是一個更好的選擇。
* SLA 要求：自動縮放是被動的，而不是預測性的。 負載開始增加和群集需要達到其目標大小之間是否有足夠的延遲？ 如果有嚴格的 SLA 要求，並且負載是固定的已知模式，則"基於計畫"是一個更好的選擇。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考慮擴展或縮減操作的延遲

完成縮放操作可能需要 10 到 20 分鐘。 設置自訂計畫時，計畫此延遲。 例如，如果需要在上午 9：00 時群集大小為 20，則將計畫觸發器設置為較早的時間（如上午 8：30，以便縮放操作在上午 9：00 之前完成）。

### <a name="preparation-for-scaling-down"></a>準備縮小規模

在群集縮減過程中，自動縮放將停用節點以滿足目標大小。 如果這些節點上正在運行任務，自動縮放將等待任務完成。 由於每個輔助節點在 HDFS 中也充當了一個角色，因此臨時資料將轉移到其餘節點。 因此，應確保其餘節點上有足夠的空間來承載所有臨時資料。

正在運行的作業將繼續運行並完成。 掛起的作業將等待正常計畫，可用輔助角色節點較少。

### <a name="minimum-cluster-size"></a>最小群集大小

不要將群集縮減到少於三個節點。 將群集擴展到少於三個節點可能會導致群集由於檔案複製不足而陷入安全模式。  有關詳細資訊，請參閱[卡在安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

## <a name="monitoring"></a>監視

### <a name="cluster-status"></a>叢集狀態

Azure 門戶中列出的群集狀態可説明您監視自動縮放活動。

![啟用基於工作節點的自動縮放群集狀態](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

下面清單中將解釋您可能看到的所有群集狀態訊息。

| 叢集狀態 | 描述 |
|---|---|
| 執行中 | 群集工作正常。 所有以前的自動縮放活動都已成功完成。 |
| Updating  | 正在更新群集自動縮放配置。  |
| HDInsight 配置  | 群集向上擴展或縮減操作正在進行中。  |
| 更新錯誤  | HDInsight 在自動縮放配置更新期間遇到問題。 客戶可以選擇重試更新或禁用自動縮放。  |
| 錯誤  | 群集出現問題，並且不可用。 刪除此群集並創建新群集。  |

要查看群集中的當前節點數，請轉到群集**的"概述"** 頁上的 **"群集大小**圖表"，或在 **"設置"** 下選擇**群集大小**。

### <a name="operation-history"></a>操作歷史記錄

您可以查看群集擴展和縮減歷史記錄，作為群集指標的一部分。 您還可以列出過去一天、一周或其他時間段內的所有縮放操作。

在**監視**下選擇**指標**。 然後從 **"指標"** 下拉清單中選擇 **"添加指標**"和 **"活動工作人員數**"。 選擇右上角的按鈕以更改時間範圍。

![啟用基於工作節點計畫的自動縮放指標](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>後續步驟

閱讀[調整規模最佳做法](hdinsight-scaling-best-practices.md)，了解手動調整叢集規模的最佳做法
