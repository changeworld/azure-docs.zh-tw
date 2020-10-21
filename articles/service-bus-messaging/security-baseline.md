---
title: 適用于服務匯流排的 Azure 安全性基準
description: 服務匯流排安全性基準提供程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4e844904f236e9d81404a3481af9349ac8881085
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310443"
---
# <a name="azure-security-baseline-for-service-bus"></a>適用于服務匯流排的 Azure 安全性基準

適用于服務匯流排的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。 此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview-v1.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。 如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

若要查看服務匯流排如何完全對應至 Azure 安全性基準測試，請參閱 [完整的服務匯流排安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源 

**指導**方針：服務匯流排與 Azure Private Link 服務的整合可讓您安全地從工作負載（例如系結至虛擬網路的虛擬機器）對訊息功能進行私用存取。 建立與您的服務匯流排命名空間的私人端點連線。 私人端點會使用您虛擬網路中的私人 IP 位址，有效地將服務帶入您的虛擬網路。 服務的所有流量都可以透過該私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。

您也可以使用防火牆來保護 Azure 服務匯流排命名空間。 Azure 服務匯流排支援以 IP 為基礎的存取控制進行輸入防火牆支援。 您可以使用 Azure 入口網站、Azure Resource Manager 範本，或透過 Azure CLI 或 Azure PowerShell 來設定防火牆規則。

- [允許透過私人端點存取 Azure 服務匯流排命名空間](private-link-service.md)

- [允許從特定 IP 位址或範圍存取 Azure 服務匯流排命名空間](service-bus-ip-filtering.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導**方針：如果使用 Azure 虛擬機器來存取您的服務匯流排實體，請啟用網路安全性群組 (NSG) 流量記錄，並將記錄傳送至儲存體帳戶以進行流量審核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。 

使用 Azure 資訊安全中心，並遵循網路保護建議來協助保護 Azure 中的服務匯流排資源。

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

- [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：在與您的服務匯流排命名空間相關聯的虛擬網路上啟用 DDoS 保護標準，以防止分散式阻絕服務 (DDoS) 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

- [如何設定 DDoS 保護](../virtual-network/manage-ddos-protection.md)

- [Azure 資訊安全中心整合式威脅情報](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針：如果使用 Azure 虛擬機器來存取您的服務匯流排實體，您可以使用網路監看員封包捕獲來調查異常的活動。

- [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導**方針：如果使用 Azure 虛擬機器來存取您的服務匯流排實體，請從 Azure Marketplace 選取供應專案，以支援具有承載檢查功能的 IDS/IPS 功能。 如果您的組織不需要根據承載檢查進行入侵偵測和/或預防，您可以使用 Azure 服務匯流排的內建防火牆功能。 您可以使用防火牆規則來限制對您的服務匯流排命名空間的存取，以取得有限範圍的 IP 位址或特定 IP 位址。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何在服務匯流排命名空間中為指定的 IP 位址新增防火牆規則](service-bus-ip-filtering.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制，以篩選進出服務匯流排資源的流量。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如，「服務」) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。 

- [瞭解和使用服務標記](../virtual-network/service-tags-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則為與您的 Azure 服務匯流排命名空間相關聯的網路資源定義和執行標準安全性設定。 使用 "Microsoft. 伺服器名稱" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對服務匯流排命名空間的網路設定進行審核或強制執行。 您也可以使用與 Azure 服務匯流排相關的內建原則定義，例如：

- 服務匯流排應該使用虛擬網路服務端點
- 應在服務匯流排中啟用診斷記錄

如果內建定義不符合您組織的需求，您也可以建立自訂原則定義。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [適用于服務匯流排命名空間的 Azure 內建原則](./policy-reference.md#azure-service-bus-messaging)

- [適用于網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network)

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：使用虛擬網路的標記，以及與您的服務匯流排命名空間相關聯的網路安全性和流量流程相關的其他資源。 針對個別的網路安全性群組規則，請使用 [描述] 欄位來指定任何規則的商務需求、持續時間和其他描述性資訊，以允許與您的服務匯流排命名空間相關聯的網路流量。 

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。 

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。 

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md) 

- [如何建立虛擬網路](../virtual-network/quick-create-portal.md) 

- [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄監視網路資源設定，並偵測與 Azure 服務匯流排相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，以匯總服務匯流排資源所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析、設定長期或封存儲存體 Azure 儲存體帳戶。 您也可以設定與服務匯流排相關的記錄，以傳送給 Azure Sentinel 或協力廠商 SIEM。

- [如何設定 Azure 服務匯流排的診斷設定](service-bus-diagnostic-logs.md)

- [瞭解 Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md) 

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：啟用 Azure 服務匯流排命名空間的診斷設定。 Azure 服務匯流排目前支援活動和操作或診斷記錄。 活動記錄包含在作業上完成作業的相關資訊。 診斷記錄會提供更豐富的資訊，說明如何使用 API，或透過使用語言 SDK 的管理用戶端，對命名空間進行的作業和動作。 具體而言，這些記錄會擷取作業類型，包括建立佇列、使用的資源，以及作業狀態。

- [如何啟用 Azure 服務匯流排的診斷設定](service-bus-diagnostic-logs.md)

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：在 Azure 監視器中，您可以根據組織的合規性法規來設定 Log Analytics 工作區保留期限，以捕獲和審核與服務匯流排相關的事件。

- [如何設定 Log Analytics 工作區的記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：分析和監視記錄中的異常行為，並定期查看與服務匯流排實體相關的結果。 使用 Azure 監視器來檢查記錄，並對與服務匯流排相關的記錄資料執行查詢。

- [有關 Log Analytics 工作區的詳細資訊](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：搭配使用 Azure 資訊安全中心與 Log Analytics 工作區來監視和警示在安全性記錄和事件中找到的異常活動。 或者，您也可以啟用和內部資料以 Azure Sentinel。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： Azure 角色型存取控制 (RBAC) 可讓您透過角色指派來管理 azure 資源的存取權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 服務匯流排有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具進行清查或查詢。

- [Azure 服務匯流排的內建角色](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針：服務匯流排的控制平面存取權是透過 Azure Active Directory (Azure AD) 來控制。 Azure AD 沒有預設密碼的概念。

服務匯流排的資料平面存取權是透過使用受控識別、應用程式註冊或共用存取簽章的 Azure AD 來控制。 連接至您的服務匯流排命名空間的用戶端會使用共用存取簽章，而且可以隨時重新產生。

- [瞭解服務匯流排的共用存取簽章](service-bus-sas.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

如果內建定義不符合您組織的需求，您也可以建立自訂原則定義。

- [如何使用 Azure 資訊安全中心來監視身分識別和存取](../security-center/security-center-identity-access.md)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導**方針： Microsoft Azure 會根據 Azure Active Directory (Azure AD) ，提供資源與應用程式的整合式存取控制管理。 使用 Azure AD 搭配 Azure 服務匯流排的主要優點是您不再需要將認證儲存在程式碼中。 相反地，您可以從 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 要求權杖的資源名稱是 https://azure.microsoft.com/services/service-bus/ 。 Azure AD 會 (使用者、群組或服務主體) 執行應用程式來驗證安全性主體。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 服務匯流排資源的要求。

- [如何使用 Azure AD 來驗證應用程式，以存取服務匯流排資源](authenticate-application.md)

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導**方針：啟用 Azure Active Directory MULTI-FACTOR AUTHENTICATION (MFA) ，並遵循 Azure 資訊安全中心身分識別和存取管理的建議，以協助保護已啟用服務匯流排的資源。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指導**方針：使用特殊許可權存取工作站 (PAW) 與 MULTI-FACTOR AUTHENTICATION (MFA) 設定為登入和設定啟用服務匯流排的資源。

- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：使用 Azure Active Directory 安全性報告和監視，來偵測環境中發生可疑或不安全的活動時。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用 Azure AD 命名位置，只允許來自 IP 位址範圍或國家/地區之特定邏輯群組的存取。 

- [如何設定 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為 Azure 資源（例如服務匯流排）的中央驗證和授權系統。 這可讓 Azure 角色型存取控制 (Azure RBAC) 至系統管理的機密資源。

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [使用 Azure Active Directory 授權存取服務匯流排資源](authenticate-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： Azure Active Directory (Azure AD) 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

另外，請定期輪替您的服務匯流排命名空間的共用存取簽章。

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

- [瞭解服務匯流排命名空間的共用存取簽章](service-bus-sas.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導**方針：您可以存取 Azure Active Directory (Azure AD) 登入活動、audit 和風險事件記錄檔來源，讓您可以與 Azure Sentinel 或協力廠商 SIEM 工具整合。

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 然後在 Azure 監視器您可以針對記錄檔中發生的特定動作，設定所需的記錄警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [使用 Azure Active Directory 授權存取服務匯流排資源](authenticate-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory 的身分識別保護和風險偵測功能，來設定偵測到與已啟用服務匯流排之資源相關的可疑動作的自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行您組織的安全性回應。

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：目前無法使用;服務匯流排尚不支援客戶加密箱。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導**方針：在與您的服務匯流排相關的資源上使用標籤，以協助追蹤儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：針對開發、測試和生產環境執行個別的訂用帳戶和管理群組。 服務匯流排命名空間應由已設定並適當標記私人端點的虛擬網路分隔。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立及利用標記](../azure-resource-manager/management/tag-resources.md)

- [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：使用虛擬機器存取您的服務匯流排實體時，請使用虛擬網路、私人端點、服務匯流排防火牆、網路安全性群組和服務標籤來減輕資料遭到外泄的可能性。

Microsoft 管理 Azure 服務匯流排的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [設定 Azure 服務匯流排命名空間的 IP 防火牆規則](service-bus-ip-filtering.md)

- [允許從特定虛擬網路存取 Azure 服務匯流排命名空間](private-link-service.md)

- [允許透過私人端點存取 Azure 服務匯流排命名空間](private-link-service.md)

- [瞭解網路安全性群組和服務標記](../virtual-network/network-security-groups-overview.md)

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針： Azure 服務匯流排預設會強制執行 TLS 加密通訊。 目前支援 TLS 版本1.0、1.1 和1.2。 不過，TLS 1.0 和1.1 是在整個產業淘汰的路徑上，因此請盡可能使用 TLS 1.2 或更新版本。

- [若要瞭解服務匯流排的安全性功能，請參閱網路安全性](network-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure 服務匯流排尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用角色型存取控制來控制資源的存取權

**指導**方針： Azure 服務匯流排支援使用 Azure Active Directory (Azure AD) 來授權服務匯流排實體的要求。 使用 Azure AD 時，您可以使用 Azure 角色型存取控制 (Azure RBAC) ，將許可權授與安全性主體（可能是使用者或應用程式服務主體）。

- [瞭解適用于 Azure 服務匯流排的 Azure RBAC 和可用角色](authenticate-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針： Azure 服務匯流排支援使用 Microsoft 管理的金鑰或由客戶管理的金鑰來加密待用資料的選項。 這項功能可讓您建立、輪替、停用及撤銷客戶管理金鑰的存取權，這些金鑰是用來加密待用 Azure 服務匯流排資料。

- [如何設定客戶管理的金鑰來加密 Azure 服務匯流排](configure-customer-managed-key.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對 Azure 服務匯流排和其他重要或相關資源的生產實例進行變更時，建立警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢及探索所有資源， (包括訂用帳戶內) 的 Azure 服務匯流排命名空間。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別的訂用帳戶（如果適當）來組織和追蹤 Azure 服務匯流排命名空間和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：根據您的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型

- 允許的資源類型

此外，請使用 Azure Resource Graph 來查詢和探索訂用帳戶內的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型
- 允許的資源類型

如果內建定義不符合您組織的需求，您也可以建立自訂原則定義。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：定義和實行 Azure 服務匯流排部署的標準安全性設定。 您也可以使用內建原則定義進行 Azure 服務匯流排例如：

- 應在服務匯流排中啟用診斷記錄
- 服務匯流排應該使用虛擬網路服務端點來限制對您私人網路的網路流量。

使用 "node.js" 命名空間中 Azure 原則別名來建立自訂原則，以進行審核或強制執行設定。

- [適用于服務匯流排的 Azure 內建原則 ](./policy-reference.md)

- [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在]，在啟用服務匯流排的資源或應用程式之間強制執行安全設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如需 Azure 原則效果的詳細資訊](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：使用 "node.js" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用 "node.js" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[拒絕] 和 [部署是否不存在]，自動強制執行 Azure 服務匯流排部署和相關資源的設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：適用于在 Azure App Service 上執行的 Azure 虛擬機器或 web 應用程式可用來存取您的服務匯流排實體，請使用受控服務識別搭配 Azure Key Vault，以簡化和保護 Azure 服務匯流排部署的共用存取簽章管理。 確定已啟用 Key Vault 虛刪除。

- [使用 Azure Active Directory 來驗證受控識別，以存取服務匯流排資源](service-bus-managed-service-identity.md)

- [為服務匯流排設定客戶管理的金鑰](configure-customer-managed-key.md)

- [如何建立 Key Vault](../key-vault/secrets/quick-create-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：適用于在 Azure App Service 上執行的 Azure 虛擬機器或 web 應用程式，可用來存取您的服務匯流排實體，請使用受控服務識別搭配 Azure Key Vault，以簡化 Azure 服務匯流排的安全。 確定已啟用 Key Vault 虛刪除。

使用受控識別，在 Azure Active Directory (Azure AD) 中，為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向任何支援 Azure AD 驗證的服務進行驗證，包括 Azure Key Vault，而不需要在您的程式碼中提供任何認證。

- [使用 Azure Active Directory 來驗證受控識別，以存取服務匯流排資源](service-bus-managed-service-identity.md)

- [為服務匯流排設定客戶管理的金鑰](configure-customer-managed-key.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：預先掃描任何上傳至非計算 Azure 資源的內容，例如 Azure 服務匯流排、App Service、Data Lake Storage、Blob 儲存體、適用於 PostgreSQL 的 Azure 資料庫等。Microsoft 無法在這些實例中存取您的資料。

支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導**方針：設定 Azure 服務匯流排的異地災難復原。 當整個 Azure 區域或資料中心 (如果未使用可用性區域) 遇到停機時，最重要的是資料處理作業能夠繼續在不同的區域或資料中心運作。 因此，「地理災害復原」和「異地複寫」對於任何企業而言都是重要的功能。 Azure 服務匯流排支援命名空間層級的地理災害復原和異地複寫。

- [瞭解 Azure 服務匯流排的異地災難復原](service-bus-geo-dr.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針： Azure 服務匯流排使用 Azure SSE)  (的 Azure 儲存體服務加密，提供待用資料加密。 服務匯流排依賴 Azure 儲存體儲存資料，而以 Azure 儲存體儲存的所有資料都會使用 Microsoft 管理的金鑰進行加密。 如果您使用 Azure Key Vault 儲存客戶管理的金鑰，請確定金鑰的定期自動備份。

使用下列 PowerShell 命令，確保 Key Vault 秘密的定期自動備份： Backup-AzKeyVaultSecret

- [如何設定客戶管理的金鑰來加密待用 Azure 服務匯流排資料](configure-customer-managed-key.md)

- [如何備份 Key Vault 秘密](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：備份客戶管理金鑰的測試還原，可用來加密服務匯流排資料。

- [如何設定客戶管理的金鑰來加密待用 Azure 服務匯流排資料](configure-customer-managed-key.md)

- [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：啟用 Key Vault 中的虛刪除，以防止金鑰遭到意外或惡意刪除。 Azure 服務匯流排需要客戶管理的金鑰才能進行虛刪除，且不會清除設定。

- [如何在 Key Vault 中啟用虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [使用金鑰設定金鑰保存庫](../event-hubs/configure-customer-managed-key.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：為您的組織開發事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以進行事件後的評論。 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，使用標記標記訂用帳戶，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料。 您必須負責根據發生事件的 Azure 資源和環境的重要性來排定補救警示的優先順序。 

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md) 

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導**方針：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

- [NIST 的發行集：適用于 IT 方案和功能的測試、訓練和練習程式指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。 

- [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導**方針：使用連續匯出功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動方式或持續持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。 

- [如何設定連續匯出](../security-center/continuous-export.md) 

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針： 

使用工作流程自動化功能 Azure 資訊安全中心自動觸發安全性警示和建議的回應，以保護您的 Azure 資源。 

- [如何在安全性中心設定工作流程自動化](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。 

- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)