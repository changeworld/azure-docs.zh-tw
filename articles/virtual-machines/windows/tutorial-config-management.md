---
title: 教學課程 - 在 Azure 中管理 Windows 虛擬機器組態
description: 在本教學課程中，您將了解如何在 Windows 虛擬機器上識別變更及管理套件更新
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cf01e4baf96e4403dae443fa6c98f74c571641a8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508310"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>教學課程：在 Azure 中監視變更及更新 Windows 虛擬機器

透過 Azure [變更追蹤](../../automation/change-tracking.md)和[更新管理](../../automation/automation-update-management.md)，您可以輕鬆地在 Azure 中識別 Windows 虛擬機器的變更，並管理這些 VM 的作業系統更新。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 管理 Windows 更新
> * 監視變更和清查。

## <a name="open-azure-cloud-shell"></a>開啟 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的 Azure 帳戶搭配使用。

若要在 Cloud Shell 中開啟任何程式碼區塊，只要選取程式碼區塊右上角的 [試試看] 即可。

您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤開啟 Cloud Shell。 選取 [複製] 來複製程式碼區塊，將其貼入 [Cloud Shell] 索引標籤，然後選取 Enter 鍵來執行程式碼。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

若要在本教學課程中設定 Azure 監視和更新管理，您需要在 Azure 中擁有 Windows VM。

首先，使用 [Get-credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) 設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

接下來，使用 [New-AzVM](/powershell/module/az.compute/new-azvm)建立 VM。 下列範例會在 `East US` 位置建立名為 `myVM` 的 VM。 資源群組 `myResourceGroupMonitor` 和支援的網路資源會隨之建立 (如果尚不存在的話)：

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

系統需要花幾分鐘的時間來建立資源和 VM。

## <a name="manage-windows-updates"></a>管理 Windows 更新

更新管理可協助您管理 Azure Windows VM 的更新和修補程式。 直接從您的 VM 中，您可以快速地：

- 評估可用更新的狀態。
- 排程必要更新的安裝。
- 檢視部署結果，確認更新已成功套用至 VM。

如需價格資訊，請參閱[更新管理的自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="enable-update-management"></a>啟用更新管理

若要啟用 VM 的更新管理：

1. 在視窗的最左邊，選取 [虛擬機器]。
1. 從清單中選擇 VM。
1. 在 [VM] 視窗的 [作業] 窗格中，選取 [更新管理]。
1. [啟用更新管理] 視窗隨即開啟。

判斷此 VM 是否已啟用更新管理的驗證將會執行。 驗證包括檢查 Log Analytics 工作區及連結的自動化帳戶，以及解決方法是否在工作區中。

您可以使用 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) 工作區來收集功能和服務 (例如更新管理) 所產生的資料。 工作區提供單一位置來檢閱和分析來自多個來源的資料。

若要在需要更新的 VM 上執行其他動作，您可以使用 Azure 自動化來對 VM 執行 Runbook。 這類動作包括下載或套用更新。

驗證程序也會檢查 VM 是否以 Microsoft Monitoring Agent (MMA) 和自動化混合式 Runbook 背景工作角色佈建。 您可以使用此代理程式與 VM 通訊，並取得更新狀態的相關資訊。

在 [啟用更新管理] 視窗中，選擇 Log Analytics 工作區與自動化帳戶，然後選取 [啟用]。 啟用解決方案最多需要 15 分鐘。

上線期間遺漏的下列任何必要條件，都會自動新增：

* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) 工作區
* [自動化](../../automation/index.yml)
* VM 上已啟用的[混合式 Runbook 背景工作角色](../../automation/automation-hybrid-runbook-worker.md)

啟用解決方案之後，[更新管理] 視窗會隨即開啟。 設定位置、Log Analytics 工作區以及要使用的自動化帳戶，然後選取 [啟用]。 如果這些選項呈現暗灰色，就表示已啟用 VM 的另一個自動化解決方案，且必須使用該解決方案的工作區和自動化帳戶。

![啟用更新管理解決方案](./media/tutorial-monitoring/manageupdates-update-enable.png)

更新管理解決方案最多可能需要 15 分鐘的時間才會啟用。 在此期間，請勿關閉瀏覽器視窗。 啟用解決方案之後，有關在 VM 上遺漏更新的相關資訊會流向 Azure 監視器記錄。 可能需要 30 分鐘到 6 小時的時間，資料才可供分析。

### <a name="view-an-update-assessment"></a>檢視更新評估

啟用 [更新管理] 之後，隨即會顯示 [更新管理] 視窗。 完成更新評估之後，您會在 [遺失更新] 索引標籤上看到遺失的更新清單。

 ![檢視更新狀態](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>排定更新部署

若要安裝更新，請將部署排定在發行排程和服務時段之後。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

選取 [更新管理] 視窗頂端的 [排程更新部署]，以針對 VM 來排程新的更新部署。 在 [新增更新部署] 視窗上，指定下列資訊：

| 選項 | 描述 |
| --- | --- |
| **名稱** |輸入唯一名稱來識別更新部署。 |
|**作業系統**| 選取 [Linux] 或 [Windows]。|
| **要更新的群組** |針對裝載於 Azure 上的 VM，根據訂用帳戶、資源群組、位置和標籤的組合來定義查詢。 此查詢會為 Azure 裝載的 VM 建立動態群組，以包含在您的部署中。 </br></br>針對未裝載在 Azure 上的 VM，請選取已儲存的現有搜尋。 在此搜尋中，您可以選取這些 VM 的群組，以包含在部署中。 </br></br> 若要深入了解，請參閱[動態群組](../../automation/automation-update-management-groups.md)。|
| **要更新的機器** |選取 [儲存的搜尋]、[匯入的群組]或 [機器]。<br/><br/>如果您選取 [機器]，您可以從下拉式清單中選擇個別機器。 每部機器的整備程度會顯示於表格的 [更新代理程式整備程度] 欄中。</br></br> 若要深入了解在 Azure 監視器記錄中建立電腦群組的不同方法，請參閱 [Azure 監視器記錄中的電腦群組](../../azure-monitor/platform/computer-groups.md) |
|**更新分類**|選擇所有必要的更新分類。|
|**包含/排除更新**|選取此選項以開啟 [包含/排除] 窗格。 要包含和要排除的更新會在個別的索引標籤上。 如需如何處理包含的詳細資訊，請參閱[排定更新部署](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment)。 |
|**排程設定**|選擇開始時間，並選取 [一次] 或 [週期性]。|
| **前置指令碼 + 後置指令碼**|選擇要在部署前和部署後執行的指令碼。|
| **維護時間範圍** | 輸入為更新設定的分鐘數。 有效值的範圍為 30 到 360 分鐘。 |
| **重新開機控制**| 選取處理重新開機的方式。 可用的選項包括：<ul><li>**在必要時重新開機**</li><li>**一律重新開機**</li><li>**永不重新開機**</li><li>**僅重新開機**</li></ul>[在必要時重新開機] 是預設選項。 如果您選取 [僅重新開機]，則不會安裝更新。|

排程設定完成之後，請按一下 [建立] 來返回狀態儀表板。 [已排定] 表格會顯示您建立的部署排程。

您也可以透過程式設計方式建立更新部署。 若要了解如何使用 REST API 來建立更新部署，請參閱[軟體更新設定 - 建立](/rest/api/automation/softwareupdateconfigurations/create)。 此外，您也可以使用 Runbook 範例來建立每週更新部署。 若要深入了解此 Runbook，請參閱[為資源群組中的一或多個 VM 建立每週更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) \(英文\)。

### <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您就可以在 [更新管理] 視窗的 [更新部署] 索引標籤中看到部署狀態。

如果目前正在執行部署，其狀態會顯示 [進行中]。 成功完成後，狀態會變更為「成功」。 如果部署中的任何更新失敗，則狀態會是 [部分失敗]。

選取已完成的更新部署，即可查看該部署的儀表板。

![特定部署的更新部署狀態儀表板](./media/tutorial-monitoring/manageupdates-view-results.png)

[更新結果] 磚會顯示 VM 上的更新總數和部署結果摘要。 右邊的表格會顯示每個更新的詳細明細及安裝結果。 每個結果會有下列其中一個值：

* **未嘗試**：未安裝更新。 根據定義的維護期間，沒有足夠的時間可用。
* **成功**：更新成功。
* **失敗**：更新失敗。

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]。

選取 [輸出] 磚，以查看負責在目標 VM 上管理更新部署的 Runbook 作業串流。

若要查看任何部署錯誤的詳細資訊，請選取 [錯誤]。

## <a name="monitor-changes-and-inventory"></a>監視變更和清查

您可以在電腦上收集並檢視軟體、檔案、Linux 精靈、Windows 服務和 Windows 登錄機碼的清查。 追蹤您電腦的設定可協助您找出環境中的操作問題，並進一步了解您電腦的狀態。

### <a name="enable-change-and-inventory-management"></a>啟用變更和清查管理

若要為您的 VM 啟用「變更」和「清查」管理：

1. 在視窗的最左邊，選取 [虛擬機器]。
1. 從清單中選擇 VM。
1. 在 VM 視窗的 [作業] 底下，選取 [清查] 或 [變更追蹤]。
1. [啟用變更追蹤和清查] 頁面會隨即開啟。

設定位置、Log Analytics 工作區以及要使用的自動化帳戶，然後選取 [啟用]。 如果選項呈現暗灰色，表示 VM 已啟用自動化解決方案。 在此情況下，必須使用已啟用的工作區以及自動化帳戶。

儘管這些解決方案在功能表上是分開顯示，但其實是相同的解決方案。 啟用其中一個會為您的 VM 同時啟用兩個。

![啟用變更與清查追蹤](./media/tutorial-monitoring/manage-inventory-enable.png)

啟用解決方案之後，可能需要一些時間在 VM 上收集清查，然後才會顯示資料。

### <a name="track-changes"></a>追蹤變更

在您 VM 的 [作業] 底下選取 [變更追蹤]，然後選取 [編輯設定]。 [變更追蹤] 窗格會隨即開啟。 選取您想要追蹤的設定類型，然後選取 [+ 新增] 來進行設定。

可用的 Windows 設定選項包括：

* Windows 登錄
* Windows 檔案

如需有關「變更追蹤」的詳細資訊，請參閱[針對 VM 上的變更進行疑難排解](../../automation/automation-tutorial-troubleshoot-changes.md)。

### <a name="view-inventory"></a>檢視清查

在您的 VM 上，選取 [作業] 底下的 [清查]。 在 [軟體] 索引標籤上，有一份資料表會顯示已找到的軟體。 每一筆軟體記錄的高階詳細資料都會顯示在資料表中。 這些詳細資料包括軟體名稱、版本、發行者及上次重新整理時間。

![檢視清查](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>監視活動記錄和變更

從您 VM 上的 [變更追蹤] 視窗，選取 [管理活動記錄連線] 來開啟 [Azure 活動記錄] 窗格。 選取 [連線] 可將變更追蹤連線至 VM 的 Azure 活動記錄。

啟用變更追蹤之後，您可以移至 VM 的 [概觀] 窗格，並選取 [停止] 來將您的 VM 停止。 出現提示時，選取 [是] 可停止 VM。 當 VM 解除配置後，選取 [啟動] 可將您的 VM 重新啟動。

將 VM 記錄停止和重新啟動，可在其活動記錄中記錄事件。 返回 [變更追蹤] 窗格，然後選取窗格底部的 [事件] 索引標籤。 一段時間之後，就會在圖表和資料表中顯示事件。 您可以選取每個事件來查看該事件的詳細資訊。

![檢視活動記錄中的變更](./media/tutorial-monitoring/manage-activitylog-view-results.png)

上一個圖表會顯示一段時間內已發生的變更。 在您新增 Azure 活動記錄連線之後，頂端的折線圖會顯示 Azure 活動記錄事件。

長條圖中的每個資料列都代表不同的可追蹤變更類型。 這些類型為 Linux 精靈、檔案、Windows 登錄機碼、軟體和 Windows 服務。 [變更] 索引標籤會顯示變更的詳細資料。 變更會以每次發生的順序出現，並會先顯示最新的變更。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定並檢閱 VM 的變更追蹤和更新管理。 您已了解如何︰

> [!div class="checklist"]
> * 建立資源群組和 VM。
> * 管理 Windows 更新
> * 監視變更和清查。

繼續進行下一個教學課程，以了解如何監視您的 VM。

> [!div class="nextstepaction"]
> [監視虛擬機器](tutorial-monitor.md)
