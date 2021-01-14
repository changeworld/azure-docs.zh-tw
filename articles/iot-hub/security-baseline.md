---
title: 適用于 Azure IoT 中樞的 Azure 安全性基準
description: Azure IoT 中樞安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 862e8f07e321975ec5b673cfe5c86de85c9d160b
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202295"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>適用于 Azure IoT 中樞的 Azure 安全性基準

適用于 Microsoft Azure IoT 中樞的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。 此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。 如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：根據預設，IoT 中樞的主機名稱會對應至具有可透過網際網路公開路由傳送 IP 位址的公用端點。 不同客戶會共用此 IoT 中樞公用端點，且廣域網路和內部部署網路中的 IoT 裝置全部都可進行存取。

IoT 中樞功能 (包括訊息路由、檔案上傳和大量裝置匯入/匯出) 也需要從 IoT 中樞透過其公用端點連線到客戶擁有的 Azure 資源。 這些連線路徑共同構成從 IoT 中樞到客戶資源的輸出流量。

建議您限制對 Azure 資源的連線， (包括 Azure IoT 中樞) 透過您所擁有和操作的虛擬網路，以減少隔離網路中的連線能力，並讓內部部署網路直接連線到 Azure 骨幹網路。 使用 Azure Private Link 和 Azure 私人端點（如果可行），以啟用從其他虛擬網路對您的服務的私用存取。

將裝置上的開啟硬體埠維持在最少的最小值，以避免不必要的存取。 此外，也會建立防止或偵測裝置實體篡改的機制。

- [IoT 虛擬網路支援](virtual-network-support.md)
- [大量網路的最佳作法](../iot-fundamentals/security-recommendations.md?context=azure%2fiot-hub%2frc%2frc#networking)
- [Azure Private Link 總覽](../private-link/private-link-overview.md)
- [Azure 網路安全性群組](../virtual-network/network-security-groups-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針：使用 Azure 資訊安全中心，並遵循網路保護建議來協助保護您的 Azure 網路資源。 啟用網路安全性群組流量記錄，並將記錄傳送至 Azure 儲存體帳戶進行審核。 您也可以將流量記錄傳送至 Log Analytics 工作區，然後使用流量分析來提供 Azure 雲端中的流量模式見解。 流量分析的一些優點是能夠將網路活動視覺化、找出熱點和安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。
 
- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md)
 
- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導** 方針：使用 IOT 中樞 IP 篩選規則封鎖已知的惡意 ip。 惡意嘗試也會透過適用於 IoT 的 Azure 資訊安全中心進行記錄和警示。

Azure DDoS 保護 Basic 已啟用，且在 IoT 中樞內不會有額外成本。 永遠開啟的流量監視，以及常見網路層級攻擊的即時風險降低，可提供 Microsoft 線上服務所使用的相同防禦。 您可以使用 Azure 全球網路的整個規模來分散和降低跨區域的攻擊流量。

- [IoT 中樞 IP 篩選器](iot-hub-ip-filtering.md)

- [適用於 IoT 的 Azure 資訊安全中心可疑 IP 位址通訊](../defender-for-iot/concept-security-alerts.md)

- [管理 Azure DDoS 保護基本](../ddos-protection/ddos-protection-overview.md)

- [Azure 資訊安全中心內的威脅防護](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：不適用;這項建議適用于產生可供客戶記錄和查看之網路封包的供應專案。 IoT 中樞不會產生面向客戶的網路封包，且不會直接部署到 Azure 虛擬網路。

**Azure 資訊安全中心監視**：否

**責任**：不適用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：從 Azure Marketplace 選取供應專案，以支援具有承載檢查功能的 IDS/IPS 功能。  如果不需要承載檢查，則可以使用 Azure 防火牆威脅情報。 Azure 防火牆威脅情報型篩選是用來警示和/或封鎖來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路界限上部署您所選擇的防火牆解決方案，以偵測及/或封鎖惡意流量。 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

- [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導** 方針：對於需要存取 Azure IoT 中樞的資源，請使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由指定服務標籤名稱 (例如，AzureIoTHub) 在規則的適當來源或目的地欄位中，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [如何使用 Azure IoT 的服務標記](iot-hub-understand-ip-address.md)
- [如需使用服務標記的詳細資訊](../virtual-network/service-tags-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：使用 Azure 原則為與您的 Azure IoT 中樞命名空間相關聯的網路資源定義和執行標準安全性設定。 使用 "Microsoft. Devices" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Machine Learning 命名空間的網路設定進行審核或強制執行。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對與您 Azure IoT 中樞部署相關聯的網路資源使用標籤，以便以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視網路資源設定，並偵測與 Azure IoT 中樞相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的 Azure IoT 中樞。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌記錄，以匯總 Azure IoT 中樞所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用儲存體帳戶來儲存長期/封存儲存體。 或者，您可以啟用和內部資料來 Azure Sentinel 或協力廠商安全性事件和事件管理 (SIEM) 。

- [設定 Azure IoT 記錄](monitor-iot-hub-reference.md#resource-logs)
- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用 azure 資源上的 azure IoT 診斷設定，以存取 audit、security 和資源記錄。 活動記錄 (自動提供) 包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他有用的項目。

- [設定 Azure IoT 中樞記錄](monitor-iot-hub-reference.md#resource-logs)

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，根據您組織的合規性法規，設定與您 Azure IoT 中樞實例相關聯的 log Analytics 工作區記錄保留期限。

- [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：分析和監視記錄中的異常行為，並定期檢查您 Azure IoT 中樞的結果。 使用 Azure 監視器與 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。

或者，您可以啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM。 

- [監視 Azure IoT 健康情況](monitor-iot-hub.md)
- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)
  
- [開始使用 Log Analytics 查詢](../azure-monitor/log-query/log-analytics-tutorial.md)
   
- [ 如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：使用適用於 IoT 的 Azure 資訊安全中心與 Log Analytics 工作區進行監視，並針對在安全性記錄檔和事件中找到的異常活動發出警示。 或者，您可以啟用和內部資料以 Azure Sentinel。 您也可以使用可能會造成安全性影響的 Azure 監視器來定義操作警示，例如，當流量意外下降時。

- [監視 Azure IoT 中樞健康情況](monitor-iot-hub.md)
- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)
- [適用於 IoT 的 Azure 資訊安全中心警示](../defender-for-iot/concept-security-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用;Azure IoT 中樞不會處理或產生反惡意程式碼的相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用;Azure IoT 中樞不會處理或產生 DNS 相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： azure 角色型存取控制 (azure RBAC) 可讓您透過角色指派來管理 azure IoT 中樞的存取權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，而這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具進行清查或查詢。 

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure IoT 中樞資源的存取管理是透過 Azure Active Directory (Azure AD) 來控制。 Azure AD 沒有預設密碼的概念。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。

您也可以使用 Azure AD Privileged Identity Management 和 Azure Resource Manager 來啟用系統管理帳戶的即時存取。

- [深入瞭解 Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：若要存取 IoT 中樞的使用者，請使用 Azure Active Directory SSO。 使用 Azure 資訊安全中心身分識別和存取建議。 

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure AD MFA 來保護您的整體 Azure 租使用者，以受益于所有服務。 IoT 中樞服務沒有 MFA 支援。  

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用安全且受 Azure 管理的工作站 (也稱為「特殊許可權存取工作站」，或 PAW 需要較高許可權的系統管理工作) 。

- [瞭解安全、受 Azure 管理的工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何啟用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：使用 Azure Active Directory 安全性報告和監視，來偵測環境中發生可疑或不安全的活動時。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)
- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：對於存取 IoT 中樞的使用者，不支援條件式存取。 若要減輕此問題，請使用 Azure AD 命名位置，只允許來自您整體 Azure 租使用者的 IP 位址範圍或國家/地區特定邏輯群組，受益所有服務（包括 IoT 中樞）。 

- [如何設定 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：若要讓使用者存取 IoT 中樞，請使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

針對裝置和服務存取，IoT 中樞會使用安全性權杖和共用存取簽章 (SAS) 權杖來驗證裝置和服務，以避免在網路上傳送金鑰。 

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT 中樞安全性權杖](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，使用 Azure AD 身分識別和存取評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。 

當環境中發生可疑或不安全的活動時，請使用 Azure AD Privileged Identity Management (PIM) 來產生記錄和警示。

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)
- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)
- [部署 Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。 

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 工作區中設定所需的警示。 

使用者 Azure 監視器資源記錄檔，以在 [連線] 類別中監視未授權的連接嘗試。

- [如何整合 Azure 活動記錄與 Azure 監視器](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [設定 IoT 中樞的資源記錄](monitor-iot-hub.md#collection-and-routing)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 Azure AD Identity Protection 功能，對偵測到與使用者身分識別相關的可疑動作設定自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。
  
- [ 如何查看 Azure AD 具風險的登入](../active-directory/identity-protection/overview-identity-protection.md)
  
- [ 如何設定及啟用 Identity Protection 風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ 如何上架 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：在 Microsoft 需要存取客戶資料的支援案例中，系統會直接向客戶要求。 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。
 
- [ 如何建立和使用標記](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：針對個別的安全性網域（例如環境類型和資料敏感度層級），使用不同的訂用帳戶和管理群組來執行隔離。 您可以限制您的應用程式和企業環境所需的 Azure 資源存取層級。 您可以透過 Azure RBAC 來控制對 Azure 資源的存取。
  
- [ 如何建立額外的 Azure 訂用帳戶](../cost-management-billing/manage/create-subscription.md)
- [ 如何建立管理群組](../governance/management-groups/create-management-group-portal.md)
- [ 如何建立和使用標記](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：從網路周邊的 Azure Marketplace 使用協力廠商解決方案，以監視是否有未經授權的機密資訊傳輸，並在警示資訊安全專業人員時封鎖這類傳輸。

針對 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針： iot 中樞會使用傳輸層安全性 (TLS) ，以保護來自 IoT 裝置和服務的連線。 目前支援三種 TLS 通訊協定版本，亦即 1.0、1.1 和 1.2 版。 強烈建議在連線到 IoT 中樞時，使用 TLS 1.2 作為慣用的 TLS 版本。

遵循 Azure 資訊安全中心待用加密及傳輸中加密的建議（適用時）。

- [IoT 中樞的 TLS 支援](iot-hub-tls-support.md)
- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure IoT 中樞尚無法使用資料識別、分類和遺失防護功能。 如有必要，請將協力廠商解決方案實行于合規性用途。

針對 Microsoft 所管理的基礎 Azure 平臺，Microsoft 會將所有客戶內容視為機密資料，並將其移至絕佳的長度，以防止客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 來管理資源的存取權

**指導** 方針：若要讓控制平面使用者存取 IoT 中樞，請使用 Azure RBAC 來控制存取權。 針對 IoT 中樞的資料平面存取權，請使用 IoT 中樞的共用存取原則。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [控制 IoT 中樞的存取權](iot-hub-devguide-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項準則主要用於計算資源。

Microsoft 管理 Azure IoT 中樞的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針： IoT 中樞支援使用客戶管理的金鑰來加密待用資料 (CMK) ，也稱為「自備金鑰」 (BYOK) 。

Azure IoT 中樞提供待用資料和傳輸中資料的加密，因為它會寫入我們的資料中心，並在您存取資料時加以解密。 根據預設，IoT 中樞會使用 Microsoft 管理的金鑰來加密待用資料。

- [使用 IoT 中樞客戶管理的金鑰來加密待用資料](iot-hub-customer-managed-keys.md)

- [了解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對 Azure IoT 中樞和其他重要或相關資源的生產實例進行變更時，建立警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：不適用;Microsoft 會在支援 Azure IoT 中樞的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針：不適用;Microsoft 會在支援 Azure IoT 中樞的基礎系統上執行修補程式管理。 

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：將標記套用至 Azure 資源 (並非所有資源都支援標籤，但大部分的) 會以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：在適當的情況下使用標記、管理群組和個別訂用帳戶來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。
  
- [ 如何建立額外的 Azure 訂用帳戶](../cost-management-billing/manage/create-subscription.md)
  
- [ 如何建立管理群組](../governance/management-groups/create-management-group-portal.md)
  
- [ 如何建立和使用標記](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據您的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的已核准軟體。

每個 IoT 中樞都有一個身分識別登錄，可用來在服務中建立每個裝置的資源。 個別或群組的裝置身分識別可以新增到允許的清單或封鎖清單，以便完整控制裝置存取。

- [IoT 中樞身分識別登錄](iot-hub-devguide-identity-registry.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型施加限制。 

使用 Azure Resource Graph 則可查詢及探索其訂用帳戶內的資源。  確保已核准環境中的所有 Azure 資源。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

* 不允許的資源類型
* 允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)
- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 Azure AD 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure Resource Manager 互動的能力。
  
- [ 如何設定條件式存取以封鎖存取 Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制使用者在計算資源中執行腳本的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則為您的 Azure Iot 中樞服務定義和實行標準安全性設定。 使用 "Microsoft. Devices" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure IoT 中樞服務的設定進行審核或強制執行。

Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合您組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

- [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。 此外，您可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。  
 
- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)
- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果您 Azure IoT 中樞或相關資源使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 "Microsoft. Devices" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)
- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用 Azure 資訊安全中心針對您的 Azure 資源執行基準掃描。 此外，請使用 Azure 原則來警示和審核 Azure 資源設定。 
 
- [ 如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導** 方針： IoT 中樞會使用安全性權杖和共用存取簽章 (SAS) 權杖來驗證裝置和服務，以避免在網路上傳送金鑰。 

使用受控識別搭配 Azure Key Vault，以簡化雲端應用程式的秘密管理。

- [IoT 中樞安全性權杖](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [如何使用適用于 IoT 中樞的受控識別](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [如何建立金鑰保存庫](../key-vault/general/quick-create-portal.md)
- [如何使用受控識別來提供 Key Vault authentication](../key-vault/general/assign-access-policy-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針： IoT 中樞會使用安全性權杖和共用存取簽章 (SAS) 權杖來驗證裝置和服務，以避免在網路上傳送金鑰。 

使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向支援 Azure AD authentication 的任何服務進行驗證，包括 Key Vault，而不需要在您的程式碼中提供任何認證。

- [IoT 中樞安全性權杖](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [如何設定 IoT 中樞的受控識別](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 
 
- [  如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機 (例如 Azure App Service) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure IoT 中樞) ，但不會對客戶內容執行。

您必須負責預先掃描即將上傳至非計算 Azure 資源的任何內容。 Microsoft 無法存取客戶資料，因此無法代表您執行客戶內容的反惡意程式碼掃描。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3：確定已更新反惡意程式碼軟體和簽章

**指導** 方針：不適用;此基準測試適用于計算資源。 支援 Azure 服務的基礎主機上已啟用 Microsoft Antimalware，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針： Azure IoT 中樞服務提供者法和架構，讓您的 IoT 中樞服務具有高可用性，並可根據特定的業務目標從災難復原。 

- [IoT 中樞高可用性和災害復原](iot-hub-ha-dr.md)

- [如何複製 IoT 中樞](iot-hub-how-to-clone.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針： Azure IoT 中樞建議次要 IoT 中樞必須包含可連接至解決方案的所有裝置身分識別。 解決方案應該保留裝置身分識別的異地複寫備份，並在切換裝置的作用中端點之前將其上傳至次要 IoT 中樞。 IoT 中樞的裝置身分識別匯出功能在此內容中很有用。

- [IoT 中樞高可用性和災害復原](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT 中樞裝置身分識別匯出](iot-hub-bulk-identity-mgmt.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針： Azure IoT 中樞建議次要 IoT 中樞必須包含可連接至解決方案的所有裝置身分識別。 解決方案應該保留裝置身分識別的異地複寫備份，並在切換裝置的作用中端點之前將其上傳至次要 IoT 中樞。 IoT 中樞的裝置身分識別匯出功能在此內容中很有用。

定期執行備份中內容的資料還原。 確定您可以還原已備份的客戶管理金鑰。

- [IoT 中樞高可用性和災害復原](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT 中樞裝置身分識別匯出](iot-hub-bulk-identity-mgmt.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：啟用 Key Vault 中的虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。 如果使用 Azure 儲存體來儲存備份，請在刪除 blob 或 blob 快照集時，啟用虛刪除來儲存及復原您的資料。
 
 
- [了解 Azure RBAC](../role-based-access-control/overview.md)
- [Azure Blob 儲存體的虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導** 方針：為您的組織開發事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以進行事件後的評論。 
  
- [ 建立您自己的安全性事件回應流程的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  使用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導** 方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性是以資訊安全中心在尋找或 analytically 中的信心，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

  
 此外，使用標記標記訂用帳戶，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料。 您必須負責根據發生事件的 Azure 資源和環境的重要性來排定補救警示的優先順序。
  
- [ Azure 資訊安全中心中的安全性警示](../security-center/security-center-alerts-overview.md)
  
- [ 使用標記來組織您的 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 識別弱式點數和間隙，然後視需要修改您的回應計畫。
  
- [ NIST 的發行--測試、訓練和練習適用于 IT 計畫和功能的指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。
  
- [ 如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動方式或持續持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。
  
- [ 如何設定連續匯出](../security-center/continuous-export.md)
 
- [ 如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用工作流程自動化功能 Azure 資訊安全中心自動觸發對安全性警示和建議的回應，以保護您的 Azure 資源。
  
- [ 如何在安全性中心設定工作流程自動化](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)