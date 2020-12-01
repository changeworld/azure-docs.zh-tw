---
title: 使用 Azure Cosmos DB 中的唯一索引鍵
description: 瞭解如何定義和使用 Azure Cosmos 資料庫的唯一金鑰。 本文也描述唯一索引鍵如何新增資料完整性層。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 165fb2937db5edfa4f51f62033afaf87cfff83ef
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353097"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Azure Cosmos DB 中的唯一索引鍵限制式
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

唯一索引鍵為 Azure Cosmos 容器加入一層資料完整性。 您會在建立 Cosmos 容器時建立唯一索引鍵原則。 利用唯一索引鍵，您就能確保邏輯分割區內一或多個值是唯一的。 您也可以確保每[分割區索引鍵](partitioning-overview.md)的唯一性。

使用唯一索引鍵原則建立容器之後，就會防止建立新的或更新現有的專案，而導致邏輯資料分割內出現重複的專案，如 unique key 條件約束所指定。 與唯一索引鍵結合的分割區索引鍵可保證項目在容器範圍內的唯一性。

例如，假設 Azure Cosmos 容器的唯一索引鍵限制式為電子郵件地址且分割區索引鍵為 `CompanyID`。 當您使用唯一索引鍵設定使用者的電子郵件位址時，每個項目在指定的 `CompanyID` 內就具有唯一的電子郵件地址。 您無法使用重複的電子郵件地址和相同的分割區索引鍵值來建立兩個項目。 在 Azure Cosmos DB 的 SQL (Core) API 中，專案會儲存為 JSON 值。 這些 JSON 值會區分大小寫。 當您選擇屬性做為唯一索引鍵時，可以為該屬性插入區分大小寫的值。 例如，如果您在 name 屬性上定義了唯一索引鍵，則 "Gaby" 與 "Gaby" 不同，而且您可以將兩者插入容器中。

若要使用相同的電子郵件地址建立項目，但名字、姓氏和電子郵件地址不同，請將其他路徑新增至唯一索引鍵原則。 您也可以使用名字、姓氏和電子郵件地址的組合來建立唯一索引鍵，而不是根據電子郵件地址來建立唯一索引鍵。 這個金鑰也稱為複合式唯一索引鍵。 在此情況下，在指定的 `CompanyID` 內允許這三個值的每個唯一組合。 

例如，容器可以包含具有下列值的項目，其中每個項目都會遵守唯一索引鍵限制式。

|CompanyID|名字|姓氏|電子郵件地址|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

如果您嘗試使用上表中列出的組合插入另一個項目，則會收到錯誤。 此錯誤表示不符合唯一索引鍵限制式。 您會收到 `Resource with specified ID or name already exists` 或傳回 `Resource with specified ID, name, or unique index already exists` 訊息。 

## <a name="define-a-unique-key"></a>定義唯一索引鍵

只有在您建立 Azure Cosmos 容器時，才能定義唯一索引鍵。 唯一索引鍵的範圍限定為某個邏輯分割區。 在上述範例中，如果您根據郵遞區號來分割容器，最終將在每個邏輯分割區中具有重複的項目。 建立唯一索引鍵時，請考慮下列屬性：

* 您無法將現有容器更新為使用不同的唯一索引鍵。 換句話說，一旦使用唯一索引鍵原則建立容器之後，便無法變更此原則。

* 若要為現有容器設定唯一索引鍵，請使用唯一索引鍵限制式建立新的容器。 使用適當的資料移轉工具，將資料從現有的容器移至新的容器。 針對 SQL 容器，使用[資料移轉工具](import-data.md)來移動資料。 對於 MongoDB 容器，使用 [mongoimport.exe 或 mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) 來移動資料。

* 唯一索引鍵原則最多可包含 16 個路徑值。 例如，值可以是 `/firstName` 、 `/lastName` 和 `/address/zipCode` 。 每個唯一索引鍵原則最多可以有 10 個唯一索引鍵條件約束或組合。 每個唯一索引限制式的組合路徑不得超過 60 個位元組。 在前一個範例中，名字、姓氏和電子郵件地址合起來是一個限制式。 此限制式會使用 16 個可能路徑中的 3 個。

* 當容器具有唯一索引鍵原則時，建立、更新和刪除專案的 [要求單位 (RU) ](request-units.md) 費用會稍微提高一點。

* 不支援疏鬆的唯一索引鍵。 如果遺漏了部分唯一路徑值，則會將它們視為 Null 值，並將其納入唯一性限制式。 因此，只有具 Null 值的單一項目能夠滿足此限制式。

* 唯一索引鍵名稱會區分大小寫。 例如，請考慮將 unique key 條件約束設為的容器 `/address/zipcode` 。 如果您的資料中有一個名為的欄位 `ZipCode` ，Azure Cosmos DB 插入 "null" 作為唯一索引鍵，因為與 `zipcode` 不相同 `ZipCode` 。 由於這個區分大小寫的緣故，因此，無法插入包含 ZipCode 的所有其他記錄，因為重複的 "null" 違反唯一索引鍵限制式。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [邏輯分區](partitioning-overview.md)
* 探索如何在建立容器時[定義唯一索引鍵](how-to-define-unique-keys.md)