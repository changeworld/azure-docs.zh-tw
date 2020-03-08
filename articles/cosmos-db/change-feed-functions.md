---
title: 如何搭配 Azure Functions 使用 Azure Cosmos DB 變更摘要
description: 使用 Azure Functions 連接到 Azure Cosmos DB 變更摘要。 之後，您可以建立在每個新事件上觸發的被動 Azure 函式。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851362"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>具有 Azure Cosmos DB 和 Azure Functions 的無伺服器事件架構

Azure Functions 提供最簡單的方式來連接到[變更](change-feed.md)摘要。 您可以建立小型的被動 Azure Functions，將會在 Azure Cosmos 容器的變更摘要中的每個新事件上自動觸發。

![以無伺服器事件為基礎的函式使用 Cosmos DB 的 Azure Functions 觸發程式](./media/change-feed-functions/functions.png)

使用[Cosmos DB 的 Azure Functions 觸發程式](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)，您可以利用[變更摘要處理器](./change-feed-processor.md)的調整和可靠的事件偵測功能，而不需要維護任何背景[工作基礎結構](./change-feed-processor.md)。 只需專注于 Azure 函式的邏輯，而不需擔心事件來源管線的其餘部分。 您甚至可以將觸發程式與任何其他[Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings)系結混合在一起。

> [!NOTE]
> 目前，僅支援將 Cosmos DB 的 Azure Functions 觸發程式用於 Core （SQL） API。

## <a name="requirements"></a>需求

若要執行無伺服器事件型流程，您需要：

* **受監視的容器**：受監視的容器是受監視的 Azure Cosmos 容器，它會儲存產生變更摘要的資料。 受監視容器的任何插入、更新都會反映在容器的變更摘要中。
* **租用容器**：租用容器會維護多個和動態無伺服器 Azure 函式實例之間的狀態，並啟用動態調整。 Cosmos DB 的 Azure Functions 觸發程式可以手動或自動建立此租用容器。 若要自動建立租用容器，[請在設定](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中設定*CreateLeaseCollectionIfNotExists*旗標。 分割的租用容器必須要有 `/id` 的分割區索引鍵定義。

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>建立 Cosmos DB 的 Azure Functions 觸發程式

在所有 Azure Functions IDE 和 CLI 整合中，現在都支援使用 Cosmos DB 的 Azure Functions 觸發程式來建立 Azure 函數：

* Visual Studio 使用者的[Visual Studio 延伸](../azure-functions/functions-develop-vs.md)模組。
* Visual Studio Code 使用者的[Visual Studio Code 延伸](/azure/javascript/tutorial-vscode-serverless-node-01)模組。
* 最後，是[核心 CLI 工具](../azure-functions/functions-run-local.md#create-func)，適用于跨平臺的 IDE 中立體驗。

## <a name="run-your-trigger-locally"></a>在本機執行您的觸發程式

您可以使用[Azure Cosmos DB 模擬器](./local-emulator.md)在[本機執行 Azure](../azure-functions/functions-develop-local.md)函式，以在沒有 Azure 訂用帳戶的情況下，建立和開發無伺服器事件為基礎的流程，或產生任何成本。

如果您想要在雲端中測試即時案例，您可以[免費試用 Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) ，而不需要任何信用卡或 Azure 訂用帳戶。

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入瞭解變更摘要：

* [變更摘要的概觀](change-feed.md)
* [讀取變更摘要的方式](read-change-feed.md)
* [使用變更摘要處理器程式庫](change-feed-processor.md)
* [如何使用變更摘要處理器程式庫](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)
