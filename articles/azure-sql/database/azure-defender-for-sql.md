---
title: 適用於 SQL 的 Azure Defender
description: 瞭解管理資料庫弱點的功能，以及偵測可能表示對您的資料庫（在 Azure SQL Database、Azure SQL 受控執行個體或 Azure Synapse 中）有威脅的異常活動。
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
ms.date: 09/21/2020
ms.openlocfilehash: d147303df43c4f86843df518c71316e6a97b6671
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678079"
---
# <a name="azure-defender-for-sql"></a>適用於 SQL 的 Azure Defender
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


適用於 SQL 的 Azure Defender 是進階 SQL 安全性功能的整合套件。 Azure Defender 適用于 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics。 其中包含的功能可用於探索和分類敏感性資料、找出潛在資料庫弱點並減低其風險，及偵測可能指出資料庫遇到威脅的異常活動。 此套件可讓您從單一的進入點位置啟用及管理前述功能。

## <a name="overview"></a>概觀

Azure Defender 提供一組先進的 SQL 安全性功能，包括 SQL 弱點評定和先進的威脅防護。
- [弱點評定](sql-vulnerability-assessment.md) 是一項易於設定的服務，可探索、追蹤及協助您補救潛在的資料庫弱點。 它會顯示您的安全性狀態，並包含可採取動作的步驟來解決安全性問題，並增強您的資料庫防護性。
- [進階威脅防護](threat-detection-overview.md)偵測到異常活動，即表示有不尋常及可能有害的活動，試圖存取或惡意探索您的資料庫。 它會持續監視您的資料庫是否有可疑的活動，並針對潛在弱點、Azure SQL 插入式攻擊和異常資料庫存取模式提供立即的安全性警示。 進階威脅防護警示會提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。

只要啟用適用於 SQL 的 Azure Defender，其中包含的所有功能即會一併啟用。 只要按一下，您就可以針對 Azure 中 [伺服器](logical-servers.md) 上或 SQL 受控執行個體中的所有資料庫啟用 Azure Defender。 啟用或管理 Azure Defender 設定需要屬於 [SQL 安全性管理員](../../role-based-access-control/built-in-roles.md#sql-security-manager) 角色，或其中一個資料庫或伺服器管理員角色。

如需 Azure Defender for SQL 定價的詳細資訊，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="getting-started-with-azure-defender"></a>開始使用 Azure Defender

下列步驟可讓您開始使用 Azure Defender。

## <a name="enable-azure-defender"></a>啟用 Azure Defender

您可以透過 [Azure 入口網站](https://portal.azure.com)存取 Azure Defender。 在您的伺服器或受控實例的 [ **安全性** ] 標題下流覽至 [ **security center** ]，以啟用 Azure Defender。

> [!NOTE]
> 系統會自動建立並設定儲存體帳戶，以儲存您的 **弱點評定** 掃描結果。 如果您已在相同資源群組和區域中的另一部伺服器上啟用 Azure Defender，則會使用現有的儲存體帳戶。
>
> Azure Defender 的成本與每個節點 Azure 資訊安全中心標準層定價一致，其中節點是整個伺服器或受控實例。 因此，您只需支付一次，即可使用 Azure Defender 保護伺服器或受控實例上的所有資料庫。 您可以一開始就使用免費試用版來試用 Azure Defender。

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>開始追蹤弱點和調查威脅警示

按一下 [弱點評估]  卡可檢視和管理弱點掃描和報告，以及追蹤您的安全性水準。 如果已收到安全性警示，請按一下 [ **Advanced 威脅防護** 卡] 以查看警示的詳細資料，並透過 [Azure 資訊安全中心安全性警示] 頁面，查看您 Azure 訂用帳戶中所有警示的匯總報告。

## <a name="manage-azure-defender-settings"></a>管理 Azure Defender 設定

若要查看及管理 Azure Defender 設定，請流覽至您伺服器或受控實例的 [ **安全性** ] 標題下的 [ **security center** ]。 在此頁面上，您可以啟用或停用 Azure Defender，以及修改整個伺服器或受控實例的弱點評定和先進的威脅防護設定。

## <a name="manage-azure-defender-settings-for-a-database"></a>管理資料庫的 Azure Defender 設定

若要覆寫特定資料庫的 Azure Defender 設定，請核取 [在 **資料庫層級啟用 Azure defender FOR SQL** ] 核取方塊。 只有當您有特定的需求，可針對個別資料庫接收個別的 Advanced 威脅防護警示或弱點評定結果，而不是針對伺服器或受控實例上的所有資料庫接收警示和結果時，才使用此選項。

選取此核取方塊之後，您就可以設定此資料庫的相關設定。

您的伺服器或受控實例的 azure Defender for SQL 設定也可以從 Azure Defender 資料庫窗格連線。 按一下 [主要 Azure Defender] 窗格中的 [ **設定** ]，然後按一下 [ **View AZURE defender for SQL server 設定** ]。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [弱點評定](sql-vulnerability-assessment.md)
- 深入瞭解 [Advanced 威脅防護](threat-detection-configure.md)
- 深入了解 [Azure 資訊安全中心](../../security-center/security-center-introduction.md)