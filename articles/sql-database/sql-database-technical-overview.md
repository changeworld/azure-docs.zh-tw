---
title: 什麼是 Azure SQL Database 服務？
description: 獲取 SQL 資料庫簡介:雲中的 Microsoft 關係資料庫管理系統 (RDBMS) 的技術詳細資訊和功能。
keywords: sql 簡介,sql 簡介,什麼是 sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 0d50ddbbeeaed48c14d07c42588efcbb20bb7d79
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411162"
---
# <a name="what-is-the-azure-sql-database-service"></a>什麼是 Azure SQL Database 服務？

Azure SQL Database 是完全受控的平台即服務 (PaaS) 資料庫引擎，可處理大部分的資料庫管理功能，例如升級、修補、備份和監視，而不需要使用者介入。 Azure SQL Database 一律會在最新穩定版本的 SQL Server 資料庫引擎上執行，並可在已修補作業系統達 99.99% 的可用性。 Azure SQL 資料庫中內置的 PaaS 功能使您能夠專注於對業務至關重要的特定於域的資料庫管理和優化活動。

使用 Azure SQL 資料庫,可以為 Azure 中的應用程式和解決方案創建高可用性和高性能數據儲存層。 SQL 資料庫是各種現代雲端應用程式的正確選擇,因為它使您能夠處理關係數據[和非關係結構](sql-database-multi-model-features.md),如圖形、JSON、空間和 XML。

它基於微軟[SQL Server 資料庫引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)的最新穩定版本。 您可以使用進階查詢處理功能,如[高效能記憶體技術與](sql-database-in-memory.md)[智慧查詢處理](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)。 事實上,SQL Server 的最新功能首先發佈到 SQL 資料庫,然後發布到 SQL Server 本身。 您將獲得最新的 SQL Server 功能,無需開銷進行修補或升級,這些功能在數百萬個資料庫中進行測試。 

SQL 資料庫讓您能夠在兩種不同的採購模型中輕鬆定義和擴充效能:基於[vCore 的採購模型](sql-database-service-tiers-vcore.md)和基於[DTU 的採購模型](sql-database-service-tiers-dtu.md)。 SQL 資料庫是一種完全託管的服務,具有內置的高可用性、備份和其他常見維護操作。 Microsoft 處理 SQL 和作業系統代碼的所有修補和更新。 您不必管理底層基礎結構。

> [!NOTE]
> 有關相關術語及其定義,請參閱 SQL[資料庫術語術語表](sql-database-glossary-terms.md)。

## <a name="deployment-models"></a>部署模型

Azure SQL Database 為 Azure SQL 資料庫提供下列部署選項：

![部署選項圖](./media/sql-database-technical-overview/deployment-options.png)

- [單個資料庫](sql-database-single-database.md)表示完全託管的隔離資料庫。 如果您有需要單個可靠數據源的現代雲應用程式和微服務,則可以使用此選項。 單個資料庫類似於 Microsoft SQL Server[資料庫引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)中包含的[資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json)。
- [託管實例](sql-database-managed-instance.md)是微軟 SQL Server[資料庫引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)的完全託管實例。 它包含一組可以一起使用的資料庫。 使用此選項可輕鬆將本地 SQL Server 資料庫遷移到 Azure 雲端,以及需要使用 SQL Server 資料庫引擎提供的資料庫功能的應用程式。
- [彈性池](sql-database-elastic-pool.md)是具有一組共享資源(如 CPU 或記憶體)的[單個資料庫](sql-database-single-database.md)的集合。 單一資料庫可以移入和移出彈性集區。

> [!IMPORTANT]
> 要瞭解 SQL 資料庫與 SQL Server 之間的功能差異,以及不同 Azure SQL 資料庫部署選項之間的差異,請參閱[SQL 資料庫功能](sql-database-features.md)。

SQL 資料庫具有多種資源類型、服務層和計算大小,可提供可預測的性能。 它提供了動態可擴充性,沒有停機時間,內置的智慧優化,全球可擴充性和可用性,以及先進安全選項。 這些功能使您能夠專注於快速應用開發和加快上市時間,而不是管理虛擬機和基礎架構。 SQL 資料庫服務目前位於全球 38 個資料中心中,因此您可以在附近的資料中心運行資料庫。

## <a name="scalable-performance-and-pools"></a>可擴充的效能和集區

您可以定義分配的資源量。 
- 對於單個資料庫,每個資料庫與其他資料庫隔離,並且是可移植的。 每個都有其自己的保證量計算、記憶體和存儲資源。 分配給資料庫的資源量專用於該資料庫,並且不會與 Azure 中的其他資料庫共用。 您可以動態[延伸單資料庫資源](sql-database-single-database-scale.md)。 單個資料庫選項提供不同的計算、記憶體和存儲資源,以滿足不同的需求。 例如,您可以獲取 1 到 80 個 vCore,或 32 GB 到 4 TB。 單資料庫的[超大規模服務層](sql-database-service-tier-hyperscale.md)使您能夠擴展至 100 TB,具有快速備份和恢復功能。
- 使用彈性池,可以分配池中所有資料庫共用的資源。 您可以創建新資料庫,也可以將現有單個資料庫移動到資源池中,以最大限度地利用資源並節省資金。 這個選項可以讓您能夠動態[擴充彈性池資源](sql-database-elastic-pool-scale.md)。
- 使用受控執行個體，每個執行個體會利用保證的資源與其他執行個體隔離。 在託管實例中,實例資料庫共享一組資源。 您可以動態[延伸託管實例資源](sql-database-managed-instance-resource-limits.md)。

您可以在通用服務層中每月以較低的成本在小型單個資料庫上構建第一個應用。 然後,您可以隨時手動或以程式設計方式將其服務層更改為業務關鍵型服務層,以滿足解決方案的需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求。 當您需要這些資源時,您只需支付所需的資源。

*動態可伸縮性*不同於*自動縮放*。 自動調整規模是指服務根據準則來自動調整規模，而動態延展性則允許在不停機的狀況下手動調整規模。 單個資料庫選項支援手動動態可伸縮性,但不支援自動縮放。 如需「自動」體驗，請考慮使用彈性集區，以根據個別資料庫的需求讓資料庫共用集區中的資源。 另一個選項是使用有助於自動實現單個資料庫的可伸縮性的腳本。 如需範例，請參閱[使用 PowerShell 來監視和調整單一資料庫的規模](scripts/sql-database-monitor-and-scale-database-powershell.md)。

### <a name="purchasing-models"></a>購買模型

SQL 資料庫提供以下購買模型:
- 基於[vCore 的採購模型](sql-database-service-tiers-vcore.md)允許您選擇 vCore 的數量、內存量以及儲存量和速度。 以虛擬核心為基礎的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。 有關 Azure 混合權益的詳細資訊,請參閱本文後面的"常見問題"部分。
- [基於 DTU 的採購模型](sql-database-service-tiers-dtu.md)在三個服務層中混合了計算、記憶體和 I/O 資源,以支援輕到重的資料庫工作負載。 各層內的計算大小分別提供這些資源的不同混用方式，而且您可以為其新增額外的儲存體資源。
- [無伺服器模型](sql-database-serverless.md)根據工作負載需求自動縮放計算,並按每秒使用的計算量進行帳單。 無伺服器計算層還會在僅計費存儲的非活動期間自動暫停資料庫,並在活動返回時自動復原資料庫。

### <a name="service-tiers"></a>服務層

Azure SQL 資料庫提供三個服務層,專為不同類型的應用程式設計:
- 專為常見工作負載設計的[通用/標準](sql-database-service-tier-general-purpose.md)服務層。 它提供面向預算的平衡計算和存儲選項。
- [業務關鍵/進階](sql-database-service-tier-business-critical.md)服務層專為具有高事務速率和最低延遲 I/O 的 OLTP 應用程式而設計。 通過使用多個隔離副本,它提供了對故障的最高彈性。
- 專為非常大的 OLTP 資料庫設計的[超大規模](sql-database-service-tier-hyperscale.md)服務層,並能夠流暢地自動縮放存儲和縮放計算。    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>可將資源使用量最大化的彈性集區

對於許多企業和應用程式而言，只要能夠建立單一資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 不可預測的使用模式會使管理成本和業務模式變得困難。 [彈性池](sql-database-elastic-pool.md)旨在解決此問題。 將性能資源分配給池,而不是單個資料庫。 您可以為池的集體性能資源付費,而不是為單個資料庫性能付費。

   ![顯示基本版、標準版和高級版中的彈性池的圖形](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

對於彈性池,您無需在資源需求波動時關注向上和向下撥號資料庫性能。 集區資料庫會視需要取用彈性集區的效能資源。 池資料庫會消耗,但不超過池的限制,因此即使單個資料庫使用沒有,您的成本仍可預測。

您可以將[資料庫添加到池中](sql-database-elastic-pool-manage-portal.md),將應用從少數資料庫擴展到數千個資料庫,所有這些都在您控制的預算範圍內。 您還可以控制池中資料庫可用的最小和最大資源,以確保池中沒有任何資料庫使用所有池資源,並且每個池資料庫都有保證的最小資源量。 要瞭解有關使用彈性池的軟體即服務 (SaaS) 應用程式的設計模式的詳細資訊,請參閱具有[SQL 資料庫的多租戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

指令碼可協助您監視及調整彈性集區的規模。 例如,請參閱使用[PowerShell 監視和縮放 Azure SQL 資料庫中的 SQL 彈性池](scripts/sql-database-monitor-and-scale-pool-powershell.md)。

> [!IMPORTANT]
> 託管實例不支援彈性池。 相反地，受控執行個體是共用受控執行個體資源的執行個體資料庫集合。

### <a name="blend-single-databases-with-pooled-databases"></a>混合使用單一資料庫與集區資料庫

您可以將單個資料庫與彈性池混合,並更改單個資料庫和彈性池的服務層以適應您的情況。 您還可以將其他 Azure 服務與 SQL 資料庫混合和匹配,以滿足獨特的應用設計需求、提高成本和資源效率以及釋放新的業務機會。

## <a name="extensive-monitoring-and-alerting-capabilities"></a>廣泛的監視和警示功能

Azure SQL 資料庫提供進階監視和故障排除功能,可説明您深入瞭解工作負載特徵。 這些功能和工具包括:
 - 最新版本的 SQL Server 資料庫引擎提供的內建監視功能。 它們使您能夠找到即時性能見解。 
 - Azure 提供的 PaaS 監視功能使您能夠監視和排除大量資料庫實例。

[查詢存儲](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)(內置 SQL Server 監視功能)可即時記錄查詢的性能,並使您能夠識別潛在的性能問題和頂級資源消費者。 自動調優和建議提供有關具有遞減性能以及缺少或重複索引的查詢的建議。 通過 SQL 資料庫中的自動調優,您可以手動應用可以修復問題的腳本,或者讓 SQL 資料庫應用修復程式。 SQL 資料庫還可以測試和驗證修復是否提供了一些好處,並根據結果保留或還原更改。 除了查詢儲存和自動調優功能外,您還可以使用標準[DMV 和 XEvent](sql-database-monitoring-with-dmvs.md)來監視工作負載性能。

Azure 提供[內建的性能監視](sql-database-performance-guidance.md)和[警報](sql-database-insights-alerts-portal.md)工具,以及性能評級,使您能夠監視數千個資料庫的狀態。 使用這些工具,您可以根據當前或預測的性能需求快速評估向上或向下擴展的影響。 此外，SQL Database 可以[發出計量和診斷記錄](sql-database-metrics-diag-logging.md)以便進行監視。 您可以將 SQL Database 設定為將資源使用量、背景工作與工作階段及連線儲存到下列其中一項 Azure 資源：

- **Azure 儲存體**：用於封存大量遙測，價格實惠。
- **Azure 事件中心**:用於將 SQL 資料庫遙測與自定義監視解決方案或熱管道集成。
- **Azure 監視器日誌**:用於具有報告、警報和緩解功能的內置監視解決方案。

![Azure 監視器結構圖](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>可用性功能

Azure SQL 資料庫使企業能夠在中斷期間繼續運營。 在傳統的 SQL Server 環境中,您通常至少設置兩台本地電腦。 這些計算機具有精確、同步維護的數據副本,以防止單個計算機或元件發生故障。 此環境提供高可用性,但它無法抵禦破壞數據中心的自然災害。

災難復原假定災難性事件在地理上具有足夠的當地語系化,以便有另一台電腦或一組具有資料副本的電腦。 在 SQL Server 中,可以使用在非同步模式下運行的「始終處於可用性」組來獲得此功能。 人們通常不希望在提交事務之前等待複製發生那麼遠,因此,當您執行計劃外故障轉移時,可能會丟失數據。

高級服務層和業務關鍵型服務層中的資料庫已經[執行類似於](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)可用性組的同步。 較低服務層中的資料庫通過使用[不同但等效的機制](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)通過存儲提供冗餘。 內置邏輯有助於防止單個計算機故障。 活動異地複製功能使您能夠在整個區域遭到破壞的地方抵禦災難。

Azure 可用性區域嘗試防止單個資料中心在單個區域中的構建中斷。 它可以説明您防止建築物斷電或網路損失。 在 SQL 資料庫中,將不同的副本放置在不同的可用性區域(有效地將不同的建築物)。

事實上,Azure 的服務級別協定[(SLA)](https://azure.microsoft.com/support/legal/sla/)由由 Microsoft 管理的數據中心組成的全球網路提供支援,有助於使應用全天候運行。 Azure 平臺完全管理每個資料庫,它保證不丟失數據,並且數據可用性的百分比很高。 Azure 會自動處理修補、備份、複寫、失敗偵測，基礎潛在硬體、軟體或網路失敗、部署錯誤修正、容錯移轉、資料庫升級和其他維護工作。 隔離計算和儲存圖層可達成標準可用性。 高級可用性是通過將計算和存儲整合到單個節點上以進行性能,然後實現類似於"始終打開可用性組"的技術來實現的。 如需 Azure SQL Database 高可用性功能的完整討論，請參閱 [SQL Database 可用性](sql-database-high-availability.md)。 

此外,SQL 資料庫還提供內置[的業務連續性和全球可伸縮性](sql-database-business-continuity.md)功能。 其中包括：

- [自動備份](sql-database-automated-backups.md):

  SQL 資料庫自動執行 SQL 資料庫的完整、差異和事務日誌備份,使您能夠還原到任何時間點。 對於單個資料庫和池資料庫,可以將 SQL 資料庫配置為將完整的資料庫備份存儲到 Azure 存儲,以便長期保留備份。 對於受控執行個體，您也可以對僅複製備份執行長期備份保留。

- [時間點還原](sql-database-recovery-using-backups.md):

  所有 SQL 資料庫部署選項都支援在任何 SQL 資料庫的自動備份保留期內恢復到任何時間點。
- [主動式異地複寫](sql-database-active-geo-replication.md)：

  單個資料庫和池資料庫選項允許您在同一或全域分佈的 Azure 資料中心中配置最多四個可讀的輔助資料庫。 例如,如果具有具有大量併發唯讀事務的目錄資料庫的 SaaS 應用程式,請使用活動異地複製來啟用全域讀取縮放。 這將消除主資料庫由於讀取工作負載造成的瓶頸。 對於受控執行個體，請使用自動容錯移轉群組。
- [自動容錯移轉群組](sql-database-auto-failover-group.md)：

  所有 SQL 資料庫部署選項都允許您使用故障轉移組實現全域級的高可用性和負載平衡。 這包括大型資料庫集、彈性池和託管實例的透明異地複製和故障轉移。 故障轉移組支援創建全域分散式 SaaS 應用程式,管理開銷最小。 這會使所有複雜的監視、路由和故障轉移業務流程留給 SQL 資料庫。
- [區域冗餘資料庫](sql-database-high-availability.md):

  SQL Database 可讓您跨多個可用性區域佈建進階或業務關鍵資料庫或彈性集區。 由於這些資料庫和彈性池具有多個冗餘副本,因此具有高可用性,因此將這些副本放入多個可用性區域可提供更高的恢復能力。 這包括從資料中心規模故障自動恢復的能力,而不會丟失數據。

## <a name="built-in-intelligence"></a>內建智慧

借助 SQL 資料庫,您可以獲得內建智慧,説明您顯著降低運行和管理資料庫的成本,並最大限度地提高應用程式的性能和安全性。 SQL 資料庫全天候運行數百萬個客戶工作負載,收集和處理大量遙測數據,同時充分尊重客戶隱私。 各種演算法持續評估遙測數據,以便服務能夠學習和適應您的應用程式。

### <a name="automatic-performance-monitoring-and-tuning"></a>自動的效能監視和微調

SQL Database 會提供您需要監視之查詢的詳細解析。 SQL 資料庫瞭解資料庫模式,並使您能夠根據工作負載調整資料庫架構。 SQL Database 提供[效能微調建議](sql-database-advisor.md)，您可以在其中檢閱微調動作並加以套用。

但是,持續監視資料庫是一項艱巨而乏味的任務,尤其是在處理許多資料庫時。 [智慧見解](sql-database-intelligent-insights.md)通過自動監視 SQL 資料庫大規模性能來為您執行此工作。 它通知您性能降級問題,識別每個問題的根本原因,並盡可能提供性能改進建議。

即使 SQL 資料庫和 Azure 提供的所有可用工具和報告,管理大量資料庫也可能無法高效完成。 您可以考慮使用[自動調優](sql-database-automatic-tuning.md)將一些監視和調優操作委託給 SQL 資料庫,而不是手動監視和調整資料庫。 SQL 資料庫會自動應用建議、測試和驗證其每個調優操作,以確保性能不斷提高。 這樣,SQL 資料庫會自動以受控和安全的方式適應您的工作負載。 自動調優意味著在每個調優操作之前和之後仔細監視和比較資料庫的性能。 如果性能沒有提高,則調優操作將恢復。

在 SQL 資料庫上運行[SaaS 多租戶應用程式的](sql-database-design-patterns-multi-tenancy-saas-applications.md)許多合作夥伴都依賴於自動性能調整,以確保其應用程式始終具有穩定且可預測的性能。 對他們而言，這項功能可大幅降低夜間發生效能事件的風險。 此外,由於部分客戶群也使用 SQL Server,因此他們使用 SQL 資料庫提供的相同索引建議來説明其 SQL Server 客戶。

[SQL 資料庫中提供了](sql-database-automatic-tuning.md)兩個自動調優方面:

- **自動索引管理**：識別應該在資料庫中新增的索引，以及應該移除的索引。
- **自動計劃更正**:識別有問題的計劃並修復 SQL 計劃性能問題。

### <a name="adaptive-query-processing"></a>自適性查詢處理

可以使用[自適應查詢處理](/sql/relational-databases/performance/intelligent-query-processing),包括多語句表值函數的交錯執行、批處理模式記憶體授予反饋和批處理模式自適應聯接。 這些自適應查詢處理功能中的每一個都應用了類似的"學習和適應"技術,有助於進一步解決與歷史上難以解決的查詢優化問題相關的性能問題。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

SQL 資料庫提供一系列[內建的安全性和合規性功能](sql-database-security-overview.md),可説明您的應用程式滿足各種安全性和合規性要求。

> [!IMPORTANT]
> Microsoft 已根據許多合規性標準認證 Azure SQL 資料庫(所有部署選項)。 有關詳細資訊,請參閱 Microsoft [Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942),您可以在其中找到 SQL 資料庫合規性認證的最最新清單。

### <a name="advance-threat-protection"></a>預先威脅保護

進階資料安全性是進階 SQL 安全性功能的整合套件。 它包括用於發現和分類敏感數據、管理資料庫漏洞以及檢測可能指示資料庫受到威脅的異常活動的功能。 此套件可讓您從單一點位置啟用及管理前述功能。

- [資料發現與類別](sql-database-data-discovery-and-classification.md):

  此功能提供 Azure SQL 資料庫中內建的功能,用於發現、分類、標記和保護資料庫中的敏感數據。 它提供了對資料庫分類狀態的可見性,並跟蹤對資料庫中及其邊界以外敏感數據的訪問。
- [漏洞評估](sql-vulnerability-assessment.md):

  這個服務可以探索、追蹤並協助您修復潛在的資料庫弱點。 它可讓您檢視安全性狀態，且包含解決安全性問題和增強資料庫防護性的可行步驟。
- [威脅偵測](sql-database-threat-detection.md):

  此功能可檢測異常活動,這些異常活動指示訪問或利用資料庫的異常和潛在有害嘗試。 它會持續監視您的資料庫是否有可疑的活動，並在發現潛在弱點、SQL 插入式攻擊和異常資料庫存取模式時提供即時安全性警示。 威脅檢測警報提供可疑活動的詳細資訊,並建議有關如何調查和減輕威脅的操作。

### <a name="auditing-for-compliance-and-security"></a>合規性和安全性稽核

[稽核](sql-database-auditing.md)會追蹤資料庫事件，並將它們寫入至您 Azure 儲存體帳戶中的稽核記錄。 稽核可協助您保持法規合規性、了解資料庫活動，以及取得可能指出商務考量或疑似安全違規的不一致和異常見解。

### <a name="data-encryption"></a>資料加密

SQL 資料庫通過提供加密幫助保護您的數據。 對於動態資料,它使用[傳輸層安全性](https://support.microsoft.com/kb/3135244)。 對於靜態資料,它使用[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。 對於正在使用的資料,它使用[始終加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)的 。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

SQL Database 可讓您透過 [Azure Active Directory 整合](sql-database-aad-authentication.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure 活動目錄支援[多重身份驗證](sql-database-ssms-mfa-authentication.md),以提高數據和應用程式安全性,同時支援單個登錄過程。

## <a name="easy-to-use-tools"></a>容易使用

SQL Database 讓應用程式的建置及維護更簡易也更有生產力。 SQL Database 讓您將精力集中於拿手項目：建置絕佳的應用程式。 您可以使用已有的工具和技能在 SQL 資料庫中管理和開發。

- [Azure 門戶](https://portal.azure.com/):

  用於管理所有 Azure 服務的基於 Web 的應用程式。
- [SQL 伺服器管理工作室](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  一個免費的、可下載的用戶端應用程式,用於管理從 SQL Server 到 SQL 資料庫的任何 SQL 基礎結構。
- [Visual Studio 中的 SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)：

  用於開發 SQL Server 關係資料庫、SQL 資料庫、整合服務套件、分析服務資料模型和報告服務報告的免費可下載用戶端應用程式。
- [視覺工作室碼](https://code.visualstudio.com/docs):

  適用於 Windows、macOS 和 Linux 的免費、可下載的開原始程式碼編輯器。 它支援延伸,包括用於查詢 Microsoft SQL Server、Azure SQL 資料庫和 Azure SQL 資料主目錄的[mssql 延伸](https://aka.ms/mssql-marketplace)。

SQL 資料庫支援在 macOS、Linux 和 Windows 上使用 Python、Java、Node.js、PHP、Ruby 和 .NET 構建應用程式。 SQL Database 支援與 SQL Server 相同的[連線庫](sql-database-libraries.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL 資料庫常見問題

### <a name="what-is-the-current-version-of-sql-database"></a>最新的 SQL Database 版本為何？

最新的 SQL Database 版本是 V12。 版本 V11 已被淘汰。

### <a name="can-i-control-when-patching-downtime-occurs"></a>我可以控制何時進行修補停機時間嗎?

否。 如果您在應用程式中[採用重試邏輯](sql-database-develop-overview.md#resiliency)，修補的影響通常不明顯。 有關詳細資訊,請參閱[Azure SQL 資料庫中的 Azure 維護事件規劃](sql-database-planned-maintenance.md)。

### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit 問題

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>適用於 SQL Server 的 Azure Hybrid Benefit 是否有雙重使用權？

對於此授權，您具有 180 天的雙重使用權，以確保移轉能順暢地執行。 在 180 天期間之後,您只能在 SQL 資料庫中的雲端中使用 SQL Server 許可證。 您不再在本地和雲端中具有雙重使用許可權。

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>適用於 SQL Server 的 Azure Hybrid Benefit 與授權行動性有何不同？

通過軟體保障,我們為 SQL Server 客戶提供許可證行動優勢。 這允許將其許可證重新分配到合作夥伴的共享伺服器。 您可以在 Azure IaaS 和 AWS EC2 上使用此優勢。

適用於 SQL Server 的 Azure Hybrid Benefit 主要在以下兩方面與授權行動性有所差異：

- 它提供將高度虛擬化工作負載移轉至 Azure 的經濟效益。 SQL Server 企業版客戶可以在 Azure 中為其擁有的每個核心在本地獲得四個核心,用於高度虛擬化的應用程式。 許可證移動性不允許將虛擬化工作負載遷移到雲的任何特殊成本優勢。
- 它為 Azure 上與本地 SQL Server 高度相容的 PaaS 目標提供了該目標。

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>適用於 SQL Server 的 Azure Hybrid Benefit 有哪些明確的權利？

SQL 資料庫客戶具有以下權限,這些權限與 SQL Server 的 Azure 混合優勢相關聯:

|使用空間|SQL Server 的 Azure 混合優勢為您帶來什麼?|
|---|---|
|具有 SA 的 SQL Server Enterprise Edition 核心客戶|<li>可以根據通用或業務關鍵型 SKU 支付基本費率</li><br><li>1 個內部部署核心 = 一般目的 SKU 中的 4 個核心</li><br><li>1 個內部部署核心 = 業務關鍵 SKU 中的 1 個核心</li>|
|具有 SA 的 SQL Server Standard Edition 核心客戶|<li>只能根據通用 SKU 支付基本費率</li><br><li>1 個內部部署核心 = 一般目的 SKU 中的 1 個核心</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>洽詢 SQL Server 工程團隊

- [DBA 堆疊交換](https://dba.stackexchange.com/questions/tagged/sql-server):詢問資料庫管理問題。
- [堆疊溢出](https://stackoverflow.com/questions/tagged/sql-server):詢問開發問題。
- [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): 提出技術問題。
- [回饋](https://aka.ms/sqlfeedback):報告錯誤和請求功能。
- [雷迪特](https://www.reddit.com/r/SQLServer/):討論 SQL 伺服器。

## <a name="next-steps"></a>後續步驟

- 有關單個資料庫和彈性池的成本比較和計算機,請參閱[定價頁](https://azure.microsoft.com/pricing/details/sql-database/)。
- 請參考以下快速入門:

  - [在 Azure 入口網站中建立 SQL Database](sql-database-single-database-get-started.md)  
  - [使用 Azure CLI 建立 SQL Database](sql-database-get-started-cli.md)
  - [使用 PowerShell 建立 SQL Database](sql-database-get-started-powershell.md)

- 如需一組 Azure CLI 和 PowerShell 範例，請參閱︰
  - [SQL Database 的 Azure CLI 範例](sql-database-cli-samples.md)
  - [SQL Database 的 Azure PowerShell 範例](sql-database-powershell-samples.md)

- 有關新功能發佈時的資訊,請參閱[SQL 資料庫的 Azure 路線圖](https://azure.microsoft.com/roadmap/?category=databases)。
- 請參閱[Azure SQL 資料庫部落格](https://azure.microsoft.com/blog/topics/database),其中 SQL Server 產品團隊成員會部落格有關 SQL 資料庫新聞和功能。

