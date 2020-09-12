---
title: 什麼是 Azure SQL Database 服務？
description: 取得 SQL Database 的簡介： Microsoft 關係資料庫管理系統的技術詳細資料與功能 (雲端中的 RDBMS) 。
keywords: sql 簡介,sql 簡介,什麼是 sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 9ee4070562e44d4f560230fa2fd069eb1fd57932
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612080"
---
# <a name="what-is-azure-sql-database"></a>什麼是 Azure SQL Database？
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 是完全受控的平臺即服務 (PaaS) database engine，可處理大部分的資料庫管理功能，例如升級、修補、備份和監視，而不需要使用者介入。 Azure SQL Database 一律在最新穩定版本的 SQL Server Database engine 上執行，並以99.99% 的可用性修補作業系統。 內建于 Azure SQL Database 的 PaaS 功能，可讓您將焦點放在對您企業至關重要的特定領域資料庫管理和優化活動上。

使用 Azure SQL Database，您可以為 Azure 中的應用程式和解決方案建立高可用性且高效能的資料儲存層。 SQL Database 可以是適用于各種新式雲端應用程式的正確選擇，因為它可讓您處理關聯式資料和 [非關聯式結構](../multi-model-features.md)，例如圖形、JSON、空間和 XML。

Azure SQL Database 是以 [Microsoft SQL Server Database engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)的最新穩定版本為基礎。 您可以使用先進的查詢處理功能，例如 [高效能的記憶體內部技術](../in-memory-oltp-overview.md) 和 [智慧型查詢處理](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)。 事實上，SQL Server 的最新功能是先釋出 SQL Database，然後再 SQL Server 本身。 您可以取得最新的 SQL Server 功能，而不需要額外的修補或升級，而是跨數百萬個資料庫進行測試。 

SQL Database 可讓您在兩個不同的購買模型內輕鬆定義和調整效能： [vCore 為基礎的購買模型](service-tiers-vcore.md) 和以 [DTU 為基礎的購買模型](service-tiers-dtu.md)。 SQL Database 是完全受控的服務，具有內建的高可用性、備份和其他常見的維護作業。 Microsoft 會處理 SQL 與作業系統程式碼的所有修補和更新。 您不需要管理基礎結構。

如果您不熟悉 Azure SQL Database，請參閱深入的[AZURE SQL 影片系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中的*Azure SQL Database 總覽*影片：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

## <a name="deployment-models"></a>部署模型

Azure SQL Database 為資料庫提供下列部署選項：

- [單一資料庫](single-database-overview.md) 代表完全受控的獨立資料庫。 如果您有需要單一可靠資料來源的新式雲端應用程式和微服務，您可以使用此選項。 單一資料庫類似于[SQL Server database engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)中的自主[資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json)。
- [彈性集](elastic-pool-overview.md) 區是 [單一資料庫](single-database-overview.md) 的集合，其中包含一組共用的資源，例如 CPU 或記憶體。 單一資料庫可以移入和移出彈性集區。

> [!IMPORTANT]
> 若要瞭解 SQL Database 和 SQL Server 之間的功能差異，以及不同 Azure SQL Database 選項之間的差異，請參閱 [SQL Database 功能](features-comparison.md)。

SQL Database 可提供多個資源類型、服務層級和計算大小的可預測效能。 它提供動態的擴充性，無停機時間、內建智慧型優化、全球擴充性和可用性，以及先進的安全性選項。 這些功能可讓您專注于快速開發應用程式，並加快上市時間，而不是管理虛擬機器和基礎結構。 SQL Database 目前位於全球38的資料中心內，所以您可以在您附近的資料中心執行您的資料庫。

## <a name="scalable-performance-and-pools"></a>可擴充的效能和集區

您可以定義指派的資源量。 
- 使用單一資料庫時，每個資料庫會與其他資料庫隔離，而且是可移植的。 每個都有自己保證的計算、記憶體和儲存體資源數量。 指派給資料庫的資源數量專屬於該資料庫，不會與 Azure 中的其他資料庫共用。 您可以動態 [調整單一資料庫資源的大小](single-database-scale.md) 。 單一資料庫選項提供不同的計算、記憶體和儲存體資源，以滿足不同的需求。 例如，您可以從1到80虛擬核心或 32 GB 到 4 TB。 適用于單一資料庫的 [超大規模服務層級](service-tier-hyperscale.md) ，可讓您透過快速的備份和還原功能，調整為 100 TB。
- 使用彈性集區時，您可以指派集區中所有資料庫所共用的資源。 您可以建立新的資料庫，或將現有的單一資料庫移至資源集區，以充分利用資源並節省成本。 此選項也可讓您動態 [調整彈性集區資源](elastic-pool-scale.md) 。

您可以在一般用途服務層級中，每個月以低成本的方式在小型單一資料庫上建立您的第一個應用程式。 然後，您可以隨時以手動或程式設計方式將其服務層級變更為商務關鍵服務層級，以符合您的解決方案需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求。 您只需支付所需的資源。

*動態擴充性* 不同于 *自動*調整。 自動調整規模是指服務根據準則來自動調整規模，而動態延展性則允許在不停機的狀況下手動調整規模。 單一資料庫選項支援手動動態調整，但不支援自動調整規模。 如需「自動」體驗，請考慮使用彈性集區，以根據個別資料庫的需求讓資料庫共用集區中的資源。 另一個選項是使用可協助自動化單一資料庫的擴充性的腳本。 如需範例，請參閱[使用 PowerShell 來監視和調整單一資料庫的規模](scripts/monitor-and-scale-database-powershell.md)。

### <a name="purchasing-models"></a>購買模型

SQL Database 提供下列購買模型：
- [VCore 為基礎的購買模型](service-tiers-vcore.md)可讓您選擇虛擬核心的數目、記憶體數量，以及儲存體的數量和速度。 以虛擬核心為基礎的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。 如需有關 Azure Hybrid Benefit 的詳細資訊，請參閱本文稍後的「常見問題」一節。
- 以 [DTU 為基礎的購買模型](service-tiers-dtu.md) 提供三個服務層級的計算、記憶體和 i/o 資源混合，以支援燈光至繁重的資料庫工作負載。 各層內的計算大小分別提供這些資源的不同混用方式，而且您可以為其新增額外的儲存體資源。
- [無伺服器模型](serverless-tier-overview.md)會根據工作負載需求自動調整計算，並依據每秒使用的計算量來計費。 無伺服器計算層也會在只有儲存體計費時，于非使用期間自動暫停資料庫，並在活動傳回時自動繼續資料庫。

### <a name="service-tiers"></a>服務層

Azure SQL Database 提供三個針對不同類型的應用程式所設計的服務層級：
- 專為一般工作負載而設計的[一般用途/標準](service-tier-general-purpose.md)服務層級。 它提供以預算為導向的平衡計算和儲存體選項。
- 針對具有高交易率和最低延遲 i/o 的 OLTP 應用程式所設計的[商務關鍵性/](service-tier-business-critical.md)高階服務層級。 它使用數個隔離的複本，為失敗提供最高的復原能力。
- 針對非常大型 OLTP 資料庫所設計的[超大規模](service-tier-hyperscale.md)服務層，以及自動調整儲存體和調整計算流暢地的能力。    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>可將資源使用量最大化的彈性集區

對於許多企業和應用程式而言，只要能夠建立單一資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 無法預測的使用模式可能會讓您難以管理成本和您的商務模型。 [彈性](elastic-pool-overview.md) 集區是設計用來解決此問題。 您可以將效能資源配置給集區，而不是個別的資料庫。 您需支付集區的集體效能資源，而非單一資料庫效能的費用。

   ![以基本、標準和 premium 版本顯示彈性集區的圖形](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

使用彈性集區時，您不需要專注于增加或減少資源的需求。 集區資料庫會視需要取用彈性集區的效能資源。 集區資料庫會取用但不超過集區的限制，因此即使沒有個別的資料庫使用量，您的成本仍會維持可預測性。

您可以 [將資料庫新增至集區，並將](elastic-pool-overview.md)您的應用程式從幾個資料庫擴充至數千個，全都在您所控制的預算內。 您也可以控制集區中資料庫可用的最小和最大資源，以確保集區中的資料庫不會使用所有集區資源，且每個集區資料庫都有保證的資源數量下限。 若要深入瞭解使用彈性集區的軟體即服務 (SaaS) 應用程式的設計模式，請參閱使用 [SQL Database 的多租使用者 SaaS 應用程式的設計模式](saas-tenancy-app-design-patterns.md)。

指令碼可協助您監視及調整彈性集區的規模。 如需範例，請參閱 [Azure SQL Database 中的使用 PowerShell 監視和調整彈性集](scripts/monitor-and-scale-pool-powershell.md)區。


### <a name="blend-single-databases-with-pooled-databases"></a>混合使用單一資料庫與集區資料庫

您可以混合使用單一資料庫與彈性集區，並變更單一資料庫和彈性集區的服務層級，以適應您的情況。 您也可以將其他 Azure 服務與 SQL Database 混合，以符合您獨特的應用程式設計需求、提高成本與資源效率，以及將新的商機發揮開。

## <a name="extensive-monitoring-and-alerting-capabilities"></a>廣泛的監視和警示功能

Azure SQL Database 提供先進的監視和疑難排解功能，可協助您深入瞭解工作負載特性。 這些功能和工具組括：
 - 最新版 SQL Server database engine 提供的內建監視功能。 它們可讓您尋找即時效能深入解析。 
 - Azure 提供的 PaaS 監視功能，可讓您監視大量資料庫實例並進行疑難排解。

[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)內建的 SQL Server 監視功能，可即時記錄查詢的效能，並可讓您識別潛在的效能問題和最上層的資源取用者。 自動調整和建議會針對具有回歸效能和遺漏或重複索引的查詢提供建議。 SQL Database 中的自動調整可讓您手動套用可修正問題的腳本，或讓 SQL Database 套用修正程式。 SQL Database 也可以測試並確認修正有提供一些優點，並根據結果保留或還原變更。 除了查詢存放區和自動調整功能之外，您還可以使用標準 [dmv 和 XEvent](monitoring-with-dmvs.md) 來監視工作負載效能。

Azure 提供 [內建的效能監視](performance-guidance.md) 和 [警示](alerts-insights-configure-portal.md) 工具（結合效能評等），可讓您監視數千個資料庫的狀態。 使用這些工具，您可以根據目前或預計的效能需求，快速評估相應增加或減少的影響。 此外，SQL Database 可以 [發出計量和資源記錄](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 檔，以便更輕鬆地進行監視。 您可以將 SQL Database 設定為將資源使用量、背景工作與工作階段及連線儲存到下列其中一項 Azure 資源：

- **Azure 儲存體**：用於封存大量遙測，價格實惠。
- **Azure 事件中樞**：用於整合 SQL Database 遙測與自訂監視解決方案或熱管線。
- **Azure 監視器記錄**：適用于具有報告、警示及緩和功能的內建監視解決方案。

![Azure 監視架構的圖表](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>可用性功能

Azure SQL Database 可讓您的企業在中斷時繼續運作。 在傳統 SQL Server 環境中，您通常會在本機設定至少兩部機器。 這些機器具有完整、同步維護的資料複本，可防止單一機器或元件失敗。 此環境提供高可用性，但不會防止天然損壞的資料中心損毀。

嚴重損壞修復假設有一個重大事件的地理位置當地語系化，足以讓另一部電腦或一組電腦有更遠的資料複本。 在 SQL Server 中，您可以使用以非同步模式執行 Always On 可用性群組來取得這項功能。 在認可交易之前，人們通常不會想要等待複寫發生的情況，因此當您執行非計畫的容錯移轉時，可能會遺失資料。

Premium 和業務關鍵服務層級中的資料庫已經與可用性群組的同步 [處理類似](high-availability-sla.md#premium-and-business-critical-service-tier-availability) 。 較低服務層級中的資料庫會使用 [不同但同等的機制](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability)，透過儲存體提供冗余。 內建邏輯有助於防止單一電腦失敗。 主動式異地複寫功能可讓您在整個區域終結的情況下防止發生損毀。

Azure 可用性區域會嘗試防止單一區域內的單一資料中心建立工作中斷。 它可協助您保護大樓免于遺失電源或網路。 在 SQL Database 中，您會將不同的複本放在不同的「可用性區域」中， (不同大樓，有效) 。

事實上，服務等級協定 (Azure 的 [ SLA) ](https://azure.microsoft.com/support/legal/sla/) ，由受 Microsoft 管理之資料中心的全球網路所支援，可協助讓您的應用程式執行24/7。 Azure 平臺可完全管理每個資料庫，並保證不會遺失任何資料，而且會有大量的資料可用性。 Azure 會自動處理修補、備份、複寫、失敗偵測，基礎潛在硬體、軟體或網路失敗、部署錯誤修正、容錯移轉、資料庫升級和其他維護工作。 隔離計算和儲存圖層可達成標準可用性。 高階可用性的達成方式是將計算和儲存體整合到單一節點上以達到效能，然後執行類似 Always On 可用性群組的技術。 如需 Azure SQL Database 高可用性功能的完整討論，請參閱 [SQL Database 可用性](high-availability-sla.md)。 

此外，SQL Database 還提供內建的 [商務持續性和全球](business-continuity-high-availability-disaster-recover-hadr-overview.md) 的擴充功能。 它們包括：

- [自動備份](automated-backups-overview.md)：

  SQL Database 會自動執行資料庫的完整、差異及交易記錄備份，讓您可以還原至任何時間點。 針對單一資料庫和集區資料庫，您可以設定 SQL Database，將完整資料庫備份儲存至 Azure 儲存體以進行長期備份保留。 對於受控執行個體，您也可以對僅複製備份執行長期備份保留。

- [時間點還原](recovery-using-backups.md)：

  所有 SQL Database 部署選項都支援復原到任何資料庫的自動備份保留期限內的任何時間點。
- [主動式異地](active-geo-replication-overview.md)複寫：

  單一資料庫和集區資料庫選項可讓您在相同或全域分散的 Azure 資料中心內，設定最多四個可讀取的次要資料庫。 例如，如果您的 SaaS 應用程式的目錄資料庫具有大量的並行唯讀交易，請使用「主動式異地複寫」來啟用全域讀取規模。 這會移除主要複本上由於讀取工作負載所造成的瓶頸。 對於受控執行個體，請使用自動容錯移轉群組。
- [自動容錯移轉群組](auto-failover-group-overview.md)：

  所有 SQL Database 部署選項都可讓您使用容錯移轉群組，以全球規模啟用高可用性和負載平衡。 這包括了大型資料庫、彈性集區和受控實例的透明異地複寫和容錯移轉。 容錯移轉群組可讓您建立全域散發的 SaaS 應用程式，並具有最少量的系統管理負擔。 這會讓所有複雜的監視、路由及容錯移轉協調流程保持 SQL Database。
- [區域冗余資料庫](high-availability-sla.md)：

  SQL Database 可讓您跨多個可用性區域佈建進階或業務關鍵資料庫或彈性集區。 因為這些資料庫和彈性集區具有多個冗余複本以達到高可用性，所以將這些複本放入多個可用性區域會提供更高的復原能力。 這包括能夠從資料中心規模的失敗自動復原，而不會遺失資料。

## <a name="built-in-intelligence"></a>內建智慧

有了 SQL Database，您就能取得內建的智慧功能，以協助您大幅降低執行和管理資料庫的成本，並將應用程式的效能和安全性最大化。 以時鐘來執行數百萬個客戶工作負載，SQL Database 會收集和處理大量的遙測資料，同時完全尊重客戶的隱私權。 各種演算法會持續評估遙測資料，讓服務能夠學習和適應您的應用程式。

### <a name="automatic-performance-monitoring-and-tuning"></a>自動的效能監視和微調

SQL Database 會提供您需要監視之查詢的詳細解析。 SQL Database 學習您的資料庫模式，並可讓您將資料庫架構調整為您的工作負載。 SQL Database 提供[效能微調建議](database-advisor-implement-performance-recommendations.md)，您可以在其中檢閱微調動作並加以套用。

不過，持續監視資料庫是一項困難又繁瑣的工作，特別是當您處理許多資料庫時。 [Intelligent Insights](intelligent-insights-overview.md) 會自動監視大規模的 SQL Database 效能，來為您執行這項作業。 它會通知您效能降低的問題，它會找出每個問題的根本原因，並盡可能提供效能改進建議。

即使使用 SQL Database 和 Azure 提供的所有可用工具和報表，也可能無法有效率地管理大量資料庫。 您可以考慮使用 [自動調整](automatic-tuning-overview.md)，將某些監視和微調動作委派給 SQL Database，而不是手動監視和調整資料庫。 SQL Database 會自動套用建議、測試及驗證其每個調整動作，以確保效能持續改進。 如此一來，SQL Database 會以控制且安全的方式自動調整您的工作負載。 自動調整表示在每個調整動作之前和之後，您資料庫的效能會受到仔細的監控和比較。 如果效能沒有改善，則會還原調整動作。

許多在 SQL Database 上執行 [SaaS 多租使用者應用程式](saas-tenancy-app-design-patterns.md) 的合作夥伴都依賴自動效能調整，以確保其應用程式一律具有穩定且可預測的效能。 對他們而言，這項功能可大幅降低夜間發生效能事件的風險。 此外，由於客戶群的一部分也會使用 SQL Server，因此會使用 SQL Database 所提供的相同索引編制建議，以協助其 SQL Server 客戶。

[SQL Database](automatic-tuning-overview.md)有兩個自動調整層面：

- **自動索引管理**：識別應該在資料庫中新增的索引，以及應該移除的索引。
- **自動計畫更正**：識別有問題的計畫並修正 SQL 計畫效能問題。

### <a name="adaptive-query-processing"></a>自適性查詢處理

您可以使用 [適應性查詢處理](/sql/relational-databases/performance/intelligent-query-processing)，包括交錯執行多重語句資料表值函式、批次模式記憶體授與意見反應，以及批次模式調適型聯接。 這些彈性查詢處理功能各有類似的「學習和調整」技術，可協助進一步解決與過去棘手查詢優化問題相關的效能問題。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

SQL Database 提供各種內 [建的安全性與合規性功能](../../active-directory/identity-protection/security-overview.md) ，可協助您的應用程式符合各種安全性與合規性需求。

> [!IMPORTANT]
> Microsoft 已通過認證 Azure SQL Database () 符合許多合規性標準的部署選項。 如需詳細資訊，請參閱 [Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在此找到最新的 SQL Database 合規性認證清單。

### <a name="advance-threat-protection"></a>預先威脅防護

進階資料安全性是進階 SQL 安全性功能的整合套件。 它包含探索和分類敏感性資料、管理資料庫弱點，以及偵測可能表示對資料庫有威脅之異常活動的功能。 此套件可讓您從單一點位置啟用及管理前述功能。

- [資料探索與分類](data-discovery-and-classification-overview.md)：

  這項功能提供內 Azure SQL Database 建的功能，可用於探索、分類、標記和保護資料庫中的敏感性資料。 它會提供資料庫分類狀態的可見度，並追蹤資料庫中敏感性資料的存取權，以及其框線以外的存取權。
- [弱點評定](sql-vulnerability-assessment.md)：

  這個服務可以探索、追蹤並協助您修復潛在的資料庫弱點。 可讓您檢視安全性狀態，且包含解決安全性問題和增強資料庫防護性的可行步驟。
- [威脅偵測](threat-detection-configure.md)：

  這項功能會偵測異常活動，指出有不尋常且可能有害的嘗試存取或惡意探索您的資料庫。 進階威脅防護會持續監視您的資料庫是否有可疑活動，並在發現潛在弱點、SQL 插入式攻擊和異常資料庫存取模式時提供即時安全性警示。 威脅防護警示會提供可疑活動的詳細資料，以及如何調查並降低威脅的建議。

### <a name="auditing-for-compliance-and-security"></a>合規性和安全性稽核

[審核](../../azure-sql/database/auditing-overview.md) 會追蹤資料庫事件，並將其寫入 Azure 儲存體帳戶中的 audit 記錄檔。 稽核可協助您保持法規合規性、了解資料庫活動，以及取得可能指出商務考量或疑似安全違規的不一致和異常見解。

### <a name="data-encryption"></a>資料加密

SQL Database 透過提供加密來協助保護您的資料。 針對移動中的資料，它會使用 [傳輸層安全性](https://support.microsoft.com/kb/3135244)。 針對待用資料，它會使用 [透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。 針對使用中的資料，它會使用 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

SQL Database 可讓您透過 [Azure Active Directory 整合](authentication-aad-overview.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure Active Directory 支援 [多重要素驗證](authentication-mfa-ssms-overview.md) ，以增加資料和應用程式安全性，同時支援單一登入精靈。

## <a name="easy-to-use-tools"></a>容易使用

SQL Database 讓應用程式的建置及維護更簡易也更有生產力。 SQL Database 讓您將精力集中於拿手項目：建置絕佳的應用程式。 您可以使用已具備的工具和技能，在 SQL Database 中管理和開發。

|工具|描述|
|:---|:---|
|[Azure 入口網站](https://portal.azure.com/)|用於管理所有 Azure 服務的 web 應用程式。|
|[Azure Data Studio](/sql/azure-data-studio/)|在 Windows、MacOS 和 Linux 上執行的跨平臺資料庫工具。|
|[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)|免費的可下載用戶端應用程式，可用於管理任何 SQL 基礎結構，從 SQL Server 到 SQL Database。|
|[Visual Studio 中的 SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)|可下載的免費用戶端應用程式，可用於開發 SQL Server 關係資料庫、Azure SQL Database 中的資料庫、Integration Services 封裝、Analysis Services 資料模型，以及 Reporting Services 報表。|
|[Visual Studio Code](https://code.visualstudio.com/docs)|適用于 Windows、macOS 和 Linux 的免費、可下載、開放原始碼的程式碼編輯器。 它支援擴充功能，包括用來查詢 Microsoft SQL Server、Azure SQL Database 和 Azure Azure Synapse Analytics (先前的 SQL 資料倉儲) 的 [mssql 擴充](https://aka.ms/mssql-marketplace) 功能。|

SQL Database 支援在 macOS、Linux 和 Windows 上使用 Python、JAVA、Node.js、PHP、Ruby 和 .NET 來建立應用程式。 SQL Database 支援與 SQL Server 相同的[連線庫](connect-query-content-reference-guide.md#libraries)。

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database 常見問題

### <a name="can-i-control-when-patching-downtime-occurs"></a>我可以控制何時發生修補停機？

不會。 如果您在應用程式中[採用重試邏輯](develop-overview.md#resiliency)，修補的影響通常不明顯。 如需詳細資訊，請參閱 [Azure SQL Database 中的規劃 Azure 維護事件](planned-maintenance.md)。



## <a name="engage-with-the-sql-server-engineering-team"></a>洽詢 SQL Server 工程團隊

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server)：詢問資料庫管理問題。
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server)：詢問開發問題。
- [Microsoft 問&問題頁面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)：詢問技術問題。
- [意見](https://aka.ms/sqlfeedback)反應：報告錯誤和要求功能。
- [Reddit](https://www.reddit.com/r/SQLServer/)：討論 SQL Server。

## <a name="next-steps"></a>接下來的步驟

- 如需有關單一資料庫和彈性集區的成本比較和計算機，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/sql-database/) 。
- 請參閱下列快速入門以開始使用：

  - [在 Azure 入口網站中建立資料庫](single-database-create-quickstart.md)  
  - [使用 Azure CLI 建立資料庫](az-cli-script-samples-content-guide.md)
  - [使用 PowerShell 建立資料庫](powershell-script-content-guide.md)

- 如需一組 Azure CLI 和 PowerShell 範例，請參閱︰
  - [SQL Database 的 Azure CLI 範例](az-cli-script-samples-content-guide.md)
  - [SQL Database 的 Azure PowerShell 範例](powershell-script-content-guide.md)

- 如需有關新功能宣佈的詳細資訊，請參閱 [SQL Database 的 Azure 藍圖](https://azure.microsoft.com/roadmap/?category=databases)。
- 請參閱 [Azure SQL Database 的 blog](https://azure.microsoft.com/blog/topics/database)，其中 SQL Server 產品團隊成員 SQL Database 新聞和功能的相關資訊。

