---
title: Azure 成本管理 + 計費概觀
description: 您可以使用 Azure 成本管理 + 計費功能來執行計費管理工作，以及管理成本的帳單存取。 您也可以使用這些功能來監視及控制 Azure 費用，並將 Azure 資源的使用方式最佳化。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contentperfq2
ms.openlocfilehash: 1dca2fa938c26ba45f162fae962aec0bfaf6cef7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353029"
---
# <a name="what-is-azure-cost-management--billing"></a>什麼是 Azure 成本管理 + 計費？

藉由使用 Microsoft 雲端，您可以大幅提升商務工作負載的技術效能。 此外也可以降低您的成本，以及管理組織資產所需的額外負荷。 不過，商機也伴隨著風險，因為您的雲端部署中有可能導入浪費和低效的因子。 Azure 成本管理 + 計費是 Microsoft 所提供的一套工具，可協助您分析、管理和最佳化工作負載的成本。 使用此套件有助於確保您的組織充分利用雲端所提供的優勢。

您可以將 Azure 工作負載想像成家裡的燈。 當您外出時，您會讓燈亮著不關嗎？ 您是否能改用更高效的燈泡，好節省一點電費？ 是否有哪個房間不需要那麼多盞燈？ 您可以使用 Azure 成本管理 + 計費功能，將類似的考量程序套用到組織所使用的工作負載。

使用 Azure 產品和服務時，您只需為使用的部分付費。 當您建立和使用 Azure 資源時，您需支付資源的費用。 由於新資源很容易部署，因此若未進行適當的分析和監視，您的工作負載成本可能會大幅上升。 您可以使用 Azure 成本管理 + 計費功能來執行下列動作：

- 進行計費管理工作，例如支付帳單
- 管理成本的帳單存取
- 下載用來產生每月發票的成本和使用方式資料
- 主動將資料分析套用至您的成本
- 設定費用閾值
- 找機會變更工作負載以將費用最佳化

若要深入了解如何以組織身分進行成本管理，請查看 [Azure 成本管理最佳做法](./costs/cost-mgt-best-practices.md)一文。

## <a name="understand-azure-billing"></a>了解 Azure 計費

Azure 計費功能可用來檢閱您已開立發票的成本，並管理計費資訊的存取權。 在較大型的組織中，採購和財務小組通常會進行計費工作。

當您註冊使用 Azure 時，就會建立計費帳戶。 您可使用計費帳戶來管理您的發票、付款及追蹤成本。 您可擁有多個計費帳戶的存取權。 例如，您可能已針對您的個人專案註冊 Azure。 因此，您可能會有一個搭配計費帳戶的個別 Azure 訂用帳戶。 但也可以透過貴組織的 Enterprise 合約或 Microsoft 客戶合約獲得存取權。 針對每個案例，您會有不同的計費帳戶。

### <a name="billing-accounts"></a>計費帳戶

Azure 入口網站目前支援下列計費帳戶類型：

- **Microsoft Online Services 方案**：當您透過 Azure 網站註冊 Azure 時，就會建立 Microsoft Online Services 方案的個別計費帳戶。 例如，當您註冊 Azure 免費帳戶、採用隨用隨付費率的帳戶，或以 Visual studio 訂閱者身分註冊時。

- **Enterprise 合約**：當貴組織簽署 Enterprise 合約 (EA) 以使用 Azure 時，就會建立 Enterprise 合約的計費帳戶。

- **Microsoft 客戶合約**：當貴組織與 Microsoft 代表共同簽署 Microsoft 客戶合約時，就會建立 Microsoft 客戶合約的計費帳戶。 精選區域中某些透過 Azure 網站註冊採用隨用隨付費率的帳戶或將其 Azure 免費帳戶升級的客戶，可能也有 Microsoft 客戶合約的計費帳戶。

### <a name="scopes-for-billing-accounts"></a>計費帳戶的範圍
範圍是計費帳戶內可供您用來檢視和管理計費的節點。 您可在此管理計費資料、付款、發票，以及進行一般帳戶管理。

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services 方案

|影響範圍  |定義  |
|---------|---------|
|計費帳戶     | 代表一或多個 Azure 訂用帳戶的單一擁有者 (帳戶管理員)。 帳戶管理員有權執行各種計費工作，例如建立訂用帳戶、檢視發票或變更訂用帳戶的計費。  |
|訂用帳戶     |  代表 Azure 資源的群組。 發票會在訂用帳戶範圍上產生。 它有自己的付款方式，可用來支付其發票。|

#### <a name="enterprise-agreement"></a>Enterprise 合約

|影響範圍  |定義  |
|---------|---------|
|計費帳戶    | 表示 Enterprise 合約註冊。 發票會在計費帳戶範圍上產生。 其使用部門和註冊帳戶進行結構化。  |
|department     |  註冊帳戶的選擇性群組。      |
|註冊帳戶     |  代表單一帳戶擁有者。 Azure 訂用帳戶會建立在註冊帳戶範圍之下。  |

#### <a name="microsoft-customer-agreement"></a>Microsoft 客戶合約

|影響範圍  |工作  |
|---------|---------|
|計費帳戶     |   表示適用於多項 Microsoft 產品和服務的客戶合約。 計費帳戶會使用帳單設定檔和發票區段進行結構化。   |
|帳單設定檔     |  表示發票及其付款方式。 發票會在此範圍產生。 帳單設定檔可以有多個發票區段。      |
|發票區段     |   代表發票中的一組成本。 訂用帳戶和其他購買項目會與發票區段範圍相關聯。    |

## <a name="understand-azure-cost-management"></a>了解 Azure 成本管理

雖然計費與成本管理相關，則實則不同。 計費是涉及向客戶開立商品或服務發票以及管理商業關係的程序。

成本管理會利用進階分析來顯示組織的成本和使用量模式。 成本管理報告顯示依使用量計費的 Azure 服務與協力廠商 Marketplace 方案所耗用的成本。 成本是根據議定的價格，並計入保留和 Azure Hybrid Benefit 折扣要素而定。 整體而言，這些報告會針對使用量和 Azure Marketplace 費用，顯示您的內部和外部成本。 其他費用，例如保留購買、支援和稅金還不會顯示在報告中。 這些報告可協助您了解您的費用和資源使用情況，並可協助您找出異常的費用。 也可提供預測性分析。 成本管理會使用 Azure 管理群組、預算及建議，清楚地說明如何組織您的費用以及如何降低成本。

您可以使用 Azure 入口網站或適用於匯出自動化的各種 API ，來與外部系統和程序整合。 也可使用自動化計費資料匯出和排程定報告。

觀看 Azure 成本管理概觀影片 以快速並概略了解 Azure 成本管理如何協助您在 Azure 中節省成本。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>計劃和控制費用

成本管理協助您規劃及控制成本的方式包括：成本分析、預算、建議，以及匯出成本管理資料。

您可以使用成本分析來探索及分析組織成本。 您可以檢視組織所彙總的成本，以了解成本是在何處累算，以及識別費用趨勢。 此外，您可以查看經過一段時間累積的成本，對照預算來預估每月、每季，或甚至每年的成本趨勢。

預算可協助您進行規劃並符合貴組織的財務責任歸屬。 其有助於避免超過成本閾值或限制。 預算也可以協助您通知其他人其費用的相關資訊以主動管理成本。 透過預算，您可以查看費用在經過一段時間的進展方式。

建議顯示如何透過識別閒置和使用量過低的資源來最佳化及改善效率。 或者，其可顯示費用較低的資源選項。 當您依照建議採取動作時，您會變更您使用資源的方式來節省成本。 若要採取行動，請先檢視成本最佳化建議以檢視潛在的使用效率不足。 接下來，您可依照建議採取動作，將 Azure 資源修改為使用更符合成本效益的選項。 然後，您可驗證此動作，以確保您所進行的變更成功。

如果您使用外部系統來存取或檢閱成本管理資料，即可輕鬆地從 Azure 匯出資料。 而且，您可以設定 CSV 格式的每日排程匯出，並且在 Azure 儲存體中儲存資料檔案。 然後，您可以從外部系統存取資料。

### <a name="cloudyn-deprecation"></a>Cloudyn 淘汰

Cloudyn 是一項與成本管理相關的 Azure 服務，即將在 2020 年底淘汰。 現有的 Cloudyn 功能會盡可能直接整合到 Azure 入口網站中。 目前不會將任何新客戶上線，但在產品完全淘汰之前將保留其支援。
 
### <a name="additional-azure-tools"></a>其他 Azure 工具

Azure 有其他不屬於 Azure 成本管理 + 計費功能集的工具。 不過，它們在成本管理流程中扮演重要角色。 若要深入了解這些工具，請參閱下列連結。

- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) - 使用此工具來評估您的前期雲端成本。
- [Azure Migrate](../migrate/migrate-services-overview.md) - 評估您目前的資料中心工作負載，以取得 Azure 替代解決方案需求的見解。
- [Azure Advisor](../advisor/advisor-overview.md) - 識別未使用的 VM，並接收有關 Azure 保留執行個體購買的建議。
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) - 將您目前的內部部署 Windows Server 或 SQL Server 授權用於 Azure 中的 VM 以節省成本。

## <a name="next-steps"></a>後續步驟

您現在已經熟悉成本管理 + 計費，下一步是開始使用此服務。

- 開始使用 Azure 成本管理來[分析成本](./costs/quick-acm-cost-analysis.md)。
- 您也可以進一步閱讀 [Azure 成本管理最佳做法](./costs/cost-mgt-best-practices.md)。