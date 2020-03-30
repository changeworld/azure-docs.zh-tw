---
title: 已知問題：從蒙戈DB遷移到 Azure 宇宙DB
titleSuffix: Azure Database Migration Service
description: 使用 Azure 資料庫移轉服務瞭解從 MongoDB 遷移到 Azure Cosmos DB 的已知問題和遷移限制。
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
ms.openlocfilehash: 194da036260a78b27748dfc7f755212ab4f30b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78256022"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>從蒙戈DB遷移到 Azure Cosmos DB 的 MongoDB API 時已知的問題/遷移限制

以下各節介紹了與從 MongoDB 遷移到 Cosmos DB MongoDB 的 API 相關的已知問題和限制。

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>遷移失敗，因為使用不正確的 SSL 憑證

* **症狀**：當使用者無法連接到 MongoDB 源伺服器時，此問題是顯而易見的。 儘管打開了所有防火牆埠，但使用者仍無法連接。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 在 Azure 資料庫移轉服務中使用自簽章憑證可能會導致遷移失敗，因為 SSL Cert 不正確。錯誤訊息可能包括"根據驗證過程遠端證書無效"。 | 使用來自 CA 的正版證書。  自簽章憑證通常僅用於內部測試。 從 CA 頒發機構安裝正版證書時，可以在 Azure 資料庫移轉服務中無問題地使用 SSL（與 Cosmos DB 的連接通過 Mongo API 使用 SSL）。<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>無法獲取要在 DMS 中映射的資料庫清單

* **症狀**：在 **"選擇源"** 邊欄選項卡上使用**Azure 存儲模式的資料**時，無法獲取**資料庫設置**邊欄選項卡上的資料庫清單。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 存儲帳戶連接字串缺少 SAS 資訊，因此無法進行身份驗證。 | 在存儲資源管理器中的 blob 容器上創建 SAS，並將具有容器 SAS 資訊的 URL 用作源詳細資訊連接字串。<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>使用不支援的資料庫版本

* **症狀**：遷移失敗。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 您嘗試從不支援的 MongoDB 版本遷移到 Azure 宇宙 DB。 | 隨著 MongoDB 的新版本發佈，將測試它們以確保與 Azure 資料庫移轉服務的相容性，並且該服務將定期更新以接受最新版本。 如果迫切需要遷移，則可以將資料庫/集合匯出到 Azure 存儲以及源到生成的轉儲的點。 在存儲資源管理器中的 blob 容器上創建 SAS，然後將具有容器 SAS 資訊的 URL 用作源詳細資訊連接字串。<br><br> |

## <a name="next-steps"></a>後續步驟

* 使用 DMS 線上查看[將蒙戈DB遷移到 Azure Cosmos DB](tutorial-mongodb-cosmos-db-online.md)的 API 教程。
* 使用 DMS 查看將[蒙戈DB遷移到 Azure Cosmos DB](tutorial-mongodb-cosmos-db.md)的 API 教程。