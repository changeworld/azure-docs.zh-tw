---
title: Azure 資訊安全中心中的安全性警示 |Microsoft Docs
description: 本主題說明哪些安全性警示，以及 Azure 資訊安全中心中可用的不同類型。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 514de1435519282335124bfd67bac82669240b78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616505"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 資訊安全中心的安全性警示

在 Azure 資訊安全中心中，有許多不同的資源類型都有各種警示。 資訊安全中心會針對部署在 Azure 上的資源，以及部署在內部部署和混合式雲端環境中的資源，產生警示。

安全性警示會由「先進偵測」觸發，而且僅適用于 Azure 資訊安全中心的「標準」層級。 有免費試用版可用。 您可以在 [安全性原則](security-center-pricing.md)中升級定價層選取項目。 若要深入了解價格，請瀏覽 [資訊安全中心](https://azure.microsoft.com/pricing/details/security-center/) 頁面。

## 回應現今的威脅<a name="respond-threats"></a>

威脅態勢在過去 20 年來已有重大變更。 在過去，公司通常只需擔心網站遭到大多想要查看「他們能做些什麼」的個別攻擊者竄改。 現今的攻擊者更加複雜且有組織性。 他們通常會有特定的財務和策略性目標。 他們也有更多的資源可供利用，因為它們可能是由民族國家或組織型犯罪所資助。

這些不斷改變的現實會導致攻擊者排名中前所未有的專業等級。 他們不再對 Web 竄改感興趣。 他們現在對竊取資訊、財務帳戶和私人資料感興趣，這些都可以用來產生開放市場的現金，或利用特定的商務、政治或軍事地位。 相較於具有財務目標的攻擊者，破壞網路以便對基礎結構和人員造成傷害的攻擊者更加令人關切。

回應時，組織通常會部署各種點解決方案，而這類解決方案會藉由尋找已知的攻擊簽章，進而著重於防禦企業周邊或端點。 這些解決方案通常會產生大量的低精確度警示，其需要資訊安全分析師進行分級和調查。 大部分的組織沒有回應這些警示所需的時間和專業知識 – 這麼多警示尚未解決。  

此外，攻擊者也演變了其方法來破壞許多以簽章為基礎的防禦措施，並[適應雲端環境](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)。 需要新的方法，才能更快速地找出新興威脅並加速偵測和回應。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：

警示是當偵測到您資源的威脅時，資訊安全中心產生的通知。 資訊安全中心會排定優先順序並列出警示，以及快速調查問題所需的資訊。 資訊安全中心也會提供如何修復攻擊的建議。

## 資訊安全中心偵測到威脅的方式為何？ <a name="detect-threats"> </a>

Microsoft 資訊安全研究人員會持續監視威脅。 由於 Microsoft 在雲端和內部部署中的全球存在，因此可以存取大量的遙測。 各種不同的資料集集合可讓您探索其內部部署取用者和企業產品的新攻擊模式和趨勢，以及其線上服務。 因此，資訊安全中心可以在攻擊者發行新的和日益複雜的攻擊時，快速地更新其偵測演算法。 這種方法可協助您跟上瞬息萬變的威脅環境。

為了偵測真正的威脅並降低誤報，資訊安全中心會收集、分析及整合來自 Azure 資源和網路的記錄資料。 它也適用于已連線的合作夥伴解決方案，例如防火牆和端點保護解決方案。 資訊安全中心會分析這項資訊，通常會將來自多個來源的資訊相互關聯，以識別威脅。

![資訊安全中心的資料收集和呈現](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

資訊安全中心會運用進階安全性分析，其遠勝於以簽章為基礎的方法。 巨量資料和 [機器學習](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) 技術突破可用來評估整個雲端網狀架構的事件 – 使用手動方式來偵測無法識別的威脅，以及預測攻擊的演化。 這些安全性分析包括︰

* **整合威脅情報**：藉由運用 microsoft 產品和服務、Microsoft 數位犯罪單位（DCU）、Microsoft Security Response CENTER （MSRC）和外部摘要的全球威脅情報，尋找已知的不良動作專案。
* **行為分析**：套用已知模式來探索惡意行為。
* **異常偵測**：使用統計分析來建置歷程基準。 它會對偏離已確立基準 (符合潛在攻擊向量) 的情況提出警示。

下列各節將進一步詳細討論這些分析。

### <a name="integrated-threat-intelligence"></a>整合式威脅情報

Microsoft 有大量全域威脅情報。 遙測會從多個來源流入，例如 Azure、Office 365、Microsoft CRM Online、Microsoft Dynamics AX、outlook.com、MSN.com、Microsoft 數位犯罪防治中心 (DCU) 和 Microsoft 安全性回應中心 (MSRC)。 研究人員也會收到主要雲端服務提供者與其他協力廠商的摘要之間共用的威脅情報資訊。 Azure 資訊安全中心可以使用這項資訊來警示您來自已知不良執行者的威脅。

### <a name="behavioral-analytics"></a>行為分析

行為分析是一種可分析及比較資料與一組已知模式的技術。 不過，這些模式並非簡單的簽章。 它們會透過已套用至大型資料集的複雜機器學習演算法來決定。 它們也能透過專業分析師仔細分析惡意行為來判定。 Azure 資訊安全中心可以使用行為分析，根據虛擬機器記錄、虛擬網路裝置記錄、網狀架構記錄、毀損傾印和其他來源的分析，來識別遭到入侵的資源。

此外，還有其他信號的相互關聯，以檢查廣泛行銷活動的支援證明。 此相互關聯有助於識別與已確立危害指標一致的事件。 

### <a name="anomaly-detection"></a>異常偵測

Azure 資訊安全中心也會使用異常偵測來識別威脅。 相較於行為分析 (這取決於衍生自大型資料集的已知模式)，異常偵測更加「個人化」，且著重於您的部署專用的基準。 機器學習服務適用於判斷您部署的正常活動，然後產生規則來定義可能代表安全性事件的極端狀況。

## <a name="how-are-alerts-classified"></a>如何分類警示？

資訊安全中心會指派警示的嚴重性，協助您排定參與每個警示的順序，如此一來，當資源遭到入侵時，您就可以立即開始使用。 嚴重性是根據資訊安全中心在尋找中的信心，或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級。

> [!NOTE]
> 在入口網站和早01-01-2019 的 REST API 版本中，警示嚴重性會以不同方式顯示。 如果您使用的是舊版的 API，請升級以取得一致的體驗，如下所述。

- **高：** 您的資源遭到入侵的機率很高。 您應立即加以了解。 資訊安全中心在不良意圖和用來發出警示的調查結果方面都具有高信賴度。 例如，偵測有已知惡意工具 (例如 Mimikatz，這是常用來竊取認證的工具) 正在執行的警示。
- **中：** 這可能是可疑的活動，可能表示資源遭到入侵。
資訊安全中心在分析或調查結果方面具有中信賴度，在不良意圖方面的信賴度則為中至高。 這些通常是機器學習或以異常為基礎的偵測。 例如，從異常位置進行的登入嘗試。
- **低：** 這可能是良性肯定或遭封鎖的攻擊。
   * 資訊安全中心沒有足夠的信賴度可確定意圖是否屬惡意以及活動是否無害。 例如，清除記錄有可能是攻擊者為了嘗試隱藏其追蹤記錄而執行的動作，但在許多情況下，這也是管理員所執行的例行性作業。
   * 資訊安全中心在封鎖攻擊時通常不會通知您，除非是我們建議您查看的特殊案例。 
- **資訊：** 當您向下切入至安全性事件時，或使用具有特定警示識別碼的 REST API 時，您只會看到資訊警示。 事件通常由多個警示組成，其中有些警示單獨來看可能僅具參考價值，但若與其他警示交互參照，則可能有進一步詳查的價值。 
 

## <a name="continuous-monitoring-and-assessments"></a>持續監視和評量

Azure 資訊安全中心受益于在 Microsoft 中提供安全性研究和資料科學小組，以持續監視威脅環境中的變更。 這包括下列計劃︰

* **威脅情報監視**︰威脅情報包含關於現有或新興威脅的機制、指標、影響和可採取動作的建議。 安全性社群會共用此資訊，而 Microsoft 會持續監視來自內部和外部來源的威脅情報摘要。
* **訊號共用**︰共用和分析 Microsoft 的資訊安全小組對於各種雲端和內部部署服務、伺服器和用戶端端點裝置組合的所提供的深入見解。
* **Microsoft 資訊安全專家**︰持續與擅長特殊資訊安全領域 (例如鑑識與 Web 攻擊偵測) 的 Microsoft 團隊攜手合作。
* **偵測微調**︰對真正的客戶資料集執行演算法，而資訊安全研究人員會與客戶一起驗證結果。 真肯定和誤判可用來縮小機器學習演算法的範圍。

結合上述努力終於獲得全新及改善的偵測功能，您因而立即受惠 – 不需採取任何的動作。

## 安全性警示類型<a name="security-alert-types"></a>

下列主題會根據資源類型，引導您完成不同的警示：

* [IaaS Windows 機器的警示](threat-protection.md#windows-machines)
* [IaaS Linux 機器的警示](threat-protection.md#linux-machines)
* [Azure App Service 的警示](threat-protection.md#app-services)
* [Azure 容器的警示](threat-protection.md#azure-containers)
* [SQL Database 和 SQL 資料倉儲的警示](threat-protection.md#data-sql)
* [Azure 儲存體的警示](threat-protection.md#azure-storage)
* [Cosmos DB 的警示](threat-protection.md#cosmos-db)

下列主題說明資訊安全中心如何使用它從與 Azure 基礎結構整合所收集的不同遙測資料，以針對部署在 Azure 上的資源套用額外的保護層級：

* [Azure 管理層（Azure Resource Manager）的警示（預覽）](threat-protection.md#management-layer)
* [Azure Key Vault 的警示（預覽）](threat-protection.md#azure-keyvault)
* [Azure 網路層的警示](threat-protection.md#network-layer)
* [來自其他服務的警示](threat-protection.md#alerts-other)

## <a name="what-are-security-incidents"></a>什麼是安全性事件？

安全性事件是相關警示的集合，而不是個別列出每個警示。 資訊安全中心使用[雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md)，將不同的警示和低精確度信號相互關聯至安全性事件。

資訊安全中心會使用事件，為您提供攻擊活動和所有相關警示的單一觀點。 這個視圖可讓您快速瞭解攻擊者所採取的動作，以及受影響的資源。 如需詳細資訊，請參閱[雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md)。

## <a name="security-alerts-in-azure-activity-log"></a>Azure 活動記錄中的安全性警示

除了在 Azure 入口網站中或以程式設計方式提供時，也會在[Azure 活動記錄](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)中將安全性警示和事件視為事件進行審核。 如需事件架構的詳細資訊，請參閱[Azure 活動記錄中的安全性警示](https://go.microsoft.com/fwlink/?linkid=2114113)。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解資訊安全中心中可用的各種警示類型。 如需詳細資訊，請參閱

* [Azure 資訊安全中心規劃和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure 資訊安全中心常見問題](https://docs.microsoft.com/azure/security-center/security-center-faq)

