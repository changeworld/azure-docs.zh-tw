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
ms.openlocfilehash: 72aefb8de57e27718b14dba6a6d82deb8b63466f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367273"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>在 Azure 自動化中編輯文字式 Runbook

Azure 自動化中的文字式編輯器可以用來編輯 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 此編輯器具有其他代碼編輯器的典型功能，如 IntelliSense。 它還具有帶有其他特殊功能的顏色編碼，可説明您訪問 Runbook 常見的資源。 

此文字式編輯器具有將 Cmdlet、資產、子 Runbook 程式碼插入 Runbook 的功能。 您可以從可用資源清單中選擇，編輯器將適當的代碼插入 Runbook，而不是自己鍵入代碼。

Azure 自動化中的每個 Runbook 有兩個版本，「草稿」和「已發佈」。 您編輯 Runbook 的「草稿」版本然後發佈，讓它可以執行。 無法編輯「已發佈」版本。 有關詳細資訊，請參閱發佈[Runbook](manage-runbooks.md#publish-a-runbook)。

本文提供執行與此編輯器不同的功能的詳細步驟。 這些不適用於[圖形運行簿](automation-runbook-types.md#graphical-runbooks)。 要使用這些 Runbook，請參閱[Azure 自動化 中的圖形創作](automation-graphical-authoring-intro.md)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="editing-a-runbook-with-the-azure-portal"></a>使用 Azure 門戶編輯 Runbook

使用下列程序以開啟 Runbook，在文字式編輯器中進行編輯。

1. 在 Azure 門戶中，選擇自動化帳戶。
2. 在 **"進程自動化"** 下，選擇**Runbook**以打開 Runbook 清單。
3. 選擇要編輯的 Runbook，然後按一下"**編輯**"。
4. 編輯 Runbook。
5. 編輯完成時，按一下 [儲存]****。
6. 如果要發佈 Runbook 的最新草稿版本，請按一下 **"發佈**"。

### <a name="insert-a-cmdlet-into-a-runbook"></a>將 Cmdlet 插入到 Runbook 中

1. 在文字編輯器的畫布中，將游標放置在要放置 Cmdlet 的位置。
2. 在程式庫控制項中，展開 **Cmdlet** 節點。
3. 展開包含要使用的 Cmdlet 的模組。
4. 按右鍵 Cmdlet 名稱以插入並選擇"**添加到畫布**"。 如果 Cmdlet 具有多個參數集，則編輯器將添加預設集。 您也以展開 Cmdlet 以選取不同的參數集合。
5. 請注意，Cmdlet 的代碼將插入其整個參數清單。
6. 為任何必需的參數提供適當的值，以代替由角括弧（<>）括起來的值。 刪除不需要的任何參數。

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>將子運行簿的代碼插入到 Runbook 中

1. 在文字編輯器的畫布中，將游標定位到要放置[子 Runbook](automation-child-runbooks.md)的代碼的位置。
2. 在程式庫控制項中，展開 **Runbook** 節點。
3. 以滑鼠右鍵按一下要插入的 Runbook，然後選取 [新增至畫布]****。
4. 會插入子 Runbook 的程式碼且具有任何 Runbook 參數的預留位置。
5. 針對每個參數以適當值取代預留位置。

### <a name="insert-an-asset-into-a-runbook"></a>將資產插入到 Runbook 中

1. 在文字編輯器的 Canvas 控制項中，將游標定位到要放置子 Runbook 的代碼的位置。
2. 在程式庫控制項中，展開 [資產] **** 節點。
3. 展開所需資產類型的節點。
4. 按右鍵要插入的資產名稱，然後選擇 **"添加到畫布**"。 對於[變數資產](automation-variables.md)，選擇 **"獲取變數"以畫布**或**添加"設置變數"以畫布**，具體取決於您是想要獲取還是設置變數。
5. 請注意，資產的代碼將插入到 Runbook 中。

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>使用 Windows PowerShell 編輯 Azure 自動化運行簿

要使用 Windows PowerShell 編輯 Runbook，請使用您選擇的編輯器並將 Runbook 保存到 **.ps1**檔。 您可以使用[匯出-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) Cmdlet 檢索 Runbook 的內容。 您可以使用[導入-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) Cmdlet 將現有的草稿運行簿替換為修改後的計畫運行簿。

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 檢索 Runbook 的內容

以下範例命令顯示如何擷取 Runbook 的指令碼，然後將其儲存至指令碼檔案。 在此範例中，會擷取「草稿」版本。 也可以檢索 Runbook 的已發佈版本，儘管無法更改此版本。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 更改 Runbook 的內容

下列範例命令顯示如何以指令碼檔案內容取代現有 Runbook 的內容。 這個範例程序與[使用 Windows PowerShell 從指令碼檔案匯入 Runbook](manage-runbooks.md#import-a-runbook) 的相同。

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
* [學習 PowerShell 工作流](automation-powershell-workflow.md)
* [Azure 自動化中的圖形創作](automation-graphical-authoring-intro.md)
* [證書](automation-certificates.md)
* [連線](automation-connections.md)
* [認證](automation-credentials.md)
* [排程](automation-schedules.md)
* [變數](automation-variables.md)

