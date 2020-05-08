---
title: 存取 Azure Cosmos DB 中的變更摘要
description: 本文說明在 Azure Cosmos DB 中可供讀取和存取變更摘要的不同選項。
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982491"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>讀取 Azure Cosmos DB 變更摘要

您可以使用下列任何選項來存取 Azure Cosmos DB 變更摘要：

* 使用 Azure Functions
* 使用變更摘要處理器
* 使用 Azure Cosmos DB SQL API SDK
* 使用變更摘要提取模型（預覽）

## <a name="using-azure-functions"></a>使用 Azure Functions

Azure Functions 是最簡單且建議的選項。 當您建立 Cosmos DB 的 Azure Functions 觸發程式時，您可以選取要連線的容器，並在每次容器變更時觸發 Azure 函式。 您可以使用 Azure Functions 入口網站、Azure Cosmos DB 入口網站，或透過 SDK 以程式設計方式建立觸發程序。 Visual Studio 和 VS Code 提供撰寫 Azure Functions 的支援，您甚至可以使用 Azure Functions CLI 進行跨平台開發。 您可以在電腦上撰寫和偵錯程式碼，然後只需按一下即可部署該功能。 若要深入了解，請參閱[使用 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)和[搭配使用變更摘要與 Azure Functions](change-feed-functions.md) 文章。

## <a name="using-the-change-feed-processor"></a>使用變更摘要處理器

變更摘要處理器會隱藏複雜性，而且仍然可讓您完全控制變更摘要。 此程式庫依循觀察者模式，您的處理函式會由程式庫呼叫。 如果您有高輸送量的變更摘要，則可將多個用戶端具現化，以讀取變更摘要。 由於您目前使用的是變更摘要處理器程式庫，因此它會自動將負載分散到不同的用戶端，您無須實作此邏輯。 程式庫會處理所有的複雜作業。 若要深入瞭解，請參閱[使用變更摘要處理器](change-feed-processor.md)。 變更摘要處理器是[AZURE COSMOS DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)的一部分。

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>使用 Azure Cosmos DB SQL API SDK

使用 SDK 時，您會取得變更摘要的低階控制。 您可以管理檢查點、存取特定的邏輯分割區索引鍵等。如果您有多個讀取器，可以`ChangeFeedOptions`使用將讀取負載分配至不同的執行緒或不同的用戶端。

## <a name="using-the-change-feed-pull-model"></a>使用變更摘要提取模型

[變更摘要提取模型](change-feed-pull-model.md)可讓您依自己的步調取用變更摘要，以及使用 FeedRanges 來平行處理變更。 FeedRange 跨越資料分割索引鍵值的範圍。 使用變更摘要提取模型，也很容易處理特定分割區索引鍵的變更。

> [!NOTE]
> 變更摘要提取模型目前僅在[Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)中為預覽狀態。 預覽尚未提供給其他 SDK 版本使用。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>變更 Cassandra 和 MongoDB Api 中的摘要

變更摘要功能會呈現為 MongoDB API 中的變更資料流程，並在 Cassandra API 中使用述詞進行查詢。 若要深入瞭解 MongoDB API 的執行詳細資料，請參閱[適用于 mongodb 的 AZURE COSMOS DB API 中的變更串流](mongodb-change-streams.md)。

原生 Apache Cassandra 提供變更資料捕獲（CDC），這是一種機制，用來標示特定資料表的封存，並在達到可設定的 CDC 記錄大小磁片時拒絕這些資料表的寫入。 Azure Cosmos DB API for Cassandra 中的變更摘要功能可增強透過 CQL 查詢具有述詞之變更的能力。 若要深入瞭解執行詳細資料，請參閱[Cassandra 的 AZURE COSMOS DB API 中的變更](cassandra-change-feed.md)摘要。

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要：

* [變更摘要的概觀](change-feed.md)
* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)
* [使用變更摘要處理器程式庫](change-feed-processor.md)
