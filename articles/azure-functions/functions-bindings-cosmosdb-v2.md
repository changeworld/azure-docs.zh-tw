---
title: 函數 2. xd 和更新版本的 Azure Cosmos DB 系結
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 觸發程序和繫結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: 2c6efd14bd974de1b01b1725b9810f153df74bf8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482168"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Functions 2.x 和更高版本的 Azure Cosmos DB 觸發程式和系結總覽

> [!div class="op_single_selector" title1="選取您要使用的 Azure Functions 執行階段版本： "]
> * [第 1 版](functions-bindings-cosmosdb.md)
> * [第2版和更高版本](functions-bindings-cosmosdb-v2.md)

這一組文章說明如何在 Azure Functions 2.x 和更新版本中使用 [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) 系結。 Azure Functions 支援適用於 Azure Cosmos DB 的觸發程序、輸入和輸出繫結。

| 動作 | 類型 |
|---------|---------|
| 建立或修改 Azure Cosmos DB 檔時執行函數 | [觸發程序](./functions-bindings-cosmosdb-v2-trigger.md) |
| 讀取 Azure Cosmos DB 檔 | [輸入系結](./functions-bindings-cosmosdb-v2-input.md) |
| 將變更儲存至 Azure Cosmos DB 檔  |[輸出系結](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 這是 Azure Functions 2.x [版和更新版本](functions-versions.md)的參考。  如需如何在 Functions 1.x 中使用這些繫結的詳細資訊，請參閱[適用於 Azure Functions 1.x 的 Azure Cosmos DB 繫結](functions-bindings-cosmosdb.md)。
>
> 這個繫結最初命名為 DocumentDB。 在2.x 版和更高版本的函式中，觸發程式、系結和封裝都命名為 Cosmos DB。

## <a name="supported-apis"></a>支援的 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>新增至您的函數應用程式

### <a name="functions-2x-and-higher"></a>Functions 2.x 和更新版本

使用觸發程式和系結會要求您參考適當的封裝。 NuGet 套件適用于 .NET 類別庫，而擴充功能配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝 [NuGet 套件]3.x 版 | |
| C # 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[延伸]模組套件組合          | 建議搭配使用 [Azure Tools 擴充] 功能與 Visual Studio Code。 |
| C # 腳本 (online-僅適用于 Azure 入口網站)          | 新增系結                            | 若要更新現有的系結延伸模組，而不需要重新發佈函數應用程式，請參閱 [更新您的延伸]模組。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[延伸模組套件組合]: ./functions-bindings-register.md#extension-bundles
[更新您的延伸模組]: ./install-update-binding-extensions-manual.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函式1.x 應用程式會自動 [參考 Nuget.exe NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) 套件2.x 版。

## <a name="next-steps"></a>接下來的步驟

- [當 Azure Cosmos DB 檔建立或修改 (觸發程式時，執行函式) ](./functions-bindings-cosmosdb-v2-trigger.md)
- [讀取 Azure Cosmos DB 檔 (輸入系結) ](./functions-bindings-cosmosdb-v2-input.md)
- [將變更儲存至 Azure Cosmos DB 檔 (輸出系結) ](./functions-bindings-cosmosdb-v2-output.md)
