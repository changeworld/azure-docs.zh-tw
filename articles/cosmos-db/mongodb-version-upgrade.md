---
title: 升級 Azure Cosmos DB 適用于 MongoDB 的 API 帳戶的 Mongo 版本
description: 如何順暢地升級現有 Azure Cosmos DB 適用于 MongoDB 帳戶之 API 的 MongoDB 通訊協定版本
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: chrande
ms.openlocfilehash: 9ce444e41d19ece984071d0f62e705a09d5f23c9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356445"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>升級 Azure Cosmos DB 適用于 MongoDB 的 API 帳戶的 MongoDB 有線通訊協定版本
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

本文說明如何升級 Azure Cosmos DB 適用于 MongoDB 的 API 帳戶的有線通訊協定版本。 升級網路通訊協定版本之後，您可以使用 Azure Cosmos DB 的 MongoDB API 中的最新功能。 升級程式不會中斷您帳戶的可用性，且不會取用 RU/秒，也不會在任何時間點減少資料庫的容量。 此進程不會影響任何現有的資料或索引。

>[!Note]
> 目前，只有使用伺服器版本3.2 的合格帳戶才能升級到3.6 版。 如果您的帳戶未顯示升級選項，請提出 [支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="upgrading-from-version-32-to-36"></a>從3.2 版升級到3。6

### <a name="benefits-of-upgrading-to-version-36"></a>升級至 3.6 版的優點

以下是3.6 版中包含的新功能：
- 增強效能和穩定性
- 支援新資料庫命令
- 預設支援彙總管線和新的彙總階段
- 支援變更資料流程
- 支援複合索引
- 下列作業的跨分割區支援： update、delete、count 和 sort
- 改善下列匯總作業的效能： $count、$skip、$limit 和 $group
- 現在支援萬用字元索引

### <a name="changes-from-version-32"></a>版本3.2 的變更

- 已 **移除 RequestRateIsLarge 錯誤** 。 來自用戶端應用程式的要求不會再傳回16500錯誤。 相反地，要求將會繼續執行，直到完成或完成時間。
- 每個要求的超時時間設定為60秒。
- 在新的網路通訊協定版本上建立的 MongoDB 集合，預設只會 `_id` 編制屬性的索引。

### <a name="action-required"></a>必要的動作

升級至3.6 版時，資料庫帳戶端點尾碼將會更新為下列格式：

```
<your_database_account_name>.mongo.cosmos.azure.com
```

您必須將應用程式和驅動程式中的現有端點取代為此資料庫帳戶。 **只有使用新端點的連線才能存取 MongoDB 3.6 版中的功能** 。 先前的端點應該有尾碼 `.documents.azure.com` 。

>[!Note]
> 如果您的帳戶是在主權、政府或受限制的 Azure 雲端中建立，此端點可能會有些微差異。

### <a name="how-to-upgrade"></a>如何升級

1. 首先，移至 Azure 入口網站並流覽至您的 Azure Cosmos DB API for MongoDB 帳戶總覽分頁。 確認您的伺服器版本為 `3.2` 。 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="使用 MongoDB 帳戶的 Azure 入口網站總覽" border="false":::

2. 從左邊的選項中，選取 [分頁] `Features` 。 這會顯示您資料庫帳戶可用的帳戶層級功能。

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="使用已醒目提示功能分頁的 MongoDB 帳戶總覽 Azure 入口網站" border="false":::

3. 按一下資料 `Upgrade to Mongo server version 3.6` 列。 如果您沒有看到此選項，表示您的帳戶可能不符合這項升級的資格。 如果是這種情況，請提出 [支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="功能分頁與選項搭配使用。" border="false":::

4. 請參閱此特定升級所顯示的資訊。 請注意，只有在您的應用程式使用更新的端點時，才會完成升級，如本節中所強調。 一旦 `Enable` 您準備好開始進行程式，請按一下 [開啟]。

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="擴充的升級指引。" border="false":::

5. 啟動程式之後， `Features` 功能表會顯示升級的狀態。 狀態會從 `Pending` 、到到 `In Progress` `Upgraded` 。 此程式不會影響現有的資料庫帳戶功能或作業。

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="啟動後的升級狀態。" border="false":::

6. 升級完成後，狀態會顯示為 `Upgraded` 。 按一下以深入瞭解完成程式所需採取的後續步驟和動作。 如果在處理您的要求時發生問題，請 [洽詢支援人員](https://azure.microsoft.com/en-us/support/create-ticket/) 。

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="升級的帳戶狀態。" border="false":::

7. **若要開始使用升級後的資料庫帳戶版本** ，請返回分頁 `Overview` ，並複製新的連接字串，以在您的應用程式中使用。 應用程式會在連接到新端點時，立即開始使用升級版本。 現有的連線不會中斷，而且可以在您方便的情況中更新。 為了確保一致的體驗，您的所有應用程式都必須使用新的端點。

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="新的總覽分頁。" border="false":::

## <a name="next-steps"></a>後續步驟

- 瞭解 [MongoDB 版本 3.6](mongodb-feature-support-36.md)支援和不支援的功能。
- 如需詳細資訊，請參閱 [Mongo 3.6 版功能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
