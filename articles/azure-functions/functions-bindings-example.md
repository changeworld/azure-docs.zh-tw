---
title: Azure Functions 觸發程序和繫結範例
description: 瞭解如何設定 Azure 函數系結
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: c95524a5de6696bd0ffe7463451d152a9d3a19b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88205959"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions 觸發程序和繫結範例

本文示範如何在 Azure 函式中設定 [觸發程式和](./functions-triggers-bindings.md) 系結。

假設您想要每當新訊息出現在 Azure 佇列儲存體時，就在 Azure 表格儲存體寫入新的資料列。 此案例可以使用 Azure 佇列儲存體觸發程序和 Azure 表格儲存體輸出繫結來實作。 

以下是此案例的 *function.json* 檔案。 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 陣列中的第一個元素是佇列儲存體觸發程序。 `type` 和 `direction` 屬性可識別觸發程序。 `name` 屬性可識別會接收佇列訊息內容的函式參數。 要監視的佇列名稱是在 `queueName` 中，而連接字串則是在 `connection` 所識別的應用程式設定中。

`bindings` 陣列中的第二個元素是 Azure 表格儲存體輸出繫結。 `type` 和 `direction` 屬性可識別繫結。 `name` 屬性指定函式如何提供新的資料表資料列，在本例中是透過函式傳回值。 資料表的名稱是在 `tableName` 中，而連接字串則是在 `connection` 所識別的應用程式設定中。

若要在 Azure 入口網站中查看及編輯*function.js*的內容，請在函式的 [**整合**] 索引標籤上，按一下 [ **Advanced editor** ] 選項。

> [!NOTE]
> `connection` 的值是包含連接字串的應用程式設定名稱，而不是連接字串本身。 繫結使用儲存在應用程式設定中的連接字串，以強制遵循 *function.json* 不包含服務祕密的最佳做法。

## <a name="c-script-example"></a>C# 指令碼範例

以下是可搭配此觸發程序和繫結使用的 C# 指令碼。 請注意，提供佇列訊息內容的參數名稱為 `order`；由於 *function.json* 中的 `name` 屬性值為 `order`，因此必須使用此名稱 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript 範例

相同的檔案 *function.js* 可以搭配 JavaScript 函式使用：

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>類別庫範例

在類別庫中，會由屬性 (而非 function.json 檔案) 提供相同的觸發程序和繫結資訊 &mdash;佇列和資料表名稱、儲存體帳戶、輸入和輸出的函式參數&mdash;。 以下為範例：

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

您現在有一個可運作的函式，它會由 Azure 佇列觸發，並將資料輸出至 Azure 資料表儲存體。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 系結運算式模式](./functions-bindings-expressions-patterns.md)
