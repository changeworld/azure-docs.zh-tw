---
title: Azure Functions 中的觸發程序和繫結
description: 瞭解如何使用觸發程式和系結，將您的 Azure 函式連線至線上事件和雲端式服務。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276500"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 觸發程序和繫結概念

在本文中，您將瞭解函數觸發程式和系結的高階概念。

觸發程式是造成函式執行的原因。 觸發程式會定義叫用函式的方式，而函式必須剛好有一個觸發程式。 觸發程式具有相關聯的資料，這通常會提供做為函式的承載。 

系結至函式是以宣告方式將另一個資源連接至函式的方法;系結可能會連接為*輸入*系結、*輸出*系結或兩者。 繫結中的資料會提供給函式作為參數。

您可以混合使用不同的系結，以符合您的需求。 系結是選擇性的，而且函數可能有一個或多個輸入和/或輸出系結。

觸發程式和系結可讓您避免硬式編碼存取其他服務。 您的函式會接收函式參數中的資料 (例如佇列訊息的內容)。 您可以使用函式的傳回值來傳送資料 (例如用以建立佇列訊息)。 

請考慮下列範例，以瞭解如何執行不同的功能。

| 範例案例 | 觸發程序 | 輸入系結 | 輸出系結 |
|-------------|---------|---------------|----------------|
| 新的佇列訊息抵達，其會執行函式以寫入至另一個佇列。 | 佇列<sup>*</sup> | *None* | 佇列<sup>*</sup> |
|排程工作會讀取 Blob 儲存體內容，並建立新的 Cosmos DB 檔。 | 計時器 | Blob 儲存體 | Cosmos DB |
|事件方格是用來從 Blob 儲存體中讀取影像，並使用來自 Cosmos DB 的檔來傳送電子郵件。 | Event Grid | Blob 儲存體和 Cosmos DB | SendGrid |
| 使用 Microsoft Graph 來更新 Excel 工作表的 webhook。 | HTTP | *None* | Microsoft Graph |

<sup>\*</sup>代表不同的佇列

這些範例不是完整的，而是提供來說明如何搭配使用觸發程式和系結。

###  <a name="trigger-and-binding-definitions"></a>觸發程式和系結定義

觸發程式和系結的定義會根據開發方法而有所不同。

| 平台 | 觸發程式和系結是由設定的 。 |
|-------------|--------------------------------------------|
| C# 類別庫 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;以C#屬性裝飾方法和參數 |
| 所有其他專案（包括 Azure 入口網站） | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;更新[函數. json](./functions-reference.md) （[架構](http://json.schemastore.org/function)） |

入口網站會提供此設定的 UI，但是您可以透過函式的 [**整合**] 索引標籤開啟可用的 [ **Advanced editor** ]，直接編輯檔案。

在 .NET 中，參數類型會定義輸入資料的資料類型。 例如，使用 `string` 系結至佇列觸發程式的文字、要讀取為二進位的位元組陣列，以及要還原序列化為物件的自訂類型。

對於 JavaScript 等具有動態類型的語言，則會使用 `dataType`function.json*檔案中的* 屬性。 例如，若要讀取二進位格式的 HTTP 要求內容，請將 `dataType` 設定為 `binary`：

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

`dataType` 也另具有 `stream` 和 `string` 兩種選項。

## <a name="binding-direction"></a>繫結方向

所有觸發程序和繫結在 function.json`direction`[ 檔案中都具有 ](./functions-reference.md) 屬性：

- 對於觸發程序，方向一律為 `in`
- 輸入和輸出繫結使用 `in` 和 `out`
- 某些繫結支援特殊方向 `inout`。 如果您使用 `inout`，則只能透過入口網站中的 [**整合**] 索引標籤使用 [ **Advanced editor** ]。

當您使用[類別庫中的屬性](functions-dotnet-class-library.md)來設定觸發程序和繫結時，請在屬性建構函式中提供方向，或從參數類型推斷方向。

## <a name="supported-bindings"></a>支援的繫結

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

如需哪些繫結為預覽狀態或已核准可用於實際執行環境的資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="resources"></a>資源
- [系結運算式和模式](./functions-bindings-expressions-patterns.md)
- [使用 Azure 函數傳回值](./functions-bindings-return-value.md)
- [如何註冊系結運算式](./functions-bindings-register.md)
- 測試：
  - [在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)
  - [手動執行非 HTTP 觸發的函式](functions-manually-run-non-http.md)
- [處理系結錯誤](./functions-bindings-errors.md)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [註冊 Azure Functions 系結延伸模組](./functions-bindings-register.md)
