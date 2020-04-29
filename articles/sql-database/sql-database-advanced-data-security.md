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
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80677459"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Azure SQL Database 的進階資料安全性

進階資料安全性是進階 SQL 安全性功能的整合套件。 其中包含的功能可用於探索和分類敏感性資料、找出潛在資料庫弱點並減低其風險，及偵測可能指出資料庫遇到威脅的異常活動。 此套件可讓您從單一的進入點位置啟用及管理前述功能。

## <a name="overview"></a>總覽

先進的資料安全性（ADS）提供一組先進的 SQL 安全性功能，包括資料探索 & 分類、弱點評估和先進的威脅防護。

- [資料探索 & 分類](sql-database-data-discovery-and-classification.md)提供 Azure SQL Database 的內建功能，可用於探索、分類、標記 & 報告您資料庫中的敏感性資料。 它可用來讓您檢視資料庫分類狀態、追蹤對資料庫內敏感性資料的存取，並具有其他多方面的用途。
- [弱點評估](sql-vulnerability-assessment.md)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它可讓您檢視安全性狀態，且包含解決安全性問題和增強資料庫防護性的可行步驟。
- [進階威脅防護](sql-database-threat-detection-overview.md)偵測到異常活動，即表示有不尋常及可能有害的活動，試圖存取或惡意探索您的資料庫。 它會持續監視您的資料庫是否有可疑的活動，並在發現潛在弱點、SQL 插入式攻擊和異常資料庫存取模式時提供即時安全性警示。 進階威脅防護警示會提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。

只要啟用 SQL ADS，其中包含的所有功能即會一併啟用。 只要按一下按鍵，即可對 SQL Database 伺服器或受控執行個體上的所有資料庫啟用 ADS。 啟用或管理 ADS 設定需要屬於[sql 安全性管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)角色、sql database 管理員角色或 sql server 系統管理員角色。 

ADS 的定價與 Azure 資訊安全中心標準層相同，每個受保護的 SQL Database 伺服器或受控執行個體算作一個節點。 新的受保護資源適用資訊安全中心標準層的免費試用。 如需詳細資訊，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="getting-started-with-ads"></a>開始使用 ADS

下列步驟可讓您開始使用 ADS。

## <a name="1-enable-ads"></a>1. 啟用 ADS

在 SQL Database 伺服器或受控實例的 [**安全性**] 標題底下，流覽至 [ **Advanced Data Security** ]，以啟用廣告。 若要對資料庫伺服器或受控執行個體上的所有資料庫啟用 ADS，請按一下 [在伺服器上啟用進階資料安全性]****。

> [!NOTE]
> 系統會自動建立並設定儲存體帳戶，以儲存您的**弱點評定**掃描結果。 如果您已針對相同資源群組和區域中的另一部伺服器啟用廣告，則會使用現有的儲存體帳戶。

![啟用 ADS](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> ADS 的費用與每個節點的 Azure 資訊安全中心標準層費用相同，其中一個節點代表整個 SQL Database 伺服器或受控執行個體。 因此，您只需付費一次，即可使用 ADS 保護資料庫伺服器或受控執行個體上的所有資料庫。 您可以先以免費試用版試用 ADS。

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. 開始分類資料、追蹤弱點，以及調查威脅警示

按一下 [資料探索與分類]**** 卡可查看建議要分類的敏感性資料行，以及使用持續敏感性標籤為資料分類。 按一下 [弱點評估]**** 卡可檢視和管理弱點掃描和報告，以及追蹤您的安全性水準。 如果已收到安全性警示，請按一下 [ **Advanced 威脅防護**] 卡片以查看警示的詳細資料，並透過 [Azure 資訊安全中心安全性警示] 頁面，查看您 Azure 訂用帳戶中所有警示的匯總報告。

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. 在 SQL Database 伺服器或受控實例上管理 ADS 設定

若要檢視及管理 ADS 設定，請針對您的 SQL Database 伺服器或受控執行個體，瀏覽至 [安全性]**** 標題下的 [進階資料安全性]****。 在此頁面上，您可以啟用或停用 ADS，以及修改整個 SQL Database 伺服器或受控實例的弱點評定和先進的威脅防護設定。

![伺服器設定](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. 管理 SQL 資料庫的 ADS 設定

若要覆寫特定資料庫的 ADS 設定，請勾選 [啟用資料庫層級的進階資料安全性]**** 核取方塊。 只有在您有特殊需求，才能針對個別資料庫接收獨立的 [威脅防護警示] 或 [弱點評估] 結果，以取代或除了針對資料庫伺服器或受控實例上的所有資料庫所接收的警示和結果以外，才使用此選項。

選取此核取方塊之後，您就可以設定此資料庫的相關設定。
 
![資料庫和先進的威脅防護設定](./media/sql-advanced-protection/database_threat_detection_settings.png) 

從 ADS 資料庫窗格，也可以觸達資料庫伺服器或受控執行個體的進階資料安全性設定。 按一下主要 ADS 窗格中的 [設定]****，然後按一下 [檢視進階資料安全性伺服器設定]****。 

![資料庫設定](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>後續步驟 

- 深入瞭解[資料探索 & 分類](sql-database-data-discovery-and-classification.md) 
- 深入瞭解[弱點評估](sql-vulnerability-assessment.md) 
- 深入瞭解[先進的威脅防護](sql-database-threat-detection.md)
- 深入瞭解[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
