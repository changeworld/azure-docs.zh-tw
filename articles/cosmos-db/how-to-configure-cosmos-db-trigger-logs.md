---
title: 使用 Cosmos DB 的 Azure Functions 觸發程式來設定和讀取記錄
description: 了解在使用 Azure Functions 的 Cosmos DB 觸發程序時，如何將記錄公開至 Azure Functions 記錄管線
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 31c9f9d94acab7322cf11d084e4010f2defd6f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85261693"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>在使用 Azure Functions 的 Cosmos DB 觸發程序時如何設定和讀取記錄

本文說明如何設定 Azure Functions 環境，以將 Cosmos DB 記錄的 Azure Functions 觸發程式傳送至設定的 [監視解決方案](../azure-functions/functions-monitoring.md)。

## <a name="included-logs"></a>包含的記錄檔

Azure Functions 的 Cosmos DB 觸發程序會在內部使用[變更摘要處理器程式庫](./change-feed-processor.md)，該程式庫會產生一組可用來監視[疑難排解用途](./troubleshoot-changefeed-functions.md)內部作業的健康情況記錄。

健康情況記錄會說明在進行負載平衡情況下或在初始化期間，Azure Functions 的 Cosmos DB 觸發程序嘗試進行作業時的運作狀況。

## <a name="enabling-logging"></a>啟用記錄

若要在使用 Azure Functions 的 Cosmos DB 觸發程序時啟用記錄，請找出您的 Azure Functions 專案或 Azure 函式應用程式中的 `host.json` 檔案，然後[設定所需的記錄層級](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson)。 您必須啟用 `Host.Triggers.CosmosDB` 的追蹤功能，如下列範例所示：

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

## <a name="query-the-logs"></a>查詢記錄檔

在 [Azure Application Insights 的 Analytics](../azure-monitor/app/analytics.md) 中執行下列查詢，以查詢 Azure Functions 的 Cosmos DB 觸發程序所產生的記錄：

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>後續步驟

* 在您的 Azure Functions 應用程式中[啟用監視](../azure-functions/functions-monitoring.md)。
* 了解在使用 Azure Functions 的 Cosmos DB 觸發程序時，如何[診斷常見問題並進行疑難排解](./troubleshoot-changefeed-functions.md)。
