---
title: 從 Runbook 啟用 Azure 自動化更新管理
description: 本文說明如何從 Runbook 啟用更新管理。
services: automation
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: ec102015355e3312f5dc15fa526fa543da75e0de
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222079"
---
# <a name="enable-update-management-from-a-runbook"></a>從 Runbook 啟用更新管理

本文說明如何使用 Tunbook，為您環境中的 VM 啟用[更新管理](overview.md)功能。 若要大規模啟用 Azure Vm，您必須使用更新管理啟用現有的 VM。

> [!NOTE]
> 啟用更新管理時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](../how-to/region-mappings.md)。

這個方法會使用兩個 runbook：

* **Enable-multiplesolution** -提示進行設定資訊的主要 runbook、查詢指定的 VM 並執行其他驗證檢查，然後叫用 **AutomationSolution** runbook，為指定資源群組內的每個 VM 設定更新管理。
* **AutomationSolution** -啟用目標資源群組中指定的一或多個 vm 的更新管理。 它會確認符合必要條件、確認已安裝 Log Analytics VM 延伸模組，並在找不到的情況下進行安裝，並將 Vm 新增至連結至自動化帳戶之指定 Log Analytics 工作區中的範圍設定。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../automation-security-overview.md)，以管理電腦。
* [Log Analytics 工作區](../../azure-monitor/platform/design-logs-deployment.md)
* [虛擬機器](../../virtual-machines/windows/quick-create-portal.md)。
* **AutomationSolution** runbook 所使用的兩個自動化資產。 此 runbook 如果尚未存在於您的自動化帳戶中，則會在第一次執行時自動匯入 **enable-multiplesolution** runbook。
    * *LASolutionSubscriptionId*： Log Analytics 工作區所在位置的訂用帳戶識別碼。
    * *LASolutionWorkspaceId*：連結至您的自動化帳戶之 Log Analytics 工作區的工作區識別碼。

    這些變數可用來設定上線 VM 的工作區。 如果未指定這些，腳本會先搜尋任何 VM 上線，以在其訂用帳戶中更新管理，後面接著自動化帳戶所在的訂用帳戶，後面接著您的使用者帳戶可以存取的所有其他訂用帳戶。 如果未正確設定，這可能會導致您的機器上線至某些隨機的 Log Analytics 工作區。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-update-management"></a>啟用更新管理

1. 在 Azure 入口網站中，流覽至 [ **自動化帳戶**]。 在 [ **自動化帳戶** ] 頁面上，從清單中選取您的帳戶。

2. 在您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

3. 選取 Log Analytics 工作區，然後按一下 [啟用]。 啟用更新管理時，會顯示橫幅。

    ![啟用更新管理](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>安裝和更新模組

您必須更新至最新的 Azure 模組並匯入 [OperationalInsights](/powershell/module/az.operationalinsights) 模組，才能使用 runbook 成功啟用 vm 的更新管理。

1. 在您的自動化帳戶中，選取 [共用資源] 下的 [模組]。

2. 選取 [更新 Azure 模組]，可將 Azure 模組更新為最新版本。

3. 按一下 [是]，即可將所有現有的 Azure 模組更新為最新版本。

    ![更新模組](media/enable-from-runbook/update-modules.png)

4. 返回 [共用資源] 之下的 [模組]。

5. 選取 [瀏覽資源庫] 以開啟模組資源庫。

6. 搜尋 `Az.OperationalInsights`，並將此模組匯入您的自動化帳戶。

    ![匯入 OperationalInsights 模組](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>選取要管理的 Azure VM

啟用更新管理後，您即可新增 Azure VM 以接收更新。

1. 從您的自動化帳戶中，選取 [**更新管理**] 區段底下的 [**更新管理**]。

2. 選取 [新增 Azure VM] 來新增您的 VM。

3. 從清單中選擇 VM，然後按一下 [ **啟用** ] 設定 vm 進行管理。

   ![啟用 VM 的更新管理](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > 如果您嘗試在更新管理設定完成之前啟用另一項功能，則會收到以下訊息：`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>匯入 Runbook 以啟用更新管理

1. 在您的自動化帳戶中，選取 [程序自動化] 下的 [Runbook]。

2. 選取 [瀏覽資源庫]。

3. 搜尋 **更新和變更追蹤**。

4. 選取 runbook，然後按一下 [**視圖來源**] 頁面上的 [匯**入**]。

5. 按一下 [確定]，然後將 Runbook 匯入至自動化帳戶。

   ![匯入 Runbook 以供設定](media/enable-from-runbook/import-from-gallery.png)

6. 在 [ **Runbook** ] 頁面上，選取 [ **啟用-enable-multiplesolution** ] Runbook，然後按一下 [ **編輯**]。 在 [文字編輯器] 上，選取 [  **發行**]。

7. 當系統提示您確認時，請按一下 **[是]** 以發佈 runbook。

## <a name="start-the-runbook"></a>啟動 Runbook

您必須已啟用 Azure VM 的更新管理，才能啟動此 Runbook。 它需要現有的 VM 和已啟用功能的資源群組，才能設定目標資源群組中的一或多個 Vm。

1. 開啟 **Enable-MultipleSolution** Runbook。

   ![多個解決方案 Runbook](media/enable-from-runbook/runbook-overview.png)

2. 按一下 [開始] 按鈕，並在下列欄位中輸入參數值：

   * **VMNAME** - 要新增至更新管理的現有 VM 名稱。 將此欄位保留空白，以新增資源群組中的所有 VM。
   * **VMRESOURCEGROUP** - 要啟用之 VM 的資源群組名稱。
   * **SUBSCRIPTIONID** - 要啟用之新 VM 的訂用帳戶識別碼。 將此欄位保留空白以使用工作區的訂用帳戶。 當您使用不同的訂用帳戶識別碼時，請為您的自動化帳戶新增執行身分帳戶作為訂用帳戶的參與者。
   * **ALREADYONBOARDEDVM** - 已手動啟用更新的 VM 名稱。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所屬的資源群組名稱。
   * **SOLUTIONTYPE** - 輸入 [更新]。

   ![Enable-MultipleSolution Runbook 參數。](media/enable-from-runbook/runbook-parameters.png)

3. 選取 [確定] 以啟動 Runbook 作業。

4. 從 [ **工作** ] 頁面監視 runbook 作業的進度和任何錯誤。

## <a name="next-steps"></a>後續步驟

* 若要使用 Vm 的更新管理，請參閱 [管理 vm 的更新和修補程式](manage-updates-for-vm.md)。

* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](../troubleshoot/update-management.md)。
