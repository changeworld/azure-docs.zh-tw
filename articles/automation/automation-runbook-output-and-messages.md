---
title: Azure 自動化中的 Runbook 輸出與訊息
description: 描述如何在 Azure 自動化中，從 runbook 建立和取出輸出和錯誤訊息。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367086"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure 自動化中的 Runbook 輸出與訊息

大部分 Azure 自動化 Runbook 都有一些輸出形式。 這個輸出可能是使用者的錯誤訊息，或要與另一個 runbook 搭配使用的複雜物件。 Windows PowerShell 提供 [多個資料流](/powershell/module/microsoft.powershell.core/about/about_redirection) 從指令碼或工作流程傳送輸出。 Azure 自動化會以不同方式處理這些資料流中的每一個。 當您建立 runbook 時，您應該遵循使用資料流程的最佳做法。

下表簡短描述每個資料流程及其在 Azure 入口網站中的行為，以用於已發佈的 runbook 和[測試 runbook](automation-testing-runbook.md)。 輸出資料流程是用於 runbook 之間通訊的主要資料流程。 其他資料流程則歸類為訊息資料流程，目的是將資訊傳達給使用者。 

| STREAM | 描述 | 已發行 | 測試 |
|:--- |:--- |:--- |:--- |
| 錯誤 |適用於使用者的錯誤訊息。 不同于例外狀況，runbook 預設會在錯誤訊息之後繼續。 |寫入工作歷程記錄 |顯示在 [測試輸出] 窗格中 |
| 偵錯 |適用於互動式使用者的訊息。 不應在 Runbook 中使用。 |未寫入作業歷程記錄 |未顯示在 [測試輸出] 窗格中 |
| 輸出 |要供其他 Runbook 使用的物件。 |寫入工作歷程記錄 |顯示在 [測試輸出] 窗格中 |
| 進度 |在 Runbook 中的每個活動之前與之後自動產生記錄。 Runbook 不應嘗試建立它自己的進度記錄，因為它們是供互動式使用者使用。 |只有在開啟 runbook 的進度記錄時才會寫入作業歷程記錄 |未顯示在 [測試輸出] 窗格中 |
| 「詳細資訊」 |提供一般或偵錯資訊的訊息。 |只有在開啟 runbook 的詳細資訊記錄時，才會寫入作業歷程記錄 |只有在 `VerbosePreference` 變數已設定為在 runbook 中繼續時，才會顯示在 [測試輸出] 窗格中 |
| 警告 |適用於使用者的警告訊息。 |寫入工作歷程記錄 |顯示在 [測試輸出] 窗格中 |

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="output-stream"></a>輸出資料流

輸出資料流程會用於腳本或工作流程正確執行時所建立之物件的輸出。 Azure 自動化主要會針對呼叫[目前 runbook](automation-child-runbooks.md)的父 runbook，使用此資料流程來取得物件。 當父系[呼叫 runbook 內嵌](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)時，子系會將輸出資料流程的資料傳回父代。 

您的 runbook 只會使用輸出串流，將一般資訊傳達給用戶端，而不會被另一個 runbook 呼叫。 不過，最佳做法是，您的 runbook 通常應該使用[Verbose 串流](#verbose-stream)來傳達一般資訊給使用者。

讓您的 runbook 使用[寫入輸出](https://technet.microsoft.com/library/hh849921.aspx)將資料寫入輸出資料流程。 或者，您也可以在腳本中將物件放在它自己的行上。

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>處理函式的輸出

當 runbook 函式寫入輸出資料流程時，會將輸出傳遞回 runbook。 如果 runbook 將該輸出指派給變數，輸出就不會寫入輸出資料流程。 若從函式中寫入至任何其他資料流，將會寫入至 Runbook 的相對應資料流。 請考慮下列範例 PowerShell 工作流程 runbook。

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

Runbook 工作的輸出資料流程如下：

```output
Output inside of function
Output outside of function
```

Runbook 作業的詳細資訊資料流程如下：

```output
Verbose outside of function
Verbose inside of function
```

在您發佈 runbook 之後，並在啟動之前，您也必須在 runbook 設定中開啟詳細資訊記錄，以取得詳細資訊資料流程輸出。

### <a name="declaring-output-data-type"></a>宣告輸出資料類型

以下是輸出資料類型的範例：

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>在工作流程中宣告輸出資料類型

工作流程會使用[OutputType 屬性](https://technet.microsoft.com/library/hh847785.aspx)來指定其輸出的資料類型。 這個屬性在執行時間沒有任何作用，但它會提供您在設計階段預期的 runbook 輸出的指示。 隨著 runbook 的工具組持續發展，在設計階段宣告輸出資料類型的重要性也會跟著增加。 因此，在您建立的任何 runbook 中包含此宣告是最佳作法。

以下範例 Runbook 會輸出字串物件，並包含其輸出類型的宣告。 如果您的 Runbook 會輸出特定類型的陣列，那麼您仍應指定類型而非陣列類型。

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>在圖形化 runbook 中宣告輸出資料類型

若要在圖形化或圖形化 PowerShell 工作流程 runbook 中宣告輸出類型，您可以選取 [**輸入及輸出**] 功能表選項，然後輸入輸出類型。 建議使用完整的 .NET 類別名稱，讓型別在父 runbook 參考它時容易識別。 使用完整名稱會將類別的所有屬性公開至 runbook 中的 databus，並在屬性用於條件式邏輯、記錄及參考做為其他 runbook 活動的值時增加彈性。<br> ![Runbook 輸入和輸出選項](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>在 [輸入和輸出屬性] 窗格的 [**輸出類型**] 欄位中輸入值之後，請務必按一下控制項外部，使其能夠辨識您的輸入。

下列範例顯示兩個圖形化 runbook 來示範輸入和輸出功能。 套用模組化 runbook 設計模型時，您會有一個 runbook 做為「驗證 Runbook 範本」，以使用「執行身分」帳戶來管理 Azure 的驗證。 第二個 runbook 通常會執行核心邏輯以自動化特定案例，在此情況下，會執行驗證 Runbook 範本。 它會將結果顯示在 [測試輸出] 窗格中。 在正常情況下，您會讓此 Runbook 針對運用子 Runbook 輸出的資源執行某些工作。

以下是 **AuthenticateTo-Azure** Runbook 的基本邏輯。<br> ![驗證 Runbook 範本範例](media/automation-runbook-output-and-messages/runbook-authentication-template.png)時的行為。

Runbook 包含 `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`的輸出類型，它會傳回驗證配置檔案屬性。<br> ![Runbook 輸出類型範例](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

雖然此 runbook 很簡單，但這裡有一個要呼叫的設定專案。 最後一個活動會執行 `Write-Output` Cmdlet，使用 `Inputobject` 參數的 PowerShell 運算式，將設定檔資料寫入變數。 `Write-Output`需要此參數。

這個範例中的第二個 runbook （名為**test-childoutputtype**）只會定義兩個活動。<br> ![範例子輸出類型 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

第一個活動會呼叫**authenticateto-azure-Azure** runbook。 第二個活動會執行 `Write-Verbose` Cmdlet，並將**資料來源**設定為**活動輸出**。 此外，**欄位路徑**會設定為**SubscriptionName**，這是**authenticateto-azure-Azure** runbook 的內容輸出。<br> ![寫入-Verbose Cmdlet 參數資料來源](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

產生的輸出是訂用帳戶的名稱。<br> ![Test-ChildOutputType Runbook 結果](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>訊息資料流

與輸出資料流程不同的是，訊息串流會將資訊傳達給使用者。 有多個訊息資料流程適用于不同種類的資訊，Azure 自動化會以不同的方式處理每個資料流程。

### <a name="warning-and-error-streams"></a>警告和錯誤資料流

警告和錯誤串流會記錄 runbook 中發生的問題。 Azure 自動化會在執行 runbook 時，將這些資料流程寫入作業歷程記錄。 當測試 runbook 時，自動化會在 Azure 入口網站的 [測試輸出] 窗格中包含資料流程。 

根據預設，runbook 會在警告或錯誤之後繼續執行。 您可以在建立訊息之前，讓 runbook 設定[喜好設定變數](#preference-variables)，指定 runbook 應該在警告或錯誤時暫停。 例如，若要在發生例外狀況時暫停 runbook，請將 `ErrorActionPreference` 變數設定為 [停止]。

使用 [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 或 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) Cmdlet 來建立警告或錯誤訊息。 活動也可以寫入警告和錯誤串流。

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>偵錯資料流

Azure 自動化使用互動式使用者的 Debug 訊息資料流程。 不應在 runbook 中使用。

### <a name="verbose-stream"></a>詳細資訊資料流

詳細資訊訊息串流支援 runbook 作業的一般相關資訊。 因為偵錯工具無法用於 runbook，所以您的 runbook 應該使用詳細訊息來取得偵錯工具的資訊。 

根據預設，作業歷程記錄不會儲存來自已發佈 runbook 的詳細資訊訊息，基於效能考慮。 若要儲存詳細資訊訊息，請使用 [Azure 入口網站**設定**] 索引標籤與 [**記錄詳細記錄**] 設定，以設定已發佈的 runbook 來記錄詳細訊息。 只有在疑難排解或偵錯 Runbook 時才開啟此選項。 在大部分情況下，您應該保留預設設定，而不要記錄詳細資訊記錄。

[測試 Runbook](automation-testing-runbook.md)時，即使 Runbook 設為記錄詳細記錄，也不會顯示詳細訊息。 若要在[測試 runbook](automation-testing-runbook.md)時顯示詳細訊息，您必須將 `VerbosePreference` 變數設定為 [繼續]。 設定該變數後，詳細訊息會顯示在 Azure 入口網站的 [測試輸出] 窗格中。

下列程式碼會使用[Write verbose](https://technet.microsoft.com/library/hh849951.aspx) Cmdlet 來建立詳細訊息。

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>進度記錄

您可以使用 Azure 入口網站的 [**設定**] 索引標籤，設定 runbook 來記錄進度記錄。 預設設定為不記錄記錄，以將效能最大化。 在大部分情況下，您應該保留預設設定。 只有在疑難排解或偵錯 Runbook 時才開啟此選項。 

如果您啟用進度記錄記錄，runbook 會在每個活動執行之前和之後，將記錄寫入作業歷程記錄。 即使 runbook 設定為記錄進度記錄，測試 runbook 也不會顯示進度訊息。

>[!NOTE]
>[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) Cmdlet 在 Runbook 中無效，因為此 Cmdlet 是用來和互動式使用者搭配使用的。

## <a name="preference-variables"></a>喜好設定變數

您可以在 runbook 中設定特定的 Windows PowerShell[喜好設定變數](https://technet.microsoft.com/library/hh847796.aspx)，以控制傳送至不同輸出資料流程之資料的回應。 下表列出可在 runbook 中使用的喜好設定變數及其預設值和有效值。 在 Azure 自動化之外的 Windows PowerShell 中使用時，可使用額外的值做為喜好設定變數。

| 變數 | 預設值 | 有效的值 |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Continue<br>SilentlyContinue |

下表列出 runbook 中有效的喜好設定變數值的行為。

| 值 | 行為 |
|:--- |:--- |
| Continue |記錄訊息並繼續執行 Runbook。 |
| SilentlyContinue |繼續執行 Runbook 但不記錄訊息。 此值有忽略訊息的作用。 |
| Stop |記錄訊息並暫停 Runbook。 |

## <a name="runbook-output"></a>擷取 Runbook 輸出和訊息

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>在 Azure 入口網站中取出 runbook 輸出和訊息

您可以使用 runbook 的 **工作** 索引標籤，在 Azure 入口網站中查看 runbook 作業的詳細資料。 除了作業的一般資訊及任何發生的例外狀況以外，[作業摘要] 也會顯示輸入參數和[輸出資料流程](#output-stream)。 作業歷程記錄包含來自輸出資料流程和[警告和錯誤串流](#warning-and-error-streams)的訊息。 如果將 runbook 設定為記錄詳細資訊和進度記錄，它也會包含[詳細資訊串流](#verbose-stream)和[進度記錄](#progress-records)中的訊息。

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>在 Windows PowerShell 中取出 runbook 輸出和訊息

在 Windows PowerShell 中，您可以使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Cmdlet 來取出 runbook 的輸出和訊息。 此 Cmdlet 需要作業的識別碼，而且具有稱為 `Stream` 的參數，用來指定要抓取的資料流程。 您可以為此參數指定 Any 的值，以取得作業的所有資料流程。

下列範例會啟動 Runbook 範例，然後等候它完成。 Runbook 完成執行後，腳本會從作業收集 runbook 輸出資料流程。

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>在圖形化 runbook 中取出 runbook 輸出和訊息

針對圖形化 runbook，會以活動層級追蹤的形式提供輸出和訊息的額外記錄。 追蹤有兩種等級：基本及詳細。 [基本追蹤] 會顯示 runbook 中每個活動的開始和結束時間，以及任何活動重試的相關資訊。 其中一些範例包括嘗試次數和活動的開始時間。 詳細的追蹤包含基本追蹤功能，以及記錄每個活動的輸入和輸出資料。 

目前活動層級的追蹤會使用詳細資訊資料流程來寫入記錄。 因此，當您啟用追蹤時，必須啟用詳細資訊記錄。 已啟用追蹤的圖形化 Runbook 則不需要記錄進度記錄。 基本追蹤有同樣的效果，且更具參考價值。

![圖形化編寫的工作串流檢視](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

您可以從啟用詳細資訊記錄和追蹤的映射中看到圖形化 runbook，在生產作業的 [**資料流程**] 視圖中提供更多的資訊。 此額外資訊對於使用 runbook 的生產環境問題進行疑難排解可能是不可或缺的。 

不過，除非您需要此資訊來追蹤 runbook 的進度以進行疑難排解，否則您可能會想要將追蹤關閉為一般做法。 追蹤記錄可能特別龐大。 透過圖形化 runbook 追蹤，您可以根據您的基本或詳細追蹤設定，取得每個活動兩到四筆記錄。

**若要啟用活動層級追蹤：**

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 選取 [程式**自動化**] 區段中的 [ **runbook** ] 以開啟 runbook 清單。
3. 在 [Runbook] 頁面上，從您的 runbook 清單中選取圖形化 runbook。
4. 在 [設定] 底下，按一下 [記錄和追蹤]。
5. 在 [記錄和追蹤] 頁面的 [記錄**詳細資訊記錄**] 底下，按一下 [**開啟**] 以啟用詳細資訊記錄。
6. 在 [**活動層級追蹤**] 下方，根據您所需的追蹤層級，將追蹤層級變更為 [**基本**] 或 [**詳細**]。<br>

   ![圖形化編寫的 [記錄和追蹤] 頁面](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>在 Microsoft Azure 監視器記錄中取出 runbook 輸出和訊息

Azure 自動化可以將 runbook 作業狀態和作業串流傳送至您的 Log Analytics 工作區。 Azure 監視器支援記錄，可讓您：

* 取得您「自動化」作業的相關深入解析。
* 根據您的 runbook 作業狀態觸發電子郵件或警示，例如 [已失敗] 或 [已暫停]。
* 跨作業資料流程撰寫先進的查詢。
* 將「自動化」帳戶之間的作業相互關聯。
* 將作業歷程記錄視覺化。

如需有關設定與 Azure 監視器記錄整合以收集、相互關聯及處理作業資料的詳細資訊，請參閱[從自動化將作業狀態和作業串流轉送到 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 runbook 執行、監視 runbook 作業和其他技術詳細資料，請參閱[追蹤 runbook 工作](automation-runbook-execution.md)。
* 若要瞭解如何設計和使用子 runbook，請參閱[Azure 自動化中的子 runbook](automation-child-runbooks.md)。
* 如需 PowerShell 的詳細資訊，包括語言參考和學習模組，請參閱[powershell](/powershell/scripting/overview)檔。
