---
title: Azure 佇列存儲觸發器和 Azure 函數概述的綁定
description: 了解如何在 Azure Functions 中使用 Azure 佇列儲存體觸發程序和輸出繫結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277306"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure 佇列存儲觸發器和 Azure 函數概述的綁定

Azure 函數可以在創建新的 Azure 佇列存儲消息時運行，並可以在函數中寫入佇列消息。

| 動作 | 類型 |
|---------|---------|
| 隨著佇列存儲資料更改而運行函數 | [觸發](./functions-bindings-storage-queue-trigger.md) |
| 寫入佇列存儲消息 |[輸出綁定](./functions-bindings-storage-queue-output.md) |

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
[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[更新擴展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函數 1.x 應用會自動具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>後續步驟

- [在佇列存儲資料更改時運行函數（觸發器）](./functions-bindings-storage-queue-trigger.md)
- [寫入佇列存儲消息（輸出綁定）](./functions-bindings-storage-queue-output.md)
