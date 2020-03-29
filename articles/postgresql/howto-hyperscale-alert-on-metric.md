---
title: 配置警報 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 本文介紹如何為後格雷SQL - 超大規模 （Citus） 的 Azure 資料庫配置和訪問指標警報
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063136"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>使用 Azure 門戶為後格雷SQL - 超大規模 （Citus） 的 Azure 資料庫的指標設置警報

本文說明如何使用 Azure 入口網站來設定「適用於 PostgreSQL 的 Azure 資料庫」警示。 您可以根據 Azure 服務的[監視指標](concepts-hyperscale-monitoring.md)接收警報。

我們將設置警報，以便當指定指標的值超過閾值時觸發。 當首次遇到條件時，警報將觸發，並在之後繼續觸發。

您可以設定讓警示在觸發時執行下列動作︰
* 向服務管理員和共同管理員發送電子郵件通知。
* 傳送電子郵件給您指定的其他電子郵件。
* 呼叫 Webhook。

您可以透過下列方式，來設定及取得警示規則的相關資訊：
* [Azure 門戶](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>從 Azure 入口網站建立計量的警示規則
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您想要監視的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

2. 在資訊看板的 [監視]**** 區段底下，選取 [警示規則]****，如下所示：

   ![選取 [警示規則]](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. 選擇 **"新建警報規則**"（+ 圖示）。

4. [建立規則]**** 頁面隨即開啟，如下所示。 填寫必要資訊：

   ![[新增計量警示] 表單](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. 在 **"條件"** 部分中，選擇 **"添加**"。

6. 從要提醒的訊號清單中選擇一個計量。 在此範例中，選取 "Storage percent"。
   
   ![選取計量](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. 配置警報邏輯：

    * **操作員**（例如 "大於"）
    * **閾值**（例如 85%）
    * 在警報觸發之前必須滿足指標規則的**聚合細微性**量（例如。 "過去 30 分鐘"）
    * 和評估**頻率**（例如 "1 分鐘"）
   
   完成時選取 [完成]****。

   ![選取計量](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. 在 [動作群組]**** 區段中，選取 [建立]**** 建立新的群組，以接收警示通知。

9. 使用名稱、簡短名稱、訂用帳戶和資源群組填寫 [新增動作群組] 表單。

    ![動作群組](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. 設定 [電子郵件/簡訊/推播/語音]**** 動作類型。
    
    選擇"電子郵件 Azure 資源管理器角色"以向訂閱擁有者、參與者和讀者發送通知。
   
    完成時選取 [確定]****。

    ![動作群組](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. 指定 [警示規則名稱]、[描述] 與 [嚴重性]。

    ![動作群組](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. 選取 [建立警示規則]**** 以建立警示。

    在幾分鐘之內，警示會開始作用，且先前所述觸發。

### <a name="managing-alerts"></a>管理警示

創建警報後，您可以選擇它並執行以下操作：

* 檢視圖表，其中顯示與此警示相關的計量臨界值及前一天的實際值。
* **編輯**或**刪除**警示規則。
* 如果您想要暫時停止或恢復接收通知，可以將警示**停用**或**啟用**。

## <a name="suggested-alerts"></a>建議警報

### <a name="disk-space"></a>磁碟空間

監視和警報對於每個生產超大規模 （Citus） 伺服器組都很重要。 基礎 PostgreSQL 資料庫需要可用磁碟空間才能正常運行。 如果磁片已滿，資料庫伺服器節點將離線並拒絕啟動，直到空間可用。 此時，它需要 Microsoft 支援請求來修復這種情況。

我們建議在每個伺服器組的每個節點上設置磁碟空間警報，即使對於非生產使用方式也是如此。 磁碟空間使用警報提供干預和保持節點正常運行所需的提前警告。 為獲得最佳效果，請嘗試以 75%、85% 和 95% 的使用量進行一系列警報。 選擇的百分比取決於資料引入速度，因為快速的資料引入會更快地填滿磁片。

當磁片接近其空間限制時，請嘗試這些技術以獲得更多的可用空間：

* 查看資料保留原則。 如果可行，將較舊的資料移動到冷存儲。
* 請考慮[將節點添加到](howto-hyperscale-scaling.md#add-worker-nodes)伺服器組並重新平衡分片。 重新平衡將資料分佈在更多電腦上。
* 考慮[增加](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes)輔助節點的容量。 每個工作人員最多隻能有 2 個 TiB 的存儲空間。 但是，在調整節點大小之前，應嘗試添加節點，因為添加節點完成得更快。

### <a name="cpu-usage"></a>CPU 使用率

監視 CPU 使用率對於建立性能基準非常有用。 例如，您可能會注意到 CPU 使用率通常約為 40-60%。 如果 CPU 使用率突然開始徘徊在 95% 左右，您可以識別異常。 CPU 使用率可能反映有機增長，但它也可能顯示雜散查詢。 創建 CPU 警報時，請設置較長的聚合細微性以捕獲長時間增加並忽略暫態峰值。

## <a name="next-steps"></a>後續步驟
* 深入了解 [在警示中設定 webhook](../azure-monitor/platform/alerts-webhooks.md)。
* 依照 [計量集合概觀](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
