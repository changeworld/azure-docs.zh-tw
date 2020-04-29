---
title: 在本機上對 PowerShell Azure Functions 進行調試
description: 瞭解如何使用 PowerShell 開發函式。
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78163755"
---
# <a name="debug-powershell-azure-functions-locally"></a>在本機上對 PowerShell Azure Functions 進行調試

Azure Functions 可讓您將函式開發為 PowerShell 腳本。

您可以使用下列標準開發工具，在本機對 PowerShell 函式進行程式碼調試：

* [Visual Studio Code](https://code.visualstudio.com/)： Microsoft 的免費、輕量和開放原始碼的文字編輯器，提供 powershell 延伸模組，可提供完整的 powershell 開發體驗。
* PowerShell 主控台：使用與您用來對任何其他 PowerShell 進程進行的相同命令進行的偵錯工具。

[Azure Functions Core Tools](functions-run-local.md)支援 Azure Functions 的本機調試，包括 PowerShell 函數。

## <a name="example-function-app"></a>範例函數應用程式

本文中使用的函式應用程式具有單一的 HTTP 觸發函式，並具有下列檔案：

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

此函式應用程式類似于您在完成[PowerShell 快速入門](functions-create-first-function-powershell.md)時所取得的功能。

中`run.ps1`的函式程式碼看起來會像下面這樣的腳本：

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>設定附加點

若要進行任何 PowerShell 函式的調試，必須停止函式，才能附加偵錯工具。 `Wait-Debugger` Cmdlet 會停止執行，並等候偵錯工具。

您只需要在`Wait-Debugger` `if`語句的正上方新增對 Cmdlet 的呼叫，如下所示：

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

從語句開始的`if`調試。 

`Wait-Debugger`備妥之後，您現在可以使用 Visual Studio Code 或 PowerShell 主控台來進行函式的調試。

## <a name="debug-in-visual-studio-code"></a>Visual Studio Code 中的 Debug

若要在 Visual Studio Code 中進行 PowerShell 函式的偵錯工具，您必須安裝下列各項：

* [適用于 Visual Studio Code 的 PowerShell 擴充功能](/powershell/scripting/components/vscode/using-vscode)
* [適用於 Visual Studio Code 的 Azure Functions 擴充功能](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)

安裝這些相依性之後，請載入現有的 PowerShell 函數專案，或[建立您的第一個 powershell](functions-create-first-function-powershell.md)函式專案。

>[!NOTE]
> 如果您的專案沒有所需的設定檔，系統會提示您加以新增。

### <a name="set-the-powershell-version"></a>設定 PowerShell 版本

PowerShell Core 會與 Windows PowerShell 並存安裝。 將 PowerShell Core 設定為 PowerShell 版本，以搭配適用于 Visual Studio Code 的 PowerShell 擴充功能使用。

1. 按 F1 以顯示命令託盤，然後搜尋`Session`。

1. 選擇 [ **PowerShell：顯示會話] 功能表**。

1. 如果您**目前的會話**不是**powershell core 6**，請選擇 [**切換到： powershell core 6**]。

當您開啟 PowerShell 檔案時，您會在視窗右下角看到以綠色顯示的版本。 選取此文字也會顯示 [會話] 功能表。 若要深入瞭解，請參閱[選擇要搭配擴充功能使用的 PowerShell 版本](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)。

### <a name="start-the-function-app"></a>啟動函數應用程式

確認`Wait-Debugger`是否已在您要附加偵錯工具的函式中設定。  `Wait-Debugger`新增之後，您可以使用 Visual Studio Code 來對函式應用程式進行 debug。

選擇 [ **Debug** ] 窗格，然後 [**附加至 PowerShell 函數**]。

![偵錯工具](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

您也可以按 F5 鍵開始進行調試。

開始調試作業會執行下列工作：

* 會`func extensions install`在終端機中執行，以安裝您的函式應用程式所需的任何 Azure Functions 擴充功能。
* 在`func host start`終端機中執行，以在函式主機中啟動函數應用程式。
* 將 PowerShell 偵錯工具附加至函式執行時間內的 PowerShell 執行時間。

>[!NOTE]
> 您必須確保 PSWorkerInProcConcurrencyUpperBound 設定為1，以確保 Visual Studio Code 中的正確調試過程。 這是預設值。

在執行函數應用程式的情況下，您需要個別的 PowerShell 主控台來呼叫 HTTP 觸發的函式。

在此情況下，PowerShell 主控台就是用戶端。 `Invoke-RestMethod`是用來觸發函數。

在 PowerShell 主控台中，執行下列命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您會發現回應不會立即傳回。 這是因為`Wait-Debugger`已附加偵錯工具，而 PowerShell 執行會儘快進入中斷模式。 這是因為[BreakAll 概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)，稍後會加以說明。 當您按下`continue`按鈕之後，偵錯工具就會立即在行上`Wait-Debugger`中斷。

此時會附加偵錯工具，而且您可以執行所有的一般偵錯工具作業。 如需在 Visual Studio Code 中使用偵錯工具的詳細資訊，請參閱[官方檔](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)。

在您繼續並完全叫用腳本之後，您會注意到：

* 執行的`Invoke-RestMethod` PowerShell 主控台已傳回結果
* Visual Studio Code 中的 PowerShell 整合式主控台正在等候執行腳本

稍後當您叫用相同的函式時，PowerShell 擴充功能中的調試`Wait-Debugger`程式會在之後立即中斷。

## <a name="debugging-in-a-powershell-console"></a>在 PowerShell 主控台中進行調試

>[!NOTE]
> 本節假設您已閱讀[Azure Functions Core Tools](functions-run-local.md)檔，並瞭解如何使用`func host start`命令來啟動函數應用程式。

開啟主控台， `cd`並移至函式應用程式的目錄中，然後執行下列命令：

```sh
func host start
```

當函式應用程式正在執行`Wait-Debugger`且已備妥時，您可以附加至進程。 您需要兩個以上的 PowerShell 主控台。

其中一個主控台會作為用戶端。 在此，您會`Invoke-RestMethod`呼叫來觸發函式。 例如，您可以執行下列命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您會發現它不會傳迴響應，這是的結果`Wait-Debugger`。 PowerShell 運行時現在正在等候附加偵錯工具。 讓我們取得附加的。

在其他 PowerShell 主控台中，執行下列命令：

```powershell
Get-PSHostProcessInfo
```

此 Cmdlet 會傳回看起來類似下列輸出的資料表：

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

請記下`ProcessId`資料表中具有`ProcessName` as `dotnet`的專案。 此程式是您的函數應用程式。

接下來，執行下列程式碼片段：

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

啟動之後，偵錯工具會中斷並顯示類似下列輸出的內容：

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

此時，您會在[PowerShell 偵錯工具](/powershell/module/microsoft.powershell.core/about/about_debuggers)中的中斷點停止。 從這裡，您可以執行所有常見的 debug 作業、不進入、跳到、繼續、結束，以及其他專案。 若要查看主控台提供的一組完整的調試命令，請執行`h`或`?`命令。

您也可以使用`Set-PSBreakpoint` Cmdlet 來設定此層級的中斷點。

當您繼續並完全叫用您的腳本之後，您會注意到：

* 您執行`Invoke-RestMethod`的 PowerShell 主控台現在已傳回結果。
* 您執行`Debug-Runspace`的 PowerShell 主控台正在等候執行腳本。

您可以再次叫用相同的函式`Invoke-RestMethod` （例如使用），然後偵錯工具會在`Wait-Debugger`命令之後中斷。

## <a name="considerations-for-debugging"></a>偵錯工具的考慮

請注意，在偵錯工具代碼時，會發生下列問題。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`可能導致偵錯工具在非預期的位置中斷

PowerShell 擴充功能會`Debug-Runspace`使用，而它接著會依賴 PowerShell `BreakAll`的功能。 這項功能會指示 PowerShell 在執行的第一個命令停止。 此行為可讓您在已調試的運行空間內設定中斷點。

Azure Functions 執行時間會在實際叫用您`run.ps1`的腳本之前執行幾個命令，因此偵錯工具可能會在`Microsoft.Azure.Functions.PowerShellWorker.psm1`或`Microsoft.Azure.Functions.PowerShellWorker.psd1`內中斷。

若發生這種中斷，請`continue`執行`c`或命令以略過此中斷點。 您接著會在預期的中斷點停止。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 PowerShell 開發函式，請參閱[Azure Functions powershell 開發人員指南](functions-reference-powershell.md)。
