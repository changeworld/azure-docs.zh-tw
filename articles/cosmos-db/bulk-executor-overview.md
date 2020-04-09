---
title: Azure Cosmos DB 大量執行程式程式庫概觀
description: 在 Azure Cosmos DB 中透過大量執行程式程式庫所提供的大量匯入和大量更新 API，來執行大量作業。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: af17f9c2ef7eea5eb531327d4df13d5885a49b7e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985587"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB 大量執行程式程式庫概觀
 
Azure Cosmos DB 是一種快速且具有彈性的全域分散式資料庫服務，其彈性相應放大的設計可支援： 

* 大型讀取和寫入輸送量 (每秒百萬個作業)。  
* 儲存大量 (數百 TB 或更多) 且可預測延遲毫秒數的交易和作業資料。  

大量執行程式程式庫可協助您利用這個大量輸送量和儲存體。 大量執行程式程式庫可讓您在 Azure Cosmos DB 中透過大量匯入和大量更新 API 來執行大量作業。 您可以在下列各節中深入了解大量執行程式程式庫的功能。 

> [!NOTE] 
> 目前，大量執行程式程式庫支援匯入和更新作業，而且僅有 Azure Cosmos DB SQL API 和 Gremlin API 帳戶可支援此程式庫。
 
## <a name="key-features-of-the-bulk-executor-library"></a>大量執行程式程式庫的主要功能  
 
* 它會大幅減少佔滿輸送量 (配置到容器) 的必要用戶端運算資源。 比起平行寫入資料而佔滿用戶端機器 CPU 的多執行緒應用程式，使用大量匯入 API 寫入資料的單一執行緒應用程式，可達到 10 倍多的寫入輸送量。  

* 它以概念來簡單化撰寫應用程式邏輯的沉悶工作，有效率地在程式庫中處理要求速率限制、要求逾時和其他暫時性例外狀況。  

* 它為執行批量操作的應用程式提供了一個簡化的機制,以便橫向擴展。在 Azure VM 上運行的單個批量執行器實例的消耗量可能大於 500K RU/s,並且可以通過在單個用戶端 VM 上添加其他實例來實現更高的輸送量率。  
 
* 它可以使用相應放大架構，在一小時內大量匯入 1 TB 以上的資料。  

* 它可以批量更新 Azure Cosmos 容器中的現有數據作為修補程式。 
 
## <a name="how-does-the-bulk-executor-operate"></a>批量執行器如何操作? 

當以實體批次觸發匯入或更新文件的大量作業時，這些資料一開始會隨機打散到與 Azure Cosmos DB 分割區索引鍵範圍對應的貯體。 在與分割區索引鍵範圍對應的每個貯體中，資料會細分為小型批次，而每個小型批次都會作為可在伺服器端上認可的承載。 大量執行程式程式庫針對不論是在分割區索引鍵範圍內還是跨分割區索引鍵範圍，並行執行這些迷你批次，都提供內建的最佳化。 下圖說明大量執行程式如何將資料批次處理到不同的分割區索引鍵：  

![大量執行程式架構](./media/bulk-executor-overview/bulk-executor-architecture.png)

批量執行器庫可確保最大限度地利用分配給集合的輸送量。 它會為每個 Azure Cosmos DB 分割區索引鍵範圍使用  [AIMD 樣式的壅塞控制機制](https://tools.ietf.org/html/rfc5681)，以有效率地處理速率限制和逾時。 

## <a name="next-steps"></a>後續步驟 
  
* 通過嘗試在[.NET](bulk-executor-dot-net.md)和[Java](bulk-executor-java.md)中使用批量執行者庫的範例應用程式,瞭解更多資訊。  
* 查看 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 和 [Java](sql-api-sdk-bulk-executor-java.md) 中的大量執行程式 SDK 資訊和版本資訊。
* 批量執行器庫整合到Cosmos DB Spark連接器中,有關詳細資訊,請參閱[Azure Cosmos DB Spark連接器](spark-connector.md)一文。  
* 大量執行程式程式庫也已整合至新版 [Azure Cosmos DB 連接器](../data-factory/connector-azure-cosmos-db.md)，可供 Azure Data Factory 複製資料。
