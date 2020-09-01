---
title: 適用于 HDInsight 的 Azure 安全性基準
description: 適用于 HDInsight 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7315ef68b3562a28ee2515077c1e7d2f23c3803f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230427"
---
# <a name="azure-security-baseline-for-hdinsight"></a>適用于 HDInsight 的 Azure 安全性基準

適用于 HDInsight 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導**方針： Azure HDInsight 的周邊安全性可透過虛擬網路來達成。 企業系統管理員可以在虛擬網路內建立叢集，並使用 (NSG) 的網路安全性群組來限制對虛擬網路的存取。 只有連入網路安全性群組規則中允許的 IP 位址可以與 Azure HDInsight 叢集通訊。 此設定可提供周邊安全性。 在虛擬網路中部署的所有叢集也會有一個私人端點，可解析為虛擬網路內的私人 IP 位址，以供對叢集閘道進行私用 HTTP 存取。

若要透過遭到外泄減少資料遺失的風險，請使用 Azure 防火牆限制 Azure HDInsight 叢集的輸出網路流量。

如何在虛擬網路內部署 Azure HDInsight，並使用網路安全性群組來保護： https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

如何使用 Azure 防火牆限制 Azure HDInsight 叢集的輸出流量： https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導**方針：使用 Azure 資訊安全中心，並針對用來保護您的 Azure HDInsight 叢集的虛擬網路、子網和網路安全性群組，修復網路保護建議。 啟用網路安全性群組 (NSG) 流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送到 Azure Log Analytics 工作區，並使用 Azure 流量分析來提供 Azure 雲端中流量的深入解析。 Azure 流量分析的一些優點是能夠將網路活動視覺化、找出作用點、找出安全性威脅、瞭解流量流程模式，以及找出網路錯誤設定。

如何啟用 NSG 流量記錄： 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用及使用 Azure 流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

瞭解 Azure 資訊安全中心所提供的網路安全性：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；基準測試適用於裝載 Web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導**方針：若要防範 DDoS 攻擊，請在部署 Azure HDInsight 的虛擬網路上啟用 Azure DDoS 標準保護。 使用 Azure 資訊安全中心整合式威脅情報，以拒絕與已知惡意或未使用的網際網路 IP 位址的通訊。

如何設定 DDoS 保護： 

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

瞭解 Azure 資訊安全中心整合威脅情報：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：啟用網路安全性群組 (NSG) flog 記錄檔，以連接到用來保護 Azure HDInsight 叢集的子網 NSG。 將 NSG 流量記錄檔記錄到 Azure 儲存體帳戶以產生流程記錄。 如果需要調查異常活動，請啟用 Azure 網路監看員封包捕獲。

如何啟用 NSG 流量記錄： 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用網路監看員：

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導**方針：需求可符合 Azure 安全性控制識別碼 1.1;將 Azure HDInsight 叢集部署到虛擬網路，並使用 (NSG) 的網路安全性群組來保護。

需要輸入流量的 Azure HDInsight 有幾個相依性。 輸入的管理流量不能透過防火牆裝置傳送。 已知和發佈必要管理流量的來源位址。 使用此資訊建立網路安全性群組規則，以允許來自受信任位置的流量，並保護對叢集的輸入流量。

若要透過遭到外泄減少資料遺失的風險，請使用 Azure 防火牆限制 Azure HDInsight 叢集的輸出網路流量。

如何在虛擬網路內部署 HDInsight，並使用網路安全性群組來保護： https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

瞭解 HDInsight 相依性和防火牆使用方式： https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight 管理 IP 位址： https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；基準測試適用於裝載 Web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組上的網路存取控制 (NSG) 連接到您的 Azure HDInsight 叢集部署所在的子網。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

瞭解和使用服務標記進行 Azure HDInsight：

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：定義和執行與您的 Azure HDInsight 叢集相關之網路資源的標準安全性設定。 使用 "Microsoft. HDInsight" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure HDInsight 叢集的網路設定進行審核或強制執行。

您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝關鍵環境成品，例如 Azure Resource Manager 範本、RBAC 控制項和原則，來簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂閱、環境，以及透過版本控制來微調控制和管理。

如何檢視可用的 Azure 原則別名： 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何建立 Azure 藍圖： 

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：針對網路安全性群組使用標籤 (nsg) 以及與 Azure HDInsight 叢集相關聯的網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure 命令列介面 (CLI) 根據其標記查閱或執行資源上的動作。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何建立虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立具有安全性設定的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄監視網路資源設定，並偵測與您的 Azure HDInsight 部署相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

如何檢視及擷取 Azure 活動記錄事件： 

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中建立警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針： Microsoft 會維護 Azure HDInsight 叢集元件的時間來源，您可能會更新計算部署的時間同步處理。

如何設定 Azure 計算資源的時間同步處理：

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 資訊安全中心監視**：目前無法使用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：您可以將 Azure HDInsight 叢集上線到 Azure 監視器，以匯總叢集所產生的安全性資料。 利用自訂查詢來偵測和回應環境中的威脅。 

如何將 Azure HDInsight 叢集上線至 Azure 監視器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何建立 Azure HDInsight 叢集的自訂查詢：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：為 HDInsight 叢集啟用 Azure 監視器，並將其導向至 Log Analytics 工作區。 這會記錄所有 Azure HDInsight 叢集節點的相關叢集資訊和 OS 計量。

如何啟用 HDInsight 叢集的記錄：

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何查詢 HDInsight 記錄：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：讓 Azure HDInsight 叢集上線以 Azure 監視器。 確定所使用的 Log Analytics 工作區已根據您組織的合規性法規設定記錄保留期限。

如何將 Azure HDInsight 叢集上線至 Azure 監視器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何設定 Log Analytics 工作區保留期限：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：讓 Azure HDInsight 叢集上線以 Azure 監視器。 確定所使用的 Azure Log Analytics 工作區已根據您組織的合規性法規設定記錄保留期限。

如何將 Azure HDInsight 叢集上線至 Azure 監視器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

如何設定 Log Analytics 工作區保留期限：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導**方針：使用 Azure Log Analytics 工作區查詢來查詢 Azure HDInsight 記錄：

如何建立 Azure HDInsight 叢集的自訂查詢：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針：使用 Azure Log Analytics 工作區來監視和警示安全性記錄中的異常活動，以及與您的 Azure HDInsight 叢集相關的事件。

如何在 Azure 資訊安全中心中管理警示：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

如何對 log analytics 記錄資料發出警示：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針： Azure HDInsight 隨附針對叢集節點映射預先安裝和啟用的 Clamscan，不過您必須管理軟體，並手動匯總/監視 Clamscan 產生的任何記錄。

瞭解 Clamscan：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：執行 dns 記錄的協力廠商解決方案。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針：手動設定每個節點的主控台記錄。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針：維護在叢集布建期間建立的本機系統管理客戶紀錄，Azure HDInsight 叢集以及您所建立的任何其他帳戶。 此外，如果使用 Azure AD 整合，Azure AD 內建角色必須明確指派，因此可供查詢。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

此外，您也可以使用 Azure 資訊安全中心的身分識別和存取管理建議。

如何使用 PowerShell 在 Azure AD 中取得目錄角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

如何使用 Azure 資訊安全中心監視身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針：布建叢集時，Azure 會要求您為 web 入口網站建立新密碼，並安全殼層 (SSH) 存取。 沒有要變更的預設密碼，不過您可以針對 SSH 和入口網站存取指定不同的密碼。

如何在布建 Azure HDInsight 叢集時設定密碼：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：整合 Azure HDInsight 叢集的驗證與 Azure Active Directory。 使用專用的系統管理帳戶來建立原則和程式。

此外，您也可以使用 Azure 資訊安全中心的身分識別和存取管理建議。

如何整合 Azure HDInsight authentication 與 Azure Active Directory：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

如何使用 Azure 資訊安全中心監視身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針：使用 Azure HDInsight 識別碼代理程式，使用 Multi-Factor Authentication 登入企業安全性套件 (ESP) 叢集，而不提供任何密碼。 如果您已經登入其他 Azure 服務（例如 Azure 入口網站），您可以使用單一登入 (SSO) 體驗來登入 Azure HDInsight 叢集。

如何啟用 Azure HDInsight 識別碼訊息代理程式：

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure AD MFA，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。 設定企業安全性套件的 Azure HDInsight 叢集可連接到網域，讓網域使用者可以使用其網域認證來驗證叢集並執行 big data 作業。 使用多重要素驗證進行驗證 (MFA) 啟用時，使用者將會面臨提供第二個驗證因素的挑戰。

如何在 Azure 中啟用 MFA： 

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 資訊安全中心監視身分識別與存取： 

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導**方針：使用 paw (特殊許可權存取工作站) 搭配多重要素驗證 (MFA) 設定為登入和設定您的 Azure HDInsight 叢集和相關資源。

了解特殊權限存取工作站： 

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA： 

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指導**方針：設定企業安全性套件的 Azure HDInsight 叢集可以連線到網域，讓網域使用者可以使用其網域認證來進行驗證。 當 AAD 環境中發生可疑或不安全的活動時，您可以使用 Azure Active Directory (AAD) 安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

如何識別標示為具風險活動的 AAD 使用者：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何在 Azure 資訊安全中心中監視使用者身分識別和存取活動：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：設定企業安全性套件的 Azure HDInsight 叢集可以連線到網域，讓網域使用者可以使用其網域認證來進行驗證。 使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。

如何在 Azure 中設定具名位置： 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 AZURE ACTIVE DIRECTORY (AAD) 作為中央驗證和授權系統。 AAD 會對待用和傳輸中資料使用強式加密以保護資料。 AAD 也會 salts、雜湊並安全地儲存使用者認證。

已設定企業安全性套件 (ESP) 的 Azure HDInsight 叢集可以連線到網域，讓網域使用者可以使用其網域認證來驗證叢集。

如何建立和設定 AAD 實例：

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

如何在 Azure HDInsight 中使用 Azure Active Directory Domain Services 設定企業安全性套件：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針：使用 AZURE ACTIVE DIRECTORY (AAD) 驗證與您的 Azure HDInsight 叢集。 AAD 提供記錄以協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期審核使用者的存取權，以確保只有適當的使用者可以繼續存取。 

如何使用 Azure 身分識別存取權檢閱： 

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指導**方針：使用 AZURE ACTIVE DIRECTORY (AAD) 登入和審核記錄，以監視是否嘗試存取停用的帳戶;這些記錄可以整合到任何協力廠商 SIEM/監視工具中。

您可以建立 AAD 使用者帳戶的診斷設定、將審核記錄和登入記錄傳送到 Azure Log Analytics 工作區，以簡化此程式。 在 Azure Log Analytics 工作區中設定所需的警示。

如何將 Azure 活動記錄整合到 Azure 監視器中： 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：已設定企業安全性套件 (ESP) 的 Azure HDInsight 叢集可以連線到網域，讓網域使用者可以使用其網域認證來驗證叢集。  使用 Azure Active Directory (AAD) 風險偵測和 Identity Protection 功能，來設定自動回應與使用者身分識別相關的偵測到可疑動作。 此外，您可以將資料內嵌到 Azure Sentinel 以進行進一步的調查。

如何查看 AAD 具風險的登入：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定及啟用 Identity Protection 風險原則：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**：無法使用;客戶加密箱尚不支援 Azure HDInsight。

客戶加密箱支援服務清單： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導**方針：在與您的 Azure HDInsight 部署相關的資源上使用標籤，以協助追蹤儲存或處理機密資訊的 Azure 資源。

如何建立和使用標籤： 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 Azure HDInsight 叢集和任何相關聯的儲存體帳戶應該以虛擬網路/子網分隔、適當地標記，並在網路安全性群組內受到保護， (NSG) 或 Azure 防火牆。 叢集資料應該包含在安全的 Azure 儲存體帳戶內，或是 Azure Data Lake Storage (Gen1 或 Gen2) 。

選擇 Azure HDInsight 叢集的儲存體選項：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

如何保護 Azure Data Lake Storage：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

如何保護 Azure 儲存體帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：針對儲存或處理敏感性資訊的 Azure HDInsight 叢集，請使用標記將叢集和相關資源標記為機密。 若要透過遭到外泄減少資料遺失的風險，請使用 Azure 防火牆限制 Azure HDInsight 叢集的輸出網路流量。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

如何使用 Azure 防火牆限制 Azure HDInsight 叢集的輸出流量：

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

了解 Azure 中的客戶資料保護： 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：加密傳輸中的所有機密資訊。 確定任何連線至 Azure HDInsight 叢集或叢集資料存放區的用戶端 (Azure 儲存體帳戶或 Azure Data Lake Storage Gen1/Gen2) 都能夠協商 TLS 1.2 或更新版本。 Microsoft Azure 資源預設會協商 TLS 1.2。 

瞭解傳輸中 Azure Data Lake Storage 加密：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

瞭解傳輸中 Azure 儲存體帳戶加密：

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：利用 Azure HDInsight 企業安全性套件 (ESP) ，您可以使用 Apache Ranger 針對儲存在檔案、資料夾、資料庫、資料表和資料行/資料行中的資料，建立及管理更細緻的存取控制和資料模糊化原則。 Hadoop 系統管理員可以設定 (RBAC) 的角色型存取控制，以使用 Apache Ranger 中的外掛程式來保護 Apache Hive、HBase、Kafka 和 Spark。

使用 Apache Ranger 設定 RBAC 原則，可讓您將許可權與組織中的角色建立關聯。 這一層的抽象概念可讓您更輕鬆地確保人員只擁有執行其職責所需的權限。

使用 HDInsight 中的 Azure Active Directory Domain Services 企業安全性套件設定：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Azure HDInsight 中的企業安全性總覽：

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針：針對儲存或處理敏感性資訊的 Azure HDInsight 叢集，請使用標記將叢集和相關資源標記為機密。 Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：如果使用 Azure SQL Database 儲存 Apache Hive 和 Apache Oozie 中繼資料，請確保 SQL 資料隨時保持加密狀態。 針對 Azure 儲存體帳戶和 Data Lake Storage (Gen1 或 Gen2) ，建議您允許 Microsoft 管理您的加密金鑰，不過，您可以選擇管理您自己的金鑰。

如何管理 Azure 儲存體帳戶的加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

如何使用客戶管理的加密金鑰建立 Azure Data Lake Storage：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

瞭解 Azure SQL Database 的加密：

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

如何使用客戶管理的金鑰來設定 SQL Database 的透明資料加密：

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：針對與 Azure HDInsight 叢集相關聯的 Azure 儲存體帳戶設定診斷設定，以監視和記錄對叢集資料的所有 CRUD 作業。 針對與 Azure HDInsight 叢集相關聯的任何儲存體帳戶或 Data Lake 存放區啟用審核。

如何啟用 Azure 儲存體帳戶的其他記錄/審核：

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

如何啟用 Azure Data Lake Storage 的額外記錄/審核：

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指引**：實作協力廠商弱點管理解決方案。

（選擇性）如果您有 Rapid7、Qualys 或任何其他弱點管理平臺訂用帳戶，您可以使用腳本動作，在 Azure HDInsight 叢集節點上安裝弱點評估代理程式，並透過個別的入口網站管理節點。

如何手動安裝 Rapid7 代理程式：

https://insightvm.help.rapid7.com/docs/install

如何手動安裝 Qualys 代理程式：

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

如何使用腳本動作：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：已針對叢集節點映射啟用自動系統更新，但您必須定期重新開機叢集節點，以確保會套用更新。

Microsoft 可維護並更新基底 Azure HDInsight 節點映射。

如何設定 HDInsight 叢集的作業系統修補排程：

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指導**方針：使用腳本動作或其他機制來修補您的 Azure HDInsight 叢集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。

如何針對以 Linux 為基礎的 Azure HDInsight 叢集設定作業系統修補排程：

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

如何使用腳本動作：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針：實行可在一段時間內比較弱點掃描的協力廠商弱點管理解決方案。 如果您有 Rapid7 或 Qualys 訂用帳戶，您可以使用該廠商的入口網站來查看並比較回溯弱點掃描。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指引**：使用常見的風險評分計畫 (例如，常見弱點評分系統) 或協力廠商掃描工具所提供的預設風險評等。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指導**方針：使用 Azure Resource Graph 來查詢/探索 (的所有資源，例如計算、儲存體、網路、埠及通訊協定等。在訂用帳戶 () 中的 ) ，包括 Azure HDInsight 叢集。  確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

如何使用 Azure Resource Graph 建立查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂用帳戶： 

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC： 

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

如何建立其他 Azure 訂用帳戶： 

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組： 

https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用標籤： 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指導**方針：定義您的計算資源的已核准 Azure 資源清單和已核准的軟體

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索您的訂用帳戶 (s) 中的資源。 確保已核准環境中的所有 Azure 資源。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：執行協力廠商解決方案，以監視未核准之軟體應用程式的叢集節點。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針：使用 Azure Resource Graph 來查詢/探索 (的所有資源，例如計算、儲存體、網路、埠及通訊協定等。在訂用帳戶 () 中的 ) ，包括 Azure HDInsight 叢集。  移除您探索到的任何未經核准 Azure 資源。 針對 Azure HDInsight 叢集節點，請執行協力廠商解決方案，以移除或警示未核准的軟體。

如何使用 Azure Graph 建立查詢： 

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：針對 Azure HDInsight 叢集節點，請執行協力廠商解決方案，以防止未經授權的軟體執行。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 原則拒絕特定的資源類型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指導**方針：針對 Azure HDInsight 叢集節點，請執行協力廠商解決方案，以防止未經授權的檔案類型執行。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制使用者透過指令碼來與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。

如何設定條件式存取以封鎖 Azure Resource Manager 的存取： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導**方針：不適用;這不適用於 Azure HDInsight，因為叢集的非系統管理員 () 不需要存取個別節點來執行工作。 叢集系統管理員具有所有叢集節點的根目錄存取權。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；基準測試適用於裝載 Web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 "Microsoft. HDInsight" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 HDInsight 叢集的網路設定。

如何檢視可用的 Azure 原則別名： 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針： Azure HDInsight 由 Microsoft 管理及維護的作業系統映射。 客戶負責執行叢集節點作業系統的安全設定。 


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在] 來強制執行 Azure HDInsight 叢集和相關資源的安全設定。

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 原則效果： 

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針： Azure HDInsight 由 Microsoft 管理及維護的作業系統映射。 負責執行作業系統層級狀態設定的客戶。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

如何在 Azure DevOps 中儲存程式碼： 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 檔：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導**方針：不適用;不適用 Azure HDInsight 的自訂映射。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導**方針：使用 "Microsoft. HDInsight" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指導**方針：執行協力廠商解決方案，為您的叢集節點作業系統維持所需的狀態。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指導**方針：使用 "Microsoft. HDInsight" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 HDInsight 叢集的設定。

如何檢視可用的 Azure 原則別名： 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導**方針：執行協力廠商解決方案，以監視叢集節點作業系統的狀態。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針： Azure HDInsight 包含攜帶您自己的金鑰的 (BYOK) Apache Kafka 的支援。 這項功能可讓您擁有及管理用來加密待用資料的金鑰。

Azure HDInsight 中的所有受控磁片都會使用 Azure 儲存體服務加密 (SSE) 進行保護。 根據預設，這些磁碟上的資料會使用 Microsoft 所管理的金鑰來加密。 如果您啟用 BYOK，您會提供加密金鑰供 Azure HDInsight 使用，並使用 Azure Key Vault 來管理。

Key Vault 也可以搭配 Azure HDInsight 部署使用，以管理叢集儲存體 (Azure 儲存體帳戶和 Azure Data Lake Storage 的金鑰) 

如何在 Azure HDInsight 上攜帶您自己的 Apache Kafka 金鑰：

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

如何管理 Azure 儲存體帳戶的加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：受控識別可用於 Azure HDInsight 中，以允許您的叢集存取 Azure Data Lake Storage Gen2 中 Azure Active Directory 網域服務、存取 Azure Key Vault 或存取檔案。

瞭解 Azure HDInsight 的受控識別：

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導**方針：如果使用任何與您 Azure HDInsight 部署相關的程式碼，您可以執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

如何設定認證掃描器： 

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針： Azure HDInsight 隨附針對叢集節點映射預先安裝和啟用的 Clamscan，不過您必須管理軟體，並手動匯總/監視 Clamscan 產生的任何記錄。

瞭解 Azure HDInsight 的 Clamscan：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：在支援 Azure 服務的基礎主機上啟用 Microsoft Antimalware，但不會對客戶內容執行。

預先掃描要上傳到任何與 Azure HDInsight 叢集部署相關之 Azure 資源的檔案，例如 Data Lake Storage、Blob 儲存體等等。Microsoft 無法在這些實例中存取客戶資料。

瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware：

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導**方針： Azure HDInsight 針對叢集節點映射預先安裝和啟用 Clamscan。 Clamscan 將會自動執行引擎和定義更新，不過，您必須手動執行記錄的匯總和管理。

瞭解 Azure Azure HDInsight 的 Clamscan：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：使用 HDInsight 叢集資料存放區的 Azure 儲存體帳戶時，請選擇適當的複製選項 (LRS、ZRS、GRS、RA-GRS) 。  使用 Azure HDInsight 叢集資料存放區的 Azure SQL Database 時，請設定主動式異地複寫。

如何設定 Azure 儲存體帳戶的儲存體冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

如何設定 Azure SQL Database 的冗余：

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指導**方針：使用 Azure HDInsight 叢集資料存放區的 Azure 儲存體帳戶時，請選擇適當的冗余選項 (LRS、ZRS、GRS、RA-GRS) 。 如果您 Azure HDInsight 部署的任何部分都使用 Azure Key Vault，請確定您的金鑰已備份。

選擇 Azure HDInsight 叢集的儲存體選項：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

如何設定 Azure 儲存體帳戶的儲存體冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

如何在 Azure 中備份 Key Vault 金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：如果 Azure Key Vault 與您的 Azure HDInsight 部署搭配使用，請測試已備份之客戶管理金鑰的還原。

如何在 Azure HDInsight 上攜帶您自己的 Apache Kafka 金鑰：

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

如何在 Azure 中還原金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導**方針：如果 Azure Key Vault 與您的 Azure HDInsight 部署搭配使用，請在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

如何在 Azure Key Vault 中啟用虛刪除：

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：確定有撰寫的事件回應計畫，可定義人員角色以及事件處理/管理階段。

如何設定 Azure 資訊安全中心內的工作流程自動化： 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針：資訊安全中心會將嚴重性指派給警示，以協助您優先處理每個警示的順序，如此一來，當資源遭到入侵時，您就可以立即取得。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 視需要找出弱式點和間隙和修訂計畫。請參閱 NIST 的發行：適用于 IT 方案和功能的測試、訓練和練習程式指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。

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

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用 Azure 資訊安全中心的工作流程自動化功能，以透過 "Logic Apps" 自動觸發對安全性警示和建議的回應。

如何設定工作流程自動化和 Logic Apps： 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指導**方針：請遵循 Microsoft 的參與規則，以確保您的滲透測試不違反 Microsoft 原則：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

您可以在以下位置找到 Microsoft 針對 Microsoft 受控雲端基礎結構、服務和應用程式進行的 Microsoft 策略、Red 小組和即時網站滲透測試的詳細資訊： https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/overview) (機器翻譯)
- 深入了解 [Azure 安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)
