---
title: 適用于 Azure 儲存體的 Azure 安全性基準
description: 適用于 Azure 儲存體的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 184b60f6147089cbdfd855b473b3346d021f7fbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176595"
---
# <a name="azure-security-baseline-for-azure-storage"></a>適用于 Azure 儲存體的 Azure 安全性基準

適用于 Azure 儲存體的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自[Azure 安全性基準測試版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供有關如何在 Azure 上使用最佳作法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱[Azure 安全性基準總覽](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在您的虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導**方針：藉由限制從特定公用 IP 位址範圍存取用戶端、在 Azure 上選取虛擬網路（vnet）或特定 Azure 資源，來設定儲存體帳戶的防火牆。 您也可以設定私人端點，讓來自您企業之儲存體服務的流量以獨佔方式透過私人網路傳輸。

注意：傳統儲存體帳戶不支援防火牆與虛擬網路。

- [如何設定 Azure 儲存體防火牆](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [如何設定 Azure 儲存體的私用端點](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2：監視和記錄 Vnet、子網和 NIC 設定和流量

**指導**方針： Azure 儲存體提供多層式安全性模型。 您可以限制從指定的 IP 位址、IP 範圍或 Azure 虛擬網路（VNet）中的子網清單，存取您儲存體帳戶的要求。 您可以使用 Azure 資訊安全中心並遵循網路保護建議，協助保護 Azure 中的網路資源。 此外，請透過儲存體帳戶防火牆為儲存體帳戶設定的虛擬網路/子網啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶以進行流量審核。 

請注意，如果您的儲存體帳戶已附加私人端點，您就無法為子網設定網路安全性群組（NSG）規則。 

- [設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [瞭解 Azure 儲存體的私用端點](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：不適用;建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：為您的 Azure 儲存體帳戶啟用 [先進的威脅防護]。 Azure 儲存體的先進威脅防護提供一層額外的安全性情報，可偵測不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 Azure 資訊安全中心整合式警示是根據與已成功解析的 IP 位址相關聯之網路通訊的活動，不論 IP 位址是已知的具風險 IP 位址（例如已知的 cryptominer），或是先前無法辨識為有風險的 IP 位址。 當活動發生異常時，就會觸發安全性警示。 

- [如何啟用先進的威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [瞭解 Azure 資訊安全中心整合式威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指引**：網路監看員封包捕獲可讓您建立「捕獲會話」，以追蹤儲存體帳戶與虛擬機器之間的流量。 系統會為擷取工作階段提供篩選器，以確保您只會擷取到您想要的流量。 封包擷取有助於以被動和主動方式診斷網路異常。 其他用途包括收集網路統計資料、取得有關網路入侵的資訊，以及偵錯用戶端與伺服器間的通訊等等。 由於能夠從遠端觸發封包擷取，因此可以減輕在所需的虛擬機器上手動執行封包擷取的工作負擔，進而省下寶貴的時間。 

- [使用入口網站以 Azure 網路監看員管理封包擷取](https://docs.microsoft.comazure/network-watcher/network-watcher-packet-capture-manage-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6：部署以網路為基礎的入侵偵測/入侵預防系統

**指導**方針： Azure 儲存體的先進威脅防護提供一層額外的安全性情報，可偵測到不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 當活動發生異常時，就會觸發安全性警示。 這些安全性警示會與 Azure 資訊安全中心整合，並透過電子郵件傳送給訂用帳戶系統管理員，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。 

- [設定 Azure 儲存體的先進威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7：管理 web 應用程式的流量

**指導**方針：不適用;建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指引**：對於需要存取儲存體帳戶之虛擬網路中的資源，請使用設定的虛擬網路虛擬網路服務標籤，以定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標記名稱（例如儲存體），您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。 

當網路存取需要限定于特定儲存體帳戶時，請使用虛擬網路服務端點原則。

- [如需使用服務標記的詳細資訊](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [如需 Azure 儲存體的虛擬網路服務端點原則的詳細資訊](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：服務

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則，為與您的 Azure 儲存體帳戶相關聯的網路資源定義和實施標準安全性設定。 使用 "Microsoft. Storage" 和 "Microsoft" 命名空間中的 Azure 原則別名來建立自訂原則，以審核或強制執行儲存體帳戶資源的網路設定。 

您也可以使用與儲存體帳戶相關的內建原則定義，例如：儲存體帳戶應使用虛擬網路服務端點 

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [儲存體的 Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [網路的 Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

**指導**方針：使用網路安全性群組（NSG）的標記，以及與網路安全性和流量相關的其他資源。 針對個別的 NSG 規則，請使用 [描述] 欄位來指定允許進出網路流量的任何規則的商務需求和（或）持續時間（等）。 使用與標記相關的任何內建 Azure 原則定義（例如「需要標記和其值」），以確保所有資源都是以標籤建立，並通知您現有的未標記資源。 您可以使用 Azure PowerShell 或 Azure CLI，根據其標記來查閱或執行資源的動作。 

- [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 原則來記錄網路資源的設定變更。 在 Azure 監視器中建立警示，以在重大網路資源的變更發生時觸發。 

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何在 Azure 監視器中建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-resource"></a>2.1：使用已核准的時間同步處理資源

**指導**方針：不適用;Microsoft 會維護 Azure 儲存體帳戶的時間來源。

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用適用于長期/封存儲存體的 Azure 儲存體帳戶，選擇性地使用固定儲存體和強制保留保存等安全性功能。

- [如何使用 Azure 監視器收集平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的審核記錄

**指導**方針： Azure 儲存體分析提供 blob、佇列和資料表的記錄。 您可以使用 Azure 入口網站來設定要為您的客戶紀錄哪些記錄。 

- [如何設定 Azure 儲存體帳戶的監視](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指引**：將安全性事件記錄儲存在 Azure 儲存體帳戶或 Log Analytics 工作區時，您可以根據組織的需求來設定保留原則。 

- [如何設定 Azure 儲存體客戶紀錄的保留原則](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [變更 Log Analytics 中的資料保留期限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：若要查看 Azure 儲存體記錄，有一般的選項，例如透過 Log Analytics 供應專案查詢，以及直接查看記錄檔的唯一選項。 在 Azure 儲存體中，記錄檔會儲存在必須直接從http://accountname.blob.core.windows.net/$logs 存取的 blob 中（預設會隱藏記錄資料夾，因此您必須直接流覽。 它不會顯示在清單命令中） 

此外，為您的 Azure 儲存體帳戶啟用 [先進的威脅防護]。 Azure 儲存體的先進威脅防護提供一層額外的安全性情報，可偵測不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 當活動發生異常時，就會觸發安全性警示。 這些安全性警示會與 Azure 資訊安全中心整合，並透過電子郵件傳送給訂用帳戶系統管理員，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。 

- [記錄和審核資料](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [如何啟用先進的威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針：在 Azure 資訊安全中心中，為儲存體帳戶啟用 [先進的威脅防護]。 啟用儲存體帳戶的診斷設定，並將記錄傳送到 Log Analytics 工作區。 將您的 Log Analytics 工作區上架到 Azure Sentinel，因為它提供安全性協調流程自動化回應（攀升情況）解決方案。 這可讓您建立和使用腳本來修復安全性問題。 

- [如何上架 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何管理 Azure 資訊安全中心中的警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [如何對 log analytics 記錄資料發出警示](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Azure 儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反惡意程式碼記錄

**指導**方針：使用 Azure 安全性中心，並啟用 Azure 儲存體的威脅防護，以使用雜湊信譽分析和作用中 Tor 結束節點（匿名 proxy）的可疑存取來偵測惡意程式碼上傳至 Azure 儲存體。 

- [設定 Azure 儲存體的先進威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針： Azure 監視器中的 Azure DNS 分析（預覽）解決方案，將深入解析探索到 DNS 基礎結構中的安全性、效能和作業。 目前，這不支援 Azure 儲存體帳戶，不過您可以使用協力廠商 dns 記錄解決方案。 

- [收集搭配 DNS 分析預覽版解決方案使用 DNS 基礎結構的深入解析](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列審核記錄

**指導**方針：不適用;基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： Azure AD 具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。 

- [如何使用 PowerShell 取得 Azure AD 中的目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：變更預設密碼（若適用）

**指導**方針： Azure 儲存體帳戶或 Azure Active Directory 具有預設或空白密碼的概念。 Azure 儲存體會實行支援 Azure 角色型存取控制（RBAC）以及共用金鑰和共用存取簽章（SAS）的存取控制模型。 共用金鑰和 SAS 驗證的特性是沒有與呼叫者相關聯的身分識別，因此無法執行以許可權為基礎的安全性主體授權。 

- [授權存取 Azure 儲存體中的資料](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [瞭解 Azure 儲存體帳戶的安全性主體和存取控制](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：使用可存取您儲存體帳戶的專用系統管理帳戶，建立相關的標準操作程式。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。 

您也可以使用適用于 Microsoft 服務的 Azure AD Privileged Identity Management 特殊許可權角色和 Azure ARM，啟用僅限時間/僅限存取權。 

- [瞭解 Azure 資訊安全中心身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Privileged Identity Management 總覽](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入（SSO）

**指導**方針：盡可能使用 Azure Active Directory SSO，而不是設定個別服務的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。 

- [瞭解使用 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [授權存取 Azure 儲存體中的資料](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [使用 Azure Active Directory 授權存取 blob 和佇列](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取，請使用多重要素驗證。

**指引**：啟用 Azure Active Directory 多重要素驗證，並遵循 Azure 資訊安全中心身分識別和存取管理建議，以協助保護您的儲存體帳戶資源。 

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何監視 Azure 資訊安全中心內的身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦（特殊許可權存取工作站）進行所有系統管理工作

**指導**方針：使用 paw （特殊許可權存取工作站），並設定 MFA 來登入及設定儲存體帳戶資源。 

- [瞭解特殊許可權存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄和警示可疑活動

**指導**方針：將 Azure 資訊安全中心風險偵測警示傳送至 Azure 監視器，並使用動作群組來設定自訂警示/通知。 為 Azure 儲存體帳戶啟用 [先進的威脅防護]，以產生可疑活動的警示。 此外，您還可以使用 Azure AD 風險偵測，來查看警示和報告有風險的使用者行為。 

- [如何設定 Azure 儲存體帳戶的 Advanced 威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [瞭解 Azure AD 風險偵測](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [如何設定自訂警示和通知的動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用名為「位置」的條件式存取，只允許來自 IP 位址範圍或國家/地區特定邏輯群組的存取權。 

- [如何在 Azure 中設定命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory （Azure AD）做為中央驗證和授權系統。 Azure AD 提供了角色型存取控制 (RBAC)，供您更細微地控制用戶端對儲存體帳戶資源的存取。  盡可能使用 Azure AD 認證做為安全性最佳作法，而不是使用帳戶金鑰，這樣會更容易遭到入侵。 當您的應用程式設計需要共用存取簽章以存取 Blob 儲存體時，請使用 Azure AD 認證來建立使用者委派共用存取簽章（SAS），以獲得更高的安全性。

- [如何建立和設定 Azure AD 實例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [使用 Azure 儲存體資源提供者來存取管理資源](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [如何使用 Azure 入口網站中的 RBAC 來設定 Azure Blob 和佇列資料的存取權](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [授權存取 Azure 儲存體中的資料](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期審查並協調使用者存取

**指導**方針：請參閱 Azure Active Directory 記錄，以協助探索過時的帳戶，其中可能包含具有儲存體帳戶系統管理角色的帳戶。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、存取可用於存取儲存體帳戶資源的企業應用程式，以及角色指派。 您應該定期檢查使用者存取權，以確保只有適當的使用者可以繼續進行存取。  

您也可以使用共用存取簽章（SAS），為您的儲存體帳戶中的資源提供安全的委派存取權，而不會危及資料的安全性。 您可以控制用戶端可以存取的資源、它們對這些資源所擁有的許可權，以及 SAS 有效的時間長度，還有其他參數。

此外，請參閱對容器和 blob 的匿名讀取權限。 根據預設，容器和其中的任何 Blob 都只能由具有指定適當權限的使用者存取。 您可以使用 Azure 監視器，針對使用匿名驗證條件之儲存體帳戶的匿名存取發出警示。

降低 unsuspected 使用者帳戶存取風險的一個有效方法，就是限制您授與使用者的存取權持續時間。 限時的 SAS Uri 是一種有效的方式，可自動將儲存體帳戶的使用者存取權設為過期。 此外，經常輪替儲存體帳戶金鑰是一種方法，可確保透過儲存體帳戶金鑰的非預期存取時間長度受限。

- [瞭解 Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何在 Azure 儲存體帳戶層級中查看和變更存取權](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [管理對容器與 Blob 的匿名讀取權限。](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [在 Azure 入口網站中監視儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [管理儲存體帳戶存取金鑰](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視嘗試存取停用的帳戶

**指導**方針：使用儲存體分析，將有關成功和失敗要求的詳細資訊記錄到儲存體服務。 所有記錄都會儲存在名為 $logs 的容器內的區塊 Blob 中，該容器是在針對儲存體帳戶啟用儲存體分析時自動建立的。

建立 Azure Active Directory 使用者帳戶的診斷設定，將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。 若要監視對 Azure 儲存體帳戶的驗證失敗，您可以建立警示，在達到儲存體資源計量的特定閾值時通知您。 此外，請使用 Azure 監視器，以使用匿名驗證條件來對儲存體帳戶的匿名存取發出警示。

- [Azure 儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [如何將 Azure 活動記錄整合到 Azure 監視器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何設定 Azure 儲存體帳戶的計量警示](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory 的風險和身分識別保護功能，為偵測到與您儲存體帳戶資源相關的可疑動作設定自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行貴組織的安全性回應。 

- [如何觀看 Azure AD 有風險的登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何上架 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：提供 Microsoft 在支援案例期間存取相關的客戶資料

**指引**：在 Microsoft 需要存取客戶資料的支援案例中，客戶加密箱（儲存體帳戶的預覽）提供一個介面，供客戶審查及核准或拒絕客戶資料存取要求。 Microsoft 不需要，也不會要求存取儲存在儲存體帳戶內的組織密碼。

- [瞭解客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護機密資訊的清查

**指引**：使用標記來協助追蹤儲存或處理敏感資訊的儲存體帳戶資源。 

- [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感資訊的系統

**指導**方針：使用個別的訂用帳戶、管理群組，以及適用于環境、資料敏感度等個別安全性網域的儲存體帳戶來執行隔離。  您可以根據所使用的網路類型和子集，限制您的儲存體帳戶，以控制您的應用程式和企業環境所需的儲存體帳戶存取權層級。 設定網路規則時，只有透過一組指定網路要求資料的應用程式可以存取儲存體帳戶。 您可以透過 Azure AD RBAC 來控制對 Azure 儲存體的存取。 您也可以設定私用端點，以在您的虛擬網路與服務之間通過 Microsoft 骨幹網路的流量，來改善安全性，以避免公開網際網路的暴露。 

- [如何建立額外的 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [虛擬網路服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的機密資訊傳輸。

**指引**：對於儲存或處理敏感資訊的儲存體帳戶資源，請使用標記將資源標示為機密。 若要透過外泄降低資料遺失的風險，請使用 Azure 防火牆來限制 Azure 儲存體帳戶的輸出網路流量。 

此外，使用虛擬網路服務端點原則來篩選輸出虛擬網路流量，以透過服務端點 Azure 儲存體帳戶，並允許只將資料外泄到特定 Azure 儲存體帳戶。

- [設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Azure 儲存體的虛擬網路服務端點原則](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有機密資訊

**指引**：您可以藉由啟用儲存體帳戶所需的安全傳輸，強制使用 HTTPS。 啟用此選項後，使用 HTTP 的連線將被拒絕。 此外，請使用 Azure 資訊安全中心和 Azure 原則，為您的儲存體帳戶強制執行安全傳輸。

- [如何在 Azure 儲存體中要求安全傳輸](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [受資訊安全中心監視的 Azure 安全性原則](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用 active discovery 工具來識別敏感性資料

**指導**方針： Azure 儲存體帳戶和相關資源尚無法使用資料識別功能。 視需要執行協力廠商解決方案，以符合合規性目的。 

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針： Azure Active Directory （Azure AD）透過角色型存取控制（RBAC）來授權受保護資源的存取權限。 Azure 儲存體會定義一組內建的 RBAC 角色，其中包含用來存取 blob 或佇列資料的常用許可權集。 

- [如何為 Azure 儲存體帳戶指派 RBAC 角色](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)

- [使用 Azure 儲存體資源提供者來存取管理資源](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [如何使用 Azure 入口網站中的 RBAC 來設定 Azure Blob 和佇列資料的存取權](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [如何建立和設定 AAD 實例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [授權存取 Azure 儲存體中的資料](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用以主機為基礎的資料遺失防護來強制存取控制

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密機密資訊待用

**指導**方針：已針對所有儲存體帳戶啟用 Azure 儲存體加密，且無法停用。 當您的資料保存到雲端時，Azure 儲存體會自動將其加密。 當您從「Azure 儲存體」讀取資料時，「Azure 儲存體」會先將資料解密，然後才傳回資料。 Azure 儲存體加密可讓您保護待用資料，而不需要修改程式碼或將程式碼新增至任何應用程式。 

- [瞭解 Azure 儲存體待用加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：對重要 Azure 資源的變更進行記錄和警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對儲存體帳戶資源發生變更時，建立警示。 您也可以啟用 Azure 儲存體記錄，以追蹤每個對 Azure 儲存體所提出的要求是否已獲得授權。 這些記錄會指出是否以匿名方式進行要求、使用 OAuth 2.0 權杖、使用共用金鑰，或使用共用存取簽章（SAS）。 此外，請使用 Azure 監視器，以使用匿名驗證條件來對儲存體帳戶的匿名存取發出警示。

- [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Azure 儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [如何設定 Azure 儲存體帳戶的計量警示](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：遵循 Azure 資訊安全中心的建議，持續地審查並監視儲存體帳戶的設定。 

- [安全性建議-參考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較回溯弱點掃描

**指導**方針：不適用;Microsoft 會在支援儲存體帳戶的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程式來優先處理探索到的弱點。

**指導**方針：使用 Azure 資訊安全中心所提供的預設風險評等（安全分數）。 

- [瞭解 Azure 資訊安全中心安全分數](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢和探索訂用帳戶內的所有資源（包括儲存體帳戶）。 請確定您的租使用者中有適當的（讀取）許可權，而且能夠列舉所有的 Azure 訂用帳戶以及您訂用帳戶內的資源。 

- [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何查看您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [瞭解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至儲存體帳戶資源，以邏輯方式將它們組織成分類法。 

- [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**.. 適當地使用標記、管理群組和個別的訂用帳戶來組織和追蹤儲存體帳戶和相關資源。 定期協調清查，並確保未經授權的資源會及時從訂用帳戶中刪除。 

此外，請使用 Advanced 威脅防護來進行 Azure 儲存體，以偵測未經授權的 Azure 資源。 

- [如何建立額外的 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [設定 Azure 儲存體的先進威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准的 Azure 資源和軟體標題的清查。

**指引**：根據您的組織需求，您必須建立已核准 Azure 資源的清查。 


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：未核准的 Azure 資源的監視

**指引**：使用 Azure 原則來對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制： 

 - 不允許的資源類型 
 - 允許的資源類型 

此外，請使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 這有助於以高安全性為基礎的環境，例如具有儲存體帳戶的環境。 

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

**指導**方針：客戶可能會在客戶的公司原則要求時，防止資源建立或使用 Azure 原則。 

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用 Azure 原則來對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制： 

- 不允許的資源類型 
- 允許的資源類型 

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：執行核准的應用程式清單

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11：限制使用者透過腳本與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取來限制使用者與 Azure Resource Manager 的互動能力，方法是設定「Microsoft Azure 管理」應用程式的「封鎖存取」。 這可能會防止在高安全性環境（例如具有儲存體帳戶的資源）中建立和變更資源。 

- [如何設定條件式存取以封鎖對 ARM 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

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

**指引**：在 "Microsoft. Storage" 命名空間中使用 Azure 原則別名來建立自訂原則，以審核或強制執行儲存體帳戶實例的設定。 您也可以將內建的 Azure 原則定義用於 Azure 儲存體帳戶，例如： 

對儲存體帳戶進行不受限制的網路存取在儲存體帳戶上部署先進的威脅防護儲存體帳戶應遷移到新的 Azure Resource Manager 資源安全傳輸至儲存體帳戶應啟用 

使用來自 Azure 資訊安全中心的建議做為儲存體帳戶的安全設定基準。 

- [如何查看可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2：為您的作業系統建立安全設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：維護所有 Azure 資源的安全設定

**指導**方針：使用 Azure 原則 [拒絕] 和 [不存在時部署]，在您的儲存體帳戶資源上強制執行安全設定。 

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [了解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4：維護作業系統的安全設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指引**：使用 Azure Repos 安全地儲存和管理您的程式碼，例如自訂的 Azure 原則、Azure Resource Manager 範本、Desired State Configuration 腳本等。若要存取您在 Azure DevOps 中管理的資源，您可以將許可權授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory （Azure AD）中定義的群組（如果與 Azure DevOps 整合），或 Active Directory （如果與 TFS 整合）。

- [如何將程式碼儲存在 Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指引**：利用 Azure 原則來警示、審查及強制執行儲存體帳戶的系統組態。 此外，開發處理常式和管線來管理原則例外狀況。 

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

**指引**：利用 Azure 資訊安全中心來執行 Azure 儲存體帳戶資源的基準掃描。 

- [如何修復 Azure 資訊安全中心中的建議](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-securely-manage-azure-secrets"></a>7.11：安全地管理 Azure 秘密

**指導**方針：當您的資料保存到雲端時，Azure 儲存體會自動將其加密。 您可以使用 Microsoft 管理的金鑰來加密儲存體帳戶，也可以使用自己的金鑰來管理加密。 如果您使用客戶提供的金鑰，您可以利用 Azure Key Vault 安全地儲存金鑰。 

此外，請經常輪替儲存體帳戶金鑰，以限制遺失或洩漏儲存體帳戶金鑰的影響。

- [待用資料的 Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [管理儲存體帳戶存取金鑰](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-securely-and-automatically-manage-identities"></a>7.12：安全且自動管理身分識別

**指導**方針：使用 Azure Active Directory 和受控識別，授權存取 Azure 儲存體帳戶中的 blob 和佇列。 Azure Blob 和佇列儲存體支援使用 Azure 資源的受控識別來進行 Azure Active Directory (Azure AD) 驗證。 適用于 Azure 資源的受控識別可以使用 Azure 虛擬機器（Vm）中執行的應用程式、函式應用程式、虛擬機器擴展集和其他服務的 Azure AD 認證，來授權 blob 和佇列資料的存取權。 藉由使用適用于 Azure 資源的受控識別搭配 Azure AD authentication，您可以避免將認證儲存在雲端中執行的應用程式。 

- [如何使用受控識別授與 Azure blob 和佇列資料的存取權](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證暴露

**指導**方針：執行認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防護](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：不適用;這項建議適用于計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：使用威脅防護 Azure 儲存體來偵測惡意程式碼上傳，以使用雜湊信譽分析和來自作用中 Tor 結束節點（匿名 proxy）的可疑存取來 Azure 儲存體。 

您也可以在上傳至非計算的 Azure 資源之前預先掃描任何內容，例如 App Service、Data Lake Storage、Blob 儲存體等等，以符合您的組織需求。

- [設定 Azure 儲存體的先進威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確認反惡意程式碼軟體和簽章已更新

**指導**方針：不適用;這項建議適用于計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保週期性自動備份

**指導**方針：您 Microsoft Azure 儲存體帳戶中的資料一律會自動複寫，以確保持久性和高可用性。 「Azure 儲存體」會複製您的資料，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 您可以選擇在相同資料中心內、在相同地區的不同資料中心內、或跨不同區域複寫您的資料。 

您也可以啟用 Azure 自動化，以製作 blob 的一般快照集。

- [瞭解 Azure 儲存體的冗余和服務等級協定](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [建立 blob 的快照集](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure 自動化概觀](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針：若要從儲存體帳戶支援的服務備份資料，有多個可用的方法，包括使用 azcopy 或協力廠商工具。 Azure Blob 儲存體的固定儲存體可讓使用者以 WORM （一次寫入，多次讀取）狀態儲存業務關鍵資料物件。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。

- [開始使用 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [設定和管理 Blob 儲存體的不可變性原則](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

客戶管理/提供的金鑰可以在 Azure Key Vault 中使用 Azure CLI 或 PowerShell 來進行備份。 

- [如何在 Azure 中備份金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：使用下列 PowerShell 命令，定期執行 Key Vault 憑證、金鑰、受管理的儲存體帳戶和密碼的資料還原： 

還原-AzKeyVaultCertificate 還原-AzKeyVaultKey 還原-AzKeyVaultManagedStorageAccount 還原-AzKeyVaultSecret 

- [如何還原 Key Vault 憑證](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [如何還原 Key Vault 金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [如何還原 Key Vault 受控儲存體帳戶](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [如何還原 Key Vault 秘密](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 blob、檔案和資料表資料](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

注意：如果您想要將資料複製到您的 Azure 資料表儲存體服務，然後再安裝 AzCopy 版本7.3。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指引**：若要在儲存體帳戶上啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存您的金鑰。 您必須同時啟用「虛刪除」和「不要清除」金鑰保存庫的屬性。 Key Vault 的虛刪除功能可復原已刪除的保存庫和保存庫物件，例如金鑰、秘密和憑證。 如果將儲存體帳戶資料備份到 Azure 儲存體 blob，請啟用虛刪除，以在 blob 或 blob 快照集遭到刪除時儲存並復原您的資料。 您應該將備份視為機密資料，並套用相關的存取和資料保護控制項做為此基準的一部分。 此外，為了改善保護，您可以將業務關鍵資料物件儲存在 WORM （一次寫入，多次讀取）狀態。

- [如何使用 Azure Key Vault 的虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Azure 儲存體 Blob 的虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [使用不可變的儲存體儲存業務關鍵的 blob 資料](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為您的組織建立事件回應指南。 請確定有寫入的事件回應計畫，可定義人員的所有角色，以及從偵測到事件處理/管理的階段，以進行後續事件審查。

- [建立您自己的安全性事件回應程式的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [客戶也可以利用 NIST 的「電腦安全性性」事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程式

**指導**方針：資訊安全中心指派每個警示的嚴重性，以協助您排定應先調查哪些警示。 嚴重性是根據資訊安全中心在尋找中的信心，或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級。 

此外，請清楚地標示訂閱（例如， 生產、非生產）：使用標記並建立命名系統，以清楚地識別和分類 Azure 資源，特別是處理敏感性資料的人員。 您必須負責根據發生事件的 Azure 資源和環境的重要性排定警示補救的優先順序。

- [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程式

**指導**方針：執行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 識別弱式點和間距，並視需要修訂計畫。

- [NIST 的發行指南，適用于 IT 計畫和功能的測試、訓練和練習計畫](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指導**方針：如果 Microsoft 安全性回應中心（MSRC）發現您的資料已由非法或未經授權的合作物件存取，microsoft 將會使用安全性事件連絡人資訊來與您聯繫。 檢查事實後的事件，以確保解決問題。

- [如何設定 Azure 資訊安全中心安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示納入事件回應系統中

**指引**：使用「連續匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」來自動觸發安全性警示和建議的回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1：進行 Azure 資源的定期滲透測試

**指導**方針：遵循 Microsoft Engagement 規則，確保您的滲透測試不會違反 microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的 Red 小組和即時網站滲透測試策略和執行。

- [Engagement 的滲透測試規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全性基準測試](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 深入瞭解[Azure 安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
