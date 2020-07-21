---
title: 適用于事件中樞的 Azure 安全性基準
description: 適用于事件中樞的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e49a6b8000def0fe175930629b33203adec1b0f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537049"
---
# <a name="azure-security-baseline-for-event-hubs"></a>適用于事件中樞的 Azure 安全性基準

適用于事件中樞的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指引**：事件中樞與虛擬網路服務端點的整合，可讓您安全地從工作負載（例如系結至虛擬網路的虛擬機器）存取訊息功能，而兩端的網路流量路徑都受到保護。

一旦系結到至少一個虛擬網路子網服務端點，個別的事件中樞命名空間就不會再接受來自虛擬網路中任何來自于已授權子網的流量。 從虛擬網路的觀點來看，將您的事件中樞命名空間系結至服務端點，會設定從虛擬網路子網到訊息服務的隔離網路通道。 

您也可以建立私人端點，這是一種網路介面，可讓您私下安全地使用 Azure 私人連結服務 Azure 事件中樞服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 

您也可以使用防火牆來保護您的 Azure 事件中樞命名空間。 Azure 事件中樞支援以 IP 為基礎的存取控制，以提供輸入防火牆支援。 您可以使用 [Azure 入口網站]、[Azure Resource Manager 範本] 或 [Azure CLI] 或 [Azure PowerShell] 來設定防火牆規則。

如何搭配 Azure 事件中樞使用虛擬網路服務端點：https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

如需詳細資訊，請參閱整合 Azure 事件中樞與 Azure 私人連結： https://docs.microsoft.com/azure/event-hubs/private-link-service 。

啟用事件中樞命名空間上的虛擬網路整合和防火牆：https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

如何設定 Azure 事件中樞命名空間的 IP 防火牆規則：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導**方針：使用 Azure 資訊安全中心並遵循網路保護建議，協助保護您在 Azure 中的事件中樞資源。 如果您使用 Azure 虛擬機器來存取您的事件中樞，請啟用網路安全性群組（NSG）流量記錄，並將記錄傳送到儲存體帳戶以進行流量審核。

如何啟用 NSG 流量記錄： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

瞭解 Azure 資訊安全中心所提供的網路安全性：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導**方針：在與事件中樞相關聯的虛擬網路上啟用 DDoS 保護標準，以防範分散式阻斷服務（DDoS）攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

如何設定 DDoS 保護：[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../virtual-network/manage-ddos-protection.md)

如需 Azure 資訊安全中心整合式威脅情報的詳細資訊：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：如果使用 Azure 虛擬機器來存取您的事件中樞，請啟用網路安全性群組（NSG）流量記錄，並將記錄傳送到儲存體帳戶以進行流量審核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

如果需要調查異常活動，請啟用網路監看員封包捕捉。

如何啟用 NSG 流量記錄： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用及使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

如何啟用網路監看員：https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導**方針：如果使用 Azure 虛擬機器來存取您的事件中樞，請從支援具有承載檢查功能之 IDS/IPS 功能的 Azure Marketplace 中選取供應專案。 如果您的組織不需要以承載檢查為基礎的入侵偵測和/或防護，您可以使用 Azure 事件中樞的內建防火牆功能。 您可以使用防火牆規則，針對限制範圍的 IP 位址或特定的 IP 位址，限制事件中樞命名空間的存取權。

Azure Marketplace：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

如何在事件中樞中為指定的 IP 位址新增防火牆規則：

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 資訊安全中心監視**：尚未提供

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：不適用，這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則，為與您的 Azure 事件中樞命名空間相關聯的網路資源定義和實施標準安全性設定。 使用 "node.js" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行事件中樞命名空間的網路設定。 您也可以使用與 Azure 事件中樞相關的內建原則定義，例如：

- 事件中樞應使用虛擬網路服務端點。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

適用于事件中樞命名空間的 Azure 內建原則：https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



網路功能的 Azure 原則範例： https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



如何建立 Azure 藍圖： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：使用虛擬網路的標籤，以及與事件中樞相關聯的網路安全性和流量的其他相關資源。

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視網路資源設定，並偵測與 Azure 事件中樞相關的網路資源變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

如何檢視及擷取 Azure 活動記錄事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中建立警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針：不適用;Microsoft 會針對記錄檔中的時間戳記，維護用於 Azure 資源的時間來源，例如 Azure 事件中樞。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：在 Azure 監視器中，設定活動記錄檔和事件中樞診斷設定中與事件中樞相關的記錄，以將記錄傳送到 Log Analytics 工作區，以供查詢或儲存到儲存體帳戶以進行長期保存儲存。

如何設定 Azure 事件中樞的診斷設定：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

瞭解 Azure 活動記錄檔：https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指引**：啟用 Azure 事件中樞命名空間的診斷設定。 Azure 事件中樞的診斷設定有三種類別：封存記錄、作業記錄和自動調整記錄。 啟用作業記錄以捕捉事件中樞操作期間發生之情況的相關資訊，具體而言，就是操作類型，包括建立事件中樞、使用的資源，以及作業的狀態。

此外，您可以啟用 Azure 活動記錄診斷設定，並將它們傳送至 Azure 儲存體帳戶、事件中樞或 Log Analytics 工作區。 活動記錄可讓您深入瞭解在您的 Azure 事件中樞和其他資源上執行的作業。 使用活動記錄，您可以決定對 Azure 事件中樞命名空間採取的任何寫入作業（PUT、POST、DELETE）的「內容、物件和時間」。

如何啟用 Azure 事件中樞的診斷設定：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何啟用 Azure 活動記錄的診斷設定：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，根據貴組織的相容性法規來設定您的 Log Analytics 工作區保留期間，以捕捉及查看事件中樞相關事件。

如何設定 Log Analytics 工作區的記錄檔保留參數：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指引**：分析和監視記錄中的異常行為，並定期查看與事件中樞相關的結果。 使用 Azure 監視器的 Log Analytics 檢閱記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。
 

如需 Log Analytics 工作區的詳細資訊：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

如何讓 Azure Sentinel 上線： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指引**：在 Azure 監視器中，設定與活動記錄檔中 Azure 事件中樞相關的記錄，並事件中樞診斷設定，將記錄傳送到 Log Analytics 工作區，以供查詢或長期保存儲存體的儲存體帳戶。 使用 Log Analytics 工作區，針對在安全性記錄檔和事件中找到的異常活動建立警示。

或者，您可以啟用和麵板上的資料來 Azure Sentinel。 

瞭解 Azure 活動記錄：https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

如何設定 Azure 事件中樞的診斷設定：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何對 Log Analytics 工作區記錄資料發出警示：https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

如何讓 Azure Sentinel 上線： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：尚未提供

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：不適用;事件中樞不會處理反惡意程式碼記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;事件中樞不會處理或產生 DNS 相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： AZURE ACTIVE DIRECTORY （AD）具有必須明確指派且可查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。 

如何使用 PowerShell 在 Azure AD 中取得目錄角色：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針：事件中樞的控制平面存取是透過 AZURE ACTIVE DIRECTORY （AD）來控制。 Azure AD 沒有預設密碼的概念。

事件中樞的資料平面存取是透過受控識別或應用程式註冊的 Azure AD，以及共用存取簽章來控制。 連線至事件中樞的用戶端會使用共用存取簽章，而且可以隨時重新產生。

瞭解事件中樞的共用存取簽章：https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶

- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

如何使用 Azure 資訊安全中心來監視身分識別和存取（預覽）：https://docs.microsoft.com/azure/security-center/security-center-identity-access

如何使用 Azure 原則：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針： Microsoft Azure 針對以 AZURE ACTIVE DIRECTORY （AD）為基礎的資源和應用程式，提供整合式存取控制管理。 搭配 Azure 事件中樞使用 Azure AD 的主要優點是您不再需要將認證儲存在程式碼中。 相反地，您可以從 Microsoft 身分識別平臺要求 OAuth 2.0 存取權杖。 要求權杖的資源名稱為 HTTPs： \/ /eventhubs.azure.net/。 Azure AD 會驗證執行應用程式的安全性主體（使用者、群組或服務主體）。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權 Azure 事件中樞資源的要求。

如何使用 Azure AD 存取事件中樞資源來驗證應用程式：https://docs.microsoft.com/azure/event-hubs/authenticate-application

瞭解具有 Azure AD 的 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory 多重要素驗證（MFA），並遵循 Azure 資訊安全中心身分識別和存取管理建議來協助保護已啟用事件中樞的資源。

如何在 Azure 中啟用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 資訊安全中心監視身分識別與存取： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導**方針：使用具有多重要素驗證（MFA）的特殊許可權存取工作站（PAW），其設定為登入並設定已啟用事件中樞的資源。

了解特殊權限存取工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。 使用 Azure AD 風險偵測來根據有風險的使用者行為來查看警示和報告。 如需其他記錄，請將 Azure 資訊安全中心風險偵測警示傳送至 Azure 監視器，並使用動作群組來設定自訂警示/通知。

如何部署 Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 風險偵測： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

如何設定自訂警示和通知的動作群組：https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。



如何在 Azure 中設定具名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AD）做為 Azure 資源（例如事件中樞）的中央驗證和授權系統。 這可允許以角色為基礎的存取控制（RBAC）來管理機密資源。

 如何建立和設定 Azure AD 實例：https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

若要瞭解 Azure 事件中樞如何與 Azure Active Directory （AAD）整合，請參閱使用 Azure Active Directory 授權存取事件中樞資源：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**： AZURE ACTIVE DIRECTORY （AD）提供可協助您探索過時帳戶的記錄檔。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

另外，請定期輪替您事件中樞的共用存取簽章。

瞭解 Azure AD 報告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 身分識別存取權檢閱： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

瞭解事件中樞的共用存取簽章：https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指引**：您可以存取 AZURE ACTIVE DIRECTORY （AD）登入活動、audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。

若要簡化此程式，您可以建立 Azure AD 使用者帳戶的診斷設定，並將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 中設定所需的記錄警示。

如何將 Azure 活動記錄整合到 Azure 監視器中： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

使用 Azure Active Directory 授權事件中樞資源的存取權：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 Azure Active Directory 的身分識別保護和風險偵測功能，針對偵測到已啟用事件中樞資源的可疑動作設定自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行貴組織的安全性回應。

如何檢視 Azure AD 風險性登入： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定和啟用身分識別保護風險原則：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何讓 Azure Sentinel 上線： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：目前無法使用;事件中樞尚不支援客戶加密箱。

客戶加密箱支援的服務清單：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指引**：在與您的事件中樞相關的資源上使用標記，以協助追蹤儲存或處理敏感資訊的 Azure 資源。

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 事件中樞命名空間應該以已啟用服務端點並適當標記的虛擬網路分隔。

您也可以使用防火牆來保護您的 Azure 事件中樞命名空間。 Azure 事件中樞支援以 IP 為基礎的存取控制，以提供輸入防火牆支援。 您可以使用 [Azure 入口網站]、[Azure Resource Manager 範本] 或 [Azure CLI] 或 [Azure PowerShell] 來設定防火牆規則。

如何建立其他 Azure 訂用帳戶： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組： https://docs.microsoft.com/azure/governance/management-groups/create

設定 Azure 事件中樞命名空間的 IP 防火牆規則：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

如何建立和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何建立虛擬網路：https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指引**：使用虛擬機器來存取您的事件中樞時，請使用虛擬網路、服務端點、事件中樞防火牆、網路安全性群組和服務標籤，以降低資料外泄的可能性。

Microsoft 會管理 Azure 事件中樞的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

設定 Azure 事件中樞命名空間的 IP 防火牆規則：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

瞭解 Azure 事件中樞的虛擬網路服務端點：https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

將 Azure 事件中樞與 Azure 私人連結整合：https://docs.microsoft.com/azure/event-hubs/private-link-service

瞭解網路安全性群組和服務標記：https://docs.microsoft.com/azure/virtual-network/security-overview

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針： Azure 事件中樞預設會強制執行 TLS 加密的通訊。 目前支援 TLS 版本1.0、1.1 和1.2。 不過，TLS 1.0 和1.1 位於整個產業的淘汰路徑，因此，請盡可能使用 TLS 1.2。

若要瞭解事件中樞的安全性功能，請參閱網路安全性：https://docs.microsoft.com/azure/event-hubs/network-security

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure 事件中樞尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**： Azure 事件中樞支援使用 AZURE ACTIVE DIRECTORY （AD）來授權事件中樞資源的要求。 使用 Azure AD，您可以使用角色型存取控制（RBAC），將許可權授與安全性主體，這可能是使用者或應用程式服務主體。

瞭解 Azure AD RBAC 和適用于 Azure 事件中樞的角色：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項準則主要用於計算資源。

Microsoft 會管理事件中樞的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針： Azure 事件中樞支援使用 Microsoft 管理的金鑰或客戶管理的金鑰來加密待用資料的選項。 這項功能可讓您建立、輪替、停用及撤銷用來加密待用資料 Azure 事件中樞的客戶管理金鑰的存取權。

如何設定客戶管理的金鑰來加密 Azure 事件中樞：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對 Azure 事件中樞的生產實例和其他重要或相關資源進行變更時，建立警示。

如何建立 Azure 活動記錄事件的警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：不適用;Microsoft 會在支援事件中樞的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：不適用;Microsoft 會在支援事件中樞的基礎系統上執行修補程式管理。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指導**方針：不適用;基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針：不適用;Microsoft 會在支援事件中樞的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：不適用;Microsoft 會在支援事件中樞的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢和探索訂用帳戶內的所有資源（包括 Azure 事件中樞命名空間）。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

如何使用 Azure Resource Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂用帳戶： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**.. 適當地使用標記、管理群組和個別的訂用帳戶來組織和追蹤 Azure 事件中樞命名空間和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

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

- 不允許的資源類型

- 允許的資源類型

此外，請使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

- 不允許的資源類型

- 允許的資源類型

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 原則拒絕特定的資源類型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11： <div>限制使用者透過指令碼來與 Azure Resource Manager 互動的能力</div>

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

如何設定條件式存取以封鎖 Azure Resource Manager 的存取： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

如需詳細資訊，請參閱[安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：定義和執行 Azure 事件中樞部署的標準安全性設定。 使用 "Microsoft EventHub" 命名空間中 Azure 原則別名來建立自訂原則，以進行審核或強制執行設定。 您也可以利用內建的原則定義來進行 Azure 事件中樞，例如：

- 應啟用事件中樞內的診斷記錄

- 事件中樞應該使用虛擬網路服務端點

適用于事件中樞命名空間的 Azure 內建原則：https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

如何檢視可用的 Azure 原則別名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure 原則 [拒絕] 和 [不存在時部署]，在已啟用事件中樞的資源上強制執行安全設定。 

如何設定和管理 Azure 原則：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
如需 Azure 原則效果的詳細資訊：https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果您事件中樞或相關資源使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

如何在 Azure DevOps 中儲存程式碼： https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文件： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指引**：在 "node.js" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審查和強制執行系統設定。 此外，開發流程和管線以管理原則例外狀況。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指引**：在 "node.js" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審查和強制執行系統設定。 使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在]，自動為您的 Azure 事件中樞部署和相關資源強制執行設定。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：針對在 Azure App Service 用來存取事件中樞的 Azure 虛擬機器或 web 應用程式，請使用受控服務識別搭配 Azure Key Vault，以簡化及保護 Azure 事件中樞部署的共用存取簽章管理。 請確定已啟用 Key Vault 虛刪除。

使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源：https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

為事件中樞設定客戶管理的金鑰：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

如何與 Azure 受控識別整合： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Key Vault： https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用受控識別提供 Key Vault 驗證： https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指引**：針對在 Azure App Service 用來存取事件中樞的 Azure 虛擬機器或 web 應用程式，請使用受控服務識別搭配 Azure Key Vault，以簡化 Azure 事件中樞的保護。 請確定已啟用 Key Vault 虛刪除。

使用受控識別，在 Azure Active Directory (AD) 中為 Azure 服務提供自動受控識別。 受控識別可讓您向任何支援 Azure AD 驗證的服務進行驗證，包括 Azure Key Vault，而您的程式碼中沒有任何認證。

使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源：https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

為事件中樞設定客戶管理的金鑰：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

如何設定受控識別： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何與 Azure 受控識別整合： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機 (例如 Azure App Service) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：預先掃描要上傳至非計算 Azure 資源的任何內容，例如 Azure 事件中樞、App Service、Data Lake Storage、Blob 儲存體、適用於 PostgreSQL 的 Azure 資料庫等等。Microsoft 無法存取您在這些實例中的資料。

支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼（例如 Azure Cache for Redis），但它不會在客戶內容上執行。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：設定 Azure 事件中樞的異地嚴重損壞修復。 當整個 Azure 區域或資料中心 (如果未使用可用性區域) 遇到停機時，最重要的是資料處理作業能夠繼續在不同的區域或資料中心運作。 因此，「地理災害復原」和「異地複寫」對於任何企業而言都是重要的功能。 Azure 事件中樞支援命名空間層級的地理災害復原和異地複寫。 

瞭解 Azure 事件中樞的異地嚴重損壞修復：https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指導**方針： Azure 事件中樞使用 Azure 儲存體服務加密（Azure SSE）提供待用資料的加密。 事件中樞依賴 Azure 儲存體來儲存資料，而且根據預設，與 Azure 儲存體一起儲存的所有資料都會使用 Microsoft 管理的金鑰進行加密。 如果您使用 Azure Key Vault 來儲存客戶管理的金鑰，請確保金鑰的定期自動備份。

使用下列 PowerShell 命令，確保定期自動備份您的 Key Vault 秘密：備份-AzKeyVaultSecret

如何設定客戶管理的金鑰來加密待用 Azure 事件中樞資料：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

如何備份 Key Vault 秘密：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：測試已備份的客戶管理金鑰的還原。

 

如何在 Azure 中還原金鑰保存庫金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指引**：在 Key Vault 中啟用虛刪除，以防止意外或惡意刪除的金鑰。 Azure 事件中樞需要客戶管理的金鑰才能進行虛刪除，而且不會進行清除設定。

針對用來捕獲事件中樞資料的 Azure 儲存體帳戶設定虛刪除。 請注意，Azure Data Lake Storage Gen 2 尚不支援這項功能。

如何在 Key Vault 中啟用虛刪除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

使用金鑰來設定金鑰保存庫：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Azure 儲存體 blob 的虛刪除：https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：確定有撰寫的事件回應計畫會定義人員的角色，以及事件處理/管理的階段。

如何設定 Azure 資訊安全中心內的工作流程自動化： https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針：資訊安全中心指派警示的嚴重性，協助您排定參與每個警示的順序，如此一來，當資源遭到入侵時，您就可以立即開始使用。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程式

**指導**方針：執行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

請參閱 NIST 的出版物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (IT 計畫和能力的測試、訓練和練習方案指南)： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢事件，以確保問題已解決。 

如何設定 Azure 資訊安全中心的安全性連絡人： https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

如何設定連續匯出： https://docs.microsoft.com/azure/security-center/continuous-export

如何將警示串流至 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用 Azure 資訊安全中心的工作流程自動化功能，以透過 "Logic Apps" 自動觸發對安全性警示和建議的回應。

如何設定工作流程自動化和 Logic Apps： https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指導**方針：請遵循 Microsoft Engagement 規則，以確保您的滲透測試不會違反 microsoft 原則： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 。
您可以在這裡找到有關 Microsoft 受管理的雲端基礎結構、服務和應用程式的 Microsoft 策略與執行的 Red 小組和即時網站滲透測試的詳細資訊：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md) (機器翻譯)
- 深入了解 [Azure 安全性基準](../security/benchmarks/security-baselines-overview.md) (機器翻譯)
