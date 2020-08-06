---
title: 長期備份保留期
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解 Azure SQL Database 如何 & Azure SQL 受控執行個體支援透過長期保留原則，最多可儲存10年的完整資料庫備份。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 4b2324c480ef81ef241f4d639c22c2ed4dd1545b
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808842"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>長期保留-Azure SQL Database 和 Azure SQL 受控執行個體

許多應用程式都有法規、合規性或其他商務用途，需要您將資料庫備份保留超過 Azure SQL Database 和 Azure SQL 受控執行個體[自動備份](automated-backups-overview.md)所提供的7-35 天。 藉由使用「長期保留 (LTR) 」功能，您可以在 Azure Blob 儲存體中儲存指定的 SQL Database 和 SQL 受控執行個體完整備份，並具有長達10年的讀取權限異地多餘儲存體。 之後您可以將任何備份還原為新的資料庫。

此外，SQL 受控執行個體引進[可設定的備份儲存體冗余](automated-backups-overview.md#backup-storage-redundancy)，讓您在本機冗余 (LRS) 、區域冗余 (ZRS) 或異地冗余 (RA GRS) [儲存體 blob](../../storage/common/storage-redundancy.md)之間選擇彈性。 此選項目前只能在建立受控實例進程期間使用，而且一旦布建資源之後就無法變更。

可以針對 Azure SQL Database 啟用長時間保留，而且在 Azure SQL 受控執行個體的公開預覽中是有限的。 本文提供長期保留的概念總覽。 若要設定長期保留，請參閱[configure AZURE SQL DATABASE ltr](long-term-backup-retention-configure.md)和[configure AZURE SQL 受控執行個體 ltr](../managed-instance/long-term-backup-retention-configure.md)。 

> [!NOTE]
> 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代超過 35 天的 LTR。


## <a name="how-long-term-retention-works"></a>長期保留的運作方式
     
長期備份保留 (LTR) 會運用[自動建立](automated-backups-overview.md)的完整資料庫備份來啟用時間點還原 (PITR)。 如果設定 LTR 原則，則會將這些備份複製到不同的 blob 以進行長期儲存。 複製是背景工作，對資料庫工作負載不會有任何效能影響。 SQL Database 中每個資料庫的 LTR 原則也可以指定建立 LTR 備份的頻率。

若要啟用 LTR，您可以使用下列四個參數的組合來定義原則：每週備份保留 (W) 、每月備份保留期 (M) 、每年備份保留 (Y) 和年中的周 (WeekOfYear) 。 如果指定 W，每週一次的備份會複製到長期儲存體。 如果指定 M，每個月的第一個備份會複製到長期儲存體。 如果指定 Y，在以 WeekOfYear 指定的當週進行的一次備份會複製到長期儲存體。 如果指定的 WeekOfYear 在設定原則時為過去，則會在下一年建立第一個 LTR 備份。 每個備份都會根據建立 LTR 備份時所設定的原則參數，保留在長期儲存體中。

> [!NOTE]
> LTR 原則的任何變更僅適用于未來的備份。 例如，如果每週備份保留 (W) 、每月備份保留 (M) ，或每年備份保留期 (Y) 修改，新的保留設定將只會套用至新的備份。 將不會修改現有備份的保留期。 如果您想要在保留期限到期之前刪除舊的 LTR 備份，您將需要[手動刪除備份](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)。
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

   ![ITR 範例](./media/long-term-retention-overview/ltr-example.png)


如果您修改上述原則，並設定 W = 0 (不會) 每週備份，備份複本的步調會依照上表中反白顯示的日期變更。 用來保留這些備份所需的儲存體數量會跟著減少。 

> [!IMPORTANT]
> 個別 LTR 備份的時間是由 Azure 所控制。 您無法手動建立 LTR 備份或控制建立備份的時間。 設定 LTR 原則之後，最多可能需要7天，第一個 LTR 備份才會顯示在可用備份清單上。  


## <a name="geo-replication-and-long-term-backup-retention"></a>異地複寫和長期備份保留

如果您使用主動式異地複寫或容錯移轉群組做為商務持續性解決方案，您應該準備好進行最終容錯移轉，並在次要資料庫或實例上設定相同的 LTR 原則。 當備份不是從次要複本產生時，您的 LTR 儲存成本不會增加。 只有當次要複本變成主要複本時，才會建立備份，備份將會建立。 它可確保在觸發容錯移轉且主要區域移至次要地區時，不中斷的 LTR 備份產生。 

> [!NOTE]
> 當原始的主資料庫從造成容錯移轉的中斷復原時，它會變成新的次要複本。 因此，備份的建立不會繼續，而且現有的 LTR 原則將不會生效，除非它再次變成主要資料庫。 

## <a name="sql-managed-instance-support"></a>SQL 受控執行個體支援

搭配使用長期備份保留與 Azure SQL 受控執行個體具有下列限制：

- **有限的公開預覽**-此預覽僅適用于 EA 和 CSP 訂用帳戶，且受限於有限的可用性。  
- [**僅限 PowerShell**](../managed-instance/long-term-backup-retention-configure.md) -目前不支援 Azure 入口網站。 必須使用 PowerShell 來啟用 LTR。 

若要要求註冊，請建立[Azure 支援票證](https://azure.microsoft.com/support/create-ticket/)。 針對 [問題類型]，選取 [技術問題]，針對 [服務] 選擇 [SQL 受控執行個體]，並針對 [問題類型] 選取 [**備份、還原和商務持續性/長期備份保留**]。 在您的要求中，請陳述您想要註冊的限時公開預覽版本為 LTR for SQL 受控執行個體。

## <a name="configure-long-term-backup-retention"></a>設定長期備份保留期

您可以使用適用于 Azure SQL Database 的 Azure 入口網站和 PowerShell，以及適用于 Azure SQL 受控執行個體的 PowerShell 來設定長期備份保留。 若要從 LTR 儲存體還原資料庫，可以依時間戳記選取特定備份。 資料庫可以還原到與原始資料庫相同的訂用帳戶下的任何現有伺服器或受控實例。

若要瞭解如何使用 Azure 入口網站或 PowerShell 來設定長期保留或從 SQL Database 備份還原資料庫，請參閱[管理 Azure SQL Database 長期備份保留](long-term-backup-retention-configure.md)

若要瞭解如何使用 PowerShell 從 SQL 受控執行個體的備份中設定長期保留或還原資料庫，請參閱[管理 AZURE SQL 受控執行個體長期備份保留](../managed-instance/long-term-backup-retention-configure.md)。

若要從 LTR 儲存體還原資料庫，可以依時間戳記選取特定備份。 可將資料庫還原至原始資料庫相同訂用帳戶底下的任何現有伺服器。 若要瞭解如何使用 Azure 入口網站或 PowerShell 從 LTR 備份還原資料庫，請參閱[管理 Azure SQL Database 長期備份保留](long-term-backup-retention-configure.md)。 在您的要求中，請確定您想要在限時公開預覽中，以 LTR for SQL 受控執行個體來註冊。

## <a name="next-steps"></a>後續步驟

因為資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一環。 若要深入了解其他 SQL Database 商務持續性解決方案，請參閱[商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
 
