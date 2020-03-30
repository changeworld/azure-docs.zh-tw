---
title: 存儲備份長達 10 年
description: 了解 Azure SQL Database 如何支援儲存多達 10 年的完整資料庫備份。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 15a2d58d2fc14c370c41d5454d62c74a5b66ad42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499969"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>儲存多達 10 年的 Azure SQL Database 備份

許多應用程式具有法規、相容性或其他商務用途，需要您保留 Azure SQL Database [自動備份](sql-database-automated-backups.md)所提供超過 7-35 天的資料庫備份。 通過使用長期保留 （LTR） 功能，可以將指定的 SQL 資料庫完整備份存儲在 Azure Blob 存儲中，並具有讀取存取異地冗余存儲長達 10 年。 之後您可以將任何備份還原為新的資料庫。 有關 Azure 存儲冗余的詳細資訊，請參閱[Azure 存儲冗余](../storage/common/storage-redundancy.md)。

> [!NOTE]
> 可以針對單一和集區資料庫啟用 LTR。 它還無法用於受控執行個體上的執行個體資料庫。 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代超過 35 天的 LTR。
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL Database 長期保留如何運作

長期備份保留 (LTR) 會運用[自動建立](sql-database-automated-backups.md)的完整資料庫備份來啟用時間點還原 (PITR)。 如果配置了 LTR 策略，這些備份將複製到不同的 Blob 進行長期存儲。 副本是一個後臺作業，對資料庫工作負荷沒有性能影響。 每個 SQL 資料庫的 LTR 策略還可以指定創建 LTR 備份的頻率。

要啟用 LTR，可以使用四個參數的組合來定義策略：每週備份保留 （W）、每月備份保留 （M）、年度備份保留 （Y） 和一年中的周 （WeekOfYear）。 如果指定 W，每週一次的備份會複製到長期儲存體。 如果指定 M，則每個月的第一個備份將複製到長期存儲。 如果指定 Y，在以 WeekOfYear 指定的當週進行的一次備份會複製到長期儲存體。 如果指定的 WeekOfYear 在配置策略時已過去，則第一個 LTR 備份將在下一年創建。 根據創建 LTR 備份時配置的策略參數，每個備份都將保存在長期存儲中。

> [!NOTE]
> 對 LTR 策略的任何更改僅適用于將來的備份。 例如，如果修改每週備份保留 （W）、每月備份保留 （M） 或年度備份保留 （Y），則新的保留設置將僅適用于新備份。 不會修改現有備份的保留。 如果您打算在保留期到期之前刪除舊的 LTR 備份，則需要[手動刪除備份](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)。
> 

LTR 策略的示例：

-  W=0, M=0, Y=5, WeekOfYear=3

   每年的第三次完整備份將保存五年。
   
- W=0, M=3, Y=0

   每月的第一個完整備份將保留三個月。

- W=12, M=0, Y=0

   每個每週完整備份皆會保留 12 週。

- W=6, M=12, Y=10, WeekOfYear=16

   每週完整備份將保留六周。 但每月的第 1 個完整備份除外，這個備份會保留 12 個月。 以及每年的第 16 週的完整備份除外，這個備份會保留 10 年。 

下表說明以下原則的長期備份日程和到期日：

W=12 週 (84 天)、M=12 個月 (365 天)、Y=10 年 (3650 天)、WeekOfYear=15 (4 月 15 日後那一周)

   ![ITR 範例](./media/sql-database-long-term-retention/ltr-example.png)



如果修改上述策略並設置 W=0（無每週備份），則備份副本的節奏將更改，如上表所示，突出顯示日期。 用來保留這些備份所需的儲存體數量會跟著減少。 

> [!IMPORTANT]
> 單個 LTR 備份的時間由 Azure SQL 資料庫控制。 您不能手動創建 LTR 備份或控制創建備份的時間。 配置 LTR 策略後，第一個 LTR 備份最多可能需要 7 天才能顯示在可用備份清單中。  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>異地複寫和長期備份保留

如果使用活動異地複製或容錯移轉組作為業務連續性解決方案，則應為最終容錯移轉做好準備，並在異地次要資料庫上配置相同的 LTR 策略。 LTR 存儲成本不會增加，因為備份不是從次要資料庫生成的。 只有當次要變成主要時，備份才會建立。 它可確保在觸發容錯移轉和主移動到次要區域時不中斷生成 LTR 備份。 

> [!NOTE]
> 當原始主資料庫從導致容錯移轉的中斷中恢復時，它將成為新的次要資料庫。 因此，備份的建立不會繼續，而且現有的 LTR 原則將不會生效，除非它再次變成主要資料庫。 

## <a name="configure-long-term-backup-retention"></a>設定長期備份保留期

要瞭解如何使用 Azure 門戶或 PowerShell 配置長期保留，請參閱[管理 Azure SQL 資料庫的長期備份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-database-from-ltr-backup"></a>從 LTR 備份還原資料庫

若要從 LTR 儲存體還原資料庫，可以依時間戳記選取特定備份。 可將資料庫還原至原始資料庫相同訂用帳戶底下的任何現有伺服器。 要瞭解如何使用 Azure 門戶或 PowerShell 從 LTR 備份還原資料庫，請參閱[管理 Azure SQL 資料庫的長期備份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="next-steps"></a>後續步驟

因為資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一環。 若要深入了解其他 SQL Database 商務持續性解決方案，請參閱[商務持續性概觀](sql-database-business-continuity.md)。
