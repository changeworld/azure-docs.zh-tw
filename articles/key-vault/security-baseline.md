---
title: 適用于 Key Vault 的 Azure 安全性基準
description: 適用于 Key Vault 的 Azure 安全性基準
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9b767693691557f684bee59aa1764395dc42bffe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590035"
---
# <a name="azure-security-baseline-for-key-vault"></a>適用于 Key Vault 的 Azure 安全性基準

適用于 Key Vault 的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自[Azure 安全性基準測試版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供有關如何在 Azure 上使用最佳作法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱[Azure 安全性基準總覽](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在您的虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指引**：整合 Azure Key Vault 與 Azure 私人連結。 Azure 私用連結服務可讓您透過虛擬網路中的私人端點，存取 Azure 服務（例如 Azure Key Vault）和 Azure 託管的客戶/合作夥伴服務。
Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如何整合 Key Vault 與 Azure 私人連結： https://docs.microsoft.com/azure/key-vault/private-link-service

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視並記錄 Vnet、子網和 Nic 的設定和流量

**指導**方針：使用 Azure 資訊安全中心並遵循網路保護建議，協助保護您在 Azure 中 Key Vault 設定的資源。 


如需有關 Azure 資訊安全中心所提供之網路安全性的詳細資訊： 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 web 應用程式

**指導**方針：不適用;這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：在與您的 Key Vault 實例相關聯的 Azure 虛擬網路上啟用 Azure DDoS 保護標準，以防範分散式阻絕服務攻擊。 使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意或未使用的網際網路 IP 位址的通訊。

 
使用 Azure 入口網站管理 Azure DDoS 保護標準： Azure 資訊安全中心中適用于 Azure 服務層的 https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection 威脅偵測： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針： Azure Key Vault 不會使用網路安全性群組（NSG），也不會攔截 Azure Key Vault 的流量記錄。 相反地，請使用 Azure 私用連結來保護您的 Azure Key Vault 實例，並啟用診斷設定來記錄計量和 audit 事件。
整合 Key Vault 與 Azure 私人連結： https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault 記錄： https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵預防系統（IDS/IPS）

**指導**方針：藉由設定 Azure Key Vault 的「先進威脅防護」（ATP），即可滿足此需求。 ATP 提供一層額外的安全性情報。 此工具會偵測可能有害的嘗試存取或惡意探索 Azure Key Vault 帳戶。


當 Azure 資訊安全中心偵測到異常活動時，它會顯示警示。 它也會以可疑活動的詳細資料，以及如何調查並補救已識別威脅的建議，以電子郵件寄給訂用帳戶管理員。


設定 Azure Key Vault 的先進威脅防護： https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 web 應用程式的流量

**指導**方針：不適用;這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指引**：對於需要存取 Azure Key Vault 實例的資源，請使用 Azure Key Vault 的 Azure 服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標籤名稱（例如，ApiManagement），您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。


Azure 服務標記總覽：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則，針對與您的 Azure Key Vault 實例相關聯的網路資源定義及執行標準安全性設定。 使用 "KeyVault" 和 "Microsoft" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Key Vault 實例的網路設定。 您也可以使用與 Azure Key Vault 相關的內建原則定義，例如：


Key Vault 應該使用虛擬網路服務端點


教學課程：建立和管理原則以強制執行合規性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Azure 原則範例：

https://docs.microsoft.com/azure/governance/policy/samples/#networ

快速入門：在入口網站中定義和指派藍圖：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

**指導**方針：使用標記來取得與 Azure Key Vault 實例的網路安全性和流量流程相關的資源，以提供中繼資料和邏輯組織。


使用與標記相關的任何內建 Azure 原則定義，例如「需要標記和其值」，以確保所有資源都是以標籤建立，並通知您現有的未標記資源。


您可以使用 Azure PowerShell 或 Azure CLI，根據其標記來查閱或執行資源的動作。


使用標記來組織您的 Azure 資源：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視網路資源設定，並偵測與 Azure Key Vault 實例相關的網路資源變更。 在 Azure 監視器中建立警示，以在重大網路資源的變更發生時觸發。

查看和取出 Azure 活動記錄事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


使用 Azure 監視器來建立、查看和管理活動記錄警示：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針：不適用;Microsoft 會針對記錄檔中的時間戳記，維護用於 Azure 資源的時間來源，例如 Azure Key Vault。

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，以匯總 Azure Key Vault 所產生的安全性資料。 在 Azure 監視器中，使用 Azure Log Analytics 工作區來查詢和執行分析，並針對長期/封存儲存體使用 Azure 儲存體帳戶。 或者，您可以啟用和內部資料，以 Azure Sentinel 或協力廠商 SIEM。 

Azure Key Vault 記錄：

https://docs.microsoft.com/azure/key-vault/key-vault-logging

快速入門：如何在面板上 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的審核記錄

**指引**：在您的 Azure Key Vault 實例上啟用診斷設定，以存取 audit、security 和診斷記錄。 活動記錄（可自動取得）包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他實用的元素。

Azure Key Vault 記錄：

https://docs.microsoft.com/azure/key-vault/key-vault-logging

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指引**：在 Azure 監視器中，針對用來保存 Azure Key Vault 記錄的 Log Analytics 工作區，請根據貴組織的合規性法規來設定保留週期。 針對長期/封存儲存體使用 Azure 儲存體帳戶。

變更資料保留週期： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**：分析和監視記錄中的異常行為，並定期審查受 Azure Key Vault 保護資源的結果。 使用 Azure 監視器的 Log Analytics 工作區來審查記錄，並對記錄資料執行查詢。 或者，您可以啟用和內部資料，以 Azure Sentinel 或協力廠商 SIEM。 


快速入門：內部 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard


Azure 監視器中的 Log Analytics 入門： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

開始使用 Azure 監視器中的記錄查詢： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指引**：在 Azure 資訊安全中心中，為 Key Vault 啟用先進的威脅防護（ATP）。 啟用 Azure Key Vault 中的診斷設定，並將記錄傳送到 Log Analytics 工作區。 將您的 Log Analytics 工作區上架到 Azure Sentinel，因為它提供安全性協調流程自動化回應（攀升情況）解決方案。 這可讓您建立和使用腳本來修復安全性問題。

快速入門： Azure Sentinel 的面板：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

管理和回應 Azure 資訊安全中心中的安全性警示：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


回應具有 Azure 監視器警示的事件：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反惡意程式碼記錄

**指導**方針：不適用;Azure Key Vault 不會處理或產生與反惡意程式碼相關的記錄檔。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;Azure Key Vault 不會處理或產生 DNS 相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列審核記錄

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指引**：維護 Azure Active Directory 註冊的應用程式，以及可存取您 Azure Key Vault 金鑰、秘密和憑證的任何使用者帳戶的清查。 您可以使用 Azure 入口網站或 PowerShell 來查詢和協調 Key Vault 存取。 若要在 PowerShell 中查看存取權，請使用下列命令：


（Get-azresource-ResourceId [Keyvaultresourceid 值]）。屬性。 AccessPolicies

向 Azure Active Directory 註冊應用程式：

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

保護對金鑰保存庫的存取：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：變更預設密碼（若適用）

**指導**方針：不適用;Azure Key Vault 沒有預設密碼的概念，因為驗證是由 Active Directory 提供，並受到角色型存取控制保護。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：使用可存取您 Azure Key Vault 實例的專用系統管理帳戶，建立相關的標準操作程式。 使用 Azure 資訊安全中心身分識別和存取管理（目前處於預覽狀態）來監視作用中系統管理帳戶的數目。


監視身分識別和存取（預覽）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：搭配 Azure Active Directory 使用單一登入（SSO）

**指引**：搭配 AppId、TenantID 和 ClientSecret 使用 Azure 服務主體，以順暢地驗證您的應用程式，並取出將用來存取 Azure Key Vault 秘密的權杖。


使用 .NET Azure Key Vault 的服務對服務驗證：

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory 多重要素驗證，並遵循 Azure 資訊安全中心身分識別和存取管理（目前處於預覽狀態）建議，以協助保護已啟用事件中樞的資源。


規劃以雲端為基礎的 Azure 多因素驗證部署：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


監視身分識別和存取（預覽）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦（特殊許可權存取工作站）進行所有系統管理工作

**指引**：搭配使用特殊許可權存取工作站（PAW）與 Azure 多重要素驗證（MFA），並將其設定為登入和設定 Key Vault 啟用的資源。


特殊許可權存取工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

規劃以雲端為基礎的 Azure 多因素驗證部署： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄和警示可疑活動

**指引**：使用 AZURE ACTIVE DIRECTORY （AAD） PRIVILEGED IDENTITY MANAGEMENT （PIM）來產生記錄，並在環境中發生可疑或不安全的活動時發出警示。 使用 AAD 風險偵測來根據有風險的使用者行為來查看警示和報告。 如需其他記錄，請將 Azure 資訊安全中心風險偵測警示傳送至 Azure 監視器，並使用動作群組來設定自訂警示/通知。


啟用 [advanced 威脅防護（ATP）] 以進行 Azure Key Vault，以產生可疑活動的警示。


部署 Azure AD Privileged Identity Management （PIM）：

https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

設定 Azure Key Vault 的先進威脅防護（預覽）： https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault


Azure Key Vault 的警示（預覽）：

https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv


Azure Active Directory 風險偵測：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events


在 Azure 入口網站中建立和管理動作群組：

https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：設定條件式存取原則的位置條件，並管理您的命名位置。 使用已命名的位置，您可以建立 IP 位址範圍或國家和地區的邏輯群組。 您可以限制對已設定之命名位置的敏感資源（例如 Key Vault 秘密）的存取權。

Azure Active Directory 條件式存取中的位置條件為何？： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AAD）做為 Azure 資源（例如 Key Vault）的中央驗證和授權系統。 這可讓角色型存取控制（RBAC）管理敏感性資源。

快速入門：在 Azure Active Directory 中建立新的租使用者： https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期審查並協調使用者存取

**指導**方針：請參閱 AZURE ACTIVE DIRECTORY （AAD）記錄，以協助探索具有 Azure Key Vault 系統管理角色的過時帳戶。 此外，使用 AAD 存取審查來有效率地管理群組成員資格、存取可用來存取 Azure Key Vault 的企業應用程式，以及角色指派。 應定期檢查使用者存取，例如每隔90天，以確保只有適當的使用者可以繼續存取。


Azure Active Directory 報表和監視檔： https://docs.microsoft.com/azure/active-directory/reports-monitoring/


什麼是 Azure AD 的存取權評論？： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視嘗試存取停用的帳戶

**指導**方針：啟用 Azure Key Vault 和 Azure Active Directory 的診斷設定，並將所有記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中設定所需的警示（例如嘗試存取已停用的密碼）。

整合 Azure AD 記錄與 Azure 監視器記錄檔： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

從舊的 Key Vault 解決方案進行遷移： https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution       

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory 的身分識別保護和風險偵測功能，針對偵測到 Azure Key Vault 受保護資源相關的可疑動作設定自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行貴組織的安全性回應。


Azure Active Directory 入口網站中有風險的登入報告： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

如何：設定和啟用風險原則： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


如何上架 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：提供 Microsoft 在支援案例期間存取相關的客戶資料

**指導**方針：不適用;Azure Key Vault 不支援客戶加密箱。

正式運作的支援服務和案例： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護機密資訊的清查

**指引**：使用標籤協助追蹤 Azure 資源，以儲存或處理已啟用 Azure Key Vault 資源的機密資訊。


使用標記來組織您的 Azure 資源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感資訊的系統

**指引**. 您可以使用設定為限制存取特定子網的虛擬網路服務端點，以保護 Azure Key Vault 的存取。


防火牆規則生效之後，您只能在您的要求源自允許的子網或 IP 位址範圍時，執行 Azure Key Vault 資料平面作業。 這也適用于 Azure 入口網站中的 Azure Key Vault 存取。 雖然您可以從 Azure 入口網站流覽至金鑰保存庫，但如果您的用戶端電腦不在允許清單中，您可能無法列出金鑰、秘密或憑證。 這也會影響 Azure Key Vault 選擇器和其他 Azure 服務。 如果防火牆規則阻止您的用戶端電腦執行此動作，您可以看到金鑰保存庫清單，但不能列出金鑰。


設定 Azure Key Vault 防火牆和虛擬網路： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 的虛擬網路服務端點： https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的機密資訊傳輸

**指導**方針： Azure Key Vault 中儲存的所有資料都會被視為機密。 使用 Azure Key Vault 資料平面存取控制來控制 Azure Key Vault 秘密的存取權。 您也可以使用 Key Vault 的內建防火牆來控制網路層的存取權。 若要監視 Azure Key Vault 的存取，請啟用 Key Vault 診斷設定，並將記錄檔傳送至 Azure 儲存體帳戶或 Log Analytics 工作區。

保護對金鑰保存庫的存取： https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

設定 Azure Key Vault 防火牆和虛擬網路： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 記錄： https://docs.microsoft.com/azure/key-vault/key-vault-logging

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有機密資訊

**指導**方針：針對驗證、管理和資料平面存取 Azure Key Vault 的所有流量都會經過加密，並透過 HTTPS：埠443進行。 （不過，CRL 偶爾會有 HTTP [埠 80] 流量）。


存取防火牆後方的 Azure Key Vault： https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用 active discovery 工具來識別敏感性資料

**指導**方針：不適用;Azure Key Vault （秘密、金鑰和憑證）內的所有資料都會被視為機密。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="46-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.6：使用 active discovery 工具來識別敏感性資料

**指導**方針：安全存取 Azure Key Vault 實例的管理和資料平面。


保護對金鑰保存庫的存取：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用以主機為基礎的資料遺失防護來強制存取控制

**指導**方針： Microsoft 會管理 Azure Key Vault 的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。


什麼是 Azure Key Vault？ https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密機密資訊待用

**指導**方針： Azure Key Vault 中的所有受管理物件（金鑰、憑證和密碼）都會加密。


Azure Key Vault 的安全性控制項： https://docs.microsoft.com/azure/key-vault/key-vault-security-controls


**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：對重要 Azure 資源的變更進行記錄和警示

**指導**方針：使用 Azure 監視器中的 Azure Key Vault 分析解決方案來審查 Azure Key Vault 的 audit 事件記錄檔。

Azure 監視器中的 Azure Key Vault 分析解決方案： https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針： Microsoft 會在支援 Azure Key Vault 的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針： N/A;Microsoft 會在支援 Key Vault 的基礎系統上執行修補程式管理。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較回溯弱點掃描

**指導**方針： Microsoft 會在支援 Key Vault 的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程式來排定所發現弱點的補救優先順序

**指導**方針：使用 Azure 資訊安全中心所提供的預設風險評等（安全分數）。

在 Azure 資訊安全中心中改善您的安全分數：

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢和探索訂用帳戶內的所有資源（包括 Azure Key Vault 實例）。 請確定您的租使用者中有適當的（讀取）許可權，而且能夠列舉所有的 Azure 訂用帳戶以及您訂用帳戶內的資源。

快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

取得目前帳戶可以存取的訂閱。： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

什麼是適用于 Azure 資源的角色型存取控制（RBAC）？ https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至 Azure Key Vault 資源，以邏輯方式將其組織成分類法。


如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：適當地使用標記、管理群組和個別訂用帳戶來組織和追蹤 Azure Key Vault 實例和相關資源。 定期協調清查，並確保未經授權的資源會及時從訂用帳戶中刪除。


建立額外的 Azure 訂用帳戶：

https://docs.microsoft.com/azure/billing/billing-create-subscription


建立用於資源組織及管理的管理群組：

https://docs.microsoft.com/azure/governance/management-groups/create


使用標記來組織您的 Azure 資源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查

**指導**方針：定義已核准的 Azure 資源清單，以及適用于您計算資源的已核准軟體

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：未核准的 Azure 資源的監視

**指引**：使用下列內建原則定義，透過 Azure 原則來對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

此外，請使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。

教學課程：建立和管理原則以強制執行合規性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

**指導**方針：不適用;這項建議適用于 Azure 整體和計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，透過 Azure 原則來對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

教學課程：建立和管理原則以強制執行合規性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 原則範例： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：執行核准的應用程式清單

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制使用者透過腳本與 AzureResources Manager 互動的能力

**指引**：使用 Azure 條件式存取來限制使用者與 Azure Resource Manager 的互動能力，方法是設定「Microsoft Azure 管理」應用程式的「封鎖存取」。 這可能會防止在高安全性環境（例如具有 Key Vault 設定的資源）中建立和變更資源。

使用條件式存取來管理 Azure 管理的存取： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行腳本的能力

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實際或邏輯上隔離高風險應用程式

**指導**方針：不適用;這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：在 "KeyVault" 命名空間中使用 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Key Vault 實例的設定。 您也可以使用內建的 Azure 原則定義來進行 Azure Key Vault，例如：

- Key Vault 物件應該要可復原
- 將 Key Vault 的診斷設定部署到 Log Analytics 工作區
- 應啟用 Key Vault 中的診斷記錄
- Key Vault 應該使用虛擬網路服務端點
- 將 Key Vault 的診斷設定部署至事件中樞

使用來自 Azure 資訊安全中心的建議，做為您 Azure Key Vault 實例的安全設定基準。

如何查看可用的 Azure 原則別名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教學課程：建立和管理原則以強制執行合規性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [部署如果不存在]，在已啟用 Azure Key Vault 的資源上強制執行安全設定。 


教學課程：建立和管理原則以強制執行合規性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 


  
瞭解 Azure 原則效果： 

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果為已啟用 Azure Key Vault 的資源使用自訂 Azure 原則定義，請使用 Azure DevOps/存放庫安全地儲存和管理您的程式碼。


如何將程式碼儲存在 Azure DevOps： 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 


Azure Repos 檔： 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導**方針：使用 "KeyVault" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、審查和強制執行系統組態。 此外，開發處理常式和管線來管理原則例外狀況。



如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

**指導**方針：使用 Azure 資訊安全中心來執行 Azure Key Vault 保護資源的基準掃描 

  

如何修復 Azure 資訊安全中心中的建議： 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

**指導**方針：不適用;此基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：搭配 Azure Key Vault 使用受控服務識別，以簡化及保護雲端應用程式的秘密管理。 請確定已啟用 Azure Key Vault 虛刪除。


如何與 Azure 受控識別整合：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


如何建立 Key Vault：

https://docs.microsoft.com/azure/key-vault/quick-create-portal


如何使用受控識別提供 Key Vault 驗證： 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：搭配 Azure Key Vault 使用受控服務識別，以簡化及保護雲端應用程式的秘密管理。 

  

如何與 Azure 受控識別整合： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  



如何建立 Key Vault： 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

如何使用受控識別提供 Key Vault 驗證：  
https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證暴露

**指導**方針：執行認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。  
  
 如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防護](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：不適用;這項建議適用于計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：在支援 Azure 服務的基礎主機（例如 Azure Key Vault）上已啟用 Microsoft 反惡意程式碼，但它不會在客戶內容上執行。


預先掃描上傳或傳送至非計算 Azure 資源的任何內容，例如 Azure Key Vault。 Microsoft 無法存取您在這些實例中的資料。


瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware： https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確認反惡意程式碼軟體和簽章已更新

**指導**方針：不適用;這項建議適用于計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保週期性自動備份

**指導**方針：使用下列 PowerShell 命令，確保定期自動備份您的 Key Vault 憑證、金鑰、受管理的儲存體帳戶和秘密：

- 備份-AzKeyVaultCertificate
- 備份-AzKeyVaultKey
- 備份-AzKeyVaultManagedStorageAccount
- 備份-AzKeyVaultSecret

（選擇性）您可以在 Azure 備份中儲存 Key Vault 備份。

如何備份 Key Vault 憑證： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何備份 Key Vault 金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何備份受管理的儲存體帳戶 Key Vault： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何備份 Key Vault 秘密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何啟用 Azure 備份： https://docs.microsoft.com/azure/backup

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針：使用下列 PowerShell 命令來執行 Key Vault 憑證、金鑰、受管理的儲存體帳戶和秘密的備份：

- 備份-AzKeyVaultCertificate
- 備份-AzKeyVaultKey
- 備份-AzKeyVaultManagedStorageAccount
- 備份-AzKeyVaultSecret

（選擇性）您可以在 Azure 備份中儲存 Key Vault 備份。

如何備份 Key Vault 憑證： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何備份 Key Vault 金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何備份受管理的儲存體帳戶 Key Vault： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何備份 Key Vault 秘密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何啟用 Azure 備份： https://docs.microsoft.com/azure/backup

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：使用下列 PowerShell 命令，定期執行 Key Vault 憑證、金鑰、受管理的儲存體帳戶和密碼的資料還原：

- 還原-AzKeyVaultCertificate
- 還原-AzKeyVaultKey
- 還原-AzKeyVaultManagedStorageAccount
- 還原-AzKeyVaultSecret

如何還原 Key Vault 憑證： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

如何還原 Key Vault 金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

如何還原 Key Vault 受管理的儲存體帳戶： https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

如何還原 Key Vault 秘密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指導**方針：確定已針對 Azure Key Vault 啟用虛刪除。 虛刪除可復原已刪除的金鑰保存庫和保存庫物件，例如金鑰、秘密和憑證。 

如何使用 Azure Key Vault 的虛刪除： 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為您的組織建立事件回應指南。 請確定有寫入的事件回應計畫，可定義人員的所有角色，以及從偵測到事件處理/管理的階段，以進行後續事件審查。 這些處理常式應該著重于保護機密系統，例如使用 Key Vault 秘密的系統。



如何設定 Azure 資訊安全中心內的工作流程自動化： 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   



建立您自己的安全性事件回應程式的指引：  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/



Microsoft 安全性回應中心的事件剖析：   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   



客戶也可以利用 NIST 的「電腦安全性性」事件處理指南，協助建立自己的事件回應計畫： 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程式

**指導**方針：資訊安全中心指派每個警示的嚴重性，以協助您排定應先調查哪些警示。 嚴重性是根據資訊安全中心在尋找中的信心，或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級。 此外，請清楚地標示訂閱（例如， 生產、非生產）並建立命名系統，以清楚地識別和分類 Azure 資源，特別是處理敏感性資料（例如 Azure Key Vault 秘密）。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程式

**指導**方針：執行練習以定期測試系統的事件回應功能，以協助保護您的 Azure Key Vault 實例和相關資源。 識別弱式點和間距，並視需要修訂計畫。


請參閱 NIST 的發行集：適用于 IT 計畫和功能的測試、訓練和練習程式指南： 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指導**方針：如果 Microsoft 安全性回應中心（MSRC）發現您的資料已由非法或未經授權的合作物件存取，microsoft 將會使用安全性事件連絡人資訊來與您聯繫。  檢查事實後的事件，以確保解決問題。



如何設定 Azure 資訊安全中心安全性連絡人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示納入事件回應系統中

**指引**：使用「連續匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure Key Vault 啟用資源的風險。 「連續匯出」可讓您以手動或持續的方式來匯出警示和建議。  您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。 

 

如何設定連續匯出： 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

如何將警示串流至 Azure Sentinel： 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護受 Azure Key Vault 保護的資源。 

 

如何設定工作流程自動化和 Logic Apps： 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在60天內補救所有重大安全性結果

**指導**方針：您不會直接在 Azure Key Vault 服務上執行畫筆測試，但建議您測試使用 Key Vault 的 Azure 資源，以確保秘密的安全性。


您必須遵循 Microsoft Engagement 規則，以確保您的滲透測試不會違反 Microsoft 原則：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1


您可以在這裡找到有關 Microsoft 管理之雲端基礎結構、服務和應用程式的 Microsoft 策略與執行的 Red 小組和即時網站滲透測試的詳細資訊： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視**：不適用

**責任**：共用

