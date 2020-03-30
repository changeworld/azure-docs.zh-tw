---
title: 高級 RS 服務層停用
description: 進階 RS 服務層級已進入淘汰階段，支援也將終止 - 請參閱移轉選項。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821034"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database 進階 RS 服務層級 (預覽狀態) 進入淘汰階段 - 移轉選項

2018 年 2 月，Microsoft 宣佈 Azure SQL Database 中的進階 RS 服務層級不再發行供一般使用，並自 2019 年 1 月 31 日起停止支援。 停止支援的期限已延後至 2019 年 6 月 30 日。 這篇文章說明從進階 RS 服務層級移轉至另一個服務層級的選項。 2019 年 6 月 30 日後，Microsoft 會自動將進階 RS 資料庫移轉到最符合進階 RS 資料庫效能需求的一般可用服務層級。

以下的移轉目的地與定價選項適用於進階 RS 客戶：

- 虛擬核心服務層級

  [以虛擬核心為基礎購買模型](sql-database-service-tiers-vcore.md)中的**一般用途**與**業務關鍵**服務層級。 這兩個服務層級可供一般使用。 基於 vCore 的採購模型還提供**超大規模**服務層，通過每個資料庫自動擴展至 100 TB，可按需適應工作負載的需求。 超大規模資料庫的服務層提供相當於[以 DTU 為基礎購買模型](sql-database-service-tiers-dtu.md)中的進階服務層，價格更接近進階 RS 服務層。
- 開發/測試定價

  [開發/測試定價](https://azure.microsoft.com/pricing/dev-test/)可讓 Visual Studio 訂用帳戶省下高達 55% 的隨附授權費率。
- Azure Hybrid Benefit 及保留容量定價

  [Azure Hybrid Benefit 及保留容量定價](https://azure.microsoft.com/pricing/details/sql-database/)可省下高達 80% 的隨附授權費率。 如需有關這些選項的詳細資訊，請參閱[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 和 [Azure SQL Database 保留的容量](sql-database-reserved-capacity.md)。

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>立即採取行動將您的進階 RS 資料庫移轉至替代的 SQL Database 服務層級

請參閱本文的指導方針，及我們的定價與說明文件，來判定您進階 RS 工作負載的正確移轉目的地。

## <a name="migrate-compute-intensive-workloads-and-save"></a>移轉計算密集型的工作負載與節省

對於計算密集型的進階 RS 工作負載，建議移轉至我們供一般使用的以虛擬核心為基礎一般用途服務層級，並藉由使用 SQL Server 適用的 Azure Hybrid Benefit 與保留的容量供應項目，省下更多的隨附授權費率。 如果您要維持以 DTU 為基礎購買選項，可將計算密集型進階 RS 資料庫移轉至標準服務層級，與進階 RS 一般可用性定價相比，這仍可以節省費用 (如果已移至一般可用性)。

> [!WARNING]
> 將您的進階 RS 工作負載移轉至以 DTU 為基礎進階服務層級，會增加目前進階 RS 定價的月費。 建議您考量使用具有 Azure Hybrid Benefit 與保留容量定價的超大規模資料庫或業務關鍵層，以保持類似或低於進階 RS 的費用。

### <a name="premium-rs-databases"></a>進階 RS 資料庫

|**如果您目前使用的是…**|**移轉至可相比的以虛擬核心為基礎…**|**移轉至可相比的以 DTU 為基礎…**|
|---|---|---|
|進階 RS 1|一般用途 1 虛擬核心 (第 4 代)|標準 3|
|進階 RS 2|一般用途 2 虛擬核心 (第 4 代)|標準 4|
|進階 RS 4|一般用途 4 虛擬核心 (第 4 代)|標準 6|
|進階 RS 6|一般用途 6 虛擬核心 (第 4 代)|標準 7|

### <a name="premium-rs-pools"></a>進階 RS 集區

|**如果您目前使用的是…**|**移轉至可相比的以虛擬核心為基礎…**|**移轉至可相比的以 DTU 為基礎…**|
|---|---|---|
|進階 RS 集區 125 DTU|一般用途 1 虛擬核心 (第 4 代)|標準集區 100 eDTU|
|進階 RS 集區 250 DTU|一般用途 2 虛擬核心 (第 4 代)|標準集區 250 eDTU|
|進階 RS 集區 500 DTU|一般用途 4 虛擬核心 (第 4 代)|標準集區 500 eDTU|
|進階 RS 集區 1000 DTU|一般用途 8 虛擬核心 (第 4 代)|標準集區 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>讓 IO 密集型工作負載最節省並得到最佳效能

建議將您的 IO 密集型單一資料庫移轉至我們以虛擬核心為基礎的超大規模資料庫負載層 (目前為預覽狀態)，並將您的 IO 密集型資料庫集區移轉至我們的一般可用業務關鍵層，以得到最佳的效能與成本。  下列以虛擬核心為基礎的選項會保持或改善您目前的效能，當與 Azure Hybrid Benefit 和保留容量定價結合使用時，可讓您節省成本。

|**如果您目前使用的是…**|**移轉至可相比的以虛擬核心為基礎…**|**移轉至可相比的以 DTU 為基礎…**|
|---|---|---|
|進階 RS 1| 超大規模 1 vCore （Gen4） 或業務關鍵 1 vCore （Gen4）|進階 1|
|進階 RS 2| 超大規模 2 vCore （Gen4） 或業務關鍵型 2 vCore（第 4 代）|進階 2|
|進階 RS 4| 超大規模 4 vCore （Gen4） 或業務關鍵 4 vCore （Gen4）|進階 4
|進階 RS 6| 超大規模 6 vCore （Gen4） 或業務關鍵 6 vCore （Gen4）|進階 6|

|**如果您目前使用的是…**|**移轉至可相比的以虛擬核心為基礎…**|**移轉至可相比的以 DTU 為基礎…**|
|---|---|---|
|進階 RS 集區 125 DTU|業務關鍵 2 虛擬核心 (第 4 代)|進階集區 125 eDTU|
|進階 RS 集區 250 DTU|業務關鍵 2 虛擬核心 (第 4 代)|進階集區 250 eDTU|
|進階 RS 集區 500 DTU|業務關鍵 4 虛擬核心 (第 4 代)|進階集區 500 eDTU|
|進階 RS 集區 1000 DTU|業務關鍵 8 虛擬核心 (第 4 代)|進階集區 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>利用我們的新優惠

我們在以虛擬核心為基礎購買模型中的服務層級符合優惠資格，可讓您省下 80% 的隨附授權定價。 將 SQL Server Standard 或 Enterprise 版授權搭配有效的軟體保證使用，可透過 [SQL Server 適用的 Azure Hybrid Benefit ](https://azure.microsoft.com/pricing/hybrid-benefit/)省下 55% 的隨附授權定價。 您可以將 Hybrid Benefit 搭配 [Azure SQL Database 保留容量](sql-database-reserved-capacity.md)定價使用，當您將承諾提前到一或三年期時，可節省達 80%。  立即從 Azure 入口網站啟用這兩項權益。

如果您有任何有關此變更的問題或疑慮，或如果您需要移轉方面的協助，請連絡 [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>從進階 RS 服務層級移轉至 DTU 或虛擬核心模型中的服務層級

### <a name="migration-of-a-database"></a>資料庫移轉

將資料庫從進階 RS 服務層級移轉至 DTU 或虛擬核心模型中的服務層級，類似在進階 RS 服務層級的之間升級或降級。

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>使用資料庫複製將進階 RS 資料庫轉換成以 DTU 為基礎或以 vCore 為基礎的資料庫

您可以將任何資料庫 (具有進階 RS 計算大小) 複製到資料庫 (具有以 DTU 或虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫複製會在複製作業開始時建立資料的快照集，而且不會在來源和目標之間執行資料同步。

## <a name="next-steps"></a>後續步驟

- 如需特定計算大小的詳細資訊和單一資料庫可用的儲存體大小選項，請參閱[單一資料庫 SQL Database 以虛擬核心為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)
- 有關可用於彈性池的特定計算大小和存儲大小選擇的詳細資訊，請參閱[彈性池的 SQL 資料庫 vCore 資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
