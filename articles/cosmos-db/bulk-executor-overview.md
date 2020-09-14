---
title: Azure Cosmos DB 大量執行程式程式庫概觀
description: 在 Azure Cosmos DB 中透過大量執行程式程式庫所提供的大量匯入和大量更新 API，來執行大量作業。
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 5d1251813486d4b7406f89ac9c09c49f985fa205
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055277"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB 大量執行程式程式庫概觀
 
Azure Cosmos DB 是一種快速且具有彈性的全域分散式資料庫服務，其彈性擴增的設計可支援： 

* 大型讀取和寫入輸送量 (每秒百萬個作業)。  
* 儲存大量 (數百 TB 或更多) 且可預測延遲毫秒數的交易和作業資料。  

大量執行程式程式庫可協助您利用這個大量輸送量和儲存體。 大量執行程式程式庫可讓您在 Azure Cosmos DB 中透過大量匯入和大量更新 API 來執行大量作業。 您可以在下列各節中深入了解大量執行程式程式庫的功能。 

> [!NOTE] 
> 目前，大量執行程式程式庫支援匯入和更新作業，而且僅有 Azure Cosmos DB SQL API 和 Gremlin API 帳戶可支援此程式庫。

> [!IMPORTANT]
> [無伺服器](serverless.md)帳戶目前不支援大量執行程式程式庫。 建議您在 .NET 上使用在 V3 版本的 SDK 中提供的 [大量支援](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) 。
 
## <a name="key-features-of-the-bulk-executor-library"></a>大量執行程式程式庫的主要功能  
 
* 它會大幅減少佔滿輸送量 (配置到容器) 的必要用戶端運算資源。 比起平行寫入資料而佔滿用戶端機器 CPU 的多執行緒應用程式，使用大量匯入 API 寫入資料的單一執行緒應用程式，可達到 10 倍多的寫入輸送量。  

* 它以概念來簡單化撰寫應用程式邏輯的沉悶工作，有效率地在程式庫中處理要求速率限制、要求逾時和其他暫時性例外狀況。  

* 它提供簡單的機制，讓執行大量作業的應用程式向外擴充。在 Azure VM 上執行的單一大量執行程式實例可以取用大於 500K RU/秒，而您可以藉由在個別用戶端 Vm 上新增額外的實例，達到更高的輸送量速率。  
 
* 它可以使用相應放大架構，在一小時內大量匯入 1 TB 以上的資料。  

* 它可以大量更新 Azure Cosmos 容器中的現有資料作為修補程式。 
 
## <a name="how-does-the-bulk-executor-operate"></a>大量執行程式如何運作？ 

當以實體批次觸發匯入或更新文件的大量作業時，這些資料一開始會隨機打散到與 Azure Cosmos DB 分割區索引鍵範圍對應的貯體。 在與分割區索引鍵範圍對應的每個貯體中，資料會細分為小型批次，而每個小型批次都會作為可在伺服器端上認可的承載。 大量執行程式程式庫針對不論是在分割區索引鍵範圍內還是跨分割區索引鍵範圍，並行執行這些迷你批次，都提供內建的最佳化。 下圖說明大量執行程式如何將資料批次處理到不同的分割區索引鍵：  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="大量執行程式架構" :::

大量執行程式程式庫可確保充分利用配置給集合的輸送量。 它會為每個 Azure Cosmos DB 分割區索引鍵範圍使用  [AIMD 樣式的壅塞控制機制](https://tools.ietf.org/html/rfc5681)，以有效率地處理速率限制和逾時。 

## <a name="next-steps"></a>後續步驟 
  
* 試用使用 [.net](bulk-executor-dot-net.md) 和 [JAVA](bulk-executor-java.md)大量執行程式程式庫的範例應用程式，以深入瞭解。  
* 查看 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 和 [Java](sql-api-sdk-bulk-executor-java.md) 中的大量執行程式 SDK 資訊和版本資訊。
* 大量執行程式程式庫已整合到 Cosmos DB Spark 連接器中，若要深入瞭解，請參閱 [Azure Cosmos DB Spark 連接器](spark-connector.md) 文章。  
* 大量執行程式程式庫也已整合至新版 [Azure Cosmos DB 連接器](../data-factory/connector-azure-cosmos-db.md)，可供 Azure Data Factory 複製資料。
