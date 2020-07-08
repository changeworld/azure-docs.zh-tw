---
title: Azure 自動化 Runbook 類型
description: 本文描述您在 Azure 自動化中可使用的 Runbook 類型，以及決定使用何種類型的考量。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 184e65c929d43e7a5d4ca3be8bd93770c55cd2a5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836561"
---
# <a name="azure-automation-runbook-types"></a>Azure 自動化 Runbook 類型

Azure 自動化流程的自動化功能支援數種類型的 Runbook，如下表所定義。 若要了解流程自動化環境，請參閱 [Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。

| 類型 | 描述 |
|:--- |:--- |
| [圖形化](#graphical-runbooks)|圖形化 Runbook 是以 Windows PowerShell 為基礎，而且完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [圖形化 PowerShell 工作流程](#graphical-runbooks)|圖形化 Runbook 是以 Windows PowerShell 工作流程為基礎，而且完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [PowerShell](#powershell-runbooks) |以 Windows PowerShell 指令碼為基礎的文字 Runbook。 |
| [PowerShell 工作流程](#powershell-workflow-runbooks)|以 Windows PowerShell 工作流程指令碼為基礎的文字 Runbook。 |
| [Python](#python-runbooks) |以 Python 指令碼為基礎的文字 Runbook。 |

在決定要針對特定 Runbook 使用何種類型時，請考慮下列考量。

* 您無法將 Runbook 從圖形化類型轉換為文字類型，反之亦然。
* 使用不同類型的 Runbook 作為子 Runbook 時有所限制。 如需詳細資訊，請參閱 [Azure 自動化中的子 Runbook](automation-child-runbooks.md)。

## <a name="graphical-runbooks"></a>圖形化 Runbook

您可以使用 Azure 入口網站中的圖形化編輯器，建立和編輯圖形化和圖形化 PowerShell 工作流程 Runbook。 不過，您無法使用另一個工具來建立或編輯這種類型的 Runbook。 圖形化 Runbook 的主要功能：

* 可以匯出至您自動化帳戶中的檔案，然後匯入至另一個自動化帳戶。 
* 產生 PowerShell 程式碼。 
* 可以在匯入期間轉換為圖形化 PowerShell 工作流程 Runbook，或從該 Runbook 進行轉換。 

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

* 使用 PowerShell 程式碼實作所有複雜的邏輯，不必處理 PowerShell 工作流程的其他複雜性。
* 啟動速度會比 PowerShell 工作流程 Runbook 更快，因為其在執行之前不需要編譯。
* 在 Azure 中以及 Windows 和 Linux 的混合式 Runbook 背景工作角色上執行。

### <a name="limitations"></a>限制

* 必須熟悉 PowerShell 指令碼。
* Runbook 無法使用[平行處理](automation-powershell-workflow.md#use-parallel-processing)來平行執行多個動作。
* 發生錯誤時，Runbook 無法使用[檢查點](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)來繼續執行 Runbook。
* 您可以使用 [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) Cmdlet (其會建立新的工作)，只將 PowerShell 工作流程 Runbook 和圖形化 Runbook 包含為子 Runbook。

### <a name="known-issues"></a>已知問題

以下是 PowerShell Runbook 目前已知的問題：

* PowerShell Runbook 無法擷取具有 Null 值的未加密[變數資產](automation-variables.md)。
* PowerShell Runbook 無法擷取名稱中含有 `*~*` 的變數資產。
* PowerShell Runbook 中落入迴圈的 [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) 作業大約在 80 次反覆運算之後就會損毀。
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

Python 2 下的 Python Runbook 編譯。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。 您也可以使用離線文字編輯器，並[匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用強固的 Python 程式庫。
* 可以在 Azure 中或在 Linux 混合式 Runbook 背景工作角色上執行。 安裝 [python 2.7](https://www.python.org/downloads/release/latest/python2) 後，支援 Windows 混合式 Runbook 背景工作角色。

### <a name="limitations"></a>限制

* 必須熟悉 Python 指令碼。
* 目前僅支援 Python 2。 任何 Python 3 特定功能都會失敗。
* 若要使用第三方程式庫，您必須[匯入套件](python-packages.md)至自動化帳戶。

## <a name="next-steps"></a>後續步驟

* 若要了解 PowerShell Runbook，請參閱[教學課程：建立 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要了解 PowerShell 工作流程 Runbook，請參閱[教學課程：建立 PowerShell 工作流程 Runbook](learn/automation-tutorial-runbook-textual.md)。
* 若要了解圖形化 Runbook，請參閱[教學課程：建立圖形化 Runbook](learn/automation-tutorial-runbook-graphical.md)。
* 若要了解 Python Runbook，請參閱[教學課程：建立 Python Runbook](learn/automation-tutorial-runbook-textual-python2.md)。