---
title: 雲端商務持續性-資料庫復原
description: 了解 Azure SQL Database 如何支援雲端商務持續性和資料庫復原，以及如何協助維持任務關鍵性雲端應用程式持續執行。
keywords: business continuity,cloud business continuity,database disaster recovery,database recovery,商務持續性,雲端商務持續性,資料庫災害復原,資料庫復原
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096858"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>使用 Azure SQL Database 的商務持續性概觀

Azure SQL Database 中的**商務持續性**是指在面臨中斷時可讓您的企業持續運作的機制、原則和程序 (特別是其運算基礎結構)。 在大部分情況下，Azure SQL Database 會處理雲端環境中可能發生的干擾性事件，並且讓您的應用程式和商務程序持續執行。 不過，SQL Database 會自動處理一些干擾性事件，例如：

- 使用者不小心刪除或更新了資料表中的資料列。
- 惡意攻擊者接著刪除資料或刪除資料庫。
- 地震導致電源中斷，而且資料中心暫時停用。

本概觀說明 Azure SQL Database 針對商務持續性和災害復原所提供的功能。 了解選項、建議和教學課程，以從可能導致資料遺失或造成資料庫和應用程式無法使用的干擾性事件中復原。 了解當使用者或應用程式錯誤影響資料完整性、Azure 區域中斷，或您的應用程式需要維護時該如何處理。

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>您可用來提供商務持續性的 SQL Database 功能

就資料庫的觀點而言，有四個主要可能中斷案例：

- 影響資料庫節點的本機硬體或軟體失敗，例如磁碟機失敗。
- 通常由應用程式 Bug 或人為錯誤所造成的資料損毀或刪除。 這類失敗是應用程式特定的，而且通常無法由資料庫服務偵測到。
- 可能由天然災害所造成的資料中心中斷。 此案例需要某種程度的異地備援，讓應用程式能容錯移轉到替代資料中心。
- 升級或維護錯誤，在規劃的基礎結構維護或升級期間發生的非預期問題，可能需要快速回復至先前的資料庫狀態。

為了減輕本機硬體和軟體失敗，SQL Database 包含[高可用性架構](sql-database-high-availability.md)，可保證從這些失敗中自動復原，最高可達99.995% 的可用性 SLA。  

為了保護您的企業免于資料遺失，SQL Database 會每週自動建立完整的資料庫備份，每隔12小時一次差異資料庫備份，而交易記錄備份每 5-10 分鐘一次。 所有服務層級的備份會儲存在 GRS 儲存體中至少7天。 所有服務層級（基本支援可設定的時間點還原備份保留期限），最多35天。 

SQL Database 也提供數種商務持續性功能，您可以用來緩和各種未規劃的案例。 

- [時態表](sql-database-temporal-tables.md)可讓您從任何時間點還原資料列版本。
- 內[建的自動備份](sql-database-automated-backups.md)和[時間點還原](sql-database-recovery-using-backups.md#point-in-time-restore)可讓您將完整的資料庫還原到設定的保留期間內的某個時間點，最多35天。
- 如果 [SQL Database 伺服器尚未刪除](sql-database-recovery-using-backups.md#deleted-database-restore)，您可以**將已刪除的資料庫還原**到它被刪除的時間點。
- [長期備份保留](sql-database-long-term-retention.md)可讓您最多保留備份 10 年。
- [主動式異地複寫](sql-database-active-geo-replication.md)可讓您建立可讀取複本，並在資料中心中斷或應用程式升級時，手動容錯移轉至任何複本。
- [自動容錯移轉群組](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities)可在發生資料中心中斷時，允許應用程式進行自動復原。

## <a name="recover-a-database-within-the-same-azure-region"></a>復原相同 Azure 區域內的資料庫

您可以使用自動資料庫備份，將資料庫還原到過去的某個時間點。 如此一來，您就可以從人為錯誤所造成的資料損毀中復原。 還原時間點可讓您在相同伺服器中建立新的資料庫，以代表損毀事件之前的資料狀態。 對於大部分的資料庫，還原作業所花費的時間少於12小時。 復原非常大或非常活躍的資料庫可能需要較長的時間。 如需復原時間的詳細資訊，請參閱[資料庫復原時間](sql-database-recovery-using-backups.md#recovery-time)。 

如果還原時間點的最大支援備份保留期限（PITR）對您的應用程式而言不足，您可以為資料庫設定長期保留（LTR）原則來加以擴充。 如需詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

## <a name="compare-geo-replication-with-failover-groups"></a>比較異地複寫與容錯移轉群組

[自動容錯移轉群組](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities)會簡化[異地](sql-database-active-geo-replication.md)複寫的部署和使用，並新增額外的功能，如下表所述：

|                                              | 異地複寫 | 容錯移轉群組  |
|:---------------------------------------------| :-------------- | :----------------|
| 自動容錯移轉                           |     否          |      是         |
| 同時故障切換多個資料庫  |     否          |      是         |
| 使用者必須在容錯移轉之後更新連接字串      |     是         |      否          |
| 支援的受控實例                   |     否          |      是         |
| 可以位於與主要相同的區域             |     是         |      否          |
| 多個複本                            |     是         |      否          |
| 支援讀取規模                          |     是         |      是         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>將資料庫復原到現有的伺服器

雖然很罕見，但 Azure 資料中心也可能會有中斷的時候。 發生中斷時，可能只會讓業務中斷幾分鐘，也可能會持續幾小時。

- 其中一個選項是在資料中心中斷結束時等待您的資料庫重新上線。 這適用於可以容忍資料庫離線的應用程式。 例如，您不需要不斷處理的開發專案或免費試用版。 當資料中心中斷時，您不會知道中斷會持續多久，因此這個選項僅適用於您可以一段時間暫時不需要資料庫。
- 另一個選項是使用[異地備援資料庫備份](sql-database-recovery-using-backups.md#geo-restore) (異地還原)，在任何 Azure 區域中的任何伺服器上還原資料庫。 異地還原使用異地備援備份做為其來源，即使因為中斷而無法存取資料庫或資料中心，也能用來復原資料庫。
- 最後，如果您已經使用作用中[異地](sql-database-active-geo-replication.md)複寫或資料庫或資料庫的[自動容錯移轉群組](sql-database-auto-failover-group.md)來設定異地次要資料庫，您可以快速地從中斷復原。 根據您選擇的這些技術，您可以使用手動或自動容錯移轉。 雖然容錯移轉本身只需要幾秒鐘的時間就能完成，服務將需要至少 1 小時才能啟動。 這是依據中斷的規模來確保容錯移轉之正當性的必要作法。 此外，基於非同步複寫的本質，容錯移轉可能會造成小規模的資料遺失。 

當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 應用程式完全復原所需的時間，也稱為復原時間目標（RTO）。 您也需要瞭解從未規劃的干擾性事件復原時，應用程式可容忍遺失的最近資料更新（時間間隔）最長期間。 可能的資料遺失稱為復原點目標（RPO）。

不同的復原方法提供不同層級的 RPO 和 RTO。 您可以選擇特定的復原方法，或使用方法的組合來達到完整的應用程式復原。 下表比較每個修復選項的 RPO 和 RTO。 自動容錯移轉群組會簡化異地複寫的部署和使用，並新增額外的功能，如下表所述。

| 修復方法 | RTO | RPO |
| --- | --- | --- | 
| 從異地複寫備份進行異地還原 | 12小時 | 1 小時 |
| 自動容錯移轉群組 | 1 小時 | 5秒 |
| 手動資料庫容錯移轉 | 30秒 | 5秒 |

> [!NOTE]
> *手動資料庫容錯移轉*是指使用未規劃的[模式](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities)，將單一資料庫容錯移轉到其異地複寫的次要複本。
請參閱此文章稍早的表格，以取得自動容錯移轉 RTO 和 RPO 的詳細資料。


如果您的應用程式符合下列任何準則，請使用自動容錯移轉群組：

- 是關鍵性應用程式。
- 具有不允許 12 小時或以上之停機時間的服務等級協定 (SLA)。
- 停機可能會衍生財務責任。
- 具有很高的資料變更率，且無法接受為時 1 小時的資料遺失。
- 與潛在的財務責任和相關企業損失相較下，使用主動式異地複寫的額外成本較低。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

您可以根據您的應用程式需求，選擇使用資料庫備份和主動式異地複寫的組合。 如需獨立資料庫的設計考慮，以及使用這些商務持續性功能的彈性集區的討論，請參閱[設計雲端嚴重損壞修復的應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和彈性集區嚴重損壞[修復策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

下列各節概述使用資料庫備份或主動式異地複寫來進行復原的步驟。 如需包括規劃需求的詳細步驟、復原後步驟，以及有關如何模擬中斷以執行災害復原演練的資訊，請參閱[從中斷復原 SQL Database](sql-database-disaster-recovery.md)。

### <a name="prepare-for-an-outage"></a>準備中斷

無論您要使用何種商務持續性功能，您都必須︰

- 識別並準備目標伺服器，包括伺服器層級 IP 防火牆規則、登入和 master 資料庫層級權限。
- 決定如何將用戶端和用戶端應用程式重新導向到新的伺服器
- 記錄其他相依性，例如稽核設定和警示

如果您沒有適當地準備，在容錯移轉或資料庫復原後讓應用程式上線將會多花費時間，而且也可能需要在有壓力的情況下進行疑難排解 - 這是不良的情況組合。

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>容錯移轉至異地複寫的次要資料庫

如果您使用主動式異地複寫或自動容錯移轉群組做為復原機制，則可以設定自動容錯移轉原則，或使用手動未規劃的[容錯移轉](sql-database-active-geo-replication-portal.md#initiate-a-failover)。 啟動容錯移轉後，次要資料庫就會成為新的主要資料庫，並準備好記錄新的交易以及回應查詢 - 只會遺失尚未複寫的資料。 如需關於設計容錯移轉程序的資訊，請參閱[設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

> [!NOTE]
> 資料中心恢復連線時，舊的主要複本會自動重新連線至新的主要複本，且會成為次要資料庫。 若您需要將主要複本重新定位至原始區域，可手動啟動規劃的容錯移轉 (容錯回復)。

### <a name="perform-a-geo-restore"></a>執行異地還原

如果您使用自動備份搭配異地備援儲存體 (預設為啟用)，您可以使用[異地還原](sql-database-disaster-recovery.md#recover-using-geo-restore)來復原資料庫。 復原通常會在 12 小時內進行，且可能遺失最多 1 小時的資料 (視最後一次記錄備份的建立並複寫時間而定)。 在復原完成之前，資料庫無法記錄任何交易或回應任何查詢。 請注意，異地還原只會將資料庫還原至最後一個可用的時間點。

> [!NOTE]
> 如果資料中心在您的應用程式切換到復原的資料庫之前就再次上線，您可以取消復原。

### <a name="perform-post-failover--recovery-tasks"></a>執行容錯移轉後/復原後工作

從其中任何一種復原機制復原之後，您都必須執行下列額外的工作，您的使用者和應用程式才能回復正常執行狀態︰

- 重新導向用戶端與用戶端應用程式到新的伺服器與還原的資料庫
- 確定有適當的伺服器層級 IP 防火牆規則供使用者連線或使用[資料庫層級防火牆](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)，才能啟用適當的規則。
- 確定有適當的登入和 master 資料庫層級權限 (或使用 [自主的使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- 依適當情況設定稽核
- 依適當情況設定警示

> [!NOTE]
> 如果您使用容錯移轉群組，並使用讀寫接聽程式來連線至資料庫，則容錯移轉後的重新導向將針對應用程式以自動且透明化的方式進行。

## <a name="upgrade-an-application-with-minimal-downtime"></a>在最少停機時間的情況下升級應用程式

有時，應用程式會因為計劃性維護 (例如應用程式升級) 而必須離線。 [管理應用程式升級](sql-database-manage-application-rolling-upgrade.md) 說明如何使用「主動式異地複寫」來輪流升級雲端應用程式，以將升級時的停機時間縮到最短，並提供發生錯誤時的復原路徑。

## <a name="next-steps"></a>後續步驟

如需獨立資料庫和彈性集區的應用程式設計考量探討，請參閱[設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
