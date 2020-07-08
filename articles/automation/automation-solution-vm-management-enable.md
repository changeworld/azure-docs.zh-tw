---
title: 在 Azure 自動化的停機期間啟動/停止 VM
description: 本文說明如何在您的 Azure VM 中啟用「停機期間啟動/停止 VM」功能。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: dde2c3e4cf496bb15ca91c72d9a41936af7051c5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743748"
---
# <a name="enable-startstop-vms-during-off-hours"></a>啟用停機期間啟動/停止 VM

循序執行本主題中的步驟，以在使用新的或現有的自動化帳戶和連結的 Log Analytics 工作區的 VM 時，啟用「停機期間啟動/停止 VM」功能。 完成安裝流程後，設定變數以自訂功能。

>[!NOTE]
>若要對傳統 VM 使用此功能，您需要「傳統執行身分帳戶」(依預設不會建立)。 請參閱[建立傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。
>

## <a name="create-resources-for-the-feature"></a>針對功能建立資源

1. 登入 Azure [入口網站](https://portal.azure.com)。
2. 搜尋並選取 [自動化帳戶]。
3. 在 [自動化帳戶] 頁面上，從清單中選取您的自動化帳戶。
4. 從 [自動化帳戶] 中，選取 [相關資源] 之下的 [啟動/停止 VM]。 您可以在此按一下**深入了解並啟用解決方案**。 如果您已部署此功能，可以按一下 [管理解決方案] 並在清單中尋找該功能。

   ![從自動化帳戶啟用](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > 您也可以按一下 [建立資源]，在 Azure 入口網站中的任何地方建立資源。 在 [Marketplace] 頁面中輸入關鍵字，例如**啟動**或**啟動/停止**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 或者，您可以輸入功能完整名稱中的一或多個關鍵字，然後按 **Enter** 鍵。 從搜尋結果中選取 [停機期間啟動/停止 VM]。

5. 在所選部署的 [停機期間啟動/停止 VM] 頁面中，檢閱摘要資訊，然後按一下 [建立]。

   ![Azure 入口網站](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>設定功能

建立資源之後，就會出現 [新增解決方案] 頁面。 系統會提示您設定功能，以便匯入您的自動化訂用帳戶。 請參閱[設定在停機期間啟動/停止 VM](automation-solution-vm-management-config.md)。

   ![VM 管理的新增解決方案頁面](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>選取 Log Analytics 工作區

1. 在 [新增解決方案] 頁面中，選取 [工作區]。 選取連結到自動化帳戶所在之 Azure 訂用帳戶的 Log Analytics 工作區。 

2. 如果您沒有工作區，請選取 [建立新工作區]。 在 Log Analytics 工作區頁面上，執行下列步驟：

   - 為新的 Log Analytics 工作區指定名稱，例如 **ContosoLAWorkspace**。
   - 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
   - 對於 [資源群組]，您可以建立新的資源群組，或選取現有的資源群組。
   - 選取 [位置] 。
   - 選取 **定價層**。 選擇 [每 GB (獨立)] 選項。 Azure 監視器已更新[定價](https://azure.microsoft.com/pricing/details/log-analytics/)，每 GB 層是唯一選項。

   > [!NOTE]
   > 啟用功能時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

3. 在 Log Analytics 工作區頁面上提供必要資訊之後，按一下 [建立]。 您可以在功能表的 [通知] 下追蹤其進度，這會在完成時帶您返回新增解決方案頁面。

## <a name="add-automation-account"></a>加入自動化帳戶

再次存取新增解決方案頁面，並選取 [自動化帳戶]。 您可以選取尚未連結 Log Analytics 工作區的現有自動化帳戶。 如果要建立新的 Log Analytics 工作區，您可以建立新的自動化帳戶以與其建立關聯。 選取現有自動化帳戶或按一下 [建立自動化帳戶]，然後在新增自動化帳戶頁面上的 [名稱] 欄位中提供下列資訊。

系統會根據所選的 Log Analytics 工作區自動填入所有其他選項。 您無法修改這些選項。 Azure 執行身分帳戶是此功能內含 Runbook 的預設驗證方法。 

按下 [確定] 後，就會驗證設定選項並建立自動化帳戶。 您可以在功能表的 [通知] 底下追蹤其進度。

## <a name="define-feature-parameters"></a>定義功能參數

1. 在新增解決方案頁面中，選取 [組態]。 參數頁面隨即出現。

    ![解決方案的 [參數] 頁面](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. 指定**目標資源群組名稱**欄位。 欄位會定義包含要管理之功能 VM 的群組名稱。 您可以輸入多個名稱，然後使用逗號加以分隔 (值不區分大小寫)。 如果您想要以訂用帳戶的所有資源群組中的 VM 為目標，則可使用萬用字元。 這些值會儲存在 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 變數中。

    > [!IMPORTANT]
    > 「目標資源群組名稱」的預設值為 **&ast;** 。 此設定會以訂用帳戶中的所有 VM 為目標。 如果您不想讓此功能以訂用帳戶中的所有 VM 為目標，必須先提供資源群組名稱的清單，然後再選取排程。
  
3. 指定 **VM 排除清單 (字串)** 欄位的值。 此值是來自目標資源群組的一或多個虛擬機器名稱。 您可以輸入多個名稱，然後使用逗號加以分隔 (值不區分大小寫)。 支援使用萬用字元。 此值儲存在變數 `External_ExcludeVMNames` 中。
  
4. 使用 [排程] 欄位，依功能選取 VM 管理的排程。 選取排程的開始日期和時間，以建立從所選時間開始的每日週期性排程。 無法選取不同的區域。 在設定功能後，若要將排程設定為特定時區，請參閱[修改啟動和關機排程](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)。

5. 若要從[動作群組](../azure-monitor/platform/action-groups.md)接收電子郵件通知，請接受**電子郵件通知**欄位中的預設值**是**，並提供有效的電子郵件地址。 如果您選取**否**，但是日後決定想要收到電子郵件通知，您可以更新動作群組，該群組是以逗號分隔的有效電子郵件地址所建立。 

6. 啟用下列警示規則︰

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>建立警示

停機期間啟動/停止 VM 不會包含預先定義的一組警示。 請檢閱[使用 Azure 監視器建立記錄警示](../azure-monitor/platform/alerts-log.md)，以了解如何建立作業失敗的警示，以支援您的 DevOps 或作業流程和程序。

## <a name="deploy-the-feature"></a>部署功能

1. 設定好功能所需的初始設定後，按一下 [確定] 以關閉參數頁面。

2. 按一下頁面底部的 [新增] 。 驗證過所有設定後，功能即會部署到您的訂用帳戶。 此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。

    > [!NOTE]
    > 如果您有「Azure 雲端解決方案提供者」(Azure CSP) 訂用帳戶，請在部署完成之後，在您的「自動化帳戶」中，前往**共用資源**下的**變數**，並將 [External_EnableClassicVMs](automation-solution-vm-management.md#variables) 變數設定為 **False**。 這會讓解決方案停止尋找傳統虛擬機器資源。

## <a name="next-steps"></a>後續步驟

* 要設定功能，請參閱[設定停機期間啟動/停止 VM](automation-solution-vm-management-config.md)。
* 要解決功能錯誤，請參閱[停機期間啟動/停止 VM 問題疑難排解](troubleshoot/start-stop-vm.md)。