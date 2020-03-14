---
title: 我在 Azure 自動化中的第一個 PowerShell Runbook
description: 教學課程將逐步引導您建立、測試和發佈簡單的 PowerShell Runbook。
keywords: azure powershell, powershell 指令碼教學課程, powershell 自動化
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367307"
---
# <a name="my-first-powershell-runbook"></a>我的第一個 PowerShell Runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 。 從您可以測試和發佈的簡單 runbook 開始，同時瞭解如何追蹤 runbook 作業的狀態。 然後修改 runbook 以實際管理 Azure 資源，在此案例中是啟動 Azure 虛擬機器。 完成本教學課程，藉由新增 runbook 參數，讓 runbook 更穩固。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-quickstart-create-account.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 由於您會停止並啟動這部電腦，因此它不應該是生產 VM。
* 如有需要，請根據您使用的 Cmdlet 匯[入 Azure 模組](shared-resources/modules.md)或[更新模組](automation-update-azure-modules.md)。

## <a name="differences-from-powershell-workflow-runbooks"></a>PowerShell 工作流程 runbook 的差異

PowerShell runbook 的生命週期、功能和管理與 PowerShell 工作流程 runbook 相同。 不過，有一些差異和限制。

| 特性  | PowerShell Runbook | PowerShell 工作流程 Runbook |
| ------ | ----- | ----- |
| 速度 | 快速執行，因為它們不會使用編譯步驟。 | 執行速度變慢。 |
| 檢查點 | 不支援檢查點。 PowerShell runbook 只能從一開始就繼續操作。 | 使用檢查點，讓活頁簿可以從任何時間點繼續操作。 |
| 命令執行 | 僅支援序列執行。 | 同時支援序列和平行執行。|
| Runspace | 單一的運行空間會執行腳本中的所有專案。 | 個別的運行時可以用於活動、命令或腳本區塊。 |

除了這些差異之外，PowerShell runbook 與 PowerShell 工作流程 runbook 有一些[語法差異](https://technet.microsoft.com/magazine/dn151046.aspx)。

## <a name="step-1---create-runbook"></a>步驟 1 - 建立 Runbook

從建立可輸出文字 `Hello World`的簡單 runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 選取 [程式**自動化**] 底下的 [ **runbook** ] 以開啟 runbook 清單。
3. 選取 [**建立 runbook**] 來建立新的 runbook。
4. 將 Runbook 命名為「MyFirstRunbook-PowerShell」。
5. 在此情況下，您將建立[PowerShell runbook](automation-runbook-types.md#powershell-runbooks)。 針對 [ **Runbook 類型**] 選取 [ **PowerShell** ]。
6. 按一下 [建立] 來建立 Runbook 並開啟文字式編輯器。

## <a name="step-2---add-code-to-the-runbook"></a>步驟 2 - 將程式碼加入至 runbook

您可以直接將程式碼輸入到 runbook 中，或從程式庫控制項選取 cmdlet、runbook 和資產，並利用任何相關的參數將它們加入至 runbook。 在本教學課程中，您將直接在 runbook 中輸入程式碼。

1. 您的 runbook 目前是空的。 在腳本主體中輸入 `Write-Output "Hello World"`。

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. 按一下 [儲存]來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a>步驟 3-測試 runbook

在您發佈 runbook 以讓它可在生產環境中使用之前，您應該先進行測試，以確定它能正常運作。 測試 runbook 會執行其草稿版本，並可讓您以互動方式來查看其輸出。

1. 按一下 [測試窗格] 來開啟 [測試] 窗格。
2. 按一下 [開始] 以開始測試。 這應該是唯一啟用的選項。
3. 請注意，會建立[runbook 工作](automation-runbook-execution.md)，並在窗格中顯示其狀態。

   作業狀態會從 `Queued`開始，表示作業正在等候雲端中的 runbook 背景工作可供使用。 當背景工作宣告作業時，狀態會變更為 `Starting`。 最後，當 runbook 實際開始執行時，狀態會變成 `Running`。

4. 當 runbook 作業完成時，[測試] 窗格會顯示其輸出。 在此情況下，您會看到 `Hello World`。

   ![測試窗格輸出](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您建立的 runbook 仍處於草稿模式。 您需要將它發佈，才能在生產環境中執行它。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 按一下 [發佈] 來發佈 Runbook，然後出現提示時按一下 [是]。
1. 向左滾動以在 [Runbook] 頁面上查看 runbook，並注意 [**撰寫狀態**] 值設定為 [**已發佈**]。
1. 捲動回右方以檢視 **MyFirstRunbook-PowerShell**的窗格。
   
   在頂端的選項可讓您立即啟動 runbook、排程未來的開始時間，或建立[webhook](automation-webhooks.md) ，以便透過 HTTP 呼叫來啟動 runbook。
1. 選取 [開始]，然後在系統提示時選取 [是]，以啟動 Runbook。 
1. 已建立的 runbook 作業會開啟 [作業] 窗格。 雖然您可以關閉此窗格，但請將它保持開啟，讓您可以觀看作業的進度。 作業狀態會顯示在 [**作業摘要**] 中，而且可能的狀態會如測試 runbook 所述。

   ![工作摘要](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. 一旦 runbook 狀態顯示 `Completed`，請按一下 [**輸出**] 以開啟 [輸出] 頁面，您可以在其中看到 `Hello World` 顯示。

   ![作業輸出](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. 關閉 [輸出] 頁面。
1. 按一下 [所有記錄] 以開啟 Runbook 作業的 [資料流] 窗格。 您應該只會在輸出資料流程中看到 `Hello World`。

    請注意，[資料流程] 窗格可以顯示 runbook 作業的其他資料流程，例如 [詳細資訊] 和 [錯誤資料流程] （如果 runbook 寫入它們）。

   ![所有記錄](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. 關閉 [資料流程] 窗格和 [作業] 窗格，以返回 [ **MyFirstRunbook-PowerShell** ] 頁面。
1. 在 [**詳細資料**] 底下，按一下 [**作業**] 以開啟此 runbook 的 [作業] 頁面。 此頁面會列出您的 runbook 所建立的所有作業。 您應該只會看到列出一項作業，因為您只會執行一次作業。

   ![作業清單](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. 按一下作業名稱，開啟您在啟動 runbook 時所看到的相同作業窗格。 使用此窗格來查看針對 runbook 所建立之任何作業的詳細資料。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步驟 5 - 加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。 若要這樣做，runbook 必須能夠使用您在建立自動化帳戶時自動建立的執行身分帳戶進行驗證。

如下列範例所示，會使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Cmdlet 來建立執行身分連線。 如果您要管理跨多個訂用帳戶的資源，您必須使用 `AzContext` 參數搭配[set-azcoNtext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)。

> [!NOTE]
> 針對 PowerShell runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount`的別名。 您可以使用這些 Cmdlet，也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)為最新版本。 即使您剛建立新的自動化帳戶，也可能需要更新您的模組。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. 按一下 [ **MyFirstRunbook-PowerShell** ] 頁面上的 [**編輯**] 來開啟文字式編輯器。
1. 您不再需要 `Write-Output` 行。 只要繼續並刪除它。
1. 輸入或複製並貼上下列程式碼，以處理您的自動化執行身分帳戶的驗證。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. 按一下 [測試] 窗格，您便可測試 Runbook。
1. 按一下 [開始] 以開始測試。 完成後，您應該會看到類似下面的輸出，其中顯示您帳戶的基本資訊。 此輸出會確認該執行身分帳戶有效。

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步驟 6 - 加入程式碼以啟動虛擬機器

由於您的 Runbook 正在驗證您的 Azure 訂用帳戶，所以您可以管理資源。 讓我們新增命令來啟動虛擬機器。 您可以在 Azure 訂用帳戶中挑選任何虛擬機器，並立即在 runbook 中將該名稱硬式編碼。

1. 在您的 runbook 腳本中，新增[update-azvm 指令程式](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0)來啟動虛擬機器。 如下所示，此 Cmdlet 會啟動名稱為 `VMName` 的虛擬機器，以及名為 `ResourceGroupName`的資源群組。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. 儲存 Runbook，然後按一下 [測試] 窗格，您便能加以測試。
1. 按一下 [**啟動**] 開始測試。 完成後，請確定虛擬機器已啟動。

## <a name="step-7---add-an-input-parameter"></a>步驟 7-新增輸入參數

您的 runbook 目前會啟動您在 runbook 中硬式編碼的虛擬機器。 如果您在啟動 runbook 時指定虛擬機器，runbook 會更有用。 讓我們將輸入參數新增至 runbook，以提供該功能。

1. 在文字式編輯器中，修改 `Start-AzVM` Cmdlet，以將變數用於 `VMName` 和 `ResourceGroupName`參數。 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. 儲存 Runbook 並開啟 [測試] 窗格。 您現在可以提供測試中使用的兩個輸入變數的值。
1. 關閉 [測試] 窗格。
1. 按一下 [發佈] 來發行新版本的 Runbook。
1. 停止您先前啟動的虛擬機器。
1. 按一下 [開始] 以啟動 Runbook。 
1. 針對您即將啟動的虛擬機器，輸入**VMNAME**和**RESOURCEGROUPNAME**的值，然後按一下 **[確定]** 。<br><br> ![傳遞參數](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. 當 runbook 完成時，請確定虛擬機器已啟動。

## <a name="next-steps"></a>後續步驟

* 如需 PowerShell 的詳細資訊，包括語言參考和學習模組，請參閱[powershell](/powershell/scripting/overview)檔。
* 若要開始使用圖形化 runbook，請參閱[我的第一個圖形化 runbook](automation-first-runbook-graphical.md)。
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)。
* 若要深入瞭解 runbook 類型以及其優點和限制，請參閱[Azure 自動化 runbook 類型](automation-runbook-types.md)。
* 如需 PowerShell 腳本支援功能的詳細資訊，請參閱[Azure 自動化中的原生 PowerShell 腳本支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
