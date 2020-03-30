---
title: 什麼是單個資料庫
description: 了解 Azure SQL Database 中的單一資料庫
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500752"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>什麼是 Azure SQL Database 中的單一資料庫

單一資料庫部署選項會使用其本身的資源集在 Azure SQL Database 中建立資料庫，並透過 SQL Database 伺服器進行管理。 使用單一資料庫時，每個資料庫會彼此隔離並且可攜，且各自在[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)或[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)內有其本身的服務層級和保證的計算大小。

> [!IMPORTANT]
> 單一資料庫是 Azure SQL Database 的三個部署選項之一。 另外兩個選項是[彈性集區](sql-database-elastic-pool.md)和[受控執行個體](sql-database-managed-instance.md)。
> [!NOTE]
> 如需 Azure SQL Database 的詞彙解釋，請參閱 [SQL 資料庫術語詞彙](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>動態延展性

您可以在無伺服器計算層中的低成本或預配計算層中的小計算大小在小型單個資料庫上構建第一個應用。 您可以隨時手動或以程式設計方式更改[計算或服務層](sql-database-single-database-scale.md)，以滿足解決方案的需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求，並且讓您只需支付您所需的資源費用。

## <a name="single-databases-and-elastic-pools"></a>單一資料庫和彈性集區

單一資料庫可移入或移出[彈性集區](sql-database-elastic-pool.md)以進行資源共用。 對於許多企業和應用程式而言，只要能夠建立單一資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 彈性集區的設計可解決此問題。 概念很簡單。 您可將效能資源配置到集區，而非個別的資料庫，並支付該集區的集體效能資源，而非單一資料庫效能的費用。

## <a name="monitoring-and-alerting"></a>監視和警示

您可以使用內置[的效能監控](sql-database-performance-guidance.md)和[警報工具](sql-database-insights-alerts-portal.md)，以及性能評級。 使用這些工具，您可以根據目前或專案的效能需求快速評估相應增加或減少的影響。 此外，SQL 資料庫可以[發出指標和資源日誌](sql-database-metrics-diag-logging.md)，以便更輕鬆地進行監視。

## <a name="availability-capabilities"></a>可用性功能

單個資料庫、彈性池和託管實例都提供了許多可用性特徵。 如需相關資訊，請參閱[可用性特性](sql-database-technical-overview.md#availability-capabilities)。

## <a name="transact-sql-differences"></a>Transact-SQL 差異

Microsoft SQL Server 和 Azure SQL Database 都支援應用程式使用的大部分 Transact-SQL 功能。 例如，資料類型、運算子、字串、算術、邏輯及資料指標函式等核心 SQL 元件在 SQL Server 與 SQL Database 中都以相同的方式運作。 不過，DDL (資料定義語言) 和 DML (資料操作語言) 元素中有幾個 T-SQL 差異導致對 T-SQL 陳述式和查詢僅提供部分支援 (將在本文章中稍後探討)。
此外，還有一些功能和語法完全不受支援，因為 Azure SQL Database 的設計目的是要將功能隔離，讓它們不與 master 資料庫和作業系統相依。 因此，大多數伺服器層級活動都不適用於 SQL Database。 如果它們設定伺服器層級選項、作業系統元件或指定檔案系統組態，便無法使用 T-SQL 陳述式和選項。 當需要這類功能時，通常會從 SQL Database 或從另一個 Azure 功能或服務以其他方式提供適合的替代方案。

如需詳細資訊，請參閱[解決移轉至 SQL Database 期間的 Transact-SQL 差異](sql-database-transact-sql-information.md)。

## <a name="security"></a>安全性

SQL Database 提供了多種[內建安全性與合規性](sql-database-security-overview.md)功能，可協助您的應用程式符合各種安全性與合規需求。

> [!IMPORTANT]
> Azure SQL 資料庫（所有部署選項）已根據許多合規性標準進行了認證。 有關詳細資訊，請參閱 Microsoft [Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在其中找到最新的 SQL 資料庫合規性認證清單。

## <a name="next-steps"></a>後續步驟

- 要快速開始使用單個資料庫，請從["單個資料庫快速入門手冊"開始](sql-database-single-database-quickstart-guide.md)。
- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 有關受支援功能的資訊，請參閱[功能](sql-database-features.md)。
