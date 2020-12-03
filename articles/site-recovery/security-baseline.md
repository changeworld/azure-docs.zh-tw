---
title: 適用于 Site Recovery 的 Azure 安全性基準
description: Site Recovery 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 860bc9440547a4c62336ccc573859ec56b3400b5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533854"
---
# <a name="azure-security-baseline-for-site-recovery"></a>適用于 Site Recovery 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview-v1.md) 的指引套用至 Site Recovery。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Site Recovery 的相關指引來分組。 已排除不適用 Site Recovery 的 **控制項**。 

若要查看 Site Recovery 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Site Recovery 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱 [Azure 安全性效能評定：網路安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針： Microsoft Azure Site Recovery 不支援部署至 Azure 虛擬網路。 使用 Azure 私人端點設定 Site Recovery 服務，以強制透過網路進行安全通訊。

- [Azure Site Recovery Private Link 支援](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針： Site Recovery 服務支援服務標籤，可讓客戶只開啟特定服務和埠的流量。 客戶必須在其防火牆或網路安全性群組上允許 "AzureSiteRecovery" 服務標籤，以允許對 Site Recovery 服務的輸出存取。

- [使用服務標記的輸出連線能力](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：使用網路安全性群組的資源標記，以及與網路安全性和流量流程相關的其他資源。 針對個別的網路安全性群組規則，請使用 [描述] 欄位來記錄允許流量進出網路的規則。 

合併任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並通知您現有的未標記資源。 

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。 

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md) 

- [如何建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md) 

- [如何使用網路安全性群組規則來篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視與 Site Recovery 服務相關之網路資源設定的任何變更。 在 Azure 監視器中建立警示，以在重大 Site Recovery 網路資源變更時通知您。

- [查看和取出 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [使用 Azure 監視器中建立、檢視及管理活動記錄警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：啟用 Azure 活動記錄診斷設定以進行審核記錄，並將記錄傳送至 Log Analytics 工作區、Azure 儲存體帳戶或 Azure 事件中樞以供保存。

使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在您的 Azure 資源上執行。

內嵌 Site Recovery 記錄 Azure 監視器中，以匯總產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用儲存體帳戶來儲存長期或封存儲存體。 此外，您也可以啟用和內部資料來 Azure Sentinel 或協力廠商安全性事件和事件管理 (SIEM) 解決方案。

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [透過 Azure 監視器記錄監視 Site Recovery](monitor-log-analytics.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用 Azure 活動記錄診斷設定以進行審核記錄，並將記錄傳送至 Log Analytics 工作區、Azure 儲存體帳戶或 Azure 事件中樞以供保存。 

使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在您的 Azure 資源上執行。

使用 Azure 監視器內嵌 Site Recovery 記錄，以匯總產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用儲存體帳戶來儲存長期/封存儲存體。 啟用和內部資料來 Azure Sentinel 或協力廠商安全性事件和事件管理 (SIEM) 解決方案。

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [透過 Azure 監視器記錄監視 Site Recovery](monitor-log-analytics.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：根據組織的合規性法規，使用 Azure 監視器設定與 Azure 復原服務保存庫相關聯的 log Analytics 工作區記錄保留期限。 

- [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區。 

在 Log Analytics 中執行查詢，以搜尋詞彙、識別趨勢、分析模式，以及從復原服務保存庫收集的活動記錄資料深入解析。

- [監視 Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何在 Azure 監視器的 Log Analytics 工作區中收集和分析 Azure 活動記錄](../azure-monitor/platform/activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：使用 Azure 監視器記錄和記錄分析來監視 Azure Site Recovery 所複寫的機器。 使用 Azure 監視器內的 Log Analytics 來撰寫和測試記錄查詢，以及以互動方式分析記錄資料。 Azure 監視器會收集活動和資源記錄，以及其他監視資料。 

將記錄結果視覺化並進行查詢，並設定警示以根據受監視的資料採取動作。 在 Log Analytics 工作區上設定警示來 Azure Sentinel，因為它提供安全性協調流程自動化回應 (SOAR) 解決方案。 這可提供自動化的解決方案，例如建立和使用腳本來補救安全性問題。 使用 Azure 監視器在您的 Log Analytics 工作區中建立自訂記錄警示。 

- [監視 Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [使用 Azure 監視器來建立、檢視及管理記錄警示](../azure-monitor/platform/alerts-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針：預設不會指派任何角色。 它們必須根據商務需求明確指派。 您可以使用 PowerShell CLI 或 Azure Active Directory (Azure AD) 來檢查任何角色指派，以找出屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用資訊安全中心的身分識別和存取管理功能來監視系統管理帳戶的數目。

此外，為了協助您追蹤專屬的系統管理帳戶，請使用資訊安全中心的建議或內建的 Azure 原則，例如： 

- 應將一個以上的擁有者指派給您的訂用帳戶 
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除 

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

建立進程來追蹤系統管理帳戶的身分識別和存取控制，並定期進行審核。

- [如何使用 Azure 資訊安全中心來監視身分識別和存取](../security-center/security-center-identity-access.md)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：搭配服務主體使用 Azure 應用程式註冊，以抓取用來透過 API 呼叫與復原服務保存庫互動的權杖。

- [如何呼叫 Azure REST Api](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [如何使用 Azure AD 向 (服務主體) 註冊用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure 復原服務 API 資訊](/rest/api/recoveryservices)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure AD、多重要素驗證，並遵循資訊安全中心的身分識別和存取建議。 
- [規劃 Azure AD Multi-Factor Authentication 部署](../active-directory/authentication/howto-mfa-getstarted.md)

- [監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用安全且受 Azure 管理的工作站 (也稱為特殊許可權存取工作站 (PAW) # A3 搭配 Azure 多重要素驗證進行系統管理工作，以及在 Site Recovery 資源上執行特殊許可權動作。

- [特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [規劃雲端式 Azure AD Multi-Factor Authentication 部署](../active-directory/authentication/howto-mfa-getstarted.md) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：當環境中發生可疑或不安全的活動時，請使用 Azure AD 的 PRIVILEGED IDENTITY MANAGEMENT (PIM) 功能來產生記錄和警示。
使用 Azure AD 風險偵測功能，查看具有風險的使用者行為警示和報告。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 風險偵測](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍、區域或國家/地區的特定邏輯群組存取 Azure 入口網站。
- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure AD 作為 Site Recovery 的中央驗證和授權系統。 Azure AD 使用強式加密來保護資料、傳輸中的資料，以及 salts、雜湊，以及安全地儲存使用者認證，藉此保護資料。 

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針：使用 Azure AD 記錄來協助探索過時的帳戶。 

使用 Azure AD 的身分識別和存取權評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 

建立可定期審核使用者存取的流程，以確保只有已完成存取權審查的使用者才能繼續存取。 

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：使用 Azure AD 作為 Site Recovery 資源的中央驗證和授權系統。 Azure AD 使用強式加密來保護待用和傳輸中的資料，以及 salts、雜湊及安全地儲存使用者認證，藉此保護資料。

您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以將它們與 Azure Sentinel 或 Azure Marketplace 中任何可用的 SIEM 或監視工具整合。

藉由建立 Azure AD 使用者帳戶的診斷設定，以及將審核和登入記錄傳送至 Log Analytics 工作區，進一步簡化此程式。 您可以在 Log Analytics 工作區中設定所需的警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 Azure AD 作為復原服務保存庫的中央驗證和授權系統。 

使用 Azure AD 的身分識別保護功能來偵測帳戶登入行為，以及設定偵測到的可疑動作（與使用者身分識別相關）的自動回應。 也可將資料內嵌到 Azure Sentinel 以供進一步調查。

- [如何查看 Azure AD 具風險的登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱 [Azure 安全性效能評定：資料保護](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：針對開發、測試和生產復原服務保存庫執行個別的訂用帳戶或管理群組。 使用虛擬網路或子網分隔資源，並適當地標記，並受到網路安全性群組或 Azure 防火牆的保護。 

當不在使用中時，關閉儲存或處理敏感性資料的虛擬機器。 執行原則和程式，使其成為週期性的流程。 

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [Site Recovery 總覽](site-recovery-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：使用 Private Link 或私人端點、網路安全性群組和服務標籤，減輕從已啟用 Site Recovery 的虛擬機器遭到外泄資料的機會。

Microsoft 會管理 Site Recovery 所使用的基礎平臺，並將所有客戶內容視為機密，並防止客戶資料遺失和公開。 Microsoft 已實行並維護一套強大的資料保護控制項和功能，以確保 Azure 中的客戶資料保持安全。 

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

- [使用 Azure 私人端點複寫虛擬機器](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [使用 Azure Site Recovery 服務標記複寫虛擬機器](azure-to-azure-about-networking.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針： Site Recovery 使用安全的 HTTPs 通道（使用進階加密標準 (AES 256) ），從 Azure 工作負載伺服器到在復原服務保存庫後方託管的 Site Recovery 服務。

目前支援 Site Recovery 的 TLS 版本為區域中的 TLS 1.0、TLS 1.1、TLS 1.2，其存留于2019的結尾。 TLS 1.2 是針對任何新區域唯一支援的 TLS 版本。

- [瞭解 Azure Site Recovery 的傳輸中加密](physical-azure-set-up-source.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Site Recovery 尚無法使用資料識別、分類和遺失防護功能。 

視需要針對合規性目的來執行協力廠商解決方案。

Microsoft 會管理 Site Recovery 所使用的基礎平臺，並將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 它已實行並維護一套強大的資料保護控制項和功能，以確保 Azure 中的客戶資料保持安全。 

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 來管理資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 來管理與 Site Recovery 資源相關之資料和資源的存取權。 

將工作責任與 Azure RBAC 分開，並授與適當的存取權。 使用內建的 Site Recovery 角色來控制 Site Recovery 的管理作業。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [使用 Role-Based 存取控制來管理 Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：使用平臺和客戶管理的金鑰來啟用雙重加密。 Site Recovery 中提供這項功能。 

Site Recovery 支援資料的待用加密。 針對 Azure IaaS 工作負載，資料會使用儲存體服務加密 (SSE) 進行靜態加密。 

只有當客戶使用以客戶管理的金鑰加密的復原服務保存庫時，才有權存取加密金鑰。 Microsoft 永遠不會維護複本、沒有金鑰的存取權，也不會將從主要位置傳輸的資料解密到任何時間點。 

- [Azure Site Recovery 的客戶管理金鑰支援](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄，以在對重要資源進行變更時建立警示。 這些資源可能包括復原服務保存庫的生產實例、Site Recovery 服務的資源，以及相關資源。
- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢或探索訂用帳戶內的所有資源，包括復原服務保存庫。 確定您的租使用者中有適當的讀取權限，以及列舉訂用帳戶中的所有 Azure 訂用帳戶和資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：將標記套用至復原服務保存庫和其他相關資源（由 Site Recovery 搭配中繼資料使用），以邏輯方式將它們組織成分類法。
- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（適當時）來組織和追蹤 Site Recovery (復原服務保存庫) 和其他相關資源。 

此外，您也可以使用下列內建原則定義，利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制： 

- 不允許的資源類型
- 允許的資源類型

請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據客戶的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制： 

- 不允許的資源類型 
- 允許的資源類型

使用 Azure Resource Graph 來查詢及探索訂用帳戶內的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型 
- 允許的資源類型

了解如何在 Azure 中建立和管理原則，對於保持符合公司標準和服務等級協定而言，相當重要。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。 這可防止在高安全性環境中建立和變更資源。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則定義和執行復原服務保存庫的標準安全性設定。 

使用 "Az.recoveryservices" 命名空間中 Azure 原則別名來建立自訂原則，以進行審核或強制執行 Site Recovery 服務的復原服務保存庫資源設定。
- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 Azure 原則 [拒絕] 和 [部署（如果不存在）] 效果，在您的 Azure 資源上強制執行安全設定。
- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果您使用復原服務保存庫的自訂 Azure 原則定義和相關資源，請選擇 Azure Repos 來安全地儲存和管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos 文件](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用內建的 Azure 原則定義以及 "az.recoveryservices" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 

此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用內建的 Azure 原則定義以及 "az.recoveryservices" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 

使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在] 效果，自動強制執行 Azure 資源的設定。
- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：客戶應該管理與 Azure Key vault 整合的 Site Recovery 秘密，同時針對啟用 Azure 磁碟加密的虛擬機器啟用損毀修復。 

- [如何建立金鑰保存庫](../key-vault/general/quick-create-portal.md)

- [如何向 Key vault 進行驗證](../key-vault/general/authentication.md)

- [如何指派 Key vault 存取原則](../key-vault/general/assign-access-policy-portal.md)

- [如何使用 Site Recovery 為啟用 Azure 磁碟加密的虛擬機器啟用 DR](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針： Site Recovery 僅支援系統管理的身分識別，客戶可以在其中啟用復原服務保存庫上的系統受控識別。 相同的方法適用于損毀修復供應專案中用來定義存取界限的資源。 

使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 

受控識別可讓您向支援 Azure AD authentication 的任何服務進行驗證，包括 Key Vault，而不需要在您的程式碼中提供任何認證。

- [如何與 Azure 受控識別整合](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [如何在復原服務保存庫上啟用系統受控識別](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針： Microsoft Antimalware 會在支援 Azure 服務的基礎主機上啟用 (例如 Site Recovery) ，不過它不會在您的內容上執行。 預先掃描即將上傳至非計算 Azure 資源的任何檔案，例如 App Service、Data Lake Storage 和 Blob 儲存體。

使用資料服務的安全中心威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。

- [瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

- [瞭解 Azure 資訊安全中心的資料服務威脅偵測](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針： Site Recovery 內部使用 Azure 儲存體帳戶來維護嚴重損壞修復解決方案的狀態，由客戶在其工作負載上進行設定。

Site Recovery 服務中繼資料使用的所有儲存體資源，其設定類型為：讀取權限異地冗余儲存體 (GRS) 。 上述類型的儲存體帳戶 GRS (例如 RAGRS、RAG ZRS) 會將您的資料複寫到次要區域 (數百英里離開來源資料的主要位置) 在中斷時繼續為客戶提供嚴重損壞修復。

這超出客戶範圍，Site Recovery 團隊在內部負責處理。 客戶可以在 Azure 中備份 Key Vault 金鑰。

- [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：定期測試已備份之客戶管理金鑰的還原。

- [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：資料會使用儲存體服務加密 (SSE) 以 Azure 的基礎結構即服務 (IaaS) 型虛擬機器進行加密。 在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

- [如何在 Key Vault 中啟用虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱 [Azure 安全性效能評定：事件回應](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 

確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理的階段，或從偵測到事件後評論的管理階段。

- [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md)

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導** 方針：根據資訊安全中心指派的警示嚴重性，排列應優先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

明確標示訂用帳戶 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md) 

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 視需要找出弱式點和間隙和修訂計畫

- [請參閱 NIST 的發行指南，以針對 IT 方案和功能進行測試、訓練和練習程式](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 

建立程式來檢查事件（post），以確定問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出您的安全性中心警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 

使用「安全性中心」資料連線器，視需要將警示串流至 Azure Sentinel。
- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用資訊安全中心的工作流程自動化功能，透過安全性警示和建議的「Logic Apps」來自動觸發回應。
- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指引**：遵循 Microsoft 參與規則，以確保滲透測試不會違反 Microsoft 原則： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定 V2 概觀](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)