---
title: 適用於 Functions 2.x 的 Azure Cosmos DB 繫結
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 觸發程序和繫結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605768"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB 觸發器和綁定，用於 Azure 函數 2.x 概述

> [!div class="op_single_selector" title1="選擇正在使用的 Azure 函數運行時的版本： "]
> * [版本 1](functions-bindings-cosmosdb.md)
> * [版本 2](functions-bindings-cosmosdb-v2.md)

這組文章介紹了如何使用 Azure 函數 2.x 中的[Azure Cosmos DB](../cosmos-db/serverless-computing-database.md)綁定。 Azure Functions 支援適用於 Azure Cosmos DB 的觸發程序、輸入和輸出繫結。

| 動作 | 類型 |
|---------|---------|
| 創建或修改 Azure Cosmos DB 文檔時運行函數 | [觸發](./functions-bindings-cosmosdb-v2-trigger.md) |
| 閱讀 Azure 宇宙資料庫文檔 | [輸入綁定](./functions-bindings-cosmosdb-v2-input.md) |
| 保存對 Azure 宇宙資料庫文檔的更改  |[輸出綁定](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 此引用適用于[Azure 函數版本 2.x](functions-versions.md)。  如需如何在 Functions 1.x 中使用這些繫結的詳細資訊，請參閱[適用於 Azure Functions 1.x 的 Azure Cosmos DB 繫結](functions-bindings-cosmosdb.md)。
>
> 這個繫結最初命名為 DocumentDB。 在 Functions 2.x 版本中，觸發程序、繫結和套件已重新命名為 Cosmos DB。

## <a name="supported-apis"></a>支援的 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>添加到功能應用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用觸發器和綁定需要引用相應的包。 NuGet 包用於 .NET 類庫，而擴展包用於所有其他應用程式類型。

| 語言                                        | 添加...                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套裝軟體]，版本 3.x | |
| C# 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[擴展包]          | 建議使用[Azure 工具擴展]與視覺化工作室代碼一起使用。 |
| C# 腳本（僅在 Azure 門戶中連線）         | 添加綁定                            | 要更新現有綁定擴展，而無需重新發佈函數應用，請參閱[更新擴展]。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[擴展包]: ./functions-bindings-register.md#extension-bundles
[更新擴展]: ./install-update-binding-extensions-manual.md
[Azure 工具擴展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函數 1.x 應用會自動具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>後續步驟

- [創建或修改 Azure 宇宙資料庫文檔時運行函數（觸發器）](./functions-bindings-cosmosdb-v2-trigger.md)
- [閱讀 Azure 宇宙資料庫文檔（輸入綁定）](./functions-bindings-cosmosdb-v2-input.md)
- [保存對 Azure Cosmos DB 文檔的更改（輸出綁定）](./functions-bindings-cosmosdb-v2-output.md)
