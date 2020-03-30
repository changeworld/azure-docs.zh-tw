---
title: 在 Azure 宇宙 DB 中使用卡珊多拉 API 進行彈性擴展
description: 瞭解可用於縮放 Azure Cosmos DB Cassandra API 帳戶的選項及其優點/缺點
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474674"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>彈性縮放 Azure 宇宙 DB Cassandra API 帳戶

有多種選項可用於探索 Cassandra 的 Azure Cosmos DB API 的彈性特性。 要瞭解如何在 Azure Cosmos DB 中有效擴展，請務必瞭解如何預配適當數量的請求單位 （RU/s） 以考慮系統中的性能要求。 要瞭解有關請求單位詳細資訊，請參閱[請求單位](request-units.md)一文。 

對於 Cassandra API，您可以使用[.NET 和 JAVA SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api)檢索單個查詢的請求單元費用。 這有助於確定您需要在服務中預配的 RU/s 量。

![資料庫作業會取用要求單位](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>處理速率限制（429 個錯誤）

如果用戶端消耗的資源 （RU/s） 多於預配的金額，Azure Cosmos DB 將返回速率限制 （429） 錯誤。 Azure Cosmos DB 中的 Cassandra API 會將這些例外狀況轉譯成 Cassandra 原生通訊協定上的多載錯誤。 

如果系統對延遲不敏感，則使用重試來處理輸送量速率限制可能就足夠了。 有關如何使用 JAVA 中的[Cassandra 重試策略](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)的[Azure Cosmos DB 擴展](https://github.com/Azure/azure-cosmos-cassandra-extensions)，請參閱 JAVA[代碼示例](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)，瞭解如何透明地處理速率限制。 您還可以使用[Spark 擴展](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)來處理速率限制。

## <a name="manage-scaling"></a>管理縮放

如果需要最小化延遲，在 Cassandra API 中存在用於管理擴展和預配輸送量 （R） 的一系列選項：

* [通過使用 Azure 門戶手動](#use-azure-portal)
* [使用控制平面功能以程式設計方式](#use-control-plane)
* [將 CQL 命令與特定 SDK 一起使用，以程式設計方式](#use-cql-queries)
* [使用自動駕駛儀動態](#use-autopilot)

以下各節說明每種方法的優點和缺點。 然後，您可以決定最佳策略，以平衡系統的擴展需求、解決方案的總體成本和效率需求。

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>使用 Azure 門戶

您可以使用 Azure 門戶縮放 Azure Cosmos DB Cassandra API 帳戶中的資源。 要瞭解更多資訊，請參閱有關[在容器和資料庫上預配輸送量](set-throughput.md)的文章。 本文介紹了在 Azure 門戶中設置[資料庫](set-throughput.md#set-throughput-on-a-database)或[容器](set-throughput.md#set-throughput-on-a-container)級別的輸送量的相對好處。 這些文章中提到的術語"資料庫"和"容器"分別映射到 Cassandra API 的"鍵空間"和"表"。

此方法的優點是它是管理資料庫上的輸送量容量的一種簡單交鑰匙方法。 但是，缺點是在許多情況下，您的擴充方法可能需要某些級別的自動化才能既經濟又高性能。 下一節將解釋相關方案和方法。

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>使用控制平面

Azure Cosmos DB 的 Cassandra API 提供了使用各種控制平面功能以程式設計方式調整輸送量的功能。 有關指南和示例，請參閱[Azure 資源管理器](manage-cassandra-with-resource-manager.md)、[電源外殼](powershell-samples-cassandra.md)和[Azure CLI](cli-samples-cassandra.md)文章。

此方法的優點是，您可以基於計時器自動擴展或縮減資源，以考慮峰值活動或低活動週期。 請看[我們在此](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)的示例，瞭解如何使用 Azure 函數和 Powershell 實現此目的。

此方法的缺點是，您無法即時回應不可預測的不斷變化的規模需求。 相反，您可能需要利用系統中的應用程式上下文、用戶端/SDK 級別或使用[自動駕駛儀](provision-throughput-autopilot.md)。

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>使用帶有特定 SDK 的 CQL 查詢

您可以通過執行給定資料庫或容器的[CQL ALTER 命令](cassandra-support.md#keyspace-and-table-options)，使用代碼動態縮放系統。

此方法的優點是，它允許您以適合應用程式的自訂方式動態回應擴展需求。 使用這種方法，您仍然可以利用標準 RU/s 費用和費率。 如果您的系統規模需求大部分是可預測的（大約 70% 或更多），則使用帶有 CQL 的 SDK 可能比使用自動駕駛pilot更具成本效益。 此方法的缺點是，實現重試可能相當複雜，而速率限制可能會增加延遲。

## <a name="use-autopilot"></a><a id="use-autopilot"></a>使用自動駕駛儀

除了手動或程式設計的方式預配輸送量外，您還可以在自動駕駛模式下配置 Azure 宇宙容器。 自動駕駛模式將自動立即擴展到指定的 RU 範圍內的消費需求，而不會損害 SL。 要瞭解更多資訊，請參閱[在自動駕駛模式下創建 Azure Cosmos 容器和資料庫](provision-throughput-autopilot.md)一文。

此方法的優點是，它是管理系統中縮放需求的最簡單方法。 它保證不在**配置的 RU 範圍內**應用速率限制。 缺點是，如果系統中的縮放需求是可預測的，則與使用上述定制控制平面或 SDK 級別方法相比，Autopilot 可能是處理縮放需求的成本效益較低的方法。

## <a name="next-steps"></a>後續步驟

- 使用 JAVA 應用程式開始[創建 Cassandra API 帳戶、資料庫和表](create-cassandra-api-account-java.md)
