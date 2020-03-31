---
title: Azure Functions HTTP 觸發程序和繫結
description: 瞭解如何在 Azure 函數中使用 HTTP 觸發器和綁定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462100"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure 函數 HTTP 觸發器和綁定概述

Azure 函數可以通過 HTTP 要求調用，以生成無伺服器 API 並回應[Webhook。](https://en.wikipedia.org/wiki/Webhook)

| 動作 | 類型 |
|---------|---------|
| 從 HTTP 要求運行函數 | [觸發](./functions-bindings-http-webhook-trigger.md) |
| 從函數返回 HTTP 回應 |[輸出綁定](./functions-bindings-http-webhook-output.md) |

本文中的代碼預設為 .NET Core 語法，用於函數版本 2.x 和更高版本。 如需 1.x 語法的資訊，請參閱 [1.x 函式範本](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

## <a name="add-to-your-functions-app"></a>添加到功能應用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用觸發器和綁定需要引用相應的包。 NuGet 包用於 .NET 類庫，而擴展包用於所有其他應用程式類型。

| 語言                                        | 添加...                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套裝軟體]，版本 3.x | |
| C# 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[擴展包]          | 建議使用[Azure 工具擴展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)與視覺化工作室代碼一起使用。 |
| C# 腳本（僅在 Azure 門戶中連線）         | 添加綁定                            | 要更新現有綁定擴展，而無需重新發佈函數應用，請參閱[更新擴展]。 |

[core tools]: ./functions-run-local.md
[擴展包]: ./functions-bindings-register.md#extension-bundles
[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[更新擴展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函數 1.x 應用會自動具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>後續步驟

- [從 HTTP 要求運行函數](./functions-bindings-http-webhook-trigger.md)
- [從函數返回 HTTP 回應](./functions-bindings-http-webhook-output.md)
