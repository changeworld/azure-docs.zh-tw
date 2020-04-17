---
title: 資料同步
description: 本概觀介紹 Azure SQL 資料同步
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 8708c458e1064e4b9ea7dc67f1a4d4fbce1547b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481957"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>使用 SQL 資料同步，跨多個雲端和內部部署資料庫同步資料

「SQL 資料同步」是一種建置在 Azure SQL Database 上的服務，可讓您跨多個 SQL 資料庫和 SQL Server 執行個體，雙向同步您選取的資料。

> [!IMPORTANT]
> Azure SQL 資料同步目前不支援 Azure SQL Database 受控執行個體。

## <a name="when-to-use-data-sync"></a>使用資料同步的時機

數據同步在需要跨多個 Azure SQL 資料庫或 SQL Server 資料庫更新數據的情況下非常有用。 以下是資料同步主要的使用案例：

- **混合資料同步:** 通過資料同步,可以在本地資料庫和 Azure SQL 資料庫之間保持數據同步,以啟用混合應用程式。 此功能對於考慮移轉至雲端，而且想要將部分應用程式放在 Azure 的客戶很有吸引力。
- **分散式應用程式：** 在許多情況下，將不同的工作負載分散到不同的資料庫會有好處。 例如，如果您有大型的實際執行資料庫，但也必須針對這些資料執行報告或分析工作負載，此時有第二個資料庫分擔這額外的工作負載就很有幫助。 這個方法可以減少對您實際執行工作負載的效能影響。 您可以使用「資料同步」，讓這兩個資料庫保持同步。
- **全球分散式應用程式:** 許多企業跨越多個地區,甚至幾個國家/地區。 若要盡可能降低網路延遲，最好讓資料靠近您所在的區域。 使用資料同步，您就可以輕鬆地讓全世界各個區域中的資料庫保持同步。

資料同步不是以下方案的首選解決方案:

| 狀況 | 某些建議的解決方案 |
|----------|----------------------------|
| 災害復原 | [Azure 異地備援備份](sql-database-automated-backups.md) |
| 讀取級別 | [使用唯讀複本對唯讀查詢工作負載進行負載平衡 (預覽)](sql-database-read-scale-out.md) |
| ETL (OLTP 到 OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 或 [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| 從內部部署 SQL Server 移轉至 Azure SQL Database | [Azure 資料庫移轉服務](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>SQL Data Sync 概觀

資料同步以「同步群組」的概念為基礎。 「同步群組」是您想要同步的資料庫群組。

資料同步使用中樞和輪輻拓撲來同步資料。 您可以將同步群組中的其中一個資料庫定義為「中樞資料庫」。 其餘的資料庫則是成員資料庫。 只有中樞和個別成員之間才會進行同步。

- **中樞資料庫**必須是 Azure SQL Database。
- **成員資料庫**可以是 SQL 資料庫、內部部署 SQL Server 資料庫，或是在 Azure 虛擬機器上的 SQL Server 執行個體。
- **同步資料庫**包含資料同步的中繼資料和紀錄。同步資料庫必須是位於與中心資料庫相同的區域中的 Azure SQL 資料庫。 「同步處理資料庫」是由客戶建立，並由客戶擁有。

> [!NOTE]
> 如果您使用內部部署資料庫當做成員資料庫，則必須[安裝和設定本機同步代理程式](sql-database-get-started-sql-data-sync.md#add-on-prem)。

![資料庫之間的同步資料](media/sql-database-sync-data/sync-data-overview.png)

同步群組具有下列屬性：

- **同步結構描述**說明要同步的資料。
- **同步處理方向**可以是雙向或只有單向。 也就是說，同步處理方向可以是「中樞到成員」** 或是「成員到中樞」**，或兩者皆可。
- **同步處理間隔**說明了進行同步處理的頻率。
- **衝突解決原則**是群組層級原則，可以是*中樞獲勝*或*成員獲勝*。

## <a name="how-does-data-sync-work"></a>資料同步運作方式

- **追蹤資料變更：** 資料同步使用 insert、update 和 delete 觸發程序追蹤變更。 變更會記錄在使用者資料庫中的資料表。 請注意,默認情況下,BULK INSERT 不會觸發觸發器。 如果未指定FIRE_TRIGGERS,則不執行插入觸發器。 新增 FIRE_TRIGGERS 選項，資料同步就能追蹤那些插入。 
- **同步處理資料：** 資料同步是以「中樞和輪輻」的模型設計。 中樞會與每個成員個別同步。 中樞的變更會下載到成員，然後成員的變更會上傳到中樞。
- **解決衝突：** 資料同步提供兩個衝突解決選項：*中樞獲勝*或*成員獲勝*。
  - 如果您選取 [中樞獲勝]**，中樞的變更永遠會覆寫成員的變更。
  - 如果您選取 [成員獲勝]**，成員的變更永遠會覆寫中樞的變更。 如果有多個成員，最終的值則取決於哪一個成員先同步。

## <a name="compare-data-sync-with-transactional-replication"></a>比較資料同步與異動複寫

| | 資料同步 | 異動複寫 |
|---|---|---|
| 優點 | - 主動-主動支援<br/>- 在內部部署與 Azure SQL Database 之間雙向進行 | - 更低的延遲性<br/>- 交易一致性<br/>- 移轉後重複使用現有的拓撲 |
| 缺點 | - 5 分鐘或更多的延遲<br/>- 無交易一致性<br/>- 更高的效能影響 | - 無法從 Azure SQL 資料庫單個資料庫或池資料庫發佈<br/>- 高維護成本 |

## <a name="get-started-with-sql-data-sync"></a>開始使用 SQL 資料同步

### <a name="set-up-data-sync-in-the-azure-portal"></a>在 Azure 入口網站中設定資料同步

- [設定 Azure SQL 資料同步](sql-database-get-started-sql-data-sync.md)
- Data Sync Agent - [適用於 Azure SQL Data Sync 的 Data Sync Agent](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>使用 PowerShell 設定資料同步

- [使用 PowerShell 在多個 Azure SQL 資料庫之間同步](scripts/sql-database-sync-data-between-sql-databases.md)
- [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>檢閱資料同步最佳做法

- [Azure SQL 資料同步最佳做法](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>發生錯誤了嗎？

- [對 Azure SQL 資料同步的問題進行疑難排解](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>一致性與效能

### <a name="eventual-consistency"></a>最終一致性

由於數據同步是基於觸發器的,因此不能保證事務一致性。 Microsoft 保證最終進行所有更改,並且數據同步不會導致資料丟失。

### <a name="performance-impact"></a>效能影響

資料同步使用 insert、update 和 delete 觸發程序追蹤變更。 其會在使用者資料庫中建立側邊資料表，以便進行變更追蹤。 這些變更追蹤活動會影響您的資料庫工作負載。 請評估您的服務層級，如有必要則請升級。

在建立同步群組期間佈建和取消佈建、更新和刪除也可能會影響資料庫效能。

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> 需求和限制

### <a name="general-requirements"></a>一般需求

- 每個資料表都必須有主索引鍵。 請勿變更任何資料列的主索引鍵值。 如果您必須變更主索引鍵值，請刪除資料列，再利用新的主索引鍵值重新建立。

> [!IMPORTANT]
> 變更現有主鍵的值將導致以下錯誤行為:
> - 即使同步不報告任何問題,中心與成員之間的數據也可能丟失。
> - 由於主鍵更改,同步可能會失敗,因為跟蹤表具有來自源的不存在行。

- 必須啟用快照集隔離。 如需詳細資訊，請參閱 [SQL Server 中的快照集隔離](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)。

### <a name="general-limitations"></a>一般限制

- 表不能具有不是主鍵的標識列。
- 主鍵不能具有以下數據類型:sql_variant、二進位、varbinary、圖像、xml。
- 當您使用下列資料類型作為主要索引鍵時請務必謹慎，原因是支援的有效位數只到秒：time、datetime、datetime2、datetimeoffset。
- 對象(資料庫、表格和列)的名稱不能包含可列印的字元句點 (.)、左方括弧 (*) 或右側方括弧 (*)。
- 不支援 Azure 活動目錄身份驗證。
- 不支援具有相同名稱但架構不同的表(例如 dbo.客戶和銷售.客戶)。
- 不支援使用者定義資料型態的欄
- 不支援在不同訂閱之間移動伺服器。 

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

## <a name="faq-about-sql-data-sync"></a>SQL 資料同步常見問題集

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>SQL 資料同步服務的成本為何？

SQL 資料同步服務本身不收取任何費用。 但是,您仍會收集進出 SQL 資料庫實例的數據傳輸費用。 如需詳細資訊，請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)。

### <a name="what-regions-support-data-sync"></a>哪些區域支援資料同步？

SQL 資料同步會在以下所有區域內上市。

### <a name="is-a-sql-database-account-required"></a>是否需要 SQL Database 帳戶？

是。 您必須具有可裝載中樞資料庫的 SQL Database 帳戶。

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>資料同步能否僅在 SQL Server 內部部署資料庫之間同步？

無法直接進行。 您可以間接在 SQL Server 內部部署資料庫之間同步，不過，必須先在 Azure 建立中樞資料庫，然後將內部部署資料庫新增到同步群組。

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>我可以使用資料同步在屬於不同訂用帳戶的 SQL Database 之間進行同步嗎？

是。 您可以在不同訂用帳戶擁有的資源群組所屬的 SQL Database 之間進行同步。

- 如果訂用帳戶屬於同一個租用戶，且您擁有所有訂用帳戶的權限，則可以在 Azure 入口網站中設定同步群組。
- 否則，您必須使用 PowerShell 來新增屬於不同訂用帳戶的同步成員。

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>是否可以使用資料同步在屬於不同雲端的 SQL 資料庫(如 Azure 公共雲和 Azure China 21Vianet)之間同步

是。 您可以在屬於不同雲端的 SQL Database 之間進行同步處理，您必須使用 PowerShell 來新增屬於不同訂用帳戶的同步成員。

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>能否使用資料同步將生產環境資料庫的資料植入空白資料庫，然後同步處理資料？

是。 請從原始結構描述編寫結構描述，藉此在新的資料庫中手動建立結構描述。 建立結構描述之後，請將資料表新增到同步群組，以複製資料並讓資料保持同步。

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>應該使用 SQL 資料同步來備份及還原資料庫嗎？

不建議使用 SQL 資料同步創建數據的備份。 無法備份並還原到特定時間點,因為 SQL 資料同步同步未進行版本控制。 此外,SQL 數據同步不會備份其他 SQL 物件(如儲存過程),也不會快速執行等效的還原操作。

如需建議的備份技術，請參閱[複製 Azure SQL 資料庫](sql-database-copy.md)。

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>資料同步是否可以同步加密的資料表和資料行？

- 如果資料庫是使用 Always Encrypted，您就只能同步「未」** 加密的資料表和資料行。 您無法同步加密的資料行，因為資料同步無法解密資料。
- 如果資料行是使用資料行層級加密 (CLE)，則只要資料列大小小於 24 Mb 的大小上限，您就可以同步資料行。 資料同步會將金鑰加密的資料行 (CLE) 視為一般的二進位資料。 若要解密其他同步成員中的資料，您必須具有相同的憑證。

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL 資料同步是否支援定序？

是。 在下列案例中 SQL 資料同步可支援定序：

- 如果選擇的同步架構表尚未位於中心或成員資料庫中,則當您部署同步組時,服務將自動建立具有在空目標資料庫中選擇的排序設置的相應表和列。
- 如果要同步處理的資料表已存在於您的中樞和成員資料庫中，則 SQL 資料同步會要求主索引鍵資料行在中樞與成員資料庫之間有相同的定序，才能成功部署同步群組。 主索引鍵資料行以外的資料行沒有任何定序限制。

### <a name="is-federation-supported-in-sql-data-sync"></a>SQL 資料同步是否支援同盟？

同盟根資料庫可使用於 SQL 資料同步服務 (無任何限制)。 無法將聯合資料庫終結點添加到當前版本的 SQL 資料同步。

## <a name="next-steps"></a>後續步驟

### <a name="update-the-schema-of-a-synced-database"></a>更新已同步資料庫的結構描述

是否必須更新同步群組中的資料庫結構描述？ 架構更改不會自動複製。 如需某些解決方案，請參閱下列文章：

- [在 Azure SQL 資料同步中自動執行結構描述變更複寫](sql-database-update-sync-schema.md)
- [使用 PowerShell 更新現有同步群組中的同步結構描述](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>監視及疑難排解

SQL 資料同步是否按預期進行? 若要監視活動並針對問題進行疑難排解，請參閱下列文章：

- [監視 Azure SQL 資料與 Azure 監視器紀錄同步](sql-database-sync-monitor-oms.md)
- [對 Azure SQL 資料同步的問題進行疑難排解](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>深入了解 Azure SQL Database

如需 SQL Database 的詳細資訊，請參閱下列文章：

- [SQL 資料庫概述](sql-database-technical-overview.md)
- [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
