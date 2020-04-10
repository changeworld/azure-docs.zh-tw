---
title: Azure 自動化 Runbook 類型
description: 描述您在 Azure 自動化中可使用的各種 Runbook，以及您在決定使用何種類型時應該納入的考量。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010166"
---
# <a name="azure-automation-runbook-types"></a>Azure 自動化 Runbook 類型

Azure 自動化過程自動化服務支援多種類型的 Runbook。 下表簡要定義了這些類型,並在以下各節中進行了更詳細的描述。 要瞭解流程自動化環境,請參閱[Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。

| 類型 | 描述 |
|:--- |:--- |
| [圖形化](#graphical-runbooks)|基於 Windows PowerShell 的圖像化執行簿,並在 Azure 門戶中的圖形編輯器中完全創建和編輯。 |
| [圖形化 PowerShell 工作流程](#graphical-runbooks)|基於 Windows PowerShell 工作流的圖形執行簿,並在 Azure 門戶中的圖形編輯器中完全創建和編輯。 |
| [PowerShell](#powershell-runbooks) |基於 Windows PowerShell 文本的文本執行簿。 |
| [PowerShell 工作流程](#powershell-workflow-runbooks)|基於 Windows PowerShell 工作流腳本的文本執行簿。 |
| [Python](#python-runbooks) |基於 Python 文稿的文字執行簿。 |

## <a name="graphical-runbooks"></a>圖形化 Runbook

您可以使用 Azure 門戶中的圖形編輯器建立和編輯圖形和圖形 PowerShell 工作流 Runbook。 但是,您無法使用其他工具創建或編輯這種類型的 Runbook。

圖形執行簿具有以下主要功能:

* 可以匯出到自動化帳戶中的檔,然後導入到另一個自動化帳戶。 
* 生成 PowerShell 程式碼。 
* 可以在導入期間轉換為圖形 PowerShell 工作流執行簿或從該功能手冊轉換。 

### <a name="advantages"></a>優點

* 使用可視插入連結配置創作模型。
* 重點介紹數據如何流經流程。
* 直觀地表示管理流程。
* 將其他 Runbook 作為子 Runbook,以創建高級工作流。
* 鼓勵模組化程式設計。

### <a name="limitations"></a>限制

* 無法在 Azure 門戶之外創建或編輯。
* 可能需要包含 PowerShell 代碼的程式碼活動來執行複雜的邏輯。
* 不能轉換為其中一種[文字格式](automation-runbook-types.md),也不能將文字運行簿轉換為圖形格式。 
* 不允許查看或直接編輯圖形工作流創建的 PowerShell 程式碼。 您可以查看在任何程式碼活動中建立的代碼。
* 不在 Linux 混合 Runbook 工作線程上運行。 請參考[使用混合 Runbook 的設定的資料中心或雲端的資源](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook 以 Windows PowerShell 為基礎。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。  您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 程式碼實作所有複雜的邏輯，不必處理 PowerShell 工作流程的其他複雜性。
* Runbook 的啟動速度會比 PowerShell 工作流程 Runbook 更快，因為它在執行之前不需要編譯。
* 可以在 Azure 中執行,也可以在 Linux 和 Windows 混合 Runbook 工作簿上執行

### <a name="limitations"></a>限制

* 必須熟悉 PowerShell 指令碼處理。
* 無法使用[平行處理](automation-powershell-workflow.md#parallel-processing)來平行執行多個動作。
* 發生錯誤時，無法使用[檢查點](automation-powershell-workflow.md#checkpoints)來繼續執行 Runbook。
* 只有使用 Start-AzureAutomationRunbook Cmdlet (其會建立新的工作) 才能將 PowerShell 工作流程 Runbook 和圖形化 Runbook 包含為子 Runbook。

### <a name="known-issues"></a>已知問題

以下是 PowerShell Runbook 目前已知的問題。

* PowerShell Runbook 無法檢查索有空值的未加密[變數資產](automation-variables.md)。
* PowerShell Runbook 無法擷取名稱中含有 *~* 的[變數資產](automation-variables.md)。
* PowerShell Runbook 中落入迴圈的 Get-Process 大約在 80 次反覆運算之後就會損毀。
* 如果 PowerShell Runbook 嘗試同時將大量資料寫入輸出資料流，便可能會失敗。   在處理大型物件時，只輸出您所需的資訊，通常就可以解決這個問題。  例如，不要輸出類似 *Get-Process* 之類的資訊，您可以使用 *Get-Process | Select ProcessName, CPU*，只輸出需要的欄位。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流程 Runbook

PowerShell Workflow Runbook 是以 [Windows PowerShell 工作流程](automation-powershell-workflow.md)為基礎的文字 Runbook。  您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。  您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 工作流程程式碼實作所有複雜的邏輯。
* 發生錯誤時，能使用[檢查點](automation-powershell-workflow.md#checkpoints)來繼續執行 Runbook。
* 使用 [平行處理](automation-powershell-workflow.md#parallel-processing) 以平行執行多個動作。
* 可併入其他圖形化 Runbook 和 PowerShell 工作流程 Runbook 成為子 Runbook，以建立高階工作流程。

### <a name="limitations"></a>限制

* 作者必須熟悉 PowerShell 工作流程。
* Runbook 必須處理 PowerShell 工作流程額外的複雜性，例如 [已還原序列化的物件](automation-powershell-workflow.md#code-changes)。
* Runbook 所需的啟動時間比 PowerShell Runbook 更久，因為它在執行之前需要編譯。
* 只能使用 Start-AzureAutomationRunbook Cmdlet (其會建立新的工作) 將 PowerShell Runbook 包含為子 Runbook。
* 無法在 Linux 混合 Runbook 工作線程上運行。

## <a name="python-runbooks"></a>Python Runbook

Python 2 下的 Python Runbook 編譯。 您可以在 Azure 入口網站中使用文字編輯器直接編輯 Runbook 的程式碼，或是搭配離線文字編輯器，然後將 [Runbook 匯入](manage-runbooks.md) Azure 自動化。

### <a name="advantages"></a>優點

* 使用強固的 Python 程式庫。
* 可以在 Azure 中運行,也可以在兩個 Linux 混合 Runbook 工作簿上運行。 安裝了[python2.7](https://www.python.org/downloads/release/latest/python2)支援 Windows 混合 Runbook 工作台。

### <a name="limitations"></a>限制

* 必須熟悉 Python 指令碼處理。
* 目前僅支援 Python 2，這表示使用 Python 3 特定函式將會失敗。
* 要使用第三方庫,必須[將包導入](python-packages.md)自動化帳戶才能使用。

## <a name="considerations"></a>考量

在決定要針對特定 Runbook 使用何種類型時，請考慮下列其他考量。

* 您無法將 Runbook 從圖形化類型轉換為文字類型，反之亦然。
* 使用不同類型的 Runbook 作為子 Runbook 時有所限制。 如需詳細資訊，請參閱 [Azure 自動化中的子 Runbook](automation-child-runbooks.md)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何編寫圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
* 若要了解適用於 Runbook 的 PowerShell 和 PowerShell 工作流程之間的差異，請參閱 [了解 Windows PowerShell 工作流程](automation-powershell-workflow.md)
* 如需如何建立或匯入 Runbook 的詳細資訊，請參閱 [建立或匯入 Runbook](manage-runbooks.md)
* 有關 PowerShell 的更多資訊(包括語言參考和學習模組),請參閱[PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。
