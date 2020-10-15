---
title: 在 Azure 自動化中建立 PowerShell Runbook
description: 本文說明如何建立、測試及發佈簡單的 PowerShell Runbook。
keywords: azure powershell, powershell 指令碼教學課程, powershell 自動化
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: a1b0dff9421f493958554c659043c49ff2874379
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87014995"
---
# <a name="tutorial-create-a-powershell-runbook"></a>教學課程：建立 PowerShell Runbook

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks) 。 PowerShell Runbook 以 Windows PowerShell 為基礎。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。

> [!div class="checklist"]
> * 建立簡單的 PowerShell Runbook
> * 測試和發佈 Runbook
> * 執行和追蹤 Runbook 作業的狀態
> * 更新 Runbook 以使用 Runbook 參數啟動 Azure 虛擬機器

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../automation-quickstart-create-account.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 您會停止並啟動這部電腦，因此其不該是生產 VM。
* 如有必要，根據您所使用的 Cmdlet，[匯入 Azure 模組](../shared-resources/modules.md)或[更新模組](../automation-update-azure-modules.md)。

## <a name="differences-from-powershell-workflow-runbooks"></a>與 PowerShell 工作流程 Runbook 的差異

PowerShell Runbook 的生命週期、功能和管理與 PowerShell 工作流程 Runbook 相同。 但是有一些差異和限制。

| 特性  | PowerShell Runbook | PowerShell 工作流程 Runbook |
| ------ | ----- | ----- |
| 速度 | 快速執行，因為其不會使用編譯步驟。 | 執行速度緩慢。 |
| 檢查點 | 不支援檢查點。 PowerShell Runbook 只能從頭開始繼續作業。 | 使用檢查點，讓活頁簿可從任何時間點繼續作業。 |
| 命令執行 | 僅支援序列執行。 | 支援序列與平行執行。|
| Runspace | 單一 Runspace 會執行指令碼中的所有內容。 | 個別 Runspace 可用於活動、命令或指令碼區塊。 |

除了這些差異以外，PowerShell Runbook 與 PowerShell 工作流程 Runbook 也有一些[語法差異](/previous-versions/technet-magazine/dn151046(v=msdn.10))。

## <a name="step-1---create-runbook"></a>步驟 1 - 建立 Runbook

由建立一個可輸出文字 `Hello World` 的簡單 Runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。

2. 選取 [程序自動化] 底下的 [Runbook]，以開啟 Runbook 清單。

3. 藉由選取 [建立 Runbook] 來建立新的 Runbook。

4. 將 Runbook 命名為「MyFirstRunbook-PowerShell」 。

5. 在此情況下，您即將建立 [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks)。 針對 [Runbook 類型]，選取 [PowerShell]。

6. 按一下 [建立]  來建立 Runbook 並開啟文字式編輯器。

## <a name="step-2---add-code-to-the-runbook"></a>步驟 2 - 將程式碼加入至 runbook

您可以直接將程式碼輸入到 runbook 中，或從程式庫控制項選取 cmdlet、runbook 和資產，並利用任何相關的參數將它們加入至 runbook。 在本教學課程中，您將直接在 Runbook 中輸入程式碼。

1. 您的 Runbook 目前是空的。 在指令碼本完中輸入 `Write-Output "Hello World"`。

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. 按一下 [儲存] 來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> 步驟 3 - 測試 Runbook

在您發佈 Runbook 之前，為了使其可用於生產環境，您應該進行測試以確定其可正常運作。 測試 Runbook 時，系統會執行其「草稿」版本並可讓您以互動方式檢視其輸出。

1. 按一下 [測試窗格]  來開啟 [測試] 窗格。

2. 按一下 [開始]  以開始測試。 這應該是唯一啟用的選項。

3. 請注意，系統會建立 [Runbook 作業](../automation-runbook-execution.md)並在窗格中顯示其狀態。

   作業狀態一開始為 [已排入佇列]，表示該作業正在等候雲端中的 Runbook 背景工作可供使用。 當背景工作腳色宣告該作業時，狀態會變成 [啟動中]。 最後，當 Runbook 實際開始執行時，狀態會變成 [執行中]。

4. 當 Runbook 作業完成時，測試窗格會顯示其輸出。 在此情況下，您可看見 `Hello World`。

   ![測試窗格輸出](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您建立的 Runbook 仍處於草稿模式。 您需要將它發佈，才能在生產環境中執行它。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 按一下 [發佈] 來發佈 Runbook，然後出現提示時按一下 [是]。

2. 向左捲動以在 [Runbook] 頁面上流檢視 Runbook，並請注意 [撰寫狀態] 值會設為 [已發佈]。

3. 捲動回右方以檢視 **MyFirstRunbook-PowerShell** 的頁面。
   
   頂端的選項可讓您立即啟動 Runbook、排程在未來啟動時間，或建立 [Webhook](../automation-webhooks.md)，以便透過 HTTP 呼叫啟動該 Runbook。

4. 選取 [開始]，然後在系統提示時選取 [是]，以啟動 Runbook。 

5. [作業] 窗格會針對已建立的 Runbook 作業開啟。 雖然您可關閉此窗格，但立即將其保持開啟狀態，以便觀看作業的進度。 作業狀態會顯示在 [作業摘要] 中，而且可能的狀態會如測試 Runbook 所述。

   ![工作摘要](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. 一旦 Runbook 狀態顯示 [已完成]，請按一下 [輸出] 以開啟 [輸出] 頁面，您可在其中看到 `Hello World` 顯示出來。

   ![作業輸出](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. 關閉 [輸出] 頁面。

8. 按一下 [所有記錄]  以開啟 Runbook 作業的 [資料流] 窗格。 您應該只會在輸出資料流中看到 `Hello World`。

    請注意，[資料流] 窗格可顯示 Runbook 作業的其他資料流，例如 Runbook 寫入時出現的 [詳細資訊] 和 [錯誤] 資料流。

   ![所有記錄](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook-PowerShell 頁面。

10. 按一下 [詳細資料] 底下的 [作業]，以開啟此 Runbook 的 [作業] 頁面。 此頁面會列出 Runbook 建立的所有作業。 由於您只執行一次作業，所以應該只會看到一項作業列出。

   ![作業清單](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. 按一下作業名稱，以開啟您啟動 Runbook 時所檢視的相同 [作業] 窗格。 使用此窗格來檢視針對 Runbook 建立的任何作業詳細資料。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步驟 5 - 加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。 若要這麼做，Runbook 必須能夠使用您在建立自動化帳戶時自動建立的執行身分帳戶進行驗證。

如以下範例所示，系統會使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Cmdlet 進行執行身分連線。 如果您要管理跨多個訂用帳戶的資源，您需要使用 `AzContext` 參數搭配 [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)。

> [!NOTE]
> 針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](../automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. 按一下 MyFirstRunbook-PowerShell 分頁上的 [編輯] 來開啟文字式編輯器。

2. 您不再需要 `Write-Output` 行。 只要繼續並予以刪除。

3. 輸入或是複製並貼上下列程式碼，此程式碼會處理自動化執行身分帳戶的驗證。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. 按一下 [測試] 窗格，您便可測試 Runbook。

5. 按一下 [開始]  以開始測試。 當測試完成後，您應該會從帳戶收到如同以下顯示基本資訊的輸出。 此輸出會確認該執行身分帳戶有效。

   ![Authenticate](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步驟 6 - 加入程式碼以啟動虛擬機器

由於您的 Runbook 正在驗證您的 Azure 訂用帳戶，所以您可以管理資源。 讓我們新增一個命令以啟動虛擬機器。 您可以在您的 Azure 訂用帳戶中挑選任何虛擬機器，而現在只要在 Runbook 中將該名稱硬式編碼。

1. 在您的 Runbook 指令碼中，新增 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) Cmdlet 以啟動虛擬機器。 如下所示，Cmdlet 會啟動名稱為 `VMName` 且具有 `ResourceGroupName` 資源群組的虛擬機器。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. 儲存 Runbook，然後按一下 [測試] 窗格，您便能加以測試。

3. 按一下 [開始] 以開始測試。 當測試完成後，請確定虛擬機器已啟動。

## <a name="step-7---add-an-input-parameter"></a>步驟 7 - 新增輸入參數

您的 Runbook 目前會啟動您在 Runbook 中硬式編碼的虛擬機器。 如果您在啟動 Runbook 時指定虛擬機器，Runbook 會更有用。 讓我們將輸入參數新增至 Runbook，以提供該功能。

1. 在文字式編輯器中，修改 `Start-AzVM` Cmdlet，以使用 `VMName` 和 `ResourceGroupName` 參數的變數。 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. 儲存 Runbook 並開啟 [測試] 窗格。 您現在可以提供測試中使用的兩個輸入變數的值。

3. 關閉 [測試] 窗格。

4. 按一下 [發佈]  來發行新版本的 Runbook。

5. 停止您先前啟動的虛擬機器。

6. 按一下 [開始]  以啟動 Runbook。 

7. 針對您即將啟動的虛擬機器，輸入 [VMNAME] 和 [RESOURCEGROUPNAME] 值，然後按一下 [確定]。

    ![傳遞參數](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. 當 Runbook 完成時，請確定虛擬機器已啟動。

## <a name="next-steps"></a>後續步驟

* 如需 PowerShell 的詳細資訊 (包括語言參考和學習模組)，請參閱 [PowerShell 文件](/powershell/scripting/overview)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
* 若要開始使用圖形化 Runbook，請參閱[建立圖形化 Runbook](automation-tutorial-runbook-graphical.md)。
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱[建立 PowerShell 工作流程 Runbook](automation-tutorial-runbook-textual.md)。
* 若要深入了解 Runbook 類型及其優點和限制，請參閱 [Azure 自動化 Runbook 類型](../automation-runbook-types.md)。
* 如需 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
