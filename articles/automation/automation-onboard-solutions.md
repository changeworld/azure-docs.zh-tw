---
title: 從 Runbook 啟用 Azure 自動化更新管理
description: 本文說明如何從 Runbook 啟用更新管理。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 95370a45b9fce69e5c1d06ac9bb987e1bfcfba4f
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204067"
---
# <a name="enable-update-management-from-a-runbook"></a>從 Runbook 啟用更新管理

本文說明如何使用 Tunbook，為您環境中的 VM 啟用[更新管理](automation-update-management.md)功能。 若要大規模啟用 Azure VM，您必須使用更新管理來啟用現有的 VM。 

> [!NOTE]
> 啟用更新管理時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)，以管理電腦。
* [虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="enable-update-management"></a>啟用更新管理

1. 在您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選取 Log Analytics 工作區，然後按一下 [啟用]。 啟用更新管理時，會顯示藍色橫幅。 

    ![啟用更新管理](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>選取要管理的 Azure VM

啟用更新管理後，您即可新增 Azure VM 以接收更新。

1. 從您的自動化帳戶，選取 [更新管理] 下的 [更新管理]。

2. 選取 [新增 Azure VM] 來新增您的 VM。

3. 從清單中選擇此 VM，然後按一下 [啟用] 來設定 VM 進行更新。 

   ![啟用 VM 的更新管理](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > 如果您嘗試在更新管理設定完成之前啟用另一項功能，則會收到以下訊息：`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>安裝和更新模組

需要更新為最新的 Azure 模組並匯入 [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) 模組，才能成功為 VM 啟用更新管理。

1. 在您的自動化帳戶中，選取 [共用資源] 下的 [模組]。 
2. 選取 [更新 Azure 模組]，可將 Azure 模組更新為最新版本。 
3. 按一下 [是]，即可將所有現有的 Azure 模組更新為最新版本。

    ![更新模組](media/automation-onboard-solutions/update-modules.png)

4. 返回 [共用資源] 之下的 [模組]。 
5. 選取 [瀏覽資源庫] 以開啟模組資源庫。 
6. 搜尋 `Az.OperationalInsights`，並將此模組匯入您的自動化帳戶。

    ![匯入 OperationalInsights 模組](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>匯入 Runbook 以啟用更新管理

1. 在您的自動化帳戶中，選取 [程序自動化] 下的 [Runbook]。
2. 選取 [瀏覽資源庫]。
3. 搜尋 `update and change tracking`。
4. 選取 Runbook，然後按一下 [檢視來源] 頁面上的 [匯入]。 
5. 按一下 [確定]，然後將 Runbook 匯入至自動化帳戶。

   ![匯入 Runbook 以供設定](media/automation-onboard-solutions/import-from-gallery.png)

6. 在 [Runbook] 頁面上，按一下 [編輯]，然後選取 [發佈]。 
7. 在 [發佈 Runbook] 窗格中，按一下 [是] 來發佈 Runbook。

## <a name="start-the-runbook"></a>啟動 Runbook

您必須已啟用 Azure VM 的更新管理，才能啟動此 Runbook。 其需要參數已啟用此功能的現有 VM 和資源群組。

1. 開啟 **Enable-MultipleSolution** Runbook。

   ![多個解決方案 Runbook](media/automation-onboard-solutions/runbook-overview.png)

2. 按一下 [開始] 按鈕，並在下列欄位中輸入參數值：

   * **VMNAME** - 要新增至更新管理的現有 VM 名稱。 將此欄位保留空白，以新增資源群組中的所有 VM。
   * **VMRESOURCEGROUP** - 要啟用之 VM 的資源群組名稱。
   * **SUBSCRIPTIONID** - 要啟用之新 VM 的訂用帳戶識別碼。 將此欄位保留空白以使用工作區的訂用帳戶。 當您使用不同的訂用帳戶識別碼時，請為您的自動化帳戶新增執行身分帳戶作為訂用帳戶的參與者。
   * **ALREADYONBOARDEDVM** - 已手動啟用更新的 VM 名稱。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所屬的資源群組名稱。
   * **SOLUTIONTYPE** - 輸入 [更新]。

   ![Enable-MultipleSolution Runbook 參數。](media/automation-onboard-solutions/runbook-parameters.png)

3. 選取 [確定] 以啟動 Runbook 作業。
4. 在 Runbook 作業頁面上監視進度和任何錯誤。

## <a name="next-steps"></a>後續步驟

* 若要排定 Runbook，請參閱[管理 Azure 自動化中的排程](shared-resources/schedules.md)。
* 若要使用 VM 的更新管理，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式的問題進行疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式的問題進行疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。