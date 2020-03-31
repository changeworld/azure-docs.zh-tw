---
title: Azure Functions SignalR Service 繫結
description: 了解如何搭配使用 SignalR Service 繫結與 Azure Functions。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523031"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>適用於 Azure Functions 的 SignalR Service 繫結

這組文章介紹了如何通過在 Azure 函數中使用 SignalR 服務綁定對連接到[Azure SignalR 服務的](https://azure.microsoft.com/services/signalr-service/)用戶端進行身份驗證和發送即時消息。 Azure Functions 支援 SignalR Service 的輸入和輸出繫結。

| 動作 | 類型 |
|---------|---------|
| 返回服務終結點 URL 和訪問權杖 | [輸入綁定](./functions-bindings-signalr-service-input.md) |
| 發送信號R服務消息 |[輸出綁定](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>添加到功能應用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用觸發器和綁定需要引用相應的包。 NuGet 包用於 .NET 類庫，而擴展包用於所有其他應用程式類型。

| 語言                                        | 添加...                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套裝軟體]，版本 3.x | |
| C# 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[擴展包]          | 建議使用[Azure 工具擴展]與視覺化工作室代碼一起使用。 |
| C# 腳本（僅在 Azure 門戶中連線）         | 添加綁定                            | 要更新現有綁定擴展，而無需重新發佈函數應用，請參閱[更新擴展]。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[擴展包]: ./functions-bindings-register.md#extension-bundles
[更新擴展]: ./install-update-binding-extensions-manual.md
[Azure 工具擴展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

有關如何一起配置和使用 SignalR 服務和 Azure 函數的詳細資訊，請參閱[Azure 函數開發和配置 Azure SignalR 服務](../azure-signalr/signalr-concept-serverless-development-config.md)。

### <a name="annotations-library-java-only"></a>注釋庫（僅限 JAVA）

要在 JAVA 函數中使用 SignalR 服務注釋，您需要向*pom.xml*檔添加依賴于*azure 函數-java-庫信號器*專案（版本 1.0 或更高版本）。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>後續步驟

- [返回服務終結點 URL 和訪問權杖（輸入綁定）](./functions-bindings-signalr-service-input.md)
- [發送信號器服務消息（輸出綁定）](./functions-bindings-signalr-service-output.md) 
