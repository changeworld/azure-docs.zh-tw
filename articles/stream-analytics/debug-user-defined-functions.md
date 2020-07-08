---
title: 在 Azure 串流分析中的 Debug 使用者定義函數
description: 本文說明如何在 Azure 串流分析中，對使用者定義函數進行調試。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: a6c2e390af39b496a871ae9b5799288ea3609bca
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85981498"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>在 Azure 串流分析中的 Debug 使用者定義函數 

當使用者定義函式（UDF）未如預期般運作時，您需要進行偵錯工具來找出問題。 當您使用[Visual Studio Code](visual-studio-code-local-run-live-input.md)或[Visual Studio](stream-analytics-vs-tools-local-run.md)在本機執行您的作業時，可以針對串流分析作業來進行 udf 的偵錯工具。

當您在本機對即時輸入資料流程執行作業時，它只會模擬在一個節點上執行雲端 Azure 串流分析引擎。 即時資料本機測試無法取代您在雲端中執行的效能和擴充性測試，但您可以在功能測試期間節省時間，而不必在每次想要測試時將作業提交至雲端。 此外，時間原則會停用本機或範例資料的本機執行，但支援即時資料測試的時間原則。

## <a name="pick-your-language"></a>選擇您的語言

您可以使用 .NET （c #）或 JavaScript 撰寫適用于 Azure 串流分析的 Udf。 

### <a name="functions-in-c"></a>C 中的函式# 

當您[使用 Visual Studio 撰寫 .Net udf](stream-analytics-edge-csharp-udf-methods.md)時，會取得與任何 .net 類別專案相同的支援層級。 這項支援包括：

* 編譯支援，例如語法檢查和編譯器支援。

* 在您的串流分析方案中新增、建立及參考 c # 專案和成品的能力。 

* 輕鬆重複使用封裝在可共用專案中的程式碼。 

* Visual Studio 中的調試直接支援。 將串流分析專案設定為 [起始專案]，並在 c # 程式碼中設定中斷點。 然後，按**F5**來對 c # 程式碼進行 debug，如同其他任何 c # 專案一樣。 

### <a name="functions-in-javascript"></a>JavaScript 中的函式

JavaScript 是在串流分析中建立函數的另一個選項。 JavaScript 程式碼會直接放入串流分析專案的函式區域中，讓跨專案的共用變得更棘手。

編譯或執行串流分析專案時，就會進行編譯。 只有在執行時間才發現問題的機會較高。 在串流分析中，不會直接對 JavaScript 函式進行任何偵錯工具支援。

## <a name="debug-options-for-javascript"></a>JavaScript 的偵錯工具選項

由於不會直接在串流分析中對 JavaScript 函式進行任何偵錯工具支援，因此您可以在 HTML 網站中封裝函式，並從該處取得輸出，藉以進行 debug。

下列範例示範如何在[Visual Studio Code](quick-create-vs-code.md)的整合執行時間環境中，以一些限制來調試 JavaScript udf。

### <a name="prerequisites"></a>必要條件

開始之前，請確定您的 Azure 串流分析專案具有下列專案：

* 輸入 
* 輸出 
* 串流分析查詢（. script.asaql） 
* 串流分析作業設定（JobConfig.js于）
* JavaScript UDF

### <a name="prepare-files"></a>準備檔案

在下圖中， *script.asaql*查詢檔案只包含對 UDF *fxcharCount*的呼叫。 這種變更可確保您在進行變更之後，仍然可以編譯專案。

> [!div class="mx-imgBorder"]
> ![串流分析測試查詢檔案](./media/debug-user-defined-functions/asaql-file.png)

在**測試**中建立額外的資料夾來裝載測試檔案，該檔案會呼叫以使用 JavaScript 函數來執行測試。 在此範例中，資料夾的名稱是*fxcharCount* ，而測試的名稱則是*Test_UDF.js*。 

下圖顯示測試檔案中的程式碼，它會載入函式檔案並執行函數。 這個範例很簡單，但您可以透過其他測試來取得輸出，以載入額外的測試資料檔和迴圈。 函式呼叫的標記法與常見的呼叫不同，因為會參考並不會將檔案載入執行時間，因此可以進行 debug。 

> [!div class="mx-imgBorder"]
> ![串流分析測試檔案](./media/debug-user-defined-functions/test-file.png)

在函式中，將下列幾行程式碼新增至檔案，以公開方法。 它們不會影響在 Visual Studio Code 中編譯器代碼的能力。

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![串流分析 JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>安裝 debug 支援

若要進行 debug，您必須[下載](https://nodejs.org/en/download/)並安裝**node.js**。 根據您所使用的平臺，安裝正確的版本。 在您安裝 node.js 執行時間之後，請重新開機 Visual Studio Code 以執行變更。 

選取 [**執行和調試]，** 或按**CTRL + SHIFT + D**開始進行調試。 下拉式方塊隨即出現，您可以在其中選取**node.js**做為執行時間。 如果您只有安裝 node.js，預設會使用它。 您應該能夠逐步執行程式碼，並在必要時使用 F11 來進入附屬檔案。 

> [!div class="mx-imgBorder"]
> ![串流分析執行和調試 udf](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Debug 使用者定義匯總 

您可以使用 JavaScript Udf 的 debug 方法來進行使用者定義匯總（UDA）的偵錯工具。 在此範例中，會將 UDA 加入至*script.asaql*查詢檔案和測試檔案。

如同 UDF，您會加入 UDA 的呼叫，以確保專案會在進行變更之後進行編譯。 

> [!div class="mx-imgBorder"]
> ![將 uda 新增至 script.asaql](./media/debug-user-defined-functions/asaql-uda.png)

在*Test_UDA.js*檔案中，您可以參考 UDA 檔案，就像使用 UDF 一樣。 此外，您可以呼叫 `main()` 、 `init()` 和 `accumulate()` 。 在 `accumulate()` 迴圈中呼叫方法，以將值放入狀態堆疊。 `computeresult()`呼叫方法來撰寫最後的查詢。 

> [!div class="mx-imgBorder"]
> ![UDA 測試檔案](./media/debug-user-defined-functions/uda-test.png)

如同在 UDF 範例中，有些程式碼必須加入至 UDA 本身，以公開相關的方法。

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![加入 UDA 的程式碼](./media/debug-user-defined-functions/uda-expose-methods.png)

選取 [**執行和調試]，** 或按**CTRL + SHIFT + D**開始進行調試。 下拉式方塊隨即出現，您可以在其中選取**node.js**做為執行時間。 如果您只有安裝 node.js，預設會使用它。 您應該能夠逐步執行程式碼，並在必要時使用 F11 來進入附屬檔案。

> [!div class="mx-imgBorder"]
> ![串流分析執行和 debug uda](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>後續步驟

* [在本機開發和調試 Azure 串流分析作業](develop-locally.md)
* [使用 Visual Studio 中的工作圖表在本機上進行 Azure 串流分析查詢的調試](debug-locally-using-job-diagram.md)
* [Azure 串流分析中的使用者定義函數](functions-overview.md)
 
