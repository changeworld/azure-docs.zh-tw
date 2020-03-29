---
title: 在本地調試 PowerShell Azure 函數
description: 瞭解如何使用 PowerShell 開發功能。
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163755"
---
# <a name="debug-powershell-azure-functions-locally"></a>在本地調試 PowerShell Azure 函數

Azure 函數允許您將函數開發為 PowerShell 腳本。

您可以使用以下標準開發工具在本地調試 PowerShell 函數，就像使用以下標準開發工具一樣調試任何 PowerShell 腳本：

* [視覺化工作室代碼](https://code.visualstudio.com/)：微軟的免費、輕量和開源文字編輯器，帶有 PowerShell 擴展，提供完整的 PowerShell 開發體驗。
* PowerShell 主控台：使用用於調試任何其他 PowerShell 進程的命令進行調試。

[Azure 函數核心工具](functions-run-local.md)支援 Azure 函數的本地調試，包括 PowerShell 函數。

## <a name="example-function-app"></a>示例函數應用

本文中使用的函數應用具有單個 HTTP 觸發功能，並且具有以下檔：

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

此函數應用程式類似于您完成[PowerShell 快速啟動](functions-create-first-function-powershell.md)時得到的應用程式。

中的`run.ps1`函數代碼如下所示：

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

## <a name="set-the-attach-point"></a>設置附加點

要調試任何 PowerShell 函數，該函數需要停止才能附加調試器。 `Wait-Debugger` Cmdlet 停止執行並等待調試器。

您只需向`Wait-Debugger``if`語句上方的 Cmdlet 添加調用，如下所示：

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

調試從語句`if`開始。 

就`Wait-Debugger`位，您現在可以使用 Visual Studio 代碼或 PowerShell 主控台調試函數。

## <a name="debug-in-visual-studio-code"></a>視覺化工作室代碼中的調試

要在視覺化工作室代碼中調試 PowerShell 功能，您必須安裝以下內容：

* [Visual Studio 代碼的 PowerShell 擴展](/powershell/scripting/components/vscode/using-vscode)
* [適用於 Visual Studio Code 的 Azure Functions 擴充功能](functions-create-first-function-vs-code.md)
* [PowerShell 核心 6.2 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)

安裝這些依賴項後，載入現有的 PowerShell 函數專案，或[創建第一個 PowerShell 函數專案](functions-create-first-function-powershell.md)。

>[!NOTE]
> 如果專案沒有所需的設定檔，系統將提示您添加這些檔。

### <a name="set-the-powershell-version"></a>設置 PowerShell 版本

PowerShell 核心與 Windows PowerShell 並排安裝。 將 PowerShell Core 設置為 PowerShell 版本，以便與 Visual Studio 代碼的 PowerShell 擴展版一起使用。

1. 按 F1 顯示命令託盤，然後搜索`Session`。

1. 選擇**電源外殼：顯示會話功能表**。

1. 如果您的**當前會話**不是**PowerShell 核心 6，** 請選擇 **"切換到：PowerShell 核心 6**"。

打開 PowerShell 檔後，可以看到視窗右下角以綠色顯示的版本。 選擇此文本還會顯示會話功能表。 要瞭解更多資訊，請參閱[選擇要使用的 PowerShell 版本](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)。

### <a name="start-the-function-app"></a>啟動函數應用

驗證`Wait-Debugger`在要附加調試器的函數中設置的。  添加`Wait-Debugger`後，您可以使用 Visual Studio 代碼調試函數應用。

選擇**調試**窗格，然後**附加到 PowerShell 函數**。

![偵錯工具](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

您還可以按 F5 鍵開始調試。

啟動調試操作執行以下任務：

* 在`func extensions install`終端中運行以安裝函數應用所需的任何 Azure 函數擴展。
* 在`func host start`終端中運行以在函數主機中啟動函數應用。
* 將 PowerShell 調試器附加到函數運行時內的 PowerShell 運行空間。

>[!NOTE]
> 您需要確保 PSWorkerInProcConininSToBound 設置為 1，以確保在視覺化工作室代碼中獲得正確的調試體驗。 這是預設值。

運行函數應用後，您需要單獨的 PowerShell 主控台來調用 HTTP 觸發功能。

在這種情況下，PowerShell 主控台是用戶端。 `Invoke-RestMethod`用於觸發函數。

在 PowerShell 主控台中，運行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您會注意到不會立即返回回應。 這是因為`Wait-Debugger`已經附加了調試器，PowerShell 執行盡可能快地進入中斷模式。 這是因為[BreakAll的概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)，這是稍後解釋。 按下該`continue`按鈕後，調試器現在在 之後的`Wait-Debugger`行上中斷。

此時，調試器已連接，您可以執行所有正常的調試器操作。 有關在 Visual Studio 代碼中使用調試器的詳細資訊，請參閱[官方文檔](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)。

繼續並完全調用腳本後，您會注意到：

* 已返回`Invoke-RestMethod`結果的 PowerShell 主控台
* 視覺化工作室代碼中的 PowerShell 集成主控台正在等待腳本執行

稍後，當您調用同一函數時，PowerShell 擴展中的調試器將在`Wait-Debugger`之後中斷。

## <a name="debugging-in-a-powershell-console"></a>在 PowerShell 主控台中調試

>[!NOTE]
> 本節假定您已閱讀[Azure 函數核心工具文檔](functions-run-local.md)，並知道如何使用 命令`func host start`啟動函數應用。

打開主控台，`cd`進入函數應用的目錄中，並運行以下命令：

```sh
func host start
```

在函數應用運行並`Wait-Debugger`到位後，您可以附加到進程。 您確實需要兩個 PowerShell 主控台。

其中一個主控台充當用戶端。 從中，您調用`Invoke-RestMethod`以觸發函數。 例如，您可以運行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您會注意到它不會返回回應，這是 的結果`Wait-Debugger`。 PowerShell 運行空間正在等待連接調試器。 讓我們附上它。

在其他 PowerShell 主控台中，運行以下命令：

```powershell
Get-PSHostProcessInfo
```

此 Cmdlet 返回類似于以下輸出的表：

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

記下`ProcessId`表中的項的`ProcessName`。 `dotnet` 此過程是您的函數應用。

接下來，運行以下程式碼片段：

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

啟動後，調試器將中斷並顯示類似以下輸出的內容：

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

此時，您將在[PowerShell 調試器](/powershell/module/microsoft.powershell.core/about/about_debuggers)中的中斷點處停止。 從這裡，您可以執行所有常見的調試操作、單一步驟、單一步驟、繼續、退出等操作。 要查看主控台中可用的完整調試命令集，請運行 。 `h` `?`

您還可以在此級別使用`Set-PSBreakpoint`Cmdlet 設置中斷點。

繼續並完全調用腳本後，您會注意到：

* 執行的 PowerShell 主控台`Invoke-RestMethod`現已返回結果。
* 執行的 PowerShell 主控台`Debug-Runspace`正在等待腳本執行。

您可以再次調用相同的函數（例如使用`Invoke-RestMethod`），調試器在`Wait-Debugger`命令後就會中斷。

## <a name="considerations-for-debugging"></a>調試注意事項

調試函數代碼時，請記住以下問題。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`可能導致調試器在意外位置中斷

PowerShell 擴展使用`Debug-Runspace`，這反過來又依賴于 PowerShell 的功能`BreakAll`。 此功能告訴 PowerShell 停止執行的第一個命令。 此行為使您能夠在調試的運行空間中設置中斷點。

Azure 函數運行時在實際調用`run.ps1`腳本之前運行一些命令，因此調試器最終可能在`Microsoft.Azure.Functions.PowerShellWorker.psm1`或`Microsoft.Azure.Functions.PowerShellWorker.psd1`中分解。

如果發生此中斷，請運行`continue``c`或 命令跳過此中斷點。 然後，在預期的中斷點停止。

## <a name="next-steps"></a>後續步驟

要瞭解有關使用 PowerShell 開發函數的更多內容，請參閱[Azure 函數 PowerShell 開發人員指南](functions-reference-powershell.md)。
