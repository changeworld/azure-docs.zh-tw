---
title: 教學課程 - 在 Azure 中管理 Linux 虛擬機器組態
description: 在本教學課程中，您將了解如何在 Linux 虛擬機器上識別變更及管理套件更新
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5dd35ed2f4533d19cc3a59788d6422416dce13f5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79223245"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>教學課程：在 Azure 中監視變更及更新 Linux 虛擬機器

Azure [變更追蹤](../../automation/change-tracking.md)可讓您輕鬆識別變更，而[更新管理](../../automation/automation-update-management.md)則可讓您管理 Azure Linux VM 的作業系統更新。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 管理 Windows 更新
> * 監視變更和清查

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-vm"></a>建立 VM

若要查看作用中的診斷和計量，您需要 VM。 首先，使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 下列範例會在 eastus  位置建立名為 myResourceGroupMonitor  的資源群組。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

現在，使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM。 下列範例會建立名為 myVM  的 VM，並產生 SSH 金鑰 (如果 ~/.ssh/  中沒有這些金鑰的話)︰

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>管理軟體更新

更新管理可讓您管理 Azure Linux VM 的更新和修補程式。
您可以直接從 VM 快速評估可用更新的狀態、排定何時安裝必要的更新，並檢閱部署結果來確認更新已成功套用至 VM。

如需價格資訊，請參閱[更新管理的自動化價格](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>啟用更新管理

啟用 VM 的更新管理：

1. 在畫面左邊，選取 [虛擬機器]  。
2. 從清單中選取 VM。
3. 在 [VM] 畫面的 [作業]  區段中，選取 [更新管理]  。 [啟用更新管理]  畫面隨即開啟。

將會執行驗證來判斷此 VM 是否已啟用更新管理。
驗證包括檢查 Log Analytics 工作區及連結的自動化帳戶，以及解決方法是否在工作區中。

[Log Analytics](../../log-analytics/log-analytics-overview.md) 工作區可用來收集功能和服務 (例如更新管理) 所產生的資料。
工作區提供單一位置來檢閱和分析來自多個來源的資料。
若要在需要更新的 VM 上執行其他動作，Azure 自動化可讓您對 VM 執行 Runbook，例如下載和套用更新。

驗證程序也會檢查 VM 是否以 Log Analytics 代理程式和自動化混合式 Runbook 背景工作角色佈建。 此代理程式用來與 VM 通訊，並取得更新狀態的相關資訊。

選擇 Log Analytics 工作區與自動化帳戶，然後選取 [啟用]  來啟用解決方案。 啟用解決方案最多需要 15 分鐘。

如果在上線期間遺漏下列任何必要條件，就會自動新增：

* [Log Analytics](../../log-analytics/log-analytics-overview.md) 工作區
* [自動化帳戶](../../automation/automation-offering-get-started.md)
* VM 上已啟用 [Hybrid Runbook 背景工作角色](../../automation/automation-hybrid-runbook-worker.md)

[更新管理]  畫面隨即開啟。 設定位置、Log Analytics 工作區以及要使用的自動化帳戶，然後選取 [啟用]  。 如果欄位呈現灰色，就表示已啟用 VM 的另一個自動化解決方案，且必須使用相同的工作區和自動化帳戶。

![啟用更新管理解決方案](./media/tutorial-monitoring/manage-updates-update-enable.png)

啟用解決方案可能需要 15 分鐘。 在此期間，請勿關閉瀏覽器視窗。 啟用解決方案之後，有關在 VM 上遺漏更新的相關資訊會流向 Azure 監視器記錄。 可能需要 30 分鐘到 6 小時，資料才可供分析。

### <a name="view-update-assessment"></a>檢視更新評量

啟用 [更新管理]  之後，隨即會顯示 [更新管理]  畫面。 完成更新評估之後，您會在 [遺失更新]  索引標籤上看到遺失的更新清單。

 ![檢視更新狀態](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>排定更新部署

若要安裝更新，請將部署排定在發行排程和服務時段之後。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

按一下 [更新管理]  畫面頂端的 [排程更新部署]  ，以針對 VM 來排程新的更新部署。 在 [新增更新部署]  畫面上，指定下列資訊：

若要建立新的更新部署，請選取 [排程更新部署]  。 [新增更新部署]  窗格隨即開啟。 為下表描述的屬性輸入相關的值，然後按一下 [建立]  ：

| 屬性 | 描述 |
| --- | --- |
| 名稱 |用以識別更新部署的唯一名稱。 |
|作業系統| Linux 或 Windows|
| 要更新的群組 |對於 Azure 機器，根據訂用帳戶、資源群組、位置及標記的組合來定義查詢，以建置要包含在您部署中的動態 Azure VM 群組。 </br></br>對於非 Azure 機器，選取現有的已儲存搜尋，以選取要包含在部署中的非 Azure 機器群組。 </br></br>若要深入了解，請參閱[動態群組](../../automation/automation-update-management-groups.md)|
| 要更新的機器 |選取已儲存的搜尋、已匯入的群組，或從下拉式清單中選擇 [機器]，然後選取個別的機器。 如果您選擇 [機器]  ，機器的整備程度會顯示於 [更新代理程式整備程度]  欄中。</br> 若要深入了解在 Azure 監視器記錄中建立電腦群組的不同方法，請參閱 [Azure 監視器記錄中的電腦群組](../../azure-monitor/platform/computer-groups.md) |
|更新分類|選取您需要的所有更新分類|
|包含/排除更新|這會開啟 [包含]/[排除]  頁面。 要包含或排除的更新會在個別的索引標籤上。 如需如何處理包含的詳細資訊，請參閱[排定更新部署](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment) |
|排程設定|選取開始時間，並選取 [一次] 或 [週期性] 以定期執行|
| 前置指令碼 + 後置指令碼|選取要在部署前和部署後執行的指令碼|
| 維護時間範圍 |為更新設定的分鐘數。 此值不可小於 30 分鐘，且不可超過 6 小時 |
| 重新開機控制| 決定應該如何處理重新開機。 可用選項包括：</br>在必要時重新開機 (預設值)</br>一律重新開機</br>永不重新開機</br>僅重新開機 - 將不會安裝更新|

您也可以透過程式設計方式建立「更新部署」。 若要了解如何使用 REST API 來建立「更新部署」，請參閱[軟體更新設定 - 建立](/rest/api/automation/softwareupdateconfigurations/create)。 此外，也有可用來建立每週「更新部署」的範例 Runbook。 若要深入了解此 Runbook，請參閱[為資源群組中的一或多個 VM 建立每週更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) \(英文\)。

排程設定完成之後，請按一下 [建立]  按鈕，您就會返回狀態儀表板。
請注意，[已排程]  表格會顯示您已建立的部署排程。

### <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您就可以在 [更新管理]  畫面的 [更新部署]  索引標籤上看到該部署的狀態。
如果該部署目前正在執行，其狀態會顯示為 [進行中]  。 當它完成時，如果成功，狀態就會變更為 [成功]  。
如果該部署中的一或多個更新失敗，則狀態會是 [部分失敗]  。
選取已完成的更新部署，即可查看該更新部署的儀表板。

![特定部署的更新部署狀態儀表板](./media/tutorial-monitoring/manage-updates-view-results.png)

[更新結果]  磚包含 VM 上的更新總數和部署結果的摘要。
右邊表格是每個更新和安裝結果的詳細解析，可能是下列其中一個值：

* **未嘗試** - 未安裝更新，因為在已定義的維護時段內，沒有足夠的時間可用。
* **成功** - 更新已順利完成
* **失敗** - 更新失敗

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]  。

選取 [輸出]  磚，以查看負責在目標 VM 上管理更新部署的 Runbook 作業串流。

若要查看部署所傳回的任何錯誤詳細資訊，請選取 [錯誤]  。

## <a name="monitor-changes-and-inventory"></a>監視變更和清查

您可以在電腦上收集並檢視軟體、檔案、Linux 精靈、Windows 服務和 Windows 登錄機碼的清查。 追蹤您電腦的設定可協助您找出環境中的操作問題，並進一步了解您電腦的狀態。

### <a name="enable-change-and-inventory-management"></a>啟用變更和清查管理

為您的 VM 啟用「變更」和「清查」管理：

1. 在畫面左邊，選取 [虛擬機器]  。
2. 從清單中選取 VM。
3. 在 VM 畫面上，選取 [作業]  區段中的 [清查]  或 [變更追蹤]  。 [啟用變更追蹤與詳細目錄]  畫面隨即開啟。

設定位置、Log Analytics 工作區以及要使用的自動化帳戶，然後選取 [啟用]  。 如果欄位呈現灰色，就表示已啟用 VM 的另一個自動化解決方案，且必須使用相同的工作區和自動化帳戶。 儘管這些解決方案在功能表上是分開的，但它們是相同的解決方案。 啟用其中一個會為您的 VM 同時啟用兩個。

![啟用變更與清查追蹤](./media/tutorial-monitoring/manage-inventory-enable.png)

啟用解決方案之後，可能需要一些時間在 VM 上收集清查，然後才會顯示資料。

### <a name="track-changes"></a>追蹤變更

在您的 VM 上，選取 [作業]  底下的 [變更追蹤]  。 選取 [編輯設定]  ，就會顯示 [變更追蹤]  頁面。 選取您想要追蹤的設定類型，然後選取 [+ 新增]  來進行設定。 可用的 Linux 選項為 [Linux 檔案] 

如需有關「變更追蹤」的詳細資訊，請參閱 [針對 VM 上的變更進行疑難排解](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>檢視清查

在您的 VM 上，選取 [作業]  底下的 [清查]  。 在 [軟體]  索引標籤上，有一份資料表列出已找到的軟體。 每一筆軟體記錄的高階詳細資料都可在資料表中進行檢視。 這些詳細資料包括軟體名稱、版本、發行者、上次重新整理時間。

![檢視清查](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>監視活動記錄和變更

從您 VM 上的 [變更追蹤]  頁面，選取 [管理活動記錄連線]  。 此工作會開啟 [Azure 活動記錄]  頁面。 選取 [連線]  可將變更追蹤連線 VM 的 Azure 活動記錄。

此設定啟用時，瀏覽至您 VM 的 [概觀]  頁面，並選取 [停止]  來將您的 VM 停止。 出現提示時，選取 [是]  可停止 VM。 當它解除配置時，選取 [啟動]  可將您的 VM 重新啟動。

將 VM 記錄停止和啟動，可在其活動記錄中記錄事件。 瀏覽回 [變更追蹤]  頁面。 選取頁面底部的 [事件]  索引標籤。 一段時間之後，就會在圖表和資料表中顯示事件。 您可以選取每一個事件來檢視該事件的相關詳細資訊。

![檢視活動記錄中的變更](./media/tutorial-monitoring/manage-activitylog-view-results.png)

圖表會顯示一段時間內已發生的變更。 在您新增活動記錄連線之後，頂端的線條圖表會顯示 Azure 活動記錄事件。 長條圖中的每個資料列都代表不同的可追蹤變更類型。 這些類型是 Linux 精靈、檔案和軟體。 [變更] 索引標籤會以發生變更時間的遞減順序 (最新的優先)，顯示視覺效果中所顯示變更的詳細資料。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定並檢閱 VM 的變更追蹤和更新管理。 您已了解如何︰

> [!div class="checklist"]
> * 建立資源群組和 VM
> * 管理 Linux 更新
> * 監視變更和清查

繼續進行下一個教學課程，以了解如何監視您的 VM。

> [!div class="nextstepaction"]
> [監視虛擬機器](tutorial-monitor.md)
