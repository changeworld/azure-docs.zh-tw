---
title: 使用中的地理複寫
description: 使用主動式異地複寫，在相同或不同資料中心區域的 Azure SQL Database 中，建立個別資料庫的可讀取次要資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: 344d4e6b57082eb9ccfcd0642732d05216ad3978
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426315"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>建立和使用主動式異地複寫-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

主動式異地複寫是 Azure SQL Database 的功能，可讓您在相同或不同資料中心 (區域) 的伺服器上，建立個別資料庫的可讀取次要資料庫。

> [!NOTE]
> Azure SQL 受控執行個體不支援主動式異地複寫。 針對 SQL 受控執行個體實例的地理容錯移轉，請使用 [自動容錯移轉群組](auto-failover-group-overview.md)。

作用中異地複寫是設計為商務持續性解決方案，可在發生區域災害或大規模中斷時，讓應用程式執行個別資料庫的快速災害復原。 如果已啟用異地複寫，則應用程式可以在不同的 Azure 區域中起始容錯移轉至次要資料庫。 在相同或不同的區域中，最多可支援四個次要資料庫，次要資料庫也可以用於唯讀存取查詢。 容錯移轉必須由應用程式或使用者手動起始。 容錯移轉之後，新的主要資料庫會有不同的連接端點。

> [!NOTE]
> 主動式異地複寫會透過串流處理資料庫交易記錄檔來複寫變更。 它與 [異動複寫](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication)無關，這會藉由執行 DML (INSERT、UPDATE、DELETE) 命令來複寫變更。

下圖說明使用作用中異地複寫之異地備援雲端應用程式的一般設定。

![作用中異地複寫](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> SQL Database 也支援自動容錯移轉群組。 如需詳細資訊，請參閱使用[自動容錯移轉群組](auto-failover-group-overview.md)。

若您的主要資料庫因為任何原因而失敗，或只需要離線，您可以起始容錯移轉至任何次要資料庫。 容錯移轉至其中一個次要資料庫啟動時，所有其他次要複本會自動連結至新的主要複本。

您可以使用主動式異地複寫管理伺服器上或彈性集區中個別資料庫或一組資料庫的複寫和容錯移轉。 若要進行相關作業，您可以使用：

- [Azure 入口網站](active-geo-replication-configure-portal.md)
- [PowerShell：單一資料庫](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell：彈性集區](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL：單一資料庫或彈性集區](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API：單一資料庫](https://docs.microsoft.com/rest/api/sql/replicationlinks)

主動式異地複寫會利用資料庫引擎的 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 技術，使用快照集隔離，以非同步方式將主資料庫上已認可的交易複寫到次要資料庫。 自動容錯移轉群組在主動式異地複寫之上提供群組語意，但使用相同的非同步複寫機制。 雖然次要資料可能會在任何指定時間點稍微落後主要資料庫，但是次要資料保證絕對不會含有部分交易。 跨區域備援可讓應用程式從天然災害、災難性人為錯誤或惡意行為所造成的全部或部分資料中心永久遺失快速復原。 在 [商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)中可以找到特定的 RPO 資料。

> [!NOTE]
> 如果兩個區域之間的網路失敗，我們會每隔 10 秒重試，以便重新建立連線。

> [!IMPORTANT]
> 若要保證主要資料庫上的重大變更在容錯移轉之前會複寫至次要資料庫，您可以強制同步處理，以確保複寫重大變更 (例如，密碼更新)。 強制進行同步處理會對效能產生影響，因為它會封鎖呼叫執行緒，直到所有認可的交易都完成複寫為止。 如需詳細資訊，請參閱 [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync)。 若要監視主要資料庫和地區資料庫之間的複寫延遲，請參閱 [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)。

下圖顯示以美國中北部區域的主要資料庫和美國中南部區域的次要資料庫所設定的作用中異地複寫範例。

![異地複寫關聯性](./media/active-geo-replication-overview/geo-replication-relationship.png)

因為次要資料庫可讀取，所以可用來卸載唯讀工作負載，例如報告作業。 如果您使用主動式異地複寫，可以在具有主要資料庫的相同區域中建立次要資料庫，但這樣不會增加應用程式發生嚴重失敗時的恢復能力。 如果您使用自動容錯移轉群組，則一律要在不同區域中建立次要資料庫。

除了災害復原之外，主動式異地複寫還可用於下列案例︰

- **資料庫移轉**：您可以使用主動式異地複寫，將資料庫從一部伺服器遷移至另一部伺服器，並將停機時間最短。
- **應用程式升級**：您可以在應用程式升級期間建立額外的次要資料庫做為容錯回復複本。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。 在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。 這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。 所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。 因此，您需要識別所有相依服務並了解其提供的保證與功能。 然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。 如需設計損毀修復解決方案的詳細資訊，請參閱 [使用主動式異地複寫設計災難復原的雲端解決方案](designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="active-geo-replication-terminology-and-capabilities"></a>作用中異地複寫的術語和功能

- **自動非同步複寫**

  您只能藉由新增至現有資料庫來建立次要資料庫。 您可以在任何伺服器中建立次要資料庫。 一旦建立之後，次要資料庫就要填入從主要資料庫複製的資料。 這個程序稱為植入。 建立並植入次要資料庫之後，主要資料庫的更新會以非同步方式自動複製到次要資料庫。 非同步複寫表示交易會先在主要資料庫上受到認可，才會複製到次要資料庫。

- **可讀取的次要資料庫**

  應用程式可以存取次要資料庫，使用用於存取主要資料庫的相同安全性主體或不同安全性主體進行唯讀作業。 在快照集隔離模式中執行次要資料庫，以確保主要資料庫更新的複寫 (記錄重播) 不會被次要資料庫上執行的查詢延遲。

> [!NOTE]
> 如果主要資料庫上有結構描述更新，次要資料庫上的記錄重播會延遲， 因為後者需要次要資料庫上的結構描述鎖定。

> [!IMPORTANT]
> 您可以使用「異地複寫」在與主要複本相同的區域中建立次要資料庫。 您可以使用此次要資料庫來對相同區域中的唯讀工作負載進行負載平衡。 不過，相同區域中的次要資料庫不會提供額外的錯誤復原，因此不適合用于嚴重損壞修復的容錯移轉目標。 它也不保證可用性區域的隔離。 使用業務關鍵或 Premium 服務層搭配 [區域冗余](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) 設定或一般用途服務層級 [區域冗余](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) 設定，以達成可用性區域隔離。
>

- **規劃的容錯移轉**

  規劃的容錯移轉會在完整同步處理完成之後，切換主要和次要資料庫的角色。 這是不會導致資料遺失的線上操作。 作業的時間取決於主資料庫上需要同步處理的交易記錄大小。 規劃的容錯移轉是針對下列案例所設計：當資料遺失無法接受時， () 在生產環境中執行 DR 演練; (b) 將資料庫重新放置到不同的區域;和 (c) 在中斷之後，將資料庫還原到主要區域， (容錯回復) 。

- **未規劃的容錯移轉**

  非計劃性或強制容錯移轉會立即將次要資料庫切換為主要角色，而不會與主要資料庫進行任何同步處理。 認可至主要但未複寫至次要複本的任何交易都會遺失。 當主要複本無法存取時，這項作業會在中斷期間設計為復原方法，但是必須快速還原資料庫可用性。 當原始的主要複本重新上線時，它會自動重新連線，並成為新的次要資料庫。 在容錯移轉之前，所有未同步處理的交易都會保留在備份檔案中，但是不會與新的主要複本同步處理，以避免發生衝突。 這些交易必須與主資料庫的最新版本手動合併。

- **多個次要資料庫**

  每個主要資料庫最多可建立 4 個次要資料庫。 如果只有一個次要資料庫卻失敗了，應用程式會暴露在更高的風險中，直到建立新的次要資料庫。 如果有多個次要資料庫存在，即使其中一個次要資料庫失敗，應用程式仍會受到保護。 其他的次要資料庫也可用來擴充唯讀工作負載

  > [!NOTE]
  > 如果您使用主動式異地複寫建置分散在世界各地的應用程式，而且必須在四個以上的區域中提供唯讀的資料存取，您可以建立次要資料庫的次要資料庫 (稱為鏈結的程序)。 如此一來您就可以達到幾乎無限擴充的資料庫複寫。 此外，鏈結可減少來自主要資料庫的複寫額外負荷。 缺點是會增加分葉最尾端之次要資料庫上的複寫延遲。

- **彈性集區中的資料庫異地複寫**

  每個次要資料庫都可以分別參與彈性集區，或完全不在任何彈性集區中。 每個次要資料庫的集區選擇都不同，而且不相依於任何其他次要資料庫 (不論是主要還是次要) 的設定。 每個彈性集區都包含在單一區域內，因此相同拓撲中的多個次要資料庫永遠不會共用彈性集區。

- **使用者控制的容錯移轉和容錯回復**

  應用程式或使用者可以隨時明確也將次要資料庫切換到主要角色。 在實際中斷期間，應該使用「未計畫」選項，這會立即將次要升級為主要複本。 當失敗的主要資料庫復原，並且可再次使用時，系統會自動將復原的主要資料庫標示為次要資料庫，並讓它與新的主要資料庫保持更新。 由於複寫的非同步本質，如果主要資料庫在將最新的變更複寫至次要資料庫之前失敗，則非計劃的容錯移轉期間會有少量的資料遺失。 當具有多個次要資料庫的主要資料庫容錯移轉時，系統會自動重新設定複寫關聯性，並且將剩餘的次要資料庫連結至新升級的主要資料庫，而不需要任何使用者介入。 解決造成容錯移轉的中斷之後，可能想要讓應用程式返回主要區域。 若要這樣做，應使用「已規劃」選項來叫用容錯移轉命令。

## <a name="preparing-secondary-database-for-failover"></a>準備次要資料庫以進行容錯移轉

為確保您的應用程式可以在容錯移轉之後立即存取新的主要複本，請確定您的次要伺服器和資料庫的驗證需求已正確設定。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](active-geo-replication-security-configure.md)。 若要在容錯移轉之後保證合規性，請確定次要資料庫上的備份保留原則符合主資料庫的備份保留原則。 這些設定不是資料庫的一部分，也不會複寫。 根據預設，次要資料庫會設定為預設的 PITR 保留期限為7天。 如需詳細資訊，請參閱 [SQL Database 自動備份](automated-backups-overview.md)。

> [!IMPORTANT]
> 如果您的資料庫是容錯移轉群組的成員，您就無法使用異地複寫容錯移轉命令起始其容錯移轉。 使用群組的容錯移轉命令。 如果您需要容錯移轉個別的資料庫，您必須先將它從容錯移轉群組中移除。 如需詳細資料，請參閱  [容錯移轉群組](auto-failover-group-overview.md) 。

## <a name="configuring-secondary-database"></a>正在設定次要資料庫

主要和次要資料庫必須有相同的服務層級。 此外，強烈建議使用相同的備份儲存體冗余和計算大小 (Dtu 或虛擬核心) 作為主要資料庫來建立次要資料庫。 如果主資料庫發生大量寫入工作負載，則計算大小較低的次要資料庫可能無法跟上。 這會導致次要複本上的重做延遲，而且可能無法使用次要複本。 為了減輕這些風險，主動式異地複寫會在必要時節流處理主要的交易記錄檔速率，以允許其次要資料庫趕上。

不平衡次要設定的另一個結果是在容錯移轉之後，應用程式效能可能會因為新主資料庫的計算容量不足而受到影響。 在此情況下，必須將資料庫服務目標擴大至必要的層級，這可能會花費大量時間和計算資源，而且在擴大程式結束時需要 [高可用性](high-availability-sla.md) 容錯移轉。

如果您決定以較低的計算大小來建立次要資料庫，則 Azure 入口網站中的記錄 IO 百分比圖表會提供一個很好的方法，來估計維持複寫負載所需的次要資料庫最小計算大小。 例如，如果您的主資料庫是 P6 (1000 DTU) 而且其記錄寫入百分比為50%，則次要複本必須至少為 P4 (500 DTU) 。 若要取出歷程記錄 IO 資料，請使用 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) view。 若要以較高的資料細微性來取得最新的記錄寫入資料，更能反映記錄檔速率的短期尖峰，請使用 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) view。

由於次要複本上較低的計算大小，會使用 HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO 等候類型（可在 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 資料庫檢視中看見）來回報主要複本上的交易記錄檔速率節流。

根據預設，次要複本的備份儲存體複本與主資料庫的備份儲存體重複。 您可以選擇使用不同的備份儲存體冗余設定次要資料庫。 備份一律會在主資料庫上執行。 如果已使用不同的備份儲存體冗余設定次要資料庫，則在容錯移轉之後，當次要複本升級為主要複本時，備份將會根據在新的主要 (先前的次要) 上選取的儲存體冗余進行計費。 

> [!NOTE]
> 主要複本上的交易記錄速率可能會因為與次要複本上較低計算大小無關的原因而受到節流。 即使次要資料庫具有與主資料庫相同或更高的計算大小，也可能會發生這種節流。 如需詳細資料，包括不同類型記錄速率節流的等候類型，請參閱 [交易記錄速率管理](resource-limits-logical-server.md#transaction-log-rate-governance)。

> [!NOTE]
> Azure SQL Database 可設定的備份儲存體冗余現已在東南亞 Azure 區域中正式推出。 使用本機冗余或區域重複的備份儲存體冗余建立源資料庫時，不支援在不同的 Azure 區域中建立次要資料庫。 

如需 SQL Database 計算大小的詳細資訊，請參閱 [SQL Database 服務層是什麼](purchasing-models.md)。

## <a name="cross-subscription-geo-replication"></a>跨訂用帳戶異地複寫

若要在屬於不同訂用帳戶的兩個資料庫之間設定主動式異地複寫 (在相同租使用者下或未) ，您必須遵循本節所述的特殊程式。  此程式是以 SQL 命令為基礎，而且需要：

- 在兩部伺服器上建立特殊許可權登入
- 將 IP 位址新增至在這兩部伺服器上執行變更之用戶端的允許清單 (例如執行 SQL Server Management Studio) 的主機 IP 位址。

執行變更的用戶端需要對主伺服器的網路存取。 雖然必須將用戶端的相同 IP 位址新增至次要伺服器上的允許清單，但不一定需要與次要伺服器的網路連線。

### <a name="on-the-master-of-the-primary-server"></a>在主伺服器的主伺服器上

1. 將 IP 位址新增至執行變更之用戶端的允許清單 (如需詳細資訊，請參閱 [設定防火牆](firewall-configure.md)) 。
1. 建立專門用來設定主動式異地複寫 (的登入，並視需要調整認證) ：

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. 建立對應的使用者，並將其指派給 dbmanager 角色：

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. 使用下列查詢，記下新登入的 SID：

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>在主伺服器上的源資料庫上

1. 針對相同登入建立使用者：

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. 將使用者新增至 db_owner 角色：

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>在次要伺服器的主伺服器上

1. 將用戶端 IP 位址新增至次要伺服器的防火牆規則底下的允許清單中。 驗證已在主伺服器上新增的相同用戶端 IP 位址，也已新增至次要資料庫。 這是在執行 ALTER DATABASE ADD 次要命令以起始異地複寫之前，必須執行的必要步驟。

1. 使用相同的使用者名稱密碼和 SID，在主伺服器上建立相同的登入：

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. 建立對應的使用者，並將其指派給 dbmanager 角色：

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>在主伺服器的主伺服器上

1. 使用新登入登入主伺服器的主伺服器。
1. 在次要伺服器上建立源資料庫的次要複本， (視需要調整資料庫名稱和伺服器名稱) ：

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

初始設定之後，就可以移除建立的使用者、登入和防火牆規則。

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>保持認證和防火牆規則同步

我們建議針對異地複寫資料庫使用 [資料庫層級 IP 防火牆規則](firewall-configure.md) ，以便將這些規則與資料庫一起複寫，以確保所有次要資料庫都具有與主要複本相同的 IP 防火牆規則。 此方法不需要客戶手動設定及維護同時裝載主要和次要資料庫之伺服器上的防火牆規則。 同樣地，針對資料存取使用 [自主資料庫使用者](logins-create-manage.md) ，確保主要與次要資料庫永遠具有相同的使用者認證，在容錯移轉時不會因為登入和密碼不相符而有任何中斷。 使用額外的 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)，客戶可以管理主要和次要資料庫的使用者存取，並且排除在資料庫中管理認證的需求。

## <a name="upgrading-or-downgrading-primary-database"></a>升級或降級主資料庫

您可以將主要資料庫升級或降級至不同的計算大小 (在相同的服務層級內，而不是一般用途和業務關鍵之間)，而不需要將任何次要資料庫中斷連線。 升級時，我們建議您先升級次要資料庫，然後再升級主要資料庫。 降級時，順序相反︰先降級主要資料庫，然後再降級次要資料庫。 當您將資料庫升級或降級到不同的服務層級時，會強制執行這項建議。

> [!NOTE]
> 如果您已在容錯移轉群組設定中建立次要資料庫，則不建議降級次要資料庫。 這是為了確保您的資料層在容錯移轉啟動之後有足夠的容量來處理一般工作負載。

> [!IMPORTANT]
> 容錯移轉群組中的主要資料庫無法調整為較高的層級，除非次要資料庫第一次調整為較高的層級。 如果您嘗試在調整次要資料庫之前調整主資料庫，您可能會收到下列錯誤：
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>防止重要資料遺失

由於廣域網路的高度延遲，連續複製採用非同步複寫機制。 如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。 不過，有些應用程式可能會要求資料不能遺失。 若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系統程序。 呼叫 **sp_wait_for_database_copy_sync** 會封鎖呼叫執行緒，直到最後認可的交易傳輸到次要資料庫。 不過，它不會等候在次要資料庫上重新執行和認可傳輸的交易。 **sp_wait_for_database_copy_sync** 以特定的連續複製連結為範圍。 任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

> [!NOTE]
> **sp_wait_for_database_copy_sync** 可避免在容錯移轉之後資料遺失，但是不保證讀取權限會完整同步。 **sp_wait_for_database_copy_sync** 程序呼叫所造成的延遲可能會相當明顯，且取決於呼叫時的交易記錄大小。

## <a name="monitoring-geo-replication-lag"></a>監視異地複寫延遲

若要監視有關 RPO 的延遲，請使用主資料庫上[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) *replication_lag_sec*資料行。 它會顯示在主資料庫上認可的交易之間的延遲（以秒為單位），並保存在次要資料庫上。 例如 如果延隔時間的值為1秒，則表示主資料庫目前是否受到中斷的影響並起始容錯移轉，將不會儲存1秒的最新轉換。

若要針對已套用至次要資料庫的主資料庫變更（也就是可從次要資料庫讀取）來測量延遲，請將次要資料庫上的 *last_commit* 時間與主資料庫上的相同值進行比較。

> [!NOTE]
> 有時主資料庫上的 *replication_lag_sec* 有 Null 值，這表示主資料庫目前不知道次要資料庫的距離。   這通常會在進程重新開機後發生，而且應該是暫時性狀況。 如果 *replication_lag_sec* 傳回 Null 一段較長的時間，請考慮警示應用程式。 這會指出次要資料庫無法與主要複本進行通訊，因為發生永久性的連接失敗。 此外，也有可能造成次要資料庫上的 *last_commit* 時間和主資料庫之間的差異變成大型的狀況。 例如 如果在很長的一段時間未變更的情況下，于主資料庫上進行認可，則在快速返回0之前，其差異會向上移至較大的值。 如果這兩個值之間的差異維持很長一段時間，請將它視為錯誤狀況。

## <a name="programmatically-managing-active-geo-replication"></a>以程式設計方式管理主動式異地複寫

如前所述，作用中異地複寫可使用 Azure PowerShell 和 REST API，以程式設計的方式管理。 下表描述可用的命令集。 主動式異地複寫包含一組可管理的 Azure Resource Manager API，包括 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/)。 這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。 如需有關如何執行存取角色的詳細資訊，請參閱 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)。

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-sql：管理單一和集區資料庫的容錯移轉

> [!IMPORTANT]
> 這些 Transact-SQL 命令僅適用於作用中異地複寫，不適用於容錯移轉群組。 因此，它們也不會套用至 SQL 受控執行個體的實例，因為它們只支援容錯移轉群組。

| Command | 描述 |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |使用 ADD SECONDARY ON SERVER 引數，針對現有資料庫建立次要資料庫並開始資料複寫 |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |使用 FAILOVER 或 FORCE_FAILOVER_ALLOW_DATA_LOSS，將次要資料庫切換為主要資料庫以便開始容錯移轉 |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |使用 REMOVE SECONDARY ON SERVER，來終止 SQL Database 和指定次要資料庫間的資料複寫。 |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |傳回伺服器上每個資料庫的所有現有複寫連結的相關資訊。 |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |取得給定資料庫之複寫連結的上次複寫時間、最後一個複寫延遲和其他相關資訊。 |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |顯示所有資料庫作業的狀態，包括複寫連結的狀態。 |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |導致應用程式等候，直到作用中次要資料庫複寫並認可所有認可的交易為止。 |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell：管理單一和集區資料庫的容錯移轉

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az.Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

| Cmdlet | 描述 |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |取得一或多個資料庫。 |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |針對現有資料庫建立次要資料庫並開始資料複寫。 |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |將次要資料庫切換為主要資料庫以開始容錯移轉。 |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |終止 SQL Database 和指定次要資料庫間的資料複寫。 |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |取得 Azure SQL Database 和資源群組或邏輯 SQL 伺服器之間的異地複寫連結。 |
|  | |

> [!IMPORTANT]
> 如需範例指令碼，請參閱[使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/setup-geodr-and-failover-database-powershell.md)和[使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)。

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API：管理單一和集區資料庫的容錯移轉

| API | 描述 |
| --- | --- |
| [Create or Update Database (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |建立、更新或還原主要或次要資料庫。 |
| [取得建立或更新資料庫狀態](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |在建立作業期間傳回狀態。 |
| [將次要資料庫設定為主要資料庫 (計劃性容錯移轉)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |從目前主要資料庫進行容錯移轉，以設定主要的次要資料庫。 **SQL 受控執行個體不支援這個選項。**|
| [將次要資料庫設定為主要資料庫 (非計劃的容錯移轉)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |從目前主要資料庫進行容錯移轉，以設定主要的次要資料庫。 這項作業可能會導致資料遺失。 **SQL 受控執行個體不支援這個選項。**|
| [取得複寫連結](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |取得異地複寫合作關係中指定資料庫的特定複寫連結。 它會擷取 sys.geo_replication_links 目錄檢視中顯示的資訊。 **SQL 受控執行個體不支援這個選項。**|
| [複寫連結 - 依資料庫列示](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | 取得異地複寫合作關係中指定資料庫的所有複寫連結。 它會擷取 sys.geo_replication_links 目錄檢視中顯示的資訊。 |
| [刪除複寫連結](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | 刪除資料庫複寫連結。 無法在容錯移轉期間進行。 |
|  | |

## <a name="next-steps"></a>後續步驟

- 如需範例指令碼，請參閱：
  - [使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/setup-geodr-and-failover-database-powershell.md)
  - [使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- SQL Database 也支援自動容錯移轉群組。 如需詳細資訊，請參閱使用[自動容錯移轉群組](auto-failover-group-overview.md)。
- 如需商務持續性的總覽和案例，請參閱 [商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- 若要瞭解 Azure SQL Database 自動備份的相關資訊，請參閱 [SQL Database 自動備份](automated-backups-overview.md)。
- 若要瞭解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](recovery-using-backups.md)。
- 若要深入了解新的主要伺服器和資料庫的驗證需求，請參閱 [災害復原後的 SQL Database 安全性](active-geo-replication-security-configure.md)。
