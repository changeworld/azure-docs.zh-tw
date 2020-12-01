---
title: 適用于 Cloud Shell 的 Azure 安全性基準
description: Cloud Shell 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 24856e458858ad86d953b50709669823b35794fc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348257"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>適用于 Cloud Shell 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview-v1.md) 的指引套用至 Cloud Shell。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。
內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Cloud Shell 的相關指引來分組。 已排除不適用 Cloud Shell 的 **控制項**。

 
若要查看 Cloud Shell 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Cloud Shell 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：客戶可以將 Azure Cloud Shell 部署至客戶擁有的虛擬網路。

當您將 Azure Cloud Shell 部署至客戶擁有的虛擬網路時，您必須建立或使用現有的虛擬網路。 確定所選的虛擬網路已將網路安全性群組套用到其子網，以及針對應用程式的受信任埠和來源設定的網路存取控制。

- [將 Cloud Shell 部署到 Azure 虛擬網路](private-vnet.md)

- [如何建立具有安全性規則的網路安全性群組](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，其使用用來存取 Azure 入口網站的相同授權，在此案例中，SSO 進入 Azure 入口網站也會向您驗證 Cloud Shell。 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，其使用用來存取 Azure 入口網站的相同授權，在此情況下，Cloud Shell 也需要連線至 Azure 入口網站所需的任何 MFA。 

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射。

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或軟體。

客戶須負責針對在環境中執行的軟體執行自動化的弱點掃描工具。  

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導** 方針：不適用;Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射。

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或軟體。

客戶須負責在其環境中執行的軟體修補程式管理。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射。

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或軟體。

客戶須負責補救透過其軟體弱點掃描探索到的弱點。 以一致的間隔匯出掃描結果，並將結果與先前的掃描進行比較，以確認已補救弱點。 使用 Azure 資訊安全中心建議的弱點管理建議時，您可以在選取的解決方案入口網站中，切換到所選解決方案的入口網站以查看歷程記錄掃描資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射。

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或軟體。

客戶須負責補救透過其軟體弱點掃描探索到的弱點。  使用常見的風險評分方案 (例如，常見的弱點評分系統) ，或協力廠商掃描工具所提供的預設風險評等。 

- [NIST 發行集--常見的弱點評分系統](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射和工具。  客戶可以根據組織需求，在自己的映射中安裝自己的工具，而這些工具在 `sudo` 安裝期間不需要許可權。

建議客戶依據您的組織需求，建立透過 Azure Cloud Shell 安裝的已核准軟體清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針： Azure Cloud Shell 是免費服務，沒有任何客戶擁有的資產。  Cloud Shell 的小組會監視和更新容器映射和工具。 

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或軟體。

客戶須負責監視在環境中執行的軟體應用程式，以確定這些應用程式已依組織原則核准。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針： Azure Cloud Shell 是免費服務，沒有任何客戶擁有的資產。  Cloud Shell 的小組會監視和更新容器映射和工具。 

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或軟體。

客戶須負責監視在環境中執行的軟體應用程式，以確保每個組織的原則都能管理未核准的軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針： Azure Cloud Shell 是免費服務，沒有任何客戶擁有的資產。  Cloud Shell 的小組會監視和更新容器映射和工具。  客戶可能不會移除特定的工具。

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或應用程式。

客戶須負責監視在環境中執行的應用程式，以確定這些應用程式已依組織原則核准。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導** 方針：不適用;Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射。

Azure Cloud Shell 可讓客戶根據其組織需求，在自己的映射中安裝自己的工具或軟體。

客戶須負責維護環境中執行之已核准軟體的清查，以確定其為每個組織的核准軟體原則。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制使用者在計算資源中執行腳本的能力

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  在 Cloud Shell 中採取的動作與在本機環境中執行的相同工具或語言所採取的動作相同。  來自個別工具和語言的動作應受到限制，客戶無法限制對 Cloud Shell 的存取，或限制可供使用者使用的功能。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針： Azure Cloud Shell 可以在客戶虛擬網路中隔離。

- [將 Cloud Shell 部署到 Azure 虛擬網路](private-vnet.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導** 方針： Cloud Shell 可讓腳本在中執行、撰寫並上傳至 Cloud Shell 環境。  將認證移至 Azure Key Vault 是我們的建議。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射和工具。  客戶可以根據組織需求，在自己的映射中安裝自己的工具，而這些工具在 `sudo` 安裝期間不需要許可權。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3：確定已更新反惡意程式碼軟體和簽章

**指導** 方針： Azure Cloud Shell 是以瀏覽器為基礎的命令列體驗，可用來進行雲端資源的互動式管理。  每個客戶容器都是暫時的，每個會話都會使用新的容器。  Cloud Shell 的小組會監視和更新容器映射和工具。  客戶可以根據組織需求，在自己的映射中安裝自己的工具，而這些工具在 `sudo` 安裝期間不需要許可權。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。
- [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md) 
- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性是以資訊安全中心在尋找或 analytically 中的信心，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。
此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。
- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md) 
- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。
- [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。
- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。
- [如何設定連續匯出](../security-center/continuous-export.md) 
- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。
- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行方法。
- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)