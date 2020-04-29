---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586118"
---
## <a name="add-to-your-functions-app"></a>新增至函數應用程式

### <a name="functions-2x-and-higher"></a>函數2.x 和更新版本

使用觸發程式和系結時，您需要參考適當的套件。 NuGet 套件適用于 .NET 類別庫，而延伸模組配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套件]3.x 版 | |
| C # 腳本，JAVA，JavaScript，Python，PowerShell | 註冊[延伸]模組配套          | 建議使用[Azure Tools 擴充]功能搭配 Visual Studio Code。 |
| C # 腳本（僅限線上 Azure 入口網站）         | 新增系結                            | 若要更新現有的系結延伸模組而不需要重新發佈函式應用程式，請參閱[更新您的擴充]功能。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[延伸模組配套]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[更新您的擴充功能]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函式1.x 應用程式會自動擁有[Microsoft Azure webjob](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 套件2.x 版的參考。