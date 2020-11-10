---
title: 適用於 Key Vault 的 Azure 安全性基準
description: 適用於 Key Vault 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1abbabd3c231a70ca473c4b64e9a8c75d2119c89
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445518"
---
# <a name="azure-security-baseline-for-key-vault"></a>適用於 Key Vault 的 Azure 安全性基準

適用于 Key Vault 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](../../security/benchmarks/security-control-network-security.md) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導** 方針：整合 Azure Key Vault 與 Azure Private Link。 

Azure Private Link 服務可讓您存取 Azure 服務 (例如，透過虛擬網路中的私人端點，Azure Key Vault) 和 Azure 裝載的客戶/合作夥伴服務。

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如何整合 Key Vault 與 Azure Private Link：

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導** 方針：使用 Azure 資訊安全中心，並遵循網路保護建議來協助保護您在 Azure 中 Key Vault 設定的資源。 

如需 Azure 資訊安全中心所提供網路安全性的詳細資訊： 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引** ：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導** 方針：在與您的 Key Vault 實例相關聯的 Azure 虛擬網路上啟用 Azure DDoS 保護 Standard，以防範分散式阻斷服務攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

 
使用 Azure 入口網站管理 Azure DDoS 保護 Standard： https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 服務層 Azure 資訊安全中心的威脅偵測： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導** 方針： Azure Key Vault 不會使用網路安全性群組 (NSG) 且不會捕捉 Azure Key Vault 的流量記錄。 相反地，請使用 Azure Private Link 保護您的 Azure Key Vault 實例，並啟用診斷設定以記錄計量和審核事件。

整合 Key Vault 與 Azure Private Link：

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault 記錄： https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導** 方針：設定適用于 Azure Key Vault 的 advanced 威脅防護 (ATP) ，可達到此需求。 ATP 提供額外一層的安全情報。 此工具會偵測可能有害的嘗試存取或惡意探索 Azure Key Vault 帳戶。

當 Azure 資訊安全中心偵測到異常活動時，就會顯示警示。 此外，它也會以可疑活動的詳細資料來傳送電子郵件給訂用帳戶管理員，以及如何調查和補救已識別威脅的建議。

設定 Azure Key Vault 的 advanced 威脅防護：

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引** ：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導** 方針：對於需要存取 Azure Key Vault 實例的資源，請使用 Azure 服務標籤進行 Azure Key Vault，以定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

Azure 服務標記總覽： https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：使用 Azure 原則為與您的 Azure Key Vault 實例相關聯的網路資源定義和執行標準安全性設定。 使用 "KeyVault" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Key Vault 實例的網路設定進行審核或強制執行。 您也可以使用與 Azure Key Vault 相關的內建原則定義，例如：

Key Vault 應該使用虛擬網路服務端點

教學課程：建立和管理原則以強制執行合規性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 原則範例：

https://docs.microsoft.com/azure/governance/policy/samples/#networ

快速入門：在入口網站中定義和指派藍圖：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對您的 Azure Key Vault 實例的網路安全性和流量流程，使用與網路安全性和流量相關的資源標記，以提供中繼資料和邏輯組織。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

使用標記來組織您的 Azure 資源：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視網路資源設定，並偵測與您的 Azure Key Vault 實例相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

查看並取出 Azure 活動記錄事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

使用 Azure 監視器來建立、查看和管理活動記錄警示：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](../../security/benchmarks/security-control-logging-monitoring.md) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針：不適用;Microsoft 會針對記錄檔中的時間戳記，維護 Azure 資源（例如 Azure Key Vault）所使用的時間來源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌記錄，以匯總 Azure Key Vault 所產生的安全性資料。 在 Azure 監視器中，您可以使用 Azure Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來取得長期/封存儲存體。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

Azure Key Vault 記錄：

https://docs.microsoft.com/azure/key-vault/key-vault-logging

快速入門：如何在面板上 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用 Azure Key Vault 實例上的診斷設定，以存取 audit、security 和診斷記錄。 活動記錄 (自動提供) 包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他有用的項目。

Azure Key Vault 記錄：

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，針對用來保存 Azure Key Vault 記錄的 Log Analytics 工作區，根據您組織的合規性法規設定保留期限。 使用 Azure 儲存體帳戶進行長期/封存儲存。

變更資料保留期限： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：分析和監視記錄中的異常行為，並定期審核您 Azure Key Vault 保護之資源的結果。 使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

快速入門：板載 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 監視器中的 Log Analytics 入門：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure 監視器中的記錄查詢入門：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導** 方針：在 Azure 資訊安全中心中，為 Key Vault 啟用 (ATP) 的 advanced 威脅防護。 啟用 Azure Key Vault 中的診斷設定，並將記錄傳送至 Log Analytics 工作區。 請將您的 Log Analytics 工作區在 Azure Sentinel 上線，因為其可提供安全性協調流程自動化回應 (SOAR) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。

快速入門：板載 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

在 Azure 資訊安全中心中管理及回應安全性警示：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

使用 Azure 監視器警示來回應事件：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用;Azure Key Vault 不會處理或產生反惡意程式碼的相關記錄。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用;Azure Key Vault 不會處理或產生 DNS 相關記錄。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../../security/benchmarks/security-control-identity-access-control.md) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針：維護您 Azure Active Directory 註冊的應用程式，以及可存取 Azure Key Vault 金鑰、秘密和憑證的任何使用者帳戶的清查。 您可以使用 Azure 入口網站或 PowerShell 來查詢和協調 Key Vault 的存取。 若要在 PowerShell 中查看存取權，請使用下列命令：

 (Get-azresource-ResourceId [KeyVaultResourceID] ) 。AccessPolicies

使用 Azure Active Directory 註冊應用程式：

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

安全存取金鑰保存庫：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針：不適用;Azure Key Vault 沒有預設密碼的概念，因為 Active Directory 提供驗證，並使用 Azure 角色型存取控制 (Azure RBAC) 進行保護。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：使用可存取 Azure Key Vault 實例的專用系統管理帳戶來建立標準作業程式。 使用 Azure 資訊安全中心的身分識別與存取管理 (目前為預覽狀態) 來監視使用中的系統管理帳戶數目。

監視身分識別和存取 (預覽) ：

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：搭配 AppId、TenantID 和 ClientSecret 使用 Azure 服務主體，以順暢地驗證您的應用程式，並取得將用來存取 Azure Key Vault 秘密的權杖。

使用 .NET 進行 Azure Key Vault 的服務對服務驗證：

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory Multi-Factor Authentication，並遵循目前為預覽狀態的 Azure 資訊安全中心身分識別和存取管理 () 建議，以協助保護您的已啟用事件中樞資源。

規劃以雲端為基礎的 Azure Multi-Factor Authentication 部署：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

監視身分識別和存取 (預覽) ：

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用具特殊許可權的存取工作站 (PAW) 搭配 Azure MULTI-FACTOR AUTHENTICATION (MFA) 設定為登入並設定 Key Vault 啟用的資源。 

特殊許可權存取工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

規劃以雲端為基礎的 Azure Multi-Factor Authentication 部署： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指導** 方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (Azure AD) PRIVILEGED IDENTITY MANAGEMENT (PIM) 來產生記錄和警示。 使用 Azure AD 風險偵測來查看警示和報告有風險的使用者行為。 如需其他記錄，請將 Azure 資訊安全中心風險偵測警示傳送至 Azure 監視器，並使用動作群組設定自訂警示/通知。

啟用「advanced 威脅防護」 (ATP) ，讓 Azure Key Vault 產生可疑活動的警示。

部署 Azure AD Privileged Identity Management (PIM) ： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

針對 Azure Key Vault (preview) 設定 advanced 威脅防護： https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure Key Vault (預覽) 的警示： https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory 風險偵測： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

在 Azure 入口網站中建立和管理動作群組： https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：設定條件式存取原則的位置條件，以及管理您的命名位置。 使用命名位置時，您可以建立 IP 位址範圍或國家/地區和區域的邏輯分組。 您可以將敏感資源的存取權（例如 Key Vault 秘密）限制在已設定的命名位置。

Azure Active Directory 條件式存取中的位置條件為何？： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure Active Directory (Azure AD) 作為 Azure 資源（例如 Key Vault）的中央驗證和授權系統。 這可讓 Azure 角色型存取控制 (Azure RBAC) 管理敏感性資源。

 

快速入門：在 Azure Active Directory 中建立新的租使用者：

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針：請參閱 Azure Active Directory (Azure AD) 記錄檔，以協助探索具有 Azure Key Vault 系統管理角色的過時帳戶。 此外，使用 Azure AD 存取評論，有效率地管理群組成員資格、存取可用來存取 Azure Key Vault 的企業應用程式，以及角色指派。 應定期檢查使用者存取，例如每隔90天，以確定只有適當的使用者可以繼續存取。

Azure Active Directory 報表和監視檔：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

什麼是 Azure AD 存取權檢查？：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指導** 方針：啟用 Azure Key Vault 和 Azure Active Directory 的診斷設定，將所有記錄傳送到 Log Analytics 工作區。 設定所需的警示 (例如，嘗試存取 Log Analytics 中) 的已停用秘密。

整合 Azure AD 記錄與 Azure 監視器記錄： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

從舊的 Key Vault 解決方案遷移： https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 Azure Active Directory 的身分識別保護和風險偵測功能，來設定偵測到與您 Azure Key Vault 受保護資源相關的可疑動作的自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行您組織的安全性回應。 

Azure Active Directory 入口網站中有風險的登入報告： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

如何：設定及啟用風險原則： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何上架 Azure Sentinel：  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：不適用;Azure Key Vault 不支援客戶加密箱。

正式推出的支援服務和案例： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](../../security/benchmarks/security-control-data-protection.md) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導** 方針：使用標籤來協助追蹤儲存或處理 Azure Key Vault 啟用資源之機密資訊的 Azure 資源。 

使用標記來組織您的 Azure 資源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：您可以使用設定為限制存取特定子網的虛擬網路服務端點，以保護對 Azure Key Vault 的存取。

在防火牆規則生效後，您只能在要求源自允許的子網或 IP 位址範圍時，執行 Azure Key Vault 資料平面作業。 這也適用于 Azure 入口網站中的 Azure Key Vault 存取。 雖然您可以從 Azure 入口網站流覽至金鑰保存庫，但如果您的用戶端電腦不在允許的清單中，您可能無法列出金鑰、秘密或憑證。 這也會影響 Azure Key Vault 選擇器和其他 Azure 服務。 如果防火牆規則讓您的用戶端電腦無法這麼做，您可以看到金鑰保存庫清單，但不能列出金鑰。

設定 Azure Key Vault 防火牆和虛擬網路： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 的虛擬網路服務端點： https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：儲存在 Azure Key Vault 中的所有資料都會被視為機密。 使用 Azure Key Vault 資料平面存取控制來控制 Azure Key Vault 秘密的存取權。 您也可以使用 Key Vault 的內建防火牆來控制網路層級的存取。 若要監視 Azure Key Vault 的存取權，請啟用 Key Vault 診斷設定，並將記錄傳送至 Azure 儲存體帳戶或 Log Analytics 工作區。

安全存取金鑰保存庫： https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

設定 Azure Key Vault 防火牆和虛擬網路： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 記錄： https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：驗證、管理和資料平面存取 Azure Key Vault 的所有流量都會經過加密，並透過 HTTPS：埠443進行加密。  (不過，CRL 偶爾會有 HTTP [埠 80] 流量。 ) Azure Key Vault 繼續允許內嵌 TLS 1.1 和 TLS 1.0 資料。 資料可能會透過用戶端上的設定限制為 TLS 1.2。



存取防火牆後方的 Azure Key Vault： https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：不適用;Azure Key Vault 中 (秘密、金鑰和憑證) 的所有資料都會被視為機密。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：安全地存取 Azure Key Vault 實例的管理和資料平面。

安全存取金鑰保存庫：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針： Microsoft 管理 Azure Key Vault 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

何謂 Azure Key Vault？

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：所有受管理物件 (金鑰、憑證和秘密) 都會在 Azure Key Vault 中進行待用加密。

支援檔：

- [加密模型和金鑰管理表](../../security/fundamentals/encryption-atrest.md)


**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器中的 Azure Key Vault 分析解決方案來檢查 Azure Key Vault audit 事件記錄檔。

Azure 監視器中的 Azure Key Vault 分析解決方案：

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](../../security/benchmarks/security-control-vulnerability-management.md) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針： Microsoft 會在支援 Azure Key Vault 的基礎系統上執行弱點管理。


**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針： N/A;Microsoft 會在支援 Key Vault 的基礎系統上執行修補程式管理。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針： Microsoft 會在支援 Key Vault 的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：使用 (Azure 資訊安全中心提供的安全分數) 的預設風險評等。

在 Azure 資訊安全中心中提升您的安全分數：

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](../../security/benchmarks/security-control-inventory-asset-management.md) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指導** 方針：使用 Azure Resource Graph 來查詢及探索所有資源 (包括您的訂用帳戶中) 的 Azure Key Vault 實例。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

取得目前帳戶可以存取的訂閱：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

什麼是 Azure 角色型存取控制 (Azure RBAC)？

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：將標記套用至 Azure Key Vault 資源，以邏輯方式將這些資源組織成分類法。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別的訂用帳戶（如果適當）來組織和追蹤 Azure Key Vault 的實例和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

建立額外的 Azure 訂用帳戶：

https://docs.microsoft.com/azure/billing/billing-create-subscription

建立資源組織和管理的管理群組：

https://docs.microsoft.com/azure/governance/management-groups/create

使用標記來組織您的 Azure 資源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指導** 方針：定義您的計算資源的已核准 Azure 資源清單和已核准的軟體

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則來限制可在客戶訂用帳戶 () 中建立的資源類型，並使用下列內建原則定義：

- 不允許的資源類型

- 允許的資源類型

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。

教學課程：建立和管理原則以強制執行合規性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：不適用;這項建議適用于整個 Azure 和計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來限制可在客戶訂用帳戶 () 中建立的資源類型，並使用下列內建原則定義：

- 不允許的資源類型

- 允許的資源類型

教學課程：建立和管理原則以強制執行合規性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 原則範例： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制使用者透過腳本與 AzureResources Manager 互動的能力

**指導** 方針：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 AZURE RESOURCE MANAGER (ARM) 互動的能力。 這可防止在高安全性環境中建立和變更資源，例如具有 Key Vault 設定的資源。

使用條件式存取來管理 Azure 管理的存取權：

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引** ：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](../../security/benchmarks/security-control-secure-configuration.md) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：在 "KeyVault" 命名空間中使用 Azure 原則別名來建立自訂原則，以對 Azure Key Vault 實例的設定進行審核或強制執行。 您也可以使用內建的 Azure 原則定義來進行 Azure Key Vault，例如：

Key Vault 物件應該要可復原

將 Key Vault 的診斷設定部署至 Log Analytics 工作區

應啟用 Key Vault 中的診斷記錄

Key Vault 應該使用虛擬網路服務端點

將 Key Vault 的診斷設定部署至事件中樞

使用 Azure 資訊安全中心中的建議做為您 Azure Key Vault 實例的安全設定基準。

如何檢視可用的 Azure 原則別名： 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教學課程：建立和管理原則以強制執行合規性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在]，在啟用 Azure Key Vault 的資源之間強制執行安全設定。 

教學課程：建立和管理原則以強制執行合規性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
瞭解 Azure 原則效果： 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果針對已啟用 Azure Key Vault 的資源使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

如何在 Azure DevOps 中儲存程式碼：  

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos 文件：  

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導** 方針：在 "KeyVault" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引** ：不適用，這項建議主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指導** 方針：使用 Azure 資訊安全中心針對您 Azure Key Vault 保護的資源執行基準掃描 

  

如何修復 Azure 資訊安全中心中的建議： 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導** 方針：不適用;此基準測試適用于計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導** 方針：使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。 確定已啟用 Azure Key Vault 虛刪除。

如何與 Azure 受控識別整合： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Key Vault： 

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何驗證 Key Vault：

https://docs.microsoft.com/azure/key-vault/general/authentication

如何指派 Key Vault 存取原則：

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。 

  

* [如何與 Azure 受控識別整合](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [如何建立 Key Vault](quick-create-portal.md)

* [如何驗證 Key Vault](authentication.md)

* [如何指派 Key Vault 存取原則](assign-access-policy-portal.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引** ：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。  
  
 如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../../security/benchmarks/security-control-malware-defense.md) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引** ：不適用，這項建議主要用於計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Key Vault) ，但不會對客戶內容執行。

預先掃描即將上傳或傳送至非計算 Azure 資源（例如 Azure Key Vault）的任何內容。 Microsoft 無法在這些實例中存取您的資料。

瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware： https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引** ：不適用，這項建議主要用於計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](../../security/benchmarks/security-control-data-recovery.md) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：使用下列 PowerShell 命令，確保定期自動備份您的 Key Vault 憑證、金鑰、受控儲存體帳戶和密碼：

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

（選擇性）您可以將 Key Vault 備份儲存在 Azure 備份內。

如何備份 Key Vault 憑證： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何備份 Key Vault 金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何備份受管理的儲存體帳戶 Key Vault： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何備份 Key Vault 秘密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何啟用 Azure 備份： https://docs.microsoft.com/azure/backup



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指導** 方針：使用下列 PowerShell 命令來執行 Key Vault 憑證、金鑰、受控儲存體帳戶和密碼的備份：

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

（選擇性）您可以將 Key Vault 備份儲存在 Azure 備份內。

如何備份 Key Vault 憑證： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何備份 Key Vault 金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何備份受管理的儲存體帳戶 Key Vault： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何備份 Key Vault 秘密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何啟用 Azure 備份： https://docs.microsoft.com/azure/backup



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：使用下列 PowerShell 命令，定期執行 Key Vault 憑證、金鑰、受控儲存體帳戶和密碼的資料還原：

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

如何還原 Key Vault 憑證：  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

如何還原 Key Vault 金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

如何還原 Key Vault 受控儲存體帳戶： https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

如何還原 Key Vault 秘密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導** 方針：確定已啟用 Azure Key Vault 的虛刪除功能。 虛刪除可讓您復原已刪除的金鑰保存庫和保存庫物件，例如金鑰、秘密和憑證。 

如何使用 Azure Key Vault 的虛刪除： 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](../../security/benchmarks/security-control-incident-response.md) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引** ：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。 這些程式應著重于保護機密系統，例如使用 Key Vault 秘密的系統。

如何設定 Azure 資訊安全中心內的工作流程自動化：  

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

建立自有安全性事件回應程序的指引：   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全性回應中心的事件剖析：    

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

客戶也可利用 NIST 的電腦安全性事件處理指南，以協助建立自己的事件回應計畫：  

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引** ：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。 此外，清楚地標示訂用帳戶 (例如 生產、非生產) 以及建立命名系統來清楚識別和分類 Azure 資源，特別是處理敏感性資料，例如 Azure Key Vault 秘密。


**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure Key Vault 實例和相關資源。 找出弱點和落差，並視需要修訂計畫。

請參閱 NIST 的出版物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (IT 計畫和能力的測試、訓練和練習方案指南)：  

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引** ：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。  事後檢討事件，確保問題已解決。

如何設定 Azure 資訊安全中心的安全性連絡人： 

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure Key Vault 啟用之資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。  您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。 

 

如何設定連續匯出： 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

如何將警示串流至 Azure Sentinel：  

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」的安全性警示和建議來自動觸發回應，以保護受 Azure Key Vault 保護的資源。 

 

如何設定工作流程自動化和 Logic Apps：  

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指導** 方針：您不會直接對 Azure Key Vault 服務執行畫筆測試，但建議您測試使用 Key Vault 的 Azure 資源，以確保秘密的安全性。

您將需要遵循 Microsoft 的參與規則，以確保您的滲透測試不會違反 Microsoft 原則：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

針對 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：  

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../../security/benchmarks/overview.md) (機器翻譯)
- 深入了解 [Azure 安全性基準](../../security/benchmarks/security-baselines-overview.md) (機器翻譯)