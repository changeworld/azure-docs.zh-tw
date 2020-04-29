---
title: 已知問題：從 MongoDB 遷移至 Azure CosmosDB
titleSuffix: Azure Database Migration Service
description: 瞭解使用 Azure 資料庫移轉服務從 MongoDB 遷移至 Azure Cosmos DB 的已知問題和遷移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: ae5d5f2d282c546f5172ca1c8cb0e420d3b6e96b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878046"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>從 MongoDB 遷移至 Azure Cosmos DB 的 MongoDB API 的已知問題/遷移限制

下列各節將說明與從 MongoDB 遷移至 Cosmos DB 的 MongoDB API 相關聯的已知問題和限制。

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>因為使用不正確的 SSL 憑證而導致遷移失敗

* **徵兆**：當使用者無法連線到 MongoDB 來源伺服器時，此問題很明顯。 儘管已開啟所有防火牆埠，但使用者仍然無法連線。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 在 Azure 資料庫移轉服務中使用自我簽署憑證可能會導致遷移失敗，因為 SSL 憑證不正確。錯誤訊息可能包含「根據驗證程式，遠端憑證無效」。 | 使用 CA 的正版憑證。  自我簽署憑證通常僅用於內部測試。 當您從 CA 授權單位安裝正版憑證時，您可以在 Azure 資料庫移轉服務中使用 SSL，而不會發生問題（連線至 Cosmos DB 使用 SSL over Mongo API）。<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>無法取得要在 DMS 中對應的資料庫清單

* **徵兆**：從 [**選取來源**] 分頁上的 [Azure 儲存體] 模式使用資料時，無法**從**[**資料庫設定**] 分頁上取得 DB 清單。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 儲存體帳戶連接字串遺漏 SAS 資訊，因此無法進行驗證。 | 在儲存體總管中的 blob 容器上建立 SAS，並使用 URL 搭配容器 SAS 資訊做為來源詳細資料連線字串。<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>使用不支援的資料庫版本

* **徵兆**：遷移失敗。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 您嘗試從不受支援的 MongoDB 版本遷移至 Azure Cosmos DB。 | 發行新版本的 MongoDB 時，會進行測試以確保與 Azure 資料庫移轉服務的相容性，而且會定期更新服務以接受最新版本。 如果立即需要遷移，您可以將資料庫/集合匯出至 Azure 儲存體，然後將來源指向產生的傾印，以因應措施。 在儲存體總管的 blob 容器上建立 SAS，然後使用 URL 搭配容器 SAS 資訊做為來源詳細資料連線字串。<br><br> |

## <a name="next-steps"></a>後續步驟

* 觀看教學課程[使用 DMS 在線上將 Mongodb 遷移至 Azure Cosmos DB 的 MONGODB API](tutorial-mongodb-cosmos-db-online.md)。
* 觀看教學課程[使用 DMS 離線將 Mongodb 遷移至適用于 MongoDB 的 AZURE COSMOS DB API](tutorial-mongodb-cosmos-db.md)。