---
title: 為 Cosmos DB 建立多個獨立的 Azure Functions 觸發程式
description: 了解如何設定多個獨立的「Azure Functions 的 Cosmos DB 觸發程序」，以建立事件驅動的架構。
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 32b680acdee29bf97a0e132fee93d5fee3377245
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77604952"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>建立多個 Azure Functions 的 Cosmos DB 觸發程序

本文說明如何將多個「Azure Functions 的 Cosmos DB 觸發程序」設定為以平行方式運作並獨立回應變更。

![以無伺服器事件為基礎的 Functions，使用 Azure Functions 的 Cosmos DB 觸發程序，並共用一個租用容器](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>以事件為基礎的架構需求

使用 [Azure Functions](../azure-functions/functions-overview.md) 建置無伺服器架構時，[建議](../azure-functions/functions-best-practices.md#avoid-long-running-functions)您建立一起運作的小型函式集，而不是大量長時間執行的函式。

當您使用 [Azure Functions 的 Cosmos DB 觸發程序](./change-feed-functions.md)建置以事件為基礎的無伺服器流程時，您會遇到以下情況：每當特定 [Azure Cosmos 容器](./databases-containers-items.md#azure-cosmos-containers)中有新事件時，您就想執行多個動作。 如果您想要觸發的動作彼此獨立，則理想的解決方式是為您想執行的**每個動作各建立一個 Azure Functions 的 Cosmos DB 觸發程序**，且全都接聽相同 Azure Cosmos 容器的變更。

## <a name="optimizing-containers-for-multiple-triggers"></a>最佳化多個觸發程序的容器

考慮到「Azure Functions 的 Cosmos DB 觸發程序」的需求**，我們需要第二個容器來儲存狀態，該容器也稱為「租用容器」**。 這表示每個 Azure 函式都需要個別的租用容器嗎？

在此，您有兩個選項：

* **針對每個函式建立一個租用容器**：除非您使用的是[共用輸送量資料庫](./set-throughput.md#set-throughput-on-a-database)，否則此方法會轉譯為額外成本。 請記住，容器層級的最小輸送量為 400 個[要求單位](./request-units.md)，而在租用容器的案例中，只會用來檢查進度及維護狀態。
* 有**一個租用容器，並將它共用**于您所有的函式：第二個選項可讓您更有效地使用容器上已布建的要求單位，因為它可讓多個 Azure Functions 共用並使用相同的布建輸送量。

本文的目標是引導您完成第二個選項。

## <a name="configuring-a-shared-leases-container"></a>設定共用租用容器

若要設定共用的租用容器，您必須對觸發程序進行的唯一額外設定就是新增 `LeaseCollectionPrefix` [屬性](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) (若使用 C#) 或 `leaseCollectionPrefix` [屬性](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) (若使用 JavaScript)。 屬性值應該是該特定觸發程序的邏輯描述項。

例如，如果您有三個觸發程序：一個會傳送電子郵件、一個會進行彙總以建立具體化檢視，還有一個會將變更傳送至另一個儲存體以供稍後分析，您可以將 `LeaseCollectionPrefix` 為 "emails" 指派給第一個、將其值為 "materialized" 指派給第二個，以及將其值為 "analytics" 指派給第三個。

重點是這三個觸發程序**可以使用相同的租用容器組態** (帳戶、資料庫和容器名稱)。

在 C# 中使用 `LeaseCollectionPrefix` 屬性的非常簡單程式碼範例，如下所示：

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

而在 JavaScript 中，您可以使用 `leaseCollectionPrefix` 屬性在 `function.json` 檔案上套用組態：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> 一律監視在您的共用租用容器上佈建的要求單位。 共用該容器的每個觸發程序都會增加輸送量平均耗用量，因此當您增加使用容器的 Azure 函式數目時，可能需要增加佈建的輸送量。

## <a name="next-steps"></a>後續步驟

* 請參閱 [Azure Functions 的 Cosmos DB 觸發程序](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)的完整組態
* 請檢查擴充的[範例清單](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)以取得所有語言。
* 請瀏覽 [GitHub 存放庫](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios)中 Azure Cosmos DB 與 Azure Functions 的無伺服器訣竅，以取得更多範例。