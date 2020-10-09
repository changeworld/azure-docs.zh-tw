---
title: Azure Functions HTTP 觸發程序和繫結
description: 瞭解如何在 Azure Functions 中使用 HTTP 觸發程式和系結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77462100"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP 觸發程式和系結總覽

Azure Functions 可透過 HTTP 要求來叫用，以建立無伺服器 Api 並回應 [webhook](https://en.wikipedia.org/wiki/Webhook)。

| 動作 | 類型 |
|---------|---------|
| 從 HTTP 要求執行函數 | [觸發程序](./functions-bindings-http-webhook-trigger.md) |
| 從函式傳回 HTTP 回應 |[輸出系結](./functions-bindings-http-webhook-output.md) |

本文中的程式碼預設為 .NET Core 語法，用於函數2.x 版和更新版本中。 如需 1.x 語法的資訊，請參閱 [1.x 函式範本](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

## <a name="add-to-your-functions-app"></a>新增至您的函數應用程式

### <a name="functions-2x-and-higher"></a>Functions 2.x 和更新版本

使用觸發程式和系結會要求您參考適當的封裝。 NuGet 套件適用于 .NET 類別庫，而擴充功能配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝 [NuGet 套件]3.x 版 | |
| C # 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[延伸]模組套件組合          | 建議搭配使用 [Azure Tools 擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 功能與 Visual Studio Code。 |
| C # 腳本 (online-僅適用于 Azure 入口網站)          | 新增系結                            | 若要更新現有的系結延伸模組，而不需要重新發佈函數應用程式，請參閱 [更新您的延伸]模組。 |

[core tools]: ./functions-run-local.md
[延伸模組套件組合]: ./functions-bindings-register.md#extension-bundles
[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[更新您的延伸模組]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函式1.x 應用程式會自動 [參考 Nuget.exe NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) 套件2.x 版。

## <a name="next-steps"></a>後續步驟

- [從 HTTP 要求執行函數](./functions-bindings-http-webhook-trigger.md)
- [從函式傳回 HTTP 回應](./functions-bindings-http-webhook-output.md)
