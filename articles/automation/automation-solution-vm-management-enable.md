---
title: 啟用 Azure 自動化在下班時間啟動/停止 Vm 解決方案
description: 本文說明如何為您的 Azure 虛擬機器啟用 Azure 自動化啟動/停止 VM 解決方案。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096957"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>啟用 Azure 自動化啟動/停止 Vm 解決方案

執行下列步驟，將 **「停機期間啟動/停止 Vm」** 解決方案新增至新的或現有的自動化帳戶和連結的 Log Analytics 工作區。 完成上執行緒序後，設定變數以自訂解決方案。

>[!NOTE]
>若要搭配傳統 Vm 使用此解決方案，您需要傳統的執行身分帳戶，預設不會建立。 如需建立傳統執行身分帳戶的指示，請參閱[建立傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。
>

## <a name="enable-solution"></a>啟用解決方案

1. 登入 Azure[入口網站](https://portal.azure.com)。

2. 搜尋並選取 [**自動化帳戶**]。

3. 在 [自動化帳戶] 頁面上，從清單中選取您的自動化帳戶。

4. 從自動化帳戶中，選取 [**相關資源**] 底下的 [**啟動/停止 VM** ]。 您可以在此按一下**深入了解並啟用解決方案**。 如果您已經部署「啟動/停止 VM」解決方案，按一下 [管理解決方案]**** 並且在清單中選找它，即可加以選取。

   ![從自動化帳戶啟用](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > 您也可以按一下 [建立資源]****，在 Azure 入口網站中的任何地方建立它。 在 [Marketplace] 頁面中輸入關鍵字，例如**啟動**或**啟動/停止**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 或者，您可以輸入解決方案完整名稱中的一或多個關鍵字，然後按 Enter 鍵。 從搜尋結果中選取 [停機期間啟動/停止 VM]****。

5. 在所選解決方案的 [停機期間啟動/停止 VM]**** 頁面中，檢閱摘要資訊，然後按一下 [建立]****。

   ![Azure 入口網站](media/automation-solution-vm-management/azure-portal-01.png)

6. [新增解決方案] 頁面隨即出現。 系統會提示您設定解決方案，然後才可以將它匯入您的自動化訂用帳戶。

   ![VM 管理的新增解決方案頁面](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. 在 [新增解決方案] 頁面中，選取 [工作區]****。 選取連結到自動化帳戶所在之同一 Azure 訂用帳戶的 Log Analytics 工作區。 如果您沒有工作區，請選取 [建立新工作區]****。 在 [Log Analytics 工作區] 頁面上，執行下列步驟：

   - 指定新 Log Analytics 工作區的名稱，例如**ContosoLAWorkspace**。
   - 如果選取的預設值不合適，請從下拉式清單中選取要連結的**訂**用帳戶。
   - 對於 [資源群組]****，您可以建立新的資源群組，或選取現有的資源群組。
   - 選取 [**位置**]。
   - 選取 [**定價層**]。 選擇 [每 GB (獨立)]**** 選項。 Azure 監視器記錄具有更新的[價格](https://azure.microsoft.com/pricing/details/log-analytics/)，而每 GB 層是唯一的選項。

   > [!NOTE]
   > 啟用解決方案時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。
   >
   > 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

8. 在 [Log Analytics 工作區] 頁面上提供必要資訊之後，按一下 [建立]****。 您可以在功能表的 [通知]**** 下追蹤其進度，這會在完成時帶您返回 [新增解決方案] 頁面。

9. 在 [新增解決方案] 頁面中，選取 [自動化帳戶]****。 如果您要建立新的 Log Analytics 工作區，您可以建立要與其相關聯的新自動化帳戶，或選取尚未連結到 Log Analytics 工作區的現有自動化帳戶。 選取現有的自動化帳戶，或按一下 [**建立自動化帳戶**]，然後在 [新增自動化帳戶] 頁面上，提供下列資訊：
 
   - 在 [名稱]**** 欄位中輸入自動化帳戶的名稱。

     系統會根據所選的 Log Analytics 工作區自動填入所有其他選項。 這些選項無法修改。 Azure 執行身分帳戶是此方案內含 Runbook 的預設驗證方法。 按下 [確定]**** 後，就會驗證設定選項並建立自動化帳戶。 您可以在功能表的 [通知]**** 底下追蹤其進度。

10. 最後，在 [新增解決方案] 頁面中，選取 [設定]****。 [參數] 頁面隨即出現。

    ![解決方案的 [參數] 頁面](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   在這裡，系統會提示您：
  
   - 指定 [目標資源群組名稱]****。 這些值是包含此解決方案所要管理的虛擬機器之資源群組名稱。 您可以輸入多個名稱，然後使用逗號加以分隔 (值不區分大小寫)。 如果您想要以訂用帳戶的所有資源群組中的 VM 為目標，則可使用萬用字元。 此值儲存在 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 變數中。
  
   - 指定 [虛擬機器排除清單 (字串)]****。 此值是來自目標資源群組的一或多個虛擬機器名稱。 您可以輸入多個名稱，然後使用逗號加以分隔 (值不區分大小寫)。 支援使用萬用字元。 這個值會儲存在 **External_ExcludeVMNames** 變數中。
  
   - 選取**排程**。 選取排程的日期和時間。 將會從您選取的時間開始，建立每日重複發生的排程。 無法選取不同的區域。 在設定解決方案後，若要將排程設定為特定時區，請參閱[修改啟動和關機排程](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)。
  
   - 若要從動作群組接收**電子郵件通知**，請接受預設值 [是]****，並提供有效的電子郵件地址。 如果您選取 [否]****，但是日後決定想要收到電子郵件通知，您可以更新[動作群組](../azure-monitor/platform/action-groups.md)，該群組是以逗號分隔的有效電子郵件地址所建立。 您還需要啟用下列警示規則︰

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > 「目標資源群組名稱」**** 的預設值為 **&ast;**。 這樣會以訂用帳戶中的所有 VM 為目標。 如果您不想解決方案以您訂用帳戶中的所有 VM 為目標，在啟用排程之前，必須先將此值更新為資源群組名稱清單。

11. 設定好解決方案所需的初始設定後，按一下 [確定]**** 以關閉 [參數] 頁面，然後選取 [建立]****。 

驗證過所有設定之後，解決方案即會部署到您的訂用帳戶。 此程序需要幾秒鐘才能完成，您可以在功能表的 [通知]**** 底下追蹤其進度。

> [!NOTE]
> 如果您有 Azure 雲端解決方案提供者（Azure CSP）訂用帳戶，在部署完成之後，請移至 [**共用資源**] 底下的 [**變數**]，並將[External_EnableClassicVMs](automation-solution-vm-management.md#variables)變數設為**False**。 這會讓解決方案停止尋找傳統虛擬機器資源。

## <a name="next-steps"></a>後續步驟

現在您已啟用解決方案，您可以將其[設定](automation-solution-vm-management-config.md)為支援您的 VM 管理需求。