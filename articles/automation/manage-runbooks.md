---
title: 管理 Azure 自動化中的 Runbook
description: 本文描述如何管理 Azure 自動化中的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ec53c4b2f80fb095f58bee9c15ac5daafb8d59ef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278372"
---
# <a name="manage-runbooks-in-azure-automation"></a>管理 Azure 自動化中的 Runbook

您可以透過[建立新](#create-a-runbook)的 runbook，或從檔案或[runbook 資源庫](automation-runbook-gallery.md)匯[入現有](#import-a-runbook)的 runbook，來將它新增至 Azure 自動化。 本文提供從檔案建立和匯入 Runbook 的資訊。 您可以在[Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)中取得存取社區 runbook 和模組的所有詳細資料。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="create-a-runbook"></a>建立 Runbook

您可以使用其中一個 Azure 入口網站或 Windows PowerShell，在 Azure 自動化中建立新的 Runbook。 一旦建立 Runbook 之後，您就能使用[了解 PowerShell 工作流程](automation-powershell-workflow.md)和 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)中的資訊來編輯它。

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中建立 Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從中樞選取 [程式**自動化**] 底下的 [ **runbook** ] 以開啟 runbook 清單。
3. 按一下 [**建立 runbook**]。
4. 輸入 runbook 的 [名稱]，然後選取其 [[類型](automation-runbook-types.md)]。 Runbook 名稱必須以字母開頭，而且可以包含字母、數位、底線和連字號。
5. 按一下 [建立] 來建立 Runbook 並開啟編輯器。

### <a name="create-a-runbook-with-powershell"></a>使用 PowerShell 建立 Runbook

您可以使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) Cmdlet 來建立空的[PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 使用*Type*參數來指定為**AzAutomationRunbook**定義的其中一個 runbook 類型。

下列範例顯示如何建立新的空白 runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>匯入 Runbook

您可以藉由匯入 PowerShell 腳本或 PowerShell 工作流程（ **. ps1**）、匯出的圖形化 runbook （ **. .Graphrunbook**）或 Python 2 腳本（ **. .py**），在 Azure 自動化中建立新的 runbook。  您必須指定匯入期間建立的 [Runbook 類型](automation-runbook-types.md)，並考量下列事項。

* 未包含工作流程的**ps1**檔案可以匯入至[Powershell Runbook](automation-runbook-types.md#powershell-runbooks)或[powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果您將它匯入 PowerShell 工作流程 runbook，則會將它轉換成工作流程。 在此情況下，runbook 中會包含批註，以描述已進行的變更。

* 包含 PowerShell 工作流程的**ps1**檔案只能匯入至[powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果檔案包含多個 PowerShell 工作流程，則匯入會失敗。 您必須將每個工作流程儲存到它們各自的檔案，並且個別匯入。

* 包含 PowerShell 工作流程的**ps1**檔案不應匯入[powershell runbook](automation-runbook-types.md#powershell-runbooks)中，因為 powershell 腳本引擎無法辨識該檔案。

* **.Graphrunbook**檔案只能匯入至新的[圖形化 runbook](automation-runbook-types.md#graphical-runbooks)。 請注意，您只能從 **.graphrunbook**檔案建立圖形化 runbook。

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>使用 Azure 入口網站從檔案匯入 runbook

您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。

> [!NOTE]
> 您只能使用入口網站，將**ps1**檔案匯入 PowerShell 工作流程 runbook。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從中樞選取 [程式**自動化**] 底下的 [ **runbook** ] 以開啟 runbook 清單。
3. 按一下 [匯**入 runbook**]。
4. 按一下 [ **Runbook**檔案]，然後選取要匯入的檔案。
5. 如果 [**名稱**] 欄位已啟用，則您可以選擇變更 runbook 名稱。 名稱必須以字母開頭，而且可以包含字母、數位、底線和連字號。
6. [Runbook 類型](automation-runbook-types.md) 會自動選取，但在考量適用的限制之後，您可以變更類型。
7. 按一下頁面底部的 [新增]。 新的 Runbook 會出現在自動化帳戶的 Runbook 清單中。
8. 您必須 [發佈 Runbook](#publish-a-runbook) ，才能執行。

> [!NOTE]
> 匯入圖形化 runbook 或圖形化 PowerShell 工作流程 runbook 之後，您可以將它轉換成另一種類型。 不過，您無法將這些圖形化 runbook 的其中一個轉換成文字 runbook。

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>使用 Windows PowerShell 從腳本檔案匯入 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) Cmdlet 將腳本檔案匯入為草稿 PowerShell 工作流程 runbook。 如果 runbook 已經存在，除非您搭配 Cmdlet 使用*Force*參數，否則匯入將會失敗。

下列範例顯示如何將腳本檔案匯入 runbook。

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

當您測試 Runbook 時， [草稿版本](#publish-a-runbook) 會執行，而且它執行的任何動作都會完成。 不會建立任何工作歷程記錄，但[輸出](automation-runbook-output-and-messages.md#output-stream)和[警告和錯誤](automation-runbook-output-and-messages.md#message-streams)資料流程會顯示在 [測試輸出] 窗格中。 只有在*VerbosePreference*變數 （automation-runbook-Output-messages. md # 喜好設定變數）設為 [**繼續**] 時，才會在 [輸出] 窗格中顯示訊息給[詳細資訊資料流程](automation-runbook-output-and-messages.md#message-streams)。

即使執行的是草稿版本，Runbook 仍會正常執行，並對環境中的資源執行任何動作。 因此，您只應在非生產資源中測試 Runbook。

測試每個[runbook 類型](automation-runbook-types.md)的程式都相同。 在文字式編輯器和 Azure 入口網站的圖形化編輯器之間進行測試並沒有差異。

1. 在[文字式編輯器](automation-edit-textual-runbook.md)或[圖形化編輯器](automation-graphical-authoring-intro.md)中開啟 runbook 的草稿版本。
1. 按一下 [測試] 按鈕以開啟 [測試] 頁面。
1. 如果 runbook 有參數，它們會列在左窗格中，您可以在其中提供要用於測試的值。
1. 如果您想要在混合式 Runbook 背景[工作角色](automation-hybrid-runbook-worker.md)上執行測試，請將 [**執行設定**] 變更為 [混合式背景**工作角色**]，然後選取目標群組的名稱。  否則，請保留預設值 **Azure**，以便在雲端中執行測試。
1. 按一下 [**啟動**] 按鈕以開始測試。
1. 您可以使用 [輸出] 窗格下的按鈕，在測試時停止或暫停[PowerShell 工作流程](automation-runbook-types.md#powershell-workflow-runbooks)或[圖形化](automation-runbook-types.md#graphical-runbooks)runbook。 暫停 Runbook 時，它會先完成目前的活動後才暫停。 Runbook 一旦暫停，您可以選擇停止或重新啟動。
1. 在 [輸出] 窗格中檢查 runbook 的輸出。

## <a name="publish-a-runbook"></a>發行 Runbook

當您建立或匯入新的 Runbook 時，您必須發佈才能執行它。 Azure 自動化中的每個 runbook 都有草稿版本和已發佈版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 當草稿版本應該可供使用時，您會將它發佈，以草稿版本覆寫目前已發行的版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中發佈 runbook

1. 在 Azure 入口網站中開啟 Runbook。
2. 按一下 **[編輯]** 。
3. 按一下 [**發佈**]，然後按 **[是]** 以回應驗證訊息。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 發佈 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) Cmdlet，透過 Windows PowerShell 發佈 runbook。 下列範例顯示如何發佈 runbook 範例。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何從 Runbook 和 PowerShell 模組資源庫中受益，請參閱[Azure 自動化的 runbook 和模組資源庫](automation-runbook-gallery.md)。
* 若要深入瞭解使用文字編輯器編輯 PowerShell 和 PowerShell 工作流程 runbook，請參閱[在 Azure 自動化中編輯文字 runbook](automation-edit-textual-runbook.md)。
* 若要深入瞭解圖形化 runbook 製作，請參閱[Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
