---
title: 雲端商務持續性-資料庫復原
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 瞭解 Azure SQL Database 和 SQL 受控執行個體如何支援雲端商務持續性和資料庫復原，以及協助讓任務關鍵性的雲端應用程式保持運作。
keywords: business continuity,cloud business continuity,database disaster recovery,database recovery,商務持續性,雲端商務持續性,資料庫災害復原,資料庫復原
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: a69332f1534e32a85ce084289dd00533612cc282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327556"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>使用 Azure SQL Database 的商務持續性概觀
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 SQL 受控執行個體中的**商務持續性**是指可讓您的企業在中斷的情況下繼續運作的機制、原則和程式，特別是其運算基礎結構。 在大部分的情況下，SQL Database 和 SQL 受控執行個體將會處理雲端環境中可能發生的干擾性事件，並讓您的應用程式和商務程式持續執行。 不過，SQL Database 會自動處理一些干擾性事件，例如：

- 使用者不小心刪除或更新了資料表中的資料列。
- 惡意攻擊者接著刪除資料或刪除資料庫。
- 地震導致電源中斷和暫時性停用的資料中心。

本總覽說明 SQL Database 和 SQL 受控執行個體供應商務持續性和嚴重損壞修復的功能。 了解選項、建議和教學課程，以從可能導致資料遺失或造成資料庫和應用程式無法使用的干擾性事件中復原。 了解當使用者或應用程式錯誤影響資料完整性、Azure 區域中斷，或您的應用程式需要維護時該如何處理。

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>您可用來提供商務持續性的 SQL Database 功能

就資料庫的觀點而言，有四個主要可能中斷案例：

- 影響資料庫節點的本機硬體或軟體失敗，例如磁碟機失敗。
- 通常由應用程式 Bug 或人為錯誤所造成的資料損毀或刪除。 這類失敗是應用程式特有的，而且通常無法由資料庫服務偵測到。
- 可能由天然災害所造成的資料中心中斷。 此案例需要某種程度的異地備援，讓應用程式能容錯移轉到替代資料中心。
- 升級或維護錯誤、規劃的基礎結構維護或升級期間發生的非預期問題，可能需要快速復原到先前的資料庫狀態。

為了減輕本機硬體和軟體失敗，SQL Database 包含 [高可用性架構](high-availability-sla.md)，可確保從這些失敗的自動復原，最高可達99.995% 的可用性 SLA。  

為了保護您的業務免于資料遺失，SQL Database 和 SQL 受控執行個體每隔12小時自動建立完整的資料庫備份、每隔12小時進行一次差異資料庫備份，以及每 5-10 分鐘備份一次交易記錄。 所有服務層級的備份至少會儲存在 GRS 儲存體中，至少7天。 所有服務層級，但基本支援可設定時間點還原的備份保留期限，最多35天。

SQL Database 和 SQL 受控執行個體也提供數種商務持續性功能，可讓您用來減輕各種未規劃的案例。

- [時態表](../temporal-tables.md)可讓您從任何時間點還原資料列版本。
- 內[建的自動備份](automated-backups-overview.md)和[時間點還原](recovery-using-backups.md#point-in-time-restore)可讓您將完整資料庫還原至設定的保留期間內的某個時間點（最多35天）。
- 如果**伺服器尚未刪除**，您可以將[已刪除的資料庫還原](recovery-using-backups.md#deleted-database-restore)到它被刪除的時間點。
- [長期備份保留](long-term-retention-overview.md)可讓您最多保留備份 10 年。 這是 SQL 受控執行個體的有限公開預覽版
- [主動式異地](active-geo-replication-overview.md) 複寫可讓您建立可讀取複本，並在發生資料中心中斷或應用程式升級時，手動容錯移轉至任何複本。
- [自動容錯移轉群組](auto-failover-group-overview.md#terminology-and-capabilities) 可讓應用程式在發生資料中心中斷時自動復原。

## <a name="recover-a-database-within-the-same-azure-region"></a>復原相同 Azure 區域內的資料庫

您可以使用自動資料庫備份，將資料庫還原至過去的時間點。 如此一來，您就可以從人為錯誤所造成的資料損毀中復原。 還原時間點可讓您在相同伺服器中建立新的資料庫，以代表發生損毀事件之前的資料狀態。 對於大部分的資料庫，還原作業需要少於12小時的時間。 復原非常大型或非常活躍的資料庫可能需要較長的時間。 如需復原時間的詳細資訊，請參閱[資料庫復原時間](recovery-using-backups.md#recovery-time)。

如果還原時間點的最大支援備份保留期限 (PITR) 不足以供您的應用程式使用，您可以為資料庫 () 設定長期保留 (LTR) 原則。 如需詳細資訊，請參閱[長期備份保留](long-term-retention-overview.md)。

## <a name="compare-geo-replication-with-failover-groups"></a>比較異地複寫與容錯移轉群組

[自動容錯移轉群組](auto-failover-group-overview.md#terminology-and-capabilities) 會簡化 [異地](active-geo-replication-overview.md) 複寫的部署和使用，並新增其他功能，如下表所述：

|                                              | 異地複寫 | 容錯移轉群組  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatic failover**                          |     否          |      是         |
| **同時容錯移轉多個資料庫**  |     否          |      是         |
| **使用者必須在容錯移轉之後更新連接字串**      |     是         |      否          |
| **SQL 受控執行個體支援**                   |     否          |      是         |
| **可位於與主要複本相同的區域**             |     是         |      否          |
| **多個複本**                            |     是         |      否          |
| **支援讀取縮放**                          |     是         |      是         |


## <a name="recover-a-database-to-the-existing-server"></a>將資料庫復原到現有的伺服器

雖然很罕見，但 Azure 資料中心可能會中斷。 發生中斷時，可能只會讓業務中斷幾分鐘，也可能會持續幾小時。

- 其中一個選項是在資料中心中斷結束時等候資料庫恢復上線。 這適用於可以容忍資料庫離線的應用程式。 例如，您不需要不斷處理的開發專案或免費試用版。 當資料中心中斷時，您不會知道中斷的持續時間，因此只有在您不需要資料庫一段時間時，此選項才會運作。
- 另一個選項是使用[異地備援資料庫備份](recovery-using-backups.md#geo-restore) (異地還原)，在任何 Azure 區域中的任何伺服器上還原資料庫。 異地還原使用異地備援備份做為其來源，即使因為中斷而無法存取資料庫或資料中心，也能用來復原資料庫。
- 最後，如果您已使用 [主動式異地](active-geo-replication-overview.md) 複寫或資料庫或資料庫的 [自動容錯移轉群組](auto-failover-group-overview.md) 來設定異地次要資料庫，您就可以快速地從中斷復原。 根據您選擇的這些技術，您可以使用手動或自動容錯移轉。 雖然容錯移轉本身只需要幾秒鐘的時間就能完成，服務將需要至少 1 小時才能啟動。 這是依據中斷的規模來確保容錯移轉之正當性的必要作法。 此外，基於非同步複寫的本質，容錯移轉可能會造成小規模的資料遺失。

當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 完全復原應用程式所需的時間，也稱為復原時間目標 (RTO) 。 您也必須瞭解最新的資料更新期間 (時間間隔) 應用程式在從未規劃的干擾性事件中復原時，可容忍遺失的情況。 潛在的資料遺失稱為復原點目標 (RPO) 。

不同的復原方法會提供不同層級的 RPO 和 RTO。 您可以選擇特定的復原方法，或使用方法的組合來達成完整的應用程式復原。 下表比較每個修復選項的 RPO 和 RTO。 自動容錯移轉群組會簡化異地複寫的部署和使用，並新增其他功能，如下表所述。

| 修復方法 | 復原時間目標 (RTO) | 復原點目標 (RPO) |
| --- | --- | --- |
| 從異地複寫備份進行異地還原 | 12小時 | 1 小時 |
| 自動容錯移轉群組 | 1 小時 | 5秒 |
| 手動資料庫容錯移轉 | 30秒 | 5秒 |

> [!NOTE]
> *手動資料庫容錯移轉* 是指使用未 [計畫的模式](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)，將單一資料庫容錯移轉到其異地複寫的次要資料庫。
請參閱此文章稍早的表格，以取得自動容錯移轉 RTO 和 RPO 的詳細資料。

如果您的應用程式符合下列任何準則，請使用自動容錯移轉群組：

- 是關鍵性應用程式。
- 具有不允許 12 小時或以上之停機時間的服務等級協定 (SLA)。
- 停機可能會衍生財務責任。
- 具有很高的資料變更率，且無法接受為時 1 小時的資料遺失。
- 與潛在的財務責任和相關企業損失相較下，使用主動式異地複寫的額外成本較低。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

您可以根據您的應用程式需求，選擇使用資料庫備份和主動式異地複寫的組合。 如需獨立資料庫的設計考慮，以及使用這些商務持續性功能的彈性集區的討論，請參閱 [設計應用程式以進行雲端損毀修復](designing-cloud-solutions-for-disaster-recovery.md) 和彈性集區嚴重損壞 [修復策略](disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

下列各節概述使用資料庫備份或主動式異地複寫來進行復原的步驟。 如需詳細步驟，包括規劃需求、復原後步驟，以及如何模擬中斷以執行嚴重損壞修復演練的詳細資訊，請參閱 SQL Database 中的 [復原資料庫免于中斷](disaster-recovery-guidance.md)。

### <a name="prepare-for-an-outage"></a>準備中斷

無論您要使用何種商務持續性功能，您都必須︰

- 識別並準備目標伺服器，包括伺服器層級 IP 防火牆規則、登入和 master 資料庫層級權限。
- 決定如何將用戶端和用戶端應用程式重新導向到新的伺服器
- 記錄其他相依性，例如稽核設定和警示

如果您沒有適當地準備，在容錯移轉或資料庫復原後讓應用程式上線將會多花費時間，而且也可能需要在有壓力的情況下進行疑難排解 - 這是不良的情況組合。

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>容錯移轉至異地複寫的次要資料庫

如果您使用主動式異地複寫或自動容錯移轉群組作為復原機制，則可設定自動容錯移轉原則或使用手動未規劃的 [容錯移轉](active-geo-replication-configure-portal.md#initiate-a-failover)。 啟動容錯移轉後，次要資料庫就會成為新的主要資料庫，並準備好記錄新的交易以及回應查詢 - 只會遺失尚未複寫的資料。 如需關於設計容錯移轉程序的資訊，請參閱[設計雲端災害復原應用程式](designing-cloud-solutions-for-disaster-recovery.md)。

> [!NOTE]
> 當資料中心恢復連線時，舊的主要複本會自動重新連線到新的主要複本，並成為次要資料庫。 若您需要將主要複本重新定位至原始區域，可手動啟動規劃的容錯移轉 (容錯回復)。

### <a name="perform-a-geo-restore"></a>執行異地還原

如果您使用自動備份搭配異地備援儲存體 (預設為啟用)，您可以使用[異地還原](disaster-recovery-guidance.md#recover-using-geo-restore)來復原資料庫。 復原通常會在 12 小時內進行，且可能遺失最多 1 小時的資料 (視最後一次記錄備份的建立並複寫時間而定)。 在復原完成之前，資料庫無法記錄任何交易或回應任何查詢。 請注意，異地還原只會將資料庫還原至最後一個可用的時間點。

> [!NOTE]
> 如果資料中心在您將應用程式切換到復原的資料庫之前重新上線，您可以取消復原。

### <a name="perform-post-failover--recovery-tasks"></a>執行容錯移轉後/復原後工作

從其中任何一種復原機制復原之後，您都必須執行下列額外的工作，您的使用者和應用程式才能回復正常執行狀態︰

- 將用戶端和用戶端應用程式重新導向至新的伺服器和還原的資料庫。
- 確定有適當的伺服器層級 IP 防火牆規則供使用者連線或使用[資料庫層級防火牆](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)，才能啟用適當的規則。
- 確定已 (適當的登入和 master 資料庫層級許可權，或使用 [包含的使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)) 。
- 適當地設定審核。
- 適當地設定警示。

> [!NOTE]
> 如果您使用容錯移轉群組，並使用讀寫接聽程式來連接到資料庫，則在容錯移轉之後的重新導向將會自動且透明地在應用程式中發生。

## <a name="upgrade-an-application-with-minimal-downtime"></a>在最少停機時間的情況下升級應用程式

有時，應用程式會因為計劃性維護 (例如應用程式升級) 而必須離線。 [管理應用程式升級](manage-application-rolling-upgrade.md) 說明如何使用「主動式異地複寫」來輪流升級雲端應用程式，以將升級時的停機時間縮到最短，並提供發生錯誤時的復原路徑。

## <a name="next-steps"></a>後續步驟

如需單一資料庫和彈性集區之應用程式設計考慮的討論，請參閱 [設計雲端嚴重損壞修復的應用程式](designing-cloud-solutions-for-disaster-recovery.md) 和彈性集區嚴重損壞 [修復策略](disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
