---
title: Cosmos DB advanced configuration 的 Azure Functions 觸發程式
description: 瞭解如何設定 Cosmos DB 的 Azure Functions 觸發程式所使用的記錄和連接原則
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 8e2b74f95f24e107cb395686fe6dd1c96566bb08
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283833"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>如何使用 Cosmos DB 的 Azure Functions 觸發程式來設定記錄和連接

本文說明使用 Cosmos DB 的 Azure Functions 觸發程式時，您可以設定的 advanced configuration 選項。

## <a name="enabling-trigger-specific-logs"></a>啟用觸發程式特定記錄檔

Azure Functions 的 Cosmos DB 觸發程序會在內部使用[變更摘要處理器程式庫](./change-feed-processor.md)，該程式庫會產生一組可用來監視[疑難排解用途](./troubleshoot-changefeed-functions.md)內部作業的健康情況記錄。

健康情況記錄會說明在進行負載平衡情況下或在初始化期間，Azure Functions 的 Cosmos DB 觸發程序嘗試進行作業時的運作狀況。

### <a name="enabling-logging"></a>啟用記錄

若要在使用 Azure Functions 的 Cosmos DB 觸發程序時啟用記錄，請找出您的 Azure Functions 專案或 Azure 函式應用程式中的 `host.json` 檔案，然後[設定所需的記錄層級](../azure-functions/functions-monitoring.md#log-levels-and-categories)。 啟用的追蹤，  `Host.Triggers.CosmosDB` 如下列範例所示：

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

在 Azure Function 中部署更新的設定之後，您會看到追蹤功能中包含 Azure Functions 的 Cosmos DB 觸發程序記錄。 您可以在「類別」** `Host.Triggers.CosmosDB` 底下，自己設定的記錄提供者中檢視記錄。

### <a name="query-the-logs"></a>查詢記錄檔

在 [Azure Application Insights 的 Analytics](../azure-monitor/app/analytics.md) 中執行下列查詢，以查詢 Azure Functions 的 Cosmos DB 觸發程序所產生的記錄：

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>設定連接原則

有兩種連線模式 - 直接模式和閘道模式。 若要深入瞭解這些連接模式，請參閱 [連接模式](sql-sdk-connection-modes.md) 的文章。 依預設會使用**閘道**來建立 Azure Functions 的 Cosmos DB 觸發程序的所有連線。 不過，它可能不是效能導向案例的最佳選項。

### <a name="changing-the-connection-mode-and-protocol"></a>變更連接模式和通訊協定

有兩個重要組態設定可用來設定用戶端連線原則 - **連線模式**和**連線通訊協定**。 您可以變更 Azure Functions 的 Cosmos DB 觸發程序所使用的預設連線模式和通訊協定，以及所有的 [Azure Cosmos DB 繫結](../azure-functions/functions-bindings-cosmosdb-v2-output.md)。 若要變更預設設定，您需要在您的 Azure Functions 專案或 Azure Functions 應用程式中找出 `host.json` 檔案，並新增下列[額外設定](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)：

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

其中 `connectionMode` 必須具有所需的連連線模式 (直接或閘道) 和 `protocol` 所需的連線通訊協定 (Tcp 或 Https)。 

如果您的 Azure Functions 專案使用 Azure Functions V1 執行階段，組態會有些許名稱差異，您應該使用 `documentDB` 而不是 `cosmosDB`：

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> 使用 Azure Functions 取用方案主控方案時，每個執行個體都有可維護的通訊端連線數量限制。 使用 Direct / TCP 模式時，會依設計建立多個連線並可叫用[取用方案限制](../azure-functions/manage-connections.md#connection-limit)，在此情況下您可以使用閘道模式或在 [App Service 模式](../azure-functions/functions-scale.md#app-service-plan)中執行 Azure Functions。

## <a name="next-steps"></a>後續步驟

* [Azure Functions 中的連線限制](../azure-functions/manage-connections.md#connection-limit)
* 在您的 Azure Functions 應用程式中[啟用監視](../azure-functions/functions-monitoring.md)。
* 了解在使用 Azure Functions 的 Cosmos DB 觸發程序時，如何[診斷常見問題並進行疑難排解](./troubleshoot-changefeed-functions.md)。