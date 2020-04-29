---
title: 適用於 Functions 2.x 的 Azure Cosmos DB 繫結
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 觸發程序和繫結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605768"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>適用于 Azure Functions 2.x 的 Azure Cosmos DB 觸發程式和系結總覽

> [!div class="op_single_selector" title1="選取您要使用的 Azure Functions 執行階段版本： "]
> * [第 1 版](functions-bindings-cosmosdb.md)
> * [第 2 版](functions-bindings-cosmosdb-v2.md)

這一組文章說明如何使用 Azure Functions 2.x 中的[Azure Cosmos DB](../cosmos-db/serverless-computing-database.md)系結。 Azure Functions 支援適用於 Azure Cosmos DB 的觸發程序、輸入和輸出繫結。

| 動作 | 類型 |
|---------|---------|
| 建立或修改 Azure Cosmos DB 檔時執行函數 | [觸發程序](./functions-bindings-cosmosdb-v2-trigger.md) |
| 閱讀 Azure Cosmos DB 檔 | [輸入系結](./functions-bindings-cosmosdb-v2-input.md) |
| 儲存 Azure Cosmos DB 檔的變更  |[輸出系結](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 此參考適用于[Azure Functions 2.x 版](functions-versions.md)。  如需如何在 Functions 1.x 中使用這些繫結的詳細資訊，請參閱[適用於 Azure Functions 1.x 的 Azure Cosmos DB 繫結](functions-bindings-cosmosdb.md)。
>
> 這個繫結最初命名為 DocumentDB。 在 Functions 2.x 版本中，觸發程序、繫結和套件已重新命名為 Cosmos DB。

## <a name="supported-apis"></a>支援的 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>新增至函數應用程式

### <a name="functions-2x-and-higher"></a>函數2.x 和更新版本

使用觸發程式和系結時，您需要參考適當的套件。 NuGet 套件適用于 .NET 類別庫，而延伸模組配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套件]3.x 版 | |
| C # 腳本，JAVA，JavaScript，Python，PowerShell | 註冊[延伸]模組配套          | 建議使用[Azure Tools 擴充]功能搭配 Visual Studio Code。 |
| C # 腳本（僅限線上 Azure 入口網站）         | 新增系結                            | 若要更新現有的系結延伸模組而不需要重新發佈函式應用程式，請參閱[更新您的擴充]功能。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[延伸模組配套]: ./functions-bindings-register.md#extension-bundles
[更新您的擴充功能]: ./install-update-binding-extensions-manual.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函式1.x 應用程式會自動擁有[Microsoft Azure webjob](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 套件2.x 版的參考。

## <a name="next-steps"></a>後續步驟

- [建立或修改 Azure Cosmos DB 檔時執行函數（觸發程式）](./functions-bindings-cosmosdb-v2-trigger.md)
- [讀取 Azure Cosmos DB 檔（輸入系結）](./functions-bindings-cosmosdb-v2-input.md)
- [儲存 Azure Cosmos DB 檔的變更（輸出系結）](./functions-bindings-cosmosdb-v2-output.md)
