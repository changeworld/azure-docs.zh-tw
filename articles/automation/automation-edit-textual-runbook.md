---
title: 在 Azure 自動化中編輯文字式 Runbook
description: 本文提供使用文字式編輯器在 Azure 自動化中使用 PowerShell 和 PowerShell 工作流程 Runbook 的不同程序。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aef6a58d18c0d9c1b18915786dd6e3359c31eda0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855787"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>在 Azure 自動化中編輯文字式 Runbook

Azure 自動化中的文字式編輯器可以用來編輯 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 此編輯器具有其他程式碼編輯器的一般功能，例如 IntelliSense。 它也具有其他特殊功能的色彩編碼，可協助您存取 runbook 通用的資源。 

此文字式編輯器具有將 Cmdlet、資產、子 Runbook 程式碼插入 Runbook 的功能。 您不需要自行輸入程式碼，而是可以從可用資源的清單中選取，而編輯器會將適當的程式碼插入 runbook。

Azure 自動化中的每個 Runbook 有兩個版本，「草稿」和「已發佈」。 您編輯 Runbook 的「草稿」版本然後發佈，讓它可以執行。 無法編輯「已發佈」版本。 如需詳細資訊，請參閱[發佈 runbook](manage-runbooks.md#publish-a-runbook)。

本文提供執行與此編輯器不同的功能的詳細步驟。 這些不適用於[圖形化 runbook](automation-runbook-types.md#graphical-runbooks)。 若要使用這些 runbook，請參閱[Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="editing-a-runbook-with-the-azure-portal"></a>使用 Azure 入口網站編輯 runbook

使用下列程序以開啟 Runbook，在文字式編輯器中進行編輯。

1. 在 [Azure 入口網站中，選取您的自動化帳戶。
2. 在 [**進程自動化**] 底下，選取 [ **runbook** ] 以開啟 runbook 清單。
3. 選擇要編輯的 runbook，然後按一下 [**編輯**]。
4. 編輯 Runbook。
5. 編輯完成時，按一下 [儲存]****。
6. 如果您想要發佈 runbook 的最新草稿版本，請按一下 [**發佈**]。

### <a name="insert-a-cmdlet-into-a-runbook"></a>將 Cmdlet 插入 runbook

1. 在文字式編輯器的 [畫布] 中，將游標放在您要放置 Cmdlet 的位置。
2. 在程式庫控制項中，展開 **Cmdlet** 節點。
3. 展開包含要使用之 Cmdlet 的模組。
4. 以滑鼠右鍵按一下要插入的 Cmdlet 名稱，然後選取 [**新增至畫布**]。 如果 Cmdlet 有一個以上的參數集，則編輯器會加入預設集合。 您也以展開 Cmdlet 以選取不同的參數集合。
5. 請注意，Cmdlet 的程式碼會連同其完整的參數清單一起插入。
6. 針對任何必要的參數，提供適當的值來取代以角括弧（<>）括住的值。 移除您不需要的任何參數。

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>將子 runbook 的程式碼插入 runbook

1. 在文字式編輯器的 [畫布] 中，將游標放在您要放置[子 runbook](automation-child-runbooks.md)程式碼的位置。
2. 在程式庫控制項中，展開 **Runbook** 節點。
3. 以滑鼠右鍵按一下要插入的 Runbook，然後選取 [新增至畫布]****。
4. 會插入子 Runbook 的程式碼且具有任何 Runbook 參數的預留位置。
5. 針對每個參數以適當值取代預留位置。

### <a name="insert-an-asset-into-a-runbook"></a>將資產插入 runbook

1. 在文字式編輯器的 Canvas 控制項中，將游標放在您要放置子 runbook 程式碼的位置。
2. 在程式庫控制項中，展開 [資產] **** 節點。
3. 展開所需資產類型的節點。
4. 以滑鼠右鍵按一下要插入的資產名稱，然後選取 [**新增至畫布**]。 針對[變數資產](automation-variables.md)，請選取 [**將「取得變數」新增至畫布**，或**將「設定變數」新增至畫布**，視您是否要取得或設定變數而定。
5. 請注意，資產的程式碼會插入至 runbook。

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>使用 Windows PowerShell 編輯 Azure 自動化 runbook

若要使用 Windows PowerShell 編輯 runbook，請使用您選擇的編輯器，並將 runbook 儲存至**ps1**檔案。 您可以使用[AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) Cmdlet 來取出 runbook 的內容。 您可以使用[AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) Cmdlet，將現有的草稿 runbook 取代為已修改的 runbook。

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 取出 runbook 的內容

以下範例命令顯示如何擷取 Runbook 的指令碼，然後將其儲存至指令碼檔案。 在此範例中，會擷取「草稿」版本。 您也可以抓取已發佈的 runbook 版本，但無法變更此版本。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 變更 runbook 的內容

下列範例命令顯示如何以指令碼檔案內容取代現有 Runbook 的內容。 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>相關文章

* [管理 Azure 自動化中的 Runbook](manage-runbooks.md)
* [瞭解 PowerShell 工作流程](automation-powershell-workflow.md)
* [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
* [憑證](automation-certificates.md)
* [連線](automation-connections.md)
* [認證](automation-credentials.md)
* [排程](automation-schedules.md)
* [變數](automation-variables.md)
* [PowerShell Cmdlet 參考](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
