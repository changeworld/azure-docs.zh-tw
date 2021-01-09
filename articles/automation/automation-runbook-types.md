---
title: Azure 自動化 Runbook 類型
description: 本文描述您在 Azure 自動化中可使用的 Runbook 類型，以及決定使用何種類型的考量。
services: automation
ms.subservice: process-automation
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: c1398d25b6d2540abea0012acd69555e5e53e25c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050964"
---
# <a name="azure-automation-runbook-types"></a>Azure 自動化 Runbook 類型

Azure 自動化流程的自動化功能支援數種類型的 Runbook，如下表所定義。 若要了解流程自動化環境，請參閱 [Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。

| 類型 | 描述 |
|:--- |:--- |
| [圖形化](#graphical-runbooks)|圖形化 Runbook 是以 Windows PowerShell 為基礎，而且完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [圖形化 PowerShell 工作流程](#graphical-runbooks)|圖形化 Runbook 是以 Windows PowerShell 工作流程為基礎，而且完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [PowerShell](#powershell-runbooks) |以 Windows PowerShell 腳本為基礎的文字 runbook。 |
| [PowerShell 工作流程](#powershell-workflow-runbooks)|以 Windows PowerShell 工作流程腳本為基礎的文字 runbook。 |
| [Python](#python-runbooks) |以 Python 腳本為基礎的文字 runbook。 |

在決定要針對特定 Runbook 使用何種類型時，請考慮下列考量。

* 您無法將 Runbook 從圖形化類型轉換為文字類型，反之亦然。
* 使用不同類型的 Runbook 作為子 Runbook 時有所限制。 如需詳細資訊，請參閱 [Azure 自動化中的子 Runbook](automation-child-runbooks.md)。

## <a name="graphical-runbooks"></a>圖形化 Runbook

您可以使用 Azure 入口網站中的圖形化編輯器，建立和編輯圖形化和圖形化 PowerShell 工作流程 Runbook。 不過，您無法使用另一個工具來建立或編輯這種類型的 Runbook。 圖形化 Runbook 的主要功能：

* 匯出至自動化帳戶中的檔案，然後匯入到另一個自動化帳戶。
* 產生 PowerShell 程式碼。
* 在匯入期間轉換為圖形化 PowerShell 工作流程 runbook 或從中轉換。

### <a name="advantages"></a>優點

* 使用虛擬插入連結設定撰寫模型。
* 將焦點放在資料如何透過此程序來流動。
* 以視覺方式呈現管理程序。
* 包含其他 Runbook 做為子 Runbook 來建立高階工作流程。
* 建議您採用模組化的程式設計。

### <a name="limitations"></a>限制

* 無法在 Azure 入口網站外部建立或編輯。
* 可能需要包含 PowerShell 程式碼的程式碼活動來執行複雜邏輯。
* 無法轉換成其中一個[文字格式](automation-runbook-types.md)，也無法將文字 Runbook 轉換成圖形格式。 
* 無法檢視或直接編輯圖形化工作流程所建立的 PowerShell 程式碼。 您可以檢視您在任何程式碼活動中所建立的程式碼。
* 無法在 Linux 混合式 Runbook 背景工作角色上執行 Runbook。 請參閱[使用混合式 Runbook 背景工作角色將資料中心或雲端中的資源自動化](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook 以 Windows PowerShell 為基礎。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。  您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 程式碼來執行所有複雜邏輯，而不需要 PowerShell 工作流程的其他複雜性。
* 啟動速度會比 PowerShell 工作流程 Runbook 更快，因為其在執行之前不需要編譯。
* 在 Azure 中以及 Windows 和 Linux 的混合式 Runbook 背景工作角色上執行。

### <a name="limitations"></a>限制

* 必須熟悉 PowerShell 指令碼。
* Runbook 無法使用[平行處理](automation-powershell-workflow.md#use-parallel-processing)來平行執行多個動作。
* 發生錯誤時，Runbook 無法使用[檢查點](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)來繼續執行 Runbook。
* 您可以使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) Cmdlet (其會建立新的工作)，只將 PowerShell 工作流程 Runbook 和圖形化 Runbook 包含為子 Runbook。

### <a name="known-issues"></a>已知問題

以下是 PowerShell Runbook 目前已知的問題：

* PowerShell Runbook 無法擷取具有 Null 值的未加密[變數資產](./shared-resources/variables.md)。
* PowerShell Runbook 無法擷取名稱中含有 `*~*` 的變數資產。
* PowerShell Runbook 中落入迴圈的 [Get-Process](/powershell/module/microsoft.powershell.management/get-process) 作業大約在 80 次反覆運算之後就會損毀。
* 如果 PowerShell Runbook 嘗試同時將大量資料寫入輸出資料流，則可能會失敗。 在處理大型物件時，讓 Runbook 只輸出所需的資訊，通常就可以解決這個問題。 例如，不是使用沒有限制的 `Get-Process`，而是讓 Cmdlet 只輸出必要參數，如 `Get-Process | Select ProcessName, CPU` 中所示。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流程 Runbook

PowerShell Workflow Runbook 是以 [Windows PowerShell 工作流程](automation-powershell-workflow.md)為基礎的文字 Runbook。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。 您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 工作流程程式碼實作所有複雜的邏輯。
* 發生錯誤時，使用[檢查點](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)來繼續執行作業。
* 使用[平行處理](automation-powershell-workflow.md#use-parallel-processing)來平行執行多個動作。
* 可併入其他圖形化 Runbook 和 PowerShell 工作流程 Runbook 成為子 Runbook，以建立高階工作流程。

### <a name="limitations"></a>限制

* 您必須熟悉 PowerShell 工作流程。
* Runbook 必須處理 PowerShell 工作流程額外的複雜性，例如 [已還原序列化的物件](automation-powershell-workflow.md#deserialized-objects)。
* Runbook 所需的啟動時間比 PowerShell Runbook 更久，因為其在執行之前必須進行編譯。
* 您只能使用 `Start-AzAutomationRunbook` Cmdlet，將 PowerShell runbook 包含為子 runbook。
* Runbook 無法在 Linux 混合式 Runbook 背景工作角色上執行。

## <a name="python-runbooks"></a>Python Runbook

Python 2 和 Python 3 下的 python runbook 編譯。 Python 3 runbook 目前為預覽狀態。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。 您也可以使用離線文字編輯器，並[匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用強固的 Python 程式庫。
* 可在 Azure 中或混合式 Runbook 背景工作角色上執行。
* 針對 Python 2，已安裝 [python 2.7](https://www.python.org/downloads/release/latest/python2) 支援 Windows 混合式 Runbook 背景工作角色。
* 針對 Python 3 雲端作業，支援 Python 3.8 版本。 如果程式碼在不同版本之間相容，則任何3.x 版本的腳本和封裝都可能適用。  
* 針對 Windows 電腦上的 Python 3 混合式作業，您可以選擇安裝任何您可能想要使用的3.x 版。  
* 針對 Linux 機器上的 Python 3 混合式作業，我們取決於電腦上安裝的 Python 3 版本，以執行 DSC OMSConfig 和 Linux 混合式背景工作角色。 建議您在 Linux 機器上安裝3.6。 不過，如果方法簽章或 Python 3 版本之間的合約沒有任何重大變更，則不同的版本也應可運作。

### <a name="limitations"></a>限制

* 必須熟悉 Python 指令碼。
* 若要使用第三方程式庫，您必須[匯入套件](python-packages.md)至自動化帳戶。
* 使用   Powershell/powershell 工作流程中的 AutomationRunbook Cmdlet 啟動 Python 3 runbook (預覽) 無法運作。 您可以使用 **AzAutomationRunbook** Cmdlet 從 Az. automation 模組或 **>start-azurermautomationrunbook** Cmdlet，在 AzureRm. automation 模組中解決這項限制。  
* Python 3 runbook (預覽版) 和套件無法搭配 PowerShell 使用。
* Azure 自動化不支援 **sys. stderr**。

### <a name="known-issues"></a>已知問題

Python 3 作業有時候會失敗，並出現例外狀況訊息 *不正確解譯器可執行檔路徑*。 如果工作延遲、啟動超過10分鐘，或使用 **AutomationRunbook** 啟動 Python 3 runbook，您可能會看到此例外狀況。 如果工作延遲，則重新開機 runbook 應該就已足夠。

## <a name="next-steps"></a>後續步驟

* 若要了解 PowerShell Runbook，請參閱[教學課程：建立 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要了解 PowerShell 工作流程 Runbook，請參閱[教學課程：建立 PowerShell 工作流程 Runbook](learn/automation-tutorial-runbook-textual.md)。
* 若要了解圖形化 Runbook，請參閱[教學課程：建立圖形化 Runbook](learn/automation-tutorial-runbook-graphical.md)。
* 若要了解 Python Runbook，請參閱[教學課程：建立 Python Runbook](learn/automation-tutorial-runbook-textual-python2.md)。
