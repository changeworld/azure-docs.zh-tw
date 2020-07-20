---
title: Durable Functions 版本總覽-Azure Functions
description: 瞭解 Durable Functions 版本。
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 3ba190f40d3b9451aec6e86ea69b7d0fe6e66aa3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807850"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions 版本總覽

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的擴充功能，可讓您在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。 如果您還不熟悉 Durable Functions，請參閱[總覽檔](durable-functions-overview.md)。

## <a name="new-features-in-2x"></a>2.x 中的新功能

本節說明2.x 版中新增的 Durable Functions 功能。

### <a name="durable-entities"></a>持久性實體

在 Durable Functions 2.x 中，我們引進了新的[實體函數](durable-functions-entities.md)概念。

實體函式會定義用於讀取和更新一小段狀態 (稱為「持久性實體」**) 的作業。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」**) 的函式。 與協調器函式不同的是，實體函式沒有任何特定的程式碼條件約束。 實體函式也會明確管理狀態，而不是透過控制流程來隱含表示狀態。

若要深入瞭解，請參閱[耐用實體](durable-functions-entities.md)一文。

### <a name="durable-http"></a>持久 HTTP

在 Durable Functions 2.x 中，我們引進了新的[持久性 HTTP](durable-functions-http-features.md#consuming-http-apis)功能，可讓您：

* 直接從協調流程函式呼叫 HTTP Api （有一些記載的限制）。
* 執行自動用戶端 HTTP 202 狀態輪詢。
* [Azure 受控](../../active-directory/managed-identities-azure-resources/overview.md)識別的內建支援。

若要深入瞭解，請參閱[HTTP 功能](durable-functions-http-features.md#consuming-http-apis)一文。

## <a name="migrate-from-1x-to-2x"></a>從1.x 遷移至2。x

本節說明如何將您現有的1.x 版 Durable Functions 遷移至2.x 版，以利用新功能。

### <a name="upgrade-the-extension"></a>升級延伸模組

在您的專案中安裝2.x 版的 Durable Functions 系結[延伸](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)模組。 如需詳細資訊，請參閱[註冊 Azure Functions](../functions-bindings-register.md)系結延伸模組。

### <a name="update-your-code"></a>更新您的程式碼

Durable Functions 2.x 引進了幾項重大變更。 Durable Functions 1.x 應用程式與 Durable Functions 2.x 不相容，而不需要變更程式碼。 本節列出將1.x 版功能更新至2.x 時必須進行的一些變更。

#### <a name="hostjson-schema"></a>架構上的 Host.js

Durable Functions 2.x 會在架構上使用新的 host.js。 1. x 的主要變更包括：

* `"storageProvider"`（和子區段 `"azureStorage"` ），用於儲存特定設定。
* `"tracing"`用於追蹤和記錄設定。
* `"notifications"`事件方格通知設定的（和子區段 `"eventGrid"` ）。

如需詳細資訊，請參閱[參考檔上的 Durable Functions host.js](durable-functions-bindings.md#durable-functions-2-0-host-json) 。

#### <a name="default-taskhub-name-changes"></a>預設的 taskhub 名稱變更

在1.x 版中，如果未在 host.js中指定工作中樞名稱，則預設為 "DurableFunctionsHub"。 在2.x 版中，預設的工作中樞名稱現在是衍生自函式應用程式的名稱。 因此，如果您在升級至2.x 時未指定工作中樞名稱，則您的程式碼將會使用新的「工作中樞」，而且所有進行中的協調流程都不會再處理它們。 若要解決此情況，您可以將工作中樞名稱明確設定為 "DurableFunctionsHub" 的 v1. x 預設值，或者您可以遵循我們的[零停機部署指引](durable-functions-zero-downtime-deployment.md)，取得如何處理進行中協調流程的重大變更的詳細資料。

#### <a name="public-interface-changes-net-only"></a>公用介面變更（僅限 .NET）

在1.x 版中，Durable Functions 支援的各種_內容_物件都有抽象基類，供單元測試使用。 做為 Durable Functions 2.x 的一部分，這些抽象基類會取代為介面。

下表代表主要變更：

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 或 `IDurableClient` |
| `DurableOrchestrationContext` 或 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 或 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

在抽象基類包含虛擬方法的情況下，這些虛擬方法已由中定義的擴充方法所取代 `DurableContextExtensions` 。

#### <a name="functionjson-changes-javascript-and-c-script"></a>變更時 function.js（JavaScript 和 c # 腳本）

在 Durable Functions 1.x 中，協調流程用戶端系結會使用 `type` 的 `orchestrationClient` 。 2.x 版改為使用 `durableClient` 。

#### <a name="raise-event-changes"></a>引發事件變更

在 Durable Functions 1.x 中，呼叫「[引發事件](durable-functions-external-events.md#send-events)API」並指定不存在的實例會導致無訊息失敗。 從2.x 開始，引發事件到不存在的協調流程會導致例外狀況。
