---
title: 管理 Azure VM 的更新和修補程式
description: 本文提供概觀，說明如何使用 Azure 自動化更新管理來管理 Azure VM 和非 Azure VM 的更新和修補程式。
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: 347f2fbc0f12aa775c42dbb14a4625dc509a20ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373017"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>管理 Azure VM 的更新和修補程式

您可以使用更新管理解決方案來管理虛擬機器的更新和修補程式。 在本教學課程中，您會了解如何快速評估可用更新的狀態、排程何時安裝必要的更新、檢閱部署結果，以及建立警示以確認更新已成功套用。

如需價格資訊，請參閱[更新管理的自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 檢視更新評估
> * 設定警示
> * 排定更新部署
> * 檢視部署的結果

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

* 已針對一或多個 Vm 啟用[更新管理](automation-update-management.md)解決方案。
* 要上架的[虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="view-update-assessment"></a>檢視更新評量

啟用 [更新管理] 之後，[更新管理] 窗格隨即開啟。 如果發現遺失了任何更新，[遺失更新] 索引標籤上會顯示遺失的更新清單。

在 [資訊連結] 底下選取更新連結，以開啟更新的支援文章。 您可以了解關於更新的重要資訊。

![檢視更新狀態](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

按一下更新的任何其他地方，即可開啟所選更新的 [記錄搜尋] 窗格。 記錄搜尋的查詢已針對該特定更新預先定義。 您可以修改此查詢或建立您自己的查詢，以檢視部署的更新或您環境中遺失項目的詳細資訊。

![檢視更新狀態](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>設定警示

在此步驟中，您會了解如何設定一個可讓您得知更新部署狀態的警示。

### <a name="alert-conditions"></a>警示條件

在您「自動化帳戶」的 [監視] 底下，移至 [警示]，然後按一下 [+ 新增警示規則]。

系統已經選取您的「自動化帳戶」作為資源。 如果您想要變更該資源，可以按一下 [選取]，然後在 [選取資源] 頁面上，從 [依資源類型篩選] 下拉式清單中選取 [自動化帳戶]。 選取您的自動化帳戶，然後選取 [完成]。

按一下 [新增條件] 以選取您更新部署適用的訊號。 下表顯示兩個適用於更新部署之可用訊號的詳細資料：

|訊號名稱|維度|描述|
|---|---|---|
|**更新部署執行總計**|- 更新部署名稱</br>- 狀態|此訊號可用來針對整體更新部署狀態發出警示。|
|**更新部署機器執行總計**|- 更新部署名稱</br>- 狀態</br>- 目標電腦</br>- 更新部署執行識別碼|此訊號可用來針對以特定機器為目標的更新部署狀態發出警示|

針對維度值，請從清單中選取有效的值。 如果清單中沒有您要尋找的值，請按一下維度旁邊的 [\+] 符號，然後輸入自訂名稱。 接著，您便可以選取所要尋找的值。 如果您想要從維度選取所有值，請按一下 [選取 \*] 按鈕。 如果您沒有為維度選取值，則在評估期間就會忽略該維度。

![設定訊號邏輯](./media/automation-tutorial-update-management/signal-logic.png)

在 [警示邏輯] 之下，針對 [閾值]，輸入 **1**。 完成之後，選取 [完成]。

### <a name="alert-details"></a>警示詳細資料

在 **2.** 定義警示詳細資料 之下，輸入警示的名稱和描述。 將執行成功的 [嚴重性] 設定為 [資訊 (嚴重性 2)]，或將執行失敗的嚴重性設定為 [資訊 (嚴重性 1)]。

![設定訊號邏輯](./media/automation-tutorial-update-management/define-alert-details.png)

在 [動作群組] 底下，選取 [新建]。 動作群組是一組可讓您跨多個警示使用的動作。 這些動作可包括 (但不限於) 電子郵件通知、Runbook、Webhook 和等多種項目。 若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

在 [動作群組名稱] 方塊中，輸入警示名稱和簡短名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

在 [動作] 底下輸入動作的名稱，例如「電子郵件通知」。 在 [動作類型] 底下，選取 [電子郵件/簡訊/推播/語音]。 在 [詳細資料] 底下，選取 [編輯詳細資料]。

在 [電子郵件/簡訊/推播/語音] 窗格中，輸入名稱。 選取 [電子郵件] 核取方塊，然後輸入有效的電子郵件地址。

![設定電子郵件動作群組](./media/automation-tutorial-update-management/configure-email-action-group.png)

在 [電子郵件/簡訊/推播/語音] 窗格中，選取 [確定]。 在 [新增動作群組] 窗格中，選取 [確定]。

若要自訂警示電子郵件的主旨，請在 [建立規則] 的 [自訂動作] 底下，選取 [電子郵件主旨]。 當您完成時，選取 [建立警示規則]。 警示會在更新部署成功時，告知您有哪些機器包含在該更新部署執行中。

## <a name="schedule-an-update-deployment"></a>排定更新部署

接下來，將部署安排在發行排程和服務時間範圍之後，以便安裝更新。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

>[!NOTE]
>當您排程更新部署時，將會建立一個連結至 **Patch-MicrosoftOMSComputers** Runbook 的[排程](shared-resources/schedules.md)資源，以處理目標電腦上的更新部署。 如果您在建立部署後從 Azure 入口網站或使用 PowerShell 刪除了排程資源，將會中斷已排程的更新部署，並在您嘗試從入口網站重新加以設定時顯示錯誤。 您只能藉由刪除對應的部署排程來刪除排程資源。  
>

若要為 VM 安排新的更新部署，請移至 [更新管理]，然後選取 [排程更新部署]。

在 [新增更新部署] 之下，指定下列資訊：

* **名稱**：輸入更新部署的唯一名稱。

* **作業系統**：為更新部署選取要作為目標的 OS。

* **要更新的群組 (預覽)** ：根據訂用帳戶、資源群組、位置及標記的組合來定義查詢，以建置要包含在您部署中的動態 Azure VM 群組。 若要深入了解，請參閱[動態群組](automation-update-management-groups.md)

* **要更新的機器**：選取已儲存的搜尋、已匯入的群組，或從下拉式清單中選擇 [機器]，然後選取個別的機器。 如果您選擇 [機器]，機器的整備程度會顯示於 [更新代理程式整備程度] 資料行中。 若要深入了解在 Azure 監視器記錄中建立電腦群組的不同方法，請參閱 [Azure 監視器記錄中的電腦群組](../azure-monitor/platform/computer-groups.md)

* **更新分類**：選取適用於各項產品、且可包含在更新部署中的支援更新分類。 此教學課程中，將所有類型保留選取。

  分類類型包括：

   |OS  |Type  |
   |---------|---------|
   |Windows     | 重大更新</br>安全性更新</br>更新彙總套件</br>Feature Pack</br>Service Pack</br>定義更新</br>工具</br>更新        |
   |Linux     | 重大更新和安全性更新</br>其他更新       |

   如需分類類型的說明，請參閱[更新分類](automation-view-update-assessments.md#update-classifications)。

* **要包含/排除的更新** - 這會開啟 [包含]/[排除] 頁面。 要包含或排除的更新會在個別的索引標籤上。

> [!NOTE]
> 請務必了解，排除會覆寫包含。 例如，如果您定義 `*` 排除規則，系統便不會安裝任何修補程式或套件，因為已將它們全部排除。 排除的修補程式仍然會顯示為從機器中遺漏。 就 Linux 機器而言，如果已包含某個套件，但排除了它的某個相依套件，就不會安裝該套件。

> [!NOTE]
> 您無法指定將已被取代的更新納入更新部署中。
>

* **排程設定**：[排程設定] 窗格將會開啟。 預設開始時間為目前時間之後的 30 分鐘。 您可以將開始時間設為 10 分鐘以後的任何時間。

   您也可以指定部署是否為發生一次，或設定週期性排程。 在 [週期性] 下選取 [一次]。 將預設值保留為 1 天，然後選取 [確定]。 這樣會設定週期性的排程。

* **前置指令碼 + 後置指令碼**：選取要在部署前和部署後執行的指令碼。 若要深入了解，請參閱[管理前置和後置指令碼](pre-post-scripts.md)。

* **維護時間範圍 (分鐘)** ：保留預設值。 維護時間範圍可控制允許安裝更新的時間長度。 在指定維護時間範圍時，請考慮下列詳細資料。

  * 維護時間範圍可控制嘗試安裝的更新數目。
  * 如果維護時間範圍即將結束，更新管理並不會停止安裝新的更新。
  * 如果超出維護時間範圍，更新管理並不會終止進行中的更新。
  * 如果在 Windows 上超出維護時間範圍，通常是因為 Service Pack 更新需要很長的時間才能安裝完成。

  > [!NOTE]
  > 若要避免在 Ubuntu 維護時間範圍以外套用更新，請重新設定自動安裝升級套件以停用自動更新。 如需有關如何設定套件的資訊，請參閱 [Ubuntu Server 指南中的自動更新主題](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) \(英文\)。

* **重新開機選項**：此設定會決定應該如何處理重新開機。 可用選項包括：
  * 在必要時重新開機 (預設值)
  * 一律重新開機
  * 永不重新開機
  * 僅重新開機 - 將不會安裝更新

> [!NOTE]
> 如果 [重新開機控制] 已設定為 [永不重新開機]，在[用來管理重新啟動的登錄機碼](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)底下所列的登錄機碼可能會造成重新開機事件。

排程設定完成後，請選取 [建立]。

![更新排程設定窗格](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

您會回到狀態儀表板。 選取 [排程更新部署] 可顯示您所建立的部署排程。

> [!NOTE]
> 「更新管理」支援部署第一方更新及預先下載修補程式。 這需要對要修補的系統進行變更，請參閱[ 第一方和預先下載的支援](automation-configure-windows-update.md)，以了解如何在系統上配置這些設定。

您也可以透過程式設計方式建立**更新部署**。 若要了解如何使用 REST API 來建立**更新部署**，請參閱[軟體更新設定 - 建立](/rest/api/automation/softwareupdateconfigurations/create)。 此外，也有可用來建立每週**更新部署**的範例 Runbook。 若要深入了解此 Runbook，請參閱[為資源群組中的一或多個 VM 建立每週更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) \(英文\)。

## <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您就可以在 [更新管理] 之下的 [更新部署] 索引標籤上看到該部署的狀態。 目前正在執行部署時，其狀態會是 [進行中]。 部署順利完成時，狀態會變更為 [成功]。 當部署中發生一或多個更新失敗時，狀態就會是 [部分失敗]。

選取已完成的更新部署，即可查看該更新部署的儀表板。

![特定部署的更新部署狀態儀表板](./media/automation-tutorial-update-management/manageupdates-view-results.png)

在 [更新結果] 之下，會有摘要提供 VM 上的更新總數和部署結果。 右邊的表格會顯示每個更新的詳細明細及安裝結果。

下列清單會顯示可用的值：

* **未嘗試**：未安裝更新，因為根據所定義的維護時間範圍持續時間，可用的時間不足。
* **成功**：更新成功。
* **失敗**：更新失敗。

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]。

選取 [輸出]，以查看負責在目標 VM 上管理更新部署的 Runbook 作業串流。

若要查看部署所傳回的任何錯誤詳細資訊，請選取 [錯誤]。

更新部署成功時，系統會傳送類似於以下範例的電子郵件，指出部署成功：

![設定電子郵件動作群組](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將 VM 上架以進行更新管理
> * 檢視更新評估
> * 設定警示
> * 排定更新部署
> * 檢視部署的結果

繼續閱讀更新管理解決方案的概觀。

> [!div class="nextstepaction"]
> [更新管理解決方案](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

