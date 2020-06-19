---
title: Azure 資訊安全中心內新功能的封存
description: 說明 Azure 資訊安全中心從六個月前及更早版本的新功能和變更。
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
ms.openlocfilehash: 253ea748462ec09cd9cf4eab14c05be678cfb82d
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860897"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure 資訊安全中心內新功能的封存？

[Azure 資訊安全中心的新功能？](release-notes.md)主要版本資訊頁面包含過去六個月的更新，而此頁面包含較舊的項目。

本頁面提供以下相關資訊：

- 新功能
- 錯誤修正
- 已被取代的功能

## <a name="november-2019"></a>2019 年 11 月

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>北美洲區域中公開預覽 Azure Key Vault 的威脅防護

Azure Key Vault 是一項重要的服務，可讓您集中管理雲端中的金鑰、秘密、密碼編譯金鑰與原則，藉此保護資料並改善雲端應用程式的效能。 由於 Azure Key Vault 會儲存敏感性和業務關鍵資料，因此對於金鑰保存庫和其中所儲存資料需要最高的安全性。

Azure 資訊安全中心對 Azure Key Vault 的威脅防護支援提供增加額外的安全情報層級，用於偵測金鑰保存庫中異常且可能有害的存取意圖或攻擊。 這個新的防護層可讓客戶不需是安全性專家或管理安全性監視系統，就能解決其金鑰保存庫的威脅。 這項功能在北美洲區域中為公開預覽狀態。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure 儲存體的威脅防護包括惡意程式碼信譽檢測

Azure 儲存體的威脅防護提供由 Microsoft 威脅情報提供技術支援的新偵測，以使用雜湊信譽分析來偵測上傳至 Azure 儲存體的惡意程式碼，以及來自作用中 Tor 結束節點的可疑存取 (匿名 Proxy)。 您現在可以使用 Azure 資訊安全中心，跨儲存體帳戶檢視偵測到的惡意程式碼。


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps 的工作流程自動化 (預覽)

具有集中受控安全性和 IT/營運的組織會實作內部工作流程程序，以在其環境中發現不一致的情況時，在組織內驅動所需的動作。 在許多情況下，這些工作流程是可重複的程序，且自動化可以大幅簡化組織內的程序。

如今，我們在資訊安全中心引進了一項新功能，可讓客戶運用 Azure Logic Apps 建立自動化設定並建立原則，以根據特定的 ASC 結果 (例如「建議」或「警示」) 加以自動觸發。 Azure 邏輯應用程式可以設定為執行邏輯應用程式連接器大量社群所支援的任何自訂動作，或使用資訊安全中心所提供的其中一個範本，例如傳送電子郵件或開啟 ServiceNow™ 票證。

如需執行工作流程之自動及手動資訊安全中心功能的詳細資訊，請參閱[工作流程自動化](workflow-automation.md)。

若要了解如何建立 Logic Apps，請參閱 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>適用於大量資源的快速修正已正式推出

由於在「安全分數」中使用者會受指定許多工作，有效補救大型車隊的問題可能會成為一項挑戰。

為了簡化安全性設定錯誤的補救措施，以及能夠快速修復大量資源的建議並改善您的安全分數，請使用「快速修正」補救。

此作業可讓您選取要套用補救的資源，並啟動將代表您設定的補救動作。

快速修正今日已在 [資訊安全中心建議] 頁面中正式推出。

請查看[安全性建議參考指南](recommendations-reference.md)中的哪些建議已啟用快速修正。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>掃描容器映像的弱點 (預覽)

Azure 資訊安全中心現在可以掃描 Azure Container Registry 中的容器映像是否有弱點。

映像掃描的運作方式是剖析容器映像檔案，然後檢查是否有任何已知的弱點 (由 Qualys 提供技術支援)。

將新的容器映像推送至 Azure Container Registry 時，會自動觸發掃描本身。 找到的弱點將會呈現為資訊安全中心建議，並包含在 Azure 安全分數中，以及如何加以修補來減少其所允許受攻擊面的相關資訊。


### <a name="additional-regulatory-compliance-standards-preview"></a>其他法規合規性標準 (預覽)

[法規合規性] 儀表板可讓您深入解析以資訊安全中心評量為基礎的合規性狀態。 儀表板會顯示您的環境如何符合特定法規標準及產業基準所指定的控制項和需求，並提供如何解決這些需求的規範性建議。

[法規合規性] 儀表板目前支援四個內建標準：Azure CIS 1.1.0、PCI-DSS、ISO 27001 和 SOC-TSP。 我們現在宣佈其他支援標準的公開預覽版本：NIST SP 800-53 R4、SWIFT CSP CSCF v2020、加拿大聯邦 PBMM 和英國官方搭配英國 NHS。 我們也會發行 Azure CIS 1.1.0 的更新版本，涵蓋來自標準和增強擴充性的更多控制項。

[深入了解如何在您的法規合規性儀表板中自訂一組標準](update-regulatory-compliance-packages.md)。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service 的威脅防護 (預覽)

Kubernetes 很快就成為在雲端中部署及管理軟體的新標準。 少數人都有豐富的 Kubernetes 經驗，且大部分都只著重於一般工程和系統管理而忽略安全性層面。 必須謹慎設定 Kubernetes 環境以確保安全，因此沒有任何針對容器受攻擊面門戶保持開啟，不讓攻擊者有機可乘。 資訊安全中心會將其在容器空間中的支援擴充至 Azure 中最快速成長的服務之一 - Azure Kubernetes Service (AKS)。

此公開預覽版本中的新功能包括：

- **探索與可見度** - 在資訊安全中心註冊的訂用帳戶內持續探索受控 AKS 執行個體。
- **安全分數建議** - 可操作的項目，以協助客戶在客戶安全分數中符合 AKS 中的安全性最佳做法，例如「應使用角色型存取控制來限制 Kubernetes 服務叢集的存取權」。
- **威脅偵測** - 主機和以叢集為基礎的分析，例如「偵測到特殊權限的容器」。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>虛擬機器弱點評估 (預覽)

安裝在虛擬機器中的應用程式通常會有可能導致虛擬機器缺口的弱點。 我們宣佈資訊安全中心標準層級包含適用於虛擬機器的內建弱點評估，不需額外付費。 弱點評量是由 Qualys 提供且處於公開預覽，可讓您持續掃描虛擬機器上所有已安裝的應用程式，以找出易受攻擊的應用程式，並在資訊安全中心入口網站的體驗中呈現結果。 資訊安全中心會負責所有部署作業，因此使用者無須執行任何額外的工作。 接下來，我們打算提供弱點評估選項，以支援客戶的獨特商務需求。

[深入了解 Azure 虛擬機器的弱點評量](security-center-vulnerability-assessment-recommendations.md)。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 虛擬機器上 SQL 伺服器的進階資料安全性 (預覽)

Azure 資訊安全中心對於在 IaaS VM 上執行的 SQL DB 進行威脅防護和弱點評估支援現為預覽狀態。

[弱點評估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 其可讓您在 Azure 安全分數中看見安全性狀態，且包含解決安全性問題和增強資料庫防護性的步驟。

[進階威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)偵測到了異常活動，這表示有不尋常及可能有害的活動試圖存取或惡意探索您的 SQL 伺服器。 其會持續監視您的資料庫是否有可疑的活動，並在發現異常資料庫存取模式時，提供動作導向的安全性警示。 這些警示會提供可疑活動的詳細資料，以及調查與降低威脅的建議動作。


### <a name="support-for-custom-policies-preview"></a>支援自訂原則 (預覽)

Azure 資訊安全中心現在支援自訂原則 (預覽階段)。

我們的客戶一直以來都想要根據他們在 Azure 原則中建立的原則，使用自己的安全性評量來擴充其目前在資訊安全中心的安全性評量涵蓋範圍。 透過自訂原則的支援，現在就可以做到這一點。

資訊安全中心建議體驗、安全分數和法規合規性標準儀表板中將會包含這些新原則。 透過自訂原則的支援，您現在可以在 Azure 原則中建立自訂方案，然後將其新增為資訊安全中心內的原則，並將其視覺化為建議。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>使用適用於社區及合作夥伴的平台來擴充 Azure 資訊安全中心的涵蓋範圍

使用資訊安全中心接收來自 Microsoft 的建議，還能接收來自其他合作夥伴 (例如 Check Point、Tenable 和 CyberArk) 的現有解決方案建議，並且將獲得提供更多整合。  資訊安全中心的簡單上線流程可將您現有的解決方案連線到資訊安全中心，讓您能夠在單一位置檢視您的安全性狀態建議、執行整合的報告，並針對內建及合作夥伴建議來運用所有資訊安全中心的功能。 您也可以將資訊安全中心建議匯出至合作夥伴產品。

[深入了解 Microsoft 情報安全性聯盟](https://www.microsoft.com/security/partnerships/intelligent-security-association)。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>與建議和警示匯出的進階整合 (預覽)

為了在資訊安全中心之上啟用企業層級案例，現在可以在 Azure 入口網站或 API 以外的其他地方，取用資訊安全中心警示和建議。 這些可以直接匯出至事件中樞和 Log Analytics 工作區。 以下是您可以針對這些新功能建立的幾個工作流程：

- 您可以透過 [匯出至 Log Analytics 工作區]，使用 Power BI 來建立自訂儀表板。
- 使用 [匯出至事件中樞] 時，您可以將資訊安全中心警示和建議匯出至您的協力廠商 SIEM、即時的協力廠商解決方案，或 Azure 資料總管。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>從 Windows 管理中心將內部部署伺服器上線到資訊安全中心 (預覽)

Windows 管理中心是 Windows Server 的管理入口網站，這些伺服器不會部署在提供其數個 Azure 管理功能 (例如備份和系統更新) 的 Azure 中。 我們最近新增了將這些非 Azure 伺服器上線的功能，可直接從 Windows 管理中心體驗受到 ASC 保護。

使用這項新體驗時，使用者要將 WAC 伺服器上線至 Azure 資訊安全中心，並直接在 Windows 管理中心體驗中檢視其安全性警示和建議。


## <a name="september-2019"></a>2019 年 9 月

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>使用自適性應用程式控制改進項目來管理規則

已改善使用自適性應用程式控制來管理虛擬機器規則的體驗。 Azure 資訊安全中心的自適性應用程式控制可協助您控制哪些應用程式可以在您的虛擬機器上執行。 除了規則管理的一般改進之外，還有新的優點可讓您控制在新增規則時，將會保護哪些檔案類型。

[深入了解自適性應用程式控制](security-center-adaptive-application.md)。


### <a name="control-container-security-recommendation-using-azure-policy"></a>使用 Azure 原則控制容器安全性建議

您現在可以透過 Azure 原則，啟用或停用在容器安全性中補救弱點的 Azure 資訊安全中心建議。

若要檢視已啟用的安全性原則，請從資訊安全中心開啟 [安全性原則] 頁面。


## <a name="august-2019"></a>2019 年 8 月

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure 防火牆的 Just-in-time (JIT) VM 存取 

適用於 Azure 防火牆的 Just-in-time (JIT) VM 存取現已正式推出。 除了 NSG 保護的環境之外，您還可以使用其來保護 Azure 防火牆保護的環境。

JIT VM 存取會使用您的 NSG 和 Azure 防火牆規則，只在需要時提供對 VM 的受控制存取，藉此減少網路體積型攻擊的風險。

當您為 VM 啟用 JIT 時，會建立一個原則來決定要保護的連接埠、連接埠維持開啟多久，以及可存取這些連接埠的已核准 IP 位址。 此原則可協助您隨時控制使用者在要求存取時可以執行的動作。

要求會記錄在 Azure 活動記錄中，因此您可以輕鬆地監視及稽核存取。 Just-In-Time 也可協助您快速識別已啟用 JIT 的現有 VM，以及建議使用 JIT 的 VM。

[深入了解 Azure 防火牆](https://docs.microsoft.com/azure/firewall/overview)。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>單鍵補救以提升您的安全性狀態 (預覽)

安全分數是一個可協助您評估工作負載安全性狀態的工具。 其會檢閱您的安全性建議，並為您排列這些建議的優先順序，讓您了解要先執行哪些建議。 這可協助您找出最嚴重的安全性弱點，以優先安排調查。

為了簡化安全性設定錯誤的補救措施，並協助您快速改善您的安全分數，我們新增了一項新功能，可讓您只需按一下，就能在大量資源上補救建議。

此作業可讓您選取要套用補救的資源，並啟動將代表您設定的補救動作。

請查看[安全性建議參考指南](recommendations-reference.md)中的哪些建議已啟用快速修正。


### <a name="cross-tenant-management"></a>跨租用戶管理

資訊安全中心現在支援跨租用戶管理案例做為 Azure Lighthouse 的一部分。 這可讓您在資訊安全中心中獲得可見度，並管理多個租用戶的安全性狀態。 

[深入了解跨租用戶管理體驗](security-center-cross-tenant-management.md)。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>網路建議的更新

Azure 資訊安全中心 (ASC) 已啟動新的網路建議，並改進了一些現有的建議。 現在，使用資訊安全中心可確保您的資源獲得更高的網路保護。 

[深入了解網路建議](recommendations-reference.md#recs-network)。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>自適性網路強化 - 正式推出

在公用雲端中執行之工作負載的其中一個最大受攻擊面，就是與公用網際網路之間的連線。 我們的客戶發現，很難知道應採用哪些網路安全性群組 (NSG) 規則，以確保 Azure 工作負載僅供必要的來源範圍使用。 透過這項功能，資訊安全中心會了解 Azure 工作負載的網路流量和連線模式，並為網際網路對應的虛擬機器提供 NSG 規則建議。 這可協助我們的客戶更有效地設定其網路存取原則，並限制其暴露於攻擊的風險。 

[深入了解自適性網路強化](security-center-adaptive-network-hardening.md)。
