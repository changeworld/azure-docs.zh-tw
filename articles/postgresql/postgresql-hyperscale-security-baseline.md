---
title: '適用于適用於 PostgreSQL 的 Azure 資料庫的 Azure 安全性基準-超大規模 (Citus) '
description: 適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 324a2acbf069e8e23246b99a7d79a3195f737a56
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492296"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale-citus"></a>適用于適用於 PostgreSQL 的 Azure 資料庫的 Azure 安全性基準-超大規模 (Citus) 

適用于適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus 的 Azure 安全性基準) 包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆會防止所有超大規模 (Citus) 協調器節點的存取，直到您指定哪些電腦擁有許可權為止。 此防火牆會根據每一個要求的來源 IP 位址來授與伺服器存取權。 若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

- [如何在適用於 PostgreSQL 的 Azure 資料庫中設定防火牆規則-超大規模 (Citus) ](./concepts-hyperscale-firewall-rules.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指引**：使用 Azure 原則，以針對與適用於 PostgreSQL 的 Azure 資料庫執行個體建立關聯其網路設定和網路資源來定義及實作標準安全性設定。 使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對適用於 PostgreSQL 的 Azure 資料庫實例的網路設定進行審核或強制執行。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [適用于網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network)

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：若要進行控制平面審核記錄，請啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

此外，透過 Azure 監視器內嵌記錄，以匯總超大規模 (Citus) 所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區 (s) 來查詢和執行分析，並使用儲存體帳戶來儲存長期/封存儲存體。 或者，您可以啟用 Azure Sentinel 或第三方安全性事件和事件管理 (SIEM)，然後使資料上線。 

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [超大規模中的計量 (Citus) ](./concepts-hyperscale-monitoring.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：超大規模 (Citus) 為伺服器群組中的每個節點提供度量。 這些計量讓您深入瞭解支援資源的行為。 每個計量都會以一分鐘的頻率發出，而且可保留最多 30 天的歷程記錄。

針對控制平面審核記錄，請啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

此外，透過 Azure 監視器內嵌記錄，以匯總超大規模 (Citus) 所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區 (s) 來查詢和執行分析，並使用儲存體帳戶來儲存長期/封存儲存體。 或者，您可以啟用 Azure Sentinel 或第三方安全性事件和事件管理 (SIEM)，然後使資料上線。 

- [超大規模中的計量 (Citus) ](./concepts-hyperscale-monitoring.md)

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，針對用來保存您的超大規模 (Citus) 記錄的 Log Analytics 工作區，根據您組織的合規性法規設定保留期限。 使用 Azure 儲存體帳戶進行長期/封存儲存。

- [如何設定 Log Analytics 工作區的記錄保留期參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [在 Azure 儲存體帳戶中儲存資源記錄](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** 方針：分析和監視來自超大規模 (Citus) 實例的記錄，以取得異常行為。 使用 Azure 監視器的 Log Analytics 檢閱記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [有關 Log Analytics 的詳細資訊](../azure-monitor/log-query/log-analytics-tutorial.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：您可以啟用超大規模 (Citus) 的診斷設定，並將記錄傳送至 Log Analytics 工作區。 您可以根據 Azure 服務的監視計量來設定和接收警示。 使用 Azure 監視器的 Log Analytics 檢閱記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

請將您的 Log Analytics 工作區在 Azure Sentinel 上線，因為其可提供安全性協調流程自動化回應 (SOAR) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。

- [超大規模中的計量 (Citus) ](./howto-hyperscale-alert-on-metric.md)

- [如何設定 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導** 方針：維護具有控制平面之系統管理存取權限的使用者帳戶清查 (例如 Azure 入口網站) 的超大規模 (Citus) 實例。 此外，維護可存取資料平面的系統管理帳戶的清查 (在超大規模 (Citus) 實例的資料庫本身) 。

超大規模 (Citus) 不支援內建的角色型存取控制，但您可以根據特定的資源提供者作業來建立自訂角色。

此外，于 postgresql 引擎會使用角色來控制對資料庫物件的存取，而新建立的超大規模 (Citus) 伺服器群組則有數個預先定義的角色。 若要修改使用者權限，請使用 PgAdmin 或 psql 等工具，以使用標準的于 postgresql 命令。

- [瞭解 Azure 訂用帳戶的自訂角色](../role-based-access-control/custom-roles.md) 

- [瞭解適用於 PostgreSQL 的 Azure 資料庫資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [瞭解適用於 PostgreSQL 的 Azure 資料庫的存取管理](./concepts-security.md#access-management)

- [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中建立使用者 ](./howto-hyperscale-create-users.md)

- [如何使用 psql 連接到于 postgresql-超大規模 (Citus) ](./quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指引**：Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制建立具有複雜性需求和密碼長度下限的密碼，這會因服務而有所不同。 您必須負責可能使用預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：使用專門用來存取超大規模 (Citus) 實例的專用系統管理帳戶來建立標準作業程式。 管理 Azure 資源的系統管理員帳戶會系結至 Azure Active Directory，也有本機伺服器系統管理員帳戶存在於超大規模 (Citus) 伺服器群組中，以管理資料庫存取權限。 使用 Azure 資訊安全中心身分識別和存取管理來監視 Azure Active Directory 內的系統管理帳戶數目。

- [瞭解 Azure 資訊安全中心身分識別和存取權](../security-center/security-center-identity-access.md) 

- [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中建立使用者 ](./howto-hyperscale-create-users.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：若要存取 Azure 入口網站，請 Azure Active Directory MULTI-FACTOR AUTHENTICATION (MFA) ，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指引**：使用已設定 Multi-Factor Authentication (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7：帳戶登入行為偏差的警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

使用 Azure AD 風險偵測來檢視風險性使用者行為的相關警示和報告。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 風險偵測](../active-directory/identity-protection/overview-identity-protection.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，僅允許從 IP 位址範圍或國家/區域的特定邏輯群組存取入口網站和 Azure Resource Manager。

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為中央驗證和授權系統，以管理于 postgresql 資源。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

超大規模 (Citus) 伺服器群組內的使用者不能直接系結到 Azure Active Directory 帳戶。 若要修改資料庫物件存取權的使用者權限，請使用標準于 postgresql 命令搭配 PgAdmin 或 psql 等工具。

- [修改使用者角色的許可權](./howto-hyperscale-create-users.md#how-to-modify-privileges-for-user-role)

- [如何建立和設定 AAD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針：檢查和協調可存取本機資料庫的兩個使用者，以及透過 Azure Active Directory 管理于 postgresql 資源的存取權。

對於具有管理資料庫 Azure 資源存取權的使用者，請參閱 Azure Active Directory (AD) 記錄檔，以協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、存取可用於存取超大規模 (Citus) 和角色指派的企業應用程式。 應定期檢查使用者存取，例如每隔90天，以確定只有適當的使用者可以繼續存取。

- [審核于 postgresql 使用者和指派的角色](https://www.postgresql.org/docs/current/database-roles.html)

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：在 AZURE ACTIVE DIRECTORY (AD) 中，您可以存取 Azure AD 登入活動、Audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。 

若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。 

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導** 方針：使用 Azure Active Directory 的身分識別保護和風險偵測功能，設定在 AZURE ACTIVE DIRECTORY (AD) 層級偵測到的可疑動作的自動回應。 您可透過 Azure Sentinel 啟用自動化回應，以實作組織的安全性回應。

您也可將記錄內嵌到 Azure Sentinel 中，以便進一步調查。

- [Azure AD Identity Protection 概觀](../active-directory/identity-protection/overview-identity-protection.md)

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：目前無法使用;超大規模 (Citus) 尚不支援客戶加密箱。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指導** 方針：使用標記協助追蹤超大規模 (Citus) 實例或儲存或處理敏感性資訊的相關資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 使用系統管理角色和防火牆規則的組合來隔離及限制適用於 PostgreSQL 的 Azure 資料庫實例的網路存取。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [瞭解適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus 中的防火牆規則) ](./concepts-hyperscale-firewall-rules.md)

- [瞭解超大規模中的角色 (Citus) ](./howto-hyperscale-create-users.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：超大規模 (Citus) 協調器節點的用戶端應用程式連接需要傳輸層安全性 (TLS) 1.2。 在您的資料庫伺服器與用戶端應用程式之間強制執行 TLS 連線，可透過加密伺服器和應用程式之間的資料流程，來防止「攔截式」攻擊。

針對透過 Azure 入口網站布建的所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用 TLS 連接的強制。

在某些情況下，協力廠商應用程式需要由信任的憑證授權單位單位產生的本機憑證檔案 (CA) 憑證檔案 ( .cer) 以安全地連接。

- [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中設定 TLS ](./concepts-hyperscale-ssl-connection-security.md)

- [需要憑證驗證以進行 TLS 連接的應用程式](./concepts-hyperscale-ssl-connection-security.md)



**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 來控制超大規模 (Citus) 控制平面的存取權 (例如 Azure 入口網站) 。 Azure RBAC 不會影響資料庫中的使用者許可權。

若要修改資料庫層級的使用者權限，請使用 PgAdmin 或 psql 等工具，以使用標準的于 postgresql 命令。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [如何使用 SQL 為適用於 PostgreSQL 的 Azure 資料庫設定使用者存取](./howto-hyperscale-create-users.md)


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：  
至少一天一次，適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 取得資料檔案和資料庫交易記錄的快照集備份。 這些備份可讓您將伺服器還原至保留期限內的任何時間點。  (所有叢集的保留期限目前為35天。 ) 所有備份都使用 AES 256 位加密進行加密。 于 postgresql 超大規模 (Citus) 產品使用 Microsoft 管理的金鑰進行加密。

- [瞭解 Azure 于 postgresql 的加密-超大規模 (Citus) 備份](./concepts-hyperscale-backup.md)



**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄來建立超大規模的生產實例發生變更時的警示 (Citus) 以及其他重要或相關資源。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：目前無法使用;Azure 資訊安全中心尚不支援適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 的弱點評定。

- [Azure 資訊安全中心中的 Azure PaaS 服務的功能涵蓋範圍](../security-center/features-paas.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢及探索所有資源 (包括)  (訂用帳戶中的超大規模 (Citus) 實例。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：將標記套用至超大規模 (Citus) 實例和其他相關資源，提供中繼資料以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（如果適當）來組織和追蹤超大規模 (Citus) 實例和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。 這可防止在高安全性環境中建立和變更資源，例如超大規模的實例 (Citus) 包含敏感性資訊。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則來定義和實行超大規模 (Citus) 實例的標準安全性設定。 使用 Azure 原則建立自訂原則，以對適用於 PostgreSQL 的 Azure 資料庫實例的網路設定進行審核或強制執行。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，您應該檢查這些設定，以確保設定符合/超過組織的安全性需求。 

- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md) 



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。  此外，您可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。 

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果針對您的超大規模使用自訂的 Azure 原則定義 (Citus) 實例和相關資源，請使用 Azure Repos 安全地儲存和管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。  此外，您可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。 

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指引**：使用 "Microsoft.DBforPostgreSQL" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、稽核和強制執行系統設定。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行適用於 PostgreSQL 的 Azure 資料庫實例和相關資源的設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 目前不直接支援受控識別。 建立適用於 PostgreSQL 的 Azure 資料庫伺服器時，您必須提供系統管理員使用者的認證。 您可以在 Azure 入口網站介面中建立額外的使用者角色。

- [建立適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md#create-an-azure-database-for-postgresql---hyperscale-citus)

- [建立額外的使用者角色](./howto-hyperscale-create-users.md#how-to-create-additional-user-roles)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意程式碼（例如，超大規模 (Citus) --不過，它不會在客戶內容上執行。

預先掃描要上傳至非計算 Azure 資源的任何內容，例如 App Service、Data Lake Storage、Blob 儲存體、適用於 PostgreSQL 的 Azure 資料庫等。Microsoft 無法存取這些執行個體中的資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：適用於 PostgreSQL 的 Azure 資料庫–超大規模 (Citus) 會自動建立每個節點的備份，並將它們儲存在本機的多餘儲存體中。 您可以使用備份，將超大規模 (Citus) 叢集還原至指定的時間。

- [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中備份和還原 ](./concepts-hyperscale-backup.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：至少一天一次，適用於 PostgreSQL 的 Azure 資料庫會取得資料檔案和資料庫交易記錄的快照集備份。 這些備份可讓您將伺服器還原至保留期限內的任何時間點。 所有叢集的保留期限目前為35天。 所有備份皆會使用 AES 256 位元加密進行加密。

在支援可用性區域的 Azure 區域中，備份快照集會儲存在三個可用性區域中。 只要至少有一個可用性區域處於線上狀態，超大規模 (Citus) 叢集就可以還原。

- [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中備份和還原 ](./concepts-hyperscale-backup.md)


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：在適用於 PostgreSQL 的 Azure 資料庫中，還原超大規模 (Citus) 叢集會從原始節點的備份建立新的叢集。 您可以將叢集還原至過去35天內的任何時間點。 還原程式會在與原始的相同 Azure 區域、訂用帳戶和資源群組中建立新的叢集。 新的叢集設定與原始叢集設定相同：相同數目的節點、虛擬核心數目、儲存體大小，以及使用者角色。

系統不會保留原始伺服器群組中的防火牆設定和于 postgresql 伺服器參數;它們會重設為預設值。 防火牆會防止所有連接。 您將需要在還原之後手動調整這些設定。

- [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中備份和還原 ](./concepts-hyperscale-backup.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**：已刪除的超大規模 (Citus) 叢集無法還原。 如果您刪除叢集，所有屬於該叢集的節點都會刪除，而且無法復原。 若要在部署後防止意外刪除或非預期的變更來保護叢集資源，系統管理員可以利用管理鎖定。

- [如何在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 中備份和還原 ](./concepts-hyperscale-backup.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

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

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。 

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

*如需詳細資訊，請參閱 [安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指引**：遵循 Microsoft 參與規則，以確保滲透測試不會違反 Microsoft 原則： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)