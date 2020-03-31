---
title: Microsoft Azure 和 Azure 監視器中的傳統警示概觀
description: 傳統警示即將淘汰。 警示可讓您監視 Azure 資源度量、事件或記錄，並在您所指定條件符合時收到通知。
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 098efd3075c6b099bdfc925cb4f09163f83532a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668261"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>什麼是 Microsoft Azure 中的傳統警示？

> [!NOTE]
> 本文說明如何建立舊版傳統計量警示。 Azure 監視器現在支援[較新的近即時指標警報和新的警報體驗](../../azure-monitor/platform/alerts-overview.md)。 經典警報[已停用](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)，但仍對不支援新警報的資源使用有限。 
>

警示可讓您對資料設定條件，並在最近的監視資料符合條件時收到通知。

## <a name="old-and-new-alerting-capabilities"></a>新舊警報功能

在舊的 Azure 監視器中，Application Insights、Log Analytics 和服務健康狀態具有個別的警示功能。 隨著時間進展，Azure 改善並結合使用者介面與不同的警示方法。 這樣的整併仍在持續進行中。

您只能在 Azure 入口網站的傳統警示使用者畫面中檢視傳統警示。 您可以從警示畫面上的 [檢視傳統警示]**** 按鈕取得此畫面。 

 ![Azure 入口網站中的警示選擇](media/alerts-classic.overview/monitor-alert-screen2.png)

新的警示使用者體驗比傳統警示體驗多了下列優點：
-   **更好的通知系統**- 所有新版警示都使用動作群組，這些是可在多個警示中重複使用的具名通知及動作群組。 傳統計量警示及舊版 Log Analytics 警示並不使用動作群組。
-   **統一的創作體驗**- Azure 監視器、日誌分析和應用程式見解的指標、日誌和活動日誌的所有警報創建都在一個位置。
-   **在 Azure 入口網站中檢視引發的 Log Analytics 警示** - 您現在也能在訂用帳戶中查看引發的 Log Analytics 警示。 先前這些警示是在個別的入口網站中。
-   **已觸發警報和警報規則的分離**- 警報規則（觸發警報的條件的定義）和觸發警報（警報規則引發的實例）是區分的，因此操作視圖和配置視圖被分開。
-   **更好的工作流程** - 新的警示編寫體驗會引導使用者進行設定警示規則的程序，而能更容易找到要取得警示的正確項目。
-   **智慧型警示彙總**和**設定警示狀態** - 新版警示包含自動群組功能，可將類似的警示顯示在一起，以減少使用者介面中的多載。 

新版計量警示比傳統計量警示多了下列優點：
-   **改善延遲**：新版計量警示的執行頻率可以達到每分鐘一次。 舊版計量警示的執行頻率一律是每 5 分鐘一次。 新版警示從發生問題到通知或動作的延遲較短 (3 至 5 分鐘)。 舊版警示則是 5 至 15 分鐘，視類型而定。  記錄警示通常會有 10 到 15 分鐘的延遲，這是因為內嵌記錄需要時間，但新版的處理方法可縮短該時間。 
-   **支援多維度計量**：您可以針對維度計量發出警示，以便監視計量中某個您感興趣的區段。
-   **對計量條件有更多的控制**：您可以定義更豐富的警示規則。 新版警示支援監視計量的最大值、最小值、平均及總計值。
-   **可合併監視多個計量**：您可以使用單一規則來監視多個計量 (目前最多兩個計量)。 若兩個計量在指定的期間內都超出其個別閾值，就會觸發警示。
-   **更好的通知系統**：所有新版警示都使用[動作群組](../../azure-monitor/platform/action-groups.md)，這些是可在多個警示中重複使用的具名通知及動作群組。  傳統計量警示及舊版 Log Analytics 警示並不使用動作群組。 
-   **來自記錄的計量** (公開預覽)：現在可將進入 Log Analytics 的記錄資料擷取並轉換成「Azure 監視器」計量，然後針對這些計量發出警示，就像任何其他計量一樣。 如需傳統警示專用的術語，請參閱[警示 (傳統)](alerts-classic.overview.md)。 


## <a name="classic-alerts-on-azure-monitor-data"></a>Azure 監視器資料的傳統警示
可用的傳統警示類型有兩種：計量警示和活動記錄警示。

* **傳統計量警示**：當指定的計量值超出您指派的閾值時，就會觸發此警示。 超過閾值且符合警示條件時，警示會產生通知。 此時，警示會被視為「已啟動」。 當警示「已解決」(也就是再次超出閾值但不再符合條件) 時，會產生另一個通知。

* **傳統活動記錄警示** - 因符合篩選準則，而由活動記錄項目觸發的串流記錄警示。 這些警示只有一個狀態：「已啟動」。 警示引擎會將篩選準則直接套用至任何新事件。 並不會搜尋以找出較舊的項目。 當新的服務健康狀態事件發生時，或當使用者或應用程式在您的訂用帳戶中執行諸如「刪除虛擬機器」作業時，這些警示可以通知您。

對於通過 Azure 監視器可用的資源日誌資料，將資料路由到日誌分析中並使用日誌查詢警報。 日誌分析現在使用[新的警報方法](../../azure-monitor/platform/alerts-overview.md) 

下圖摘要說明 Azure 監視器中的資料來源，以及就概念而言如何發出該資料的警示。

![警示的說明](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>警示 (傳統) 的分類法
Azure 使用下列詞彙來描述傳統警示及其功能：
* **警示** - 符合時會啟動之準則 (一或多個規則或條件) 的定義。
* **作用中** - 符合傳統警示所定義準則時的狀態。
* **已解決** - 先前符合傳統警示所定義的準則，但之後已不再符合時的狀態。
* **通知** - 根據變成作用中之傳統警示而採取的動作。
* **動作** - 傳送給通知接收者的特定呼叫 (例如以電子郵件傳送位址或張貼到 Webhook URL)。 通知通常可觸發多個動作。

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>如何收到 Azure 監視器傳統警示傳來的通知？
在過去，Azure 的警示來自不同的服務，各自使用其專屬的內建通知方法。 

Azure 監視器會建立可重複使用的通知群組，稱為*動作群組*。 動作群組可指定一組通知接受者。 每次參考動作群組的警示啟動時，所有接收者都會接收到該通知。 動作群組可讓您在許多警示物件之間重複使用一組接收者 (例如您隨時待命的工程師清單)。 除了電子郵件地址、簡訊號碼和一些其他動作，動作群組的通知支援還包括張貼到 Webhook URL。  如需詳細資訊，請參閱[動作群組](../../azure-monitor/platform/action-groups.md)。 

舊版傳統活動記錄警示會使用動作群組。

不過，舊版計量警示不會使用動作群組。 您可以改為設定下列動作： 
- 將電子郵件通知傳送至服務管理員、共同管理員或您指定的其他電子郵件。
- 呼叫 webhook，可讓您啟動其他自動化動作。

Webhook 可啟用自動化和修復，例如使用：
- Azure 自動化 Runbook
- Azure Function
- Azure 邏輯應用程式
- 第三方服務

## <a name="next-steps"></a>後續步驟
使用下列項目取得有關警示規則和設定這些規則的資訊：

* 瞭解有關[指標](data-platform.md)的更多
* [透過 Azure 入口網站設定傳統的計量警示](alerts-classic-portal.md)
* 設定[傳統的計量警示 PowerShell](alerts-classic-portal.md)
* 設定[傳統的計量警示命令列介面 (CLI)](alerts-classic-portal.md)
* 設定[傳統的計量警示 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 瞭解有關[活動日誌的更多內容](platform-logs-overview.md)
* [透過 Azure 入口網站設定活動記錄警示](activity-log-alerts.md)
* [透過 Resource Manager 設定活動記錄警示](alerts-activity-log.md)
* 檢閱[活動記錄警示 webhook 結構描述](activity-log-alerts-webhook.md)
* 深入了解[動作群組](action-groups.md)
* 配置[較新的警報](alerts-metric.md)
