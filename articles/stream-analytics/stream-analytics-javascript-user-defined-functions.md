---
title: Azure 串流分析 JavaScript 使用者定義函式
description: 本文會簡介串流分析中的 JavaScript 使用者定義函式。
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235406"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Azure 串流分析中的 JavaScript 使用者定義函式
 
Azure 串流分析支援以 JavaScript 撰寫的使用者定義函式。 JavaScript 提供豐富的 **String**、**RegExp**、**Math**、**Array** 和 **Date** 方法，可讓使用串流分析作業建立複雜的資料轉換變得更容易。

## <a name="overview"></a>概觀

JavaScript 使用者定義的函式支援無狀態且只做為計算用途的純量函式，而且不需要外部連線能力。 函數的傳回值只能是純量 (單一) 值。 將 JavaScript 使用者定義函式新增至作業之後，您可以在查詢中的任何位置使用函式，就像是內建的純量函式。

從以下的一些案例可以看出 JavaScript 使用者定義函式很實用：
* 剖析及操作具有規則運算式函式的字串，例如：**Regexp_Replace()** 和 **Regexp_Extract()**
* 解碼和編碼資料，例如：從二進位轉換成十六進位
* 使用 JavaScript **Math** 函式進行數學運算
* 進行陣列作業，例如，排序、連結、尋找及填入

以下是一些在串流分析中無法使用 JavaScript 使用者定義函式達成的事項：
* 呼叫外部 REST 端點，例如，進行反向 IP 查詢或從外部來源提取參考資料
* 對輸入/輸出執行自訂事件格式序列化或還原序列化
* 建立自訂彙總

雖然沒有在函式定義中封鎖 **Date.GetDate()** 或 **Math.random()** 等函式，您仍應避免使用這些函式。 這些函式**不會**在每次呼叫時都傳回同樣的結果，且「串流分析」服務不會記錄函式叫用和傳回值的日誌。 若函式針對同樣事件傳回不同的值，則當您或串流分析重新啟動某項作業之後，將不保證其具有可重覆性。

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>將 JavaScript 使用者定義函式新增至作業

> [!NOTE]
> 下列步驟適用於設定為在雲端執行的 Stream Analytics 作業。 如果您的串流分析作業設定為在 Azure IoT Edge 上執行，請改為使用 Visual Studio 並[使用C# 撰寫使用者定義的函式](stream-analytics-edge-csharp-udf.md)。

若要在串流分析作業中建立 JavaScript 使用者定義的函式，請選取 [作業拓撲]  底下的 [函式]  。 然後，從 [+新增]  下拉式功能表中選取 [JavaScript UDF]  。 

![新增 JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

您接著必須提供下列屬性，然後選取 [儲存]  。

|屬性|描述|
|--------|-----------|
|函式別名|輸入名稱以在查詢中叫用函式。|
|輸出類型|會由 JavaScript 使用者定義函式傳回給串流分析查詢的類型。|
|函式定義|可實作會在每次從查詢中叫用 UDF 時執行的 JavaScript 函式。|

## <a name="test-and-troubleshoot-javascript-udfs"></a>針對 JavaScript UDF 進行測試和疑難排解 

您可以在任何瀏覽器中測試和偵錯 JavaScript UDF 邏輯。 串流分析入口網站目前不支援對這類使用者定義函數的邏輯進行偵錯和測試。 函數如預期般運作後，即可將函數新增至串流分析工作 (如上所述)，然後直接從查詢加以叫用。 您可以使用[適用於 Visual Studio 的串流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)來以 JavaScript UDF 測試查詢邏輯。

JavaScript 執行階段錯誤會被視為嚴重問題，並顯示在活動記錄。 若要擷取記錄檔，在 Azure 入口網站中，請移至您的作業並選取 [活動記錄]  。

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>在查詢中呼叫 JavaScript 使用者定義函式

在查詢中使用前面加上 **udf** 的函式別名，即可輕易地叫用 JavaScript 函式。 以下舉例說明一個會在串流分析查詢中叫用的 JavaScript UDF，其會將十六進位值轉換為整數。

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>支援的 JavaScript 物件

Azure 串流分析 JavaScript 使用者定義函式支援標準的內建 JavaScript 物件。 如需這些物件的清單，請參閱[全域物件](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)。

### <a name="stream-analytics-and-javascript-type-conversion"></a>串流分析與 JavaScript 類型轉換

串流分析查詢語言與 JavaScript 支援的類型有一些差異。 此表列出兩者之間的轉換對應：

串流分析 | JavaScript
--- | ---
BIGINT | Number (JavaScript 只能準確地表示最高到 2^53 的整數)
Datetime | Date (JavaScript 只支援毫秒)
double | Number
nvarchar(MAX) | String
Record | Object
Array | Array
NULL | Null

以下是 JavaScript 至串流分析的轉換：

JavaScript | 串流分析
--- | ---
Number | Bigint (若數字為整數且介於 long.MinValue 和 long.MaxValue 之間，否則為 double)
Date | Datetime
String | nvarchar(MAX)
Object | Record
Array | Array
Null、Undefined | NULL
任何其他類型 (例如，函式或錯誤) | 不支援 (產生執行階段錯誤)

JavaScript 語言需區分大小寫，且 JavaScript 程式碼中物件欄位的大小寫必須符合內送資料中欄位的大小寫。 相容性層級 1.0 的作業會將 SQL SELECT 陳述式欄位轉換成小寫。 若是相容性層級 1.1 和以上，SELECT 陳述式的欄位會與 SQL 查詢中所指定的大小寫相同。

## <a name="other-javascript-user-defined-function-patterns"></a>其他 JavaScript 使用者定義函式模式

### <a name="write-nested-json-to-output"></a>將巢狀 JSON 寫入至輸出

如果您的後續處理步驟使用串流分析作業輸出做為輸入，且其需要 JSON 格式，您可以將 JSON 字串寫入至輸出。 以下範例會呼叫 **JSON.stringify()** 函式以包裝所有輸入的名稱/值對，並將它們以單一字串值於輸出中寫入。

**JavaScript 使用者定義函式定義：**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**範例查詢︰**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>後續步驟

* [Machine Learning UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
