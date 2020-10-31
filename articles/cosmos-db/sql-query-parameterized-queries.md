---
title: Azure Cosmos DB 中的參數化查詢
description: 瞭解 SQL 參數化查詢如何提供強大的使用者輸入處理和植入，以及防止透過 SQL 資料隱碼意外地公開資料。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: a0f354a0420521ede45740a4eb9c492089e36bda
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089214"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB 中的參數化查詢
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 支援以熟悉 @ 標記法表示的參數查詢。 參數化 SQL 可提供穩固的使用者輸入處理和植入，並防止透過 SQL 資料隱碼意外地公開資料。

## <a name="examples"></a>範例

例如，您可以撰寫採用 `lastName` 和 `address.state` 做為參數的查詢，並 `lastName` 根據使用者輸入來執行和的不同值 `address.state` 。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然後，您可以將此要求傳送給 Azure Cosmos DB，作為參數化的 JSON 查詢，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

下列範例會使用參數化查詢來設定 TOP 引數：

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

參數值可以是任何有效的 JSON：字串、數位、布林值、null、甚至是陣列或嵌套 JSON。 因為 Azure Cosmos DB 無架構，所以不會對任何類型驗證參數。

以下是每個 Azure Cosmos DB SDK 中參數化查詢的範例：

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文件資料](modeling-data.md)
