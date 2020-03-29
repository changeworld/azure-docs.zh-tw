---
title: Azure Cosmos DB 中的資料分割
description: 瞭解 Azure Cosmos DB 中的分區、選擇分區鍵時的最佳做法以及如何管理邏輯分區
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251865"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割

Azure Cosmos DB 使用分區來縮放資料庫中的各個容器，以滿足應用程式的性能需求。 在分區中，容器中的項被劃分為稱為*邏輯分區*的不同子集。 邏輯分區是根據與容器中的每個項關聯的*分區鍵*的值形成的。 邏輯分區中的所有項具有相同的分區鍵值。

例如，容器保存項。 每個專案都有`UserID`屬性的唯一值。 如果`UserID`用作容器中項的分區鍵，並且有 1，000 個唯一`UserID`值，則為容器創建 1，000 個邏輯分區。

除了確定項的邏輯分區的分區鍵外，容器中的每個項都有一個*項 ID（* 在邏輯分區中是唯一的）。 組合分區鍵和項 ID 將創建項的索引 ，該*索引*唯一地標識項。

[選擇分區金鑰](partitioning-overview.md#choose-partitionkey)是影響應用程式性能的重要決策。

## <a name="managing-logical-partitions"></a>管理邏輯分區

Azure Cosmos 透明地自動管理邏輯分區在物理分區上的位置，以有效地滿足容器的可伸縮性和性能需求。 隨著應用程式的輸送量和存儲要求的增加，Azure Cosmos DB 移動邏輯分區以自動將負載分散到更多伺服器中。 

Azure Cosmos DB 使用基於雜湊的分區來跨物理分區展開邏輯分區。 Azure Cosmos DB 會雜湊項的分區鍵值。 雜湊結果確定物理分區。 然後，Azure Cosmos DB 在物理分區中均勻分配分區金鑰雜湊的鍵空間。

訪問單個邏輯分區中資料的查詢比訪問多個分區的查詢更具成本效益。 事務（在預存程序或觸發器中）僅允許針對單個邏輯分區中的項。

要瞭解有關 Azure Cosmos DB 如何管理分區，請參閱[邏輯分區](partition-data.md)。 （無需瞭解內部詳細資訊來生成或運行應用程式，但在此處添加了好奇的讀者。

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>選擇分割區索引鍵

以下是選擇分區鍵的良好指南：

* 單個邏輯分區的上限為 20 GB 的存儲空間。  

* Azure Cosmos 容器的最低輸送量為每秒 400 個請求單位 （RU/s）。 在資料庫上預配輸送量時，每個容器的最小 RU 為每秒 100 個請求單位 （RU/s）。 對同一分區鍵的請求不能超過分配給分區的輸送量。 如果請求超過分配的輸送量，則請求的速率限制。 因此，請務必挑選不會在應用程式內產生「作用點」的分割區索引鍵。

* 選擇具有各種不同值的分割區索引鍵，以及存取平均分散到邏輯分割區的模式。 這有助於將資料和容器中的活動分散到邏輯分區集中，以便資料存儲和輸送量的資源可以跨邏輯分區分佈。

* 選擇一個分區鍵，該分區鍵會均勻地跨所有分區均勻地分佈，並隨時間均勻分佈。 選擇分區鍵應平衡對高效分區查詢和事務的需求與跨多個分區分發項以實現可伸縮性的目標。

* 分區鍵的候選項可能包含在查詢中經常顯示為篩選器的屬性。 可藉由在篩選述詞中包含分割區索引鍵，有效地路由傳送查詢。

## <a name="next-steps"></a>後續步驟

* 瞭解[Azure Cosmos DB 中的分區和水準縮放](partition-data.md)。
* 瞭解[Azure 宇宙 DB 中的預配輸送量](request-units.md)。
* 瞭解[Azure 宇宙 DB 中的全域分發](distribute-data-globally.md)。
