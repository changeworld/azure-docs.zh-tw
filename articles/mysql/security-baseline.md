---
title: 適用于適用於 MySQL 的 Azure 資料庫的 Azure 安全性基準
description: 適用於 MySQL 的 Azure 資料庫安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7d01e033b6349861d5d89493aa5132368a53ca09
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201394"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>適用于適用於 MySQL 的 Azure 資料庫的 Azure 安全性基準

適用於 MySQL 的 Azure 資料庫 Azure 安全性基準，會提供有助於改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指引**：使用私人端點為適用於 MySQL 的 Azure 資料庫設定 Private Link。 Private Link 可讓您透過私人端點連線到 Azure 中的各種 PaaS 服務。 Azure Private Link 基本上會將 Azure 服務帶入私人虛擬網路 (VNet) 內部。 您的虛擬網路與 MySQL 執行個體之間的流量會經由 Microsoft 骨幹網路傳輸。

您也可以使用虛擬網路服務端點來保護和限制對適用於 MySQL 的 Azure 資料庫實作的網路存取。 虛擬網路規則是一項防火牆安全性功能，可控制適用於 MySQL 的 Azure 資料庫伺服器是否接受從虛擬網路中特定子網路所傳來的通訊。

您也可以使用防火牆規則保護適用於 MySQL 的 Azure 資料庫伺服器。 伺服器防火牆會防止對資料庫伺服器的所有存取，直到您指定哪些電腦擁有權限。 若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

- [如何設定適用於 MySQL 的 Azure 資料庫的 Private Link](howto-configure-privatelink-portal.md)

- [如何在適用於 MySQL 的 Azure 資料庫中建立和管理 VNet 服務端點和 VNet 規則](../azure-sql/database/vnet-service-endpoint-rule-overview.md)

- [如何設定適用於 MySQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-portal.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指引**：當適用於 MySQL 的 Azure 資料庫執行個體固定於私人端點時，您可以在相同的虛擬網路中部署虛擬機器。 您可使用網路安全性群組 (NSG) 來降低資料外洩的風險。 啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶進行流量稽核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

- [如何設定適用於 MySQL 的 Azure 資料庫的 Private Link](howto-configure-privatelink-portal.md)

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：為適用於 MySQL 的 Azure 資料庫使用 Azure 進階威脅防護。 進階威脅防護會偵測異常活動，指出有不尋常及可能有害的活動試圖存取或惡意探索資料庫。

在與適用於 MySQL 的 Azure 資料庫執行個體相關聯的虛擬網路上，啟用 Azure DDoS 保護標準，以抵禦分散式阻斷服務攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

- [如何設定適用於 MySQL 的 Azure 資料庫的 Advanced 威脅防護](howto-database-threat-protection-portal.md)

- [如何設定 DDoS 保護](../ddos-protection/manage-ddos-protection.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指引**：當適用於 MySQL 的 Azure 資料庫執行個體固定於私人端點時，您可以在相同的虛擬網路中部署虛擬機器。 然後可設定網路安全性群組 (NSG)，以降低資料外洩的風險。 啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶進行流量稽核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指引**：為適用於 MySQL 的 Azure 資料庫使用 Azure 進階威脅防護。 進階威脅防護會偵測異常活動，指出有不尋常及可能有害的活動試圖存取或惡意探索資料庫。

- [如何設定適用於 MySQL 的 Azure 資料庫的 Advanced 威脅防護](howto-database-threat-protection-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指引**：對於需要存取適用於 MySQL 的 Azure 資料庫執行個體的資源，請使用虛擬網路服務標籤，定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的正確來源或目的地欄位中指定服務標籤名稱 (例如 SQL.WestUs)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

注意:適用於 MySQL 的 Azure 資料庫使用「Microsoft.Sql」服務標籤。

- [如需使用服務標記的詳細資訊](../virtual-network/service-tags-overview.md)

- [瞭解適用於 MySQL 的 Azure 資料庫的服務標記使用方式](concepts-data-access-and-security-vnet.md#terminology-and-description)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指引**：使用 Azure 原則，針對與適用於 MySQL 的 Azure 資料庫執行個體相關聯的網路設定和網路資源，定義及實作標準安全性設定。 請在「Microsoft.DBforMySQL」和「Microsoft.Network」命名空間中，使用 Azure 原則別名來建立自訂原則，以稽核或強制執行適用於 MySQL 的 Azure 資料庫執行個體的網路設定。 您也可以使用與網路或適用於 MySQL 的 Azure 資料庫執行個體相關的內建原則定義，例如：

- 應啟用 DDoS 保護標準

- 應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [適用于網路的 Azure 原則範例](../governance/policy/samples/index.md)

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指引**：針對適用於 MySQL 的 Azure 資料庫，使用網路安全性和流量相關的資源標籤，以提供中繼資料和邏輯組織。

使用任何與標記相關的內建 Azure 原則定義，例如 **需要標記和其值** ，以確保所有資源都是使用標籤建立的，並通知您現有的未標記資源。

您可使用 Azure PowerShell 或 Azure CLI，根以據資源的標籤對資源進行查詢或執行動作。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視網路資源設定，並偵測適用於 MySQL 的 Azure 資料庫執行個體相關聯的網路資源變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**：Microsoft 會維護 Azure 資源所使用的時間來源，例如記錄中適用於 MySQL 的 Azure 資料庫的時間戳記。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：啟用診斷設定和伺服器記錄並內嵌記錄，以彙總適用於 MySQL 的 Azure 資料庫執行個體所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶進行長期/封存儲存。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

- [瞭解適用於 MySQL 的 Azure 資料庫的伺服器記錄](concepts-monitoring.md#server-logs)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指引**：在適用於 MySQL 的 Azure 資料庫執行個體上啟用診斷設定，以存取稽核、慢速查詢及 MySQL 計量記錄。 請確定您已特別啟用 MySQL 稽核記錄。 活動記錄 (自動提供) 包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他有用的元素。 您也可以啟用 Azure 活動記錄診斷設定，並將記錄傳送到相同的 Log Analytics 工作區或儲存體帳戶。

- [瞭解適用於 MySQL 的 Azure 資料庫的伺服器記錄](concepts-monitoring.md#server-logs)

- [如何設定及存取適用於 MySQL 的 Azure 資料庫的慢速查詢記錄](howto-configure-server-logs-in-portal.md)

- [如何設定和存取適用於 MySQL 的 Azure 資料庫的 audit 記錄](howto-configure-audit-logs-portal.md)

- [如何設定 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，請在用來保留適用於 MySQL 的 Azure 資料庫記錄的 Log Analytics 工作區，根據所屬組織的合規性規定來設定保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

- [如何設定 Log Analytics 工作區的記錄保留期參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [在 Azure 儲存體帳戶中儲存資源記錄](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**：分析和監視來自適用於 MySQL 的 Azure 資料庫執行個體的記錄，以了解異常行為。 使用 Azure 監視器的 Log Analytics 檢閱記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [有關 Log Analytics 的詳細資訊](../azure-monitor/log-query/log-analytics-tutorial.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指引**：為適用於 MySQL 的 Azure 資料庫啟用 Azure 進階威脅防護。 進階威脅防護會偵測異常活動，指出有不尋常及可能有害的活動試圖存取或惡意探索資料庫。

此外，您可以啟用 MySQL 的伺服器記錄和診斷設定，並將記錄傳送至 Log Analytics 工作區。 請將您的 Log Analytics 工作區在 Azure Sentinel 上線，因為其可提供安全性協調流程自動化回應 (SOAR) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。

- [如何啟用適用於 MySQL 的 Azure 資料庫 (Preview) 的 Advanced 威脅防護 ](howto-database-threat-protection-portal.md)

- [瞭解適用於 MySQL 的 Azure 資料庫的伺服器記錄](concepts-monitoring.md#server-logs)

- [如何設定及存取適用於 MySQL 的 Azure 資料庫的慢速查詢記錄](howto-configure-server-logs-in-portal.md)

- [如何設定和存取適用於 MySQL 的 Azure 資料庫的 audit 記錄](howto-configure-audit-logs-portal.md)

- [如何設定 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指引**：不適用；適用於 MySQL 的 Azure 資料庫不會處理或產生與反惡意程式碼相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指引**：不適用；適用於 MySQL 的 Azure 資料庫不會處理或產生與 DNS 相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指引**：維護具有適用於 MySQL 執行個體的 Azure 資料庫執行個體管理平面 (例如 Azure 入口網站) 管理存取權的使用者帳戶詳細目錄。 此外，可維護適用於 MySQL 的 Azure 資料庫執行個體資料平面 (位於資料庫本身內) 的系統管理帳戶詳細目錄。 (建立 MySQL 伺服器時，您會提供管理員使用者的認證。 此管理員可用於建立其他 MySQL 使用者。)

適用於 MySQL 的 Azure 資料庫不支援內建角色型存取控制，但可根據特定的資源提供者選項來建立自訂角色。

- [瞭解 Azure 訂用帳戶的自訂角色](../role-based-access-control/custom-roles.md) 

- [瞭解適用於 MySQL 的 Azure 資料庫資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdbformysql)

- [瞭解適用於 MySQL 的 Azure 資料庫的存取管理](concepts-security.md#access-management)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指引**：Azure AD 沒有預設密碼的概念。

在建立適用於 MySQL 的 Azure 資料庫資源本身時，Azure 會使用強式密碼強制建立管理使用者。 不過，建立 MySQL 執行個體之後，您可以使用所建立的第一個伺服器管理員帳戶來建立其他使用者，並授與對方管理存取權。 建立這些帳戶時，請確定每個帳戶都設定了不同的強式密碼。

- [如何建立適用於 MySQL 的 Azure 資料庫的其他帳戶](howto-create-users.md)

- [如何更新系統管理員密碼](howto-create-manage-server-portal.md#update-admin-password)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：請使用具有適用於 MySQL 的 Azure 資料庫執行個體存取權的專用系統管理帳戶，建立標準作業程序。 可使用 Azure 資訊安全中心的身分識別與存取管理，監視系統管理帳戶的數目。

- [瞭解 Azure 資訊安全中心身分識別和存取權](../security-center/security-center-identity-access.md)

- [瞭解如何在適用於 MySQL 的 Azure 資料庫中建立系統管理員使用者](howto-create-users.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指引**：可支援透過資料庫中直接設定的使用者名稱/密碼，以及使用 Azure Active Directory (AD) 身分識別並利用 Azure AD 權杖進行連線，來登入適用於 MySQL 的 Azure 資料庫。 使用 Azure AD 權杖時，則可支援不同的方法，例如使用 Azure AD 使用者、Azure AD 群組，或連線至資料庫的 Azure AD 應用程式。

另外可透過 REST API 提供 MySQL 的控制平面存取權，並支援 SSO。 若要驗證，請將授權標頭設為從 Azure Active Directory 取得的 JSON Web 權杖。

- [使用 Azure Active Directory 來向適用於 MySQL 的 Azure 資料庫進行驗證](howto-configure-sign-in-azure-ad-authentication.md)

- [瞭解適用於 MySQL 的 Azure 資料庫 REST API](/rest/api/mysql/)

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指引**：啟用 Azure Active Directory Multi-Factor Authentication (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。 利用 Azure AD 權杖登入資料庫時，即可要求使用多重要素驗證登入資料庫。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [使用 Azure Active Directory 來向適用於 MySQL 的 Azure 資料庫進行驗證](howto-configure-sign-in-azure-ad-authentication.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指引**：使用已設定多重要素驗證 (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

- [瞭解特殊權限存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指引**：為適用於 MySQL 的 Azure 資料庫啟用 Azure 進階威脅防護，針對可疑活動產生警示。

此外，當環境中發生可疑或不安全的活動時，您可以使用 Azure AD Privileged Identity Management (PIM) 來產生記錄和警示。

可使用 Azure AD 風險偵測來檢視風險性使用者行為的相關警示和報告。

- [如何設定適用於 MySQL 的 Azure 資料庫的 Advanced 威脅防護](howto-database-threat-protection-portal.md)

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 風險偵測](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，僅允許從 IP 位址範圍或國家/區域的特定邏輯群組存取入口網站和 Azure Resource Manager。

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指引**：使用 Azure Active Directory (AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

若要登入適用於 MySQL 的 Azure 資料庫，建議使用 Azure AD 並透過 Azure AD 權杖連線。 使用 Azure AD 權杖時，則可支援不同的方法，例如使用 Azure AD 使用者、Azure AD 群組，或連線至資料庫的 Azure AD 應用程式。

Azure AD 認證也可以用於管理平面層級 (例如，Azure 入口網站) 的管理作業，以控制 MySQL 管理帳戶。

- [使用 Azure Active Directory 來向適用於 MySQL 的 Azure 資料庫進行驗證](howto-configure-sign-in-azure-ad-authentication.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：請檢閱 Azure Active Directory 記錄，以協助探索過時的帳戶，其中可能u.有些帳戶具有適用於 MySQL 的 Azure 資料庫系統管理角色。 此外，使用 Azure 身分識別存取權檢閱，可有效管理群組成員資格、可能會用於存取適用於 MySQL 的 Azure 資料庫的企業應用程式存取權，以及角色指派。 請定期檢閱使用者的存取權 (例如每 90 天)，以確定只有適當的使用者具有持續存取權。

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：為適用於 MySQL 的 Azure 資料庫和 Azure Active Directory 啟用診斷設定，將所有記錄傳送到 Log Analytics 工作區。 請在 Log Analytics 中設定所需的警示 (例如失敗的驗證嘗試)。

- [如何設定及存取適用於 MySQL 的 Azure 資料庫的慢速查詢記錄](./howto-configure-server-logs-in-portal.md)

- [如何設定和存取適用於 MySQL 的 Azure 資料庫的 audit 記錄](./howto-configure-audit-logs-portal.md)

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指引**：為適用於 MySQL 的 Azure 資料庫啟用 Azure 進階威脅防護，針對可疑活動產生警示。

使用 Azure Active Directory Identity Protection 和風險偵測功能，設定對所偵測到可疑動作的自動化回應。 您可透過 Azure Sentinel 啟用自動化回應，以實作組織的安全性回應。

您也可將記錄內嵌到 Azure Sentinel 中，以便進一步調查。

- [如何設定適用於 MySQL 的 Azure 資料庫的 Advanced 威脅防護](howto-database-threat-protection-portal.md)

- [Azure AD Identity Protection 概觀](../active-directory/identity-protection/overview-identity-protection.md)

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指引**：不適用；適用於 MySQL 的 Azure 資料庫尚不支援客戶加密箱。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的適用於 MySQL 的 Azure 資料庫執行個體或相關資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 請搭配使用 Private Link、服務端點，及/或防火牆規則，隔離並限制對適用於 MySQL 的 Azure 資料庫執行個體的網路存取。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何設定適用於 MySQL 的 Azure 資料庫的 Private Link](concepts-data-access-security-private-link.md)

- [如何在適用於 MySQL 的 Azure 資料庫中建立和管理 VNet 服務端點和 VNet 規則](../azure-sql/database/vnet-service-endpoint-rule-overview.md)

- [如何設定適用於 MySQL 的 Azure 資料庫防火牆規則](concepts-firewall-rules.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指引**：使用 Azure VM 存取適用於 MySQL 的 Azure 資料庫執行個體時，請使用 Private Link、MySQL 網路設定、網路安全性群組及服務標籤，降低資料外洩的可能性。

Microsoft 會管理適用於 MySQL 的 Azure 資料庫的基礎結構，並已實作嚴格的控制項，以避免遺失或公開客戶資料。

- [如何緩和適用於 MySQL 的 Azure 資料庫的資料遭到外泄](concepts-data-access-security-private-link.md#data-exfiltration-prevention)

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指引**：適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將 MySQL 伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 在 Azure 入口網站中，請確保在預設情況下，所有適用於 MySQL 的 Azure 資料庫執行個體均啟用「強制執行安全連線」。

適用於 MySQL 的 Azure 資料庫目前支援的 TLS 版本為 TLS 1.0、TLS 1.1、TLS 1.2。

- [如何在傳輸中為適用於 MySQL 的 Azure 資料庫設定加密](concepts-ssl-connection-security.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指引**：適用於 MySQL 的 Azure 資料庫目前無法使用資料識別、分類及外洩防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：無法使用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 來控制適用於 MySQL 的 Azure 資料庫控制平面的存取 (例如 Azure 入口網站) 。 針對資料平面存取 (在該資料庫本身內)，請使用 SQL 查詢來建立使用者和設定使用者權限。 Azure RBAC 不會影響資料庫中的使用者許可權。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [如何使用 SQL 為適用於 MySQL 的 Azure 資料庫設定使用者存取](howto-create-users.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。

Microsoft 會管理適用於 MySQL 的 Azure 資料庫的基礎結構，並已實作嚴格的控制項，以避免遺失或公開客戶資料。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指引**：適用於 MySQL 的 Azure 資料庫服務針對待用資料儲存體加密是使用符合 FIPS 140-2 的加密模組。 資料 (包含備份) 會在磁碟上加密，但不包括執行查詢時所建立的暫存檔案。 該服務使用包含在 Azure 儲存體加密中的 AES 256 位元加密，且金鑰是由系統進行管理。 儲存體加密會一律啟用，且無法停用。

適用於 MySQL 的 Azure 資料庫使用客戶管理金鑰的資料加密，可讓您攜帶自己的金鑰 (BYOK) 來保護待用資料。 此時，您必須要求存取權，才能使用這項功能。 若要這樣做，請連絡：

AskAzureDBforMySQL@service.microsoft.com

- [瞭解適用於 MySQL 的 Azure 資料庫的待用加密](concepts-security.md)

- [如何為適用於 MySQL 的 Azure 資料庫設定客戶管理的金鑰](concepts-data-encryption-mysql.md)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指引**：使用 Azure 監視器搭配 Azure 活動記錄，以在適用於 MySQL 的 Azure 資料庫的生產執行個體及其他重要或相關資源有所變更時建立警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：遵循 Azure 資訊安全中心保護適用於 MySQL 的 Azure 資料庫和相關資源的建議。

Microsoft 會在支援適用於 MySQL 的 Azure 資料庫的基礎系統上執行弱點管理。

- [瞭解 Azure 資訊安全中心建議](../security-center/recommendations-reference.md)

- [Azure 資訊安全中心中的 Azure PaaS 服務的功能涵蓋範圍](../security-center/features-paas.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指引**：Microsoft 會在支援適用於 MySQL 的 Azure 資料庫的基礎系統上，執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢及探索所有資源， (包括訂用帳戶內) 的適用於 MySQL 的 Azure 資料庫實例。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至適用於 MySQL 的 Azure 資料庫執行個體和其他相關資源，讓中繼資料可以透過邏輯方式將其組織成分類法。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：在適當情況下，使用建立標籤、管理群組和個別訂閱來組織及追蹤適用於 MySQL 的 Azure 資料庫執行個體及其相關資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指引**：不適用，這項建議主要用於計算資源和整體 Azure。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型

- 允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢及探索訂用帳戶內的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源和整體 Azure。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型

- 允許的資源類型

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。 這可以避免在高安全性環境中 (例如包含敏感資訊的適用於 MySQL 的 Azure 資料庫執行個體) 建立和變更資源。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：使用 Azure 原則，為您的適用於 MySQL 的 Azure 資料庫執行個體定義並實作標準安全性設定。 使用 **microsoft.dbformysql** 命名空間中 Azure 原則別名來建立自訂原則，以對適用於 MySQL 的 Azure 資料庫實例的網路設定進行審核或強制執行。 您也可以使用與適用於 MySQL 的 Azure 資料庫實例相關的內建原則定義，例如：

應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]

- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指引**：如果針對適用於 MySQL 的 Azure 資料庫執行個體及相關資源，使用自訂的 Azure 原則，請使用 Azure Repos 以安全地儲存和管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指引**：使用「Microsoft.DBforMySQL」命名空間中的 Azure 原則別名來建立自訂原則，以警示、稽核和強制執行系統設定。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用 **microsoft.dbformysql** 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統設定。 使用 Azure 原則 [稽核]、[拒絕] 和 [在不存在時部署]，針對您的適用於 MySQL 的 Azure 資料庫及相關資源，自動強制執行設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指引**：對於在 Azure App Service 上執行的 Azure 虛擬機器或 Web 應用程式 (用於存取您的適用於 MySQL 的 Azure 資料庫執行個體)，請將受控服務識別與 Azure Key Vault 結合使用，以簡化和保護適用於 MySQL 的 Azure 資料庫密碼管理。 確保已啟用 Key Vault 虛刪除。

- [如何與 Azure 受控識別整合](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何建立 Key Vault](../key-vault/general/quick-create-portal.md)

- [如何使用受控識別來提供 Key Vault authentication](../key-vault/general/assign-access-policy-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指引**：適用於 MySQL 的 Azure 資料庫執行個體支援 Azure Active Directory 驗證以存取資料庫。  建立適用於 MySQL 的 Azure 資料庫執行個體時，需要提供管理員使用者的認證。 此系統管理員可用於建立其他資料庫使用者。  

對於在 Azure App Service 上執行的 Azure 虛擬機器或 Web 應用程式 (用於存取您的適用於 MySQL 的 Azure 資料庫執行個體)，請將受控服務識別與 Azure Key Vault 結合使用，以儲存和擷取適用於 MySQL 的 Azure 資料庫執行個體的認證。 確保已啟用 Key Vault 虛刪除。

使用受控識別，在 Azure Active Directory (AD) 中為 Azure 服務提供自動受控識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

- [如何設定受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [如何與 Azure 受控識別整合](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機 (例如，適用於 SQL 的 Azure 資料庫) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：支援 Azure 服務的基礎主機 (例如，適用於 MySQL 的 Azure 資料庫) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

預先掃描要上傳至非計算 Azure 資源的任何內容，例如 App Service、Data Lake Storage、Blob 儲存體、適用於 MySQL 的 Azure 資料庫等等。Microsoft 無法存取這些執行個體中的資料。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機 (例如，適用於 MySQL 的 Azure 資料庫) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：N/A

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指引**：適用於 MySQL 的 Azure 資料庫會取得資料檔案和交易記錄的備份。 根據支援的儲存體大小上限，我們採用完整和差異備份 (4 TB 儲存體伺服器上限) 或快照集備份 (最多 16 TB 儲存體伺服器上限)。 在您設定的備份保留期限內，這些備份可讓您將伺服器還原至任何時間點。 預設的備份保留期限是七天。 可選擇設定的期限最多為 35 天。 所有備份皆會使用 AES 256 位元加密進行加密。

- [瞭解適用於 MySQL 的 Azure 資料庫的備份](concepts-backup.md)

- [瞭解適用於 MySQL 的 Azure 資料庫初始設定](tutorial-design-database-using-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指引**：適用於 MySQL 的 Azure 資料庫會自動建立伺服器備份，並根據使用者的選擇，將其儲存在本地備援或異地備援儲存體中。 備份可以用來將伺服器還原至某個時間點。 備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。 

如果要使用 Azure Key Vault 來儲存適用於 MySQL 的 Azure 資料庫執行個體的認證，請確保定期自動備份您的金鑰。 

- [瞭解適用於 MySQL 的 Azure 資料庫的備份](howto-restore-server-portal.md) 

- [如何備份 Key Vault 金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：在適用於 MySQL 的 Azure 資料庫中，還原執行作業會從原始伺服器的備份中建立新的伺服器。 有兩種類型的還原可使用：時間點還原和異地還原。 時間點還原可搭配任一備份備援選項使用，並在原始伺服器所在區域中建立新的伺服器。 異地還原只能在將伺服器設定為使用異地備援儲存體時使用，這可供將伺服器還原至不同的區域。

預估的復原時間取決於數個因素，包括資料庫大小、交易記錄大小、網路頻寬，以及在相同區域中同時進行復原的資料庫總數。 復原時間通常不到 12 小時。

定期測試適用於 MySQL 的 Azure 資料庫執行個體的還原功能。

- [瞭解適用於 MySQL 的 Azure 資料庫中的備份與還原](concepts-backup.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指引**：適用於 MySQL 的 Azure 資料庫採用完整、差異及記錄備份。 在您設定的備份保留期限內，這些備份可讓您將伺服器還原至任何時間點。 預設的備份保留期限是七天。 可選擇設定的期限最多為 35 天。 所有備份皆會使用 AES 256 位元加密進行加密。 確保已啟用 Key Vault 虛刪除。

- [瞭解適用於 MySQL 的 Azure 資料庫中的備份與還原](concepts-backup.md)

**Azure 資訊安全中心監視**：是

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

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

- [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

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

**指引**：遵循 Microsoft 參與規則，以確保滲透測試不會違反 Microsoft 原則： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [您可以在這裡找到有關 Microsoft 所管理的雲端基礎結構、服務和應用程式的 Microsoft 策略和執行紅色小組和即時網站滲透測試的詳細資訊](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)