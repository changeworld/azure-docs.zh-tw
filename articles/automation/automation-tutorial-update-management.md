---
title: 在 Azure 自動化中管理 Azure VM 的更新和修補程式
description: 本文說明如何使用更新管理來管理 Azure VM 的更新和修補程式。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 79cffa7aedd0fc04dd4a747ef28bc67cacf37905
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204883"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>管理 Azure VM 的更新和修補程式

本文說明如何使用 Azure 自動化的[更新管理](automation-update-management.md)功能來管理 Azure VM 的更新和修補程式。 如需價格資訊，請參閱[更新管理的自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

> [!NOTE]
> 更新管理支援部署第一方更新及預先下載修補程式。 這種支援需要在修補的系統上進行變更。 如需了解如何在系統上設定這些設定，請參閱[設定 Windows Update 設定以進行 Azure 自動化更新管理](automation-configure-windows-update.md)。

使用本文中的程序之前，請確定您已使用下列其中一種技術，在您的虛擬機器上啟用更新管理：

* [從自動化帳戶啟用更新管理](automation-onboard-solutions-from-automation-account.md)
* [藉由瀏覽 Azure 入口網站來啟用更新管理](automation-onboard-solutions-from-browse.md)
* [從 Runbook 啟用更新管理](automation-onboard-solutions.md)
* [從 Azure VM 啟用更新管理](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>限制部署的範圍

更新管理會使用工作區中的範圍設定，來鎖定要接收更新的電腦。 如需詳細資訊，請參閱[限制更新管理部署範圍](automation-scope-configurations-update-management.md)。

## <a name="view-update-assessment"></a>檢視更新評量

若要檢視更新評估：

1. 在您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。 

2. [更新管理] 頁面上會列出您環境的更新。 如果發現遺失了任何更新，[遺失更新] 索引標籤上會顯示遺失的更新清單。

3. 在 [資訊連結] 下，選取某項更新的連結可開啟支援文章，其中提供有關更新的重要資訊。

    ![檢視更新狀態](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. 按一下更新的任何其他地方，即可開啟 [記錄搜尋] 窗格。 記錄搜尋的查詢已針對該特定更新預先定義。 您可以修改此查詢或建立您自己的查詢，以檢視詳細資訊。

    ![檢視更新狀態](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>設定警示

遵循下列步驟來設定警示，以讓您得知更新部署的狀態：

1. 在自動化帳戶的 [監視] 下，移至 [警示]，然後按一下 [新增警示規則]。

2. 在 [建立警示規則] 頁面上，已選取您的自動化帳戶作為資源。 如果想變更此值，請按一下 [編輯資源]。 

3. 在 [選取資源] 頁面上，從 [依資源類型篩選] 下拉式功能表中選取 [自動化帳戶]。 

4. 選取您想要使用的自動化帳戶，然後按一下 [完成]。

5. 按一下 [新增條件] 以選取您更新部署適用的訊號。 下表顯示兩個可用訊號的詳細資料。

    |訊號名稱|維度|描述
    |---|---|---|
    |`Total Update Deployment Runs`|- 更新部署名稱<br>- 狀態    |針對更新部署的整體狀態發出警示。|
    |`Total Update Deployment Machine Runs`|- 更新部署名稱</br>- 狀態</br>- 目標電腦</br>- 更新部署執行識別碼    |針對鎖定特定機器的更新部署狀態發出警示。|

6. 針對維度，請從清單中選取有效的值。 如果清單中沒有您希望的值，請按一下維度旁邊的 **\+** ，然後輸入自訂名稱。 然後選取要尋找的值。 如果想要針對維度選取所有值，請按一下 [選取 \*] 按鈕。 如果您沒有為維度選取值，則更新管理會忽略該維度。

    ![設定訊號邏輯](./media/automation-tutorial-update-management/signal-logic.png)

7. 在 [警示邏輯] 下，於 [時間彙總] 和 [閾值] 欄位中輸入值，然後按一下 [完成]。

8. 在下一個窗格中，輸入警示的名稱和描述。

9. 將執行成功的 [嚴重性] 欄位設定為 [資訊 (嚴重性 2)]，或將執行失敗的嚴重性設定為 [資訊 (嚴重性 1)]。

    ![設定訊號邏輯](./media/automation-tutorial-update-management/define-alert-details.png)

10. 視您想要啟用警示規則的方式而定，按一下 [是] 或 [否]。

11. 如果您不想要有此規則的警示，請選取 [隱藏警示]。

## <a name="configure-action-groups-for-your-alerts"></a>設定警示的動作群組

設定好警示之後，您就可以設定動作群組，這是可跨多個警示使用的一組動作。 這些動作可包括電子郵件通知、Runbook、Webhook 和等多種項目。 若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

1. 選取警示，然後選取 [動作群組] 下的 [新建]。 

2. 輸入動作群組的完整名稱和簡短名稱。 當使用指定的群組傳送通知時，更新管理會使用簡短名稱。

3. 在 [動作] 中，輸入指定動作的名稱，例如**電子郵件通知**。 

4. 針對 [動作類型]，選取適當的類型，例如 [電子郵件/簡訊/推送/語音]。 

5. 按一下 [編輯詳細資料]。

6. 在窗格中填入動作類型。 例如，如果是使用 [電子郵件/簡訊/推送/語音]，請輸入動作名稱，選取 [電子郵件] 核取方塊，輸入有效的電子郵件地址，然後按一下 [確定]。

    ![設定電子郵件動作群組](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. 在 [新增動作群組] 窗格中，按一下 [確定]。

8. 如需警示電子郵件，您可以自訂電子郵件主旨。 選取 [建立規則] 下的 [自訂動作]，然後選取 [電子郵件主旨]。 

9. 當您完成時，按一下 [建立警示規則]。 

## <a name="schedule-an-update-deployment"></a>排定更新部署

排程更新部署時，將會建立一個連結至 **Patch-MicrosoftOMSComputers** Runbook 的[排程](shared-resources/schedules.md)資源，以處理目標電腦上的更新部署。 您必須將部署安排在發行排程和服務時間範圍之後，以便安裝更新。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

>[!NOTE]
>如果您在建立部署後從 Azure 入口網站或使用 PowerShell 刪除了排程資源，將會中斷已排程的更新部署，並在您嘗試從入口網站重新設定排程資源時顯示錯誤。 您只能藉由刪除對應的部署排程來刪除排程資源。  

若要排程新的更新部署：

1. 在您的自動化帳戶中，移至 [更新管理] 底下的 [更新管理]，然後選取 [排程更新部署]。

2. 在 [新增更新部署] 下，使用 [名稱] 欄位來輸入部署的唯一名稱。

3. 選取要進行更新部署的目標作業系統。

4. 在 [要更新的群組 (預覽)] 區域中，結合訂用帳戶、資源群組、位置及標記來定義查詢，以建立要包含在您部署中的動態 Azure VM 群組。 若要進一步了解，請參閱[搭配更新管理使用動態群組](automation-update-management-groups.md)。

5. 在 [要更新的電腦] 中，選取已儲存的搜尋、已匯入的群組，或從下拉式功能表中選擇 [機器]，然後選取個別的機器。 利用此選項，您可以查看每一部機器的 Log Analytics 代理程式的整備狀態。 若要深入了解在 Azure 監視器記錄中建立電腦群組的不同方法，請參閱 [Azure 監視器記錄中的電腦群組](../azure-monitor/platform/computer-groups.md)。

6. 使用 [更新分類] 區域來指定產品的[[更新分類]](automation-view-update-assessments.md#work-with-update-classifications)。 針對每個產品，取消選取所有支援的更新分類，但不要取消選取要納入您更新部署中的項目。

7. 使用 [包含/排除更新] 區域來選取要部署的特定更新。 [包含/排除] 頁面會依知識庫文章識別碼顯示要包含或排除的更新。 
    
   > [!IMPORTANT]
   > 請記得，排除項目會覆寫包含項目。 例如，如果您定義排除規則 `*`，更新管理即會將所有修補程式或套件從安裝中排除。 排除的修補程式仍然會顯示為從機器中遺漏。 若使用 Linux 電腦，如果您納入已排除之相依套件的套件，更新管理不會安裝主要套件。

   > [!NOTE]
   > 您無法指定將已取代的更新納入更新部署中。

8. 選取 [排程設定]。 預設開始時間為目前時間之後的 30 分鐘。 您可以將開始時間設為 10 分鐘以後的任何時間。

9. 使用 [週期性] 欄位來指定部署是否應發生一次或使用週期性排程，然後按一下 [確定]。

10. 在 [前置指令碼 + 後置指令碼 (預覽)] 區域，選取在部署前和部署後要執行的指令碼。 若要深入了解，請參閱[管理前指令碼和後指令碼](pre-post-scripts.md)。
    
11. 使用 [維護時間範圍 (分鐘)] 欄位，指定允許安裝更新的時間長度。 在指定維護時間範圍時，請考慮下列詳細資料：

    * 維護時間範圍可控制要安裝的更新數目。
    * 如果維護時間範圍即將結束，更新管理並不會停止安裝新的更新。
    * 如果超出維護時間範圍，更新管理並不會終止進行中的更新。
    * 如果在 Windows 上超出維護時間範圍，通常是因為 Service Pack 更新需要很長的時間才能安裝完成。

    > [!NOTE]
    > 若要避免在 Ubuntu 維護時間範圍以外套用更新，請重新設定 `Unattended-Upgrade` 套件以停用自動更新。 如需有關如何設定套件的資訊，請參閱 [Ubuntu Server 指南中的自動更新主題](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

12. 使用 [重新開機選項] 欄位，指定在部署期間處理重新開機的方式。 有下列選項可供使用： 
    * 必要時重新開機 (預設值)
    * 一律重新開機
    * 永不重新開機
    * 僅重新開機；此選項不會安裝更新

    > [!NOTE]
    > 如果 [重新開機選項] 已設定為 [永不重新開機]，在[用來管理重新啟動的登錄機碼](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)底下所列的登錄機碼可能會造成重新開機事件。

13. 部署排程設定完成後，請按一下 [建立]。

    ![更新排程設定窗格](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. 您會回到狀態儀表板。 選取 [排程更新部署] 可顯示您所建立的部署排程。

## <a name="schedule-an-update-deployment-programmatically"></a>以程式設計方式排程更新部署

若要了解如何使用 REST API 來建立更新部署，請參閱[軟體更新設定 - 建立](/rest/api/automation/softwareupdateconfigurations/create)。 

您也可以使用 Runbook 範例來建立每週更新部署。 若要深入了解此 Runbook，請參閱[為資源群組中的一或多個 VM 建立每週更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) \(英文\)。

## <a name="check-deployment-status"></a>檢查部署狀態

排定的部署開始之後，您可以在 [更新管理] 底下的 [更新部署] 索引標籤上查看其狀態。 目前正在執行部署時，其狀態會是 [進行中]。 部署順利完成後，狀態會變更為**已成功**。 若部署中發生一或多個更新失敗時，狀態就會是**部分失敗**。

## <a name="view-results-of-a-completed-update-deployment"></a>檢視已完成更新部署的結果

當部署完成時，您可以選取它來查看其儀表板。

![特定部署的更新部署狀態儀表板](./media/automation-tutorial-update-management/manageupdates-view-results.png)

在 [更新結果] 之下，會有摘要提供目標虛擬機器上的更新總數和部署結果。 右邊的表格會顯示每個更新的詳細明細及安裝結果。

可用的值為：

* **未嘗試** - 未安裝更新，因為根據所定義的維護時間範圍持續時間，可用的時間不足。
* **未選取** - 未選取該更新來進行部署。
* **成功** - 更新已順利完成。
* **失敗** - 更新失敗。

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]。

選取 [輸出]，以查看負責在目標 VM 上管理更新部署的 Runbook 作業串流。

若要查看部署所傳回的任何錯誤詳細資訊，請選取 [錯誤]。

## <a name="view-the-deployment-alert"></a>檢視部署警示

當您的更新部署完成時，您會收到您在部署設定期間所指定的警示。 例如，以下是確認修補程式的電子郵件。

![設定電子郵件動作群組](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>後續步驟

* 如需範圍設定的詳細資訊，請參閱[限制更新管理部署範圍](automation-scope-configurations-update-management.md)。
* 如果您需要搜尋儲存在 Log Analytics 工作區中的記錄，請參閱 [Azure 監視器記錄中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
* 如果已完成部署，請參閱[取消工作區與自動化帳戶的連結以進行更新管理部署](automation-unlink-workspace-update-management.md)。
* 若要從更新管理中刪除您的 VM，請參閱[從更新管理中移除 VM](automation-remove-vms-from-update-management.md)。
* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式的問題進行疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式的問題進行疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。