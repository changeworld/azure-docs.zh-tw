---
title: Durable Functions 版本總覽-Azure Functions
description: 瞭解 Durable Functions 版本。
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 4c8a536086e426a2d83d26538f9d0efe1ea63eb4
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705787"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions 版本總覽

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/webjobs-create.md) 的擴充功能，可讓您在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。 如果您還不熟悉 Durable Functions，請參閱 [總覽檔](durable-functions-overview.md)。

## <a name="new-features-in-2x"></a>2.x 中的新功能

本節說明2.x 版中加入的 Durable Functions 功能。

### <a name="durable-entities"></a>持久性實體

在 Durable Functions 2.x 中，我們引進了新的 [實體函數](durable-functions-entities.md) 概念。

實體函式會定義用於讀取和更新一小段狀態 (稱為「持久性實體」) 的作業。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」**) 的函式。 不同于協調器函式，實體函數沒有任何特定的程式碼條件約束。 實體函式也會明確管理狀態，而不是透過控制流程來隱含表示狀態。

若要深入瞭解，請參閱長期 [實體](durable-functions-entities.md) 文章。

### <a name="durable-http"></a>持久 HTTP

在 Durable Functions 2.x 中，我們引進了新的 [持久 HTTP](durable-functions-http-features.md#consuming-http-apis) 功能，可讓您：

* 從協調流程函式直接呼叫 HTTP Api (，並) 一些記載的限制。
* 執行自動用戶端 HTTP 202 狀態輪詢。
* [Azure 受控](../../active-directory/managed-identities-azure-resources/overview.md)識別的內建支援。

若要深入瞭解，請參閱 [HTTP 功能](durable-functions-http-features.md#consuming-http-apis) 文章。

## <a name="migrate-from-1x-to-2x"></a>從1.x 遷移至2。x

本節說明如何將您現有的1.x 版 Durable Functions 遷移至2.x 版，以利用新功能。

### <a name="upgrade-the-extension"></a>升級擴充功能

在您的專案中安裝最新的2.x 版 Durable Functions 系結延伸模組。

#### <a name="javascript-and-python"></a>JavaScript 和 Python

Durable Functions 2.x 適用于 [Azure Functions 擴充](../functions-bindings-register.md#extension-bundles)功能套件組合的2.x 版。

若要更新專案中的延伸套件組合版本，請開啟 host.js，並更新 `extensionBundle` 區段以使用2.x 版 (`[2.*, 3.0.0)`) 。

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

#### <a name="net"></a>.NET

更新您的 .NET 專案以使用最新版的 Durable Functions 系結 [延伸](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)模組。

如需詳細資訊，請參閱 [註冊 Azure Functions](../functions-bindings-register.md#local-csharp) 系結延伸模組。

### <a name="update-your-code"></a>更新您的程式碼

Durable Functions 2.x 引進了幾項重大變更。 Durable Functions 1.x 應用程式與 Durable Functions 2.x 不相容，不需要變更程式碼。 本節列出將1.x 版函式升級到2.x 時必須進行的一些變更。

#### <a name="hostjson-schema"></a>架構上的 Host.js

Durable Functions 2.x 會在架構上使用新的 host.js。 從1.x 進行的主要變更包括：

* `"storageProvider"` (以及儲存區特定設定的子區段 `"azureStorage"`) 。
* `"tracing"` 用於追蹤和記錄設定。
* `"notifications"` (，以及 `"eventGrid"` 事件方格通知設定的小節) 。

如需詳細資訊，請參閱 [Durable Functions host.js參考檔](durable-functions-bindings.md#durable-functions-2-0-host-json) 。

#### <a name="default-taskhub-name-changes"></a>預設 taskhub 名稱變更

在1.x 版中，如果未在 host.js中指定工作中樞名稱，則預設為 ">durablefunctionshub"。 在2.x 版中，預設的工作中樞名稱現在是衍生自函式應用程式的名稱。 因此，如果您在升級至2.x 時未指定工作中樞名稱，則您的程式碼將會使用新的工作中樞來運作，而且所有的進行中的協調流程都不會再有應用程式進行處理。 若要解決此問題，您可以將工作中樞名稱明確設定為 ">durablefunctionshub" 的 v1. x 預設值，或者，您可以遵循我們的 [零停機部署指引](durable-functions-zero-downtime-deployment.md) ，以取得如何處理進行中協調流程的重大變更的詳細資料。

#### <a name="public-interface-changes-net-only"></a>公用介面變更 ( 僅限 .NET) 

在1.x 版中，Durable Functions 所支援的各種 _內容_ 物件都有要在單元測試中使用的抽象基類。 在 Durable Functions 2.x 的過程中，這些抽象基類會取代為介面。

下表代表主要變更：

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 或 `IDurableClient` |
| `DurableOrchestrationContext` 或 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 或 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

在抽象基類包含虛擬方法的情況下，這些虛擬方法已由中定義的擴充方法取代 `DurableContextExtensions` 。

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.jsJavaScript 和 c # 腳本 (變更) 

在 Durable Functions 1.x 中，協調流程用戶端系結會使用 `type` 的 `orchestrationClient` 。 2.x 版會改為使用 `durableClient` 。

#### <a name="raise-event-changes"></a>引發事件變更

在 Durable Functions 1.x 中，呼叫 [引發事件](durable-functions-external-events.md#send-events) API，並指定不存在的實例會導致無訊息失敗。 從2.x 開始，引發事件到不存在的協調流程會導致例外狀況。
