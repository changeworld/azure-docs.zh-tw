---
title: 什麼是適用於 PostgreSQL 的 Azure 資料庫
description: 在彈性伺服器內容中提供適用於 PostgreSQL 的 Azure 資料庫關聯式資料庫服務概觀。
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491320"
---
# <a name="what-is-azure-database-for-postgresql"></a>什麼是適用於 PostgreSQL 的 Azure 資料庫？

適用於 PostgreSQL 的 Azure 資料庫是 Microsoft 雲端中以 [PostgreSQL Community Edition](https://www.postgresql.org/) \(英文\) (可供 GPLv2 授權使用) 資料庫引擎為基礎的關聯式資料庫服務。 Azure Database for PostgreSQL 提供：

- 內建高可用性。
- 最多 35 天的自動備份和指定時間點還原資料保護。
- 自動維護基礎硬體、作業系統和資料庫引擎，讓服務保持在安全和最新狀態。
- 可預測的效能，使用隨用隨付計價方式。
- 在幾秒內進行彈性調整。
- 企業級安全性和領先業界的合規性，可保護待用和移動中的機密資料。
- 監視和自動化功能，簡化大規模部署的管理和監視。
- 領先業界的支援經驗。

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫":::

這些功能幾乎不需要管理，而且免費提供。 這些功能可讓您專注於快速開發應用程式及加快上市時間，而不是將寶貴的時間和資源耗費在管理虛擬機器和基礎結構上。 此外，您可以使用開放原始碼工具與選擇的平台繼續開發應用程式，並提供企業所需的速度和效率而不需要學習新技能。

## <a name="deployment-models"></a>部署模型

由 PostgreSQL 社群版本提供的適用於 PostgreSQL 的 Azure 資料庫有三種部署模式：

- 單一伺服器
- 彈性伺服器 (預覽)
- 超大規模資料庫 (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器

適用於 PostgreSQL 的 Azure 資料庫單一伺服器是完全受控的資料庫服務，具有自訂資料庫的最低需求。 單一伺服器平台的設計訴求是處理大部分的資料庫管理功能，例如修補、備份、高可用性，並透過最少的使用者組態和控制帶來安全性。 此架構針對內建高可用性進行最佳化，提供達 99.99% 的單一可用性區域可用性。 同時支援 PostgreSQL 9.5、9,6、10 和 11 社群版本。 本服務已在各個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/services/)中正式推出。

單一伺服器部署選項提供三個定價層：「基本」、「一般用途」及「記憶體最佳化」。 每一層提供不同的資源功能，來支援您的資料庫工作負載。 您可以在小型資料庫中建置第一個應用程式，一個月只需少許花費，然後調整規模以滿足解決方案的需求。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求。 您只需支付您所需的資源費用。 如需詳細資訊，請參閱[定價層](./concepts-pricing-tiers.md)。

雲端原生應用程式最適合使用單一伺服器處理自動修補，無需細部控管修補排程和自訂 PostgreSQL 組態設定。

如需單一伺服器部署模式的詳細概觀，請參閱[單一伺服器概觀](./overview-single-server.md)。

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器 (預覽)

適用於 PostgreSQL 的 Azure 資料庫彈性伺服器是完全受控的資料庫服務，其設計目的是要在資料庫管理功能和設定方面提供更細微的控制和彈性。 一般而言，此服務會根據使用者需求提供更多彈性和自訂選項。 彈性伺服器架構可讓使用者選擇在單一可用性區域和跨多個可用性區域中實現高可用性。 彈性伺服器提供更好的成本最佳化控制項，讓您能夠停止/啟動伺服器和高載計算層，非常適合不需要持續使用完整計算容量的工作負載。 此服務目前支援 PostgreSQL 11 和 12 社群版本，並計畫新增較新的版本。 本服務目前已在各個 Azure 區域中提供公開預覽版本。

彈性伺服器最適合

- 需要更佳的控制和自訂功能的應用程式開發。
- 可停止/啟動伺服器的成本最佳化控制項。
- 區域備援高可用性
- 可管理的維護期間
  
如需彈性伺服器部署模式的詳細概觀，請參閱[彈性伺服器概觀](./flexible-server/overview.md)。

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus)

超大規模 (Citus) 選項會使用分區化，水平調整多部電腦上的查詢。 其查詢引擎可平行處理從這些伺服器傳入的 SQL 查詢，以提升大型資料集的回應速度。 其可為需要更大規模和效能的應用程式提供服務，這些工作負載通常有逼近或超過 100 GB 的資料。

超大規模 (Citus) 部署選項可提供：

- 使用分區化的跨多部電腦水平調整
- 跨這些伺服器的查詢平行處理能力，以提升大型資料集的回應速度
- 多租用戶應用程式、即時作業分析和高輸送量交易式工作負載的絕佳支援
  
針對 PostgreSQL 所建置的應用程式可以在超大規模 (Citus) 上執行分散式查詢，只要有標準[連線程式庫](./concepts-connection-libraries.md)並進行些許變更即可。

## <a name="next-steps"></a>後續步驟

深入了解適用於 PostgreSQL 的 Azure 資料庫的三種部署模式，並根據需求選擇適當的選項。

- [單一伺服器](./overview-single-server.md)
- [彈性伺服器](./flexible-server/overview.md)
- 超大規模資料庫 (Citus)