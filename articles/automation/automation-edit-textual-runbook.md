---
title: 在 Azure 自動化中編輯文字式 Runbook
description: 本文說明如何使用 Azure 自動化文字式編輯器來處理 PowerShell 和 PowerShell 工作流程 Runbook。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d388162738930030ba311a04a0dce1db15590c79
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836833"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>在 Azure 自動化中編輯文字式 Runbook

您可以使用 Azure 自動化中的文字式編輯器來編輯 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 此編輯器具有其他程式碼編輯器的一般功能，例如 IntelliSense。 其也會搭配其他特殊功能來使用色彩編碼，以協助您存取 Runbook 通用的資源。 

此文字式編輯器具有將 Cmdlet、資產、子 Runbook 程式碼插入 Runbook 的功能。 您不需要自行輸入程式碼，而是可以從可用資源的清單中選取，編輯器就會將適當的程式碼插入 Runbook 中。

Azure 自動化中的每個 Runbook 有兩個版本，「草稿」和「已發佈」。 您編輯 Runbook 的「草稿」版本然後發佈，讓它可以執行。 無法編輯「已發佈」版本。 如需詳細資訊，請參閱[發佈 Runbook](manage-runbooks.md#publish-a-runbook)。

本文提供執行與此編輯器不同的功能的詳細步驟。 這些不適用於[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)。 若要使用這些 Runbook，請參閱 [Azure 自動化中的圖形化撰寫](automation-graphical-authoring-intro.md)。

## <a name="edit-a-runbook-with-the-azure-portal"></a>使用 Azure 入口網站編輯 Runbook

1. 在 Azure 入口網站中，選取您的自動化帳戶。
2. 在 [程序自動化] 底下選取 [Runbook] 開啟 Runbook 清單。
3. 選擇要編輯的 Runbook，然後按一下 [編輯]。
4. 編輯 Runbook。
5. 當您完成編輯時，按一下 [儲存]  。
6. 如果您要發佈 Runbook 的最新草稿版本，請按一下 [發佈]。

### <a name="insert-a-cmdlet-into-a-runbook"></a>將 Cmdlet 插入 Runbook

1. 在文字式編輯器的 [畫布] 中，將游標移至您要放置 Cmdlet 的位置。
2. 在程式庫控制項中，展開 **Cmdlet** 節點。
3. 展開包含所要使用 Cmdlet 的模組。
4. 以滑鼠右鍵按一下要插入的 Cmdlet 名稱，然後選取 [新增至畫布]。 如果 Cmdlet 有一個以上的參數集合，編輯器會新增預設集合。 您也以展開 Cmdlet 以選取不同的參數集合。
5. 請注意，會插入 Cmdlet 的程式碼且具有參數的完整清單。
6. 針對任何必要的參數，提供適當的值來取代以角括弧 (<>) 括住的值。 移除您不需要的任何參數。

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>將子 Runbook 的程式碼插入 Runbook

1. 在文字式編輯器的 [畫布] 中，將游標移至您要放置[子 Runbook](automation-child-runbooks.md) 程式碼的位置。
2. 在程式庫控制項中，展開 **Runbook** 節點。
3. 以滑鼠右鍵按一下要插入的 Runbook，然後選取 [新增至畫布]。
4. 會插入子 Runbook 的程式碼且具有任何 Runbook 參數的預留位置。
5. 針對每個參數以適當值取代預留位置。

### <a name="insert-an-asset-into-a-runbook"></a>將資產插入 Runbook

1. 在文字式編輯器的 [畫布] 控制項中，將游標移至您要放置子 Runbook 程式碼的位置。
2. 在程式庫控制項中，展開 [資產]  節點。
3. 展開所需資產類型的節點。
4. 以滑鼠右鍵按一下要插入的資產名稱，然後選取 [新增至畫布]。 針對[變數資產](automation-variables.md)，根據您要取得還是設定變數而定，選取 [將「取得變數」新增至畫布] 或 [將「設定變數」新增至畫布]。
5. 請注意，資產的程式碼會插入 Runbook 中。

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>使用 Windows PowerShell 編輯 Azure 自動化 Runbook

若要使用 Windows PowerShell 編輯 Runbook，請使用您選擇的編輯器，然後將 Runbook 儲存為 **.ps1** 檔案。 您可以使用 [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) Cmdlet 來擷取 Runbook 的內容。 您可以使用 [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) Cmdlet，將現有的草稿 Runbook 取代為已修改的 Runbook。

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 擷取 Runbook 的內容

以下範例命令顯示如何擷取 Runbook 的指令碼，然後將其儲存至指令碼檔案。 在此範例中，會擷取「草稿」版本。 也可以擷取「已發佈」版本的 Runbook，雖然這個版本無法變更。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 變更 Runbook 的內容

下列範例命令顯示如何以指令碼檔案內容取代現有 Runbook 的內容。 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

* [管理 Azure 自動化中的 Runbook](manage-runbooks.md)。
* [了解 PowerShell 工作流程](automation-powershell-workflow.md)。
* [Azure 自動化中的圖形化撰寫](automation-graphical-authoring-intro.md)。
* [憑證](automation-certificates.md)。
* [連接](automation-connections.md)。
* [認證](automation-credentials.md)。
* [排程](automation-schedules.md)。
* [變數](automation-variables.md)。
* [PowerShell Cmdlet 參考](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)。
