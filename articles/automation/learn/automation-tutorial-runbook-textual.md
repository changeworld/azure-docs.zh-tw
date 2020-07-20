---
title: 在 Azure 自動化中建立 PowerShell 工作流程 Runbook
description: 本文說明如何建立、測試及發佈簡單的 PowerShell 工作流程 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: dd9aee01edf45f89feb6a6010c8d958511bc3904
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185393"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>教學課程：建立 PowerShell 工作流程 Runbook

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell 工作流程 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)。 PowerShell Workflow Runbook 是以 Windows PowerShell 工作流程為基礎的文字 Runbook。 您可以使用 Azure 入口網站的文字編輯器來建立和編輯 Runbook 的程式碼。 

> [!div class="checklist"]
> * 建立簡單的 PowerShell 工作流程 Runbook
> * 測試和發佈 Runbook
> * 執行和追蹤 Runbook 作業的狀態
> * 更新 Runbook 以使用 Runbook 參數啟動 Azure 虛擬機器

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../index.yml) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 您會停止並啟動這部電腦，因此其不該是生產 VM。

## <a name="step-1---create-new-runbook"></a>步驟 1 - 建立新的 Runbook

由建立一個可輸出文字 `Hello World` 的簡單 Runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。

   [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 其中大部分資產是自動包含在新自動化帳戶的模組。 您也應該擁有與您的訂用帳戶相關聯的認證資產。
 
2. 選取 [程序自動化] 底下的 [Runbook]，以開啟 Runbook 清單。

3. 藉由選取 [建立 Runbook] 來建立新的 Runbook。

4. 將 Runbook 命名為「MyFirstRunbook-Workflow」 。

5. 在此情況下，您即將建立 [PowerShell 工作流程 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)。 針對 [Runbook 類型]，選取 [PowerShell 工作流程]。

6. 按一下 [建立]  來建立 Runbook 並開啟文字式編輯器。

## <a name="step-2---add-code-to-the-runbook"></a>步驟 2 - 將程式碼加入至 runbook

您可以直接將程式碼輸入到 Runbook 中，或從程式庫控制項選取 Cmdlet、Runbook 和資產，並利用任何相關的參數將其新增至 Runbook。 在本教學課程中，您會直接在 Runbook 中輸入程式碼。

1. 您的 Runbook 目前是空白的，只有必要的 `Workflow` 關鍵字、Runbook 名稱以及括住整個工作流程的大括弧。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. 在大括號內輸入 `Write-Output "Hello World"`。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. 按一下 [儲存] 來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a>步驟 3 - 測試 Runbook

在您發佈 Runbook 之前，為了使其可用於生產環境，您應該進行測試以確定其可正常運作。 測試 Runbook 時，系統會執行其「草稿」版本並可讓您以互動方式檢視其輸出。

1. 選取 [測試窗格] 來開啟 [測試] 窗格。

2. 按一下 [開始] 以開始測試，並測試唯一啟用的選項。

3. 請注意，系統會建立 [Runbook 作業](../automation-runbook-execution.md)並在窗格中顯示其狀態。

   作業狀態一開始為 [已排入佇列]，表示該作業正在等候雲端中的 Runbook 背景工作可供使用。 當背景工作腳色宣告該作業時，狀態會變成 [啟動中]。 最後，當 Runbook 實際開始執行時，狀態會變成 [執行中]。

4. 當 Runbook 作業完成時，測試窗格會顯示其輸出。 在此情況下，您可看見 `Hello World`。

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您所建立的 Runbook 仍處於草稿模式。 您必須先將其發佈，才能在生產環境中加以執行。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 按一下 [發佈] 來發佈 Runbook，然後出現提示時按一下 [是]。

2. 向左捲動以在 [Runbook] 頁面上流檢視 Runbook，並請注意 [撰寫狀態] 值會設為 [已發佈]。

3. 捲動回到右側，檢視 **MyFirstRunbook-Workflow** 的頁面。

   頂端的選項可讓您立即啟動 Runbook、排程在未來啟動時間，或建立 [Webhook](../automation-webhooks.md)，以便透過 HTTP 呼叫啟動該 Runbook。

4. 選取 [開始]，然後在系統提示時選取 [是]，以啟動 Runbook。

   ![啟動 Runbook](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. [作業] 窗格會針對已建立的 Runbook 作業開啟。 在此情況下，讓窗格保持開啟狀態，以便觀看作業的進度。

6. 請注意，作業狀態會顯示在 [作業摘要] 中。 此狀態會符合您在測試 Runbook 時看到的狀態。

   ![工作摘要](../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png)

7. 一旦 Runbook 狀態顯示 [已完成]，請按一下 [輸出]。 [輸出] 頁面隨即開啟，您可在其中看到 `Hello World` 訊息。

   ![工作摘要](../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png)

8. 關閉 [輸出] 頁面。

9. 按一下 [所有記錄]  以開啟 Runbook 作業的 [資料流] 窗格。 您應該只會在輸出資料流中看到 `Hello World`。 請注意，[資料流] 窗格可顯示 Runbook 作業的其他資料流，例如 Runbook 寫入時出現的詳細資料和錯誤資料流。

   ![工作摘要](../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png)

10. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook 頁面。

11. 按一下 [資源] 底下的 [作業]，以開啟此 Runbook 的 [作業] 頁面。 此頁面會列出 Runbook 建立的所有作業。 由於您只執行一次作業，所以應該只會看到一項作業列出。

   ![工作](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. 按一下作業名稱，以開啟您啟動 Runbook 時所檢視的相同 [作業] 窗格。 使用此窗格來檢視針對 Runbook 建立的任何作業詳細資料。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步驟 5 - 加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。 除非使用訂用帳戶的認證進行驗證，否則無法這麼做。 驗證會使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)Cmdlet。

>[!NOTE]
>針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](../automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。

1. 瀏覽至 MyFirstRunbook-Workflow 頁面，然後按一下 [編輯] 以開啟文字編輯器。

2. 刪除 `Write-Output` 行。

3. 將游標放在大括弧之間的空白行。

4. 輸入或是複製並貼上下列程式碼，此程式碼會處理自動化執行身分帳戶的驗證。

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. 按一下 [測試] 窗格，您便可測試 Runbook。

6. 按一下 [開始]  以開始測試。 當測試完成後，您應該會從帳戶收到如同以下顯示基本資訊的輸出。 此動作可確認認證有效。

   ![Authenticate](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步驟 6 - 加入程式碼以啟動虛擬機器

由於您的 Runbook 正在向 Azure 訂用帳戶進行驗證，所以您可以管理資源。 讓我們新增一個命令以啟動虛擬機器。 您可以在您的 Azure 訂用帳戶中挑選任何 VM，而現在您會在 Runbook 中將該名稱硬式編碼。 如果您要管理跨多個訂用帳戶的資源，您需要使用 `AzContext` 參數搭配 [Get-AzContext](/powershell/module/az.accounts/get-azcontext) Cmdlet。

1. 輸入對 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) Cmdlet 的呼叫，以提供要啟動 VM 的名稱和資源組名，如下所示。 

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

2. 儲存 Runbook，然後按一下 [測試] 窗格，您便能加以測試。

3. 按一下 [開始]  以開始測試。 當測試完成後，請確認 VM 已啟動。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>步驟 7 - 將輸入參數加入至 Runbook

您的 Runbook 目前會啟動您在 Runbook 中硬式編碼的 VM。 如果您可以在 Runbook 啟動時指定 VM，其會更加實用。 讓我們將輸入參數新增至 Runbook，以提供該功能。

1. 將 `VMName` 和 `ResourceGroupName` 參數的變數新增至 Runbook，並使用變數搭配 `Start-AzVM` Cmdlet，如下所示。

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

4. 按一下 [發佈]  來發行新版本的 Runbook。

5. 停止您已啟動的 VM。

6. 按一下 [開始]  以啟動 Runbook。 

7. 針對您即將啟動的 VM，輸入 [VMNAME] 和 [RESOURCEGROUPNAME] 的值。

   ![啟動 Runbook](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. 當 Runbook 完成時，請確認 VM 已啟動。

## <a name="next-steps"></a>後續步驟

* 如需 PowerShell 的詳細資訊 (包括語言參考和學習模組)，請參閱 [PowerShell 文件](/powershell/scripting/overview)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
* 若要開始使用圖形化 Runbook，請參閱[建立圖形化 Runbook](automation-tutorial-runbook-graphical.md)。
* 若要開始使用 PowerShell Runbook，請參閱[建立 PowerShell Runbook](automation-tutorial-runbook-textual-powershell.md)。
* 若要深入了解 Runbook 類型以及其優點和限制，請參閱 [Azure 自動化 Runbook 類型](../automation-runbook-types.md)。
* 如需 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
