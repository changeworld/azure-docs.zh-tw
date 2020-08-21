---
title: 如何建立 Azure 自動化更新管理的更新部署
description: 本文說明如何排程更新部署並檢查其狀態。
services: automation
ms.subservice: update-management
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 4336ba272dd83ad2a35060c1c7524a564b928484
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717688"
---
# <a name="how-to-deploy-updates-and-review-results"></a>如何部署更新和檢查結果

本文說明如何排程更新部署，並在部署完成後檢查程式。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>排定更新部署

排程更新部署時，將會建立一個連結至 **Patch-MicrosoftOMSComputers** Runbook 的[排程](../shared-resources/schedules.md)資源，以處理目標電腦上的更新部署。 您必須將部署安排在發行排程和服務時間範圍之後，以便安裝更新。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

>[!NOTE]
>如果您在建立部署後從 Azure 入口網站或使用 PowerShell 刪除了排程資源，將會中斷已排程的更新部署，並在您嘗試從入口網站重新設定排程資源時顯示錯誤。 您只能藉由刪除對應的部署排程來刪除排程資源。  

若要排程新的更新部署：

1. 在您的自動化帳戶中，移至 [更新管理] 底下的 [更新管理]，然後選取 [排程更新部署]。

2. 在 [ **新增更新部署**] 下的 [ **名稱** ] 欄位中，輸入部署的唯一名稱。

3. 選取要進行更新部署的目標作業系統。

4. 在 [要更新的群組 (預覽)] 區域中，結合訂用帳戶、資源群組、位置及標記來定義查詢，以建立要包含在您部署中的動態 Azure VM 群組。 若要進一步了解，請參閱[搭配更新管理使用動態群組](update-mgmt-groups.md)。

5. 在 [要更新的電腦] 中，選取已儲存的搜尋、已匯入的群組，或從下拉式功能表中選擇 [機器]，然後選取個別的機器。 利用此選項，您可以查看每一部機器的 Log Analytics 代理程式的整備狀態。 若要深入了解在 Azure 監視器記錄中建立電腦群組的不同方法，請參閱 [Azure 監視器記錄中的電腦群組](../../azure-monitor/platform/computer-groups.md)。

6. 使用 [更新分類] 區域來指定產品的[[更新分類]](update-mgmt-view-update-assessments.md#work-with-update-classifications)。 針對每個產品，取消選取所有支援的更新分類，但不要取消選取要納入您更新部署中的項目。

    如果您的部署只是要套用一組選取的更新，則必須在設定 **包含/排除更新** 選項時，取消選取所有預先選取的更新分類，如下一個步驟所述。 這可確保只有您已指定要 *包含* 在此部署中的更新會安裝在目的電腦上。

7. 您可以使用 **包含/排除更新** 區域，在部署中新增或排除選取的更新。 在 [ **包含/排除** ] 頁面上，您可以輸入要包含或排除的 KB 文章識別碼編號。

   > [!IMPORTANT]
   > 請記得，排除項目會覆寫包含項目。 例如，如果您定義排除規則 `*`，更新管理即會將所有修補程式或套件從安裝中排除。 排除的修補程式仍然會顯示為從機器中遺漏。 若使用 Linux 電腦，如果您納入已排除之相依套件的套件，更新管理不會安裝主要套件。

   > [!NOTE]
   > 您無法指定將已取代的更新納入更新部署中。

8. 選取 [排程設定]。 預設開始時間為目前時間之後的 30 分鐘。 您可以將開始時間設為 10 分鐘以後的任何時間。

9. 使用 [ **週期** ] 欄位來指定部署是否發生一次或使用週期性排程，然後選取 **[確定]**。

10. 在 [前置指令碼 + 後置指令碼 (預覽)] 區域，選取在部署前和部署後要執行的指令碼。 若要深入了解，請參閱[管理前指令碼和後指令碼](update-mgmt-pre-post-scripts.md)。
    
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

13. 當您完成部署排程的設定時，請選取 [ **建立**]。

    ![更新排程設定窗格](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. 您會回到狀態儀表板。 選取 [排程更新部署] 可顯示您所建立的部署排程。

## <a name="schedule-an-update-deployment-programmatically"></a>以程式設計方式排程更新部署

若要了解如何使用 REST API 來建立更新部署，請參閱[軟體更新設定 - 建立](/rest/api/automation/softwareupdateconfigurations/create)。

您也可以使用 Runbook 範例來建立每週更新部署。 若要深入了解此 Runbook，請參閱[為資源群組中的一或多個 VM 建立每週更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) \(英文\)。

## <a name="check-deployment-status"></a>檢查部署狀態

排定的部署開始之後，您可以在 [更新管理] 底下的 [更新部署] 索引標籤上查看其狀態。 目前正在執行部署時，其狀態會是 [進行中]。 部署順利完成後，狀態會變更為**已成功**。 若部署中發生一或多個更新失敗時，狀態就會是**部分失敗**。

## <a name="view-results-of-a-completed-update-deployment"></a>檢視已完成更新部署的結果

當部署完成時，您可以選取它來查看其結果。

[![更新特定部署的部署狀態儀表板](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

在 [更新結果] 之下，會有摘要提供目標虛擬機器上的更新總數和部署結果。 右邊的表格會顯示每個更新的詳細明細及安裝結果。

可用的值為：

* **未嘗試** - 未安裝更新，因為根據所定義的維護時間範圍持續時間，可用的時間不足。
* **未選取** - 未選取該更新來進行部署。
* **成功** - 更新已順利完成。
* **失敗** - 更新失敗。

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]。

選取 [輸出]，以查看負責在目標 VM 上管理更新部署的 Runbook 作業串流。

若要查看部署所傳回的任何錯誤詳細資訊，請選取 [錯誤]。

## <a name="next-steps"></a>後續步驟

若要瞭解如何建立警示來通知您有關更新部署的結果，請參閱 [建立更新管理的警示](update-mgmt-configure-alerts.md)。