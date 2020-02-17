---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7a5d4f7ee4bd5c9377fb53c18a7f07c9d6f8aa3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170316"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurityEmails_Audit.json) |請確認已在進階資料安全性伺服器設定中的 [警訊接收者] 欄位提供電子郵件地址。 當在 SQL 受控執行個體上偵測到異常活動時，此電子郵件地址就會收到警訊通知。 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurityEmails_Audit.json) |請確認已在進階資料安全性伺服器設定中的 [警訊接收者] 欄位提供電子郵件地址。 當在 SQL 伺服器上偵測到異常活動時，此電子郵件地址就會收到警訊通知。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在 SQL 受控執行個體上啟用進階資料安全性](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |在沒有「進階資料安全性」的情況下稽核 SQL 受控執行個體 |AuditIfNotExists, Disabled |1.0.0 |
|[應在 SQL 伺服器上啟用進階資料安全性](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |在沒有「進階資料安全性」的情況下稽核 SQL 伺服器 |AuditIfNotExists, Disabled |1.0.0 |
|[在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureThreatDetectionTypes_Audit.json) |建議您在 SQL 伺服器上啟用所有進階威脅防護類型。 啟用所有類型可避免面臨 SQL 插入式攻擊、資料庫弱點和任何其他異常活動。 |AuditIfNotExists, Disabled |1.0.0 |
|[在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_EnsureThreatDetectionTypes_Audit.json) |建議您在 SQL 伺服器上啟用所有進階威脅防護類型。 啟用所有類型可避免面臨 SQL 插入式攻擊、資料庫弱點和任何其他異常活動。 |AuditIfNotExists, Disabled |1.0.0 |
|[應針對 SQL 伺服器佈建 Azure Active Directory 管理員](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |為 SQL Server 稽核 Azure Active Directory 系統管理員的佈建情況，以啟用 Azure AD 驗證。 Azure AD 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別 |AuditIfNotExists, Disabled |1.0.0 |
|[應啟用 SQL 伺服器上的稽核](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |應在 SQL Server 上啟用稽核，以追蹤伺服器上所有資料庫的活動，並儲存在稽核記錄中。 |AuditIfNotExists, Disabled |1.0.0 |
|[應為 PostgreSQL 資料庫伺服器啟用連線節流](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |此原則可協助稽核您環境中任何未啟用連線節流的 PostgreSQL 資料庫。 此設定可針對每個發生太多無效密碼登入失敗的 IP 啟用暫時連線節流。 |AuditIfNotExists, Disabled |1.0.0 |
|[在 SQL 伺服器上部署進階資料安全性](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAdvancedDataSecurity_Deploy.json) |此原則會在 SQL Server 上啟用進階資料安全性。 包括開啟威脅偵測和弱點評估。 此原則將自動在與 SQL server 相同的區域和資源群組中，建立前置詞為 'sqlva' 的儲存體帳戶，以儲存掃描結果。 |DeployIfNotExists |1.0.0 |
|[在 SQL 伺服器上部署稽核](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditing_Deploy.json) |此原則可確保稽核已在 SQL 伺服器上啟用，以增強安全性與合規性。 這會在與 SQL 伺服器相同的區域自動建立儲存體帳戶來儲存稽核記錄。 |DeployIfNotExists |1.0.0 |
|[將 Azure SQL Database 的診斷設定部署至事件中樞](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlDB_DiagnosticsLog_Deploy.json) |針對 Azure SQL Database 部署診斷設定，以串流至所有缺少此診斷設定而建立或更新之 Azure SQL Database 上的區域事件中樞。 |DeployIfNotExists |1.0.0 |
|[部署 SQL DB 透明資料加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlDBEncryption_Deploy.json) |在 SQL 資料庫上啟用透明資料加密 |DeployIfNotExists |1.0.0 |
|[在 SQL 伺服器上部署威脅偵測](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/DeployTdOnSqlServers_Deploy.json) |這項原則會確保 SQL Server 已啟用威脅偵測。 |DeployIfNotExists |1.0.0 |
|[應為 PostgreSQL 資料庫伺服器記錄中斷連線。](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |此原則可協助稽核您環境中任何未啟用 log_disconnections 的 PostgreSQL 資料庫。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurityEmailAdmins_Audit.json) |稽核 SQL 受控執行個體進階威脅防護設定中是否已啟用 [以電子郵件傳送通知給系統管理員和訂用帳戶擁有者]。 這可確保在 SQL 受控執行個體上偵測到的異常活動都會盡快回報給系統管理員。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurityEmailAdmins_Audit.json) |稽核 SQL 伺服器進階威脅防護設定中是否已啟用 [以電子郵件傳送通知給系統管理員和訂用帳戶擁有者]。 這可確保在 SQL 伺服器上偵測到的異常活動都會盡快回報給系統管理員。 |AuditIfNotExists, Disabled |1.0.0 |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |此原則會針對未強制執行 SSL 連線的任何 MySQL 伺服器進行稽核。 適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 |Audit, Disabled |1.0.0 |
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |此原則會針對未強制執行 SSL 連線的任何 PostgreSQL 伺服器進行稽核。 適用於 PostgreSQL 的 Azure 資料庫偏好使用安全通訊端層 (SSL)，來將用戶端應用程式連接到 PostgreSQL 服務。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊 |Audit, Disabled |1.0.0 |
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |此原則會稽核所有未啟用異地備援備份之適用於 MariaDB 的 Azure 資料庫。 |Audit, Disabled |1.0.0 |
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |此原則會稽核所有未啟用異地備援備份的適用於 MySQL 的 Azure 資料庫。 |Audit, Disabled |1.0.0 |
|[應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |此原則會稽核所有未啟用異地備援備份的適用於 PostgreSQL 的 Azure 資料庫。 |Audit, Disabled |1.0.0 |
|[應為 PostgreSQL 資料庫伺服器啟用記錄檢查點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |此原則可協助稽核您環境中任何未啟用 log_checkpoints 設定的 PostgreSQL 資料庫。 |AuditIfNotExists, Disabled |1.0.0 |
|[應為 PostgreSQL 資料庫伺服器啟用記錄連線](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |此原則可協助稽核您環境中任何未啟用 log_connections 設定的 PostgreSQL 資料庫。 |AuditIfNotExists, Disabled |1.0.0 |
|[應為 PostgreSQL 資料庫伺服器啟用記錄持續時間](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDuration_Audit.json) |此原則可協助稽核您環境中任何未啟用 log_duration 設定的 PostgreSQL 資料庫。 |AuditIfNotExists, Disabled |1.0.0 |
|[應為 Azure SQL Database 啟用長期異地備援備份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |此原則會稽核所有未啟用長期異地備援備份的 Azure SQL Database。 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL 審核設定應已設定動作群組來擷取重要活動](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |AuditActionsAndGroups 屬性至少應包含 SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP、FAILED_DATABASE_AUTHENTICATION_GROUP、BATCH_COMPLETED_GROUP，才可確保完整記錄稽核 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL 受控執行個體的 TDE 保護裝置應以您自己的金鑰加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |具有自備金鑰支援的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL 伺服器的 TDE 保護裝置應以您自己的金鑰加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |具有自備金鑰支援的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL 伺服器應設定有 90 天以上的稽核保留天數。](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |對稽核保留期間設為 90 天以下的 SQL 伺服器進行稽核。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在 SQL 資料庫上啟用透明資料加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |應啟用透明資料加密，以保護待用資料，並滿足合規性需求 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |請確認已為弱點評估設定中的 [掃描報告收件者] 欄位提供電子郵件地址。 此電子郵件地址會在 SQL 伺服器上執行定期掃描後收到掃描結果摘要。 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL 受控執行個體上應啟用弱點評估](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |稽核未啟用週期性弱點評估掃描的 SQL 受控執行個體。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |1.0.0 |
|[弱點評估應於您的 SQL 伺服器上啟用](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |稽核未啟用週期性弱點評估掃描的 Azure SQL 伺服器。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |1.0.0 |
