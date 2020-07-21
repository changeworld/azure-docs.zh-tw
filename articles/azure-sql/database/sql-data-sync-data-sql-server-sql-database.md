---
title: 什麼是適用于 Azure 的 SQL 資料同步？
description: 本總覽介紹 Azure 的 SQL 資料同步，可讓您跨多個雲端和內部部署資料庫同步資料。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 02207f8b5cf8628c3b2bbb72ab8d38ca9a4a5731
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504215"
---
# <a name="what-is-sql-data-sync-for-azure"></a>什麼是適用于 Azure 的 SQL 資料同步？

SQL 資料同步是以 Azure SQL Database 為基礎的服務，可讓您跨內部部署和雲端中的多個資料庫，雙向同步處理您選取的資料。 

> [!IMPORTANT]
> Azure SQL 資料同步目前不支援 Azure SQL 受控執行個體。


## <a name="overview"></a>概觀 

資料同步是以同步處理群組的概念為基礎。 同步群組是一組您想要同步處理的資料庫。

資料同步使用中樞和輪輻拓撲來同步資料。 您會將同步處理群組中的其中一個資料庫定義為中樞資料庫。 其餘的資料庫則是成員資料庫。 同步只會在中樞和個別成員之間進行。

- **中樞資料庫**必須是 Azure SQL Database。
- **成員資料庫**可以是 Azure SQL Database 或 SQL Server 實例中的資料庫。
- **同步中繼資料資料庫**包含資料同步的中繼資料和記錄。同步處理中繼資料資料庫必須是與中樞資料庫位於相同區域中的 Azure SQL Database。 同步中繼資料資料庫是由客戶所建立，而且是由客戶所擁有。 每個區域和訂用帳戶只能有一個同步中繼資料資料庫。 同步處理群組或同步處理代理程式存在時，無法刪除或重新命名同步中繼資料資料庫。 Microsoft 建議您建立新的空白資料庫作為 [同步中繼資料資料庫]。 資料同步會在此資料庫中建立資料表，並頻繁執行工作負載。

> [!NOTE]
> 如果您使用內部部署資料庫當做成員資料庫，則必須[安裝和設定本機同步代理程式](sql-data-sync-sql-server-configure.md#add-on-prem)。

![資料庫之間的同步資料](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

同步群組具有下列屬性：

- **同步結構描述**說明要同步的資料。
- **同步處理方向**可以是雙向或只有單向。 也就是說，同步處理方向可以是「中樞到成員」** 或是「成員到中樞」**，或兩者皆可。
- **同步處理間隔**說明了進行同步處理的頻率。
- **衝突解決原則**是群組層級原則，可以是*中樞獲勝*或*成員獲勝*。

## <a name="when-to-use"></a>使用時機

當資料需要在 Azure SQL Database 或 SQL Server 中的數個資料庫之間保持更新時，資料同步非常有用。 以下是資料同步主要的使用案例：

- **混合式資料同步處理：** 使用資料同步，您可以在 SQL Server 和 Azure SQL Database 中的資料庫之間保持資料同步，以啟用混合式應用程式。 此功能對於考慮移轉至雲端，而且想要將部分應用程式放在 Azure 的客戶很有吸引力。
- **分散式應用程式：** 在許多情況下，將不同的工作負載分散到不同的資料庫會有好處。 例如，如果您有大型的實際執行資料庫，但也必須針對這些資料執行報告或分析工作負載，此時有第二個資料庫分擔這額外的工作負載就很有幫助。 這個方法可以減少對您實際執行工作負載的效能影響。 您可以使用「資料同步」，讓這兩個資料庫保持同步。
- **全域散發的應用程式：** 許多企業都跨越數個區域，甚至數個國家/地區。 若要盡可能降低網路延遲，最好讓資料靠近您所在的區域。 使用資料同步，您就可以輕鬆地讓全世界各個區域中的資料庫保持同步。

在下列案例中，資料同步不是慣用的解決方案：

| 情節 | 某些建議的解決方案 |
|----------|----------------------------|
| 災害復原 | [Azure 異地備援備份](automated-backups-overview.md) |
| 讀取級別 | [使用唯讀複本對唯讀查詢工作負載進行負載平衡 (預覽)](read-scale-out.md) |
| ETL (OLTP 到 OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 或 [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| 從 SQL Server 遷移至 Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||



## <a name="how-it-works"></a>運作方式

- **追蹤資料變更：** 資料同步使用 insert、update 和 delete 觸發程序追蹤變更。 變更會記錄在使用者資料庫中的資料表。 請注意，BULK INSERT 預設不會引發觸發程式。 如果未指定 FIRE_TRIGGERS，就不會執行 insert 觸發程式。 新增 FIRE_TRIGGERS 選項，資料同步就能追蹤那些插入。 
- **同步處理資料：** 資料同步是在中樞和輪輻模型中所設計。 中樞會個別同步處理每個成員。 從中樞進行的變更會下載至成員，然後將成員的變更上傳至中樞。
- **解決衝突：** 資料同步提供兩個衝突解決選項：*中樞獲勝*或*成員獲勝*。
  - 如果您選取 [中樞獲勝]**，中樞的變更永遠會覆寫成員的變更。
  - 如果您選取 [成員獲勝]**，成員的變更永遠會覆寫中樞的變更。 如果有多個成員，最終的值則取決於哪一個成員先同步。

## <a name="compare-with-transactional-replication"></a>與異動複寫比較

| 資料同步 | 異動複寫 |
|---|---|---|
| 優點 | - 主動-主動支援<br/>- 在內部部署與 Azure SQL Database 之間雙向進行 | - 更低的延遲性<br/>- 交易一致性<br/>- 移轉後重複使用現有的拓撲 <br/>-Azure SQL 受控執行個體支援 |
| 缺點 | - 5 分鐘或更多的延遲<br/>- 無交易一致性<br/>- 更高的效能影響 | -無法從 Azure SQL Database 發佈 <br/>- 高維護成本 |

## <a name="get-started"></a>開始使用 

### <a name="set-up-data-sync-in-the-azure-portal"></a>在 Azure 入口網站中設定資料同步

- [設定 Azure SQL 資料同步](sql-data-sync-sql-server-configure.md)
- Data Sync Agent - [適用於 Azure SQL Data Sync 的 Data Sync Agent](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>使用 PowerShell 設定資料同步

- [使用 PowerShell 在 Azure SQL Database 中的多個資料庫之間進行同步處理](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [使用 PowerShell 在 Azure SQL Database 中的資料庫和 SQL Server 實例中的資料庫之間進行同步處理](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>檢閱資料同步最佳做法

- [Azure SQL 資料同步最佳做法](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>發生錯誤了嗎？

- [對 Azure SQL 資料同步的問題進行疑難排解](../../sql-database/sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>一致性與效能

### <a name="eventual-consistency"></a>「最終」一致性

由於資料同步是以觸發程式為基礎，因此不保證交易一致性。 Microsoft 保證最終會進行所有變更，而且資料同步不會造成資料遺失。

### <a name="performance-impact"></a>效能影響

資料同步使用 insert、update 和 delete 觸發程序追蹤變更。 其會在使用者資料庫中建立側邊資料表，以便進行變更追蹤。 這些變更追蹤活動會影響您的資料庫工作負載。 請評估您的服務層級，如有必要則請升級。

在建立同步群組期間佈建和取消佈建、更新和刪除也可能會影響資料庫效能。

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> 需求和限制

### <a name="general-requirements"></a>一般需求

- 每個資料表都必須有主索引鍵。 請勿變更任何資料列的主索引鍵值。 如果您必須變更主索引鍵值，請刪除資料列，再利用新的主索引鍵值重新建立。

> [!IMPORTANT]
> 變更現有主鍵的值將會導致下列錯誤行為：
> - 即使同步處理並未回報任何問題，中樞和成員之間的資料也可能會遺失。
> - 同步處理可能會失敗，因為追蹤資料表具有來源的非現有資料列，因為主要金鑰變更。

- 必須啟用快照集隔離。 如需詳細資訊，請參閱 [SQL Server 中的快照集隔離](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)。

### <a name="general-limitations"></a>一般限制

- 資料表不能有不是主要索引鍵的識別欄位。
- 主鍵不能有下列資料類型： SQL_variant、binary、Varbinary、image、xml。
- 當您使用下列資料類型作為主要索引鍵時請務必謹慎，原因是支援的有效位數只到秒：time、datetime、datetime2、datetimeoffset。
- 物件（資料庫、資料表和資料行）的名稱不能包含可列印的字元句點（.）、左方括弧（[）或右方括弧（]）。
- 不支援 Azure Active Directory 驗證。
- 不支援具有相同名稱但不同架構（例如，dbo. customers 和 sales）的資料表。
- 不支援具有使用者定義資料類型的資料行
- 不支援在不同的訂用帳戶之間移動伺服器。 

#### <a name="unsupported-data-types"></a>不支援的資料類型

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (支援 XML)
- Cursor、RowVersion、Timestamp、Hierarchyid

#### <a name="unsupported-column-types"></a>不支援的資料行類型

資料同步無法同步處理唯讀或系統產生的資料行。 例如：

- 計算資料行。
- 適用於時態表的系統所產生的資料行。

#### <a name="limitations-on-service-and-database-dimensions"></a>服務和資料庫維度的限制

| **維度**                                                  | **限制**              | **因應措施**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| 任何資料庫可以隸屬的同步群組數目上限。       | 5                      |                             |
| 單一同步群組中的端點數目上限              | 30                     |                             |
| 單一同步群組中的內部部署端點數目上限。 | 5                      | 建立多個同步群組 |
| 資料庫名稱、資料表名稱、結構描述名稱和資料行名稱                       | 每個名稱 50 個字元 |                             |
| 一個同步群組中的資料表                                          | 500                    | 建立多個同步群組 |
| 一個同步群組中一個資料表中的資料行                              | 1000                   |                             |
| 一個資料表上的資料列大小                                        | 24 Mb                  |                             |
| 最小同步處理間隔                                           | 5 分鐘              |                             |

> [!NOTE]
> 如果只有一個同步群組，則在單一同步群組中最多可有 30 個端點。 如果有多個同步群組，則所有同步群組之間的端點總數不能超過 30 個。 如果資料庫屬於多個同步群組，系統會將它計算為多個端點，而不是一個。

### <a name="network-requirements"></a>網路需求

建立同步處理群組時，資料同步服務必須連接到中樞資料庫。 當您建立同步群組時，Azure SQL 伺服器在其設定中必須具有下列 `Firewalls and virtual networks` 設定：

 * [*拒絕公用網路存取*] 必須設定為 [*關閉*]。
 * [*允許 Azure 服務和資源存取此伺服器*] 必須設定為 *[是]*，或者您必須為[資料同步服務所使用的 ip 位址](network-access-controls-overview.md#data-sync)建立 ip 規則。

一旦建立並布建同步群組之後，您就可以停用這些設定。 同步代理程式會直接連線到中樞資料庫，您可以使用伺服器的[防火牆 IP 規則](firewall-configure.md)或[私人端點](private-endpoint-overview.md)，以允許代理程式存取中樞伺服器。

> [!NOTE]
> 如果您變更同步處理群組的架構設定，就必須允許資料同步服務再次存取伺服器，才能重新布建中樞資料庫。

## <a name="faq-about-sql-data-sync"></a>SQL 資料同步常見問題集

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>SQL 資料同步服務的成本為何？

SQL 資料同步服務本身不收費。 不過，您仍然可以收集資料移入和移出 SQL Database 實例的資料傳輸費用。 如需詳細資訊，請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)。

### <a name="what-regions-support-data-sync"></a>哪些區域支援資料同步？

SQL 資料同步會在以下所有區域內上市。

### <a name="is-a-sql-database-account-required"></a>是否需要 SQL Database 帳戶？

是。 您必須擁有 SQL Database 帳戶，才能裝載中樞資料庫。

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>我可以使用資料同步只在 SQL Server 資料庫之間進行同步處理

無法直接進行。 不過，您可以透過在 Azure 中建立中樞資料庫，然後將內部部署資料庫新增至同步處理群組，以間接方式在 SQL Server 資料庫之間進行同步處理。

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>我可以使用資料同步，在屬於不同訂閱的 SQL Database 中的資料庫之間進行同步處理

是。 您可以在屬於不同訂用帳戶所擁有之資源群組的資料庫之間進行同步處理。

- 如果訂用帳戶屬於同一個租用戶，且您擁有所有訂用帳戶的權限，則可以在 Azure 入口網站中設定同步群組。
- 否則，您必須使用 PowerShell 來新增屬於不同訂用帳戶的同步成員。

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>我可以使用資料同步，在屬於不同雲端的 SQL Database 資料庫之間進行同步處理（例如 Azure 公用雲端和 Azure 中國世紀）

是。 您可以在屬於不同雲端的資料庫之間進行同步處理。 您必須使用 PowerShell 來新增屬於不同訂用帳戶的同步成員。

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>能否使用資料同步將生產環境資料庫的資料植入空白資料庫，然後同步處理資料？

是。 請從原始結構描述編寫結構描述，藉此在新的資料庫中手動建立結構描述。 建立結構描述之後，請將資料表新增到同步群組，以複製資料並讓資料保持同步。

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>應該使用 SQL 資料同步來備份及還原資料庫嗎？

不建議使用 SQL 資料同步來建立資料的備份。 您無法備份並還原到特定的時間點，因為 SQL 資料同步同步處理未建立版本。 此外，SQL 資料同步不會備份其他 SQL 物件（例如預存程式），也不會快速執行相當於還原作業的作業。

如需一項建議的備份技術，請參閱[複製 Azure SQL Database 中的資料庫](database-copy.md)。

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>資料同步是否可以同步加密的資料表和資料行？

- 如果資料庫是使用 Always Encrypted，您就只能同步「未」** 加密的資料表和資料行。 您無法同步加密的資料行，因為資料同步無法解密資料。
- 如果資料行是使用資料行層級加密 (CLE)，則只要資料列大小小於 24 Mb 的大小上限，您就可以同步資料行。 資料同步會將金鑰加密的資料行 (CLE) 視為一般的二進位資料。 若要解密其他同步成員中的資料，您必須具有相同的憑證。

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL 資料同步是否支援定序？

是。 在下列案例中 SQL 資料同步可支援定序：

- 如果選取的同步架構資料表尚未存在於您的中樞或成員資料庫中，則當您部署同步處理群組時，服務會自動使用空的目的地資料庫中選取的定序設定來建立對應的資料表和資料行。
- 如果要同步處理的資料表已存在於您的中樞和成員資料庫中，則 SQL 資料同步會要求主索引鍵資料行在中樞與成員資料庫之間有相同的定序，才能成功部署同步群組。 主索引鍵資料行以外的資料行沒有任何定序限制。

### <a name="is-federation-supported-in-sql-data-sync"></a>SQL 資料同步是否支援同盟？

同盟根資料庫可使用於 SQL 資料同步服務 (無任何限制)。 您無法將同盟資料庫端點新增至目前的 SQL 資料同步版本。

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>我可以使用資料同步來同步處理從 Dynamics 365 匯出的資料，使用自備資料庫（BYOD）功能嗎？

Dynamics 365 自備資料庫功能可讓系統管理員將應用程式中的資料實體匯出至自己的 Microsoft Azure SQL database。 如果資料是使用累加**式推送**匯出（不支援完整推送），而且 [**目標資料庫中的啟用觸發**程式] 設定為 **[是]**，則可以使用資料同步將此資料同步至其他資料庫。

## <a name="next-steps"></a>後續步驟

### <a name="update-the-schema-of-a-synced-database"></a>更新已同步資料庫的結構描述

是否必須更新同步群組中的資料庫結構描述？ 不會自動複寫架構變更。 如需某些解決方案，請參閱下列文章：

- [使用 Azure 中的 SQL 資料同步自動複寫架構變更](../../sql-database/sql-database-update-sync-schema.md)
- [使用 PowerShell 更新現有同步群組中的同步結構描述](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>監視及疑難排解

SQL 資料同步如預期般執行嗎？ 若要監視活動並針對問題進行疑難排解，請參閱下列文章：

- [使用 Azure 監視器記錄監視 SQL 資料同步](../../sql-database/sql-database-sync-monitor-oms.md)
- [對 Azure SQL 資料同步的問題進行疑難排解](../../sql-database/sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>深入了解 Azure SQL Database

如需 Azure SQL Database 的詳細資訊，請參閱下列文章：

- [SQL Database 概觀](sql-database-paas-overview.md)
- [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
 
