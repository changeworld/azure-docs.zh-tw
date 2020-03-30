---
title: Azure Functions 的 Azure 服務匯流排繫結
description: 瞭解如何在 Azure 函數中發送 Azure 服務匯流排觸發器和綁定。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277410"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服務匯流排繫結

Azure 函數通過[觸發器和綁定](./functions-triggers-bindings.md)與[Azure 服務匯流排](https://azure.microsoft.com/services/service-bus)集成。 通過與服務匯流排集成，可以生成回應和發送佇列或主題消息的函數。

| 動作 | 類型 |
|---------|---------|
| 創建服務匯流排佇列或主題消息時運行函數 | [觸發](./functions-bindings-service-bus-trigger.md) |
| 發送 Azure 服務匯流排消息 |[輸出綁定](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>添加到功能應用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用觸發器和綁定需要引用相應的包。 NuGet 包用於 .NET 類庫，而擴展包用於所有其他應用程式類型。

| 語言                                        | 添加...                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套裝軟體]，版本 4.x | |
| C# 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[擴展包]          | 建議使用[Azure 工具擴展]與視覺化工作室代碼一起使用。 |
| C# 腳本（僅在 Azure 門戶中連線）         | 添加綁定                            | 要更新現有綁定擴展，而無需重新發佈函數應用，請參閱[更新擴展]。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[擴展包]: ./functions-bindings-register.md#extension-bundles
[更新擴展]: ./install-update-binding-extensions-manual.md
[Azure 工具擴展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函數 1.x 應用會自動具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>後續步驟

- [創建服務匯流排佇列或主題消息時運行函數（觸發器）](./functions-bindings-service-bus-trigger.md)
- [從 Azure 函數發送 Azure 服務匯流排消息（輸出綁定）](./functions-bindings-service-bus-output.md)
