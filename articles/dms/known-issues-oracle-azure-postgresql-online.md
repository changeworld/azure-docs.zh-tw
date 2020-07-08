---
title: 已知問題：從 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務從 Oracle 線上移轉到適用於 PostgreSQL 的 Azure 資料庫單一伺服器的已知問題和移轉限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 05/20/2020
ms.openlocfilehash: 2cf8ff446fe3441fc039ef3c2afef6308224666f
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701213"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>從 Oracle 線上移轉到適用於 PostgreSQL 的 Azure 資料庫單一伺服器的已知問題/移轉限制

下列各節說明從 Oracle 線上移轉到適用於 PostgreSQL 的 Azure 資料庫單一伺服器的已知問題和相關限制。

## <a name="oracle-versions-supported-as-a-source-database"></a>支援作為來源資料庫的 Oracle 版本

Azure 資料庫移轉服務支援連線至：

- Oracle 版本的10g、11g 和 12c。
- Oracle Enterprise、Standard、Express 和 Personal Edition。

Azure 資料庫移轉服務不支援連線到多租用戶容器資料庫 (CDB)。

## <a name="postgresql-versions-supported-as-a-target-database"></a>支援作為目標資料庫的 PostgreSQL 版本

Azure 資料庫移轉服務支援移轉至適用於 PostgreSQL 的 Azure 資料庫單一伺服器版本 9.5、9.6、10 和 11。 如需適用於 PostgreSQL 的 Azure 資料庫單一伺服器中版本支援的最新資訊，請參閱[支援的 PostgreSQL 資料庫版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)。

## <a name="datatype-limitations"></a>資料類型限制

下列資料類型**不會**進行遷移：

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- 巢狀資料表
- 使用者定義資料類型
- 注意
- 虛擬資料行
- 以 ROWID 資料行為基礎的具體化檢視

此外，空的 BLOB/CLOB 資料行在目標上會對應至 NULL。

## <a name="lob-limitations"></a>LOB 限制

- 啟用有限大小的 LOB 模式時，Oracle 來源上的空 LOB 會複寫為 Null 值。
- 不支援長物件名稱 (超過 30 個位元組)。
- LONG 和 LONG RAW 資料行中的資料不能超過 64k。 任何超過 64k 的資料都會遭到截斷。
- 只有在 Oracle 12 中，不支援對 LOB 資料行所做的任何變更 (已遷移)。
- 不支援 XMLTYPE 和 LOB 資料行的更新 (已遷移)。

## <a name="known-issues-and-limitations"></a>已知問題和限制

- 使用者在 Oracle Server 上必須擁有 DBA f權限。
- 分割區/子分割區作業 (ADD、DROP、EXCHANGE 和 TRUNCATE) 所造成的資料變更不會遷移，而且可能會導致下列錯誤：
  - 若為 ADD 作業，已新增資料的更新和刪除可能會傳回「0 個資料列受影響」警告。
  - 若為 DROP 和 TRUNCATE 作業，新的插入可能會導致「重複」錯誤。
  - 若為 EXCHANGE 作業，可能會發生「0 個資料列受影響」警告和「重複」錯誤。
- 無法複寫名稱包含單引號的資料表。
