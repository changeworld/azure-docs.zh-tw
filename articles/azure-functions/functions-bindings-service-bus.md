---
title: Azure Functions 的 Azure 服務匯流排繫結
description: 瞭解如何在 Azure Functions 中傳送 Azure 服務匯流排觸發程式和系結。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e15dfec049197fa056cbd55fd839b3eb93be77c
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530364"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服務匯流排繫結

Azure Functions 透過[觸發程式和](./functions-triggers-bindings.md)系結與[Azure 服務匯流排](https://azure.microsoft.com/services/service-bus)整合。 與服務匯流排整合可讓您建立可回應和傳送佇列或主題訊息的函式。

| 動作 | 類型 |
|---------|---------|
| 建立服務匯流排佇列或主題訊息時執行函數 | [觸發程序](./functions-bindings-service-bus-trigger.md) |
| 傳送 Azure 服務匯流排訊息 |[輸出系結](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>新增至您的函數應用程式

> [!NOTE]
> 服務匯流排系結目前不支援使用受控識別進行驗證。 相反地，請使用 [服務匯流排共用存取](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)簽章。

### <a name="functions-2x-and-higher"></a>Functions 2.x 和更新版本

使用觸發程式和系結會要求您參考適當的封裝。 NuGet 套件適用于 .NET 類別庫，而擴充功能配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝 [NuGet 套件]4.x 版 | |
| C # 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[延伸]模組套件組合          | 建議搭配使用 [Azure Tools 擴充] 功能與 Visual Studio Code。 |
| C # 腳本 (online-僅適用于 Azure 入口網站)          | 新增系結                            | 若要更新現有的系結延伸模組，而不需要重新發佈函數應用程式，請參閱 [更新您的延伸]模組。 |

[NuGet 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[延伸模組套件組合]: ./functions-bindings-register.md#extension-bundles
[更新您的延伸模組]: ./install-update-binding-extensions-manual.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函式1.x 應用程式會自動參考 [到 Nuget.exe NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) 套件2.x 版。

## <a name="next-steps"></a>後續步驟

- [ (觸發程式建立服務匯流排佇列或主題訊息時，執行函式) ](./functions-bindings-service-bus-trigger.md)
- [從 Azure Functions (輸出系結傳送 Azure 服務匯流排訊息) ](./functions-bindings-service-bus-output.md)
