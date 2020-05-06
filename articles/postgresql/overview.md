---
title: 「適用於 PostgreSQL 的 Azure 資料庫」關聯式資料庫服務的概觀。
description: 提供 Azure Database for PostgreSQL 關聯式資料庫服務的概觀。
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "74481669"
---
# <a name="what-is-azure-database-for-postgresql"></a>什麼是 Azure Database for PostgreSQL？
適用於 PostgreSQL 的 Azure 資料庫是 Microsoft 雲端中專為開發人員建置的關聯式資料庫服務。 此服務是以開放原始碼 [PostgreSQL](https://www.postgresql.org/) 資料庫引擎的社群版本作為基礎，可供用於兩種部署選項：單一伺服器和超大規模 (Citus)。

## <a name="azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
單一伺服器部署選項可提供：

- 內建[高可用性](concepts-high-availability.md)但沒有任何額外成本 (99.99% SLA)
- 可預測的效能，使用隨用隨付定價方式
- 在幾秒內就能完成的[隨需垂直調整](concepts-pricing-tiers.md)
- [監視和警示](concepts-monitoring.md)以評估您的伺服器
- 企業級安全性與合規性
- [受到保護以保護](concepts-security.md)機密的待用資料和移動中資料
- 最多 35 天的[自動備份和指定時間點還原](concepts-business-continuity.md)


上述所有功能幾乎不需要管理，而且免費提供。 這些功能可讓您專注於快速開發應用程式及加快上市時間，而不是將寶貴的時間和資源耗費在管理虛擬機器和基礎結構上。 您可以繼續使用所選擇的開放原始碼工具及平台來開發應用程式，而不需要學習新技能。

單一伺服器部署選項提供三個定價層：「基本」、「一般用途」及「記憶體最佳化」。 每一層提供不同的資源功能，來支援您的資料庫工作負載。 您可以在小型資料庫中建置第一個應用程式，一個月只需少許花費，然後調整規模以滿足解決方案的需求。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求。 您只需支付您所需的資源費用。 如需詳細資訊，請參閱 [定價層](concepts-pricing-tiers.md)。

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫 - Hyperscale (Citus)
超大規模 (Citus) 選項會使用分區化，水平調整多部電腦上的查詢。 其查詢引擎可平行處理從這些伺服器傳入的 SQL 查詢，以提升大型資料集的回應速度。 其可為需要更大規模和效能的應用程式提供服務，這些工作負載通常有逼近或超過 100 GB 的資料。

超大規模 (Citus) 部署選項可提供：

- 使用分區化的跨多部電腦水平調整
- 跨這些伺服器的查詢平行處理能力，以提升大型資料集的回應速度
- 多租用戶應用程式、即時作業分析和高輸送量交易式工作負載的絕佳支援

針對 PostgreSQL 所建置的應用程式可以在超大規模 (Citus) 上執行分散式查詢，只要有標準[連線程式庫](./concepts-connection-libraries.md)並進行些許變更即可。

## <a name="contacts"></a>連絡人
若您對適用於 PostgreSQL 的 Azure 資料庫有任何疑問或建議，請傳送電子郵件給適用於 PostgreSQL 的 Azure 資料庫小組 ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com))。 此地址僅供提問一般問題，不可用來傳送支援票證。

此外，請根據您的情況考量下列連絡要點：
- 若要連絡 Azure 支援或修正帳戶問題，[請從 Azure 入口網站提出票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要提供意見反應或要求新功能，請透過 [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) 建立項目。

## <a name="next-steps"></a>後續步驟
- 如需成本比較和計算機，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。
- 從建立第一個適用於 PostgreSQL 的 Azure 資料庫[單一伺服器](./quickstart-create-server-database-portal.md)或[超大規模 (Citus)](./quickstart-create-hyperscale-portal.md) 開始
- 使用 Python、PHP、Ruby、C\#、Java、Node.js 建置您的第一個應用程式：[連線程式庫](./concepts-connection-libraries.md)
