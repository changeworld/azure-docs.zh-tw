---
title: 已知問題：從 Oracle 遷移到 Azure 資料庫，用於 PostgreSQL
titleSuffix: Azure Database Migration Service
description: 瞭解使用 Azure 資料庫移轉服務從 Oracle 到 Azure 資料庫的連線遷移的已知問題和遷移限制，以便 PostgreSQL-Single 伺服器使用 PostgreSQL-Single 伺服器。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235251"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>從 Oracle 到 Azure DB 的連線遷移對 PostgreSQL 單一伺服器的已知問題/遷移限制

以下各節介紹了與從 Oracle 到 Azure 資料庫的 PostgreSQL 單一伺服器連線遷移相關的已知問題和限制。

## <a name="oracle-versions-supported-as-a-source-database"></a>作為源資料庫支援的 Oracle 版本

Azure 資料庫移轉服務支援連接到：

- 甲骨文版本 10g、11g 和 12c。
- Oracle 企業版、標準版、快速版和個人版。

Azure 資料庫移轉服務不支援連接到多租戶容器資料庫 （CDB）。

## <a name="postgresql-versions-supported-as-a-target-database"></a>作為目標資料庫支援的 PostgreSQL 版本

Azure 資料庫移轉服務支援遷移到 Azure 資料庫，用於 PostgreSQL 單一伺服器版本 9.5、9.6、10 和 11。 有關 PostgreSQL 單一伺服器 Azure 資料庫中版本支援的當前資訊，請參閱文章["支援 PostgreSQL 資料庫版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)"。

## <a name="datatype-limitations"></a>資料類型限制

**以下資料類型不會**遷移：

- BFILE
- ROWID
- REF
- UROWID
- 任何資料
- SDO_GEOMETRY
- 巢狀資料表
- 使用者定義資料類型
- 注意
- 虛擬列
- 基於 ROWID 列的具體視圖

此外，空 BLOB/CLOB 列映射到目標上的 Null。

## <a name="lob-limitations"></a>LOB 限制

- 啟用有限大小的 LOB 模式後，Oracle 源上的空 LOB 將複製為 Null 值。
- 不支援長物件名稱（超過 30 位元組）。
- 長列和長 RAW 列中的資料不能超過 64k。 超過 64k 的任何資料將被截斷。
- 僅在 Oracle 12 中，不支援對 LOB 列的任何更改（遷移）。
- 不支援（遷移）到 XMLTYPE 和 LOB 列的 UPDAT。

## <a name="known-issues-and-limitations"></a>已知問題和限制

- 客戶必須使用 SYSDBA 連接到 Oracle。
- 分區/分區操作（ADD、DROP、EXCHANGE 和 TRUNCATE）導致的資料更改不會遷移，並可能導致以下錯誤：
  - 對於 ADD 操作，對添加的資料的更新和刪除可能會返回"受影響的 0 行"警告。
  - 對於 DROP 和 TRUNCATE 操作，新插入可能會導致"重複"錯誤。
  - 對於 EXCHANGE 操作，可能會出現"0 行受影響的"警告和"重複"錯誤。
- 名稱包含撇號的表無法複製。
