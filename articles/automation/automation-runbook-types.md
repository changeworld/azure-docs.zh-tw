---
title: Azure 自動化 Runbook 類型
description: 說明您可以在 Azure 自動化中使用的不同 runbook 類型，以及決定要使用哪種類型的考慮。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535514"
---
# <a name="azure-automation-runbook-types"></a>Azure 自動化 Runbook 類型

Azure 自動化流程自動化服務支援數種類型的 runbook，如下表所定義。 若要瞭解進程自動化環境，請參閱[Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。

| 類型 | 描述 |
|:--- |:--- |
| [圖形化](#graphical-runbooks)|以 Windows PowerShell 為基礎的圖形化 runbook，並完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [圖形化 PowerShell 工作流程](#graphical-runbooks)|以 Windows PowerShell 工作流程為基礎的圖形化 runbook，並完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [PowerShell](#powershell-runbooks) |以 Windows PowerShell 腳本為基礎的文字 runbook。 |
| [PowerShell 工作流程](#powershell-workflow-runbooks)|以 Windows PowerShell 工作流程腳本為基礎的文字 runbook。 |
| [Python](#python-runbooks) |以 Python 腳本為基礎的文字 runbook。 |

決定要用於特定 runbook 的類型時，請考慮下列事項。

* 您無法將 runbook 從圖形化轉換成文字類型，或另一種方式。
* 使用不同類型的 runbook 做為子 runbook 時，有一些限制。 如需詳細資訊，請參閱 [Azure 自動化中的子 Runbook](automation-child-runbooks.md)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="graphical-runbooks"></a>圖形化 Runbook

您可以使用 Azure 入口網站中的圖形化編輯器，建立和編輯圖形化和圖形化 PowerShell 工作流程 Runbook。 不過，您無法使用另一個工具來建立或編輯這種類型的 runbook。 圖形化 runbook 的主要功能：

* 可以匯出至您的自動化帳戶中的檔案，然後匯入至另一個自動化帳戶。 
* 產生 PowerShell 程式碼。 
* 可以在匯入期間，轉換成圖形化 PowerShell 工作流程 runbook 或從其轉換。 

### <a name="advantages"></a>優點

* 使用視覺效果插入連結-設定撰寫模型。
* 將焦點放在資料如何流經整個過程。
* 以視覺方式呈現管理程序。
* 包含其他 runbook 做為子 runbook，以建立高階工作流程。
* 鼓勵模組化程式設計。

### <a name="limitations"></a>限制

* 無法在 Azure 入口網站外部建立或編輯。
* 可能需要包含 PowerShell 程式碼的程式碼活動，才能執行複雜的邏輯。
* 無法轉換成其中一種[文字格式](automation-runbook-types.md)，您也無法將文字 runbook 轉換成圖形格式。 
* 無法查看或直接編輯圖形化工作流程所建立的 PowerShell 程式碼。 您可以在任何程式碼活動中，看到您建立的程式碼。
* 無法在 Linux 混合式 Runbook 背景工作角色上執行 runbook。 請參閱[使用混合式 Runbook 背景工作角色將資料中心或雲端中的資源自動化](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook 以 Windows PowerShell 為基礎。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。  您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 程式碼實作所有複雜的邏輯，不必處理 PowerShell 工作流程的其他複雜性。
* 啟動的速度比 PowerShell 工作流程 runbook 更快，因為它們不需要在執行之前先行編譯。
* 在 Azure 和混合式 Runbook 背景工作角色的 Windows 和 Linux 上執行。

### <a name="limitations"></a>限制

* 您必須熟悉 PowerShell 腳本。
* Runbook 無法使用[平行處理](automation-powershell-workflow.md#parallel-processing)來平行執行多個動作。
* 如果發生錯誤，runbook 就無法使用[檢查點](automation-powershell-workflow.md#checkpoints)繼續執行 runbook。
* 您可以使用[AzAutomationRunbook 指令程式](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)（建立新的工作），僅包含 PowerShell 工作流程 runbook 和圖形化 runbook 做為子 runbook。

### <a name="known-issues"></a>已知問題

以下是 PowerShell runbook 目前的已知問題：

* PowerShell runbook 無法取得具有 null 值的未加密[變數資產](automation-variables.md)。
* PowerShell Runbook 無法擷取名稱中含有  的`*~*`變數資產。
* 在 PowerShell runbook 中，迴圈中的「[取得進程](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7)」作業可能會在大約80次反覆運算後損毀。
* 如果 PowerShell runbook 嘗試同時將大量資料寫入輸出資料流程，可能會失敗。 您通常可以藉由讓 runbook 只輸出處理大型物件所需的資訊，來解決此問題。 例如，您可以讓 Cmdlet `Get-Process`只輸出所需的參數，如同中`Get-Process | Select ProcessName, CPU`的，而不使用沒有任何限制的。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流程 Runbook

PowerShell 工作流程 runbook 是以[Windows PowerShell 工作流程](automation-powershell-workflow.md)為基礎的文字 runbook。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。 您也可以使用任何離線文字編輯器，並 [匯入 Runbook](manage-runbooks.md) 到 Azure 自動化。

### <a name="advantages"></a>優點

* 使用 PowerShell 工作流程程式碼實作所有複雜的邏輯。
* 如果發生錯誤，請使用[檢查點](automation-powershell-workflow.md#checkpoints)繼續操作。
* 使用[平行處理](automation-powershell-workflow.md#parallel-processing)來平行執行多個動作。
* 可以包含其他圖形化 runbook 和 PowerShell 工作流程 runbook 做為子 runbook，以建立高階工作流程。

### <a name="limitations"></a>限制

* 您必須熟悉 PowerShell 工作流程。
* Runbook 必須處理 PowerShell 工作流程的額外複雜性，例如已還原序列化的[物件](automation-powershell-workflow.md#code-changes)。
* Runbook 需要比 PowerShell runbook 更長的啟動時間，因為它們必須在執行之前先進行編譯。
* 您只能使用`Start-AzAutomationRunbook` Cmdlet，將 PowerShell runbook 納入為子 runbook。
* Runbook 無法在 Linux 混合式 Runbook 背景工作角色上執行。

## <a name="python-runbooks"></a>Python Runbook

Python 2 下的 Python Runbook 編譯。 您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。 您也可以使用離線文字編輯器，並將[runbook 匯入](manage-runbooks.md)Azure 自動化。

### <a name="advantages"></a>優點

* 使用健全的 Python 程式庫。
* 可以在 Azure 或 Linux 混合式 Runbook 背景工作角色上執行。 已安裝[python 2.7](https://www.python.org/downloads/release/latest/python2)支援 Windows 混合式 Runbook 背景工作角色。

### <a name="limitations"></a>限制

* 您必須熟悉 Python 腳本。
* 目前僅支援 Python 2。 任何 Python 3 特定功能都會失敗。
* 若要使用協力廠商程式庫，您必須將[套件匯入](python-packages.md)至自動化帳戶。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解圖形化 runbook 製作，請參閱[Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
* 若要瞭解適用于 runbook 的 PowerShell 和 PowerShell 工作流程之間的差異，請參閱[學習 Windows PowerShell 工作流程](automation-powershell-workflow.md)。
* 如需如何建立或匯入 runbook 的詳細資訊，請參閱[在 Azure 自動化中管理 runbook](manage-runbooks.md)。
* 如需 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[powershell](https://docs.microsoft.com/powershell/scripting/overview)檔。
