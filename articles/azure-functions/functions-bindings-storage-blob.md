---
title: 適用于 Azure Functions 的 Azure Blob 儲存體觸發程式和系結
description: 瞭解如何在 Azure Functions 中使用 Azure Blob 儲存體觸發程式和系結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: eead2d6ae2860bf06f1b878c1bb1692d74c06df6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87055965"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>適用于 Azure Functions 總覽的 Azure Blob 儲存體系結

Azure Functions 透過[觸發程式和](./functions-triggers-bindings.md)系結與[Azure 儲存體](../storage/index.yml)整合。 與 Blob 儲存體整合可讓您建立函式來回應 Blob 資料的變更，以及讀取和寫入值。

| 動作 | 類型 |
|---------|---------|
| 以 blob 儲存體資料變更的形式執行函數 | [觸發程序](./functions-bindings-storage-blob-trigger.md) |
| 讀取函式中的 blob 儲存體資料 | [輸入系結](./functions-bindings-storage-blob-input.md) |
| 允許函數撰寫 blob 儲存體資料 |[輸出系結](./functions-bindings-storage-blob-output.md) |

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
[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[更新您的延伸模組]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函式1.x 應用程式會自動 [參考 Nuget.exe NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) 套件2.x 版。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>接下來的步驟

- [在 blob 儲存體資料變更時執行函數](./functions-bindings-storage-blob-trigger.md)
- [在函數執行時讀取 blob 儲存體資料](./functions-bindings-storage-blob-input.md)
- [從函式寫入 blob 儲存體資料](./functions-bindings-storage-blob-output.md)
