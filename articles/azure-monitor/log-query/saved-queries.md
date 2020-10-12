---
title: Azure 監視器 Log Analytics 中儲存的查詢
description: 您可以根據需求開始和修改的查詢
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: 462358ab967d4ba26a265d7b5f401b995223815e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85959837"
---
# <a name="saved-queries-in-azure-monitor-log-analytics"></a>Azure 監視器 Log Analytics 中儲存的查詢

Log Analytics 提供了一組範例查詢，您可以自行執行這些查詢，或將其作為您自己查詢的起點。 本文描述查詢範例以及如何使用它們。

如果您不熟悉 Log Analytics 或 KQL 查詢語言，查詢範例是很好的起點。 他們可以提供資源的即時深入解析，並提供良好的方式來開始學習和使用 KQL，進而縮短開始使用 Log Analytics 所需的時間。 我們已收集並策劃超過250個範例查詢，其設計目的是要提供您立即的價值，而這幾個範例查詢會持續成長。

## <a name="in-context-queries"></a>內容內部查詢

新體驗會篩選並建議內容中的查詢。 換句話說，系統只會自動顯示與您選取的範圍相關的查詢。

- 針對 **單一資源** –查詢會根據資源類型進行篩選。
- 針對 **資源群組** ，查詢會根據特定資源群組中的資源進行篩選。
- 針對 **工作區** –查詢會根據工作區上所安裝的解決方案進行篩選。

此行為對於所有 Log Analytics 範圍都是一致的。 如果您看不到所需資源類型的範例查詢，可能是因為篩選是在內容中所致。 稍後的章節將說明如何移除內容約制內的範圍，以便您可以查看所有可能的查詢。

> [!TIP]
> 範圍中的資源越多，入口網站篩選和顯示範例查詢對話方塊的時間就越長。

## <a name="example-query-user-interface"></a>範例查詢使用者介面

您可以從兩個不同的位置取得範例查詢。

### <a name="example-query-dialog"></a>查詢對話方塊範例

當您第一次進入 Log Analytics 體驗時，會自動顯示 *範例查詢對話方塊* 。  您也可以按一下 [ **範例查詢**] 畫面右上方的方式來存取它。

![資訊看板](media/saved-queries/sidebar-2.png)

範例查詢對話方塊接著會出現，如下所示：  

![範例查詢畫面](media/saved-queries/example-query-start.png)

先前的螢幕擷取畫面會顯示 Azure Key Vault 實例的 [記錄] 畫面。 如本文先前所述，查詢會顯示在內容中。  因此，螢幕擷取畫面只會顯示 Key Vault 的相關範例。 如果您選取整個訂用帳戶，則會顯示該訂用帳戶中所有資源類型的查詢。  

每個範例查詢都是以卡片表示。 您可以快速掃描查詢以找出所需的內容。 您可以直接從對話方塊執行查詢，或選擇將它載入至查詢編輯器，以進行額外的微調和調整。

### <a name="sidebar-query-experience"></a>提要欄位查詢體驗

您可以從 Log Analytics 左側提要欄位的 [查詢] 窗格存取對話體驗的所有相同功能。 您可以將滑鼠停留在查詢名稱上，以取得查詢描述和其他功能。

![資訊看板](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>尋找和篩選查詢

此區段中的選項適用于對話方塊和側邊欄查詢體驗，但使用的使用者介面稍有不同。  

### <a name="use-favorites"></a>使用我的最愛

您最常用的查詢可讓您快速存取。

> [!TIP]
> 稍後收集和查看查詢是開始使用的絕佳方式。 尋找您需要的查詢，然後按一下查詢旁的星號，將它新增至 [我的最愛]。 如果您稍後發現查詢對您而言並不實用，您可以取消我的最愛。  

### <a name="filtering-and-group-by"></a>篩選和分組依據

雖然查詢對話方塊體驗檔案系統只會顯示具有正確內容的查詢，但您可以選擇移除篩選，並查看所有查詢。

### <a name="group-by"></a>群組依據

按一下 [ *分組方式* ] 下拉式清單來變更查詢的群組：

![範例查詢畫面 groupby](media/saved-queries/example-query-groupby.png)

此對話方塊支援分組依據：

- **資源類型** – Azure 中定義的資源，例如虛擬機器。 如需 Azure 監視器 Logs/Log Analytics 資料表與資源類型的完整對應，請參閱 [Azure 監視器資料表參考](/azure/azure-monitor/reference/tables/tables-resourcetype) 。  
- **Category** – *安全性* 或 *Audit*等資訊類型。 類別與 [資料表] 側邊窗格中定義的類別相同。 如需完整的類別清單，請參閱 [Azure 監視器資料表參考](/azure/azure-monitor/reference/tables/tables-category) 。  
- **解決方案** -與查詢相關聯的 Azure 監視器方案
- **主題** -範例查詢的主題，例如 *活動記錄* 或 *應用程式記錄*。 主題屬性對範例查詢而言是唯一的，而且可能會根據特定的資源類型而有所不同。

群組值也可作為活動目錄。 按一下畫面左側的其中一個值，就會將查詢檢視向右滾動至按下的專案。

### <a name="filter"></a>Filter

您也可以根據稍早所述的 groupby 值篩選查詢。 在範例查詢對話方塊中，會在頂端找到篩選。

![範例查詢螢幕篩選](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>組合分組依據和篩選

篩選和分組依據功能的設計目的是要以串聯方式運作。 它們提供查詢相片順序的彈性。 例如，如果您使用具有多個資源的資源群組，您可能會想要向下篩選至特定的資源類型，並依主題排列產生的查詢。

## <a name="sample-query-dialog-appearance-behavior"></a>範例查詢對話方塊外觀行為

如果您是 KQL 專業人員，而且想要直接進入查詢編輯器，您可以切換查詢對話方塊「關閉」。 當關閉切換時，在 Log Analytics 畫面載入時，不會載入範例查詢對話方塊。

![開啟的範例](media/saved-queries/examples-on-off.png)

您一律可以從 Log Analytics 頂端列的 [ *範例查詢* ] 按鈕存取範例查詢快顯視窗體驗。

## <a name="query-explorer"></a>查詢總管

用於儲存及共用使用者所產生查詢的查詢瀏覽器體驗，在當時仍維持不變。

## <a name="next-steps"></a>接下來的步驟

[開始使用 KQL 查詢](get-started-queries.md)

