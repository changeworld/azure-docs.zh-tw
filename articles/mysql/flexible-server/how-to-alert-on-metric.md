---
title: 設定計量警示-Azure 入口網站-適用於 MySQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何從 Azure 入口網站設定和存取適用於 MySQL 的 Azure 資料庫彈性伺服器的計量警示。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: d4385ccda665e9acd2d2f9fd340e675b8a9dfe6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936664"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>使用 Azure 入口網站針對適用於 MySQL 的 Azure 資料庫彈性的伺服器設定計量警示 

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文說明如何使用 Azure 入口網站來設定「適用於 MySQL 的 Azure 資料庫」警示。 您可以收到以 Azure 服務的監視計量為基礎的警示。

當所指定計量的值超出您指派的臨界值時，就會觸發警示。 不論是一開始符合條件時，還是之後不再符合條件時，都會觸發警示。 計量警示具狀態，也就是說只會在狀態變更時傳送通知。

您可以設定讓警示在觸發時執行下列動作︰
* 傳送電子郵件通知給服務管理員和共同管理員
* 傳送電子郵件給您指定的其他電子郵件。
* 呼叫 Webhook

您可以透過下列方式，來設定及取得警示規則的相關資訊：
* [Azure 入口網站](../../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>從 Azure 入口網站建立計量的警示規則
1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您要監視的適用於 MySQL 的 Azure 資料庫彈性伺服器。
2. 在提要欄位的 [ **監視** ] 區段底下，選取 [ **警示**]。
3. 選取 [+ 新增警示規則]。
4. [建立規則] 頁面隨即開啟。 填寫必要資訊：
5. 在 [ **條件** ] 區段中，選擇 [ **選取條件**]。
6. 您會看到一份支援的信號，請選取您想要建立警示的度量。 例如，選取 [儲存體百分比]。
7. 您會看到過去六小時的計量圖表。 使用 [ **圖表週期** ] 下拉式清單，選取以查看較長的度量歷程記錄。
8. 選取 **臨界值** 類型 (例如 「靜態」或「動態」 ) ， **運算子** (例如 「大於」 ) ， (例如， **匯總類型** 。 平均) 。 這會決定計量警示規則將評估的邏輯。
    - 如果您使用的是 **靜態** 閾值，請繼續定義 **臨界值** (例如。 ) 85%。 度量圖表可協助判斷哪些可能是合理的閾值。
    - 如果您使用的是 **動態** 閾值，請繼續定義 **閾值敏感度**。 計量圖表會顯示以最近資料為基礎的計算臨界值。 [深入了解動態閾值條件類型和敏感度選項](../../azure-monitor/platform/alerts-dynamic-thresholds.md)。
9. 藉由使用「匯總類型」函數 (例如，將 **匯總資料細微性調整 (期間) ** 間隔，來調整條件。 「30分鐘」 ) ， **頻率** (例如「每隔15分鐘」 ) 。
10. 按一下 [完成]。
11. 新增動作群組。 動作群組是 Azure 訂用帳戶擁有者定義的通知喜好設定集合。 在 [ **動作群組** ] 區段中，選擇 [ **選取動作群組** ]，以選取現有的動作群組以附加至警示規則。
12. 您也可以建立新的動作群組，以接收警示的通知。 如需詳細資訊，請參閱 [建立和管理動作群組](../../azure-monitor/platform/action-groups.md) 。
13. 若要建立新的動作群組，請選擇 [ **+ 建立動作群組**]。 以 **訂**用帳戶、 **資源群組**、 **動作組名** 和 **顯示名稱**填寫「建立動作群組」表單。
14. 設定動作群組的 **通知** 。
    
    在 [ **通知類型**] 中，選擇 [電子郵件 Azure Resource Manager 角色]，以選取要接收通知的訂用帳戶擁有者、參與者和讀者。 選擇傳送電子郵件的 **Azure Resource Manager 角色** 。
    您也可以選擇 [ **電子郵件/SMS 訊息/推播/語音** ]，將通知傳送給特定收件者。

    提供通知類型的 **名稱** ，並在完成時選取 [ **審核 + 建立** ]。

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. 填入 **警示規則的詳細資料** ，例如 **警示規則名稱**、 **描述**、 **將警示規則儲存至資源群組** 和 **嚴重性**。

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. 選取 [建立警示規則]**** 以建立警示。
    在幾分鐘之內，警示會開始作用，且先前所述觸發。
## <a name="manage-your-alerts"></a>管理警示
建立警示之後，您可以選取它，然後進行下列動作：

* 檢視圖表，其中顯示與此警示相關的計量臨界值及前一天的實際值。
* **編輯**或**刪除**警示規則。
* 如果您想要暫時停止或恢復接收通知，可以將警示**停用**或**啟用**。


## <a name="next-steps"></a>後續步驟
- 深入瞭解如何 [設定計量警示](../../azure-monitor/platform/alerts-metric.md)。
- 深入瞭解 [適用於 MySQL 的 Azure 資料庫彈性伺服器中的可用計量](./concepts-monitoring.md)。
- [瞭解計量警示在 Azure 監視器中的運作方式](../../azure-monitor/platform/alerts-metric-overview.md)
