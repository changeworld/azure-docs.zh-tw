---
title: 適用于內容傳遞網路的 Azure 安全性基準
description: 內容傳遞網路安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: azure-cdn
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 184f42b6da0133068e0a0218353e49c37e3c8c47
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730250"
---
# <a name="azure-security-baseline-for-content-delivery-network"></a>適用于內容傳遞網路的 Azure 安全性基準

此安全性基準會將來自 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至內容傳遞網路。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于內容傳遞網路的相關指引來分組。 尚未排除適用于內容傳遞網路的 **控制項**。

若要瞭解內容傳遞網路如何完全對應至 Azure 安全性基準測試，請參閱 [完整的內容傳遞網路安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="identity-management"></a>身分識別管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：身分識別管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針：依國家或地區限制對內容傳遞網路內容的存取。 在 CDN 端點上建立特定路徑的規則，以允許或封鎖所選國家或地區中使用地區篩選功能的內容。

- [依國家或地區限制 Azure CDN 內容](cdn-restrict-access-by-country.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="privileged-access"></a>特殊權限存取

如需詳細資訊，請參閱 [Azure 安全性效能評定：特殊權限存取](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對業務關鍵系統的系統管理存取

**指導** 方針：使用 azure 角色型存取控制 (內容傳遞網路中的 azure RBAC) ，藉由限制哪些帳戶被授與存取權給他們所在的訂用帳戶和管理群組，來隔離對商務關鍵性系統的存取。

也可限制存取管理、身分識別和安全性系統，這些系統具有您商務關鍵性存取權的系統管理存取權，例如 Active Directory 網網域控制站 (Dc) 、安全性工具和系統管理工具，以及安裝在商務關鍵性系統上的代理程式。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理群組存取](../governance/management-groups/overview.md#management-group-access) 

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊權限存取工作站

**指引**：安全、隔離的工作站對於敏感性角色 (例如系統管理員、開發人員及重要服務操作員) 的安全性來說至關重要。 使用高度安全的使用者工作站及/或 Azure 防禦來進行系統管理工作。 使用 Azure Active Directory (Azure AD) 、Microsoft Defender 進階威脅防護 (ATP) 和/或 Microsoft Intune 部署安全且受管理的使用者工作站以進行系統管理工作。 受保護的工作站可以集中管理以施行安全設定，包括增強式驗證、軟體和硬體基準、受限的邏輯和網路存取。

- [瞭解特殊許可權的存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [部署特殊權限存取工作站](/security/compass/privileged-access-deployment) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循恰到好處的系統管理 (最低權限原則) 

**指導** 方針：內容傳遞網路會與 Azure 角色型存取控制整合 (RBAC) 來管理其資源。 使用 Azure RBAC 透過角色指派來管理 Azure 資源存取權。 將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站之類的工具進行清查或查詢。 

依據角色的要求，限制您透過 Azure RBAC 指派給資源的許可權。 這類權限可補強 Azure AD Privileged Identity Management (PIM) 的 Just-In-Time (JIT) 方法，且您應定期加以檢閱。 

此外，您也可以使用內建角色來配置許可權，並只在必要時才建立自訂角色。

- [什麼是 Azure 角色型存取控制 (Azure RBAC) ](../role-based-access-control/overview.md) 

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

## <a name="asset-management"></a>資產管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：資產管理](../security/benchmarks/security-controls-v2-asset-management.md)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：確保安全性小組能夠看到資產的風險

**指導** 方針：確定安全性小組已獲得 Azure 租使用者和訂用帳戶中的安全性讀取者許可權，以便他們使用 Azure 資訊安全中心監視安全性風險。 

根據安全性小組的權責架構，監視安全性風險可能是由中央安全性小組或區域小組負責。 不過，安全性見解和風險一律必須在組織內集中匯總。 

「安全性讀取者」權限可以廣泛套用至整個租用戶 (根管理群組)，或將範圍限定於管理群組或特定訂用帳戶。 

注意：您可能需要額外的許可權，才能看到工作負載和服務。 

- [安全性讀取者角色概觀](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組概觀](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：確保安全性小組可以存取資產清查和中繼資料

**指導** 方針：將標記套用至您的 Azure 資源、資源群組和訂用帳戶，以邏輯方式將它們組織成分類法。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md) 

- [Azure 資訊安全中心資產庫存管理](../security-center/asset-inventory.md) 

- [資源命名與標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來審核和限制使用者可以在您的環境中布建的服務。 使用 Azure Resource Graph 則可查詢及探索其訂用帳戶內的資源。 您也可以使用 Azure 監視器來建立規則，以在偵測到未核准的服務時觸發警示。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/built-in-policies.md#general) 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：確保資產生命週期管理的安全性

**指導** 方針：內容資料網路無法用於確保生命週期管理程式中資產的安全性。 客戶必須負責維護資產的屬性和網路設定，這些資產會被視為高衝擊。 建議客戶建立程式來捕獲屬性和網路設定變更、測量變更影響，以及建立補救工作（適用的話）。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄與威脅偵測

如需詳細資訊，請參閱 [Azure 安全性效能評定：記錄與威脅偵測](../security/benchmarks/security-controls-v2-data-protection.md)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：啟用 Azure 資源的威脅偵測

**指導** 方針：內容傳遞網路不提供原生功能來監視與其資源相關的安全性威脅。

將任何記錄從內容傳遞網路轉送至您的安全性資訊和事件管理 (SIEM) 解決方案，可用來設定自訂威脅偵測。 

監視不同類型的 Azure 資產是否有潛在威脅和異常，並著重于取得高品質的警示，以減少因分析師進行排序所需的誤報。 警示可能源自于記錄資料、代理程式或其他資料。

- [建立自訂分析規則來偵測威脅](../sentinel/tutorial-detect-threats-custom.md) 

- [使用 Azure Sentinel 的網路威脅情報](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

**指導** 方針：內容傳遞網路不適合部署到虛擬網路，因為這樣您無法啟用網路安全性群組流量記錄、透過防火牆路由傳送流量或執行封包捕獲。

內容傳遞網路會記錄它為客戶存取所處理的所有網路流量。 在您已部署的供應專案資源內啟用網路流程功能，並設定將這些記錄傳送至儲存體帳戶，以進行長期保留和審核。

- [教學課程-如何設定 Azure 內容傳遞網路中的診斷記錄](cdn-azure-diagnostic-logs.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄功能

**指導** 方針：自動提供的活動記錄，包含內容傳遞網路資源的所有寫入作業 (PUT、POST、DELETE) ，但讀取作業 (取得) 。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

啟用 CDN 的 Azure 資源記錄檔，您可以使用 Azure 資訊安全中心和 Azure 原則來啟用資源記錄和記錄資料收集。 這些記錄檔可能很重要，可供稍後調查安全性事件和執行法庭訓練。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md) 

- [瞭解 Azure 資訊安全中心資料收集](../security-center/security-center-enable-data-collection.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱 [Azure 安全性效能評定：事件回應](../security/benchmarks/security-controls-v2-incident-response.md)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備 – 更新 Azure 的事件回應流程

**指引**：確定您的組織具有回應安全性事件的流程、已更新 Azure 的這些處理序，而且會定期執行以確保就緒。

- [在企業環境中實作安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf) (英文)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備 – 設定事件通知

**指引**：在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 當 Microsoft 安全回應中心 (MSRC) 發現您的資料已遭非法或未經授權的合作對象存取時，Microsoft 會使用此連絡人資訊來與您連絡。 您也可以選擇根據事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析 – 根據高品質警示建立事件

**指引**：確定您具有建立高品質警示和測量警示品質的程序。 這可讓您從過去的事件中汲取經驗，並且為分析師設定警示的優先順序，以避免浪費時間處理誤報。 

您可以根據從過去的事件獲取的經驗、已驗證的社群來源，以及可藉由融合和相互關聯不同訊號來源而產生和清除警示的工具，來建立高品質的警示。 

Azure 資訊安全中心可在許多 Azure 資產間提供高品質的警示。 您可以使用 ASC 資料連接器將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立進階警示規則，以自動產生事件供調查之用。 

使用匯出功能匯出 Azure 資訊安全中心警示和建議，以利找出 Azure 資源的風險。 以手動或持續不斷的方式匯出警示和建議。

- [如何設定匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測和分析 – 調查事件

**指引**：確定分析師在調查潛在的事件時可查詢和使用各種資料來源，以全面觀照發生的狀況。 您應收集各種記錄，以追蹤整個攻擊鏈中潛在攻擊者的活動，而避免產生盲點。  您也應確實獲取深入解析和知識，以供其他分析師和未來的歷程記錄參考使用。  

調查的資料來源包括從範圍內的服務和執行中的系統收集到的集中式記錄來源，但也可包括：

- 網路資料 – 使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器，擷取網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能，建立執行中系統磁碟的快照集。 

    - 使用作業系統的原生記憶體傾印功能，建立執行中系統記憶體的快照集。

    - 使用 Azure 服務的快照集功能或軟體本身的功能，建立執行中系統的快照集。

Azure Sentinel 可讓您對絕大多數的記錄來源進行廣泛的資料分析，並提供案例管理入口網站來管理事件的完整生命週期。 調查期間的情報資訊可以與事件相關聯，以供追蹤和報告之用。 

- [為 Windows 機器的磁碟建立快照集](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [為 Linux 機器的磁碟建立快照集](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測與分析 – 設定事件的優先順序

**指引**：根據警示嚴重性和資產敏感度，為分析師提供應優先關注哪些事件的內容。 

Azure 資訊安全中心會指派每個警示的嚴重性，以協助您設定應優先調查哪些警示。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，請使用標籤來標示資源，並建立命名系統以識別及分類 Azure 資源，尤其是處理敏感資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：圍堵、根除及復原 – 將事件處理自動化

**指引**：將手動重複的工作自動化，以縮短回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行，進而降低每個事件的速度，以及減少分析師可以處理的事件數目。 手動工作也會使分析師更加疲勞，而增加人為錯誤導致延遲的風險，並降低分析師有效專注處理複雜工作的能力。 請使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能來自動觸發動作，或執行劇本以回應傳入的安全性警示。 劇本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在資訊安全中心設定工作流程自動化](../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心設定自動化威脅回應](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="posture-and-vulnerability-management"></a>狀況和弱點管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：狀況和弱點管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)。

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：建立計算資源的安全設定

**指導** 方針：不適用;內容傳遞網路沒有提供任何安全性設定功能。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自動補救軟體弱點

**指引**：快速部署軟體更新，以補救作業系統和應用程式中的軟體弱點。 

使用常見的風險評分方案設定補救的優先順序 (例如，常見的弱點評分系統) ，或協力廠商掃描工具所提供的預設風險評等。 使用應用程式的內容呈現高安全性風險，以及哪些應用程式需要高執行時間，以量身打造您的環境。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：執行一般攻擊模擬

**指引**：如有需要，請對您的 Azure 資源執行滲透測試或紅隊活動，並確保補救所有重要的安全性結果。
請遵循 Microsoft 雲端滲透測試參與規則，以確保您的滲透測試不會違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md) (機器翻譯)

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="governance-and-strategy"></a>控管與策略

如需詳細資訊，請參閱 [Azure 安全性效能評定：治理和策略](../security/benchmarks/security-controls-v2-governance-strategy.md)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理和資料保護策略 

**指引**：務必記載並傳達明確的策略，以持續監視及保護系統和資料。 請優先探索、評估、保護及監視業務關鍵資料和系統。 

此策略應該包含下列項目的已記載指引、原則和標準： 

-   符合商業風險的資料分類標準

-   安全性組織對風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用的程序 

-   資產在其生命週期中的安全性

-   符合組織資料分類的必要存取控制策略

-   Azure 原生和第三方資料保護功能的使用

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

您可以在參考的連結取得詳細資訊。

- [Azure 安全性架構建議 - 儲存體、資料和加密](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (機器翻譯)

- [Azure 安全性基礎觀念 - Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md) (機器翻譯)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (機器翻譯)

- [Azure 安全性效能評定 - 資產管理](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure 安全性效能評定 - 資料保護](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指引**：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立企業範圍的策略來分割對產的存取。

審慎權衡安全性區隔的需求，以及需要與彼此通訊並存取資料的啟用每日作業需求。

確保在各種控制項類型上一致地實作分割策略，這些控制項類型包括網路安全性、身分識別和存取模型，以及應用程式權限/存取模型與人力流程控制。

- [Azure 中的分割策略指引 (影片)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) (機器翻譯)

- [Azure 中的分割策略指引 (文件)](/security/compass/governance#enterprise-segmentation-strategy) (機器翻譯)

- [使用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理策略

**指引**：持續測量並降低個別資產及其裝載環境的風險。 優先處理高價值資產和高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性效能評定 - 狀態和弱點管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：協調組織角色、責任及權責

**指引**：務必為安全性組織中的角色和責任記載並傳達清楚的策略。 優先為安全性決策提供清楚的權責、讓每個人熟知共同責任模型，並讓技術團隊熟知保護雲端的技術。

- [Azure 安全性最佳做法 1 – 人員：讓小組熟知雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (機器翻譯)

- [Azure 安全性最佳做法 2 - 人員：讓小組熟知雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (機器翻譯)

- [Azure 安全性最佳做法 3 - 流程：指派雲端安全性決策的權責](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指引**：建立 Azure 網路安全性方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式網路管理和安全性責任

-   與企業分割策略一致的虛擬網路分割模型

-   不同威脅和攻擊案例的補救策略

-   網際網路邊緣和輸入與輸出策略

-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性成品 (例如網路圖、參考網路架構)

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 安全性效能評定 - 網路安全性](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [企業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊權限存取策略

**指引**：建立 Azure 身分識別和特殊權限存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同使用案例和條件中的增強式驗證方法

-   高權限使用者的保護

-   異常使用者活動監視和處理  

-   使用者身分識別、存取權檢閱與核對流程

您可以在參考的連結取得詳細資訊。

- [Azure 安全性效能評定 - 身分識別管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure 安全性效能評定 - 特殊權限存取](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指引**：建立記錄和威脅回應策略，以快速偵測並補救威脅，同時符合合規性需求。 優先為分析師提供高品質的警示和順暢的體驗，使其能夠全心處理威脅，而非整合和手動步驟。 

此策略應包含針對下列元素而記載的指引、原則和標準：

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義且與 NIST 或其他產業架構一致的事件回應流程 

-   記錄擷取和保留，以支援威脅偵測、事件回應及合規性需求

-   使用 SIEM、原生 Azure 功能及其他來源，集中顯示及相互關聯威脅的相關資訊 

-   與客戶、供應商和相關公開合作對象之間的溝通和通知計畫

-   使用 Azure 原生和協力廠商平台進行事件處理，例如記錄和威脅偵測、鑑定，以及攻擊補救和根除

-   處理事件和事件後活動的流程，例如吸取的經驗和證據保留

您可以在參考的連結取得詳細資訊。

- [Azure 安全性效能評定 - 記錄與威脅偵測](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure 安全性效能評定 - 事件回應](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 安全性最佳做法 4 - 流程。更新雲端的事件回應流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [Azure 採用架構、記錄與報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理與監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定 V2 概觀](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)