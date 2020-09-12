---
title: 適用于 Azure Cache for Redis 的 Azure 安全性基準
description: 適用于 Azure Cache for Redis 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0ab9eb36e9d254c4dab5aed36ec6e9784f04d958
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400733"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>適用于 Azure Cache for Redis 的 Azure 安全性基準

適用于 Azure Cache for Redis 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導**方針：在虛擬網路中部署 Azure Cache for Redis 實例 (VNet) 。 VNet 是雲端中的私人網路。 當 Azure Cache for Redis 執行個體是以 VNet 設定時，它將無法公開定址，而只能從 VNet 中的虛擬機器和應用程式存取。

您也可以指定具有開始和結束 IP 位址範圍的防火牆規則。 設定防火牆規則時，只有來自指定 IP 位址範圍的用戶端連線可以連接至快取。

如何設定 Premium Azure Cache for Redis 的虛擬網路支援：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

如何設定 Azure Cache for Redis 防火牆規則：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導**方針：當虛擬機器部署在與 Azure Cache for Redis 實例相同的虛擬網路時，您可以使用網路安全性群組 (NSG) 來降低資料遭到外泄的風險。 啟用 NSG 流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

如何啟用 NSG 流量記錄： 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用及使用流量分析： 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針： Azure 虛擬網路 (VNet) 部署為您的 Azure Cache for Redis 提供增強的安全性和隔離，以及子網、存取控制原則及其他功能，以進一步限制存取。 在 VNet 中部署時，Azure Cache for Redis 無法公開定址，且只能從 VNet 內的虛擬機器和應用程式存取。

在與 Azure Cache for Redis 實例相關聯的 Vnet 上啟用 DDoS 保護標準，以防止分散式阻絕服務 (DDoS) 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

如何設定 Premium Azure Cache for Redis 的虛擬網路支援：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

使用 Azure 入口網站管理 Azure DDoS 保護 Standard：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：當虛擬機器部署在與 Azure Cache for Redis 實例相同的虛擬網路時，您可以使用網路安全性群組 (NSG) 來降低資料遭到外泄的風險。 啟用 NSG 流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

如何啟用 NSG 流量記錄： 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用及使用流量分析： 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導**方針：當您使用 Azure Cache for Redis 在 Azure App Service 或計算實例上執行的 web 應用程式時，請在 Azure 虛擬網路內部署所有資源 (VNet) ，並使用 (web 應用程式閘道上的 Azure Web 應用程式防火牆) WAF 進行保護。 將 WAF 設定為以「防止模式」執行。 預防模式會封鎖規則偵測到的入侵和攻擊。 攻擊者會收到「403 未經授權存取」例外狀況，且連線會關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

或者，您也可以從 Azure Marketplace 選取供應專案，以支援具有承載檢查和/或異常偵測功能的 IDS/IPS 功能。

瞭解 Azure WAF 功能：

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

如何部署 Azure WAF：

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組 (NSG) 或 Azure 防火牆的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

您也可以使用 (ASG) 的應用程式安全性群組，協助簡化複雜的安全性設定。 Asg 可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。

虛擬網路服務標記：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

應用程式安全性群組：

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則來定義和執行與您的 Azure Cache for Redis 實例相關之網路資源的標準安全性設定。 使用 "Microsoft. Cache" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Cache for Redis 實例的網路設定進行審核或強制執行。 您也可以利用內建的原則定義，例如：

應該只允許對 Redis Cache 的安全連線

應啟用 DDoS 保護標準

您也可以使用 Azure 藍圖，將金鑰環境成品（例如 Azure Resource Manager (ARM) 範本、Azure 角色型存取控制 (Azure RBAC) 和原則）封裝在單一藍圖定義中，以簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂閱、環境，以及透過版本控制來微調控制和管理。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何建立 Azure 藍圖： 

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：針對與您 Azure Cache for Redis 部署相關聯的網路資源使用標籤，以便以邏輯方式將它們組織成分類法。

如何建立和使用標籤： 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與 Azure Cache for Redis 實例相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

如何檢視及擷取 Azure 活動記錄事件： 

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中建立警示： 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的 Azure Cache for Redis。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在控制平面層級的 Azure Cache for Redis 實例上所執行的作業。 您可以使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在 Azure Cache for Redis 實例的控制平面層級執行。

如何啟用 Azure 活動記錄的診斷設定： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指引**：啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在控制平面層級的 Azure Cache for Redis 實例上所執行的作業。 您可以使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在 Azure Cache for Redis 實例的控制平面層級執行。

雖然可以藉由啟用診斷設定來取得計量，但資料平面上的 audit 記錄還無法用於 Azure Cache for Redis。

如何啟用 Azure 活動記錄的診斷設定： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：在 Azure 監視器中，根據您組織的合規性法規，為與您的 Azure Cache for Redis 實例相關聯的 log Analytics 工作區設定記錄保留期限。

請注意，資料平面上的 audit 記錄還無法供 Azure Cache for Redis。

如何設定記錄保留參數：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導**方針：啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢，以搜尋詞彙、識別趨勢、分析模式，以及根據可能已針對 Azure Cache for Redis 收集的活動記錄資料，提供許多其他見解。

請注意，資料平面上的 audit 記錄還無法供 Azure Cache for Redis。

如何啟用 Azure 活動記錄的診斷設定： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

如何在 Azure 監視器中收集和分析 Log Analytics 工作區中的 Azure 活動記錄： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針：您可以設定，以根據與 Azure Cache for Redis 實例相關的計量和活動記錄來接收警示。 Azure 監視器可讓您設定警示以傳送電子郵件通知、呼叫 webhook，或叫用 Azure 邏輯應用程式。

雖然可以藉由啟用診斷設定來取得計量，但資料平面上的 audit 記錄還無法用於 Azure Cache for Redis。

如何設定 Azure Cache for Redis 的警示： https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：不適用;Azure Cache for Redis 不會處理或產生反惡意程式碼的相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;Azure Cache for Redis 不會處理或產生 DNS 相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： AZURE ACTIVE DIRECTORY (AD) 具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

如何使用 PowerShell 在 Azure AD 中取得目錄角色： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Azure Cache for Redis 的控制平面存取權是透過 AZURE ACTIVE DIRECTORY (AD) 來控制。 Azure AD 沒有預設密碼的概念。 

Azure Cache for Redis 的資料平面存取權是透過存取金鑰來控制。 連接到您快取的用戶端會使用這些金鑰，而且可以隨時重新產生。

不建議您在應用程式中建立預設密碼。 相反地，您可以將密碼儲存在 Azure Key Vault 中，然後使用 Azure Active Directory 來取得它們。

如何重新產生 Azure Cache for Redis 存取金鑰： https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶

- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽) ： https://docs.microsoft.com/azure/security-center/security-center-identity-access

如何使用 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針： Azure Cache for Redis 會使用存取金鑰來驗證使用者，且不支援在資料平面層級 (SSO) 的單一登入。 Azure Cache for Redis 的控制平面存取權可透過 REST API 取得，並支援 SSO。 若要驗證，請將要求的授權標頭設定為從 Azure Active Directory 取得的 JSON Web 權杖。

瞭解 Azure Cache for Redis REST API： https://docs.microsoft.com/rest/api/redis/

了解使用 Azure AD 的 SSO： https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory (AD) 多重要素驗證 (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

如何在 Azure 中啟用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 資訊安全中心監視身分識別與存取： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有的系統管理工作

**指引**：使用已設定多重要素驗證 (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

瞭解特殊許可權存取工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA： 

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

如何部署 Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 風險偵測： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：在 AZURE ACTIVE DIRECTORY (AD 中設定命名位置) 條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。

如何在 Azure 中設定具名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指引**：使用 Azure Active Directory (AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

Azure AD 驗證無法用來直接存取 Azure Cache for Redis 的資料平面，不過 Azure AD 的認證可用於控制平面層級的系統管理 (也就是 Azure 入口網站) ，以控制 Azure Cache for Redis 的存取金鑰。


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： AZURE ACTIVE DIRECTORY (AD) 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。 

瞭解 Azure AD 報告： https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 身分識別存取權檢閱： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指導**方針：您可以存取 AZURE ACTIVE DIRECTORY (AD) 登入活動、audit 和風險事件記錄檔來源，讓您可以與 Azure Sentinel 或協力廠商 SIEM 整合。

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 中設定所需的記錄警示。

如何將 Azure 活動記錄整合到 Azure 監視器中： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

如何在面板上 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：若要控制平面上的帳戶登入行為偏差，請使用 AZURE ACTIVE DIRECTORY (AD) Identity Protection 和風險偵測功能，以對偵測到與使用者身分識別相關的可疑動作設定自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

如何檢視 Azure AD 風險性登入： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定及啟用 Identity Protection 風險原則： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何讓 Azure Sentinel 上線： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**：尚未提供;Azure Cache for Redis 尚不支援客戶加密箱。

客戶加密箱支援的服務清單：

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 資訊安全中心監視**：目前無法使用

**責任**：不適用

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 Azure Cache for Redis 實例應以虛擬網路/子網分隔並適當地標記。 您也可以選擇使用 Azure Cache for Redis 防火牆來定義規則，如此一來，只有來自指定 IP 位址範圍的用戶端連線才能連接至快取。

如何建立其他 Azure 訂用帳戶： 

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組： 

https://docs.microsoft.com/azure/governance/management-groups/create

如何將 Azure Cache for Redis 部署至 Vnet：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

如何設定 Azure Cache for Redis 防火牆規則：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

如何建立和使用標籤： 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**：尚未提供;Azure Cache for Redis 尚無法使用資料識別、分類和遺失防護功能。

Microsoft 管理 Azure Cache for Redis 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針： Azure Cache for Redis 預設需要 TLS 加密通訊。 目前支援 TLS 版本1.0、1.1 和1.2。 不過，TLS 1.0 和1.1 都是在整個產業淘汰的路徑上，因此請盡可能使用 TLS 1.2。 如果您的用戶端程式庫或工具不支援 TLS，則啟用未加密的連接可透過 Azure 入口網站或管理 Api 來完成。 在無法進行加密連接的情況下，建議您將快取和用戶端應用程式放入虛擬網路中。

瞭解 Azure Cache for Redis 傳輸中加密：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

瞭解 Vnet 快取案例中所使用的必要端口：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure Cache for Redis 尚無法使用資料識別、分類和遺失防護功能。 標記包含敏感性資訊的實例，並視需要執行協力廠商解決方案，以符合合規性需求。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制對 Azure Cache for Redis 控制平面的存取 (即 Azure 入口網站) 。 

如何設定 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。

Microsoft 管理 Azure Cache for Redis 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

了解 Azure 中的客戶資料保護： 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針： Azure Cache for Redis 會將客戶資料儲存在記憶體中，而且在受到 Microsoft 所執行的許多控制項的高度保護下，預設不會加密記憶體。 如果您的組織需要的話，請先將內容加密，然後再儲存在 Azure Cache for Redis 中。

如果使用「Redis 資料持續性」 Azure Cache for Redis 功能，資料會傳送至您所擁有和管理的 Azure 儲存體帳戶。 您可以在快取建立期間，以及在現有高階快取的 [資源] 功能表上，設定 [新增 Azure Cache for Redis] 分頁的持續性。

Azure 儲存體中的資料會以透明的方式使用256位 AES 加密（可用的最強區塊編碼器之一）進行加密和解密，並符合 FIPS 140-2 規範。 無法停用 Azure 儲存體加密。 您可以依賴 Microsoft 管理的金鑰來加密您的儲存體帳戶，也可以使用您自己的金鑰來管理加密。

如何在 Azure Cache for Redis 中設定持續性： https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

瞭解 Azure 儲存體帳戶的加密： https://docs.microsoft.com/azure/storage/common/storage-service-encryption

瞭解 Azure 客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對 Azure Cache for Redis 和其他重要或相關資源的生產實例進行變更時，建立警示。

如何建立 Azure 活動記錄事件的警示： 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：遵循 Azure 資訊安全中心保護 Azure Cache for Redis 實例和相關資源的建議。

Microsoft 會在支援 Azure Cache for Redis 的基礎系統上執行弱點管理。

瞭解 Azure 資訊安全中心建議： https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針： Microsoft 會在支援 Azure Cache for Redis 的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

如何使用 Azure Resource Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂用帳戶： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別的訂用帳戶（如果適當）來組織和追蹤 Azure Cache for Redis 的實例和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

此外，您也可以使用下列內建原則定義，使用 Azure 原則對可在客戶訂用帳戶 () s 中建立的資源類型進行限制：

- 不允許的資源類型

- 允許的資源類型

如何建立其他 Azure 訂用帳戶： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組： https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指引**：不適用，這項建議主要用於計算資源和整體 Azure。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

不允許的資源類型

允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢/探索訂用帳戶 () 中的資源。

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢： 

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源和整體 Azure。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

不允許的資源類型

允許的資源類型

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 原則拒絕特定的資源類型： 

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：透過指令碼限制使用者與 Azure Resource Manager 互動的能力

**指導**方針：設定 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 AZURE RESOURCE MANAGER (ARM) 互動的能力。

如何設定條件式存取以封鎖對 ARM 的存取：

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則定義和執行 Azure Cache for Redis 實例的標準安全性設定。 使用 "Microsoft. Cache" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Cache for Redis 實例的設定進行審核或強制執行。 您也可以使用與 Azure Cache for Redis 實例相關的內建原則定義，例如：

應該只允許對 Redis Cache 的安全連線

如何檢視可用的 Azure 原則別名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 原則效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果您的 Azure Cache for Redis 實例和相關資源使用自訂 Azure 原則定義或 Azure Resource Manager 範本，請使用 Azure Repos 安全地儲存和管理您的程式碼。

如何在 Azure DevOps 中儲存程式碼： https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文件： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導**方針：使用 "Microsoft. Cache" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指導**方針：使用 "Microsoft. Cache" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在]，自動強制執行 Azure Cache for Redis 實例和相關資源的設定。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：適用于在 Azure App Service 上執行的 Azure 虛擬機器或 web 應用程式可用來存取您的 Azure Cache for Redis 實例，請使用受控服務識別搭配 Azure Key Vault 以簡化 Azure Cache for Redis 秘密管理的安全。 確定已啟用 Key Vault 虛刪除。

如何與 Azure 受控識別整合： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Key Vault： 

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何驗證 Key Vault：

https://docs.microsoft.com/azure/key-vault/general/authentication

如何指派 Key Vault 存取原則：

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：適用于在 Azure App Service 上執行的 Azure 虛擬機器或 web 應用程式可用來存取您的 Azure Cache for Redis 實例，請使用受控服務識別搭配 Azure Key Vault 以簡化 Azure Cache for Redis 秘密管理的安全。 確保已啟用 Key Vault 虛刪除。

使用受控識別，在 Azure Active Directory 中為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向任何支援 AAD 驗證的服務進行驗證，包括 Azure Key Vault，而不需要在您的程式碼中提供任何認證。

如何設定受控識別： 

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何與 Azure 受控識別整合： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機 (例如 Azure App Service) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Cache for Redis) ，但不會對客戶內容執行。

預先掃描要上傳至非計算 Azure 資源的任何內容，例如 App Service、Data Lake Storage、Blob 儲存體、適用於 PostgreSQL 的 Azure 資料庫等。Microsoft 無法存取這些執行個體中的資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Cache for Redis) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：啟用 Redis 持續性。 Redis 永續性可讓您保存儲存在 Redis 中的資料。 您也可以擷取快照和備份資料，以在硬體失敗時載入。 這是優於基本或標準層的重大優勢，基本或標準層的所有資料是儲存在記憶體中，若發生快取節點故障的失敗，資料可能會遺失。

您也可以使用 Azure Cache for Redis 匯出。 匯出可讓您將儲存在 Azure Cache for Redis 中的資料匯出至與 Redis 相容的 RDB 檔案。 您可以使用這項功能，將資料從一個 Azure Cache for Redis 執行個體移到另一個執行個體，或移到另一個 Redis 伺服器。 在匯出過程中，會在主控 Azure Cache for Redis server 實例的虛擬機器上建立暫存檔案，並將檔案上傳至指定的儲存體帳戶。 當匯出作業完成時的狀態為成功或失敗時，都會刪除暫存檔案。

如何啟用 Redis 持續性：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

如何使用 Azure Cache for Redis 匯出：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指導**方針：啟用 Redis 持續性。 Redis 永續性可讓您保存儲存在 Redis 中的資料。 您也可以擷取快照和備份資料，以在硬體失敗時載入。 這是優於基本或標準層的重大優勢，基本或標準層的所有資料是儲存在記憶體中，若發生快取節點故障的失敗，資料可能會遺失。

您也可以使用 Azure Cache for Redis 匯出。 匯出可讓您將儲存在 Azure Cache for Redis 中的資料匯出至與 Redis 相容的 RDB 檔案。 您可以使用這項功能，將資料從一個 Azure Cache for Redis 執行個體移到另一個執行個體，或移到另一個 Redis 伺服器。 在匯出過程中，會在主控 Azure Cache for Redis server 實例的虛擬機器上建立暫存檔案，並將檔案上傳至指定的儲存體帳戶。 當匯出作業完成時的狀態為成功或失敗時，都會刪除暫存檔案。

如果使用 Azure Key Vault 儲存 Azure Cache for Redis 實例的認證，請務必定期自動備份您的金鑰。

如何啟用 Redis 持續性：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

如何使用 Azure Cache for Redis 匯出：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

如何備份 Key Vault 金鑰： 

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：使用 Azure Cache for Redis 匯入。 匯入可以用來從任何雲端或環境中執行的 Redis 伺服器（包括在 Linux、Windows 上執行的 Redis，或任何雲端提供者，例如 Amazon Web Services 等），帶入 Redis 相容 RDB 檔案。 匯入資料是使用預先填入資料建立快取的輕鬆方式。 在進行匯入程序的期間，Azure Cache for Redis 會從 Azure 儲存體將 RDB 檔案載入記憶體，然後將金鑰插入快取中。

定期測試 Azure Key Vault 秘密的資料還原。

如何使用 Azure Cache for Redis 匯入：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

如何還原 Key Vault 秘密：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導**方針： Redis 匯出和 Redis 持續性的 Azure Cache for Redis 備份會儲存在您選取的 Azure 儲存體帳戶內。 Azure 儲存體中的資料會以透明的方式使用256位 AES 加密（可用的最強區塊編碼器之一）進行加密和解密，並符合 FIPS 140-2 規範。 無法停用 Azure 儲存體加密。 您可以依賴 Microsoft 管理的金鑰來加密您的儲存體帳戶，也可以使用您自己的金鑰來管理加密。

瞭解 Azure 儲存體帳戶的加密： https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Azure 資訊安全中心監視**：是

**責任**：Microsoft

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

如何設定 Azure 資訊安全中心內的工作流程自動化： 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

建立自有安全性事件回應程序的指引： 

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全性回應中心的事件剖析： 

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客戶也可利用 NIST 的電腦安全性事件處理指南，以協助建立自己的事件回應計畫： 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

請參閱 NIST 的出版物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (IT 計畫和能力的測試、訓練和練習方案指南)： 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢事件，以確保問題已解決。

如何設定 Azure 資訊安全中心的安全性連絡人： 

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

如何設定連續匯出： 

https://docs.microsoft.com/azure/security-center/continuous-export

如何將警示串流至 Azure Sentinel： 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用 Azure 資訊安全中心的工作流程自動化功能，以透過 "Logic Apps" 自動觸發對安全性警示和建議的回應。

如何設定工作流程自動化和 Logic Apps： 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指引**：遵循 Microsoft 參與規則，以確保滲透測試不會違反 Microsoft 原則： 

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

針對 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：  

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/overview) (機器翻譯)
- 深入了解 [Azure 安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)
