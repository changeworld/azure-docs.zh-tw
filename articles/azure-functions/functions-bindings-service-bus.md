---
title: Azure Functions 的 Azure 服務匯流排繫結
description: 瞭解如何在 Azure Functions 中傳送 Azure 服務匯流排觸發程式和系結。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277410"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服務匯流排繫結

Azure Functions 透過[觸發程式和](./functions-triggers-bindings.md)系結與[Azure 服務匯流排](https://azure.microsoft.com/services/service-bus)整合。 與服務匯流排整合，可讓您建立可回應和傳送佇列或主題訊息的函式。

| 動作 | 類型 |
|---------|---------|
| 建立服務匯流排佇列或主題訊息時執行函數 | [觸發程序](./functions-bindings-service-bus-trigger.md) |
| 傳送 Azure 服務匯流排訊息 |[輸出系結](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>新增至函數應用程式

### <a name="functions-2x-and-higher"></a>函數2.x 和更新版本

使用觸發程式和系結時，您需要參考適當的套件。 NuGet 套件適用于 .NET 類別庫，而延伸模組配套則用於所有其他應用程式類型。

| Language                                        | 加入者 。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套件]，第4.x 版 | |
| C#腳本，JAVA，JavaScript，Python，PowerShell | 註冊[延伸]模組配套          | 建議使用[Azure Tools 擴充功能]功能搭配 Visual Studio Code。 |
| C#腳本（僅限線上 Azure 入口網站）         | 新增系結                            | 若要更新現有的系結延伸模組而不需要重新發佈函式應用程式，請參閱[更新您的擴充]功能。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[延伸]: ./functions-bindings-register.md#extension-bundles
[更新您的擴充]: ./install-update-binding-extensions-manual.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函式1.x 應用程式會自動擁有[Microsoft Azure webjob](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 套件2.x 版的參考。

## <a name="next-steps"></a>後續步驟

- [建立服務匯流排佇列或主題訊息時執行函數（觸發程式）](./functions-bindings-service-bus-trigger.md)
- [從 Azure Functions 傳送 Azure 服務匯流排訊息（輸出系結）](./functions-bindings-service-bus-output.md)
