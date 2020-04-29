---
title: 使用 Visual Studio App Center 和 Azure 服務在雲端中儲存、管理及保存應用程式資料
description: 瞭解可讓您在雲端中儲存、管理和保存行動應用程式資料的服務，例如 Visual Studio App Center。
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240903"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>從雲端儲存、同步和查詢行動應用程式資料
無論您建立哪種應用程式，都可能產生和處理資料。 您的應用程式使用者有極高的期望。 他們希望應用程式在所有情況下都能快速且順暢地工作。 大部分的應用程式也可以跨多個裝置執行。 您可以從桌上型電腦或行動裝置存取您的應用程式。 多個使用者可能會同時使用應用程式並共用資料，並預期會立即即時存取資料。

您的應用程式使用者不一定會有網際網路連線能力。 應用程式的設計和預期會與網際網路連線搭配使用。 開發人員必須選擇適當的解決方案來儲存資料，並將其同步至雲端，為其應用程式提供絕佳的客戶體驗。

Microsoft 提供各種不同的服務，讓您不需要加速伺服器、挑選您的資料庫，或擔心調整或安全性，以盡可能提供豐富的體驗。 這些服務提供絕佳的開發人員體驗，可讓您使用 SQL 或 NoSQL Api 將應用程式資料儲存在雲端中。 您也可以同步處理所有裝置上的資料，並讓應用程式使用或不需要網路連線，以協助建立可擴充且強大的應用程式。

使用下列服務在雲端中管理和儲存行動應用程式資料。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 資料](/appcenter/data/)是一種資料管理服務。 它可讓應用程式在線上和離線案例中，跨不同的裝置和平臺來管理、保存及同步處理雲端中的應用程式資料。 以 Azure Cosmos DB 為基礎，App Center 資料會隨著您的使用者群和應用程式數目成長而調整。 它可確保您所有資料的低延遲、高可用性和高擴充性。

**主要功能**
- 從 Visual Studio App Center 入口網站輕鬆布建至新的 Azure Cosmos DB 資料庫或現有的 Azure Cosmos DB 資料庫。
- NoSQL 資料庫支援，可輕鬆地儲存、同步及查詢應用程式資料。
- 這項服務是以 Azure Cosmos DB 為基礎，繼承 Azure Cosmos DB 所提供的大部分主要功能，而且可以全域擴充以符合您的商務需求。
- 線上和離線同步處理功能，可在不同裝置之間同步資料。
- 您可以用來輕鬆管理私用應用程式資料的行動用戶端 Sdk。
- 適用于 iOS、Android、Xamarin 和 React Native 的平臺支援。

**參考**
- [使用 Visual Studio App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center 資料](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是全域散發的多模型資料庫服務。 您可以使用它來建立全球規模的應用程式。 有了 Azure Cosmos DB，您就可以彈性和獨立調整全球任何數目的 Azure 區域的輸送量和儲存體。 您可以使用您最愛的 API 介面，利用快速的單一位數毫秒資料存取。 這些表面包括 SQL、MongoDB、Cassandra、資料表或 Gremlin。 Azure Cosmos DB 針對輸送量、延遲、可用性和一致性，唯一提供完整的服務等級協定（Sla）。

**主要功能**
- 支援各種不同的 Api，其中包括 SQL （核心） API、Cassandra API、MongoDB API、Gremlin API 和資料表 API。
- 通行的全域散發會在您的使用者所在的地方複寫您的資料。 您的使用者可以與最接近它們的資料複本進行互動。
- 沒有架構或索引管理，因為資料庫引擎與架構完全無關。
- 因為全球各地的所有 Azure 區域都有提供 Azure Cosmos DB，這包括公用雲端中54個以上的區域，所以會有廣泛的區域存在。
- 明確定義的多重一致性選擇，因為 Azure Cosmos DB 的多宿主複寫通訊協定會謹慎設計，以提供五個定義完善的一致性選擇。 這五種選擇包括強式、限定過期、會話、一致前置詞和最終。
- 99.999% 的讀取和寫入可用性。
- 以程式設計方式（或透過 Azure 入口網站）會叫用您 Azure Cosmos DB 帳戶的區域性容錯移轉，以確保您的應用程式能夠承受區域性災難。
- 保證全球第99個百分位數的低延遲。

**參考**
- [Azure 入口網站](https://portal.azure.com) 
- [Azure Cosmos DB 檔](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)是一般用途的關係資料庫受控服務。 您可以使用它來為 Azure 雲端中的應用程式和解決方案建立高度可用且高效能的資料儲存層。

**主要功能**
- **彈性資料庫模型和工具：** 有了彈性資料庫，開發人員可以將資源集區在一組資料庫中進行調整。 若要以系統管理的方式管理這些資源，請將腳本提交為作業。 然後，SQL database 會跨資料庫執行腳本。
- **高效能：** 高輸送量應用程式可以利用最新版本。 它提供了25% 以上的 premium 資料庫能力。
- **備份、複寫和高可用性：** 內建複寫和資料庫層級的 Microsoft 支援 SLA 可提供應用程式持續性，並防範嚴重事件。 主動式異地複寫可讓您設定容錯移轉和自助式還原，讓您完全掌控「糟糕的復原」。 資料還原可從最多35天的資料備份中取得。
- **近乎零的維護：** 自動軟體是服務的一部分。 內建的系統複本有助於提供固有的資料保護、資料庫執行時間和系統穩定性。 系統複本會自動移至新電腦。 它們會即時布建，因為舊的失敗。
- **安全性：** Azure SQL Database 提供一系列的安全性功能，以符合組織或產業規定的合規性政策：
    - 「審核」可讓開發人員執行符合規範的工作，並取得活動的相關知識。
    - 開發人員和 IT 可以在資料庫層級上執行原則，以協助限制具有資料列層級安全性、動態資料遮罩和透明資料加密的敏感性資料存取，以進行 Azure SQL Database。
    - 主要雲端審計員會在 Azure 合規性認證和核准的範圍中驗證 Azure SQL Database，例如 HIPAA BAA、ISO/IEC 27001:2005、FedRAMP 和 EU Model 子句。

**參考**
- [Azure 入口網站](https://portal.azure.com) 
- [Azure SQL Database 文件](/azure/sql-database/) 
