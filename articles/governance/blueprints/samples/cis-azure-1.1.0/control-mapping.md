---
title: CIS Microsoft Azure 基礎基準測試藍圖範例控制項
description: CI Microsoft Azure 基礎基準測試藍圖範例與 Azure 原則的建議對應。
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: 34d38f34dcd4233706f9b4578bc2dc2a644e4c2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74707417"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CI Microsoft Azure 基礎基準測試藍圖範例的建議對應。

下列文章將詳細說明 Azure 藍圖 CIS Microsoft Azure 基礎基準測試藍圖範例如何對應至 CIS Microsoft Azure 基礎基準測試建議。 如需關於建議的詳細資訊，請參閱 [CIS Microsoft Azure基礎基準測試](https://www.cisecurity.org/benchmark/azure/)。

以下是 **CIS Microsoft Azure 基礎基準測試 v1.1.0** 建議的對應。 使用右側的導覽區可直接跳到特定的建議對應。
許多對應的建議都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，找出並選取 **\[預覽\] 稽核 CIS Microsoft Azure 基礎基準測試 v1.1.0 建議，並部署特定的 VM 延伸模組，以支援稽核需求**內建原則方案。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md) \(英文\)。

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 確定已為所有具特殊權限的使用者啟用多重要素驗證

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您在具特殊權限的 Azure Active Directory 帳戶上未啟用多重要素驗證時進行監視。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 確定已為所有不具特殊權限的使用者啟用多重要素驗證

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您在不具特殊權限的 Azure Active Directory 帳戶上未啟用多重要素驗證時進行監視。

- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 確定沒有任何來賓使用者

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您監視可能需要移除的來賓帳戶。

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 確定不會建立自訂訂用帳戶擁有者角色

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您監視可能需要移除的自訂訂用帳戶擁有者角色。

- 自訂訂用帳戶擁有者角色不應存在

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 確定已選取標準定價層

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您監視未啟用資訊安全中心標準層的網路和虛擬機器。

 - 應選取資訊安全中心標準定價層

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 確定 [監視代理程式的自動化佈建] 會設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已啟用 Log Analytics 代理程式的自動化佈建。

- 您的訂用帳戶上應啟用 Log Analytics 監視代理程式的自動化佈建

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 確定 ASC 預設原則設定 [監視系統更新] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在虛擬機器上安裝系統更新。

- 您應在機器上安裝系統更新

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 確定 ASC 預設原則設定 [監視 OS 弱點] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您監視未修復的虛擬機器弱點。

- 您應在機器上修復安全性組態的弱點

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 確定 ASC 預設原則設定 [監視端點保護] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在虛擬機器上啟用端點保護。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 確定 ASC 預設原則設定 [監視磁碟加密] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已將虛擬機器磁碟加密。

- 應在虛擬機器上套用磁碟加密

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 確定 ASC 預設原則設定 [監視網路安全性群組] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您保護網際網路對應的虛擬機器。

- 應強化與虛擬機器互動的網際網路網路安全性群組規則

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 確定 ASC 預設原則設定 [監視 Web 應用程式防火牆] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您保護執行 Web 應用程式的虛擬機器。

- 應在 IaaS 上強化 Web 應用程式的 NSG 規則

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 確定 ASC 預設原則設定 [啟用新一代防火牆 (NGFW) 監視] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，藉由限制存取來協助保護子網路和虛擬機器免於受到威脅。 這個 CIS Microsoft Azure Foundations Benchmark 建議所參考的資訊安全中心原則已由兩個新的建議所取代。 以下參考的原則均會提出新的建議。

- 子網路應該與網路安全性群組建立關聯
- 虛擬機器應該與網路安全性群組建立關聯

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 確定 ASC 預設原則設定 [監視弱點評估] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已偵測到並修復弱點。

- 弱點評量解決方案應修復弱點

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 確定 ASC 預設原則設定 [監視儲存體 Blob 加密] 不是 [已停用]

系統會針對所有新的和現有的儲存體帳戶啟用 Azure 儲存體服務加密且無法停用 (這是預設的 Azure 功能；沒有原則指派)。

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 確定 ASC 預設原則設定 [監視 JIT 網路存取] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您控制對虛擬機器的存取。

- Just-In-Time 網路存取控制應套用在虛擬機器上

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 確定 ASC 預設原則設定 [監視自適性應用程式允許清單] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在虛擬機器上啟用自適性應用程式控制。

- 應在虛擬機器上啟用自適性應用程式控制

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 確定 ASC 預設原則設定 [監視 SQL 稽核] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助確定已啟用 SQL 伺服器稽核。

- 應該在 SQL Server 上的進階資料安全性設定上啟用稽核

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 確定 ASC 預設原則設定 [監視 SQL 加密] 不是 [已停用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在 SQL 資料庫上啟用透明資料加密。

- 應在 SQL 資料庫上啟用透明資料加密

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 確定已設定 [安全性連絡人電子郵件]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確啟用安全性通知。

- 應為您的訂用帳戶提供安全性連絡人電子郵件地址

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 確定已設定安全性連絡人的 [電話號碼]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確啟用安全性通知。

- 應為您的訂用帳戶提供安全性連絡人電話號碼

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 確定 [在出現高嚴重性警示時傳送電子郵件通知] 會設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確啟用安全性通知。

- 應針對高嚴重性警示啟用電子郵件通知

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 確定 [同時將電子郵件傳送給訂用帳戶擁有者] 會設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確啟用安全性通知。

- 應已針對高嚴重性警示啟用傳送給訂用帳戶擁有者的電子郵件通知

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 確定 [需要安全傳輸] 設定為 [已啟用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您監視允許不安全連線的儲存體帳戶。

- 應啟用儲存體帳戶的安全傳輸

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 確定儲存體帳戶的預設網路存取規則設定為拒絕

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您監視允許不受限制存取的儲存體帳戶。

- 稽核不受限制的儲存體帳戶網路存取

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 確定已啟用 [信任的 Microsoft 服務] 來進行儲存體帳戶存取

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您監視不允許來自信任 Microsoft 服務之存取的儲存體帳戶。

- 儲存體帳戶應允許來自信任 Microsoft 服務的存取

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 確定 [稽核] 設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助確定已啟用 SQL 伺服器稽核。 

- 應該在 SQL Server 上的進階資料安全性設定上啟用稽核

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 確定已在 SQL 伺服器的 [稽核] 原則中正確設定了 'AuditActionGroups' 原則

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確設定 SQL 伺服器稽核。

- SQL 審核設定應已設定動作群組來擷取重要活動

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 確定 [稽核] 保留期「大於 90 天」

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 SQL 伺服器記錄至少會保留 90 天。

- SQL 伺服器應設定有 90 天以上的稽核保留天數。

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 確定 SQL 伺服器上的 [進階資料安全性] 設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在 SQL 伺服器和 SQL 受控執行個體上啟用進階資料安全性。

- 應在 SQL 受控執行個體上啟用進階資料安全性
- 應在 SQL 伺服器上啟用進階資料安全性

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 確定 [威脅偵測類型] 設定為 [全部]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在 SQL 伺服器和 SQL 受控執行個體上正確設定進階威脅防護。

- 在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 確定已設定 [傳送警示給]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確啟用進階資料安全性通知。

- SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 確定 [電子郵件服務和共同管理員] 為 [已啟用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確啟用進階資料安全性通知。

- 應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知
- 應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 確定已設定 Azure Active Directory 管理員

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已為 SQL 伺服器佈建 Azure Active Directory 系統管理員。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 確定 SQL Database 上的 [資料加密] 設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在 SQL 資料庫上啟用透明資料加密。

- 應在 SQL 資料庫上啟用透明資料加密

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 確定 SQL 伺服器的 TDE 保護裝置已使用 BYOK 加密 (使用您自己的金鑰)

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 SQL 伺服器和 SQL 受控執行個體的透明資料加密保護裝置會使用您自己的金鑰進行加密。

- SQL 受控執行個體的 TDE 保護裝置應以您自己的金鑰加密
- SQL 伺服器的 TDE 保護裝置應以您自己的金鑰加密

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 確定會為 MySQL 資料庫伺服器將 [強制執行 SSL 連線] 設定為 [已啟用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 MySQL 資料庫伺服器會強制執行 SSL 連線。

- 應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 確定會為 PostgreSQL 資料庫伺服器將 [log_checkpoints] 伺服器參數設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 PostgreSQL 資料庫伺服器會記錄檢查點。

- 應為 PostgreSQL 資料庫伺服器啟用記錄檢查點

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 確定會為 PostgreSQL 資料庫伺服器將 [強制執行 SSL 連線] 設定為 [已啟用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 PostgreSQL 資料庫伺服器會強制執行 SSL 連線。

- 應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 確定會為 PostgreSQL 資料庫伺服器將 [log_connections] 伺服器參數設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 PostgreSQL 資料庫伺服器會記錄連線。

- 應為 PostgreSQL 資料庫伺服器啟用記錄連線

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 確定會為 PostgreSQL 資料庫伺服器將 [log_disconnections] 伺服器參數設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 PostgreSQL 資料庫伺服器會記錄中斷連線。

- 應為 PostgreSQL 資料庫伺服器記錄中斷連線。

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 確定會為 PostgreSQL 資料庫伺服器將 [log_duration] 伺服器參數設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 PostgreSQL 資料庫伺服器會記錄已完成陳述式的持續時間。

- 應為 PostgreSQL 資料庫伺服器啟用記錄持續時間

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 確定會為 PostgreSQL 資料庫伺服器將 [connection_throttling] 設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您減緩 PostgreSQL 資料庫伺服器上的暴力密碼破解攻擊。

- 應為 PostgreSQL 資料庫伺服器啟用連線節流

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 確定已設定 Azure Active Directory 系統管理員

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已為 SQL 伺服器佈建 Azure Active Directory 系統管理員。 CIS Microsoft Azure Foundations Benchmark 會包含這項建議；不過，它是[建議 4.8](#48-ensure-that-azure-active-directory-admin-is-configured) 的複本。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 確定記錄設定檔存在

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定所有 Azure 訂用帳戶都會有記錄設定檔存在。 

- Azure 訂用帳戶應具有用於活動記錄的記錄設定檔

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 確定 [活動記錄保留] 會設定為 365 天或更長天數

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定活動記錄至少會保留一年。

- 活動記錄至少應保留一年

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 確定稽核設定檔會擷取所有活動

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確設定記錄設定檔。

- Azure 監視器記錄設定檔應收集 [寫入]、[刪除] 和 [動作] 分類的記錄

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 確定記錄設定檔會擷取所有區域 (包括全球) 的活動記錄

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已正確設定記錄設定檔。

- Azure 監視器應從所有區域收集活動記錄

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 確定 Azure KeyVault 的記錄是 [已啟用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定會為金鑰保存庫啟用診斷記錄。

- 應啟用 Key Vault 中的診斷記錄

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 確定網路監看員為 [已啟用]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已針對部署資源的所有區域啟用網路監看員。 此原則需要指定所有適用區域的參數陣列。 此原則方案定義中的預設值為 [eastus]。

- 應啟用網路監看員

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 確定 [OS 磁碟] 已加密

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在虛擬機器上啟用磁碟加密。

- 應在虛擬機器上套用磁碟加密

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 確定 [資料磁碟] 已加密

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在虛擬機器上啟用磁碟加密。

- 應在虛擬機器上套用磁碟加密

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 確定 [未連結的磁碟] 已加密

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已將未連結的磁碟加密。

- 未連結的磁碟應加密

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 確定只安裝核准的擴充功能

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定只會安裝核准的虛擬機器擴充功能。 此原則需要指定所有虛擬機器擴充功能的參數陣列。 此原則方案定義會包含客戶應驗證的建議預設值。 

 - 僅應安裝已核准的 VM 擴充功能

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 確定已為所有虛擬機器套用最新 OS 修補程式

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在虛擬機器上安裝系統更新。

- 您應在機器上安裝系統更新

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 確定已為所有虛擬機器安裝端點

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定已在虛擬機器上啟用端點保護。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 確定金鑰保存庫可復原

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定金鑰保存庫物件可在意外刪除的情況下復原。

- Key Vault 物件應該要可復原

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 在 Azure Kubernetes Services 中啟用角色型存取控制 (RBAC)

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定會為 Kubernetes 服務叢集中的受控權限使用角色型存取控制。

- \[預覽\]：應在 Kubernetes 服務上使用角色型存取控制 (RBAC)

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 確定 Web 應用程式在 Azure App Service 中會將所有 HTTP 流量重新導向至 HTTPS

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式只能透過安全連線進行存取。

- Web 應用程式應只可經由 HTTPS 存取

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 確定 Web 應用程式使用最新版本的 TLS 加密

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式會使用最新的 TLS 版本。

- 您的 API 應用程式應使用最新的 TLS 版本
- 您的函式應用程式應使用最新的 TLS 版本
- 您的 Web 應用程式應使用最新的 TLS 版本

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定只有具備有效憑證的用戶端可連線至 Web 應用程式。

- 確定 API 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]
- 確定函式應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]
- 確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 確定已在 App Service 上啟用 [向 Azure Active Directory 註冊]

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式會使用受控識別。

- 確定已在 API 應用程式上啟用 [向 Azure Active Directory 註冊]
- 確定已在函數應用程式上啟用 [向 Azure Active Directory 註冊]
- 確定已在 Web 應用程式上啟用 [向 Azure Active Directory 註冊]

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 確定 '.Net Framework' 在作為 Web 應用程式的一部分時，版本是最新的

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式會使用最新版的 .Net Framework。

- 確定 '.Net Framework' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 '.Net Framework' 在作為函式應用程式的一部分時，版本是最新的
- 確定 '.Net Framework' 在作為 Web 應用程式的一部分時，版本是最新的

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 確定 'PHP' 在用來執行 Web 應用程式時，版本是最新的

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式會使用最新版的 PHP。

- 確定 'PHP' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'PHP' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'PHP' 在作為 Web 應用程式的一部分時，版本是最新的

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 確定 'Python' 在用來執行 Web 應用程式時，版本是最新的

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式會使用最新版的 Python。

- 確定 'Python' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為 Web 應用程式的一部分時，版本是最新的

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 確定 'Java' 在用來執行 Web 應用程式時，版本是最新的

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式會使用最新版的 Java。

- 確定 'Java' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'Java' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'Java' 在作為 Web 應用程式的一部分時，版本是最新的

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 確定 'HTTP' 在用來執行 Web 應用程式時，版本是最新的

此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以協助您確定 Web 應用程式會使用最新版的 HTTP。

- 確定 'HTTP' 在用來執行 API 應用程式時，版本是最新的
- 確定 'HTTP' 在用來執行函式應用程式時，版本是最新的
- 確定 'HTTP' 在用來執行 Web 應用程式時，版本是最新的


## <a name="next-steps"></a>後續步驟

您已檢閱 CIS Microsoft Azure 基礎基準測試藍圖的控制項對應，接下來請瀏覽下列文章以了解藍圖，或瀏覽 Azure 入口網站中的 Azure 原則來指派方案：

> [!div class="nextstepaction"]
> [CIS Microsoft Azure 基礎基準測試藍圖 - 概觀](./index.md)
> [CIS Microsoft Azure 基礎基準測試藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。