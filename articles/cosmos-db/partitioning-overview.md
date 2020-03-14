---
title: Azure Cosmos DB 中的資料分割
description: 瞭解 Azure Cosmos DB 中的資料分割、選擇資料分割索引鍵時的最佳作法，以及如何管理邏輯分割區
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251865"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割

Azure Cosmos DB 使用資料分割來調整資料庫中的個別容器，以符合您應用程式的效能需求。 在資料分割中，容器中的專案會分割成不同的子集，稱為*邏輯*分割區。 邏輯分割區是根據與容器中每個專案相關聯的資料*分割索引鍵*的值來組成。 邏輯分割區中的所有專案都具有相同的資料分割索引鍵值。

例如，容器會保留專案。 每個專案都有 `UserID` 屬性的唯一值。 如果 `UserID` 作為容器中專案的分割區索引鍵，而且有1000個唯一 `UserID` 值，則會為容器建立1000個邏輯分割區。

除了用來決定專案邏輯分割區的分割區索引鍵之外，容器中的每個專案都有一個*專案識別碼*（在邏輯分割區內是唯一的）。 結合資料分割索引鍵和專案識別碼會建立專案的*索引*，以唯一識別該專案。

[選擇分割](partitioning-overview.md#choose-partitionkey)區索引鍵是一項重要決策，會影響應用程式的效能。

## <a name="managing-logical-partitions"></a>管理邏輯分割區

Azure Cosmos DB 透明且自動地管理實體分割區上邏輯分割區的位置，以有效率地滿足容器的擴充性和效能需求。 隨著應用程式的輸送量和儲存體需求增加，Azure Cosmos DB 會移動邏輯分割區，以自動將負載分散到更多的伺服器上。 

Azure Cosmos DB 使用以雜湊為基礎的資料分割，將邏輯磁碟分割分散到實體資料分割。 Azure Cosmos DB 會雜湊專案的分割區索引鍵值。 雜湊的結果會決定實體分割區。 然後，Azure Cosmos DB 會將分割區索引鍵雜湊的索引鍵空間平均分配給實體磁碟分割。

在單一邏輯分割區記憶體取資料的查詢比存取多個分割區的查詢更符合成本效益。 只有單一邏輯分割區中的專案可以使用交易（在預存程式或觸發程式中）。

若要深入瞭解 Azure Cosmos DB 如何管理資料分割，請參閱[邏輯](partition-data.md)分割區。 （您不需要瞭解內部詳細資料來建立或執行您的應用程式，但在這裡為好奇的讀者加入）。

## <a id="choose-partitionkey"></a>選擇分割區索引鍵

以下是選擇資料分割索引鍵的絕佳指引：

* 單一邏輯分割區的儲存空間上限為 20 GB。  

* Azure Cosmos 容器的輸送量最低為每秒400個要求單位（RU/秒）。 在資料庫上布建輸送量時，每個容器的最小 ru 為每秒100個要求單位（RU/秒）。 對相同分割區索引鍵的要求不能超過配置給分割區的輸送量。 如果要求超過配置的輸送量，則要求會受到速率限制。 因此，請務必挑選不會在應用程式內產生「作用點」的分割區索引鍵。

* 選擇具有各種不同值的分割區索引鍵，以及存取平均分散到邏輯分割區的模式。 這有助於將容器中的資料和活動分散到一組邏輯分割區，讓資料儲存和輸送量的資源可以分散到邏輯分割區。

* 選擇分割區索引鍵，將工作負載平均分散到所有分割區，並在一段時間後平均分佈。 您選擇的資料分割索引鍵應該在有效率的資料分割查詢和交易的需求之間取得平衡，目的是要將專案分散到多個資料分割，以達成擴充性。

* 資料分割索引鍵的候選項目可能包含經常顯示為查詢中篩選的屬性。 可藉由在篩選述詞中包含分割區索引鍵，有效地路由傳送查詢。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure Cosmos DB 中的資料分割和水準調整](partition-data.md)。
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)。
