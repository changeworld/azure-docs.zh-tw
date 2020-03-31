---
title: Azure Blob 存儲觸發器和用於 Azure 函數的綁定
description: 瞭解如何在 Azure 函數中使用 Azure Blob 存儲觸發器和綁定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277228"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob 存儲綁定，用於 Azure 函數概述

Azure 函數通過[觸發器和綁定](./functions-triggers-bindings.md)與[Azure 存儲](https://docs.microsoft.com/azure/storage/)集成。 通過與 Blob 存儲集成，可以生成回應 Blob 資料更改以及讀取和寫入值的函數。

| 動作 | 類型 |
|---------|---------|
| 當 Blob 存儲資料更改時運行函數 | [觸發](./functions-bindings-storage-blob-trigger.md) |
| 在函數中讀取 blob 存儲資料 | [輸入綁定](./functions-bindings-storage-blob-input.md) |
| 允許函數寫入 blob 存儲資料 |[輸出綁定](./functions-bindings-storage-blob-output.md) |

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

- [Blob 存儲資料更改時運行函數](./functions-bindings-storage-blob-trigger.md)
- [運行函數時讀取 Blob 存儲資料](./functions-bindings-storage-blob-input.md)
- [從函數寫入 blob 存儲資料](./functions-bindings-storage-blob-output.md)
