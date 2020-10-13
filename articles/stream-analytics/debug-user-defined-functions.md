---
title: 在 Azure 串流分析中進行使用者自訂函式的調試
description: 本文說明如何在 Azure 串流分析中進行使用者自訂函式的調試。
author: jenssuessmeyer
ms.author: jenss
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 567def7abaff3cd8d70eb56f0aa117d1eeb52a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91300423"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>在 Azure 串流分析中進行使用者自訂函式的調試 

當使用者定義的函式 (UDF) 無法如預期般運作時，您需要將它們進行調試以找出問題。 當您使用 [Visual Studio Code](visual-studio-code-local-run-live-input.md) 或 [Visual Studio](stream-analytics-vs-tools-local-run.md)在本機執行您的作業時，您可以針對串流分析作業進行 udf 的偵錯工具。

當您在本機針對即時輸入串流執行作業時，它只會模擬在一個節點上執行雲端 Azure 串流分析引擎的作業。 即時資料本機測試無法取代您在雲端中執行的效能和擴充性測試，但您不需要在每次想要進行測試時，將工作提交至雲端，即可節省功能性測試期間的時間。 此外，使用本機或範例資料來停用本機執行的時間原則，但即時資料測試支援時間原則。

## <a name="pick-your-language"></a>選擇您的語言

您可以使用 .NET (c # ) 或 JavaScript 撰寫適用于 Azure 串流分析的 Udf。 

### <a name="functions-in-c"></a>C 中的函式# 

當您 [使用 Visual Studio 撰寫 .Net udf](stream-analytics-edge-csharp-udf-methods.md)時，會取得與任何 .net 類別專案相同的支援層級。 這項支援包括：

* 編譯支援，例如語法檢查和編譯器支援。

* 在您的串流分析解決方案中加入、建立及參考 c # 專案和成品的能力。 

* 輕鬆重複使用可共用專案中封裝的程式碼。 

* 直接 Visual Studio 中的 Debug 支援。 將串流分析專案設定為啟動專案，並在 c # 程式碼中設定中斷點。 然後，按 **F5** 來將 c # 程式碼進行偵錯工具，就像您針對任何其他 c # 專案一樣。 

### <a name="functions-in-javascript"></a>JavaScript 中的函數

JavaScript 是在串流分析中建立函式的另一個選項。 JavaScript 程式碼會直接放入串流分析專案的函式區域中，讓跨專案共用變得更困難。

編譯或執行串流分析專案時，就會進行編譯。 只有在執行時間才發現問題的機率較高。 對串流分析中的 JavaScript 函式，不會有直接的偵錯工具支援。

## <a name="debug-options-for-javascript"></a>JavaScript 的偵錯工具選項

因為對串流分析中的 JavaScript 函式沒有任何偵錯工具支援，所以您可以在 HTML 網站中封裝函式，並從該處取得輸出，來進行 debug。

下列範例示範如何在 [Visual Studio Code](quick-create-visual-studio-code.md)中的整合執行時間環境中，以一些限制來偵測 JavaScript udf。

### <a name="prerequisites"></a>必要條件

開始之前，請確定您的 Azure 串流分析專案具有下列專案：

* 輸入 
* 輸出 
* 串流分析查詢 ( >script.asaql)  
* ) 上的串流分析作業設定 ( # B0
* JavaScript UDF

### <a name="prepare-files"></a>準備檔案

在下圖中， *>script.asaql* 查詢檔案僅包含 UDF *fxcharCount*的呼叫。 這種變更可確保您在進行變更之後，仍然可以編譯專案。

> [!div class="mx-imgBorder"]
> ![串流分析測試查詢檔案](./media/debug-user-defined-functions/asaql-file.png)

在 **測試** 中建立額外的資料夾來裝載測試檔案，其會呼叫以執行 JavaScript 函數的測試。 在此範例中，資料夾的名稱是 *fxcharCount* ，而測試的名稱是 *Test_UDF.js*。 

下圖顯示測試檔案中的程式碼，此程式碼會載入函式檔案並執行函式。 這個範例很簡單，但您可以載入其他測試資料檔案，並透過其他測試來取得輸出，以執行迴圈。 函式呼叫的標記法與一般呼叫稍有不同，因為該檔案會被參考，而不會載入執行時間，因此可以進行 debug。 

> [!div class="mx-imgBorder"]
> ![串流分析測試檔案](./media/debug-user-defined-functions/test-file.png)

在函式中，將下列程式程式碼新增至檔案，以公開方法。 它們不會影響在 Visual Studio Code 中編譯器代碼的能力。

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![串流分析 JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>安裝 debug 支援

您必須 [下載](https://nodejs.org/en/download/) 並安裝 **node.js**，才能進行 debug 錯。 根據您所使用的平臺安裝正確的版本。 安裝 node.js 執行時間之後，請重新開機 Visual Studio Code 以執行變更。 

選取 [ **執行] 和 [執行]，** 或按 **CTRL + SHIFT + D** 開始調試。 下拉式方塊隨即出現，您可以在其中選取做為執行時間的 **node.js** 。 如果您只安裝了 node.js，預設會使用它。 如有需要，您應該能夠逐步執行程式碼，並在必要時使用 F11。 

> [!div class="mx-imgBorder"]
> ![串流分析執行和 debug udf](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>調試使用者定義匯總 

您可以使用 JavaScript Udf 的 debug 方法，來將使用者定義匯總 (UDA) 。 在此範例中，UDA 會新增至 *>script.asaql* 查詢檔和測試檔案。

如同 UDF，您會加入 UDA 的呼叫，以確保專案會在變更之後進行編譯。 

> [!div class="mx-imgBorder"]
> ![將 uda 新增至 >script.asaql](./media/debug-user-defined-functions/asaql-uda.png)

在 *Test_UDA.js* 檔案中，您會參考 UDA 檔案，就像使用 UDF 一樣。 此外，您可以呼叫 `main()` 、 `init()` 和 `accumulate()` 。 `accumulate()`方法會在迴圈中呼叫，以將值放入狀態堆疊。 `computeresult()`呼叫方法來撰寫最終的查詢。 

> [!div class="mx-imgBorder"]
> ![UDA 測試檔案](./media/debug-user-defined-functions/uda-test.png)

如同 UDF 範例，某些程式碼必須加入至 UDA 本身，才能公開相關的方法。

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![新增至 UDA 的程式碼](./media/debug-user-defined-functions/uda-expose-methods.png)

選取 [ **執行] 和 [執行]，** 或按 **CTRL + SHIFT + D** 開始調試。 下拉式方塊隨即出現，您可以在其中選取做為執行時間的 **node.js** 。 如果您只安裝了 node.js，預設會使用它。 如有需要，您應該能夠逐步執行程式碼，並在必要時使用 F11。

> [!div class="mx-imgBorder"]
> ![串流分析執行和 debug uda](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>後續步驟

* [在本機開發和調試 Azure 串流分析作業](develop-locally.md)
* [使用 Visual Studio 中的工作圖表在本機進行 Azure 串流分析查詢的調試](debug-locally-using-job-diagram.md)
* [Azure 串流分析中的使用者定義函數](functions-overview.md)
 
