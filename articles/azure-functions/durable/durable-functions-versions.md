---
title: 持久函數版本概述 - Azure 函數
description: 瞭解持久功能版本。
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152885"
---
# <a name="durable-functions-versions-overview"></a>持久功能版本概述

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的擴充功能，可讓您在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。 如果您還不熟悉持久功能，請參閱[概述文檔](durable-functions-overview.md)。

## <a name="new-features-in-2x"></a>2.x 中的新功能

本節介紹在版本 2.x 中添加的持久函數的功能。

### <a name="durable-entities"></a>耐用實體

在持久函數 2.x 中，我們引入了一種新的[實體函數](durable-functions-entities.md)概念。

實體函式會定義用於讀取和更新一小段狀態 (稱為「持久性實體」**) 的作業。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」**) 的函式。 與協調器函數不同，實體函數沒有任何特定的代碼約束。 實體函式也會明確管理狀態，而不是透過控制流程來隱含表示狀態。

要瞭解更多資訊，請參閱[持久實體](durable-functions-entities.md)文章。

### <a name="durable-http"></a>耐用的 HTTP

在耐用功能 2.x 中，我們引入了一個新的[耐用 HTTP](durable-functions-http-features.md#consuming-http-apis)功能，允許您：

* 直接從業務流程函式呼叫 HTTP API（具有一些有文檔記錄的限制）。
* 實現用戶端 HTTP 202 狀態輪詢。
* 對[Azure 託管標識](../../active-directory/managed-identities-azure-resources/overview.md)的內置支援。

要瞭解更多資訊，請參閱[HTTP 功能](durable-functions-http-features.md#consuming-http-apis)一文。

## <a name="migrate-from-1x-to-2x"></a>從 1.x 遷移到 2.x

本節介紹如何將現有版本 1.x 持久函數遷移到版本 2.x 以利用新功能。

### <a name="upgrade-the-extension"></a>升級擴展

在專案中安裝[持久函數綁定擴展](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)的版本 2.x。 有關詳細資訊[，請參閱註冊 Azure 函數綁定擴展](../functions-bindings-register.md)。

### <a name="update-your-code"></a>更新代碼

持久功能 2.x 引入了幾個重大更改。 持久函數 1.x 應用程式不相容持久函數 2.x，無需更改代碼。 本節列出了將版本 1.x 函數升級到 2.x 時必須進行的某些更改。

#### <a name="hostjson-schema"></a>Host.json 架構

持久函數 2.x 使用新的主機.json 架構。 從 1.x 到 1.x 的主要更改包括：

* `"storageProvider"`（和`"azureStorage"`子節）用於特定于存儲的配置。
* `"tracing"`用於跟蹤和日誌記錄配置。
* `"notifications"`事件網格通知`"eventGrid"`配置的（和子部分）。

有關詳細資訊，請參閱[持久函數主機.json 參考文檔](durable-functions-bindings.md#durable-functions-2-0-host-json)。

#### <a name="default-taskhub-name-changes"></a>預設任務中心名稱更改

在版本 1.x 中，如果在 host.json 中未指定任務中心名稱，則將其預設為"持久功能Hub"。 在版本 2.x 中，預設任務中心名稱現在派生自函數應用的名稱。 因此，如果在升級到 2.x 時未指定任務中心名稱，則代碼將使用新任務中心運行，並且所有運行中的業務流程將不再具有應用程式處理它們。 要解決這個問題，您可以顯式將任務中心名稱設置為 v1.x 預設值"持久功能Hub"，也可以按照我們的[零停機時間部署指南](durable-functions-zero-downtime-deployment.md)瞭解如何處理在運行中業務流程的突發更改的詳細資訊。

#### <a name="public-interface-changes-net-only"></a>公共介面更改（僅限.NET）

在版本 1.x 中，持久函數支援的各種_上下文_物件具有抽象基類，用於單元測試。 作為持久函數 2.x 的一部分，這些抽象基類被介面替換。

下表表示主要更改：

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 或 `IDurableClient` |
| `DurableOrchestrationContext` 或 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 或 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

在抽象基類包含虛擬方法的情況下，這些虛擬方法已被 中`DurableContextExtensions`定義的擴充方法所取代。

#### <a name="functionjson-changes-javascript-and-c-script"></a>函數.json 更改（JavaScript 和 C# 腳本）

在持久函數 1.x 中，業務流程用戶端綁定使用`type``orchestrationClient`的 。 版本 2.x`durableClient`改為使用。
