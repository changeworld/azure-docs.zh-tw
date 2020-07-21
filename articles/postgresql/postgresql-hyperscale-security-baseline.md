---
title: 適用于 Azure 安全性基準測試的適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫安全性基準
description: 適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫的安全性基準提供程式指引和資源，可用於執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ca08ac30c5d8e925197a0df1d327bf9a0923b046
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531224"
---
# <a name="azure-database-for-postgresql---hyperscale-security-baseline-for-azure-security-benchmark"></a>適用于 Azure 安全性基準測試的適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫安全性基準

適用于適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路中的 Azure 資源

**指導**方針：適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆會防止對您的超大規模資料庫（Citus）協調員節點的所有存取，直到您指定哪些電腦擁有許可權為止。 此防火牆會根據每一個要求的來源 IP 位址來授與伺服器存取權。 若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

* [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）中設定防火牆規則](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）無法與虛擬網路、子網或網路安全性群組建立關聯。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）所使用的端點都是由 Microsoft 管理。 您必須負責任何想要部署到內部部署系統的其他控制項。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）無法與虛擬網路、子網或網路安全性群組建立關聯。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統（IDS/IPS）

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）所使用的端點都是由 Microsoft 管理。 您必須負責任何想要部署到內部部署系統的其他控制項。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）無法與虛擬網路、子網或網路安全性群組建立關聯。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指引**：使用 Azure 原則，以針對與適用於 PostgreSQL 的 Azure 資料庫執行個體建立關聯其網路設定和網路資源來定義及實作標準安全性設定。 使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行適用於 PostgreSQL 的 Azure 資料庫實例的網路設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [網路的 Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）無法與虛擬網路、子網或網路安全性群組建立關聯。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）無法與虛擬網路、子網或網路安全性群組建立關聯。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**.. 針對控制平面的審核記錄，啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

此外，透過 Azure 監視器內嵌記錄，以匯總超大規模資料庫（Citus）所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用儲存體帳戶進行長期/封存儲存體。 或者，您可以啟用 Azure Sentinel 或第三方安全性事件和事件管理 (SIEM)，然後使資料上線。

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [超大規模資料庫（Citus）中的計量](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：超大規模資料庫（Citus）會針對伺服器群組中的每個節點提供計量。 計量讓您深入瞭解支援資源的行為。 每個計量都會以一分鐘的頻率發出，而且可保留最多 30 天的歷程記錄。

針對 [控制平面] [審核記錄]，啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

此外，透過 Azure 監視器內嵌記錄，以匯總超大規模資料庫（Citus）所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用儲存體帳戶進行長期/封存儲存體。 或者，您可以啟用 Azure Sentinel 或第三方安全性事件和事件管理 (SIEM)，然後使資料上線。

* [超大規模資料庫（Citus）中的計量](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，針對用來保存超大規模資料庫（Citus）記錄的 Log Analytics 工作區，請根據貴組織的合規性法規來設定保留週期。 使用 Azure 儲存體帳戶進行長期/封存儲存。

* [如何設定 Log Analytics 工作區的記錄保留期參數](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [將資源記錄儲存在 Azure 儲存體帳戶中](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**：分析和監視來自超大規模資料庫（Citus）實例的記錄是否有異常行為。 使用 Azure 監視器的 Log Analytics 檢閱記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如需 Log Analytics 的詳細資訊](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：您可以啟用超大規模資料庫（Citus）的診斷設定，並將記錄傳送到 Log Analytics 工作區。 您可以根據 Azure 服務的監視計量來設定和接收警示。 使用 Azure 監視器的 Log Analytics 檢閱記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

請將您的 Log Analytics 工作區在 Azure Sentinel 上線，因為其可提供安全性協調流程自動化回應 (SOAR) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。

* [超大規模資料庫（Citus）中的計量](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-alert-on-metric)

* [如何設定 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）不會處理或產生與反惡意程式碼相關的記錄檔。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;適用於 PostgreSQL 的 Azure 資料庫不會處理或產生 DNS 相關的記錄檔。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指引**：維護具有超大規模資料庫（Citus）實例控制平面（例如 Azure 入口網站）之系統管理存取權的使用者帳戶清查。 此外，維護可存取超大規模資料庫（Citus）實例之資料平面（在資料庫本身內）的系統管理帳戶清查。

超大規模資料庫（Citus）不支援內建的角色型存取控制，但您可以根據特定的資源提供者作業來建立自訂角色。

此外，于 postgresql 引擎會使用角色來控制資料庫物件的存取權，而新建立的超大規模資料庫（Citus）伺服器群組則隨附數個預先定義的角色。 若要修改使用者權限，請使用 PgAdmin 或 psql 這類工具的標準于 postgresql 命令。

* [瞭解 Azure 訂用帳戶的自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

* [瞭解適用於 PostgreSQL 的 Azure 資料庫資源提供者作業](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql)

* [瞭解適用於 PostgreSQL 的 Azure 資料庫的存取管理](https://docs.microsoft.com/azure/postgresql/concepts-security#access-management])

* [如何在適用於 PostgreSQL 的 Azure 資料庫中建立使用者超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

* [如何使用 psql 連接到于 postgresql-超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指引**：Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制以複雜性需求和最小密碼長度來建立密碼，這會因服務而有所不同。 您必須負責可使用預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：使用專用的系統管理帳戶（用來存取您的超大規模資料庫（Citus）實例）來建立標準操作程式。 用來管理 Azure 資源的管理帳戶會系結至 Azure Active Directory，超大規模資料庫（Citus）伺服器群組中也有本機伺服器系統管理員帳戶，可用來管理資料庫存取權限。 使用 Azure 資訊安全中心身分識別和存取管理來監視 Azure Active Directory 內的系統管理帳戶數目。

* [瞭解 Azure 資訊安全中心身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何在適用於 PostgreSQL 的 Azure 資料庫中建立使用者超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指引**.. 登入適用於 PostgreSQL 的 Azure 資料庫-超大規模資料庫（Citus）支援同時使用資料庫中直接設定的使用者名稱/密碼，以及使用 AZURE ACTIVE DIRECTORY （AD）身分識別並利用 Azure AD token 來連接。 使用 Azure AD 權杖時，則支援不同的方法，例如使用 Azure AD 使用者、Azure AD 群組，或連線至資料庫的 Azure AD 應用程式。

* [使用 Azure Active Directory 向適用於 PostgreSQL 的 Azure 資料庫進行驗證](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory Multi-Factor Authentication (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。 利用 Azure AD 權杖登入資料庫時，即可要求使用多重要素驗證登入資料庫。

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [使用 Azure Active Directory 向適用於 PostgreSQL 的 Azure 資料庫進行驗證](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

* [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指引**：使用已設定 Multi-Factor Authentication (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

* [瞭解特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7：帳戶登入行為偏差的警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

使用 Azure AD 風險偵測來檢視風險性使用者行為的相關警示和報告。

* [如何部署 Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [了解 Azure AD 風險偵測](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，僅允許從 IP 位址範圍或國家/區域的特定邏輯群組存取入口網站和 Azure Resource Manager。

* [如何在 Azure 中設定具名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指引**：使用 Azure Active Directory (AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

Azure AD 認證也可用於管理平面層級 (例如 Azure 入口網站) 的管理作業，以控制 PostgreSQL 管理帳戶。

* [使用 Azure Active Directory 向適用於 PostgreSQL 的 Azure 資料庫進行驗證](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針：針對可存取本機資料庫的使用者以及透過 Azure Active Directory，審查並協調存取。

請參閱 Azure Active Directory （AD）記錄檔，以協助探索過時的帳戶，其中可能包含超大規模資料庫（Citus）系統管理角色。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、存取可用於存取超大規模資料庫（Citus）的企業應用程式，以及角色指派。 請定期檢閱使用者的存取權 (例如每 90 天)，以確定只有適當的使用者具有持續存取權。

* [審查于 postgresql 使用者和指派的角色](https://www.postgresql.org/docs/current/database-roles.html)

* [瞭解 Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：在 AZURE ACTIVE DIRECTORY （AD）中，您可以存取 Azure AD 登入活動、Audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。

若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。

* [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory 的身分識別保護和風險偵測功能，針對在 AZURE ACTIVE DIRECTORY （AD）層級偵測到的可疑動作設定自動回應。 您可透過 Azure Sentinel 啟用自動化回應，以實作組織的安全性回應。

您也可將記錄內嵌到 Azure Sentinel 中，以便進一步調查。

* [Azure AD Identity Protection 總覽](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [如何檢視有風險的 Azure AD 登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：目前無法使用;超大規模資料庫（Citus）尚不支援客戶加密箱。

* [客戶加密箱支援的服務清單](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標記來協助追蹤超大規模資料庫（Citus）實例或儲存或處理敏感資訊的相關資源。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 使用系統管理角色和防火牆規則的組合，來隔離和限制適用於 PostgreSQL 的 Azure 資料庫實例的網路存取。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [瞭解適用於 PostgreSQL 的 Azure 資料庫中的防火牆規則超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

* [瞭解超大規模資料庫中的角色（Citus）](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針： Microsoft 會管理適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：用戶端應用程式與超大規模資料庫（Citus）協調器節點的連線需要傳輸層安全性（TLS）1.2。 在您的資料庫伺服器和用戶端應用程式之間強制執行 TLS 連線，可將伺服器與應用程式之間的資料流程加密，以協助防止「攔截式」攻擊。

針對透過 Azure 入口網站布建的所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用強制執行 TLS 連線。

在某些情況下，協力廠商應用程式需要從信任的憑證授權單位單位（CA）憑證檔案（.cer）產生的本機憑證檔案，才能安全地連接。

* [如何在適用於 PostgreSQL 的 Azure 資料庫中設定 TLS 超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

* [需要憑證驗證以進行 TLS 連線的應用程式](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指引**：超大規模資料庫（Citus）尚未提供資料識別、分類及遺失防護功能。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用以角色為基礎的存取控制來控制對資源的存取

**指引**：使用 Azure 角色型存取控制（RBAC）來控制對超大規模資料庫（Citus）控制平面的存取（例如 Azure 入口網站）。 RBAC 不會影響資料庫中的使用者權限。

若要在資料庫層級修改使用者權限，請使用 PgAdmin 或 psql 之類的工具來使用標準于 postgresql 命令。

* [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [如何使用適用於 PostgreSQL 的 Azure 資料庫的 SQL 設定使用者存取權](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。

Microsoft 會管理超大規模資料庫（Citus）的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：至少一天一次，適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）會取得資料檔案和資料庫交易記錄的快照集備份。 這些備份可讓您將伺服器還原到保留期限內的任何時間點。 （所有叢集的保留期限目前為35天）。所有備份都會使用 AES 256 位加密進行加密。 于 postgresql 超大規模資料庫（Citus）產品使用 Microsoft 管理的金鑰進行加密。

* [瞭解 Azure 于 postgresql-超大規模資料庫（Citus）備份的加密](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對超大規模資料庫（Citus）的生產實例和其他重要或相關資源進行變更時，建立警示。

* [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：目前無法使用;Azure 資訊安全中心尚未支援適用於 PostgreSQL 的 Azure 資料庫-超大規模資料庫（Citus）的弱點評估。

* [Azure 資訊安全中心中 Azure PaaS 服務的功能涵蓋範圍](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：對於由 microsoft 管理的基礎平臺，microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體標題部署自動修補程式管理解決方案

**指引**：對於由 microsoft 管理的基礎平臺，microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：對於由 microsoft 管理的基礎平臺，microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針： Microsoft 會在支援適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）的基礎系統上執行弱點管理。 如需有關設定資料庫叢集和相關資源的弱點，請使用常見的風險評分計畫（例如，常見的弱點評分系統）或協力廠商掃描工具所提供的預設風險評等。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：使用 Azure Resource Graph 來查詢和探索訂用帳戶內的所有資源（包括超大規模資料庫（Citus）實例）。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至超大規模資料庫（Citus）實例和其他相關資源，以邏輯方式將它們組織成分類法。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**.. 適當地使用標記、管理群組和個別的訂用帳戶來組織和追蹤超大規模資料庫（Citus）實例和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義並維護已核准 Azure 資源的清查

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

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

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。 這可能會防止在高安全性環境（例如超大規模資料庫（Citus）中包含機密資訊的實例）中建立和變更資源。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

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

*如需詳細資訊，請參閱[安全性控制：安全設定](/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則，為您的超大規模資料庫（Citus）實例定義及執行標準安全性設定。 使用 Azure 原則來建立自訂原則，以審核或強制執行適用於 PostgreSQL 的 Azure 資料庫實例的網路設定。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法（JSON）中匯出範本，這應該進行檢查以確保設定符合/超過組織的安全性需求。

* [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [單一和多重資源匯出至 Azure 入口網站中的範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 azure 原則 [拒絕] 和 [不存在時部署]，在您的 Azure 資源上強制執行安全設定。 此外，您可以使用 Azure Resource Manager 範本來維護貴組織所需之 Azure 資源的安全性設定。

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [建立和管理原則以強制執行合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Resource Manager 範本總覽](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果您的超大規模資料庫（Citus）實例和相關資源使用自訂的 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

* [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指引**：使用 azure 原則 [拒絕] 和 [不存在時部署]，在您的 Azure 資源上強制執行安全設定。 此外，您可以使用 Azure Resource Manager 範本來維護貴組織所需之 Azure 資源的安全性設定。

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [建立和管理原則以強制執行合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Resource Manager 範本總覽](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指引**：使用 "Microsoft.DBforPostgreSQL" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、稽核和強制執行系統設定。 使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在] 自動強制執行適用於 PostgreSQL 的 Azure 資料庫實例和相關資源的設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：不適用;您可以使用 Azure Key Vault 安全地管理您的資料庫管理員密碼。 為該 Key Vault 秘密指派適當範圍的許可權，以限制對您資料庫密碼的存取。 避免在您的程式碼存放庫中儲存任何連接字串或資料庫秘密。

* [如何連接到您的于 postgresql 資料庫](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

* [如何建立 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [如何使用受控識別提供 Key Vault 驗證](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指引**：適用於 PostgreSQL 的 Azure 資料庫伺服器支援 Azure Active Directory 驗證以存取資料庫。 建立適用於 PostgreSQL 的 Azure 資料庫伺服器時，需要提供系統管理員使用者的認證。 此系統管理員可用於建立其他資料庫使用者。

針對在 Azure App Service 上執行的 Azure 虛擬機器或 Web 應用程式 (用於存取適用於 PostgreSQL 的 Azure 資料庫伺服器)，請將受控服務識別與 Azure Key Vault 結合使用，以儲存和擷取適用於 PostgreSQL 的 Azure 資料庫伺服器的認證。 確保已啟用 Key Vault 虛刪除。

使用受控識別，在 Azure Active Directory (AD) 中為 Azure 服務提供自動受控識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

* [如何設定受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

* [如何與 Azure 受控識別整合](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機 (例如 Azure App Service) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：在支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼，例如超大規模資料庫（Citus）--不過，它不會對客戶內容執行。

預先掃描要上傳至非計算 Azure 資源的任何內容，例如 App Service、Data Lake Storage、Blob 儲存體、適用於 PostgreSQL 的 Azure 資料庫等。Microsoft 無法存取這些執行個體中的資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼，例如超大規模資料庫（Citus）--不過，它不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料復原](/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）會自動建立每個節點的備份，並將它們儲存在本機的多餘儲存體中。 備份可以用來將您的超大規模資料庫（Citus）叢集還原到指定的時間。

* [如何在適用於 PostgreSQL 的 Azure 資料庫中備份和還原超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針：至少一天一次，適用於 PostgreSQL 的 Azure 資料庫會取得資料檔案和資料庫交易記錄的快照集備份。 這些備份可讓您將伺服器還原到保留期限內的任何時間點。 所有叢集的保留期限目前為35天。 所有備份皆會使用 AES 256 位元加密進行加密。

在支援可用性區域的 Azure 區域中，備份快照集會儲存在三個可用性區域中。 只要至少有一個可用性區域上線，超大規模資料庫（Citus）叢集便可還原。

* [如何在適用於 PostgreSQL 的 Azure 資料庫中備份和還原超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：在適用於 PostgreSQL 的 Azure 資料庫中，還原超大規模資料庫（Citus）叢集會從原始節點的備份建立新的叢集。 您可以將叢集還原到過去35天內的任何時間點。 還原程式會在與原始相同的 Azure 區域、訂用帳戶和資源群組中建立新的叢集。 新的叢集設定與原始叢集設定相同：相同數目的節點、虛擬核心數目、儲存體大小和使用者角色。

系統不會從原始伺服器群組保留防火牆設定和于 postgresql 伺服器參數;系統會將它們重設為預設值。 防火牆會防止所有連線。 您將需要在還原之後手動調整這些設定。

* [如何在適用於 PostgreSQL 的 Azure 資料庫中備份和還原超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指引**：無法還原已刪除的超大規模資料庫（Citus）叢集。 如果您刪除叢集，屬於該叢集的所有節點都會被刪除，而且無法復原。 若要保護部署後不會遭到意外刪除或非預期變更的叢集資源，系統管理員可以利用管理鎖定。

* [如何在適用於 PostgreSQL 的 Azure 資料庫中備份和還原超大規模資料庫（Citus）](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [如何設定 Azure 資訊安全中心內的工作流程自動化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [客戶也可以利用 NIST 的「電腦安全性性」事件處理指南，協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

* [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心的安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

* [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

* [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期滲透測試您的 Azure 資源，並確保修復所有重要的安全性結果

**指引**： * [遵循 Microsoft 參與規則，確保您的滲透測試不會違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
