---
title: 從 Runbook 啟用 Azure 自動化變更追蹤和清查
description: 本文說明如何從 Runbook 啟用變更追蹤和清查。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 014442dee1be23a189e22a505abf86050601b2aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826737"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Runbook 的 [啟用變更追蹤和清查]

此文說明如何使用 Runbook，為您環境中的 VM 啟用[變更追蹤和清查](change-tracking.md)功能。 若要大規模啟用 Azure VM，您必須使用「變更追蹤和清查」來啟用現有的 VM。 

> [!NOTE]
> 啟用「變更追蹤和清查」時，僅支援特定區域連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)，以管理電腦。
* [虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查 

1. 在 Azure 入口網站中，選取 [自動化帳戶]，然後在清單中選取您的自動化帳戶。
1. 在 [組態管理] 下選取 [清查]。
1. 選取現有的 Log Analytics 工作區，或建立新的工作區。 
1. 按一下 [啟用] 。

    ![啟用變更追蹤和清查](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>選取要管理的 Azure VM

啟用「變更追蹤和清查」之後，您可以新增 Azure VM 以供功能管理。

1. 從您的自動化帳戶中，選取 [組態管理] 底下的 [變更追蹤] 或 [清查]。

2. 按一下 [新增 Azure VM] 來新增您的 VM。

3. 從清單中選擇您的虛擬機器，並按一下 [啟用]。 此動作會啟用 VM 的「變更追蹤和清查」解決方案。

   ![為 VM 啟用「變更追蹤和清查」](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > 如果您嘗試在「變更追蹤和清查」設定完成之前啟用另一項功能，則會收到以下訊息：`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>安裝和更新模組

需要更新為最新的 Azure 模組並匯入 [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) 模組，才能成功為 VM 啟用「變更追蹤和清查」。

1. 在您的自動化帳戶中，選取 [共用資源] 下的 [模組]。 
2. 選取 [更新 Azure 模組]，可將 Azure 模組更新為最新版本。 
3. 按一下 [是]，即可將所有現有的 Azure 模組更新為最新版本。

    ![更新模組](media/automation-enable-changes-from-runbook/update-modules.png)

4. 返回 [共用資源] 之下的 [模組]。 
5. 選取 [瀏覽資源庫] 以開啟模組資源庫。 
6. 搜尋 Az.OperationalInsights，並將此模組匯入至自動化帳戶。

    ![匯入 OperationalInsights 模組](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>匯入 Runbook 以啟用「變更追蹤和清查」

1. 在您的自動化帳戶中，選取 [程序自動化] 下的 [Runbook]。
2. 選取 [瀏覽資源庫]。
3. 搜尋 `update and change tracking`。
4. 選取 Runbook，然後按一下 [檢視來源] 頁面上的 [匯入]。 
5. 按一下 [確定]，然後將 Runbook 匯入至自動化帳戶。

   ![匯入 Runbook 以供設定](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. 在 [Runbook] 頁面上，按一下 [編輯]，然後選取 [發佈]。 
7. 在 [發佈 Runbook] 窗格中，按一下 [是] 來發佈 Runbook。

## <a name="start-the-runbook"></a>啟動 Runbook

您必須已啟用 Azure VM 的「變更追蹤和清查」，才能啟動此 Runbook。 其需要參數已啟用此功能的現有 VM 和資源群組。

1. 開啟 **Enable-MultipleSolution** Runbook。

   ![多個解決方案 Runbook](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. 按一下 [開始] 按鈕，並在下列欄位中輸入參數值：

   * **VMNAME** - 要新增至「變更追蹤和清查」的現有 VM 名稱。 將此欄位保留空白，以新增資源群組中的所有 VM。
   * **VMRESOURCEGROUP** - 要啟用之 VM 的資源群組名稱。
   * **SUBSCRIPTIONID** - 要啟用之新 VM 的訂用帳戶識別碼。 將此欄位保留空白以使用工作區的訂用帳戶。 當您使用不同的訂用帳戶識別碼時，請為您的自動化帳戶新增執行身分帳戶作為訂用帳戶的參與者。
   * **ALREADYONBOARDEDVM** - 已手動啟用變更的 VM 名稱。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所屬的資源群組名稱。
   * **SOLUTIONTYPE** - 輸入 **ChangeTracking**。

   ![Enable-MultipleSolution Runbook 參數。](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. 選取 [確定] 以啟動 Runbook 作業。
1. 在 Runbook 作業頁面上監視進度和任何錯誤。

## <a name="next-steps"></a>後續步驟

* 若要排定 Runbook，請參閱[管理 Azure 自動化中的排程](shared-resources/schedules.md)。
* 如需使用此功能的詳細資訊，請參閱[管理變更追蹤和清查](change-tracking-file-contents.md)。
* 如需範圍設定的相關資訊，請參閱[使用變更追蹤和清查的範圍設定](automation-scope-configurations-change-tracking.md)。
* 若要了解如何使用此功能來識別安裝在環境中的軟體，請參閱[探索您的 VM 上安裝的軟體](automation-tutorial-installed-software.md)。
* 如果您在啟用此功能時不要將自動化帳戶與 Log Analytics 工作區整合，請參閱[從自動化帳戶取消連結工作區](automation-unlink-workspace-change-tracking.md)。
* VM 的變更部署完成後，您可以將 VM 移除，如[從變更追蹤和清查中移除 VM](automation-remove-vms-from-change-tracking.md) 所說明。
* 若要對此功能的一般問題進行疑難排解，請參閱[對變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。