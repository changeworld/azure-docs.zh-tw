---
title: 進階資料安全性
description: 瞭解探索和分類敏感性資料、管理資料庫弱點，以及偵測可能表示對 Azure SQL Database、Azure SQL 受控執行個體或 Azure Synapse 中的資料庫有威脅的異常活動功能。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 53765ee97f0f253db4df4ecca3c1c90d6068fb07
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983989"
---
# <a name="advanced-data-security"></a>進階資料安全性
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


先進的資料安全性（ADS）是先進 SQL 安全性功能的整合套件。 ADS 適用于 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse 分析。 其中包含的功能可用於探索和分類敏感性資料、找出潛在資料庫弱點並減低其風險，及偵測可能指出資料庫遇到威脅的異常活動。 此套件可讓您從單一的進入點位置啟用及管理前述功能。

## <a name="overview"></a>概觀

ADS 提供一組先進的 SQL 安全性功能，包括資料探索 & 分類、SQL 弱點評定和先進的威脅防護。
- [資料探索 & 分類](data-discovery-and-classification-overview.md)提供內建于 Azure SQL Database、azure SQL 受控執行個體和 azure Synapse 的功能，可用於探索、分類、標記和報告資料庫中的敏感性資料。 它可用來讓您檢視資料庫分類狀態、追蹤對資料庫內敏感性資料的存取，並具有其他多方面的用途。
- [弱點評估](sql-vulnerability-assessment.md)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它會顯示您的安全性狀態，並包含可採取動作的步驟來解決安全性問題，並增強您的資料庫防護。
- [進階威脅防護](threat-detection-overview.md)偵測到異常活動，即表示有不尋常及可能有害的活動，試圖存取或惡意探索您的資料庫。 它會持續監視您的資料庫是否有可疑的活動，並針對潛在弱點、Azure SQL 插入式攻擊，以及異常的資料庫存取模式，提供立即的安全性警示。 進階威脅防護警示會提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。

啟用 [Advanced Data Security] 一次即可啟用所有這些包含的功能。 只要按一下，您就可以針對您在 Azure 中的[伺服器](logical-servers.md)上或 SQL 受控執行個體中的所有資料庫啟用 ADS。 啟用或管理 ADS 設定需要屬於[SQL 安全性管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)角色，或其中一個資料庫或伺服器管理員角色。

ADS 定價與 Azure 資訊安全中心標準層一致，其中每個受保護的伺服器或受控實例會計為一個節點。 新的受保護資源適用資訊安全中心標準層的免費試用。 如需詳細資訊，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="getting-started-with-ads"></a>開始使用 ADS

下列步驟可讓您開始使用 ADS。

## <a name="1-enable-ads"></a>1. 啟用 ADS

在伺服器或受控實例的 [**安全性**] 標題底下，流覽至 [ **Advanced Data Security** ]，以啟用廣告。

> [!NOTE]
> 系統會自動建立並設定儲存體帳戶，以儲存您的**弱點評定**掃描結果。 如果您已針對相同資源群組和區域中的另一部伺服器啟用廣告，則會使用現有的儲存體帳戶。

![啟用 ADS](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> ADS 的成本會與每個節點 Azure 資訊安全中心標準層定價一致，其中節點是整個伺服器或受控實例。 因此，您只需支付一次，即可使用 ADS 保護伺服器或受控實例上的所有資料庫。 您可以先以免費試用版試用 ADS。

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. 開始分類資料、追蹤弱點，以及調查威脅警示

按一下 [資料探索與分類]**** 卡可查看建議要分類的敏感性資料行，以及使用持續敏感性標籤為資料分類。 按一下 [弱點評估]**** 卡可檢視和管理弱點掃描和報告，以及追蹤您的安全性水準。 如果已收到安全性警示，請按一下 [ **Advanced 威脅防護**] 卡片以查看警示的詳細資料，並透過 [Azure 資訊安全中心安全性警示] 頁面，查看您 Azure 訂用帳戶中所有警示的匯總報告。

## <a name="3-manage-ads-settings"></a>3. 管理 ADS 設定

若要查看和管理 ADS 設定，請在伺服器或受控實例的 [**安全性**] 標題下，流覽至 [ **Advanced Data Security** ]。 在此頁面上，您可以啟用或停用 ADS，以及修改整個伺服器或受控實例的弱點評定和先進的威脅防護設定。

![伺服器設定](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-database"></a>4. 管理資料庫的 ADS 設定

若要覆寫特定資料庫的 ADS 設定，請勾選 [啟用資料庫層級的進階資料安全性]**** 核取方塊。 只有在您有特定需求，才能針對個別資料庫接收獨立的「威脅防護」警示或弱點評估結果，以取代或除了針對伺服器或受控實例上的所有資料庫所接收的警示和結果以外，才使用此選項。

選取此核取方塊之後，您就可以設定此資料庫的相關設定。

![資料庫和先進的威脅防護設定](./media/advanced-data-security/database_threat_detection_settings.png)

您也可以從 ADS 資料庫窗格連線到您伺服器或受控實例的 Advanced data security 設定。 按一下主要 ADS 窗格中的 [設定]****，然後按一下 [檢視進階資料安全性伺服器設定]****。

![資料庫設定](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>後續步驟

- 深入瞭解[資料探索 & 分類](data-discovery-and-classification-overview.md)
- 深入瞭解[弱點評估](sql-vulnerability-assessment.md)
- 深入瞭解[先進的威脅防護](threat-detection-configure.md)
- 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
