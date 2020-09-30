---
title: 概觀 - 適用於 MySQL 的 Azure 資料庫
description: 了解適用於 MySQL 的 Azure 資料庫服務，這是 Microsoft 雲端中以 MySQL Community Edition 為基礎的關聯式資料庫服務。
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 51c194ca9b091bc685f293320750da55925ad49d
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565610"
---
# <a name="what-is-azure-database-for-mysql"></a>什麼是 Azure Database for MySQL？

適用於 MySQL 的 Azure 資料庫是 Microsoft 雲端中以 [MySQL Community Edition](https://www.mysql.com/products/community/) \(英文\) (可供 GPLv2 授權使用) 資料庫引擎 5.6、5.7 及 8.0 版為基礎的關聯式資料庫服務。 Azure Database for MySQL 提供：

- 內建高可用性。
- 最多 35 天的自動備份和指定時間點還原資料保護。
- 自動維護基礎硬體、作業系統和資料庫引擎，讓服務保持在安全和最新狀態。
- 可預測的效能，使用隨用隨付計價方式。
- 在幾秒內進行彈性調整。
- 可停止/啟動伺服器的成本最佳化控制項。 
- 企業級安全性和領先業界的合規性，可保護待用和移動中的機密資料。
- 監視和自動化功能，簡化大規模部署的管理和監視。
- 領先業界的支援經驗。

這些功能幾乎不需要管理，而且免費提供。 這些功能可讓您專注於快速開發應用程式及加快上市時間，而不是將寶貴的時間和資源耗費在管理虛擬機器和基礎結構上。 此外，您可以使用開放原始碼工具與選擇的平台繼續開發應用程式，並提供企業所需的速度和效率而不需要學習新技能。

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Azure Database for MySQL 概念圖":::

## <a name="deployment-models"></a>部署模型

由 MySQL 社群版本提供的適用於 MySQL 的 Azure 資料庫有二種部署模式：
- 單一伺服器 
- 彈性伺服器 (預覽)
  
### <a name="azure-database-for-mysql---single-server"></a>適用於 MySQL 的 Azure 資料庫 - 單一伺服器

適用於 MySQL 的 Azure 資料庫單一伺服器是完全受控的資料庫服務，具有自訂資料庫的最低需求。 單一伺服器平台的設計訴求是處理大部分的資料庫管理功能，例如修補、備份、高可用性，並透過最少的使用者組態和控制帶來安全性。 此架構針對內建高可用性進行最佳化，提供達 99.99% 的單一可用性區域可用性。 同時支援 MySQL 5.6、5.7 和 8.0 社群版本。 本服務已在各個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/services/)中正式推出。

單一伺服器部署選項提供三個定價層：「基本」、「一般用途」及「記憶體最佳化」。 每一層提供不同的資源功能，來支援您的資料庫工作負載。 您可以在小型資料庫中建置第一個應用程式，一個月只需少許花費，然後調整規模以滿足解決方案的需求。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求。 您只需支付您所需的資源費用。 如需詳細資訊，請參閱[定價層](concepts-pricing-tiers.md)。

雲端原生應用程式最適合使用單一伺服器處理自動修補，無需細部控管修補排程和自訂 MySQL 組態設定。 

如需單一伺服器部署模式的詳細概觀，請參閱[單一伺服器概觀](single-server-overview.md)。

### <a name="azure-database-for-mysql---flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫 - 彈性伺服器 (預覽)

適用於 MySQL 的 Azure 資料庫彈性伺服器是完全受控的資料庫服務，其設計目的是要在資料庫管理功能和設定方面提供更細微的控制和彈性。 一般而言，此服務會根據使用者需求提供更多彈性和自訂選項。 彈性伺服器架構可讓使用者選擇在單一可用性區域和跨多個可用性區域中實現高可用性。 彈性伺服器提供更好的成本最佳化控制項，讓您能夠停止/啟動伺服器和高載計算層，非常適合不需要持續使用完整計算容量的工作負載。 此服務目前支援 MySQL 5.7 社群版本，並計畫新增較新的版本。 本服務目前已在各個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/services/)中提供公開預覽版本。

彈性伺服器最適合 
- 需要更佳的控制和自訂功能的應用程式開發。
- 區域備援高可用性
- 可管理的維護期間

如需彈性伺服器部署模式的詳細概觀，請參閱[彈性伺服器概觀](flexible-server/overview.md)。

## <a name="contacts"></a>連絡人
若您對適用於 MySQL 的 Azure 資料庫有任何疑問或需要建議，請傳送電子郵件給適用於 MySQL 的 Azure 資料庫小組 ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))。 此電子郵件地址不是技術支援的別名。

此外，請根據您的情況考量下列連絡要點：

- 若要連絡 Azure 支援，[請從 Azure 入口網站提出票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修正您的帳戶問題，請在 Azure 入口網站中提出[支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供意見反應或要求新功能，請透過 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 建立項目。

## <a name="next-steps"></a>後續步驟

深入了解適用於 MySQL 的 Azure 資料庫的二種部署模式，並根據需求選擇適當的選項。

- [單一伺服器](single-server/index.yml)
- [彈性伺服器](flexible-server/index.yml)
- [選擇適用您工作負載的適當 MySQL 部署選項](select-right-deployment-type.md)
