---
title: 存取 Azure Cosmos DB 中的變更摘要
description: 本文說明可用來在 Azure Cosmos DB 中讀取和存取變更摘要的不同選項。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688122"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>讀取 Azure Cosmos DB 變更摘要

您可以使用下列任何選項來存取 Azure Cosmos DB 變更摘要：

* 使用 Azure Functions
* 使用變更摘要處理器程式庫
* 使用 Azure Cosmos DB SQL API SDK

## <a name="using-azure-functions"></a>使用 Azure Functions

Azure Functions 是最簡單且建議的選項。 為 Cosmos DB 創建 Azure 函數觸發器時，可以選擇要連接的容器，並且每當對容器進行更改時，就會觸發 Azure 函數。 您可以使用 Azure Functions 入口網站、Azure Cosmos DB 入口網站，或透過 SDK 以程式設計方式建立觸發程序。 Visual Studio 和 VS Code 提供撰寫 Azure Functions 的支援，您甚至可以使用 Azure Functions CLI 進行跨平台開發。 您可以在電腦上撰寫和偵錯程式碼，然後只需按一下即可部署該功能。 若要深入了解，請參閱[使用 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)和[搭配使用變更摘要與 Azure Functions](change-feed-functions.md) 文章。

## <a name="using-the-change-feed-processor-library"></a>使用變更摘要處理器程式庫

變更摘要處理器程式庫會隱藏複雜性，但仍然可讓您完全控制變更摘要。 此程式庫依循觀察者模式，您的處理函式會由程式庫呼叫。 如果您有高輸送量的變更摘要，則可將多個用戶端具現化，以讀取變更摘要。 由於您目前使用的是變更摘要處理器程式庫，因此它會自動將負載分散到不同的用戶端，您無須實作此邏輯。 程式庫會處理所有的複雜作業。 如果您想要擁有自己的負載平衡器，您可以實作 `IPartitionLoadBalancingStrategy`，自訂處理變更摘要的分割區策略。 若要深入了解，請參閱[使用變更摘要處理器程式庫](change-feed-processor.md)。

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>使用 Azure Cosmos DB SQL API SDK

使用 SDK 時，您會取得變更摘要的低階控制。 您可以管理檢查點、訪問特定的邏輯分區金鑰等。如果有多個讀取器，則可以使用`ChangeFeedOptions`將讀取負載分發到不同的執行緒或不同的用戶端。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>更改卡珊多拉和蒙戈DB的 API 中源

更改源功能在 MongoDB API 中顯示為更改流，在 Cassandra API 中使用謂詞顯示。 要瞭解有關 MongoDB API 的實現詳細資訊的詳細資訊，請參閱[MongoDB 的 Azure Cosmos DB API 中的更改流](mongodb-change-streams.md)。

本機 Apache Cassandra 提供更改資料捕獲 （CDC），這是一種機制，用於標記存檔的特定表，以及在達到 CDC 日誌的可配置磁片大小後拒絕對這些表的寫入。 Cassandra 的 Azure Cosmos DB API 中的更改源功能增強了通過 CQL 使用謂詞查詢更改的能力。 要瞭解有關實現詳細資訊的詳細資訊，請參閱[Cassandra 的 Azure Cosmos DB API 中的更改源](cassandra-change-feed.md)。

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要：

* [變更摘要的概觀](change-feed.md)
* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)
* [使用更改饋送處理器庫](change-feed-processor.md)
