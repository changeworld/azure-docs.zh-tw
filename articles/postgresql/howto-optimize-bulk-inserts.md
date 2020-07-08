---
title: 優化大量插入-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何在適用於 PostgreSQL 的 Azure 資料庫單一伺服器上優化大量插入作業。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74770130"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>優化適用於 PostgreSQL 的 Azure 資料庫單一伺服器上的大量插入和使用暫時性資料 
本文說明如何在適用於 PostgreSQL 的 Azure 資料庫伺服器上最佳化大量插入作業及使用暫時性資料。

## <a name="use-unlogged-tables"></a>使用未記錄的資料表
如果您有工作負載作業涉及暫時性資料或大量插入大型資料集，應考慮使用未記錄的資料表。

未記錄的資料表是一項 PostgreSQL 功能，可以有效率地將大量插入最佳化。 PostgreSQL 會使用預寫記錄檔 (WAL)。 根據預設，其提供不可部分完成的作業特性和耐久性。 Atomicity (不可部分完成的作業)、Consistency (一致性)、Isolation (隔離性) 及 Durability (耐久性) 組成 ACID 屬性。 

插入未記錄的資料表，表示 PostgreSQL 執行插入時未寫入至交易記錄，而交易記錄本身就是一項 I/O 作業。 如此一來，這些資料表會比一般資料表快很多。

使用下列選項來建立未記錄的資料表：
- 使用 `CREATE UNLOGGED TABLE <tableName>` 語法建立新的未記錄資料表。
- 使用 `ALTER TABLE <tableName> SET UNLOGGED` 語法將現有的已記錄資料表轉換為未記錄資料表。  

若要反轉程序，請使用 `ALTER TABLE <tableName> SET LOGGED` 語法。

## <a name="unlogged-table-tradeoff"></a>未記錄資料表的缺點
未記錄的資料表可能因當機而缺損。 在經歷當機或未正常關機後，未記錄的資料表將會自動截斷。 未記錄資料表的內容也不會複寫到待命伺服器。 在未記錄的資料表上建立的任何索引也會自動成為未記錄的格式。 插入作業完成後，可以將資料表轉換為已記錄格式，以保有插入的持久性。

在某些客戶工作負載中，使用未記錄的資料表約可帶來 15-20% 的效能提升。

## <a name="next-steps"></a>後續步驟
檢閱您的工作負載是否使用了暫時性資料和大量插入。 請參閱下列 PostgreSQL 文件：
 
- [建立資料表 SQL 命令](https://www.postgresql.org/docs/current/static/sql-createtable.html)
