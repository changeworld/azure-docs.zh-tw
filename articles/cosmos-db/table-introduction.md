---
title: Azure Cosmos DB 資料表 API 簡介
description: 了解如何透過 Azure Tables API，使用 Azure Cosmos DB 來儲存及查詢大量機碼值資料 (低延遲)。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 01/08/2021
ms.author: sngun
ms.openlocfilehash: 1cf3bf30b37a09b5dfe94bf1e754a7f8e9dcd82c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045660"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB 簡介：資料表 API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[Azure Cosmos DB](introduction.md) 提供的表格 API 適用於針對 Azure 資料表儲存體所撰寫、且需要進階功能的應用程式，例如：

* [周全的全域發佈](distribute-data-globally.md)。
* 全球[專用輸送量](partitioning-overview.md) (使用佈建的輸送量時)。
* 99 百分位數的單一數字毫秒延遲。
* 保證高可用性。
* 自動次要索引。

針對 Azure 資料表儲存體所撰寫的應用程式可使用資料表 API (不變更程式碼) 來移轉至 Azure Cosmos DB，並且利用進階功能。 資料表 API 具有適用於 .NET、Java、Python 和 Node.js 的用戶端 SDK。

> [!NOTE]
> [無伺服器容量模式](serverless.md)現已可在 Azure Cosmos DB 的 資料表 API 上使用。

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 現為維護模式，且即將被取代。 請升級至新的.NET Standard 程式庫[Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)，才能繼續取得資料表 API 支援的最新功能。

## <a name="table-offerings"></a>表格供應項目
如果您目前是使用 Azure 資料表儲存體，移至 Azure Cosmos DB 資料表 API 可獲得下列好處：

| 功能 | Azure 資料表儲存體 | Azure Cosmos DB 資料表 API |
| --- | --- | --- |
| Latency | 快速，但延遲沒有上限。 | 一位數毫秒的讀取和寫入延遲，並在世界各地支援任何規模的 <10 毫秒延遲讀取和寫入 (第 99 個百分位數)。 |
| Throughput | 變數輸送量模型。 資料表每秒 20,000 個作業的延展性限制。 | 高延展性且[每個資料表都有專用的保留輸送量](request-units.md) (由 SLA 支援)。 帳戶沒有輸送量上限，而且支援每個資料表每秒 > 1 千萬個作業。 |
| 全球發佈 | 具有一個選擇性高可用性可讀取次要讀取區域的單一區域。 | [周全的全域發佈](distribute-data-globally.md)介於 1 到任何數量的區域。 隨時隨地在世界各地支援[自動和手動容錯移轉](high-availability.md)。 可讓任何區域接受寫入作業的多重寫入區域。 |
| 編製索引 | PartitionKey 和 RowKey 只有主要索引。 沒有次要索引。 | 根據預設，對所有屬性自動執行完整的編製索引，但不進行索引管理。 |
| 查詢 | 查詢執行作業會使用主索引鍵的索引，要不然會進行掃描。 | 查詢可以利用自動編製屬性的索引，加快查詢速度。 |
| 一致性 | 主要區域內的強式。 次要區域內的事件式。 | [五個定義完善的一致性層級](consistency-levels.md)，可以您應用程式的需求作為基礎，進行可用性、延遲、輸送量及一致性的取捨。 |
| 定價 | 以使用量為基礎。 | 同時適用於[以使用量為基礎](serverless.md)和[已佈建的容量](set-throughput.md)模式。 |
| SLA | 視複寫策略而定，99.9% 到 99.99% 的可用性。 | 單一區域帳戶上 99.999% 的讀取可用性、99.99% 的寫入可用性，以及多區域帳戶上 99.999% 的寫入可用性。 涵蓋可用性、延遲、輸送量和一致性的[完整 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。 |

## <a name="get-started"></a>開始使用

在 [Azure 入口網站](https://portal.azure.com)中建立 Azure Cosmos DB 帳戶。 然後開始使用我們的[使用 .NET 快速入門資料表 API](create-table-dotnet.md)。 

> [!IMPORTANT]
> 如果您在預覽期間建立了資料表 API 帳戶，請建立[新表格 API 帳戶](create-table-dotnet.md#create-a-database-account)以使用正式推出的資料表 API SDK。
>

## <a name="next-steps"></a>後續步驟

以下是可讓您快速入門的一些指標：
* [使用資料表 API 來建置 .NET 應用程式](create-table-dotnet.md)
* [在 .NET 中利用資料表 API 進行開發](tutorial-develop-table-dotnet.md)
* [使用資料表 API 來查詢資料表資料](tutorial-query-table.md)
* [了解如何使用資料表 API 來設定 Azure Cosmos DB 全域發佈](tutorial-global-distribution-table.md)
* [Azure Cosmos DB 資料表 .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB 資料表 .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB 資料表 Java SDK](table-sdk-java.md)
* [Azure Cosmos DB 資料表 Node.js SDK](table-sdk-nodejs.md)
* [適用於 Python 的 Azure Cosmos DB 資料表](table-sdk-python.md)
