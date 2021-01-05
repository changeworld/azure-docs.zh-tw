---
title: Azure Functions SignalR Service 繫結
description: 了解如何搭配使用 SignalR Service 繫結與 Azure Functions。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763484"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>適用於 Azure Functions 的 SignalR Service 繫結

這一組文章說明如何使用 Azure Functions 中的 SignalR Service 系結，來驗證和傳送即時訊息給連接到 [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) 的用戶端。 Azure Functions 支援 SignalR Service 的輸入和輸出繫結。

| 動作 | 類型 |
|---------|---------|
| 處理來自 SignalR Service 的訊息 | [觸發程式系結](./functions-bindings-signalr-service-trigger.md) |
| 傳回服務端點 URL 和存取權杖 | [輸入系結](./functions-bindings-signalr-service-input.md) |
| 傳送 SignalR Service 訊息 |[輸出系結](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>新增至您的函數應用程式

### <a name="functions-2x-and-higher"></a>Functions 2.x 和更新版本

使用觸發程式和系結會要求您參考適當的封裝。 NuGet 套件適用于 .NET 類別庫，而擴充功能配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝 [NuGet 套件]3.x 版 | |
| C # 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[延伸]模組套件組合          | 建議搭配使用 [Azure Tools 擴充] 功能與 Visual Studio Code。 |
| C # 腳本 (online-僅適用于 Azure 入口網站)          | 新增系結                            | 若要更新現有的系結延伸模組，而不需要重新發佈函數應用程式，請參閱 [更新您的延伸]模組。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[延伸模組套件組合]: ./functions-bindings-register.md#extension-bundles
[更新您的延伸模組]: ./functions-bindings-register.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

如需如何設定和使用 SignalR Service 和 Azure Functions 的詳細資訊，請參閱 [使用 Azure SignalR Service Azure Functions 開發和](../azure-signalr/signalr-concept-serverless-development-config.md)設定。

### <a name="annotations-library-java-only"></a>批註程式庫 (僅限 JAVA) 

若要在 JAVA 函式中使用 SignalR Service 注釋，您必須將相依性新增至 *pom.xml* 檔案 (1.0 版或更) 高版本的 azure 函式 *-SignalR* 成品。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>後續步驟

- [處理來自 SignalR Service (觸發程式系結的訊息) ](./functions-bindings-signalr-service-trigger.md)
- [傳回 (輸入系結的服務端點 URL 和存取權杖) ](./functions-bindings-signalr-service-input.md)
- [傳送 SignalR Service 訊息 (輸出系結) ](./functions-bindings-signalr-service-output.md)