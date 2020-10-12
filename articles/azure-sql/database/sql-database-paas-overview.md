---
title: 什麼是 Azure SQL Database 服務？
description: 取得 SQL Database 簡介：Microsoft 關聯式資料庫管理系統 (RDBMS) 在雲端中的技術詳細資料和功能。
keywords: sql 簡介,sql 簡介,什麼是 sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 0fd3778d50216e337b872f0a27cb30b04a0219f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617215"
---
# <a name="what-is-azure-sql-database"></a>什麼是 Azure SQL Database？
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 是完全受控的平台即服務 (PaaS) 資料庫引擎，其無須經過使用者，即可處理大部分的資料庫管理功能，例如升級、修補、備份及監視。 Azure SQL Database 一律會在 SQL Server 資料庫引擎與具有 99.99% 可用性的已修補 OS 其最新穩定版本上執行。 內建於 Azure SQL Database 中的 PaaS 功能，可讓您專注於對企業至關重要的特定領域資料庫系統管理與最佳化活動。

有了 Azure SQL Database，您可為 Azure 中的應用程式與解決方案建立高度可用且高效能的資料儲存層。 SQL Database 可謂是適合各種現代化雲端應用程式的選擇，因為其可供處理關聯式資料，以及[非關聯式結構](../multi-model-features.md) (例如圖表、JSON、空間和 XML)。

Azure SQL Database 以最新穩定版本的 [Microsoft SQL Server 資料庫引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)為基礎。 您可使用進階查詢處理功能，例如[高效能記憶體內部技術](../in-memory-oltp-overview.md)和[智慧查詢處理](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)。 事實上，SQL Server 的最新功能會先發行至 SQL Database，然後才發行至 SQL Server 本身。 您可取得最新的 SQL Server 功能，無需額外的修補或升級，即可在數百萬個資料庫之間進行測試。 

SQL Database 可供輕鬆地在兩個不同的購買模型內定義和調整效能：[以虛擬核心為基礎的購買模型](service-tiers-vcore.md)和[以 DTU 為基礎的購買模型](service-tiers-dtu.md)。 SQL Database 是完全受控的服務，其具有內建的高可用性、備份與其他一般維護作業。 Microsoft 可處理 SQL 和作業系統程式碼的所有修補和更新。 您不需要管理基礎結構。

如果您不熟悉 Azure SQL Database，請觀看我們深入的 [Azure SQL 影片系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中的 *Azure SQL Database 概觀*影片：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

## <a name="deployment-models"></a>部署模型

Azure SQL Database 為資料庫提供下列部署選項：

- [單一資料庫](single-database-overview.md)代表完全受控的隔離資料庫。 如果有需要單一可靠資料來源的現代化雲端應用程式和微服務，則可使用此選項。 單一資料庫類似於 [SQL Server 資料庫引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)中的[自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json)。
- [彈性集區](elastic-pool-overview.md)是具有一組共用資源 (例如 CPU 或記憶體) 的[單一資料庫](single-database-overview.md)集合。 單一資料庫可以移入和移出彈性集區。

> [!IMPORTANT]
> 若要了解 SQL Database 和 SQL Server 之間的功能差異，以及不同 Azure SQL Database 選項之間的差異，請參閱 [SQL Database 功能](features-comparison.md)。

SQL Database 利用多種資源類型、服務層級和計算大小來提供可預測的效能。 其提供無須停機的動態可擴縮性、內建智慧最佳化、全球可擴縮性和可用性，以及進階安全性選項。 這些功能可供專注於快速開發應用程式及加快上市時間，而非管理虛擬機器和基礎結構。 SQL Database 目前在全球 38 個資料中心提供，您可在附近的資料中心執行資料庫。

## <a name="scalable-performance-and-pools"></a>可擴充的效能和集區

您可定義指派的資源數量。 
- 使用單一資料庫，每個資料庫都彼此隔離且可移植。 各自都有保證數量的計算、記憶體和儲存體資源。 指派給資料庫的資源數量會專用於該資料庫，而不會與 Azure 中的其他資料庫共用。 您可動態[擴大和縮小單一資料庫資源](single-database-scale.md)。 單一資料庫選項提供不同的計算、記憶體和儲存體資源，以滿足不同需求。 例如，您可取得 1 到 80 個虛擬核心，或 32 GB 到 4 TB。 單一資料庫的[超大規模資料庫服務層級](service-tier-hyperscale.md)可供擴充到 100 TB，且有快速備份和還原功能。
- 使用彈性集區，您可指派集區中所有資料庫所要共用的資源。 您可建立新的資料庫，或將現有單一資料庫移至資源集區，以充分利用資源並節省成本。 此選項也可供動態[擴大和縮小彈性集區資源](elastic-pool-scale.md)。

您可在一般目的服務層級中以每個月很低的費用，在一個小型單一資料庫上建置第一個應用程式。 然後，您可隨時以手動或程式設計方式，將其服務層級變更為業務關鍵服務層級，以符合解決方案需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求。 您只需支付所需的資源費用。

「動態可擴縮性」與「自動調整」不同。 自動調整規模是指服務根據準則來自動調整規模，而動態延展性則允許在不停機的狀況下手動調整規模。 單一資料庫選項支援手動的動態可擴縮性，但不支援自動調整。 如需「自動」體驗，請考慮使用彈性集區，以根據個別資料庫的需求讓資料庫共用集區中的資源。 另一個選項是使用可協助對單一資料庫自動調整可擴縮性的指令碼。 如需範例，請參閱[使用 PowerShell 來監視和調整單一資料庫的規模](scripts/monitor-and-scale-database-powershell.md)。

### <a name="purchasing-models"></a>購買模型

SQL Database 提供下列購買模型：
- [以虛擬核心為基礎的購買模型](service-tiers-vcore.md)可供選擇虛擬核心數目、記憶體數量，以及儲存體數量和速度。 以虛擬核心為基礎的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。 如需 Azure Hybrid Benefit 的詳細資訊，請參閱本文稍後的＜常見問題集＞一節。
- [以 DTU 為基礎的購買模型](service-tiers-dtu.md)提供三個服務層級中的計算、記憶體和 I/O 資源混合，以支援輕量型到重量型的資料庫工作負載。 各層內的計算大小分別提供這些資源的不同混用方式，而且您可以為其新增額外的儲存體資源。
- [無伺服器模型](serverless-tier-overview.md)可根據工作負載需求自動調整計算，並以每秒使用的計算量來計費。 無伺服器計算層也會在只有儲存體仍在計費的資料庫非使用中期間自動暫停資料庫，並在有活動傳回時自動繼續執行資料庫。

### <a name="service-tiers"></a>服務層

Azure SQL Database 提供三個專為不同應用程式類型所設計的服務層級：
- [一般目的/標準](service-tier-general-purpose.md)服務層級，專為一般工作負載所設計。 其提供以預算為導向且平衡的計算與儲存體選項。
- [業務關鍵/進階](service-tier-business-critical.md)服務層級，專為具有高交易率和最低延遲 I/O 的 OLTP 應用程式所設計。 其藉由使用數個隔離的複本來提供最高失敗復原能力。
- [超大規模資料庫](service-tier-hyperscale.md)服務層級，專為超大型 OLTP 資料庫所設計，且能夠自動調整儲存體並流暢地調整計算。    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>可將資源使用量最大化的彈性集區

對於許多企業和應用程式而言，只要能夠建立單一資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 無法預測的使用模式可能會使得難以管理成本和商務模型。 [彈性集區](elastic-pool-overview.md)的設計可解決此問題。 您可將效能資源配置給集區，而非個別資料庫。 然後針對集區的整體效能資源 (而非單一資料庫效能) 付費。

   ![顯示基本、標準和進階版彈性集區的圖表](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

使用彈性集區，您就不必隨著資源的需求波動而專注於調高或調低資料庫效能。 集區資料庫會視需要取用彈性集區的效能資源。 集區資料庫會取用集區的資源，但不會超過其限制，因此成本可在個別資料庫使用情形無法預測的狀況下維持可預測性。

您可[將資料庫新增至集區並從中移除](elastic-pool-overview.md)，並將應用程式從數個資料庫擴充至數千個，且全都在可掌控的預算內。 您也可以控制集區中資料庫可用的資源下限和上限，以確保集區中不會有任一資料庫使用所有的集區資源，且每個集區資料庫都有保證的最低資源數量。 若要深入了解使用彈性集區的軟體即服務 (SaaS) 應用程式設計模式，請參閱[採用 SQL Database 的多租用戶 SaaS 應用程式設計模式](saas-tenancy-app-design-patterns.md)。

指令碼可協助您監視及調整彈性集區的規模。 如需範例，請參閱[使用 PowerShell 在 Azure SQL Database 中監視和調整彈性集區](scripts/monitor-and-scale-pool-powershell.md)。


### <a name="blend-single-databases-with-pooled-databases"></a>混合使用單一資料庫與集區資料庫

您可混合使用單一資料庫與彈性集區，並變更單一資料庫和彈性集區的服務層級來適應情況。 您也可以使用 SQL Database 來混合並搭配其他 Azure 服務，以滿足獨特應用程式的設計需求、有效運用成本和資源，且產生新的商機。

## <a name="extensive-monitoring-and-alerting-capabilities"></a>廣泛的監視和警示功能

Azure SQL Database 提供進階監視和疑難排解功能，可協助更深入解析工作負載特性。 這些功能和工具包括：
 - 最新版的 SQL Server 資料庫引擎所提供的內建監視功能。 這些功能可供尋找即時效能深入解析。 
 - Azure 提供的 PaaS 監視功能，可供監視大量的資料庫執行個體並為其進行疑難排解。

[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)是內建的 SQL Server 監視功能，可即時記錄查詢效能，並可供找出潛在的效能問題和最耗用資源的資源取用者。 自動調整和建議為效能迴歸以及索引遺漏或重複的查詢提供相關建議。 SQL Database 中的自動調整可供手動套用指令碼來修正問題，或讓 SQL Database 套用修正程式。 SQL Database 也可測試並驗證修正程式是否提供一些好處，並根據結果保留或還原變更。 除了查詢存放區和自動調整功能以外，您還可使用標準 [DMV 和 XEvent](monitoring-with-dmvs.md) 來監視工作負載效能。

Azure 提供[內建的效能監視](performance-guidance.md)和[警示](alerts-insights-configure-portal.md)工具，並搭配使用效能分級，其可供監視數千個資料庫的狀態。 使用這些工具，即可根據目前或所預計效能需求來快速評定擴大或縮小的影響。 此外，SQL Database 可[發出計量和資源記錄](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)，以供更輕鬆地進行監視。 您可以將 SQL Database 設定為將資源使用量、背景工作與工作階段及連線儲存到下列其中一項 Azure 資源：

- **Azure 儲存體**：用於封存大量遙測，價格實惠。
- **Azure 事件中樞**：用於整合 SQL Database 遙測與自訂監視解決方案或經常性管線。
- **Azure 監視器記錄**：用於具有報告、警示及風險降低功能的內建監視解決方案。

![Azure 監視架構的圖表](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>可用性功能

Azure SQL Database 可讓企業在面臨中斷時持續運作。 在傳統的 SQL Server 環境中，您通常會在本機設定至少兩部電腦。 這些電腦具有完全保持同步的資料複本，以提供保護來防止單一電腦或元件失敗。 此環境提供高可用性，但無法提供保護來防止自然災害損毀資料中心。

災害復原假設在災難性事件遠端設置了具有資料複本的另一部電腦或另一組電腦。 在 SQL Server 中，您可使用在非同步模式下執行的 Always On 可用性群組來取得這項功能。 使用者通常不想要等候複寫在那麼遠的地方出現再認可交易，因此當執行非計劃性容錯移轉時，可能會遺失資料。

進階和業務關鍵服務層級中的資料庫已執行與可用性群組的同步處理[類似的功能](high-availability-sla.md#premium-and-business-critical-service-tier-availability)。 較低服務層級中資料庫則使用[不同但對等的機制](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability)，以透過儲存體提供備援。 內建邏輯有助於提供保護來防止單一電腦失敗。 主動式異地複寫功能可供提供保護來防止整個區域損毀的災害。

Azure 可用性區域會嘗試提供保護來防止單一區域內單一資料中心建築物的中斷。 其可協助提供保護來防止建築物的電源或網路中斷。 在 SQL Database 中，您會將不同複本放在不同的可用性區域中 (實際不同的建築物)。

事實上，由 Microsoft 受控資料中心全球網路提供的 Azure 服務等級協定 [(SLA)](https://azure.microsoft.com/support/legal/sla/)，可協助讓應用程式 24 小時全年無休地運作。 Azure 平台可完全管理每個資料庫，並保證任何資料都不會遺失及高度的資料可用性。 Azure 會自動處理修補、備份、複寫、失敗偵測，基礎潛在硬體、軟體或網路失敗、部署錯誤修正、容錯移轉、資料庫升級和其他維護工作。 隔離計算和儲存圖層可達成標準可用性。 整合單一節點上的計算和儲存體以獲取效能，然後實作類似 Always On 可用性群組的技術，則可達成進階可用性。 如需 Azure SQL Database 高可用性功能的完整討論，請參閱 [SQL Database 可用性](high-availability-sla.md)。 

此外，SQL Database 還提供內建的[商務持續性和全球可擴縮性](business-continuity-high-availability-disaster-recover-hadr-overview.md)功能。 其中包括：

- [自動備份](automated-backups-overview.md)：

  SQL Database 會自動執行資料庫的完整、差異及交易記錄備份，以供您還原至任何時間點。 針對單一資料庫和集區資料庫，您可設定 SQL Database 將完整資料庫備份儲存至 Azure 儲存體，以供長期備份保留。 對於受控執行個體，您也可以對僅複製備份執行長期備份保留。

- [時間點還原](recovery-using-backups.md)：

  所有 SQL Database 部署選項均支援復原到任何資料庫自動備份保留期間內的任何時間點。
- [主動式異地複寫](active-geo-replication-overview.md)：

  單一資料庫和集區資料庫選項可供在相同或分散於全球的 Azure 資料中心最多設定四個可讀取的次要資料庫。 例如，如果 SaaS 應用程式的目錄資料庫具有大量並行唯讀交易，請使用主動式異地複寫功能來啟用全球讀取級別。 這會移除主要資料庫上因為讀取工作負載所造成的瓶頸。 對於受控執行個體，請使用自動容錯移轉群組。
- [自動容錯移轉群組](auto-failover-group-overview.md)：

  所有 SQL Database 部署選項均可供使用容錯移轉群組來啟用全球規模的高可用性和負載平衡。 這包括大型資料庫、彈性集區和受控執行個體集合的透明異地複寫和容錯移轉。 容錯移轉群組可供建立分散於全球的 SaaS 應用程式，其管理負擔最小。 這可讓 SQL Database 處理所有複雜的監視、路由傳送及容錯移轉協調流程。
- [區域備援資料庫](high-availability-sla.md)：

  SQL Database 可讓您跨多個可用性區域佈建進階或業務關鍵資料庫或彈性集區。 由於這些資料庫和彈性集區有多個備援複本可提供高可用性，因此將這些複本放入多個可用性區域即可提供更高的復原能力。 這包括能夠從資料中心規模的失敗自動復原，而不會遺失任何資料。

## <a name="built-in-intelligence"></a>內建智慧

採用 SQL Database，您可取得內建智慧來協助大幅降低執行和管理資料庫的成本，並將應用程式的效能和安全性提到最高。 SQL Database 隨時執行數以百萬計的客戶工作負載，並收集和處理大量的遙測資料，同時完全尊重客戶隱私權。 各種演算法會持續評估遙測資料，讓服務能夠了解應用程式並隨其調整。

### <a name="automatic-performance-monitoring-and-tuning"></a>自動的效能監視和微調

SQL Database 會提供您需要監視之查詢的詳細解析。 SQL Database 會了解資料庫模式，並可供根據自己的工作負載調整資料庫結構描述。 SQL Database 提供[效能微調建議](database-advisor-implement-performance-recommendations.md)，您可以在其中檢閱微調動作並加以套用。

不過，持續監視資料庫是冗長乏味的艱辛工作，尤其是在處理許多資料庫時。 [Intelligent Insights](intelligent-insights-overview.md) 會自動大規模監視 SQL Database 效能來替您代勞。 其會通知效能降低的問題、識別每個問題的根本原因，然後盡可能提供效能改善建議。

即使使用 SQL Database 和 Azure 提供的所有可用工具和報表，都可能無法有效率地管理大量資料庫。 除了手動監視和調整資料庫，您也可以考慮使用[自動調整](automatic-tuning-overview.md)，將一些監視和調整動作委派給 SQL Database。 SQL Database 會自動套用建議、測試及驗證每個調整動作，以確保持續改善效能。 如此一來，SQL Database 會以受控制且安全的方式自動根據工作負載進行調整。 自動調整表示在每個調整動作前後，資料庫效能都會受到仔細的監控和比較。 如果效能沒有改善，則會還原調整動作。

在 SQL Database 上執行 [SaaS 多租用戶應用程式](saas-tenancy-app-design-patterns.md)的許多合作夥伴都依賴自動效能調整來確保其應用程式始終擁有穩定且可預測效能。 對他們而言，這項功能可大幅降低夜間發生效能事件的風險。 此外，由於其部分客戶也使用 SQL Server，因此合作夥伴會使用 SQL Database 所提供的相同索引建議來協助其 SQL Server 客戶。

[SQL Database 中可用的](automatic-tuning-overview.md)自動調整層面有兩個：

- **自動索引管理**：識別應該在資料庫中新增的索引，以及應該移除的索引。
- **自動計劃修正**：識別有問題的計劃並修正 SQL 計劃效能問題。

### <a name="adaptive-query-processing"></a>自適性查詢處理

您可使用[自適性查詢處理](/sql/relational-databases/performance/intelligent-query-processing)，包括交錯執行多重陳述式資料表值函式、批次模式記憶體授與回饋，以及批次模式自適性聯結。 每個自適性查詢處理功能都會應用類似的「了解並適應」技術，協助進一步解決與過去很棘手的查詢最佳化問題相關效能問題。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

SQL Database 提供了各式各樣的[內建安全性與合規性功能](../../active-directory/identity-protection/security-overview.md)，協助您的應用程式符合各種安全性與合規性需求。

> [!IMPORTANT]
> Azure SQL Database (所有部署選項) 已通過許多合規性標準的 Microsoft 認證。 如需詳細資訊，請參閱 [Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可在當中找到 SQL Database 合規性認證的最新清單。

### <a name="advance-threat-protection"></a>進階威脅防護

適用於 SQL 的 Azure Defender 是進階 SQL 安全性功能的整合套件。 其功能包括管理資料庫弱點，以及偵測可能表示對資料庫造成威脅的異常活動。 此套件可讓您從單一點位置啟用及管理前述功能。

- [弱點評量](sql-vulnerability-assessment.md)：

  這個服務可以探索、追蹤並協助您修復潛在的資料庫弱點。 可讓您檢視安全性狀態，且包含解決安全性問題和增強資料庫防護性的可行步驟。
- [威脅偵測](threat-detection-configure.md)：

  這項功能可偵測異常活動，指出有不尋常及可能有害的活動試圖存取或惡意探索資料庫。 進階威脅防護會持續監視您的資料庫是否有可疑活動，並在發現潛在弱點、SQL 插入式攻擊和異常資料庫存取模式時提供即時安全性警示。 威脅防護警示會提供可疑活動的詳細資料，以及如何調查並降低威脅的建議。

### <a name="auditing-for-compliance-and-security"></a>合規性和安全性稽核

[稽核](../../azure-sql/database/auditing-overview.md)會追蹤資料庫事件，並將它們寫入至您 Azure 儲存體帳戶中的稽核記錄。 稽核可協助您保持法規合規性、了解資料庫活動，以及取得可能指出商務考量或疑似安全違規的不一致和異常見解。

### <a name="data-encryption"></a>資料加密

SQL Database 透過提供加密來協助保護您的資料。 針對移動中的資料，其會使用[傳輸層安全性](https://support.microsoft.com/kb/3135244)。 針對待用資料，其會使用[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。 針對使用中的資料，會使用 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)。

### <a name="data-discovery-and-classification"></a>資料探索與分類

[資料探索與分類](data-discovery-and-classification-overview.md)提供內建於 Azure SQL Database 中的功能，可用來探索、分類、標記及保護資料庫中的敏感資料。 可供檢視資料庫分類狀態、追蹤對資料庫內敏感性資料的存取，並具有其他多方面的用途。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

SQL Database 可讓您透過 [Azure Active Directory 整合](authentication-aad-overview.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure Active Directory 支援[多重要素驗證](authentication-mfa-ssms-overview.md)，以提高資料和應用程式安全性，同時支援單一登入程序。

## <a name="easy-to-use-tools"></a>容易使用

SQL Database 讓應用程式的建置及維護更簡易也更有生產力。 SQL Database 讓您將精力集中於拿手項目：建置絕佳的應用程式。 您可使用已經擁有的工具和技能，在 SQL Database 中進行管理和開發。

|工具|描述|
|:---|:---|
|[Azure 入口網站](https://portal.azure.com/)|Web 應用程式，用於管理所有 Azure 服務。|
|[Azure Data Studio](/sql/azure-data-studio/)|可在 Windows、macOS 和 Linux 上執行的跨平台資料庫工具。|
|[Transact-SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)|可下載的免費用戶端應用程式，其用於管理任何 SQL 基礎結構 (從 SQL Server 到 SQL Database)。|
|[Visual Studio 中的 SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)|可供下載的免費用戶端應用程式，可用來開發 SQL Server 關聯式資料庫、Azure SQL Database 中的資料庫、Integration Services 套件、Analysis Services 資料模型及 Reporting Services 報告。|
|[Visual Studio Code](https://code.visualstudio.com/docs) \(英文\)|可下載的免費開放原始碼程式碼編輯器，其適用於 Windows、macOS 和 Linux。 其支援延伸模組，包括可供查詢 Microsoft SQL Server、Azure SQL Database 和 Azure Synapse Analytics (先前稱為 SQL 資料倉儲) 的 [mssql 延伸模組](https://aka.ms/mssql-marketplace)。|

SQL Database 支援在 macOS、Linux 和 Windows 上使用 Python、Java、Node.js、PHP、Ruby 和 .NET 建置應用程式。 SQL Database 支援與 SQL Server 相同的[連線庫](connect-query-content-reference-guide.md#libraries)。

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database 常見問題集

### <a name="can-i-control-when-patching-downtime-occurs"></a>我能否控制修補停機時間何時發生？

不可以。 如果您在應用程式中[採用重試邏輯](develop-overview.md#resiliency)，修補的影響通常不明顯。 如需詳細資訊，請參閱[規劃 Azure SQL Database 的 Azure 維護事件](planned-maintenance.md)。



## <a name="engage-with-the-sql-server-engineering-team"></a>洽詢 SQL Server 工程團隊

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server) \(英文\)：詢問資料庫管理問題。
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server)：詢問開發問題。
- [Microsoft 問與答的問題頁面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)：詢問技術問題。
- [意見反應](https://aka.ms/sqlfeedback)：報告 Bug 和要求功能。
- [Reddit](https://www.reddit.com/r/SQLServer/)：討論 SQL Server。

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫和彈性集區的成本比較和計算機，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)。
- 請參閱下列快速入門，以便開始使用產品：

  - [在 Azure 入口網站中建立資料庫](single-database-create-quickstart.md)  
  - [使用 Azure CLI 建立資料庫](az-cli-script-samples-content-guide.md)
  - [使用 PowerShell 建立資料庫](powershell-script-content-guide.md)

- 如需一組 Azure CLI 和 PowerShell 範例，請參閱︰
  - [SQL Database 的 Azure CLI 範例](az-cli-script-samples-content-guide.md)
  - [SQL Database 的 Azure PowerShell 範例](powershell-script-content-guide.md)

- 如需所公告新功能的資訊，請參閱[適用於 SQL Database 的 Azure 藍圖](https://azure.microsoft.com/roadmap/?category=databases)。
- 請參閱 [Azure SQL Database 部落格](https://azure.microsoft.com/blog/topics/database)，這是 SQL Server 產品小組成員發表 SQL Database 消息和功能的地方。

