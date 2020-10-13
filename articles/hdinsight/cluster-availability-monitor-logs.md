---
title: 如何使用 HDInsight 中的 Azure 監視器記錄監視叢集可用性
description: 瞭解如何使用 Azure 監視器記錄來監視叢集健康情況和可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 19e3f1a157ee2c042dfebfc96c9b51c3c4698ebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163725"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>如何使用 HDInsight 中的 Azure 監視器記錄監視叢集可用性

HDInsight 叢集包含 Azure 監視器記錄整合，可提供可查詢的計量和記錄，以及可設定的警示。 本文說明如何使用 Azure 監視器來監視您的叢集。

## <a name="azure-monitor-logs-integration"></a>Azure 監視器記錄整合

Azure 監視器記錄可讓多個資源（例如 HDInsight 叢集）所產生的資料在單一位置收集和匯總，以達成統一的監視體驗。

先決條件是，您需要 Log Analytics 工作區來儲存收集到的資料。 如果您尚未建立，您可以依照此處的指示執行： [建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>啟用 HDInsight Azure 監視器記錄整合

從入口網站中的 [HDInsight 叢集資源] 頁面，選取 [ **Azure 監視器**]。 然後，選取 [ **啟用** ]，然後從下拉式清單中選取您的 Log Analytics 工作區。

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

根據預設，這會在所有叢集節點上安裝 OMS 代理程式，但邊緣節點除外。 由於叢集邊緣節點上未安裝 OMS 代理程式，因此在預設情況下，Log Analytics 中不會有邊緣節點上的遙測。

## <a name="query-metrics-and-logs-tables"></a>查詢計量和記錄資料表

啟用 Azure 監視器記錄整合之後 (這可能需要幾分鐘的時間) 、流覽至您的 **Log Analytics 工作區** 資源，然後選取 [ **記錄**]。

![Log Analytics 工作區記錄](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

記錄檔會列出一些範例查詢，例如：

| 查詢名稱                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 電腦目前的可用性    | 圖表傳送記錄的電腦數目（每小時）                     |
| 列出心跳                 | 列出過去一小時內的所有電腦心跳                           |
| 每部電腦的最後一個信號 | 顯示每部電腦傳送的最後一個信號                             |
| 無法使用的電腦           | 列出過去5小時內未傳送任何心跳的所有已知電腦 |
| 可用性比率               | 計算每部已連線電腦的可用性比率                |

例如，選取 [在該查詢上**執行**] 來執行「**可用性比率**」範例查詢，如上面的螢幕擷取畫面所示。 這會顯示叢集中每個節點的可用性比率（以百分比表示）。 如果您已啟用多個 HDInsight 叢集以將計量傳送至相同的 Log Analytics 工作區，您會看到所有節點的可用性比率 (不包括顯示在這些叢集中的邊緣節點) 。

![Log Analytics 工作區記錄「可用性比率」範例查詢](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> 可用性比率是以24小時為單位來測量，因此您的叢集至少必須執行24小時，您才能看到精確的可用性費率。

您可以按一下右上角的 [ **釘** 選]，將此資料表釘選到共用儀表板。 如果您沒有任何可寫入的共用儀表板，您可以在這裡查看如何建立儀表板： [在 Azure 入口網站中建立和共用儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

## <a name="azure-monitor-alerts"></a>Azure 監視器警示

您也可以設定 Azure 監視器警示，當計量的值或查詢的結果符合特定條件時，就會觸發此警示。 例如，讓我們建立警示，以在一或多個節點未于5小時內傳送心跳時傳送電子郵件 (亦即，假設為無法使用) 。

從**記錄**檔中，選取 [在該查詢上**執行**] 以執行**無法使用的電腦**範例查詢，如下所示。

![Log Analytics 工作區記錄「無法使用的電腦」範例](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

如果有所有節點可供使用，此查詢現在應該會傳回零個結果。 按一下 [ **新增警示規則** ] 開始設定此查詢的警示。

![Log Analytics 工作區新增警示規則](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

警示有三個元件：在此案例中 (Log Analytics 工作區建立規則的 *資源*) 、觸發警示的 *條件* ，以及決定觸發警示時所發生之 *動作的動作群組* 。
按一下 **條件標題**（如下所示），以完成設定信號邏輯。

![入口網站警示建立規則條件](media/cluster-availability-monitor-logs/portal-condition-title.png)

這會開啟 **設定信號邏輯**。

設定 **警示邏輯** 區段，如下所示：

*依據：結果數目、條件：大於、臨界值：0。*

因為此查詢只會傳回無法使用的節點作為結果，所以如果結果數目超過0，就應該引發警示。

在 [ **評估依據** ] 區段中，根據您要檢查是否有無法使用之節點的頻率來設定 **期間** 和 **頻率** 。

基於此警示的目的，您想要確定 **句號 = Frequency。** 您可以在 [這裡](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)找到有關期間、頻率和其他警示參數的詳細資訊。

當您完成信號邏輯的設定時，請選取 [ **完成** ]。

![警示規則設定信號邏輯](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

如果您還沒有現有的動作群組，請按一下 [**動作群組**] 區段底下的 [**建立新**的]。

![警示規則會建立新的動作群組](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

這將會開啟 [ **新增動作群組**]。 選擇 **動作組名**、 **簡短名稱**、 **訂**用帳戶和 **資源群組。** 在 [ **動作** ] 區段下，選擇 [ **動作名稱** ]，然後選取 [ **電子郵件/SMS/推播/語音** ] 作為 **動作類型。**

> [!NOTE]
> 除了電子郵件/SMS/推播/語音之外，還有其他幾個可觸發警示的動作，例如 Azure Function、LogicApp、Webhook、ITSM 和 Automation Runbook。 [瞭解更多資訊。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

這會開啟 **電子郵件/SMS/Push/Voice**。 選擇收件者的**名稱**，核取 [**電子郵件**]**方塊，然後**輸入您要傳送警示的目標電子郵件地址。 在 [**電子郵件/SMS/推播/語音**] 中選取 **[確定]** ，然後在 [**新增動作群組**] 中完成動作群組的設定。

![警示規則會建立新增動作群組](media/cluster-availability-monitor-logs/portal-add-action-group.png)

這些 blade 關閉之後，您應該會看到您的動作群組列在 [ **動作群組** ] 區段底下。 最後，輸入**警示規則名稱**和**描述**，並選擇**嚴重性**，以完成 [**警示詳細資料**] 區段。 按一下 [ **建立警示規則** ] 以完成。

![入口網站建立警示規則完成](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定 **嚴重性** 的能力是一種功能強大的工具，可在建立多個警示時使用。 例如，您可以建立一個警示來引發警告 (嚴重性 1) 如果單一前端節點停止運作，而另一個警示在這兩個前端節點停止運作的情況下引發重大 (嚴重性 0) 。

當符合此警示的條件時，將會引發警示，而您會收到一封包含警示詳細資料的電子郵件，如下所示：

![Azure 監視器警示電子郵件範例](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

您也可以前往**Log Analytics 工作區**中的**警示**，以依嚴重性分組來查看所有已引發的警示。

![Log Analytics 工作區警示](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

選取嚴重性群組 (例如 **嚴重性1，** 如上所示) 將會顯示該嚴重性的所有警示的記錄，如下所示：

![Log Analytics 工作區嚴重性一項警示](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>後續步驟

* [叢集可用性 - Apache Ambari](./hdinsight-cluster-availability.md)
* [使用 Azure 監視器記錄](hdinsight-hadoop-oms-log-analytics-tutorial.md)
