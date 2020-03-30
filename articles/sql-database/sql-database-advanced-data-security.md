---
title: 進階資料安全性
description: 了解探索及分類敏感性資料、管理資料庫弱點，以及偵測可能表示對 Azure SQL 資料庫有威脅之異常活動的功能。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: 1f0e6694e596dc60264dfe0789a2f80090e0da3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269129"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Azure SQL Database 的進階資料安全性

進階資料安全性是進階 SQL 安全性功能的整合套件。 其中包含的功能可用於探索和分類敏感性資料、找出潛在資料庫弱點並減低其風險，及偵測可能指出資料庫遇到威脅的異常活動。 此套件可讓您從單一的進入點位置啟用及管理前述功能。

## <a name="overview"></a>總覽

高級資料安全 （ADS） 提供一組高級 SQL 安全功能，包括資料發現&分類、漏洞評估和高級威脅防護。

- [資料發現&分類](sql-database-data-discovery-and-classification.md)提供了 Azure SQL 資料庫中內置的功能，用於發現、分類和標記&保護資料庫中的敏感性資料。 它可用來讓您檢視資料庫分類狀態、追蹤對資料庫內敏感性資料的存取，並具有其他多方面的用途。
- [弱點評估](sql-vulnerability-assessment.md)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它可讓您檢視安全性狀態，且包含解決安全性問題和增強資料庫防護性的可行步驟。
- [進階威脅防護](sql-database-threat-detection-overview.md)偵測到異常活動，即表示有不尋常及可能有害的活動，試圖存取或惡意探索您的資料庫。 它會持續監視您的資料庫是否有可疑的活動，並在發現潛在弱點、SQL 插入式攻擊和異常資料庫存取模式時提供即時安全性警示。 進階威脅防護警示會提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。

只要啟用 SQL ADS，其中包含的所有功能即會一併啟用。 只要按一下按鍵，即可對 SQL Database 伺服器或受控執行個體上的所有資料庫啟用 ADS。 啟用或管理 ADS 設置需要屬於[SQL 安全管理器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)角色、SQL 資料庫管理員角色或 SQL 伺服器管理員角色。 

ADS 的定價與 Azure 資訊安全中心標準層相同，每個受保護的 SQL Database 伺服器或受控執行個體算作一個節點。 新的受保護資源適用資訊安全中心標準層的免費試用。 如需詳細資訊，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="getting-started-with-ads"></a>開始使用 ADS

下列步驟可讓您開始使用 ADS。

## <a name="1-enable-ads"></a>1. 啟用 ADS

通過在 SQL 資料庫伺服器或託管實例**的安全**標題下導航到**高級資料安全**，啟用 ADS。 若要對資料庫伺服器或受控執行個體上的所有資料庫啟用 ADS，請按一下 [在伺服器上啟用進階資料安全性]****。

> [!NOTE]
> 將自動創建和配置存儲帳戶以存儲**漏洞評估**掃描結果。 如果您已經為同一資源組和地區中的另一台伺服器啟用了 ADS，則將使用現有的存儲帳戶。

![啟用 ADS](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> ADS 的費用與每個節點的 Azure 資訊安全中心標準層費用相同，其中一個節點代表整個 SQL Database 伺服器或受控執行個體。 因此，您只需付費一次，即可使用 ADS 保護資料庫伺服器或受控執行個體上的所有資料庫。 您可以先以免費試用版試用 ADS。

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. 開始對資料進行分類、跟蹤漏洞和調查威脅警報

按一下 [資料探索與分類]**** 卡可查看建議要分類的敏感性資料行，以及使用持續敏感性標籤為資料分類。 按一下 [弱點評估]**** 卡可檢視和管理弱點掃描和報告，以及追蹤您的安全性水準。 如果已收到安全警報，請按一下**高級威脅保護**卡以查看警報的詳細資訊，並通過 Azure 安全中心安全警報頁面查看有關 Azure 訂閱中所有警報的整合報告。

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. 管理 SQL 資料庫伺服器或託管實例上的 ADS 設置

若要檢視及管理 ADS 設定，請針對您的 SQL Database 伺服器或受控執行個體，瀏覽至 [安全性]**** 標題下的 [進階資料安全性]****。 在此頁上，您可以啟用或禁用 ADS，並修改整個 SQL 資料庫伺服器或託管實例的漏洞評估和高級威脅保護設置。

![伺服器設定](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. 管理 SQL 資料庫的 ADS 設置

若要覆寫特定資料庫的 ADS 設定，請勾選 [啟用資料庫層級的進階資料安全性]**** 核取方塊。 僅當您有特定要求為各個資料庫接收單獨的高級威脅保護警報或漏洞評估結果，以代替或除了為資料庫伺服器或託管實例。

選中該核取方塊後，即可配置此資料庫的相關設置。
 
![資料庫和高級威脅防護設置](./media/sql-advanced-protection/database_threat_detection_settings.png) 

從 ADS 資料庫窗格，也可以觸達資料庫伺服器或受控執行個體的進階資料安全性設定。 按一下主要 ADS 窗格中的 [設定]****，然後按一下 [檢視進階資料安全性伺服器設定]****。 

![資料庫設定](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>後續步驟 

- 深入了解[資料探索與分類](sql-database-data-discovery-and-classification.md) 
- 深入了解[弱點評估](sql-vulnerability-assessment.md) 
- 瞭解有關[高級威脅防護的更多](sql-database-threat-detection.md)
- 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)詳細資訊
