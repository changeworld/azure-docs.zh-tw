---
title: 使用 Visual Studio 應用中心和 Azure 服務在雲中存儲、管理和持久化應用程式資料
description: 瞭解 Visual Studio 應用中心等服務，這些服務允許您在雲中存儲、管理和持久化移動應用程式資料。
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240903"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>從雲存儲、同步和查詢移動應用程式資料
無論您構建何種應用程式，您都可能會生成和處理資料。 應用程式的使用者有很高的期望。 他們希望應用程式在所有情況下快速、無縫地工作。 大多數應用程式還跨多個設備工作。 您可以通過桌面或行動裝置訪問應用程式。 多個使用者可能同時使用該應用程式並共用資料，期望獲得對資料的即時和即時訪問。

您的應用程式使用者並不總是具有互聯網連接。 應用程式被設計和期望使用或不使用互聯網連接。 開發人員必須選擇正確的解決方案來存儲和同步其資料到雲，為其應用程式提供出色的客戶體驗。

Microsoft 提供各種服務，無需啟動伺服器、選擇資料庫或擔心規模或安全性，從而提供盡可能豐富的體驗。 這些服務提供了出色的開發人員體驗，允許您使用 SQL 或 NoSQL API 在雲中存儲應用程式資料。 您還可以在所有設備上同步資料，並使應用程式能夠使用或不帶網路連接，以説明構建可擴展且可靠的應用程式。

使用以下服務在雲中管理和存儲移動應用程式資料。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[應用中心資料](/appcenter/data/)是一種資料管理服務。 它使應用程式能夠在連線和離線方案中跨不同的設備和平臺管理、持久和同步雲中的應用程式資料。 應用中心資料構建在 Azure Cosmos DB 之上，隨著使用者群和應用程式數量的增加而擴展。 它可確保低延遲、高可用性和高可擴充性，為您的所有資料。

**主要功能**
- 從視覺化工作室應用中心門戶輕鬆預配到新的 Azure Cosmos 資料庫或現有的 Azure Cosmos 資料庫。
- NoSQL 資料庫支援輕鬆存儲、同步和查詢應用程式資料。
- 此服務基於 Azure Cosmos DB 構建，繼承了 Azure Cosmos DB 提供的大部分關鍵功能，並可以在全球範圍內擴展以滿足業務需求。
- 線上和離線同步功能，用於跨設備同步資料。
- 移動用戶端 SDK，可用於輕鬆管理專用應用程式資料。
- 對 iOS、Android、Xamarin 和反應本機平臺支援。

**引用**
- [使用視覺化工作室應用中心註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用應用中心資料](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是全域散發的多模型資料庫服務。 您可以使用它構建行星級應用程式。 使用 Azure Cosmos DB，您可以彈性地獨立地擴展全球任意數量的 Azure 區域的輸送量和存儲。 您可以使用您最喜愛的 API 曲面進行快速的、單位毫秒的資料訪問。 這些曲面包括 SQL、蒙戈DB、卡珊多拉、表或格雷姆林。 Azure Cosmos DB 唯一提供全面的服務等級協定 （SL），用於輸送量、延遲、可用性和一致性。

**主要功能**
- 支援廣泛的 API，包括 SQL （核心） API、卡珊多拉 API、蒙戈DB API、Gremlin API 和表 API。
- 交鑰匙全球分發可隨時隨地複製您的資料。 使用者可以與最接近他們的資料的副本進行交互。
- 沒有架構或索引管理，因為資料庫引擎是完全架構無關的。
- 無處不在的區域存在，因為 Azure Cosmos DB 在全球所有 Azure 區域都可用，包括公共雲中的 54 多個區域。
- 定義精確、多重一致性選項，因為 Azure Cosmos DB 的多主機複製協定經過精心設計，可提供五個定義良好的一致性選項。 這五個選項是強的、有邊界的過時、會話、一致的首碼和最終選項。
- 99.999% 的讀取和寫入可用性。
- 以程式設計方式（或通過 Azure 門戶）調用 Azure Cosmos DB 帳戶的區域容錯移轉，以確保應用程式的設計能夠抵禦區域災難。
- 保證全球第 99 個百分位時的低延遲。

**引用**
- [Azure 門戶](https://portal.azure.com) 
- [Azure Cosmos DB 文件](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)是一個通用關係資料庫託管服務。 可以使用它為 Azure 雲中的應用程式和解決方案創建高可用性和高效能資料存儲層。

**主要功能**
- **彈性資料庫模型和工具：** 使用彈性資料庫，開發人員可以將資源彙集到一組資料庫中進行縮放。 要管理這些資源，請將腳本作為作業提交。 然後，SQL 資料庫在資料庫中執行腳本。
- **高性能：** 高通量應用程式可以利用最新版本。 它可提供 25% 以上的高級資料庫功能。
- **備份、複製和高可用性：** 內置複製和 Microsoft 支援的 SLA 在資料庫級別提供應用程式連續性和抵禦災難性事件的保護。 活動異地複製允許您配置容錯移轉和自助服務還原，從而提供對"oops 恢復"的完全控制。 資料恢復可從最多 35 天的資料備份中獲取。
- **接近零的維護：** 自動軟體是服務的一部分。 內置系統副本有助於提供固有的資料保護、資料庫停機時間和系統穩定性。 系統副本將自動移動到新電腦。 它們在舊舊的失敗時被動態配置。
- **安全性：** Azure SQL 資料庫提供一系列安全功能，以滿足組織或行業授權的合規性策略：
    - 審核使開發人員能夠執行與合規性相關的任務，並獲取有關活動的知識。
    - 開發人員和 IT 可以在資料庫級別實施策略，以説明通過 Azure SQL 資料庫的行級安全性、動態資料掩蔽和透明資料加密來限制對敏感性資料的訪問。
    - Azure SQL 資料庫由關鍵雲審核員驗證，作為關鍵 Azure 合規性認證和批准範圍的一部分，例如 HIPAA BAA、ISO/IEC 27001：2005、FedRAMP 和歐盟模型條款。

**引用**
- [Azure 門戶](https://portal.azure.com) 
- [Azure SQL 資料庫文檔](/azure/sql-database/) 
