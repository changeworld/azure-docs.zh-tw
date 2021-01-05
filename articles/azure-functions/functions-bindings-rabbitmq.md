---
title: 適用于 Azure Functions 的 Azure RabbitMQ 系結
description: 瞭解如何在 Azure Functions 中傳送 Azure RabbitMQ 觸發程式和系結。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746519"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Azure Functions 總覽的 RabbitMQ 系結

> [!NOTE]
> RabbitMQ 系結僅在 **Premium 和專用** 方案上受到完整支援。 不支援耗用量。

Azure Functions 透過[觸發程式和](./functions-triggers-bindings.md)系結與[RabbitMQ](https://www.rabbitmq.com/)整合。 Azure Functions RabbitMQ 擴充功能可讓您使用 RabbitMQ API 搭配函式來傳送和接收訊息。

| 動作 | 類型 |
|---------|---------|
| 在 RabbitMQ 訊息通過佇列時執行函數 | [觸發程序](./functions-bindings-rabbitmq-trigger.md) |
| 傳送 RabbitMQ 訊息 |[輸出系結](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>新增至您的函數應用程式

若要開始使用此延伸模組進行開發，請務必先 [設定 RabbitMQ 端點](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint)。 若要深入瞭解 RabbitMQ，請參閱他們的 [開始使用] [頁面](https://www.rabbitmq.com/getstarted.html)。

### <a name="functions-3x-and-higher"></a>函數3.x 和更新版本

使用觸發程式和系結會要求您參考適當的封裝。 NuGet 套件適用于 .NET 類別庫，而擴充功能配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝 [NuGet 套件]4.x 版 | |
| C # 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[延伸]模組套件組合          | 建議搭配使用 [Azure Tools 擴充] 功能與 Visual Studio Code。 |
| C # 腳本 (online-僅適用于 Azure 入口網站)          | 新增系結                            | 若要更新現有的系結延伸模組，而不需要重新發佈函數應用程式，請參閱 [更新您的延伸]模組。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[延伸模組套件組合]: ./functions-bindings-register.md#extension-bundles
[更新您的延伸模組]: ./functions-bindings-register.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>函數1.x 和2。x

函數1.x 和2.x 不支援 RabbitMQ 系結延伸模組。 請使用函數3.x 和更新版本。

## <a name="next-steps"></a>後續步驟

- [ (觸發程式建立 RabbitMQ 訊息時執行函數) ](./functions-bindings-rabbitmq-trigger.md)
- [傳送 RabbitMQ 訊息 Azure Functions (輸出系結) ](./functions-bindings-rabbitmq-output.md)