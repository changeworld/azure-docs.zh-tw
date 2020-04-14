---
title: Azure 自動化 Runbook 類型
description: 描述可在 Azure 自動化中使用不同類型的 Runbook 以及確定要使用的類型的注意事項。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 4e8a5d2d168b8f60b7a32a8af358c6097003de60
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261290"
---
# <a name="azure-automation-runbook-types"></a>Azure 自動化 Runbook 類型

Azure 自動化過程自動化服務支援幾種類型的 Runbook,如下表中定義的。 要瞭解流程自動化環境,請參閱[Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。

| 類型 | 描述 |
|:--- |:--- |
| [圖形化](#graphical-runbooks)|基於 Windows PowerShell 的圖像化執行簿,並在 Azure 門戶中的圖形編輯器中完全創建和編輯。 |
| [圖形化 PowerShell 工作流程](#graphical-runbooks)|基於 Windows PowerShell 工作流的圖形執行簿,並在 Azure 門戶中的圖形編輯器中完全創建和編輯。 |
| [PowerShell](#powershell-runbooks) |基於 Windows PowerShell 文本的文本執行簿。 |
| [PowerShell 工作流程](#powershell-workflow-runbooks)|基於 Windows PowerShell 工作流腳本的文本執行簿。 |
| [Python](#python-runbooks) |基於 Python 文稿的文字執行簿。 |

在確定要用於特定 Runbook 的類型時,請考慮以下注意事項。

* 不能將 Runbook 從圖形類型轉換為文本類型,或者從另一種方式轉換。
* 使用不同類型的 Runbook 作為子 Runbook 時存在限制。 如需詳細資訊，請參閱 [Azure 自動化中的子 Runbook](automation-child-runbooks.md)。

## <a name="graphical-runbooks"></a>圖形化 Runbook

您可以使用 Azure 門戶中的圖形編輯器建立和編輯圖形和圖形 PowerShell 工作流 Runbook。 但是,您無法使用其他工具創建或編輯這種類型的 Runbook。 圖形手冊的主要功能:

* 可以匯出到自動化帳戶中的檔,然後導入到另一個自動化帳戶。 
* 生成 PowerShell 程式碼。 
* 可以在導入期間轉換為圖形 PowerShell 工作流執行簿或從其轉換。 

### <a name="advantages"></a>優點

* 使用可視插入連結配置創作模型。
* 關注數據如何流經流程。
* 以視覺方式呈現管理程序。
* 將其他 Runbook 作為子 Runbook 進行創建高級工作流。
* 鼓勵模組化程式設計。

### <a name="limitations"></a>限制

* 無法在 Azure 門戶之外創建或編輯。
* 可能需要包含 PowerShell 代碼的程式碼活動來執行複雜的邏輯。
* 無法轉換為其中一種[文字格式](automation-runbook-types.md),也無法將文本 Runbook 轉換為圖形格式。 
* 無法查看或直接編輯圖形工作流創建的 PowerShell 程式碼。 您可以查看在任何程式碼活動中建立的代碼。
* 無法在 Linux 混合 Runbook 工作線程上運行 Runbook。 請參考[使用混合 Runbook 的設定的資料中心或雲端的資源](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook 以 Windows PowerShell 為基礎。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。  您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 程式碼實作所有複雜的邏輯，不必處理 PowerShell 工作流程的其他複雜性。
* 比 PowerShell 工作流執行簿更快地啟動,因為它們在運行之前不需要編譯。
* 在 Azure 和 Windows 和 Linux 的混合 Runbook 工作簿上運行。

### <a name="limitations"></a>限制

* 您必須熟悉 PowerShell 腳本。
* Runbook 不能使用[並行處理](automation-powershell-workflow.md#parallel-processing)並行執行多個操作。
* 如果出現錯誤,Runbook 不能使用[檢查點](automation-powershell-workflow.md#checkpoints)恢復 Runbook。
* 通過使用創建新作業[的「開始-AzAutomationRunbook」cmdlet,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)只能將 PowerShell 工作流執行簿和圖形執行簿作為子 Runbook。

### <a name="known-issues"></a>已知問題

以下是 PowerShell Runbook 的目前已知問題:

* PowerShell Runbook 無法檢查索有空值的未加密[變數資產](automation-variables.md)。
* PowerShell Runbook 無法擷取名稱中含有  的`*~*`變數資產。
* PowerShell Runbook 中迴圈中的[「獲取過程](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7)」操作在大約 80 次反覆運算後可能會崩潰。
* 如果 PowerShell Runbook 嘗試一次向輸出流寫入大量數據,則該運行簿可能會失敗。 通常,可以通過使 Runbook 輸出只是處理大型物件所需的資訊來解決此問題。 例如,可以讓 cmdlet 輸出僅具有所需的參數,而不是在沒有限制`Get-Process`時使用`Get-Process | Select ProcessName, CPU`cmdlet 輸出, 就像在 中一樣。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流程 Runbook

PowerShell Workflow Runbook 是以 [Windows PowerShell 工作流程](automation-powershell-workflow.md)為基礎的文字 Runbook。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。 您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 工作流程程式碼實作所有複雜的邏輯。
* 如果出現錯誤[,請使用檢查點](automation-powershell-workflow.md#checkpoints)恢復操作。
* 使用[並行處理](automation-powershell-workflow.md#parallel-processing)並行執行多個操作。
* 可以包括其他圖形執行簿和 PowerShell 工作流執行簿作為子 Runbook 以創建進階工作流。

### <a name="limitations"></a>限制

* 您必須熟悉 PowerShell 工作流。
* Runbook 必須處理 PowerShell 工作串流的額外複雜性,例如[反序列化物件](automation-powershell-workflow.md#code-changes)。
* 運行簿的啟動時間比 PowerShell Runbook 要長,因為它們必須在運行前進行編譯。
* 您只能使用`Start-AzAutomationRunbook`cmdlet 將 PowerShell Runbook 作為子執行簿。
* Runbook 無法在 Linux 混合 Runbook 工作線程上運行。

## <a name="python-runbooks"></a>Python Runbook

Python 2 下的 Python Runbook 編譯。 您可以使用 Azure 門戶中的文字編輯器直接編輯 Runbook 的代碼。 您還可以使用離線文字編輯器並將[Runbook 匯入](manage-runbooks.md)Azure 自動化。

### <a name="advantages"></a>優點

* 使用強大的 Python 庫。
* 可以在 Azure 或 Linux 混合 Runbook 工作簿 上運行。 安裝了[python2.7](https://www.python.org/downloads/release/latest/python2)支援 Windows 混合 Runbook 工作台。

### <a name="limitations"></a>限制

* 您必須熟悉 Python 文稿。
* 目前僅支援 Python 2。 任何 Python 3 特定的函數都失敗。
* 要使用第三方庫,必須[將包導入](python-packages.md)自動化帳戶。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關圖形執行簿創作的更多資訊,請參閱[Azure 自動化 中的圖形創作](automation-graphical-authoring-intro.md)。
* 要瞭解 Runbook 的 PowerShell 工作流和 PowerShell 工作流之間的差異,請參閱[學習 Windows PowerShell 工作流](automation-powershell-workflow.md)。
* 有關如何創建或導入 Runbook 的詳細資訊,請參閱[在 Azure 自動化 中管理 Runbook。](manage-runbooks.md)
* 要瞭解有關 PowerShell 的更多資訊,包括語言參考和學習模組,請參閱[PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。
