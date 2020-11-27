---
title: 適用于 Azure 儲存體的 Azure 安全性基準
description: 適用于 Azure 儲存體的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 29e72f8358591614489be2731d16e89428ef388b
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301364"
---
# <a name="azure-security-baseline-for-azure-storage"></a>適用于 Azure 儲存體的 Azure 安全性基準

適用于 Azure 儲存體的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](../../security/benchmarks/security-control-network-security.md) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導** 方針：設定儲存體帳戶的防火牆，方法是限制存取特定公用 IP 位址範圍的用戶端、選取虛擬網路 (vnet) on azure 或特定 Azure 資源。 您也可以設定私人端點，讓來自企業之儲存體服務的流量可專門透過私人網路進行傳輸。

注意：傳統儲存體帳戶不支援防火牆和虛擬網路。

- [如何設定 Azure 儲存體防火牆](./storage-network-security.md#change-the-default-network-access-rule)

- [如何設定 Azure 儲存體的私人端點](./storage-private-endpoints.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2：監視和記錄 Vnet、子網和 NIC 設定和流量

**指導** 方針： Azure 儲存體提供多層式安全性模型。 您可以限制只有源自所指定 IP 位址、IP 範圍或來自 Azure 虛擬網路 (VNet) 中子網路清單的要求，才能存取您的儲存體帳戶。 您可以使用 Azure 資訊安全中心，並遵循網路保護建議來協助保護 Azure 中的網路資源。 此外，透過儲存體帳戶防火牆為儲存體帳戶設定的虛擬網路/子網啟用 NSG 流量記錄，並將記錄傳送至儲存體帳戶以進行流量審核。 

請注意，如果您將私人端點附加至儲存體帳戶，就無法設定網路安全性群組 (NSG) 子網的規則。 

- [設定 Azure 儲存體防火牆和虛擬網路](./storage-network-security.md)

- [如何啟用 NSG 流量記錄](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../../security-center/security-center-network-recommendations.md)

- [瞭解 Azure 儲存體的私人端點](./storage-private-endpoints.md#known-issues)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：不適用;建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**：為您的 Azure 儲存體帳戶啟用先進的威脅防護。 Azure 儲存體的「先進威脅防護」提供額外一層的安全性情報，可偵測到不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 Azure 資訊安全中心整合式警示是以與已成功解決的 IP 位址相關聯的活動為基礎，而 IP 位址是否為已知的具風險 IP 位址 (例如，已知的 cryptominer) 或先前無法辨識為有風險的 IP 位址。 出現異常活動時，就會觸發安全性警示。 

- [如何啟用先進的威脅防護](./azure-defender-storage-configure.md?tabs=azure-portal)

- [了解 Azure 資訊安全中心的整合式威脅情報](../../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導** 方針：網路監看員封包捕獲可讓您建立抓取會話，以追蹤儲存體帳戶與虛擬機器之間的流量。 系統會為擷取工作階段提供篩選器，以確保您只會擷取到您想要的流量。 封包擷取有助於以被動和主動方式診斷網路異常。 其他用途包括收集網路統計資料、取得有關網路入侵的資訊，以及偵錯用戶端與伺服器間的通訊等等。 由於能夠從遠端觸發封包擷取，因此可以減輕在所需的虛擬機器上手動執行封包擷取的工作負擔，進而省下寶貴的時間。 

- [使用入口網站以 Azure 網路監看員管理封包擷取](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6：部署網路型入侵偵測/入侵防護系統

**指導**： Azure 儲存體的 Advanced 威脅防護提供額外一層的安全性情報，以偵測存取或惡意探索儲存體帳戶的異常和可能有害嘗試。 出現異常活動時，就會觸發安全性警示。 這些安全性警示會與 Azure 資訊安全中心整合，也會透過電子郵件傳送給訂用帳戶系統管理員，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。 

- [設定 Azure 儲存體的 advanced 威脅防護](./azure-defender-storage-configure.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7：管理 web 應用程式的流量

**指導** 方針：不適用;建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針：對於需要存取儲存體帳戶的虛擬網路中的資源，請針對設定的虛擬網路使用虛擬網路服務標籤，以定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如儲存體) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。 

當需要將網路存取範圍設定為特定的儲存體帳戶時，請使用虛擬網路服務端點原則。

- [如需使用服務標記的詳細資訊](../../virtual-network/service-tags-overview.md)

- [如需 Azure 儲存體的虛擬網路服務端點原則的詳細資訊](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：服務

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：為與您的 Azure 儲存體帳戶建立關聯 Azure 原則的網路資源，定義並執行標準安全性設定。 使用 "Microsoft. Storage" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對儲存體帳戶資源的網路設定進行審核或強制執行。 

您也可以使用與儲存體帳戶相關的內建原則定義，例如：儲存體帳戶應該使用虛擬網路服務端點 

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [儲存體的 Azure 原則範例](../../governance/policy/samples/built-in-policies.md#storage)

- [適用于網路的 Azure 原則範例](../../governance/policy/samples/built-in-policies.md#network)

- [如何建立 Azure 藍圖](../../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

**指導** 方針：將標記用於網路安全性群組 (NSG) 以及與網路安全性和流量相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。 使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。 您可使用 Azure PowerShell 或 Azure CLI，根以據資源的標籤對資源進行查詢或執行動作。 

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

- [如何建立虛擬網路](../../virtual-network/quick-create-portal.md)

- [如何建立具有安全性設定的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 原則來記錄網路資源的設定變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。 

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何在 Azure 監視器中建立警示](../../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](../../security/benchmarks/security-control-logging-monitoring.md) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-resource"></a>2.1：使用已核准的時間同步處理資源

**指導** 方針：不適用;Microsoft 會維護 Azure 儲存體帳戶的時間來源。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區 (s) 來查詢和執行分析，並將 Azure 儲存體帳戶用於長期/封存儲存體，並選擇性地使用不可變的儲存體和強制保留保留的安全性功能。

- [如何使用 Azure 監視器收集平臺記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的審核記錄

**指導** 方針： Azure 儲存體分析提供 blob、佇列和資料表的記錄。 您可以使用 Azure 入口網站來設定要為您的客戶紀錄哪些記錄。 

- [如何為您的 Azure 儲存體帳戶設定監視](./storage-monitor-storage-account.md#configure-monitoring-for-a-storage-account)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄檔

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄檔存放區保留

**指導** 方針：將安全性事件記錄檔儲存在 Azure 儲存體帳戶或 Log Analytics 工作區時，您可以根據組織的需求來設定保留原則。 

- [如何設定 Azure 儲存體客戶紀錄的保留原則](./storage-monitor-storage-account.md#configure-logging)

- [變更 Log Analytics 中的資料保留期限](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** 方針：若要檢查 Azure 儲存體記錄，有一些常用的選項，例如透過 Log Analytics 供應專案查詢，以及直接查看記錄檔的唯一選項。 在 Azure 儲存體中，記錄檔會儲存在必須直接存取的 blob 中 `http://accountname.blob.core.windows.net/$logs` (記錄資料夾預設為隱藏，因此您必須直接流覽。 它不會顯示在清單命令中)  

此外，針對您的 Azure 儲存體帳戶啟用 Advanced 威脅防護。 Azure 儲存體的「先進威脅防護」提供額外一層的安全性情報，可偵測到不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 出現異常活動時，就會觸發安全性警示。 這些安全性警示會與 Azure 資訊安全中心整合，也會透過電子郵件傳送給訂用帳戶系統管理員，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。 

- [記錄和審核資料](./storage-analytics-logging.md#how-logs-are-stored)

- [如何啟用先進的威脅防護](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導** 方針：在 Azure 資訊安全中心中，為儲存體帳戶啟用 Advanced 威脅防護。 啟用儲存體帳戶的診斷設定，並將記錄傳送至 Log Analytics 工作區。 請將 Log Analytics 工作區在 Azure Sentinel 上線，因為其可提供安全性協調流程自動化回應 (SOAR) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。 

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

- [如何在 Azure 資訊安全中心中管理警示](../../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../../azure-monitor/learn/tutorial-response.md)

- [Azure 儲存體分析記錄](./storage-analytics-logging.md) (部分機器翻譯)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反惡意程式碼記錄

**指導** 方針：使用 Azure 安全性中心並啟用威脅防護，以使用「雜湊信譽分析」和「使用 Tor 結束節點的可疑存取」 (匿名 proxy) ，來偵測惡意程式碼上傳至 Azure 儲存體的 Azure 儲存體。 

- [設定 Azure 儲存體的 advanced 威脅防護](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針： Azure 監視器中的 Azure DNS 分析 (預覽版) 解決方案會收集安全性、效能和作業的 DNS 基礎結構見解。 目前，這並不支援 Azure 儲存體帳戶，不過您可以使用協力廠商 dns 記錄解決方案。 

- [收集搭配 DNS 分析預覽版解決方案使用 DNS 基礎結構的深入解析](../../azure-monitor/insights/dns-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列審核記錄

**指導** 方針：不適用;基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../../security/benchmarks/security-control-identity-access-control.md) (機器翻譯)。

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： Azure AD 有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。 

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure 儲存體的帳戶或 Azure Active Directory 具有預設或空白密碼的概念。 Azure 儲存體會將支援 Azure 角色型存取控制的存取控制模型， (Azure RBAC) 以及 (SAS) 的共用金鑰和共用存取簽章。 共用金鑰和 SAS 驗證的特性是，沒有與呼叫端相關聯的身分識別，因此無法執行安全性主體許可權型授權。 

- [授權存取 Azure 儲存體中的資料](./storage-auth.md)

- [瞭解 Azure 儲存體帳戶的安全性主體和存取控制](./storage-introduction.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：使用可存取儲存體帳戶的專用系統管理帳戶來建立標準作業程式。 使用 Azure 資訊安全中心身分識別與存取管理來監視系統管理帳戶的數目。 

您也可以使用適用于 Microsoft 服務和 Azure ARM 的 Azure AD Privileged Identity Management 特殊許可權角色，來啟用及時且足夠的存取權。 

- [瞭解 Azure 資訊安全中心身分識別和存取權](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management 總覽](../../active-directory/privileged-identity-management/index.yml)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一 Sign-On (SSO) 

**指導** 方針：可能的話，請使用 Azure Active Directory SSO，而不是針對個別服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。 

- [瞭解 Azure AD 的 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [授權存取 Azure 儲存體中的資料](./storage-auth.md)

- [使用 Azure Active Directory 授權存取 blob 和佇列](./storage-auth-aad.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證。

**指導** 方針：啟用 Azure Active Directory 多重要素驗證，並遵循 Azure 資訊安全中心身分識別和存取管理的建議，以協助保護您的儲存體帳戶資源。 

- [如何在 Azure 中啟用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：針對所有系統管理工作使用專用電腦 (特殊許可權的存取工作站) 

**指導** 方針：使用 paw (特殊許可權存取工作站) 設定 MFA 以登入和設定儲存體帳戶資源。 

- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄和警示可疑活動

**指導** 方針：將 Azure 資訊安全中心風險偵測警示傳送至 Azure 監視器，並使用動作群組設定自訂警示/通知。 啟用 Azure 儲存體帳戶的 Advanced 威脅防護，以產生可疑活動的警示。 此外，使用 Azure AD 風險偵測來查看警示和報告有風險的使用者行為。 

- [如何設定 Azure 儲存體帳戶的 Advanced 威脅防護](./azure-defender-storage-configure.md)

- [了解 Azure AD 風險偵測](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定自訂警示和通知的動作群組](../../azure-monitor/platform/action-groups.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。 

- [如何在 Azure 中設定命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure 提供 Azure 角色型存取控制 (的 Azure RBAC) ，可讓您更精細地控制用戶端對儲存體帳戶中資源的存取權。  盡可能使用 Azure AD 認證作為安全性最佳做法，而不是使用帳戶金鑰，這可能更容易遭到入侵。 當您的應用程式設計需要共用存取簽章以存取 Blob 儲存體時，請使用 Azure AD 認證來建立使用者委派共用存取簽章 (SAS) 以獲得較佳的安全性。

- [如何建立和設定 Azure AD 執行個體](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [使用 Azure 儲存體資源提供者存取管理資源](./authorization-resource-provider.md)

- [如何在 Azure 入口網站中使用 Azure RBAC 設定 Azure Blob 和佇列資料的存取權](./storage-auth-aad-rbac-portal.md)

- [授權存取 Azure 儲存體中的資料](./storage-auth.md)

- [使用 (SAS) 的共用存取簽章來授與 Azure 儲存體資源的有限存取權 ](./storage-sas-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期審核和協調使用者存取

**指導** 方針：請參閱 Azure Active Directory 記錄，以協助探索可包含具有儲存體帳戶系統管理角色的過時帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、存取可用於存取儲存體帳戶資源的企業應用程式，以及角色指派。 您應定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。  

您也可以使用 (SAS) 的共用存取簽章，為您的儲存體帳戶中的資源提供安全的委派存取，而不會危害資料的安全性。 您可以控制用戶端可能存取的資源、這些資源上的許可權，以及 SAS 的有效時間長度，以及其他參數。

此外，請檢查容器和 blob 的匿名讀取權限。 根據預設，容器和其中的任何 Blob 都只能由具有指定適當權限的使用者存取。 您可以使用 Azure 監視器，透過匿名驗證條件來對儲存體帳戶進行匿名存取的警示。

降低 unsuspected 使用者帳戶存取風險的一個有效方法是限制您授與使用者的存取持續時間。 有時間限制的 SAS Uri 是自動讓使用者存取儲存體帳戶到期的一種有效方法。 此外，經常輪替儲存體帳戶金鑰是一種方式，可確保透過儲存體帳戶金鑰的非預期存取期限有限。

- [瞭解 Azure AD 報告](../../active-directory/reports-monitoring/index.yml)

- [如何在 Azure 儲存體帳戶層級上查看和變更存取權](./storage-auth-aad-rbac-portal.md)

- [使用 (SAS) 的共用存取簽章來授與 Azure 儲存體資源的有限存取權 ](./storage-sas-overview.md)

- [管理對容器與 Blob 的匿名讀取權限。](../blobs/anonymous-read-access-configure.md)

- [在 Azure 入口網站中監視儲存體帳戶](./storage-monitor-storage-account.md)

- [管理儲存體帳戶存取金鑰](./storage-account-keys-manage.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視嘗試存取已停用的帳戶

**指導** 方針：使用儲存體分析，將有關成功和失敗要求的詳細資訊記錄到儲存體服務。 所有記錄都會儲存在名為 $logs 的容器內的區塊 Blob 中，該容器是在針對儲存體帳戶啟用儲存體分析時自動建立的。

針對 Azure Active Directory 使用者帳戶建立診斷設定，將審核記錄和登入記錄傳送至 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。 若要監視 Azure 儲存體帳戶的驗證失敗，您可以建立警示，以在達到儲存體資源計量的特定臨界值時通知您。 此外，您也可以使用 Azure 監視器，透過匿名驗證條件來對儲存體帳戶進行匿名存取的警示。

- [Azure 儲存體分析記錄](./storage-analytics-logging.md) (部分機器翻譯)

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何設定 Azure 儲存體帳戶的計量警示](./storage-monitor-storage-account.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導** 方針：使用 Azure Active Directory 的風險和身分識別保護功能，對偵測到的儲存體帳戶資源相關的可疑動作設定自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行您組織的安全性回應。 

- [如何檢視有風險的 Azure AD 登入](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定和啟用身分識別保護風險原則](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：在 Microsoft 需要存取客戶資料的支援案例中，客戶加密箱 (Preview 儲存體帳戶) 提供一個介面，讓客戶可以檢查及核准或拒絕客戶資料存取要求。 Microsoft 不會要求，也不會要求存取您組織儲存在儲存體帳戶內的秘密。

- [瞭解客戶加密箱](../../security/fundamentals/customer-lockbox-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](../../security/benchmarks/security-control-data-protection.md) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導** 方針：使用標籤來協助追蹤儲存或處理敏感性資訊的儲存體帳戶資源。 

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：使用個別的訂用帳戶、管理群組和儲存體帳戶，為個別的安全性網域（例如環境、資料敏感度）實行隔離。  您可以根據所使用的網路類型和子集來限制儲存體帳戶，以控制您的應用程式和企業環境所需的儲存體帳戶存取層級。 設定網路規則時，只有透過一組指定網路要求資料的應用程式才能存取儲存體帳戶。 您可以透過 Azure RBAC 控制對 Azure 儲存體的存取。 您也可以設定私人端點來改善安全性，因為您的虛擬網路與服務之間的流量會流經 Microsoft 骨幹網路，以消除公用網際網路的風險。 

- [如何建立額外的 Azure 訂閱](../../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

- [設定 Azure 儲存體防火牆和虛擬網路](./storage-network-security.md)

- [虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸。

**指導** 方針：針對儲存或處理敏感性資訊的儲存體帳戶資源，請使用標記將資源標示為機密。 若要透過遭到外泄減少資料遺失的風險，請使用 Azure 防火牆限制 Azure 儲存體帳戶的輸出網路流量。 

此外，使用虛擬網路服務端點原則來篩選輸出虛擬網路流量，以透過服務端點 Azure 儲存體帳戶，並且只允許特定 Azure 儲存體帳戶的資料遭到外泄。

- [設定 Azure 儲存體防火牆和虛擬網路](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Azure 儲存體的虛擬網路服務端點原則](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [瞭解 Azure 中的客戶資料保護](../../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：您可以啟用儲存體帳戶所需的安全傳輸，來強制使用 HTTPS。 啟用此選項後，使用 HTTP 的連線將被拒絕。 此外，請使用 Azure 資訊安全中心和 Azure 原則來強制執行儲存體帳戶的安全傳輸。

- [如何在 Azure 儲存體中要求安全傳輸](./storage-require-secure-transfer.md)

- [由安全性中心監視的 Azure 安全性原則](../../security-center/policy-reference.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure 儲存體帳戶和相關資源尚無法使用資料識別功能。 若需要達到合規性目標，請實作協力廠商解決方案。 

- [瞭解 Azure 中的客戶資料保護](../../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針： Azure Active Directory (Azure AD) 透過 azure RBAC (的 azure 角色型存取控制，來授與受保護資源的存取權限。 Azure 儲存體會定義一組 Azure 內建角色，其中包含用來存取 blob 或佇列資料的一般許可權集。 

- [如何為 Azure 儲存體帳戶指派 Azure 角色](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [使用 Azure 儲存體資源提供者存取管理資源](./authorization-resource-provider.md)

- [如何在 Azure 入口網站中使用 Azure RBAC 設定 Azure Blob 和佇列資料的存取權](./storage-auth-aad-rbac-portal.md)

- [如何建立和設定 AAD 執行個體](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [授權存取 Azure 儲存體中的資料](./storage-auth.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的機密資訊

**指導** 方針：已為所有儲存體帳戶啟用 Azure 儲存體加密，且無法停用。 Azure 儲存體將資料保存到雲端時，會自動將您的資料加密。 當您從「Azure 儲存體」讀取資料時，「Azure 儲存體」會先將資料解密，然後才傳回資料。 Azure 儲存體加密可讓您保護待用資料，而不需要修改程式碼或將程式碼新增至任何應用程式。 

- [瞭解靜態加密 Azure 儲存體](./storage-service-encryption.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄來建立儲存體帳戶資源發生變更時的警示。 您也可以啟用 Azure 儲存體記錄，以追蹤每個對 Azure 儲存體的要求授權。 這些記錄會指出是以匿名方式、使用 OAuth 2.0 權杖、使用共用金鑰，或使用共用存取簽章 (SAS) 來進行要求。 此外，您也可以使用 Azure 監視器，透過匿名驗證條件來對儲存體帳戶進行匿名存取的警示。

- [如何建立 Azure 活動記錄事件的警示](../../azure-monitor/platform/alerts-activity-log.md)

- [Azure 儲存體分析記錄](./storage-analytics-logging.md) (部分機器翻譯)

- [如何設定 Azure 儲存體帳戶的計量警示](./storage-monitor-storage-account.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](../../security/benchmarks/security-control-vulnerability-management.md) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：遵循 Azure 資訊安全中心的建議，持續地審核和監視儲存體帳戶的設定。 

- [安全性建議 - 參考指南](../../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較回溯弱點掃描

**指導** 方針：不適用;Microsoft 會在支援儲存體帳戶的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序。

**指導** 方針：使用 (Azure 資訊安全中心提供的安全分數) 的預設風險評等。 

- [瞭解 Azure 資訊安全中心安全分數](../../security-center/secure-score-security-controls.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](../../security/benchmarks/security-control-inventory-asset-management.md) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指導** 方針：使用 Azure Resource Graph 來查詢及探索所有資源， (包括訂用帳戶 () 中的儲存體帳戶) 。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。 

- [如何使用 Azure Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：將標記套用至儲存體帳戶資源，以邏輯方式將這些資料組織成分類法。 

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（如果適當）來組織和追蹤儲存體帳戶和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。 

此外，請針對 Azure 儲存體使用 Advanced 威脅防護，以偵測未經授權的 Azure 資源。 

- [如何建立額外的 Azure 訂閱](../../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

- [設定 Azure 儲存體的 advanced 威脅防護](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查。

**指導** 方針：您將需要根據組織的需求建立已核准 Azure 資源的清查。 


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未核准的 Azure 資源

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制： 

 - 不允許的資源類型 
 - 允許的資源類型 

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。 這有助於以高安全性為基礎的環境，例如具有儲存體帳戶的環境。 

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：在計算資源內監視未核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

**指導** 方針：客戶可能會在客戶的公司原則要求時，防止資源建立或使用 Azure 原則。 

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制： 

- 不允許的資源類型 
- 允許的資源類型 

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11：限制使用者透過腳本與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。 這可防止在高安全性環境中建立和變更資源，例如具有儲存體帳戶的資源。 

- [如何設定條件式存取以封鎖對 ARM 的存取](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行腳本的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實體或邏輯上隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](../../security/benchmarks/security-control-secure-configuration.md) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 "Microsoft. Storage" 命名空間中 Azure 原則別名來建立自訂原則，以對儲存體帳戶實例的設定進行審核或強制執行。 您也可以針對 Azure 儲存體帳戶使用內建的 Azure 原則定義，例如： 

稽核不受限制的儲存體帳戶網路存取  
在儲存體帳戶部署進階威脅防護  
儲存體帳戶應移轉至新的 Azure Resource Manager 資源  
應啟用儲存體帳戶的安全傳輸  

使用 Azure 資訊安全中心中的建議作為儲存體帳戶的安全設定基準。 

- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2：建立作業系統的安全設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：維護所有 Azure 資源的安全設定

**指導** 方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在]，在您的儲存體帳戶資源上強制執行安全設定。 

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure 原則效果](../../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4：維護作業系統的安全設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：使用 Azure Repos 安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本、Desired State Configuration 腳本等。若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統建構管理工具

**指導** 方針：利用 Azure 原則來警示、審核和強制執行儲存體帳戶的系統組態。 此外，開發流程和管線以管理原則例外狀況。 

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

**指導** 方針：利用 Azure 資訊安全中心為您的 Azure 儲存體帳戶資源執行基準掃描。 

- [如何修復 Azure 資訊安全中心中的建議](../../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-securely-manage-azure-secrets"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：當您將資料保存到雲端時，Azure 儲存體會自動加密資料。 您可以使用 Microsoft 管理的金鑰來加密儲存體帳戶，也可以使用自己的金鑰管理加密。 如果您使用客戶提供的金鑰，您可以利用 Azure Key Vault 安全地儲存金鑰。 

此外，請定期輪替儲存體帳戶金鑰，以限制遺失或洩漏儲存體帳戶金鑰的影響。

- [待用資料的 Azure 儲存體加密](./storage-service-encryption.md)

- [管理儲存體帳戶存取金鑰](./storage-account-keys-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-securely-and-automatically-manage-identities"></a>7.12：安全且自動管理身分識別

**指導** 方針：在具有 Azure Active Directory 和受控識別的 Azure 儲存體帳戶中授權對 blob 和佇列的存取。 Azure Blob 和佇列儲存體支援使用 Azure 資源的受控識別來進行 Azure Active Directory (Azure AD) 驗證。 適用于 Azure 資源的受控識別可以從 Azure 虛擬機器中執行的應用程式（ (Vm) 、函式應用程式、虛擬機器擴展集和其他服務），使用 Azure AD 認證來授權存取 blob 和佇列資料。 藉由使用適用于 Azure 資源的受控識別搭配 Azure AD authentication，您可以避免將認證儲存在雲端中執行的應用程式。 

- [如何使用受控識別授與 Azure blob 和佇列資料的存取權](./storage-auth-aad-rbac-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../../security/benchmarks/security-control-malware-defense.md) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：使用 Azure 儲存體的威脅防護來偵測惡意程式碼上傳至 Azure 儲存體使用雜湊信譽分析和可疑存取從 active Tor 結束節點 (匿名 proxy) 。 

您也可以在上傳至非計算 Azure 資源（例如 App Service、Data Lake Storage、Blob 儲存體等）之前，預先掃描任何內容的內容，以符合您的組織需求。

- [設定 Azure 儲存體的 advanced 威脅防護](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確定已更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](../../security/benchmarks/security-control-data-recovery.md) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份 Ups

**指導** 方針：您 Microsoft Azure 儲存體帳戶中的資料一律會自動進行複寫，以確保持久性和高可用性。 「Azure 儲存體」會複製您的資料，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 您可以選擇在相同資料中心內、在相同地區的不同資料中心內、或跨不同區域複寫您的資料。 

您也可以啟用 Azure 自動化，以取得 blob 的一般快照集。

- [瞭解 Azure 儲存體的冗余和 Service-Level 協定](./storage-redundancy.md)

- [建立 blob 的快照集](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure 自動化概觀](../../automation/automation-intro.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：為了從儲存體帳戶支援的服務備份資料，有多種方法可供使用，包括使用 azcopy 或協力廠商工具。 Azure Blob 儲存體的固定儲存體可讓使用者以 WORM (一次寫入，多次讀取) 狀態儲存業務關鍵資料。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。

- [開始使用 AzCopy](./storage-use-azcopy-v10.md)

- [設定和管理 Blob 儲存體的不變性原則](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

客戶管理/提供的金鑰可以在 Azure Key Vault 中使用 Azure CLI 或 PowerShell 進行備份。 

- [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：使用下列 PowerShell 命令，定期執行 Key Vault 憑證、金鑰、受控儲存體帳戶和密碼的資料還原： 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [如何還原 Key Vault 憑證](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [如何還原 Key Vault 金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [如何還原 Key Vault 受控儲存體帳戶](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [如何還原 Key Vault 秘密](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [AzCopy 是一種命令列公用程式，可讓您用來將 blob、檔案和資料表資料複製到儲存體帳戶或從中複製](./storage-use-azcopy-v10.md)

注意：如果您想要將資料複製到您的 Azure 表格儲存體服務或從中複製資料，請安裝 AzCopy 7.3 版。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：若要在儲存體帳戶上啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存您的金鑰。 您必須同時啟用虛刪除和不清除金鑰保存庫的屬性。 Key Vault 的虛刪除功能可復原已刪除的保存庫和保存庫物件，例如金鑰、秘密和憑證。 如果您要將儲存體帳戶資料支援 Azure 儲存體 blob，請在刪除 blob 或 blob 快照集時，啟用虛刪除來儲存及復原您的資料。 您應該將備份視為機密資料，並將相關的存取和資料保護控制項套用為此基準的一部分。 此外，為了改善保護，您可以將業務關鍵資料物件儲存在 WORM (寫一次，以讀取許多) 狀態。

- [如何使用 Azure Key Vault 的虛刪除](../../key-vault/general/key-vault-recovery.md)

- [Azure 儲存體 Blob 的虛刪除](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [使用不可變儲存體儲存業務關鍵 Blob 資料](../blobs/storage-blob-immutable-storage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](../../security/benchmarks/security-control-incident-response.md) (機器翻譯)。

### <a name="101-create-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程式

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。 

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。 您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](../../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程式

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

- [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](../../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1：進行 Azure 資源的定期滲透測試

**指導** 方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../../security/benchmarks/overview.md) (機器翻譯)
- 深入了解 [Azure 安全性基準](../../security/benchmarks/security-baselines-overview.md) (機器翻譯)