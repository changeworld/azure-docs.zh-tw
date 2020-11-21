---
title: 設定警示-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: '本文說明如何設定及存取適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus 的計量警示) '
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 73705434aef3ee438c02fbfd6502d30e7620b695
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026449"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>使用 Azure 入口網站設定適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus 的計量警示) 

本文說明如何使用 Azure 入口網站來設定「適用於 PostgreSQL 的 Azure 資料庫」警示。 您可以根據 Azure 服務的 [監視計量](concepts-hyperscale-monitoring.md) 來接收警示。

我們會設定警示，以在指定的計量值超出閾值時觸發。 警示會在第一次符合條件時觸發，並在之後繼續觸發。

您可以設定讓警示在觸發時執行下列動作︰
* 傳送電子郵件通知給服務管理員和共同管理員。
* 傳送電子郵件給您指定的其他電子郵件。
* 呼叫 Webhook。

您可以透過下列方式，來設定及取得警示規則的相關資訊：
* [Azure 入口網站](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 監視器 REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>從 Azure 入口網站建立計量的警示規則
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您想要監視的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

2. 在資訊看板的 [監視] 區段底下，選取 [警示規則]，如下所示：

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="選取警示規則":::

3. 選取 [ **新增警示規則** ] (+ 圖示) 。

4. [建立規則] 頁面隨即開啟，如下所示。 填寫必要資訊：

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="[新增計量警示] 表單":::

5. 在 [ **條件** ] 區段中，選取 [ **新增**]。

6. 從要提醒的訊號清單中選擇一個計量。 在此範例中，選取 "Storage percent"。
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="螢幕擷取畫面顯示 [設定信號邏輯] 頁面，您可以在其中查看多個信號。":::

7. 設定警示邏輯：

    * **運算子** (例如 「大於」 ) 
    * **臨界值** (例如 85%) 
    * 在警示觸發之前，必須滿足計量規則的 **匯總細微性** 量（ (例如）。 「過去30分鐘內」 ) 
    * **評估的頻率** (例如 「1分鐘」 ) 
   
   完成時選取 [完成]。

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="螢幕擷取畫面顯示窗格，您可以在其中設定警示邏輯。":::

8. 在 [動作群組] 區段中，選取 [建立] 建立新的群組，以接收警示通知。

9. 使用名稱、簡短名稱、訂用帳戶和資源群組填寫 [新增動作群組] 表單。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="螢幕擷取畫面顯示 [新增動作群組] 表單，您可以在其中輸入描述的值。":::

10. 設定 [電子郵件/簡訊/推播/語音] 動作類型。
    
    選擇 [電子郵件 Azure Resource Manager 角色]，將通知傳送給訂用帳戶擁有者、參與者和讀者。
   
    完成時選取 [確定]。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="螢幕擷取畫面顯示 [電子郵件/S]/[推播/語音] 窗格。":::

11. 指定 [警示規則名稱]、[描述] 與 [嚴重性]。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="螢幕擷取畫面：顯示 [警示詳細資料] 窗格。"::: 

12. 選取 [建立警示規則] 以建立警示。

    在幾分鐘之內，警示會開始作用，且先前所述觸發。

### <a name="managing-alerts"></a>管理警示

建立警示之後，您可以選取它，並執行下列動作：

* 檢視圖表，其中顯示與此警示相關的計量臨界值及前一天的實際值。
* **編輯** 或 **刪除** 警示規則。
* 如果您想要暫時停止或恢復接收通知，可以將警示 **停用** 或 **啟用**。

## <a name="suggested-alerts"></a>建議的警示

### <a name="disk-space"></a>磁碟空間

監視和警示對於每個生產超大規模 (Citus) 伺服器群組都很重要。 基礎于 postgresql 資料庫需要可用的磁碟空間才能正確運作。 如果磁片已滿，資料庫伺服器節點將會離線，並拒絕啟動，直到可用空間為止。 屆時，它需要 Microsoft 支援要求來修正此情況。

建議您在每個伺服器群組中的每個節點上設定磁碟空間警示，即使非生產環境使用也是如此。 磁碟空間使用量警示可提供介入所需的事先警告，讓節點保持良好狀態。 為了獲得最佳結果，請嘗試75%、85% 和95% 使用量的一連串警示。 要選擇的百分比取決於資料內嵌速度，因為快速資料內嵌會更快地填滿磁片。

當磁片接近其空間限制時，請嘗試這些技術來取得更多可用空間：

* 檢查資料保留原則。 如果可行，將較舊的資料移至冷儲存體。
* 考慮 [將節點新增](howto-hyperscale-scale-grow.md#add-worker-nodes) 至伺服器群組，並重新平衡分區。 重新平衡會將資料分散到更多電腦上。
* 請考慮增加背景工作節點 [的容量](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) 。 每個背景工作最多可有 2 TiB 的儲存體。 不過，在調整節點大小時，應該先嘗試加入節點，因為新增節點的完成速度更快。

### <a name="cpu-usage"></a>CPU 使用量

監視 CPU 使用量很適合用來建立效能的基準。 例如，您可能會注意到，CPU 使用量通常大約是40-60%。 如果 CPU 使用量突然開始停留在95%，您就可以辨識異常。 CPU 使用率可能會反映有機的成長，但可能也會顯示偏離的查詢。 建立 CPU 警示時，請設定長匯總的資料細微性來攔截長時間的增加，並忽略短暫的尖峰。

## <a name="next-steps"></a>下一步
* 深入了解 [在警示中設定 webhook](../azure-monitor/platform/alerts-webhooks.md)。
* 依照 [計量集合概觀](../azure-monitor/platform/data-platform.md) 中的做法，確保您的服務可使用且有回應。
