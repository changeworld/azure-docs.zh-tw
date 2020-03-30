---
title: 我在 Azure 自動化中的第一個 PowerShell Runbook
description: 教學課程將逐步引導您建立、測試和發佈簡單的 PowerShell Runbook。
keywords: azure powershell, powershell 指令碼教學課程, powershell 自動化
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367307"
---
# <a name="my-first-powershell-runbook"></a>我的第一個 PowerShell Runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [電源外殼](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 。 從可以測試和發佈的簡單 Runbook 開始，同時瞭解如何跟蹤 Runbook 作業的狀態。 然後修改 Runbook 以實際管理 Azure 資源，在這種情況下啟動 Azure 虛擬機器。 通過添加 Runbook 參數，完成本教程以使 Runbook 更加健壯。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-quickstart-create-account.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 由於您停止並啟動此電腦，因此它不應是生產 VM。
* 如有必要，請根據使用的 Cmdlet[導入 Azure 模組](shared-resources/modules.md)或[更新模組](automation-update-azure-modules.md)。

## <a name="differences-from-powershell-workflow-runbooks"></a>與 PowerShell 工作流運行簿的差異

PowerShell 運行簿與 PowerShell 工作流運行簿具有相同的生命週期、功能和管理。 但是，存在一些差異和限制。

| 特性  | 電源殼運行簿 | 電源外殼工作流運行簿 |
| ------ | ----- | ----- |
| 速度 | 運行速度快，因為它們不使用編譯步驟。 | 運行得更慢。 |
| 檢查點 | 不支援檢查點。 PowerShell 運行簿只能從一開始就恢復操作。 | 使用檢查點，允許活頁簿從任何點恢復操作。 |
| 命令執行 | 僅支援串列執行。 | 支援串列和並存執行。|
| Runspace | 單個運行空間運行腳本中的所有內容。 | 單獨的運行空間可用於活動、命令或腳本塊。 |

除了這些差異之外，PowerShell Runbook 與 PowerShell 工作流 Runbook 有一些[語法差異](https://technet.microsoft.com/magazine/dn151046.aspx)。

## <a name="step-1---create-runbook"></a>步驟 1 - 建立 Runbook

首先創建一個簡單的運行手冊，輸出文本`Hello World`。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 **"流程自動化**"下選擇**Runbook**以打開 Runbook 清單。
3. 通過選擇 **"創建運行簿"創建新的 Runbook。**
4. 將 Runbook 命名為「MyFirstRunbook-PowerShell」 ****。
5. 在這種情況下，您將創建一個[PowerShell 運行簿](automation-runbook-types.md#powershell-runbooks)。 為**Runbook 類型**選擇**PowerShell。**
6. 按一下 [建立] **** 來建立 Runbook 並開啟文字式編輯器。

## <a name="step-2---add-code-to-the-runbook"></a>步驟 2 - 將程式碼加入至 runbook

您可以直接將程式碼輸入到 runbook 中，或從程式庫控制項選取 cmdlet、runbook 和資產，並利用任何相關的參數將它們加入至 runbook。 在本教程中，您將直接在 Runbook 中鍵入代碼。

1. 您的 Runbook 當前為空。 鍵入`Write-Output "Hello World"`腳本正文。

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. 按一下 [儲存] **** 來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a>步驟 3 - 測試回合簿

在發佈 Runbook 使其在生產中可用之前，應對其進行測試以確保其正常工作。 測試 Runbook 會運行其草稿版本，並允許您以對話模式查看其輸出。

1. 按一下 [測試窗格] **** 來開啟 [測試] 窗格。
2. 按一下 [開始] **** 以開始測試。 這應該是唯一啟用的選項。
3. 請注意，將創建[Runbook 作業](automation-runbook-execution.md)，其狀態將顯示在窗格中。

   作業狀態以 開始`Queued`，指示作業正在等待雲中的 Runbook 工作人員變為可用。 狀態更改為`Starting`工作人員聲明作業時的狀態。 最後，當 Runbook 實際開始運行時，狀態變為`Running`該狀態。

4. 運行簿作業完成後，"測試"窗格將顯示其輸出。 在這種情況下，您將看到`Hello World`。

   ![測試窗格輸出](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您創建的 Runbook 仍處於草稿模式。 您需要將它發佈，才能在生產環境中執行它。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 按一下 [發佈]**** 來發佈 Runbook，然後出現提示時按一下 [是]****。
1. 向左滾動以查看 Runbook 頁上的 Runbook，並注意 **"創作狀態"** 值設置為 **"已發佈**"。
1. 捲動回右方以檢視 **MyFirstRunbook-PowerShell**的窗格。
   
   頂部的選項允許您立即啟動 Runbook、安排將來的開始時間或創建[Webhook，](automation-webhooks.md)以便通過 HTTP 調用啟動 Runbook。
1. 選取 [開始]****，然後在系統提示時選取 [是]****，以啟動 Runbook。 
1. 為已創建的 Runbook 作業打開作業窗格。 儘管可以關閉此窗格，但請立即將其保持打開狀態，以便可以監視作業的進度。 作業狀態顯示在**作業摘要**中，並且可能的狀態如測試 Runbook 所述。

   ![工作摘要](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Runbook 狀態顯示`Completed`後，按一下"**輸出"** 以打開"輸出"頁，您可以在`Hello World`其中顯示。

   ![作業輸出](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. 關閉 [輸出] 頁面。
1. 按一下 [所有記錄] **** 以開啟 Runbook 作業的 [資料流] 窗格。 您應該只在輸出`Hello World`流中看到。

    請注意，如果 Runbook 寫入 Runbook，則"流"窗格可以顯示 Runbook 作業的其他流，如"詳細"和"錯誤"流。

   ![所有記錄](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. 關閉"流"窗格和作業窗格以返回到 **"MyFirstRunbook-PowerShell"** 頁。
1. 在 **"詳細資訊**"下，按一下 **"作業**"以打開此 Runbook 的作業頁面。 此頁列出 Runbook 創建的所有作業。 您應該只看到列出的一個作業，因為您只運行該作業一次。

   ![作業清單](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. 按一下作業名稱可打開啟動 Runbook 時查看的相同作業窗格。 使用此窗格可以查看為 Runbook 創建的任何作業的詳細資訊。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步驟 5 - 加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。 為此，Runbook 必須能夠使用創建自動化帳戶時自動創建的"運行為"帳戶進行身份驗證。

如下例所示，"運行為"連接與[連接-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Cmdlet 進行。 如果要跨多個訂閱管理資源，則需要將`AzContext`參數與[Get-AzCoNtext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)一起使用。

> [!NOTE]
> 對於 PowerShell 運行`Add-AzAccount`簿`Add-AzureRMAccount`，並且是`Connect-AzAccount`的別名。 您可以使用這些 Cmdlet，也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)到最新版本。 即使您剛剛創建了新的自動化帳戶，您也可能需要更新模組。

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
1. 按一下 **"MyFirstRunbook-PowerShell"** 頁上**的"編輯"，** 打開文字編輯器。
1. 您不再需要這`Write-Output`條線了。 繼續刪除它。
1. 鍵入或複製並粘貼以下代碼，這些代碼處理具有"自動運行為帳戶"的身份驗證。

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

1. 按一下 **"測試"窗格**，以便可以測試 Runbook。
1. 按一下 [開始] **** 以開始測試。 完成後，您應該會看到類似于以下內容的輸出，顯示帳戶中的基本資訊。 此輸出會確認該執行身分帳戶有效。

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步驟 6 - 加入程式碼以啟動虛擬機器

由於您的 Runbook 正在驗證您的 Azure 訂用帳戶，所以您可以管理資源。 讓我們添加一個命令來啟動虛擬機器。 您可以選擇 Azure 訂閱中的任何虛擬機器，並且只需在 Runbook 中立即輸入名稱的硬編碼。

1. 添加到 runbook 腳本，添加[啟動 AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) Cmdlet 以啟動虛擬機器。 如下所示，Cmdlet 啟動具有名稱`VMName`且資源組名為 的`ResourceGroupName`虛擬機器。

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

1. 儲存 Runbook，然後按一下 [測試]**** 窗格，您便能加以測試。
1. 按一下 **"開始"** 以開始測試。 完成後，請確保虛擬機器已啟動。

## <a name="step-7---add-an-input-parameter"></a>步驟 7 - 添加輸入參數

Runbook 當前啟動在 Runbook 中硬式編碼虛擬機器。 如果在啟動 Runbook 時指定虛擬機器，則 Runbook 將更有用。 讓我們向 Runbook 添加輸入參數以提供該功能。

1. 在文字編輯器中`Start-AzVM`，修改 Cmdlet 以使用參數`VMName`和`ResourceGroupName`的變數。 

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
1. 按一下 [發佈] **** 來發行新版本的 Runbook。
1. 停止以前啟動的虛擬機器。
1. 按一下 [開始] **** 以啟動 Runbook。 
1. 鍵入要啟動的虛擬機器的**VMNAME**和**RESOURCEGROUPNAME**的值，然後按一下"**確定**"。<br><br> ![傳遞參數](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. 運行簿完成後，請確保虛擬機器已啟動。

## <a name="next-steps"></a>後續步驟

* 有關 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[PowerShell 文檔](/powershell/scripting/overview)。
* 要開始使用圖形運行簿，請參閱[我的第一個圖形運行簿](automation-first-runbook-graphical.md)。
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)。
* 要瞭解有關 Runbook 類型及其優點和限制的更多資訊，請參閱[Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 有關 PowerShell 腳本支援功能的詳細資訊，請參閱[Azure 自動化 中的本機 PowerShell 腳本支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
