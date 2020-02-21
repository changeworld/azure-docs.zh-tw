---
title: Azure 資產的安全性最佳作法
titleSuffix: Azure security
description: 本文提供一組可在 Azure 中保護您的資料、應用程式和其他資產的操作最佳作法。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 72d7a2dd112e5e7a5105ff977e3917ccdfd7b53e
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500297"
---
# <a name="azure-operational-security-best-practices"></a>Azure 作業安全性最佳做法
本文提供一組可在 Azure 中保護您的資料、應用程式和其他資產的操作最佳作法。

最佳作法是根據共識的意見，並使用目前的 Azure 平台功能及功能集。 意見和技術會隨著時間而改變，而本文會定期更新以反映這些變更。

## <a name="define-and-deploy-strong-operational-security-practices"></a>定義及部署強大的作業安全性作法
Azure 作業安全性是指使用者可在 Azure 中用來保護其資料、應用程式和其他資產的服務、控制措施與功能。 Azure 作業安全性的基礎架構涵蓋透過 Microsoft 特有功能獲得的知識，包括[安全性開發週期 (SDL)](https://www.microsoft.com/sdl)、[Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) 方案，以及對網路安全性威脅型態的深層認知。

## <a name="manage-and-monitor-user-passwords"></a>管理和監視使用者密碼
下表列出與管理使用者密碼相關的一些最佳作法：

**最佳做法**：確保您在雲端擁有適當層級的密碼保護。   
**詳細資料**：遵循[microsoft 密碼指導](https://www.microsoft.com/research/publication/password-guidance/)方針中的指引，其範圍限於 microsoft 身分識別平臺（Azure Active Directory、Active Directory 和 Microsoft 帳戶）的使用者。

**最佳做法**：監視與您的使用者帳戶相關的可疑動作。   
**詳細資料**：使用 Azure AD 安全性報告，監視有[風險的使用者](/azure/active-directory/reports-monitoring/concept-user-at-risk)和有風險的登[入](../../active-directory/reports-monitoring/concept-risk-events.md)。

**最佳做法**：自動偵測並補救高風險的密碼。   
**詳細資料**： [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)是 Azure AD Premium P2 版本的一項功能，可讓您：

- 偵測影響組織身分識別的潛在弱點
- 針對偵測到的與您組織的身分識別有關的可疑動作，設定自動回應
- 調查可疑事件並採取適當的動作來解決問題

## <a name="receive-incident-notifications-from-microsoft"></a>接收來自 Microsoft 的事件通知
請確定您的安全性作業小組會收到來自 Microsoft 的 Azure 事件通知。 事件通知可讓您的安全性小組知道您已遭盜用 Azure 資源，讓他們能夠快速回應並修復潛在的安全性風險。

在 Azure 註冊入口網站中，您可以確保系統管理員連絡人資訊包含通知安全性作業的詳細資料。 連絡人資訊為電子郵件地址和電話號碼。

## <a name="organize-azure-subscriptions-into-management-groups"></a>將 Azure 訂用帳戶組織成管理群組
如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 [Azure 管理群組](/azure/governance/management-groups/create)可提供高於訂用帳戶的範圍層級。 您會將訂用帳戶組織成稱為「管理群組」的容器，並將您的治理條件套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的條件。

您可以將管理群組和訂用帳戶的彈性結構建立到目錄中。 每個目錄都有一個最上層管理群組，稱為「根管理群組」。 這個根管理群組會建置於階層內，讓所有的管理群組和訂用帳戶摺疊於其中。 根管理群組可讓全域原則和 RBAC 指派在目錄層級套用。

以下是使用管理群組的一些最佳作法：

**最佳做法**：確保新的訂用帳戶在新增時套用了治理元素，例如原則和許可權。   
**詳細資料**：使用根管理群組來指派適用于所有 Azure 資產的全企業安全性元素。 原則和許可權是元素的範例。

**最佳做法**：將管理群組的最上層與分割策略對齊，以提供每個區段內的控制和原則一致性點。   
**詳細資料**：為根管理群組底下的每個區段建立單一管理群組。 請勿在根之下建立任何其他管理群組。

**最佳做法**：限制管理群組深度，以避免妨礙作業和安全性的混淆。   
**詳細資料**：將您的階層限制為三個層級，包括根。

**最佳做法**：使用根管理群組，小心地選取要套用至整個企業的專案。   
**詳細資料**：確保根管理群組元素在每個資源上都有清楚的需求，而且它們會受到影響。

良好的候選人包括：

- 具有明顯業務影響的法規需求（例如，與資料主權相關的限制）
- 接近零的可能負面影響作業的需求，例如已仔細檢查的 audit 效果或 RBAC 許可權指派原則

**最佳做法**：先仔細規劃並測試根目錄管理群組上所有全企業的變更，再套用它們（原則、RBAC 模型等等）。   
**詳細資料**：根管理群組中的變更可能會影響 Azure 上的每個資源。 雖然它們提供強大的方式來確保整個企業的一致性，但錯誤或不正確的使用方式可能會對生產作業造成負面影響。 在測試實驗室或生產試驗中測試對根管理群組所做的所有變更。

## <a name="streamline-environment-creation-with-blueprints"></a>使用藍圖簡化環境建立
[Azure 藍圖](/azure/governance/blueprints/overview)服務可讓雲端架構人員和中央資訊技術小組定義一組可重複使用的 Azure 資源，以符合組織的標準、模式和需求。 Azure 藍圖可以讓開發小組使用一組內建元件來快速建立和建立新的環境，以及他們在組織合規性中建立這些環境的信心。

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>監視儲存體服務在行為上是否有非預期的變更
與傳統環境相比，在雲端環境託管的分散式應用程式一旦發生問題，無論要為其進行診斷或疑難排解，都更加複雜。 應用程式可以在 PaaS 或 IaaS 基礎架構、內部部署環境、行動裝置或是這幾種環境的組合上部署。 您應用程式的網路流量可能會周遊公用及私人網路，而且您的應用程式可能會使用多個儲存技術。

您應該持續監視您應用程式使用的儲存服務在行為上是否有任何非預期的變更 (例如回應時間更慢)。 使用記錄收集更多詳細的資料，並深入分析問題。 透過監視與記錄取得的診斷資訊將有助於判斷應用程式所遭遇問題的根本原因。 之後，您才能針對問題進行疑難排解，並決定該採取哪些合宜的步驟來矯正它。

[Azure 儲存體分析](../../storage/common/storage-analytics.md)會執行記錄，並提供 Azure 儲存體帳戶的計量資料。 建議您使用此資料來追蹤要求、分析使用量趨勢，以及診斷儲存體帳戶的問題。

## <a name="prevent-detect-and-respond-to-threats"></a>預防、偵測及回應威脅
[Azure 資訊安全中心](../../security-center/security-center-intro.md)藉由提供對 Azure 資源安全性的可見度（及控制），協助您預防、偵測及回應威脅。 它會在您的 Azure 訂用帳戶之間提供整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於各種安全性解決方案。

資訊安全中心的免費層僅針對您的 Azure 資源提供有限的安全性。 標準層可將這些功能擴充到內部部署及其他雲端。 資訊安全中心 Standard 可協助您尋找和修正安全性弱點、套用存取和應用程式控制項以封鎖惡意活動、流量分析和情報來偵測威脅，以及在遭受攻擊時快速回應。 前 60 天可以免費試用資訊安全中心標準定價層。 我們建議您將[Azure 訂用帳戶升級為資訊安全中心 Standard](../../security-center/security-center-get-started.md)。

使用資訊安全中心來取得所有 Azure 資源的安全性狀態的中央視圖。 只需看一眼，便可確認安全性控制項是否已就緒並正確設定，並快速找出任何需要注意的資源。

資訊安全中心也會與[Microsoft Defender Advanced 威脅防護（ATP）](../../security-center/security-center-wdatp.md)整合，以提供完整的端點偵測和回應（EDR）功能。 透過 Microsoft Defender ATP 整合，您可以找出異常狀況。 您也可以在資訊安全中心監視的伺服器端點上偵測和回應先進的攻擊。

幾乎所有的企業組織都有安全性資訊和事件管理（SIEM）系統，藉由合併各種信號收集裝置的記錄資訊，協助識別新興的威脅。 然後，資料分析系統會分析這些記錄，以協助識別在所有記錄收集和分析解決方案中不可避免的雜訊「有趣」。

[Azure Sentinel](/azure/sentinel/overview)是可調整、雲端原生的安全性資訊和事件管理（SIEM），以及安全性協調流程自動化回應（攀升情況）解決方案。 Azure Sentinel 透過警示偵測、威脅可見度、主動式搜尋和自動化威脅回應，提供智慧型安全性分析和威脅情報。

以下是預防、偵測和回應威脅的一些最佳作法：

**最佳做法**：使用以雲端為基礎的 SIEM 來增加 SIEM 解決方案的速度和擴充性。   
**詳細資料**：調查[Azure Sentinel](/azure/sentinel/overview)的特性和功能，並將其與您目前在內部部署中使用的功能做比較。 如果符合您組織的 SIEM 需求，請考慮採用 Azure Sentinel。

**最佳做法**：找出最嚴重的安全性弱點，讓您可以設定調查的優先順序。   
**詳細資料**：請檢查您的[azure 安全分數](../../security-center/security-center-secure-score.md)，以查看內建于 Azure 資訊安全中心的 azure 原則和計畫所產生的建議。 這些建議有助於解決安全性更新、端點保護、加密、安全性設定、遺失 WAF、網際網路連線的 Vm 等最高的風險。

安全分數是以網際網路安全性（CIS）控制中心為基礎，可讓您對外部來源的組織 Azure 安全性進行基準測試。 外部驗證可協助您驗證及擴充小組的安全性策略。

**最佳做法**：監視機器、網路、儲存體和資料服務以及應用程式的安全性狀態，以探索潛在的安全性問題並設定其優先順序。  
**詳細資料**：遵循資訊安全中心啟動的[安全性建議](../../security-center/security-center-recommendations.md)，其中包含最高優先順序的專案。

**最佳做法**：將資訊安全中心警示整合到您的安全性資訊和事件管理（SIEM）解決方案。   
**詳細資料**：具有 SIEM 的大部分組織都會使用它做為需要分析師回應之安全性警示的中央 clearinghouse。 資訊安全中心所產生的已處理事件會發佈到 Azure 活動記錄檔，其中一個可透過 Azure 監視器取得的記錄。 Azure 監視器提供合併的管線，將您的所有監視資料路由傳送至 SIEM 工具。 如需相關指示，請參閱[整合資訊安全中心中的安全性解決方案](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem)。 如果您使用的是 Azure Sentinel，請參閱[Connect Azure 資訊安全中心](../../sentinel/connect-azure-security-center.md)。

**最佳做法**：將 Azure 記錄與您的 SIEM 整合。   
**詳細**資料：使用[Azure 監視器來收集及匯出資料](/azure/azure-monitor/overview#integrate-and-export-data)。 這是啟用安全性事件調查的關鍵，而線上記錄保留會受到限制。 如果您使用的是 Azure Sentinel，請參閱[連接資料來源](../../sentinel/connect-data-sources.md)。

**最佳做法**：藉由將端點偵測和回應（EDR）功能整合到您的攻擊調查中，加速調查和搜尋程式，並減少誤報。   
**詳細資料**：透過您的資訊安全中心安全性原則[啟用 Microsoft Defender ATP 整合](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration)。 請考慮使用 Azure Sentinel 進行威脅搜尋和事件回應。

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>監視以端對端案例為基礎的網路監視
客戶可以結合網路資源 (例如虛擬網路、ExpressRoute、應用程式閘道及負載平衡器)，在 Azure 中建置端對端網路。 每個網路資源都可進行監視。

[Azure 網路監看員](../../network-watcher/network-watcher-monitoring-overview.md)是一個區域性服務。 使用其診斷和視覺化工具可監視並診斷位於和進出 Azure 的網路案例層級條件。

以下是網路監視及可用工具的最佳作法。

**最佳做法**：利用封包擷取自動化遠端網路監視作業。  
**詳細資料**：使用網路監看員無須登入您的 VM，就能監視及診斷網路問題。 您可以設定警示來觸發[封包擷取](../../network-watcher/network-watcher-alert-triggered-packet-capture.md)，並能存取封包層級的即時效能資訊。 當您發現問題時，可以詳加調查，從而進行更好的診斷。

**最佳做法**：使用流量記錄獲取對網路流量的見解。  
**詳細資料**：使用[網路安全性群組流量記錄](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)，更深入了解您的網路流量模式。 流量記錄中的資訊可協助您收集合規性資料，並稽核及監視您的網路安全性設定檔。

**最佳做法**：診斷 VPN 連線問題。  
**詳細資料**：使用網路監看員[診斷最常見的 VPN 閘道及連線問題](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)。 您不僅可以找出問題，還可以使用詳細的記錄進一步調查。

## <a name="secure-deployment-by-using-proven-devops-tools"></a>使用經證實的 DevOps 工具進行安全的部署
使用下列 DevOps 最佳做法以確保您企業和團隊的生產力與效率。

**最佳做法**：自動建置及部署服務。  
**詳細資料**：[基礎架構即程式碼](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)是一組技術和實作，可協助 IT 專業人員減輕每日建置及管理模組化基礎架構的負擔。 它可讓 IT 專業人員建置並維護其新式伺服器環境，就像是軟體開發人員建置並維護應用程式程式碼一樣。

您可以使用 [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) 搭配宣告式範本佈建應用程式。 在單一的範本中，您可以部署多個服務及其相依性。 您可以使用相同的範本，在應用程式生命週期的每個階段重複部署應用程式。

**最佳做法**：自動建置並部署至 Azure Web 應用程式或雲端服務。  
**詳細資料**：您可以設定 Azure DevOps Projects[自動建立和部署](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops)至 Azure web apps 或雲端服務。 Azure DevOps 在每次程式碼簽入之後，于對 Azure 執行組建之後，自動部署二進位檔。 套件建置程序等同於 Visual Studio 中的 [套件] 命令，而發佈步驟等同於 Visual Studio 中的 [發佈] 命令。

**最佳做法**：自動化發行管理。  
**詳細資料**：[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) 是用於自動化多階段部署及管理發行程序的解決方案。 建立受控持續部署管線以快速、輕鬆且經常發行。 您可以使用 Azure Pipelines，將發行程序自動化，而且可以擁有預先定義的核准工作流程。 在內部部署及部署至雲端、擴充，並視需要自訂。

**最佳做法**：在您啟動應用程式或將更新部署至生產環境之前，請先檢查該應用程式的效能。  
**詳細資料**：執行以雲端為基礎的[負載測試](/azure/devops/test/load-test/overview#alternatives)，以：

- 尋找您應用程式中的效能問題。
- 提升部署品質。
- 確定您的應用程式仍可使用。
- 確定您的應用程式可以處理後續上市或行銷活動的流量。

[Apache JMeter](https://jmeter.apache.org/)是一種免費、熱門的開放原始碼工具，具備強大的社區支援。

**最佳做法**：監視應用程式效能。  
**詳細資料**：[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」(APM) 服務。 使用 Application Insights 監視您的即時 Web 應用程式。 它會自動偵測效能異常。 其中包括分析工具可協助您診斷問題，並了解使用者實際如何運用您的應用程式。 它是設計來協助您持續改善效能和可用性。

## <a name="mitigate-and-protect-against-ddos"></a>減少並防範 DDoS
分散式阻斷服務 (DDoS) 是一種嘗試耗盡應用程式資源的攻擊類型。 目標是影響應用程式的可用性，及其處理合法要求的能力。 這些攻擊會變得越來越複雜，而且大小和影響越來越大。 它們可以鎖定可透過網際網路公開觸達的任何端點。

設計和建置 DDoS 復原需要規劃與設計各種不同的失敗模式。 以下是在 Azure 上建置 DDoS 復原服務的最佳做法。

**最佳做法**：確保安全性在應用程式的整個生命週期 (從設計和實作到部署與作業) 中具有優先順序。 應用程式可能存在一些錯誤 (bug)，讓非常小量的要求使用大量資源，因而導致服務中斷。  
**詳細資料**：若要協助保護在 Microsoft Azure 上執行的服務，您應該對您應用程式架構有良好的了解，而且必須專注於[軟體品質的五大要素](https://docs.microsoft.com/azure/architecture/guide/pillars)。 您應該了解一般流量、應用程式與其他應用程式之間的連線模型，以及公開至公用網際網路的服務端點。

確保應用程式有足夠的彈性，能夠處理以應用程式本身為目標的阻斷服務，至關重要。 從[安全性開發週期 (SDL)](https://www.microsoft.com/sdl) 開始，安全性和隱私權便會內建於 Azure 平台之中。 SDL 會在每個開發階段處理安全性，並確保 Azure 會持續更新以使其更為安全。

**最佳做法**：將應用程式設計為可[水平調整](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)以滿足放大負載的需求，遭遇 DDoS 攻擊時尤其需要。 如果您的應用程式相依於服務的單一執行個體，它會建立單一失敗點。 佈建多個執行個體可讓系統更有彈性且更具延展性。  
**詳細資料**：若是 [Azure App Service](/azure/app-service/app-service-value-prop-what-is)，選取可提供多個執行個體的 [App Service 方案](../../app-service/overview-hosting-plans.md)。

對於 Azure 雲端服務，設定您的每個角色以使用[多個執行個體](../../cloud-services/cloud-services-choose-me.md)。

對於 [Azure 虛擬機器](/azure/virtual-machines/windows/overview)，確保 VM 架構包含多個 VM，而且每個 VM 都包含於[可用性設定組](/azure/virtual-machines/virtual-machines-windows-manage-availability)中。 建議您使用虛擬機器擴展集，自動調整功能。

**最佳做法**：將應用程式中的安全性防禦分層，可降低成功攻擊的機會。 使用 Azure 平台的內建功能，為您的應用程式實作安全設計。  
**詳細資料**：攻擊的風險會隨著應用程式大小 (介面區) 而提高。 您可以使用允許清單來關閉負載平衡器 ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) 和 [Azure 應用程式閘道](/azure/application-gateway/application-gateway-create-probe-portal)) 上不需要的公開 IP 位址空間和接聽連接埠，來減少介面區。

[網路安全性群組](../../virtual-network/security-overview.md)是減少攻擊面的另一種方法。 您可以使用[服務標記](../../virtual-network/security-overview.md#service-tags)和[應用程式安全性群組](../../virtual-network/security-overview.md#application-security-groups)，將建立安全性規則與設定網路安全性的複雜性近可能降低，直到成為應用程式結構的自然延伸。

應盡可能將 Azure 服務部署於[虛擬網路](../../virtual-network/virtual-networks-overview.md)上。 此種做法可讓服務資源透過私人 IP 位址進行通訊。 根據預設，來自虛擬網路的 Azure 服務流量會使用公用 IP 位址作為來源 IP 位址。

使用[服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)會在從虛擬網路存取 Azure 服務時，將服務流量切換為使用虛擬網路私人位址作為來源 IP 位址。

我們通常會看到客戶的內部部署資源連同其在 Azure 中的資源遭受攻擊。 如果您將內部部署環境連線到 Azure，請將內部部署資源在公用網際網路上的曝光率降至最低。

Azure 有兩個 DDoS [服務供應項目](../../virtual-network/ddos-protection-overview.md)，可防止網路攻擊：

- 基本保護預設會整合到 Azure 中，不需任何額外成本。 Azure 全球部署網路的規模與能力可提供防禦，透過一律啟動的流量監視和即時緩和，來抵禦常見的網路層攻擊。 基本保護不需要設定使用者也不需要變更應用程式，而且有助於保護所有 Azure 服務，包括像是 Azure DNS 的 PaaS 服務。
- 標準保護則針對網路攻擊，提供進階的 DDoS 防護功能。 其會自動調整以保護特定的 Azure 資源。 只需在建立虛擬網路時啟用保護即可。 您也可以在建立之後再啟用保護，而不需進行任何應用程式或資源變更。

## <a name="enable-azure-policy"></a>啟用 Azure 原則
[Azure 原則](/azure/governance/policy/overview)是 Azure 中的一項服務，可讓您用來建立、指派和管理原則。 這些原則會對您的資源強制執行規則和效果，讓這些資源保持符合您的公司標準和服務等級協定。 Azure 原則會透過評估您的資源是否符合指派的策略來滿足此需求。

啟用 Azure 原則來監視和強制執行貴組織的書面原則。 這會藉由集中管理混合式雲端工作負載的安全性原則，確保符合公司或法規的安全性需求。 瞭解如何[建立和管理原則以強制執行合規性](../../governance/policy/tutorials/create-and-manage.md)。 如需原則元素的總覽，請參閱[Azure 原則定義結構](../../governance/policy/concepts/definition-structure.md)。

以下是您採用 Azure 原則之後要遵循的一些安全性最佳做法：

**最佳做法**：原則支援數種類型的效果。 您可以在[Azure 原則定義結構](../../governance/policy/concepts/definition-structure.md#policy-rule)中閱讀相關資訊。 商務營運可能會受到**拒絕**效果和**補救**效果的負面影響，因此請從**審核**效果開始，以限制原則產生負面影響的風險。   
**詳細資料**：[以 audit 模式啟動原則部署](../../governance/policy/concepts/definition-structure.md#policy-rule)，然後在稍後的進度中進行**拒絕**或**補救**。 在您移至 [**拒絕**] 或 [**修復**] 之前，請先測試並檢查審核效果的結果。

如需詳細資訊，請參閱[建立和管理原則以強制執行合規性](../../governance/policy/tutorials/create-and-manage.md)。

**最佳做法**：識別負責監視原則違規的角色，並確保快速地採取正確的補救動作。   
**詳細資料**：透過[Azure 入口網站](../../governance/policy/how-to/get-compliance-data.md#portal)或經由[命令列](../../governance/policy/how-to/get-compliance-data.md#command-line)，讓指派的角色監視合規性。

**最佳做法**： Azure 原則是組織撰寫之原則的技術標記法。 將所有 Azure 原則對應到組織原則，以減少混淆並提高一致性。   
**詳細資料**：在 azure[原則描述](../../governance/policy/concepts/definition-structure.md#display-name-and-description)或 azure 原則[計畫](../../governance/policy/concepts/definition-structure.md#initiatives)描述中新增對組織原則的參考，以在貴組織的檔或 azure 原則本身中進行檔對應。

## <a name="monitor-azure-ad-risk-reports"></a>監視 Azure AD 風險報告
大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 探索遭入侵的身分識別並不容易。 Azure AD 會使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 每個偵測到的可疑動作都會儲存在名為[風險偵測](../../active-directory/reports-monitoring/concept-risk-events.md)的記錄中。 風險偵測會記錄在 Azure AD 的安全性報告中。 如需詳細資訊，請參閱有[風險的安全性報告上的使用者](../../active-directory/reports-monitoring/concept-user-at-risk.md)和有風險的登[入安全性報告](../../active-directory/reports-monitoring/concept-risky-sign-ins.md)。

## <a name="next-steps"></a>後續步驟
如需更多安全性最佳做法，請參閱 [Azure 安全性最佳做法與模式](best-practices-and-patterns.md)，以便在使用 Azure 設計、部署和管理雲端解決方案時使用。

下列資源可提供更多有關 Azure 安全性和相關 Microsoft 服務的一般資訊：
* [Azure 安全性小組部落格](https://blogs.msdn.microsoft.com/azuresecurity/) - Azure 安全性的最新資訊
* [Microsoft 安全性回應中心](https://technet.microsoft.com/library/dn440717.aspx) -- 可在其中回報 Microsoft 安全性弱點 (包括 Azure 的問題) 或透過電子郵件傳送給 secure@microsoft.com
