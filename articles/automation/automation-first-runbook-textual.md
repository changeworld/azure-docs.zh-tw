---
title: 我在 Azure 自動化中的第一個 PowerShell 工作流程 Runbook
description: 本教學課程逐步引導您使用 PowerShell 工作流程建立、測試和發佈簡單的文字 Runbook。
keywords: powershell 工作流程, powershell 工作流程範例, 工作流程 powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 16b6a0cf3e43b172667f55b1ac95e8a278769f9d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246392"
---
# <a name="my-first-powershell-workflow-runbook"></a>我的第一個 PowerShell 工作流程 Runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 從您測試和發佈的簡單 runbook 開始，同時瞭解如何追蹤 runbook 作業的狀態。 然後修改 runbook 以實際管理 Azure 資源，並透過啟動 Azure 虛擬機器來說明。 最後，藉由新增 runbook 參數，讓 runbook 更穩固。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 由於您會停止並啟動這部電腦，因此它不應該是生產 VM。

## <a name="step-1---create-new-runbook"></a>步驟 1 - 建立新的 Runbook

您會由建立一個可輸出文字 Hello World的簡單 Runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。

   [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 這些資產大部分都是自動包含在新的自動化帳戶中的模組。 您也應該擁有與您的訂用帳戶相關聯的認證資產。
 
1. 選取 [程式**自動化**] 底下的 [ **runbook** ] 以開啟 runbook 清單。
1. 選取 [**建立 runbook**] 來建立新的 runbook。
1. 將 Runbook 命名為「MyFirstRunbook-Workflow」。
1. 在此情況下，您要建立[PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 因此，請選取 [ **Powershell 工作流程**] 作為 [ **Runbook 類型**]。
1. 按一下 [建立] 來建立 Runbook 並開啟文字式編輯器。

## <a name="step-2---add-code-to-the-runbook"></a>步驟 2 - 將程式碼加入至 runbook

您可以直接在 runbook 中輸入程式碼，也可以從程式庫控制項選取 Cmdlet、runbook 和資產，並使用任何相關參數將其新增至 runbook。 在本教學課程中，您會直接在 runbook 中輸入程式碼。

1. 您的 runbook 目前是空白的，只有必要的**Workflow**關鍵字、runbook 的名稱，以及將整個工作流程的大括弧。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. 在大括弧之間輸入 `Write-Output "Hello World"`。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. 按一下 [儲存]來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a>步驟 3 - 測試 Runbook

在您發佈 runbook 以讓它可在生產環境中使用之前，您應該先進行測試，以確定它能正常運作。 測試 runbook 會執行其草稿版本，並可讓您以互動方式來查看其輸出。

1. 選取 [**測試窗格]** 以開啟 [測試] 窗格。
1. 按一下 [**啟動**] 以啟動測試，並測試 [唯一啟用] 選項。
1. 請注意，會建立[runbook 工作](automation-runbook-execution.md)，並在窗格中顯示其狀態。

   作業狀態會以「已**排入佇列**」開始，表示作業正在等候雲端中的 runbook 背景工作可供使用。 當背景工作宣告作業時，狀態會變更為 [**正在啟動**]。 最後，當 runbook 實際開始執行時，狀態會變成 [**正在**執行]。

1. 當 runbook 作業完成時，[測試] 窗格會顯示其輸出。 在此情況下，您會看到 Hello World。

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您建立的 runbook 仍處於草稿模式。 您必須先發佈該檔案，才能在生產環境中執行。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 按一下 [發佈] 來發佈 Runbook，然後出現提示時按一下 [是]。
1. 在 [ **runbook** ] 頁面中向左流覽以查看 runbook，並注意 [**撰寫狀態**] 欄位設定為 [**已發佈**]。
1. 向右返回以查看**MyFirstRunbook-Workflow**的頁面。

   在頂端的選項可讓您立即啟動 runbook、排程未來的開始時間，或建立[webhook](automation-webhooks.md) ，以便透過 HTTP 呼叫來啟動 runbook。

1. 選取 [開始]，然後在系統提示時選取 [是]，以啟動 Runbook。

   ![啟動 Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. 已建立的 runbook 作業會開啟 [作業] 窗格。 在此情況下，讓窗格保持開啟狀態，讓您可以觀賞作業的進度。

1. 請注意，作業狀態會顯示在 [**作業摘要**] 中。 此狀態會符合您在測試 runbook 時看到的狀態。

   ![工作摘要](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. 一旦 Runbook 狀態顯示 [已完成]，請按一下 [輸出]。 [輸出] 頁面隨即開啟，您可以在其中看到**Hello World**訊息。

   ![工作摘要](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. 關閉 [輸出] 頁面。

1. 按一下 [所有記錄] 以開啟 Runbook 作業的 [資料流] 窗格。 您應該只會在輸出資料流程中看到**Hello World** 。 請注意，[資料流程] 窗格可以顯示 runbook 作業的其他資料流程，例如 [詳細資訊] 和 [錯誤資料流程] （如果 runbook 寫入它們）。

   ![工作摘要](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. 關閉 [資料流程] 窗格和 [作業] 窗格，以返回 [ **MyFirstRunbook** ] 頁面。
1. 按一下 [**資源**] 下方的 [**作業**]，開啟此 runbook 的 [作業] 頁面。 此頁面會列出您的 runbook 所建立的所有作業。 您應該只會看到列出一項作業，因為您只會執行一次作業。

   ![工作](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. 按一下作業名稱，開啟您在啟動 runbook 時所看到的相同作業窗格。 使用此窗格來查看針對 runbook 所建立之任何作業的詳細資料。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步驟 5 - 加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。 除非使用訂用帳戶的認證進行驗證，否則無法執行此動作。 驗證會使用**disconnect-azaccount** Cmdlet。

>[!NOTE]
>針對 PowerShell runbook， **disconnect-azaccount**和**add-AzureRMAccount**是**disconnect-azaccount**的別名。 您可以使用這些 Cmdlet，也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)為最新版本。 即使您剛建立新的自動化帳戶，也可能需要更新您的模組。

1. 流覽至 [ **MyFirstRunbook-Workflow** ] 頁面，然後按一下 [**編輯**] 來開啟文字式編輯器。
2. 刪除 `Write-Output` 行。
3. 將游標放在大括弧之間的空白行。
4. 輸入或複製並貼上下列程式碼，以處理您的自動化執行身分帳戶的驗證。

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. 按一下 [測試] 窗格，您便可測試 Runbook。
1. 按一下 [開始] 以開始測試。 完成後，您應該會看到類似下面的輸出，其中顯示您帳戶的基本資訊。 此動作會確認認證是否有效。

   ![驗證](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步驟 6 - 加入程式碼以啟動虛擬機器

既然您的 runbook 正在向 Azure 訂用帳戶進行驗證，您就可以管理資源。 讓我們新增命令來啟動虛擬機器。 您可以在 Azure 訂用帳戶中挑選任何 VM，而現在您會在 runbook 中硬式編碼該名稱。 如果您要管理跨多個訂用帳戶的資源，您必須使用*set-azcoNtext*參數搭配[set-azcoNtext](/powershell/module/az.accounts/get-azcontext) Cmdlet。

1. 輸入[update-azvm 指令程式](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
)的呼叫，以提供要啟動之 VM 的名稱和資源組名，如下所示。 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. 儲存 Runbook，然後按一下 [測試] 窗格，您便能加以測試。
1. 按一下 [開始] 以開始測試。 完成後，請檢查 VM 是否已啟動。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>步驟 7 - 將輸入參數加入至 Runbook

您的 runbook 目前會啟動您在 runbook 中硬式編碼的 VM。 如果您可以在 runbook 啟動時指定 VM，它會更有用。 讓我們將輸入參數新增至 runbook，以提供該功能。

1. 將*VMName*和*ResourceGroupName*的值新增至 runbook，並搭配使用相關聯的變數與**update-azvm** Cmdlet，如下所示。

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. 儲存 Runbook 並開啟 [測試] 窗格。 您現在可以提供測試中兩個輸入變數的值。
3. 關閉 [測試] 窗格。
4. 按一下 [發佈] 來發行新版本的 Runbook。
5. 停止您已啟動的 VM。
6. 按一下 [開始] 以啟動 Runbook。 
7. 針對您即將啟動的 VM，輸入**VMNAME**和**RESOURCEGROUPNAME**的值。

   ![啟動 Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. 當 runbook 完成時，請確認 VM 已啟動。

## <a name="next-steps"></a>後續步驟

* 如需 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[powershell](https://docs.microsoft.com/powershell/scripting/overview)檔。
* 若要開始使用圖形化 runbook，請參閱[我的第一個圖形化 runbook](automation-first-runbook-graphical.md)。
* 若要開始使用 PowerShell Runbook，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要深入瞭解 runbook 類型以及其優點和限制，請參閱[Azure 自動化 runbook 類型](automation-runbook-types.md)。
* 如需 PowerShell 腳本支援功能的詳細資訊，請參閱[Azure 自動化中的原生 PowerShell 腳本支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
