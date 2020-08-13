---
title: 如何使用 HDInsight 中的 Azure 監視器記錄監視叢集可用性
description: 瞭解如何使用 Azure 監視器記錄來監視叢集的健全狀況和可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 19e3f1a157ee2c042dfebfc96c9b51c3c4698ebc
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163725"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>如何使用 HDInsight 中的 Azure 監視器記錄監視叢集可用性

HDInsight 叢集包含 Azure 監視器記錄整合，可提供查詢計量和記錄，以及可設定的警示。 本文說明如何使用 Azure 監視器來監視您的叢集。

## <a name="azure-monitor-logs-integration"></a>Azure 監視器記錄整合

Azure 監視器記錄可讓多個資源（例如 HDInsight 叢集）所產生的資料在一個位置收集和匯總，以達成一致的監視體驗。

必要條件是，您需要有 Log Analytics 工作區來儲存收集的資料。 如果您尚未建立，您可以依照下列指示進行：[建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>啟用 HDInsight Azure 監視器記錄整合

從入口網站的 [HDInsight 叢集資源] 頁面中，選取 [ **Azure 監視器**]。 然後，選取 [**啟用**]，然後從下拉式選單中選取您的 Log Analytics 工作區。

![HDInsight 操作管理套件](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

根據預設，這會在所有叢集節點上安裝 OMS 代理程式，但邊緣節點除外。 因為叢集邊緣節點上未安裝 OMS 代理程式，所以 Log Analytics 中的邊緣節點上預設不會有遙測。

## <a name="query-metrics-and-logs-tables"></a>查詢計量和記錄資料表

一旦啟用 Azure 監視器記錄整合 (這可能需要幾分鐘的時間) ，流覽至您的**Log Analytics 工作區**資源，然後選取 [**記錄**]。

![Log Analytics 工作區記錄](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

記錄會列出一些範例查詢，例如：

| 查詢名稱                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 目前的電腦可用性    | 圖表傳送記錄的電腦數目（每小時）                     |
| 列出心跳                 | 列出過去一小時內的所有電腦的心跳                           |
| 每部電腦的最後一個信號 | 顯示每部電腦傳送的最後一個信號                             |
| 無法使用的電腦           | 列出過去5小時內未傳送信號的所有已知電腦 |
| 可用性速率               | 計算每部連線電腦的可用性費率                |

例如，在該查詢上選取 [**執行**]，以執行 [**可用性速率**] 範例查詢，如上面的螢幕擷取畫面所示。 這會顯示叢集中每個節點的可用性速率（以百分比表示）。 如果您已啟用多個 HDInsight 叢集以將計量傳送至相同的 Log Analytics 工作區，您會看到所有節點的可用性費率 (不包括顯示在這些叢集中的邊緣節點) 。

![Log Analytics 工作區記錄「可用性速率」範例查詢](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> 可用性速率是以24小時的期間來測量，因此您的叢集至少必須執行24小時，您才會看到正確的可用性速率。

您可以按一下右上角的 [**釘**選]，將此資料表釘選到共用的儀表板。 如果您沒有任何可寫入的共用儀表板，您可以在這裡瞭解如何建立它：[在 Azure 入口網站中建立和共用儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

## <a name="azure-monitor-alerts"></a>Azure 監視器警示

您也可以設定 Azure 監視器警示，當計量的值或查詢的結果符合特定條件時，就會觸發此警示。 例如，讓我們建立警示，以在一或多個節點未在5小時內傳送信號時傳送電子郵件 (亦即，假設為無法使用) 。

在 [**記錄**] 中，選取 [在該查詢上**執行**] 來執行 [**無法使用的電腦**] 範例查詢，如下所示。

![Log Analytics 工作區記錄「無法使用的電腦」範例](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

如果所有節點都可以使用，此查詢應該不會立即傳回零個結果。 按一下 [**新增警示規則**]，開始設定此查詢的警示。

![Log Analytics 工作區新增警示規則](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

警示有三個元件：在此情況下， (Log Analytics 工作區中建立規則的*資源*) 、觸發警示的*條件*，以及決定觸發警示時將發生什麼情況的*動作群組*。
按一下 [**條件] 標題**（如下所示），以完成設定信號邏輯。

![入口網站警示建立規則條件](media/cluster-availability-monitor-logs/portal-condition-title.png)

這會開啟 [**設定信號邏輯**]。

設定 [**警示邏輯**] 區段，如下所示：

*依據：結果數目，條件：大於，臨界值：0。*

由於此查詢只會傳回無法使用的節點做為結果，因此，如果結果數目大於0，就應該引發警示。

在 [**評估依據**] 區段中，根據您要檢查無法使用的節點的頻率來設定**期間**和**頻率**。

基於此警示的目的，您會想要確認**Period = Frequency。** 您可以在[這裡](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)找到有關期間、頻率和其他警示參數的詳細資訊。

當您完成設定信號邏輯時，請選取 [**完成**]。

![警示規則設定信號邏輯](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

如果您還沒有現有的動作群組，請按一下 [**動作群組**] 區段底下的 [**建立新**的]。

![警示規則會建立新的動作群組](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

這會開啟 [**新增動作群組**]。 選擇 [**動作組名**]、[**簡短名稱**]、[**訂**用帳戶] 和 [**資源群組]。** 在 [**動作**] 區段下，選擇**動作名稱**，然後選取 [**電子郵件/SMS/推播/語音**] 作為**動作類型。**

> [!NOTE]
> 除了電子郵件/SMS/推播/語音以外，還有其他幾個動作會觸發警示，例如 Azure 函數、LogicApp、Webhook、ITSM 和自動化 Runbook。 [瞭解更多資訊。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

這會開啟 [**電子郵件/SMS/推播/語音**]。 選擇收件者的**名稱**，**勾選**[**電子郵件**] 方塊，然後輸入您要傳送警示的目標電子郵件地址。 在 [**電子郵件/SMS/推播/語音**] 中選取 **[確定]** ，然後在 [**新增動作群組**] 中完成動作群組的設定。

![警示規則會建立新增動作群組](media/cluster-availability-monitor-logs/portal-add-action-group.png)

在這些 blade 關閉之後，您應該會看到您的動作群組列在 [**動作群組**] 區段底下。 最後，輸入**警示規則名稱**和**描述**，並選擇**嚴重性**，以完成 [**警示詳細資料**] 區段。 按一下 [**建立警示規則**] 完成。

![入口網站建立警示規則完成](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定**嚴重性**的能力是一種功能強大的工具，可在建立多個警示時使用。 例如，您可以建立一個警示來引發警告 (嚴重性 1) 如果單一前端節點中斷，另一個警示則會在這兩個前端節點關閉時引發重要的 (嚴重性 0) 。

當符合此警示的條件時，將會引發警示，而您會收到包含警示詳細資料的電子郵件，如下所示：

![Azure 監視器警示電子郵件範例](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

您也可以前往**Log Analytics 工作區**中的 [**警示**]，以查看所有已引發的警示（依嚴重性分組）。

![Log Analytics 工作區警示](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

選取嚴重性群組 (**嚴重性1，** 如上所示) 會顯示該嚴重性的所有警示的記錄，如下所示：

![Log Analytics 工作區嚴重性一個警示](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>後續步驟

* [叢集可用性 - Apache Ambari](./hdinsight-cluster-availability.md)
* [使用 Azure 監視器記錄](hdinsight-hadoop-oms-log-analytics-tutorial.md)
