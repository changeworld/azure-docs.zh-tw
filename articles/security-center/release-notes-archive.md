---
title: Azure 資訊安全中心中新功能的封存
description: 說明在 Azure 資訊安全中心六個月前和更早版本的新功能和變更。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: c0883e91d5e806fb166c3ddeafc4ce130ff3f66f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210839"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>封存 Azure 資訊安全中心中的新功能？

[Azure Active Directory？版本資訊頁面中的主要新功能](release-notes.md)包含過去六個月的更新，而此頁面包含較舊的專案。

本頁提供下列資訊：

- 新功能
- 錯誤修正
- 已被取代的功能

## <a name="november-2019"></a>2019 年 11 月

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>北美洲區域公開預覽 Azure Key Vault 的威脅防護

Azure Key Vault 是一項重要的服務，可讓您集中管理雲端中的金鑰、秘密、密碼編譯金鑰和原則，藉此保護資料並改善雲端應用程式的效能。 由於 Azure Key Vault 會儲存機密和商務關鍵資料，因此需要金鑰保存庫和其中所儲存資料的最高安全性。

Azure 資訊安全中心對 Azure Key Vault 的威脅防護支援提供一層額外的安全性情報，可偵測不尋常且可能有害的存取或惡意探索金鑰保存庫的嘗試。 這個新的保護層可讓客戶在不是安全性專家或管理安全性監視系統的情況下，解決其金鑰保存庫的威脅。 這項功能在北美洲區域中處於公開預覽狀態。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure 儲存體的威脅防護包括惡意程式碼信譽篩選

Azure 儲存體的威脅防護提供由 Microsoft 威脅情報提供技術支援的新偵測，以偵測使用雜湊信譽分析的惡意程式碼上 Azure 儲存體傳，以及來自作用中 Tor 結束節點（匿名 proxy）的可疑存取。 您現在可以使用 Azure 資訊安全中心，跨儲存體帳戶查看偵測到的惡意程式碼。


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps 的工作流程自動化（預覽）

具有集中管理安全性和 IT/營運的組織會執行內部工作流程程式，以在其環境中發現不一致的情況時，驅動組織內所需的動作。 在許多情況下，這些工作流程是可重複的程式，而且自動化可以大幅簡化組織內的進程。

今天，我們在資訊安全中心引進了一項新功能，可讓客戶利用 Azure Logic Apps 建立自動化設定，並建立原則，以根據特定的 ASC 結果（例如建議或警示）自動加以觸發。 Azure 邏輯應用程式可以設定為執行大量邏輯應用程式連接器所支援的自訂動作，或使用資訊安全中心所提供的其中一個範本，例如傳送電子郵件或開啟 ServiceNow™票證。

如需執行工作流程之自動和手動資訊安全中心功能的詳細資訊，請參閱[工作流程自動化](workflow-automation.md)。

若要瞭解如何建立 Logic Apps，請參閱[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>適用于大量資源的快速修正已正式運作

透過將使用者指定為安全分數一部分的許多工作，能夠有效補救大型車隊的問題，可能會成為一項挑戰。

為了簡化安全性錯誤的補救措施，以及能夠快速修復大量資源的建議並改善您的安全分數，請使用快速修正補救。

此作業可讓您選取要套用補救的資源，並啟動將代表您設定的補救動作。

快速修正已在 [資訊安全中心建議] 頁面中正式推出。

在[安全性建議的參考指南](recommendations-reference.md)中，查看哪些建議已啟用快速修正。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>掃描容器映射的弱點（預覽）

Azure 資訊安全中心現在可以掃描 Azure Container Registry 中的容器映射是否有弱點。

影像掃描的運作方式是剖析容器影像檔案，然後檢查是否有任何已知的弱點（由 Qualys 提供技術支援）。

將新的容器映射推送至 Azure Container Registry 時，會自動觸發掃描本身。 找到的弱點將會呈現為資訊安全中心建議，並包含在 Azure 安全分數中，以及如何修補它們以減少所允許的攻擊面的相關資訊。


### <a name="additional-regulatory-compliance-standards-preview"></a>其他法規合規性標準（預覽）

[法規遵循] 儀表板可讓您深入瞭解以資訊安全中心評量為基礎的合規性狀況。 儀表板會顯示您的環境如何符合特定法規標準和產業基準所指定的控制項和需求，並提供如何解決這些需求的規範性建議。

「法規合規性儀表板」目前支援四個內建標準： Azure CIS 1.1.0、PCI-DSS、ISO 27001 和 SOC-TSP。 我們現在宣佈公開預覽版本的其他支援標準： NIST SP 800-53 R4、SWIFT CSP CSCF v2020、加拿大聯邦 PBMM 和英國官方與 UK NHS 搭配使用。 我們也會發行 Azure CIS 1.1.0 的更新版本，涵蓋來自標準和增強擴充性的更多控制項。

[深入瞭解在您的法規合規性儀表板中自訂一組標準](update-regulatory-compliance-packages.md)。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service 的威脅防護（預覽）

Kubernetes 很快就成為在雲端中部署和管理軟體的新標準。 少數人都有豐富的 Kubernetes 經驗，而且大部分都只著重于一般工程和系統管理，並忽略安全性層面。 Kubernetes 環境必須謹慎設定以確保安全，因此不會對攻擊者公開開放的容器焦點攻擊表面門。 資訊安全中心會將容器空間中的支援擴充到 Azure 中最快速成長的服務之一-Azure Kubernetes Service （AKS）。

此公開預覽版本中的新功能包括：

- **探索 & 可見度**-資訊安全中心已註冊的訂用帳戶內的受控 AKS 實例持續探索。
- **安全分數建議**-可採取動作的專案，協助客戶符合客戶安全分數的 AKS 中的安全性最佳作法，例如「應使用角色型存取控制來限制 Kubernetes Service 叢集的存取權」。
- **威脅偵測**-主機和叢集型分析，例如「偵測到特殊許可權容器」。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>虛擬機器弱點評估（預覽）

安裝在虛擬機器中的應用程式通常會有可能導致虛擬機器缺口的弱點。 我們宣佈資訊安全中心 Standard 層包含適用于虛擬機器的內建弱點評估，不需額外付費。 Qualys 在公開預覽中提供的弱點評量，可讓您持續掃描虛擬機器上所有已安裝的應用程式，以找出易受攻擊的應用程式，並在資訊安全中心入口網站的體驗中呈現結果。 資訊安全中心會負責所有部署作業，因此使用者不需要執行任何額外的工作。 接下來，我們打算提供弱點評估選項，以支援客戶獨特的商務需求。

[深入瞭解 Azure 虛擬機器的弱點評](security-center-vulnerability-assessment-recommendations.md)量。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 上 SQL server 的 Advanced data security 虛擬機器（預覽）

Azure 資訊安全中心對於在 IaaS Vm 上執行的 SQL Db 進行威脅防護和弱點評估的支援現為預覽狀態。

[弱點評估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它會在 Azure 安全分數中提供您安全性狀態的可見度，並包含解決安全性問題和增強資料庫防護的步驟。

[先進的威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索您的 SQL server。 它會持續監視您的資料庫是否有可疑的活動，並針對異常的資料庫存取模式提供動作導向的安全性警示。 這些警示會提供可疑的活動詳細資料，以及調查和緩和威脅的建議動作。


### <a name="support-for-custom-policies-preview"></a>支援自訂原則（預覽）

Azure 資訊安全中心現在支援自訂原則（預覽階段）。

我們的客戶想要根據他們在 Azure 原則中建立的原則，使用自己的安全性評量，來擴充其目前的安全性評量涵蓋範圍資訊安全中心。 有了自訂原則的支援，現在就可以做到這一點。

這些新原則將會是資訊安全中心建議體驗、安全分數和法規合規性標準儀表板的一部分。 透過自訂原則的支援，您現在可以在 Azure 原則中建立自訂方案，然後將它新增為資訊安全中心中的原則，並將其視覺化為建議。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>使用適用于社區和合作夥伴的平臺擴充 Azure 資訊安全中心涵蓋範圍

您不僅可以使用資訊安全中心接收來自 Microsoft 的建議，也能從其他合作夥伴（例如 Check Point、Tenable 和 CyberArk）中的現有解決方案，提供更多整合。  資訊安全中心的簡單上線流程可以將您現有的解決方案連接到資訊安全中心，讓您能夠在單一位置查看您的安全性狀態建議、執行整合的報表，並針對內建和合作夥伴的建議運用所有資訊安全中心的功能。 您也可以將資訊安全中心建議匯出至合作夥伴產品。

[深入瞭解 Microsoft 智慧型安全性關聯](https://www.microsoft.com/security/partnerships/intelligent-security-association)。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>與匯出建議和警示（預覽）的先進整合

為了在資訊安全中心之上啟用企業層級案例，現在可以在 Azure 入口網站或 API 以外的其他地方取用資訊安全中心警示和建議。 這些可以直接匯出至事件中樞和 Log Analytics 工作區。 以下是您可以針對這些新功能建立的一些工作流程：

- 使用 [匯出至 Log Analytics 工作區] 時，您可以使用 Power BI 建立自訂儀表板。
- 使用 [匯出至事件中樞] 時，您可以將資訊安全中心警示和建議匯出至您的協力廠商 Siem、即時的協力廠商解決方案，或 Azure 資料總管。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>將內部內部部署伺服器上架到 Windows 管理中心的資訊安全中心（預覽）

Windows 管理中心是 Windows Server 的管理入口網站，這些伺服器不會部署在 Azure 中，而是提供數個 Azure 管理功能，例如備份和系統更新。 我們最近新增了將這些非 Azure 伺服器上架的功能，可直接從 Windows 管理中心體驗中受到 ASC 保護。

使用這項新體驗時，使用者將 WAC 伺服器上線以 Azure 資訊安全中心，並直接在 Windows 管理中心體驗中查看其安全性警示和建議。


## <a name="september-2019"></a>2019 年 9 月

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>使用彈性應用程式控制功能來管理規則

已改善使用彈性應用程式控制來管理虛擬機器規則的體驗。 Azure 資訊安全中心的彈性應用程式控制可協助您控制哪些應用程式可以在您的虛擬機器上執行。 除了規則管理的一般改進之外，還有新的優點可讓您控制當您新增規則時，將會保護哪些檔案類型。

[深入瞭解彈性應用](security-center-adaptive-application.md)程式控制。


### <a name="control-container-security-recommendation-using-azure-policy"></a>使用 Azure 原則控制容器安全性建議

您現在可以透過 Azure 原則來啟用或停用在容器安全性中補救弱點的 Azure 資訊安全中心建議。

若要查看已啟用的安全性原則，請從資訊安全中心開啟 [安全性原則] 頁面。


## <a name="august-2019"></a>2019 年 8 月

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure 防火牆的及時（JIT） VM 存取 

適用于 Azure 防火牆的即時（JIT） VM 存取現已正式運作。 除了 NSG 保護的環境之外，您還可以使用它來保護 Azure 防火牆保護的環境。

JIT VM 存取會藉由使用您的 NSG 和 Azure 防火牆規則，只在需要時提供對 Vm 的受控制存取，藉此減少網路體積型攻擊的風險。

當您為 Vm 啟用 JIT 時，您會建立一個原則來決定要保護的埠、埠維持開啟的時間長度，以及可存取這些埠的已核准 IP 位址。 此原則可協助您隨時控制使用者要求存取時可以執行的動作。

要求會記錄在 Azure 活動記錄中，因此您可以輕鬆地監視和審核存取。 即時頁面也可協助您快速識別已啟用 JIT 的現有 Vm，以及建議使用 JIT 的 Vm。

[深入瞭解 Azure 防火牆](https://docs.microsoft.com/azure/firewall/overview)。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>單鍵補救以提升您的安全性狀態（預覽）

安全分數是一個可協助您評估工作負載安全性狀態的工具。 它會審查您的安全性建議，並為您排定優先順序，讓您知道要先執行哪些建議。 這可協助您找出最嚴重的安全性弱點，以排定調查的優先順序。

為了簡化安全性錯誤的補救，並協助您快速改善您的安全分數，我們新增了一項新功能，可讓您只需按一下，就能在大量資源上補救建議。

此作業可讓您選取要套用補救的資源，並啟動將代表您設定的補救動作。

在[安全性建議的參考指南](recommendations-reference.md)中，查看哪些建議已啟用快速修正。


### <a name="cross-tenant-management"></a>跨租用戶管理

資訊安全中心現在支援跨租使用者管理案例作為 Azure 燈塔的一部分。 這可讓您在資訊安全中心中取得可見度，並管理多個租使用者的安全性狀態。 

[深入瞭解跨租使用者管理體驗](security-center-cross-tenant-management.md)。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>網路建議的更新

Azure 資訊安全中心（ASC）已啟動新的網路建議，並改良了一些現有的功能。 現在，使用資訊安全中心可為您的資源確保更高的網路保護。 

[深入瞭解網路建議](recommendations-reference.md#recs-network)。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>彈性網路強化-正式推出

在公用雲端中執行之工作負載的其中一個最大攻擊面，就是與公用網際網路之間的連線。 我們的客戶發現，很難知道應採用哪一個網路安全性群組（NSG）規則，以確保 Azure 工作負載僅適用于所需的來源範圍。 透過這項功能，資訊安全中心會學習 Azure 工作負載的網路流量和連線模式，並為網際網路對向虛擬機器提供 NSG 的規則建議。 這可協助我們的客戶更有效地設定其網路存取原則，並限制暴露于攻擊的風險。 

[深入瞭解彈性網路強化](security-center-adaptive-network-hardening.md)。
