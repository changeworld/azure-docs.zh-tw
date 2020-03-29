---
title: 優化查詢統計資訊集合 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 本文介紹如何優化 Azure 資料庫上的查詢統計資訊集合，用於 PostgreSQL - 單伺服器
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770164"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>優化後資料庫 - 單伺服器 Azure 資料庫上的查詢統計資訊集合
本文說明如何在「適用於 PostgreSQL 的 Azure 資料庫」伺服器中最佳化查詢統計資料集合。

## <a name="use-pg_stats_statements"></a>使用 pg_stats_statements
**Pg_stat_statements** 是適用於 PostgreSQL 的 Azure 資料庫中依預設啟用的 PostgreSQL 擴充功能。 此擴充功能提供了相關機制，可對伺服器執行的所有 SQL 陳述式追蹤執行統計資料。 此模組會在每次執行查詢連結，而產生頗高的效能成本。 啟用 **pg_stat_statements** 會強制將查詢文字寫入磁碟上的檔案。

如果您有查詢文字很長的唯一查詢，或您未主動監視 **pg_stat_statements**，請停用 **pg_stat_statements**，以達到最佳效能。 若要這麼做，請將設定變更為 `pg_stat_statements.track = NONE`。

在某些客戶工作負載上，我們發現停用 **pg_stat_statements** 最多可提升 50% 的效能。 但停用 pg_stat_statements 的代價是無法對效能問題進行疑難排解。

若要設定 `pg_stat_statements.track = NONE`：

- 在 Azure 入口網站中移至 [PostgreSQL 資源管理頁面，然後選取伺服器參數刀鋒視窗](howto-configure-server-parameters-using-portal.md)。

  ![PostgreSQL 伺服器參數刀鋒視窗](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- 若使用 [Azure CLI](howto-configure-server-parameters-using-cli.md)，將 az postgres server configuration set 設為 `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`。

## <a name="use-the-query-store"></a>使用查詢存放區 
在適用於 PostgreSQL 的 Azure 資料庫中，[查詢存放區](concepts-query-store.md)功能提供更有效率的方法來追蹤查詢統計資料。 我們建議使用這項功能替代 pg_stats_statements**。 

## <a name="next-steps"></a>後續步驟
考慮在 [Azure 入口網站](howto-configure-server-parameters-using-portal.md)中或使用 [Azure CLI](howto-configure-server-parameters-using-cli.md) 來設定 `pg_stat_statements.track = NONE`。

如需詳細資訊，請參閱 
- [查詢存儲使用方案](concepts-query-store-scenarios.md) 
- [查詢存儲最佳實踐](concepts-query-store-best-practices.md) 
