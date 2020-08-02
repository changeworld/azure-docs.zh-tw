---
title: 在 Azure Cosmos DB 中彈性調整 Cassandra API
description: 了解可用來調整 Azure Cosmos DB Cassandra API 帳戶的選項及其優點/缺點
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: 26f635525afea289e2e791b802478040a7851eee
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486505"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>彈性調整 Azure Cosmos DB Cassandra API 帳戶

有各種不同的選項可用來探索適用於 Cassandra 的 Azure Cosmos DB API 的彈性特質。 若要了解如何在 Azure Cosmos DB 中有效進行調整，請務必了解如何佈建適當數量的要求單位 (RU/秒)，以因應系統中的效能需求。 若要深入了解要求單位，請參閱[要求單位](request-units.md)一文。 

針對 Cassandra API，您可以使用 [.NET 和 Java SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api) 來擷取個別查詢的要求單位費用。 這有助於判斷您需要在服務中佈建的 RU/秒數量。

:::image type="content" source="./media/request-units/request-units.png" alt-text="資料庫作業會取用要求單位" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>處理速率限制 (429 錯誤)

如果用戶端所耗用的資源數超過您所佈建的數量 (RU/秒)，Azure Cosmos DB 將會傳回速率受到限制 (429) 錯誤。 Azure Cosmos DB 中的 Cassandra API 會將這些例外狀況轉譯成 Cassandra 原生通訊協定上的多載錯誤。 

如果您的系統較不受延遲影響，則使用重試來處理輸送量速率限制可能即已足夠。 請參閱 [Java 程式碼範例](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)，以了解如何使用 Java 中的 [Cassandra 重試原則 的](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) [Azure Cosmos DB 延伸模組](https://github.com/Azure/azure-cosmos-cassandra-extensions)，以透明的方式處理速率限制。 您也可以使用 [Spark 延伸模組](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) 來處理速率限制。

## <a name="manage-scaling"></a>管理調整

如果您需要將延遲降到最低，有一系列選項可用來管理 Cassandra API 中的調整和佈建輸送量 (RU)：

* [使用 Azure 入口網站手動執行](#use-azure-portal)
* [使用控制平面功能以程式設計方式執行](#use-control-plane)
* [使用 CQL 命令搭配特定的 SDK 以程式設計方式執行](#use-cql-queries)
* [使用自動調整動態執行](#use-autoscale)

以下各節說明每種方法的優缺點。 接著，您即可決定在系統的調整需求、整體成本，與解決方案的效能需求之間取得平衡的最佳策略。

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>使用 Azure 入口網站

您可以使用 Azure 入口網站來調整 Azure Cosmos DB Cassandra API 帳戶中的資源。 若要深入了解，請參閱[在容器和資料庫上佈建輸送量](set-throughput.md)的相關文章。 本文說明在 Azure 入口網站中的[資料庫](set-throughput.md#set-throughput-on-a-database)或[容器](set-throughput.md#set-throughput-on-a-container)層級設定輸送量的相對優勢。 這些文章中提到的「資料庫」和「容器」詞彙，分別對應於 Cassandra API 的 "keyspace" 和「資料表」。

此方法的優點是，這在管理資料庫的輸送量容量時，是較直截了當的方式。 但其缺點是，在許多情況下，您進行調整的方法可能需要相當程度的自動化，以符合成本效益並達到高效能。 以下幾節將說明相關的案例和方法。

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>使用控制平面

Azure Cosmos DB 的 Cassandra API 可讓您使用我們的各種控制平面功能，以程式設計方式調整輸送量。 如需指引和範例，請參閱 [Azure Resource Manager](manage-cassandra-with-resource-manager.md)、[PowerShell](powershell-samples.md) 和 [Azure CLI](cli-samples.md) 等文章。

此方法的優點是，您可以根據計時器來自動擴大或縮小資源，以因應尖峰活動或活動較少的期間。 請參閱[這裡](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)的範例，以了解如何使用 Azure Functions 和 PowerShell 來完成此操作。

此方法的缺點是，您無法即時回應無法預測的變動調整需求。 在此情況下，您可能需要在用戶端/SDK 層級使用系統中的應用程式內容，或使用[自動調整](provision-throughput-autoscale.md)。

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>搭配使用 CQL 查詢與特定 SDK

您可以對指定的資料庫或容器執行 [CQL ALTER 命令](cassandra-support.md#keyspace-and-table-options)，以使用程式碼動態調整系統。

此方法的優點是，您將能夠以適用於應用程式的動態和自訂方式因應調整需求。 使用此方法時，您仍可利用標準 RU/秒的費用和速率。 如果您的系統調整需求大多是可預測的 (約 70% 或以上)，則搭配使用 SDK 與 CQL 可能會是比使用自動調整更符合成本效益的自動調整方法。 此方法的缺點是，在速率限制可能增加延遲的情況下，實作重試的程序可能會很複雜。

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>使用自動調整佈建的輸送量

除了以標準 (手動) 或程式設計方式佈建輸送量以外，您也可以在自動調整佈建的輸送量中設定 Azure Cosmos 容器。 自動調整會自動並立即在指定的 RU 範圍內進行調整，以符合您的使用量需求，而不會影響到 SLA。 若要深入了解，請參閱[在自動調整中建立 Azure Cosmos 容器和資料庫](provision-throughput-autoscale.md)一文。

此方法的優點是，您可以透過最簡單的方式在系統中管理調整需求。 此方法絕不會**在設定的 RU 範圍內**套用速率限制。 其缺點是，如果您系統中的調整需求是可預測的，則相較於使用上述定制的控制平面或 SDK 層級方法，使用自動調整來處理調整需求的成本效益可能會較低。

若要使用 CQL 設定或改變自動調整的最大輸送量 (RU)，請使用下列程式碼 (請據以取代 keyspace/資料表名稱)：

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>後續步驟

- 開始使用 Java 應用程式來[建立 Cassandra API 帳戶、資料庫及資料表](create-cassandra-api-account-java.md)。
