---
title: Azure 自動化中的 Runbook 輸出與訊息
description: 介紹如何在 Azure 自動化中從 Runbook 創建和檢索輸出和錯誤訊息。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367086"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure 自動化中的 Runbook 輸出與訊息

大部分 Azure 自動化 Runbook 都有一些輸出形式。 此輸出可以是一條錯誤訊息給使用者或一個複雜物件，用於另一個 Runbook。 Windows PowerShell 提供 [多個資料流](/powershell/module/microsoft.powershell.core/about/about_redirection) 從指令碼或工作流程傳送輸出。 Azure 自動化會以不同方式處理這些資料流中的每一個。 在創建 Runbook 時，應遵循使用流的最佳做法。

下表簡要描述了每個流及其在 Azure 門戶中為已發佈的 Runbook 和[Runbook 測試](automation-testing-runbook.md)期間的行為。 輸出流是用於在 Runbook 之間通信的主流。 其他流被歸類為消息流，旨在向使用者傳達資訊。 

| STREAM | 描述 | 已發行 | 測試 |
|:--- |:--- |:--- |:--- |
| 錯誤 |適用於使用者的錯誤訊息。 與異常不同，Runbook 在預設情況下出現錯誤訊息後繼續。 |寫入作業歷史記錄 |顯示在測試輸出窗格中 |
| 偵錯 |適用於互動式使用者的訊息。 不應在 Runbook 中使用。 |未寫入作業歷史記錄 |未顯示在測試輸出窗格中 |
| 輸出 |要供其他 Runbook 使用的物件。 |寫入作業歷史記錄 |顯示在測試輸出窗格中 |
| 進度 |在 Runbook 中的每個活動之前與之後自動產生記錄。 Runbook 不應嘗試創建自己的進度記錄，因為它們是面向互動式使用者的。 |僅當為 Runbook 啟用進度日誌記錄時，才寫入作業歷史記錄 |未顯示在測試輸出窗格中 |
| 「詳細資訊」 |提供一般或偵錯資訊的訊息。 |僅當為 Runbook 啟用詳細日誌記錄時，才寫入作業歷史記錄 |僅當變數設置為"在 Runbook 中繼續"時`VerbosePreference`，才在測試輸出窗格中顯示 |
| 警告 |適用於使用者的警告訊息。 |寫入作業歷史記錄 |顯示在測試輸出窗格中 |

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="output-stream"></a>輸出資料流

輸出流用於腳本或工作流正確運行時創建的物件的輸出。 Azure 自動化主要使用此流的物件由調用[當前 Runbook](automation-child-runbooks.md)的父 Runbook 使用。 當父項[內聯調用 Runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)時，子級將資料從輸出流返回到父項。 

Runbook 使用輸出流僅在另一個 Runbook 從未調用用戶端時才能將常規資訊傳達給用戶端。 但是，最佳做法是 Runbook 通常應使用[詳細流](#verbose-stream)向使用者傳達常規資訊。

讓 Runbook 使用[寫入輸出](https://technet.microsoft.com/library/hh849921.aspx)將資料寫入輸出流。 或者，您可以將物件放在腳本中自己的行上。

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>處理來自函數的輸出

當 Runbook 函數寫入輸出流時，輸出將傳回 Runbook。 如果 Runbook 將輸出分配給變數，則輸出不會寫入輸出流。 若從函式中寫入至任何其他資料流，將會寫入至 Runbook 的相對應資料流。 請考慮以下示例 PowerShell 工作流運行簿。

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook 作業的輸出流為：

```output
Output inside of function
Output outside of function
```

Runbook 作業的詳細流為：

```output
Verbose outside of function
Verbose inside of function
```

發佈 Runbook 後，在啟動 Runbook 之前，還必須在 Runbook 設置中打開詳細日誌記錄，以獲得詳細流輸出。

### <a name="declaring-output-data-type"></a>宣告輸出資料類型

以下是輸出資料類型的示例：

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>在工作流中聲明輸出資料類型

工作流使用[OutputType 屬性](https://technet.microsoft.com/library/hh847785.aspx)指定其輸出的資料類型。 此屬性在運行時不起作用，但它在設計時提供 Runbook 預期輸出的指示。 隨著 Runbook 工具集的不斷發展，在設計時聲明輸出資料類型的重要性也隨之增加。 因此，最佳做法是在此聲明包含在您創建的任何 Runbook 中。

以下範例 Runbook 會輸出字串物件，並包含其輸出類型的宣告。 如果您的 Runbook 會輸出特定類型的陣列，那麼您仍應指定類型而非陣列類型。

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>在圖形運行簿中聲明輸出資料類型

要在圖形或圖形 PowerShell 工作流運行簿中聲明輸出類型，可以選擇 **"輸入和輸出"** 功能表選項並輸入輸出類型。 建議使用 full .NET 類名稱，使該類型在父 Runbook 引用時易於識別。 使用全名會將類的所有屬性公開到 Runbook 中的 databus，並在屬性用作條件邏輯、日誌記錄和引用作為其他 Runbook 活動的值時增加靈活性。<br> ![Runbook 輸入和輸出選項](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>在"輸入和輸出屬性"窗格中的 **"輸出類型"** 欄位中輸入值後，請確保按一下控制項外部，以便它識別您的條目。

下面的示例顯示了兩個圖形運行簿，用於演示輸入和輸出功能。 應用模組化 Runbook 設計模型，有一個 Runbook 作為身份驗證 Runbook 範本，使用"運行即"帳戶使用 Azure 管理身份驗證。 第二個 Runbook 通常執行核心邏輯以自動執行給定的方案，在這種情況下，將執行身份驗證 Runbook 範本。 它將結果顯示到測試輸出窗格。 在正常情況下，您會讓此 Runbook 針對運用子 Runbook 輸出的資源執行某些工作。

以下是 **AuthenticateTo-Azure** Runbook 的基本邏輯。<br> ![驗證 Runbook 範本範例](media/automation-runbook-output-and-messages/runbook-authentication-template.png)時的行為。

Runbook 包括返回身份驗證設定檔`Microsoft.Azure.Commands.Profile.Models.PSAzureContext`屬性的輸出類型 。<br> ![Runbook 輸出類型範例](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

雖然此 Runbook 非常簡單，但此處有一個配置項需要調用。 最後一個活動執行`Write-Output`Cmdlet，使用`Inputobject`參數的 PowerShell 運算式將設定檔資料寫入變數。 此參數是 需要的`Write-Output`。

此示例中的第二個運行簿名為 **"測試-子輸出類型**"，它僅定義兩個活動。<br> ![範例子輸出類型 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

第一個活動調用**身份驗證到 Azure**運行簿。 第二個活動運行`Write-Verbose`Cmdlet，**資料來源**設置為**活動輸出**。 此外，**欄位路徑**設置為**上下文.訂閱.訂閱名稱**，從**身份驗證到 Azure**運行簿的上下文輸出。<br> ![寫入詳細 Cmdlet 參數資料來源](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

產生的輸出是訂用帳戶的名稱。<br> ![Test-ChildOutputType Runbook 結果](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>訊息資料流

與輸出流不同，消息流將資訊傳達給使用者。 不同類型的資訊有多個消息流，Azure 自動化以不同的方式處理每個流。

### <a name="warning-and-error-streams"></a>警告和錯誤資料流

警告和錯誤流記錄運行簿中出現的問題。 Azure 自動化在執行 Runbook 時將這些流寫入作業歷史記錄。 在測試 Runbook 時，自動化包括 Azure 門戶中的"測試輸出"窗格中的流。 

預設情況下，Runbook 在警告或錯誤後繼續執行。 您可以在創建消息之前讓 Runbook 設置[首選項變數](#preference-variables)，從而指定 Runbook 應暫停警告或錯誤。 例如，要使 Runbook 在錯誤時掛起，就像在異常時那樣，將`ErrorActionPreference`變數設置為 Stop。

使用 [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 或 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) Cmdlet 來建立警告或錯誤訊息。 活動也可以寫入警告和錯誤流。

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>偵錯資料流

Azure 自動化使用互動式使用者的調試消息流。 它不應在 Runbook 中使用。

### <a name="verbose-stream"></a>詳細資訊資料流

詳細消息流支援有關 Runbook 操作的一般資訊。 由於調試流不適用於 Runbook，因此 Runbook 應使用詳細消息來獲取調試資訊。 

預設情況下，出於性能原因，作業歷史記錄不會存儲來自已發佈的 Runbook 的詳細消息。 要存儲詳細郵件，請使用 Azure 門戶 **"配置**"選項卡與 **"日誌詳細記錄"** 設置一起配置已發佈的 Runbook 以記錄詳細消息。 只有在疑難排解或偵錯 Runbook 時才開啟此選項。 在大多數情況下，應保留不記錄詳細記錄的預設設置。

[測試 Runbook](automation-testing-runbook.md)時，即使 Runbook 設為記錄詳細記錄，也不會顯示詳細訊息。 要[在測試 Runbook](automation-testing-runbook.md)時顯示詳細消息，必須將`VerbosePreference`變數設置為"繼續"。 使用該變數集，詳細消息將顯示在 Azure 門戶的"測試"輸出窗格中。

以下代碼使用[Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) Cmdlet 創建詳細消息。

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>進度記錄

可以使用 Azure 門戶的 **"配置"** 選項卡配置運行簿以記錄進度記錄。 預設設置是不記錄記錄，以最大限度地提高性能。 在大多數情況下，應保留預設設置。 只有在疑難排解或偵錯 Runbook 時才開啟此選項。 

如果啟用進度記錄日誌記錄，則 Runbook 在每次活動運行之前和之後將記錄寫入作業歷史記錄。 即使 Runbook 配置為記錄進度記錄，測試 Runbook 也不會顯示進度消息。

>[!NOTE]
>[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) Cmdlet 在 Runbook 中無效，因為此 Cmdlet 是用來和互動式使用者搭配使用的。

## <a name="preference-variables"></a>喜好設定變數

您可以在 Runbook 中設置某些 Windows PowerShell[首選項變數](https://technet.microsoft.com/library/hh847796.aspx)，以控制對發送到不同輸出流的資料的回應。 下表列出了可在 Runbook 中使用的首選項變數及其預設值和有效值。 在 Azure 自動化之外的 Windows PowerShell 中使用首選項變數時，可以使用其他值。

| 變數 | 預設值 | 有效的值 |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Continue<br>SilentlyContinue |

下一個表列出了在 Runbook 中有效的首選項變數值的行為。

| 值 | 行為 |
|:--- |:--- |
| Continue |記錄訊息並繼續執行 Runbook。 |
| SilentlyContinue |繼續執行 Runbook 但不記錄訊息。 此值有忽略訊息的作用。 |
| Stop |記錄訊息並暫停 Runbook。 |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>擷取 Runbook 輸出和訊息

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>在 Azure 門戶中檢索 Runbook 輸出和消息

您可以使用 Runbook 的 **"作業"** 選項卡在 Azure 門戶中查看 Runbook 作業的詳細資訊。 作業摘要除了顯示作業和已發生的任何異常的一般資訊外，還顯示輸入參數和[輸出流](#output-stream)。 作業歷史記錄包括來自輸出流和[警告和錯誤流](#warning-and-error-streams)的消息。 它還包括來自[詳細流](#verbose-stream)的消息和[進度記錄](#progress-records)（如果 Runbook 配置為記錄詳細記錄和進度記錄）。

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>檢索 Windows PowerShell 中的 Runbook 輸出和消息

在 Windows PowerShell 中，您可以使用[獲取-AzAutomationJob輸出](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)Cmdlet 從 Runbook 檢索輸出和消息。 此 Cmdlet 需要作業的 ID，並且具有一`Stream`個參數，該參數名為，用於指定要檢索的流。 您可以為此參數指定"Any"的值，以檢索作業的所有流。

下列範例會啟動 Runbook 範例，然後等候它完成。 運行簿完成執行後，腳本將從作業收集 Runbook 輸出流。

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>檢索圖形運行簿中的 Runbook 輸出和消息

對於圖形 Runbook，輸出和消息的額外日誌記錄以活動級跟蹤的形式提供。 追蹤有兩種等級：基本及詳細。 基本跟蹤顯示 Runbook 中每個活動的開始和結束時間，以及與任何活動重試相關的資訊。 一些示例包括嘗試次數和活動開始時間。 詳細跟蹤包括基本跟蹤功能以及每個活動的輸入和輸出資料的日誌記錄。 

當前活動級跟蹤使用詳細流寫入記錄。 因此，在啟用跟蹤時，必須啟用詳細日誌記錄。 已啟用追蹤的圖形化 Runbook 則不需要記錄進度記錄。 基本追蹤有同樣的效果，且更具參考價值。

![圖形化編寫的工作串流檢視](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

從圖像中可以看到，為圖形 Runbook 啟用詳細日誌記錄和跟蹤可在生產**作業流**視圖中提供更多資訊。 對於使用 Runbook 解決生產問題，這些額外的資訊可能是必不可少的。 

但是，除非需要此資訊來跟蹤 Runbook 的故障排除進度，否則您可能希望作為一般做法關閉跟蹤。 追蹤記錄可能特別多。 使用圖形 Runbook 跟蹤，您可以根據基本或詳細跟蹤的配置，每個活動獲取 2 到 4 條記錄。

**要啟用活動級跟蹤：**

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 **"流程自動化**"部分中選擇**Runbook**以打開 Runbook 清單。
3. 在 Runbook 頁上，從 Runbook 清單中選擇圖形運行簿。
4. 在 [設定]**** 底下，按一下 [記錄和追蹤]****。
5. 在"日誌記錄和跟蹤"頁上，在 **"記錄詳細記錄**"下，按一下"**打開**"以啟用詳細日誌記錄。
6. 在**活動級別跟蹤**下，根據所需的跟蹤級別將跟蹤級別更改為 **"基本**"或 **"詳細**"。<br>

   ![圖形化編寫的 [記錄和追蹤] 頁面](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>檢索 Microsoft Azure 監視器日誌中的 Runbook 輸出和消息

Azure 自動化可以將 Runbook 作業狀態和作業流發送到日誌分析工作區。 Azure 監視器支援允許您執行的日誌：

* 取得您「自動化」作業的相關深入解析。
* 根據 runbook 作業狀態觸發電子郵件或警報，例如，失敗或掛起。
* 跨作業流編寫高級查詢。
* 將「自動化」帳戶之間的作業相互關聯。
* 視覺化作業歷史記錄。

有關配置與 Azure 監視器日誌的集成以收集、關聯和操作作業資料的詳細資訊，請參閱[將作業狀態和作業流從自動化轉發到 Azure 監視器日誌](automation-manage-send-joblogs-log-analytics.md)。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關 Runbook 執行、監視 Runbook 作業和其他技術詳細資訊的詳細資訊，請參閱[跟蹤 Runbook 作業](automation-runbook-execution.md)。
* 要瞭解如何設計和使用子 Runbook，請參閱[Azure 自動化 中的子 Runbook。](automation-child-runbooks.md)
* 有關 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[PowerShell 文檔](/powershell/scripting/overview)。
