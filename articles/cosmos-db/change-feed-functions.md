---
title: 如何搭配 Azure Functions 使用 Azure Cosmos DB 變更摘要
description: 使用 Azure 函數連接到 Azure 宇宙資料庫更改源。 稍後，您可以創建在每個新事件上觸發的被動 Azure 函數。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851362"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>具有 Azure Cosmos DB 和 Azure 函數的無伺服器事件體系結構

Azure 函數提供了連接到[更改源](change-feed.md)的最簡單方法。 您可以創建小型無功 Azure 函數，這些函數將在 Azure Cosmos 容器的更改源中的每個新事件上自動觸發。

![使用 Cosmos DB 的 Azure 函數觸發器的無伺服器事件函數](./media/change-feed-functions/functions.png)

使用[Cosmos DB 的 Azure 函數觸發器](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)，可以利用[更改源處理器](./change-feed-processor.md)的縮放和可靠的事件檢測功能，而無需維護任何[輔助基礎結構](./change-feed-processor.md)。 只關注 Azure 函數的邏輯，而不必擔心事件源管道的其餘部分。 您甚至可以將觸發器與任何其他 Azure[函數綁定混合](../azure-functions/functions-triggers-bindings.md#supported-bindings)。

> [!NOTE]
> 目前，Cosmos DB 的 Azure 函數觸發器僅支援與核心 （SQL） API 一起使用。

## <a name="requirements"></a>需求

要實現基於伺服器事件的無伺服器流，您需要：

* **受監視的容器**：受監視的容器是被監視的 Azure Cosmos 容器，它存儲從中生成更改源的資料。 任何插入、對受監視容器的更新都反映在容器的更改源中。
* **租約容器**：租約容器在多個動態無伺服器 Azure 函數實例中維護狀態，並啟用動態縮放。 此租約容器可以手動或自動創建由 Cosmos DB 的 Azure 函數觸發器。 要自動創建租約容器，請設置[配置](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中的*CreateLease 集合IfNot存在*標誌。 分區租約容器需要具有`/id`分區金鑰定義。

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>為宇宙資料庫創建 Azure 函數觸發器

現在，所有 Azure 函數 IDE 和 CLI 集成都支援使用 Cosmos DB 的 Azure 函數觸發器創建 Azure 函數：

* [視覺化工作室擴展](../azure-functions/functions-develop-vs.md)為視覺工作室使用者。
* [視覺化工作室代碼擴展](/azure/javascript/tutorial-vscode-serverless-node-01)為視覺化工作室代碼使用者。
* 最後[，核心 CLI 工具](../azure-functions/functions-run-local.md#create-func)可進行跨平臺 IDE 不可知體驗。

## <a name="run-your-trigger-locally"></a>在本地運行觸發器

您可以使用[Azure Cosmos DB 模擬器](./local-emulator.md)[在本地](../azure-functions/functions-develop-local.md)運行 Azure 函數，以創建和開發基於伺服器的事件流，而無需 Azure 訂閱或產生任何成本。

如果要在雲中測試即時方案，可以[免費試用 Cosmos DB，](https://azure.microsoft.com/try/cosmosdb/)而無需任何信用卡或 Azure 訂閱。

## <a name="next-steps"></a>後續步驟

現在，您可以在以下文章中繼續瞭解有關更改源的更多資訊：

* [變更摘要的概觀](change-feed.md)
* [讀取變更摘要的方式](read-change-feed.md)
* [使用更改饋送處理器庫](change-feed-processor.md)
* [如何使用變更摘要處理器程式庫](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)
