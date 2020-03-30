---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586118"
---
## <a name="add-to-your-functions-app"></a>添加到功能應用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用觸發器和綁定需要引用相應的包。 NuGet 包用於 .NET 類庫，而擴展包用於所有其他應用程式類型。

| 語言                                        | 添加...                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套裝軟體]，版本 3.x | |
| C# 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[擴展包]          | 建議使用[Azure 工具擴展]與視覺化工作室代碼一起使用。 |
| C# 腳本（僅在 Azure 門戶中連線）         | 添加綁定                            | 要更新現有綁定擴展，而無需重新發佈函數應用，請參閱[更新擴展]。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[擴展包]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[更新擴展]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Azure 工具擴展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函數 1.x 應用會自動具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。