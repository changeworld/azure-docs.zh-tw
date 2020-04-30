---
title: 長期備份保留期
description: 瞭解 Azure SQL Database 如何透過長期保留原則，支援最多10年的時間儲存完整資料庫備份。
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
ms.openlocfilehash: e85c8c0990a2659dba1f254a8f1aa7c7be7852eb
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508083"
---
# <a name="azure-sql-database-long-term-retention"></a>SQL Database 長期保留

許多應用程式具有法規、相容性或其他商務用途，需要您保留 Azure SQL Database [自動備份](sql-database-automated-backups.md)所提供超過 7-35 天的資料庫備份。 藉由使用長期保留（LTR）功能，您可以在 Azure Blob 儲存體中儲存指定的 SQL database 完整備份，並具有最多10年的讀取權限異地多餘儲存體。 之後您可以將任何備份還原為新的資料庫。 如需 Azure 儲存體冗余的詳細資訊，請參閱[Azure 儲存體冗余](../storage/common/storage-redundancy.md)。 

可以針對單一和集區資料庫啟用長時間保留，而且在 Azure SQL Database 受控實例的公開預覽中是有限的。 

> [!NOTE]
> 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代超過 35 天的 LTR。


## <a name="how-sql-database-long-term-retention-works"></a>SQL Database 長期保留如何運作

長期備份保留 (LTR) 會運用[自動建立](sql-database-automated-backups.md)的完整資料庫備份來啟用時間點還原 (PITR)。 如果設定 LTR 原則，則會將這些備份複製到不同的 blob 以進行長期儲存。 複製是背景工作，對資料庫工作負載不會有任何效能影響。 每個 SQL 資料庫的 LTR 原則也可以指定建立 LTR 備份的頻率。

若要啟用 LTR，您可以使用下列四個參數的組合來定義原則：每週備份保留（W）、每月備份保留（M）、每年備份保留（Y）和年中的周（WeekOfYear）。 如果指定 W，每週一次的備份會複製到長期儲存體。 如果指定 M，每個月的第一個備份會複製到長期儲存體。 如果指定 Y，在以 WeekOfYear 指定的當週進行的一次備份會複製到長期儲存體。 如果指定的 WeekOfYear 在設定原則時為過去，則會在下一年建立第一個 LTR 備份。 每個備份都會根據建立 LTR 備份時所設定的原則參數，保留在長期儲存體中。

> [!NOTE]
> LTR 原則的任何變更僅適用于未來的備份。 例如，如果每週備份保留（W）、每月備份保留（M）或每年備份保留（Y）都已修改，新的保留設定將只會套用至新的備份。 將不會修改現有備份的保留期。 如果您想要在保留期限到期之前刪除舊的 LTR 備份，您將需要[手動刪除備份](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)。
> 

LTR 原則的範例：

-  W=0, M=0, Y=5, WeekOfYear=3

   每年的第三次完整備份將會保留五年。
   
- W=0, M=3, Y=0

   每個月的第一個完整備份將會保留三個月。

- W=12, M=0, Y=0

   每個每週完整備份皆會保留 12 週。

- W=6, M=12, Y=10, WeekOfYear=16

   每週完整備份將會保留六周。 但每月的第 1 個完整備份除外，這個備份會保留 12 個月。 以及每年的第 16 週的完整備份除外，這個備份會保留 10 年。 

下表說明以下原則的長期備份日程和到期日：

W=12 週 (84 天)、M=12 個月 (365 天)、Y=10 年 (3650 天)、WeekOfYear=15 (4 月 15 日後那一周)

   ![ITR 範例](./media/sql-database-long-term-retention/ltr-example.png)



如果您修改上述原則並設定 W = 0 （沒有每週備份），備份複本的步調會依照上表中反白顯示的日期而變更。 用來保留這些備份所需的儲存體數量會跟著減少。 

> [!IMPORTANT]
> 個別 LTR 備份的時間是由 Azure SQL Database 所控制。 您無法手動建立 LTR 備份或控制建立備份的時間。 設定 LTR 原則之後，最多可能需要7天，第一個 LTR 備份才會顯示在可用備份清單上。  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>異地複寫和長期備份保留

如果您使用主動式異地複寫或容錯移轉群組做為商務持續性解決方案，您應該準備進行最終容錯移轉，並在異地次要資料庫上設定相同的 LTR 原則。 因為不會從次要複本產生備份，所以您的 LTR 儲存成本不會增加。 只有當次要變成主要時，備份才會建立。 它可確保在觸發容錯移轉且主要區域移至次要地區時，不中斷的 LTR 備份產生。 

> [!NOTE]
> 當原始的主資料庫從造成容錯移轉的中斷復原時，它會變成新的次要複本。 因此，備份的建立不會繼續，而且現有的 LTR 原則將不會生效，除非它再次變成主要資料庫。 

## <a name="managed-instance-support"></a>受控執行個體支援

搭配使用長期備份保留與 Azure SQL Database 受控實例具有下列限制：

- **有限的公開預覽**-此預覽僅適用于 EA 和 CSP 訂用帳戶，且受限於有限的可用性。  
- [**僅限 PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) -目前沒有 Azure 入口網站支援。 必須使用 PowerShell 來啟用 LTR。 

若要要求註冊，請建立[Azure 支援票證](https://azure.microsoft.com/support/create-ticket/)。 針對 [問題類型]，選取 [技術問題]，針對 [服務] 選擇 [SQL Database 受控執行個體]，並針對 [問題類型] 選取 [**備份、還原和商務持續性/長期備份保留**]。 在您的要求中，請陳述您想要註冊為受控實例之 LTR 的有限公開預覽版。

## <a name="configure-long-term-backup-retention"></a>設定長期備份保留期

若要瞭解如何使用 Azure 入口網站或 PowerShell 設定長期保留，請參閱[管理 Azure SQL Database 長期備份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-database-from-ltr-backup"></a>從 LTR 備份還原資料庫

若要從 LTR 儲存體還原資料庫，可以依時間戳記選取特定備份。 可將資料庫還原至原始資料庫相同訂用帳戶底下的任何現有伺服器。 若要瞭解如何使用 Azure 入口網站或 PowerShell 從 LTR 備份還原資料庫，請參閱[管理 Azure SQL Database 長期備份保留](sql-database-long-term-backup-retention-configure.md)。 在您的要求中，請確定您想要在受管理的實例之 LTR 公開預覽中註冊。

## <a name="next-steps"></a>後續步驟

因為資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一環。 若要深入了解其他 SQL Database 商務持續性解決方案，請參閱[商務持續性概觀](sql-database-business-continuity.md)。
