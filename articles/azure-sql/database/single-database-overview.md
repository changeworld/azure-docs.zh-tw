---
title: 什麼是單一資料庫？
description: 瞭解 Azure SQL Database 中的單一資料庫資源類型。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84343314"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>什麼是 Azure SQL Database 中的單一資料庫？
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

單一資料庫資源類型會使用自己的資源集在 Azure SQL Database 中建立資料庫，並透過 [伺服器](logical-servers.md)進行管理。 使用單一資料庫時，每個資料庫都是獨立的，而且是可移植的。 每個都在以 [DTU 為基礎的購買模型](service-tiers-dtu.md) 或 [vCore 為基礎的購買模型](service-tiers-vcore.md) ，以及保證的計算大小內都有自己的服務層級。

> [!IMPORTANT]
> 單一資料庫是 Azure SQL Database 的一種資源類型。 另一個則是 [彈性](elastic-pool-overview.md)集區。

## <a name="dynamic-scalability"></a>動態延展性

您可以在無伺服器計算層級的小型單一資料庫中建立第一個應用程式，或在布建的計算層中以較小的計算大小來建立您的第一個應用程式。 您可隨時以手動或程式設計方式變更 [計算或服務層級](single-database-scale.md) ，以符合解決方案的需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求，並且讓您只需支付您所需的資源費用。

## <a name="single-databases-and-elastic-pools"></a>單一資料庫和彈性集區

單一資料庫可移入或移出[彈性集區](elastic-pool-overview.md)以進行資源共用。 對於許多企業和應用程式而言，只要能夠建立單一資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 彈性集區的設計可解決此問題。 概念很簡單。 您可將效能資源配置到集區，而非個別的資料庫，並支付該集區的集體效能資源，而非單一資料庫效能的費用。

## <a name="monitoring-and-alerting"></a>監視和警示

您可以使用內建的 [效能監視](performance-guidance.md) 和 [警示工具](alerts-insights-configure-portal.md)，並結合效能評等。 使用這些工具，您可以根據目前或專案的效能需求快速評估相應增加或減少的影響。 此外，SQL Database 可[發出計量和資源記錄](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)，以供更輕鬆地進行監視。

## <a name="availability-capabilities"></a>可用性功能

單一資料庫和彈性集區提供許多可用性特性。 如需相關資訊，請參閱[可用性特性](sql-database-paas-overview.md#availability-capabilities)。

## <a name="transact-sql-differences"></a>Transact-SQL 差異

Microsoft SQL Server 和 Azure SQL Database 都支援應用程式使用的大部分 Transact-SQL 功能。 例如，資料類型、運算子、字串、算術、邏輯及資料指標函式等核心 SQL 元件在 SQL Server 與 SQL Database 中都以相同的方式運作。 不過，DDL (資料定義語言) 和 DML (資料操作語言) 元素中有幾個 T-SQL 差異導致對 T-SQL 陳述式和查詢僅提供部分支援 (將在本文章中稍後探討)。

此外，也有一些不支援的功能和語法，因為 Azure SQL Database 是為了隔離 master 資料庫與作業系統相依性的功能而設計的。 因此，大多數伺服器層級活動都不適用於 SQL Database。 如果設定伺服器層級選項、設定作業系統元件或指定檔案系統設定，則無法使用 t-sql 語句和選項。 當需要這類功能時，通常會從 SQL Database 或從另一個 Azure 功能或服務以其他方式提供適合的替代方案。

如需詳細資訊，請參閱[解決移轉至 SQL Database 期間的 Transact-SQL 差異](transact-sql-tsql-differences-sql-server.md)。

## <a name="security"></a>安全性

SQL Database 提供各種內 [建的安全性與合規性](security-overview.md) 功能，可協助您的應用程式符合各種安全性與合規性需求。

> [!IMPORTANT]
> Azure SQL Database 已經過許多合規性標準的認證。 如需詳細資訊，請參閱 [Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可在當中找到 SQL Database 合規性認證的最新清單。

## <a name="next-steps"></a>接下來的步驟

- 若要快速開始使用單一資料庫，請從 [單一資料庫快速入門手冊](quickstart-content-reference-guide.md)開始。
- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](migrate-to-database-from-sql-server.md)。
- 如需支援功能的詳細資訊，請參閱 [功能](features-comparison.md)。
