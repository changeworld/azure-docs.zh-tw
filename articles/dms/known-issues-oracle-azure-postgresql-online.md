---
title: 已知問題：從 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫
titleSuffix: Azure Database Migration Service
description: 瞭解使用 Azure 資料庫移轉服務從 Oracle 線上遷移到適用於 PostgreSQL 的 Azure 資料庫單一伺服器的已知問題和遷移限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235251"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>從 Oracle 線上遷移至適用于于 postgresql 的 Azure DB 的已知問題/遷移限制-單一伺服器

從 Oracle 線上遷移到適用於 PostgreSQL 的 Azure 資料庫單一伺服器相關的已知問題和限制，將在下列各節中說明。

## <a name="oracle-versions-supported-as-a-source-database"></a>支援做為源資料庫的 Oracle 版本

Azure 資料庫移轉服務支援連接至：

- Oracle 版本的10g、11g 和12c。
- Oracle Enterprise、Standard、Express 和 Personal Edition。

Azure 資料庫移轉服務不支援連接到多租使用者容器資料庫（CDBs）。

## <a name="postgresql-versions-supported-as-a-target-database"></a>支援做為目標資料庫的于 postgresql 版本

Azure 資料庫移轉服務支援遷移至適用於 PostgreSQL 的 Azure 資料庫-單一伺服器版本9.5、9.6、10和11。 如需適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中版本支援的最新資訊，請參閱[支援的于 postgresql 資料庫版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)一文。

## <a name="datatype-limitations"></a>資料類型限制

下列資料類型**不**會被遷移：

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- 巢狀資料表
- 使用者定義資料類型
- 備忘錄
- 虛擬資料行
- 根據 ROWID 資料行的具體化視圖

此外，空的 BLOB/CLOB 資料行在目標上會對應到 Null。

## <a name="lob-limitations"></a>LOB 限制

- 啟用有限大小的 LOB 模式時，Oracle 來源上的空 Lob 會複寫為 Null 值。
- 不支援長物件名稱（超過30個位元組）。
- 長時間和較長的原始資料行中的資料不能超過64k。 超過64k 的任何資料都會遭到截斷。
- 在 Oracle 12 中，對 LOB 資料行所做的任何變更都不受支援（遷移）。
- 不支援 XMLTYPE 和 LOB 資料行的更新（遷移）。

## <a name="known-issues-and-limitations"></a>已知問題和限制

- 客戶必須使用 SYSDBA 來連接到 Oracle。
- 分割區/子分割區作業（ADD、DROP、EXCHANGE 和截斷）所產生的資料變更不會遷移，而且可能會造成下列錯誤：
  - 針對加入作業，已新增資料的更新和刪除可能會傳回「0個受影響資料列」警告。
  - 對於卸載和截斷作業，新的插入可能會導致「重複」錯誤。
  - 對於 EXCHANGE 作業，可能會發生「0個數據列受影響」警告和「重複」錯誤。
- 無法複寫名稱包含撇號的資料表。
