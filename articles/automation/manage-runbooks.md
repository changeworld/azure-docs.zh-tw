---
title: 管理 Azure 自動化中的 Runbook
description: 本文描述如何管理 Azure 自動化中的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad8c05b3347ed4741d574a5e6bcc1d928db08411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366831"
---
# <a name="manage-runbooks-in-azure-automation"></a>管理 Azure 自動化中的 Runbook

您可以通過[創建新](#create-a-runbook)Runbook 或從檔或[Runbook 庫](automation-runbook-gallery.md)[導入現有](#import-a-runbook)Runbook 來將 Runbook 添加到 Azure 自動化。 本文提供從檔案建立和匯入 Runbook 的資訊。 您可以在[Runbook 和 Azure 自動化的模組庫中](automation-runbook-gallery.md)獲取訪問社區 Runbook 和模組的所有詳細資訊。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="create-a-runbook"></a>建立 Runbook

您可以使用其中一個 Azure 入口網站或 Windows PowerShell，在 Azure 自動化中建立新的 Runbook。 一旦建立 Runbook 之後，您就能使用[了解 PowerShell 工作流程](automation-powershell-workflow.md)和 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)中的資訊來編輯它。

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中建立 Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從中心，在 **"進程自動化**"下選擇**Runbook**以打開 Runbook 清單。
3. 按一下 **"創建運行簿**"。
4. 輸入 Runbook 的名稱並選擇其[類型](automation-runbook-types.md)。 Runbook 名稱必須以字母開頭，並可以包含字母、數位、底線和破折號。
5. 按一下 [建立] **** 來建立 Runbook 並開啟編輯器。

### <a name="create-a-runbook-with-powershell"></a>使用 PowerShell 建立 Runbook

您可以使用[新阿茲自動化 Runbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) Cmdlet 創建一個空[的 PowerShell 工作流運行簿](automation-runbook-types.md#powershell-workflow-runbooks)。 使用`Type`參數指定為`New-AzAutomationRunbook`定義的 Runbook 類型之一。

下面的示例演示如何創建新的空 Runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>匯入 Runbook

您可以通過導入 PowerShell 腳本或 PowerShell 工作流 **（.ps1）、** 匯出的圖形運行**簿 （.graphrunbook）** 或 Python 2 腳本 **（.py）** 在 Azure 自動化中創建新的 Runbook。  您必須指定匯入期間建立的 [Runbook 類型](automation-runbook-types.md)，並考量下列事項。

* 不包含工作流的 **.ps1**檔可以導入到[PowerShell 運行簿](automation-runbook-types.md#powershell-runbooks)或[PowerShell 工作流運行簿](automation-runbook-types.md#powershell-workflow-runbooks)中。 如果將其導入 PowerShell 工作流運行簿，則將其轉換為工作流。 在這種情況下，注釋包含在 Runbook 中，以描述已進行的更改。

* 包含 PowerShell 工作流的 **.ps1**檔只能導入到[PowerShell 工作流運行簿](automation-runbook-types.md#powershell-workflow-runbooks)中。 如果檔包含多個 PowerShell 工作流，則導入將失敗。 您必須將每個工作流程儲存到它們各自的檔案，並且個別匯入。

* 包含 PowerShell 工作流的 **.ps1**檔不應導入[到 PowerShell 運行簿](automation-runbook-types.md#powershell-runbooks)中，因為 PowerShell 腳本引擎無法識別它。

* **.graphrunbook**檔只能導入到新的[圖形運行簿](automation-runbook-types.md#graphical-runbooks)中。 請注意，您只能從 **.graphrunbook**檔創建圖形運行簿。

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>使用 Azure 門戶從檔導入 Runbook

您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。

> [!NOTE]
> 您只能使用門戶將 **.ps1**檔導入 PowerShell 工作流運行簿。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從中心，在 **"進程自動化**"下選擇**Runbook**以打開 Runbook 清單。
3. 按一下 **"導入運行簿**"。
4. 按一下**Runbook 檔**並選擇要導入的檔。
5. 如果啟用了 **"名稱"** 欄位，則可以選擇更改 Runbook 名稱。 名稱必須以字母開頭，並可以包含字母、數位、底線和破折號。
6. [Runbook 類型](automation-runbook-types.md) 會自動選取，但在考量適用的限制之後，您可以變更類型。
7. 按一下 **[建立]**。 新的 Runbook 會出現在自動化帳戶的 Runbook 清單中。
8. 您必須 [發佈 Runbook](#publish-a-runbook) ，才能執行。

> [!NOTE]
> 導入圖形運行簿或圖形 PowerShell 工作流運行簿後，可以將其轉換為其他類型。 但是，無法將這些圖形運行簿之一轉換為文本 Runbook。

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>使用 Windows PowerShell 從指令檔導入運行簿

使用[導入-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) Cmdlet 將指令檔導入為 PowerShell 工作流運行簿草稿。 如果 Runbook 已存在，則導入將失敗，`Force`除非您將參數與 Cmdlet 一起使用。

下面的示例演示如何將指令檔導入 Runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>測試 Runbook

當您測試 Runbook 時， [草稿版本](#publish-a-runbook) 會執行，而且它執行的任何動作都會完成。 未創建作業歷史記錄，但"測試輸出"窗格中將顯示["輸出](automation-runbook-output-and-messages.md#output-stream)"和["警告和錯誤](automation-runbook-output-and-messages.md#message-streams)"流。 僅當`VerbosePreference`變數*（自動化運行簿-輸出和消息.md_首選項變數）設置為"繼續"時，發送到["詳細"流](automation-runbook-output-and-messages.md#message-streams)的消息才會顯示在"輸出"窗格中。

即使執行的是草稿版本，Runbook 仍會正常執行，並對環境中的資源執行任何動作。 因此，您只應在非生產資源中測試 Runbook。

測試每[種類型的 Runbook](automation-runbook-types.md)的過程是相同的。 在 Azure 門戶中的文字編輯器和圖形編輯器之間的測試沒有區別。

1. 在[文字編輯器](automation-edit-textual-runbook.md)或[圖形編輯器](automation-graphical-authoring-intro.md)中打開 Runbook 的草稿版本。
1. 按一下 [測試]**** 按鈕以開啟 [測試] 頁面。
1. 如果 Runbook 具有參數，則它們列在左側窗格中，您可以在其中提供用於測試的值。
1. 如果要在[混合 Runbook 工作執行緒](automation-hybrid-runbook-worker.md)上運行測試，請將 **"回合設定"** 更改為 **"混合輔助角色**"，然後選擇目標群組的名稱。  否則，請保留預設值 **Azure**，以便在雲端中執行測試。
1. 按一下"**開始"** 按鈕開始測試。
1. 您可以在"輸出"窗格下使用按鈕在測試時停止或掛起[PowerShell 工作流](automation-runbook-types.md#powershell-workflow-runbooks)或[圖形](automation-runbook-types.md#graphical-runbooks)運行簿。 暫停 Runbook 時，它會先完成目前的活動後才暫停。 Runbook 一旦暫停，您可以選擇停止或重新啟動。
1. 檢查"輸出"窗格中的 Runbook 中的輸出。

## <a name="publish-a-runbook"></a>發行 Runbook

當您建立或匯入新的 Runbook 時，您必須發佈才能執行它。 Azure 自動化中的每個 Runbook 都有草稿版本和已發佈版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 當草稿版本應可用時，您可以發佈它，用草稿版本覆蓋當前發佈的版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 門戶中發佈 Runbook

1. 在 Azure 入口網站中開啟 Runbook。
2. 按一下 **[編輯]**。
3. 按一下 **"發佈"，** 然後按一下 **"是**"以回應驗證消息。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 發佈運行簿

使用[發佈-阿茲自動化 Runbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) Cmdlet 使用 Windows PowerShell 發佈 Runbook。 下面的示例演示如何發佈示例 Runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>後續步驟

* 要瞭解如何從 Runbook 和 PowerShell 模組庫中獲益，請參閱[Azure 自動化的 Runbook 和模組庫](automation-runbook-gallery.md)。
* 要瞭解有關使用文字編輯器編輯 PowerShell 和 PowerShell 工作流 Runbook 的詳細資訊，請參閱[在 Azure 自動化中編輯文本 Runbook](automation-edit-textual-runbook.md)。
* 要瞭解有關圖形運行簿創作的更多資訊，請參閱[Azure 自動化 中的圖形創作](automation-graphical-authoring-intro.md)。
