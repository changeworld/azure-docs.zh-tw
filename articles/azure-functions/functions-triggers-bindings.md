---
title: Azure Functions 中的觸發程序和繫結
description: 瞭解如何使用觸發器和綁定將 Azure 函數連接到線上活動和基於雲的服務。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276500"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 觸發程序和繫結概念

在本文中，您將瞭解有關函數觸發器和綁定的高級概念。

觸發器是導致函數運行的原因。 觸發器定義如何調用函數，並且函數必須具有一個觸發器。 觸發程序具有相關聯的資料，它通常提供作為函式的承載。 

綁定到函數是一種聲明性地將另一個資源連接到函數的方法;綁定可以作為*輸入綁定*、*輸出綁定*或兩者連接。 繫結中的資料會提供給函式作為參數。

您可以混合使用不同繫結，以符合您的需求。 繫結是選擇性的，而且一個函數可能有一或多個輸入和/或輸出繫結。

觸發器和綁定可讓您避免對其他服務的硬編碼訪問。 您的函式會接收函式參數中的資料 (例如佇列訊息的內容)。 您可以使用函式的傳回值來傳送資料 (例如用以建立佇列訊息)。 

請考慮以下有關如何實現不同功能的示例。

| 範例案例 | 觸發程序 | 輸入綁定 | 輸出綁定 |
|-------------|---------|---------------|----------------|
| 新的佇列消息到達，它運行一個函數以寫入另一個佇列。 | 佇列<sup>*</sup> | *無* | 佇列<sup>*</sup> |
|計畫的作業讀取 Blob 存儲內容並創建新的 Cosmos DB 文檔。 | 計時器 | Blob 儲存體 | Cosmos DB |
|事件網格用於從 Blob 存儲讀取圖像，從 Cosmos DB 讀取文檔以發送電子郵件。 | Event Grid | Blob 存儲和宇宙資料庫 | SendGrid |
| 使用 Microsoft 圖形更新 Excel 工作表的 Web 鉤子。 | HTTP | *無* | Microsoft Graph |

<sup>\*</sup>表示不同的佇列

這些示例並非詳盡無遺，但提供是為了說明如何使用觸發器和綁定在一起。

###  <a name="trigger-and-binding-definitions"></a>觸發器和綁定定義

觸發器和綁定的定義因開發方法而異。

| Platform | 觸發器和綁定由... |
|-------------|--------------------------------------------|
| C# 類別庫 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用 C# 屬性的修飾方法和參數 |
| 所有其他（包括 Azure 門戶） | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;更新[函數.json](./functions-reference.md) （[架構](http://json.schemastore.org/function)） |

門戶為此配置提供了 UI，但您可以通過打開可通過函數的 **"集成"** 選項卡提供**的高級編輯器**來直接編輯檔。

在 .NET 中，參數類型定義輸入資料的資料類型。 例如，用於`string`綁定到佇列觸發器的文本、要讀取為二進位的位元組陣列和用於從序列化到物件的自訂類型。

對於 JavaScript 等具有動態類型的語言，則會使用 *function.json* 檔案中的 `dataType` 屬性。 例如，若要讀取二進位格式的 HTTP 要求內容，請將 `dataType` 設定為 `binary`：

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

所有觸發程序和繫結在 function.json[](./functions-reference.md) 檔案中都具有 `direction` 屬性：

- 對於觸發程序，方向一律為 `in`
- 輸入和輸出繫結使用 `in` 和 `out`
- 某些繫結支援特殊方向 `inout`。 如果使用 ，`inout`則只有**高級編輯器**可通過門戶中的 **"集成**"選項卡獲得。

當您使用[類別庫中的屬性](functions-dotnet-class-library.md)來設定觸發程序和繫結時，請在屬性建構函式中提供方向，或從參數類型推斷方向。

## <a name="supported-bindings"></a>支援的繫結

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

如需哪些繫結為預覽狀態或已核准可用於實際執行環境的資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="resources"></a>資源
- [繫結運算式和模式](./functions-bindings-expressions-patterns.md)
- [使用 Azure 函數傳回值](./functions-bindings-return-value.md)
- [如何註冊綁定運算式](./functions-bindings-register.md)
- 測試：
  - [在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)
  - [手動執行非 HTTP 觸發的函式](functions-manually-run-non-http.md)
- [處理繫結錯誤](./functions-bindings-errors.md)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [註冊 Azure 函數綁定擴展](./functions-bindings-register.md)
