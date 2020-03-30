---
title: 我在 Azure 自動化中的第一個 PowerShell 工作流程 Runbook
description: 本教學課程逐步引導您使用 PowerShell 工作流程建立、測試和發佈簡單的文字 Runbook。
keywords: powershell 工作流程, powershell 工作流程範例, 工作流程 powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367256"
---
# <a name="my-first-powershell-workflow-runbook"></a>我的第一個 PowerShell 工作流程 Runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [電源外殼](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 從您測試和發佈的簡單 Runbook 開始，同時瞭解如何跟蹤 Runbook 作業的狀態。 然後修改 Runbook 以實際管理 Azure 資源，通過啟動 Azure 虛擬機器進行說明。 最後，通過添加 Runbook 參數使 Runbook 更加健壯。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 由於您停止並啟動此電腦，因此它不應是生產 VM。

## <a name="step-1---create-new-runbook"></a>步驟 1 - 建立新的 Runbook

首先創建一個簡單的運行手冊，輸出文本`Hello World`。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。

   [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 這些資產大多數是自動包含在新自動化帳戶中的模組。 您還應將憑據資產與您的訂閱相關聯。
 
1. 在 **"流程自動化**"下選擇**Runbook**以打開 Runbook 清單。
1. 通過選擇 **"創建運行簿"創建新的 Runbook。**
1. 將 Runbook 命名為「MyFirstRunbook-Workflow」 ****。
1. 在這種情況下，您將創建一個[PowerShell 工作流運行簿](automation-runbook-types.md#powershell-workflow-runbooks)。 為**Runbook 類型**選擇**PowerShell 工作流**。
1. 按一下 [建立] **** 來建立 Runbook 並開啟文字式編輯器。

## <a name="step-2---add-code-to-the-runbook"></a>步驟 2 - 將程式碼加入至 runbook

您可以直接在 Runbook 中鍵入代碼，也可以從庫控制項中選擇 Cmdlet、Runbook 和資產，並將它們添加到具有任何相關參數的 Runbook 中。 在本教程中，您將直接在 Runbook 中鍵入代碼。

1. Runbook 當前為空，只有必需`Workflow`的關鍵字、Runbook 的名稱以及包含整個工作流的大括弧。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. 在`Write-Output "Hello World"`大括弧之間鍵入。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. 按一下 [儲存] **** 來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a>步驟 3 - 測試 Runbook

在發佈 Runbook 使其在生產中可用之前，應對其進行測試以確保其正常工作。 測試 Runbook 會運行其草稿版本，並允許您以對話模式查看其輸出。

1. 選擇 **"測試"窗格**以打開"測試"窗格。
1. 按一下"**開始"** 以啟動測試，並測試唯一啟用的選項。
1. 請注意，將創建[Runbook 作業](automation-runbook-execution.md)，其狀態將顯示在窗格中。

   作業狀態以 開始`Queued`，指示作業正在等待雲中的 Runbook 工作人員變為可用。 狀態更改為`Starting`工作人員聲明作業時的狀態。 最後，當 Runbook 實際開始運行時，狀態變為`Running`該狀態。

1. 運行簿作業完成後，"測試"窗格將顯示其輸出。 在這種情況下，您將看到`Hello World`。

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您創建的 Runbook 仍處於草稿模式。 必須先發佈它，然後才能在生產中運行它。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 按一下 [發佈]**** 來發佈 Runbook，然後出現提示時按一下 [是]****。
1. 向左滾動以查看 Runbook 頁中的**Runbook，** 並注意"**創作狀態"** 欄位設置為 **"已發佈**"。
1. 向後滾動到右側以查看**MyFirstRunbook-工作流的**頁面。

   頂部的選項允許您立即啟動 Runbook、安排將來的開始時間或創建[Webhook，](automation-webhooks.md)以便通過 HTTP 調用啟動 Runbook。

1. 選取 [開始]****，然後在系統提示時選取 [是]****，以啟動 Runbook。

   ![啟動 Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. 為已創建的 Runbook 作業打開作業窗格。 在這種情況下，請保持窗格打開狀態，以便可以監視作業的進度。

1. 請注意，作業狀態顯示在**作業摘要**中。 此狀態與您在測試 Runbook 時看到的狀態匹配。

   ![工作摘要](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. 運行簿狀態顯示`Completed`後，按一下"**輸出**"。 將打開"輸出"頁，您可以在其中查看`Hello World`消息。

   ![工作摘要](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. 關閉 [輸出] 頁面。

1. 按一下 [所有記錄] **** 以開啟 Runbook 作業的 [資料流] 窗格。 您應該只在輸出`Hello World`流中看到。 請注意，如果 Runbook 寫入 runbook，則"流"窗格可以顯示 Runbook 作業的其他流，例如詳細和錯誤流。

   ![工作摘要](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. 關閉"流"窗格和"作業"窗格以返回到 **"MyFirstRunbook"** 頁。
1. 按一下 **"資源**下的**作業**"以打開此 Runbook 的作業頁面。 此頁列出 Runbook 創建的所有作業。 您應該只看到列出的一個作業，因為您只運行該作業一次。

   ![工作](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. 按一下作業名稱可打開啟動 Runbook 時查看的相同作業窗格。 使用此窗格可以查看為 Runbook 創建的任何作業的詳細資訊。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>步驟 5 - 加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。 除非使用訂閱的憑據進行身份驗證，否則它無法執行此操作。 身份驗證使用`Connect-AzAccount`Cmdlet。

>[!NOTE]
>對於 PowerShell 運行`Add-AzAccount`簿`Add-AzureRMAccount`，並且是`Connect-AzAccount`的別名。 您可以使用這些 Cmdlet，也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)到最新版本。 即使您剛剛創建了新的自動化帳戶，您也可能需要更新模組。

1. 導航到**MyFirstRunbook-工作流**頁面，然後按一下 **"編輯**"打開文字編輯器。
2. 刪除行`Write-Output`。
3. 將游標放在大括弧之間的空白行。
4. 鍵入或複製並粘貼以下代碼，這些代碼處理具有"自動運行為帳戶"的身份驗證。

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. 按一下 **"測試"窗格**，以便可以測試 Runbook。
1. 按一下 [開始] **** 以開始測試。 完成後，您應該會看到類似于以下內容的輸出，顯示帳戶中的基本資訊。 此操作確認憑據有效。

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>步驟 6 - 加入程式碼以啟動虛擬機器

現在 Runbook 正在對 Azure 訂閱進行身份驗證，可以管理資源。 讓我們添加一個命令來啟動虛擬機器。 您可以在 Azure 訂閱中選擇任何 VM，現在您將在 Runbook 中硬編碼該名稱。 如果要跨多個訂閱管理資源，則需要將`AzContext`參數與[Get-AzCoNtext](/powershell/module/az.accounts/get-azcontext) Cmdlet 一起使用。

1. 提供 VM 的名稱和資源組名稱，以便首先輸入對[啟動-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) Cmdlet 的調用，如下所示。 

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

1. 儲存 Runbook，然後按一下 [測試]**** 窗格，您便能加以測試。
1. 按一下 [開始] **** 以開始測試。 完成後，檢查 VM 已啟動。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>步驟 7 - 將輸入參數加入至 Runbook

Runbook 當前啟動在 Runbook 中硬式編碼 VM。 如果可以在啟動 Runbook 時指定 VM，則該 VM 將更有用。 讓我們向 Runbook 添加輸入參數以提供該功能。

1. 將`VMName`和`ResourceGroupName`參數的變數添加到 Runbook，並將變數與`Start-AzVM`Cmdlet 一起使用，如下所示。

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
4. 按一下 [發佈] **** 來發行新版本的 Runbook。
5. 停止已啟動的 VM。
6. 按一下 [開始] **** 以啟動 Runbook。 
7. 鍵入要啟動的 VM 的**VMNAME**和**RESOURCEGROUPNAME**的值。

   ![啟動 Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. 運行簿完成後，驗證 VM 是否已啟動。

## <a name="next-steps"></a>後續步驟

* 有關 PowerShell 的更多資訊（包括語言參考和學習模組），請參閱[PowerShell 文檔](https://docs.microsoft.com/powershell/scripting/overview)。
* 要開始使用圖形運行簿，請參閱[我的第一個圖形運行簿](automation-first-runbook-graphical.md)。
* 要開始使用 PowerShell 運行簿，請參閱[我的第一個 PowerShell 運行簿](automation-first-runbook-textual-powershell.md)。
* 要瞭解有關 Runbook 類型及其優點和限制的更多資訊，請參閱[Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 有關 PowerShell 腳本支援功能的詳細資訊，請參閱[Azure 自動化 中的本機 PowerShell 腳本支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
