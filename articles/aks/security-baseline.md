---
title: 適用于 Azure Kubernetes Service 的 Azure 安全性基準
description: Azure Kubernetes Service 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6612e6982ae4826869134dfa46b35a3f202d4bbb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183238"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>適用于 Azure Kubernetes Service 的 Azure 安全性基準

適用于 Azure Kubernetes Service 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：根據預設，系統會自動建立網路安全性群組和路由表，並建立 Microsoft Azure Kubernetes SERVICE (AKS) 叢集。 AKS 會在使用負載平衡器、埠對應或輸入路由建立服務的情況下，自動修改網路安全性群組以取得適當的流量流程。 網路安全性群組會自動與客戶節點上的虛擬 Nic 和位於虛擬網路之子網的路由表產生關聯。 

使用 AKS 網路原則來限制網路流量，方法是根據所選的命名空間和標籤選取器，定義叢集中 Linux pod 之間的輸入和輸出流量規則。 網路原則使用方式需要 Azure CNI 外掛程式搭配已定義的虛擬網路和子網，而且只能在建立叢集時啟用。 無法在現有的 AKS 叢集上部署。

您可以執行私用 AKS 叢集，以確保 AKS API 伺服器和節點集區之間的網路流量，只會保留在私人網路中。 控制平面或 API 伺服器位於受 AKS 管理的 Azure 訂用帳戶中，並使用內部 (RFC1918) 的 IP 位址，而客戶的叢集或節點集區則位於自己的訂用帳戶中。 伺服器和叢集或節點集區會使用 API 伺服器虛擬網路中的 Azure Private Link 服務，以及在客戶 AKS 叢集的子網中公開的私人端點互相通訊。  或者，使用 AKS API 伺服器的公用端點，但使用 AKS API 伺服器授權的 IP 範圍功能來限制存取。 

- [Azure Kubernetes Service (AKS) 中應用程式和叢集的安全性概念](concepts-security.md) (機器翻譯)

- [在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod 之間的流量](use-network-policies.md)

- [建立私人 Azure Kubernetes Service 叢集](private-clusters.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針：使用「安全性中心」並遵循其網路保護建議，以保護 AZURE KUBERNETES SERVICE (AKS) 叢集所使用的網路資源。 

啟用網路安全性群組流量記錄，並將記錄傳送至 Azure 儲存體帳戶進行審核。 您也可以將流量記錄傳送至 Log Analytics 工作區，然後使用流量分析來提供 Azure 雲端中流量模式的深入解析，以視覺化網路活動、找出熱點和安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

- [如何啟用網路安全性流程記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：使用 Azure 應用程式閘道的 Web 應用程式防火牆 (WAF) 在 AKS 叢集之前，藉由篩選 Web 應用程式的連入流量來提供額外的安全性層級。 Azure WAF 會使用開放式 Web 應用程式安全性專案所提供的一組規則， (OWASP) ，以進行攻擊，例如跨網站腳本或針對此流量的 cookie 中毒。 

使用 API 閘道進行驗證、授權、節流、快取、轉換和監視 AKS 環境中使用的 Api。 API 閘道可做為微服務的前門、將用戶端與您的微服務分離，並藉由消除處理跨領域考慮的負擔，來降低微服務的複雜度。

- [瞭解 AKS 中網路連線能力和安全性的最佳作法](operator-best-practices-network.md)

- [應用程式閘道輸入控制器 ](../application-gateway/ingress-controller-overview.md)

- [搭配 Azure Kubernetes Service 中部署的微服務使用 Azure API 管理](../api-management/api-management-kubernetes.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：在部署 AZURE KUBERNETES SERVICE (AKS) 元件的虛擬網路上啟用 Microsoft 分散式阻斷服務 (DDoS) 標準保護，以防範 DDoS 攻擊。
安裝網路原則引擎和建立 Kubernetes 網路原則，以控制 AKS 中 pod 之間的流量，根據預設，這些 pod 之間允許所有流量。 網路原則只應用於 AKS 中的 Linux 型節點和 Pod。 定義規則來限制 pod 通訊，以提升安全性。 

選擇根據設定（例如指派的標籤、命名空間或流量埠）來允許或拒絕流量。 必要的網路原則可自動套用，因為 pod 會在 AKS 叢集中以動態方式建立。 

- [在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod 之間的流量](use-network-policies.md)

- [如何設定 DDoS 保護](../ddos-protection/manage-ddos-protection.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：請視需要使用網路監看員封包捕獲來調查異常活動。 

當您在訂用帳戶中建立或更新虛擬網路時，虛擬網路的區域中會自動啟用網路監看員。 您也可以使用 PowerShell、Azure CLI、REST API 或 ARMClient 方法，建立新的網路監看員實例

- [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：利用 Web 應用程式防火牆 (WAF) 啟用 Azure 應用程式閘道，保護您的 AZURE KUBERNETES SERVICE (AKS) 叢集。 

如果不需要以承載檢查或行為分析為基礎的入侵偵測和/或預防，則可以在「偵測模式」中使用和設定具有 WAF 的 Azure 應用程式閘道來記錄警示和威脅，或「防止模式」主動封鎖偵測到的入侵和攻擊。

- [瞭解使用 WAF 保護 AKS 叢集的最佳作法](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [如何 (Azure WAF) 部署 Azure 應用程式閘道 ](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：使用 Azure 應用程式啟用閘道的 Web 應用程式防火牆 (WAF) 在 AKS 叢集之前，以篩選連入流量。 開放式 Web 應用程式安全性專案 (OWASP) 提供一組規則，可在 Azure WAF 中用來監看跨網站腳本或 cookie 中毒等攻擊。

將完整功能變數名稱 (FQDN) 標記套用至應用程式，以便在網路安全性群組內設定應用程式規則時輕鬆使用。 設定網路規則之後。 使用 FQDN 標籤（例如 AzureKubernetesService）新增應用程式規則，其中包含可透過 TCP 埠443和埠80存取的所有必要 Fqdn。 

- [瞭解 AKS 中網路連線能力和安全性的最佳作法](operator-best-practices-network.md)

- [在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod 之間的流量](use-network-policies.md)

- [如何 (Azure WAF) 部署 Azure 應用程式閘道 ](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導** 方針：使用虛擬網路服務標籤，在與 AZURE KUBERNETES SERVICE (AKS) 實例相關聯的網路安全性群組上定義網路存取控制。 當您藉由指定服務標籤名稱來建立安全性規則，以允許或拒絕對應服務的流量時，可使用服務標籤來取代特定的 IP 位址。 

Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

將 Azure 標記套用至 AKS 叢集中的節點集區。 它們不同于虛擬網路服務標籤，而且會套用至節點集區中的每個節點，並持續進行升級。 

- [瞭解和使用服務標記](../virtual-network/service-tags-overview.md)

- [瞭解 AKS 的 Nsg](support-policies.md)

- [控制 Azure Kubernetes Service (AKS) 中叢集節點的連出流量](limit-egress-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：使用與 AZURE KUBERNETES SERVICE (AKS) 叢集相關聯的網路資源 Azure 原則定義和實行標準安全性設定。 使用 ">microsoft.containerservice" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 AKS 叢集的網路設定進行審核或強制執行。 

此外，也可以使用與 AKS 相關的內建原則定義，例如：

• Kubernetes 服務上應定義授權 IP 範圍

•在 Kubernetes 叢集中強制使用 HTTPS 輸入

•確保服務只在 Kubernetes 叢集中允許的埠上接聽

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [適用于網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對網路安全性群組和其他資源使用標籤，以流入和流出 AZURE KUBERNETES SERVICE (AKS) 叢集的流量。 針對個別的網路安全性群組規則使用 [描述] 欄位，以指定商務需求和/或持續時間，以及允許流量進出網路的任何規則。
使用任何內建的 Azure 原則標記相關定義，例如「需要標籤和其值」，確保所有資源都是使用標籤建立的，並接收現有未標記資源的通知。

選擇根據命名空間和具有網路原則的標籤選取器，允許或拒絕叢集中特定的網路路徑。 使用這些命名空間和標籤作為流量設定規則的描述項。 使用 Azure PowerShell 或 Azure 命令列介面 (CLI) 根據其標記查閱或執行資源的動作。

- [使用 CLI Azure 原則](/cli/azure/policy?view=azure-cli-latest)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

- [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視網路資源設定，並偵測與 AZURE KUBERNETES SERVICE (AKS) 叢集相關之網路資源的變更。 

在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。 活動記錄中 AzureContainerService 使用者的任何專案都會記錄為平臺動作。 

使用 Azure 監視器記錄來啟用和查詢記錄，使其無法 AKS 主要元件，kube-apiserver 和 kube-控制器-管理員。 使用容器執行時間建立和管理執行 kubelet 的節點，並透過受控 Kubernetes API 伺服器部署其應用程式。 

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

- [在 Azure Kubernetes Service (AKS) 中啟用並檢閱 Kubernetes 主要節點記錄](view-master-logs.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： AZURE KUBERNETES SERVICE (AKS) 節點使用 ntp.ubuntu.com 進行時間同步處理，以及 UDP 埠123和網路時間通訊協定 (ntp) 。 

如果使用自訂 DNS 伺服器，請確定叢集節點可存取 NTP 伺服器。 

- [瞭解 AKS 叢集節點的 NTP 網域和埠需求](limit-egress-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：從 Azure Kubernetes Services 啟用 audit 記錄 (AKS) 主要元件、kube-apiserver 和 kube 控制器-管理員，這些元件是以受控服務的形式提供。 

• kube-auditaksService： hcpService 中控制平面作業 (的 [審核記錄] 顯示名稱)  

• masterclient： MasterClientCertificate 審核記錄檔中的顯示名稱，您從 az aks 取得認證的憑證 

• nodeclient： ClientCertificate 的顯示名稱，由代理程式節點使用

也可啟用其他的 audit 記錄，例如 kube-audit。 

將這些記錄匯出至 Log Analytics 或另一個儲存體平臺。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

根據您的組織商務需求，將此資料啟用並在面板上 Azure Sentinel 或協力廠商 SIEM。

- [在這裡查看記錄檔架構（包括記錄角色）](view-master-logs.md)

- [瞭解容器的 Azure 監視器](../azure-monitor/insights/container-insights-overview.md)

- [如何啟用容器的 Azure 監視器](../azure-monitor/insights/container-insights-onboard.md)

- [在 Azure Kubernetes Service (AKS) 中啟用並檢閱 Kubernetes 主要節點記錄](view-master-logs.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：使用活動記錄來監視對 AZURE KUBERNETES SERVICE (AKS) 資源的動作，以查看所有活動及其狀態。 使用活動記錄來判斷在訂用帳戶中的資源上所採取的作業：啟動作業的人員

作業進行的時間

作業的狀態

其他可能協助您研究作業的屬性值

透過 Azure PowerShell、Azure 命令列介面 (CLI) 、Azure REST API 或 Azure 入口網站，從活動記錄中取出資訊。 

在 AKS 主要元件上啟用 audit 記錄，例如： 

• kube-auditaksService： hcpService 中控制平面作業 (的 [審核記錄] 顯示名稱)  

• masterclient： MasterClientCertificate 審核記錄檔中的顯示名稱，您從 az aks 取得認證的憑證 

• nodeclient： ClientCertificate 的顯示名稱，由代理程式節點使用

也開啟其他的 audit 記錄，例如 kube-audit。 

- [如何在 AKS 中啟用和審核 Kubernetes 主要節點記錄](view-master-logs.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導** 方針：啟用 Log Analytics 代理程式的自動安裝，以便從 AKS 叢集節點收集資料。 此外，從 Azure 資訊安全中心開啟 Azure Log Analytics 監視代理程式的自動布建，預設會關閉自動布建。 代理程式也可以手動安裝。 使用自動布建時，資訊安全中心會在所有支援的 Azure Vm 和任何新建立的虛擬機器上部署 Log Analytics 代理程式。 「安全性中心」會從 Azure 虛擬機器收集資料， (VM) 、虛擬機器擴展集和 IaaS 容器（例如 Kubernetes 叢集節點）來監視安全性弱點和威脅。 您可以使用 Azure Log Analytics 代理程式來收集資料，該代理程式會從電腦讀取各種安全性相關設定和事件記錄檔，並將資料複製到您的工作區以進行分析。 

需要資料收集，以提供遺失更新、設定錯誤的 OS 安全性設定、endpoint protection 狀態，以及健康情況和威脅偵測的可視性。

- [如何啟用 Log Analytics 代理程式的自動布建](../security-center/security-center-enable-data-collection.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：讓您的 AZURE KUBERNETES SERVICE (AKS) 實例，以根據貴組織的合規性需求 Azure 監視器和設定對應的 Azure Log Analytics 工作區保留期限。 

- [如何設定 Log Analytics 工作區的記錄保留期參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：讓您的 AZURE KUBERNETES SERVICE (AKS) 實例，以 Azure 監視器和設定叢集的診斷設定。 

使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。 Azure 監視器記錄會在 Azure 入口網站中啟用及管理，或透過 CLI 來管理，並使用 Kubernetes 角色型存取控制 (Kubernetes RBAC) 、Azure RBAC 和非 RBAC 啟用的 AKS 叢集。

查看 AKS 主要元件所產生的記錄檔 (kube apiserver 和 kube-controllermanager) ，以針對您的應用程式和服務進行疑難排解。 啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM，以進行集中式記錄管理和監視。

- [如何在 AKS 中啟用和審核 Kubernetes 主要節點記錄](view-master-logs.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：使用 AZURE KUBERNETES SERVICE (AKS) 與「安全性中心」搭配使用，以深入瞭解 AKS 節點。 查看資訊安全中心對主機和叢集層級偵測到的威脅和惡意活動的警示。 安全性中心會持續分析 AKS 叢集中所發生的原始安全性事件，例如網路資料、進程建立及 Kubernetes 審核記錄。 判斷此活動是否為預期的行為，或應用程式是否運作不正常。 使用 Azure 監視器中的計量和記錄來證實您的結果。 

- [瞭解 Azure Kubernetes Services 與 Security Center 的整合](../security-center/defender-for-kubernetes-introduction.md)

- [如何啟用 Azure 資訊安全中心標準層](../security-center/security-center-get-started.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：安裝及啟用適用于 Azure 的 Microsoft 反惡意程式碼，以 AZURE KUBERNETES SERVICE (AKS) 的虛擬機器和虛擬機器擴展集節點。 查看安全中心的警示以進行補救。

- [適用於 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

- [安全性警示參考指南](../security-center/alerts-reference.md)

- [容器的警示-Azure Kubernetes Service 叢集](../security-center/alerts-reference.md#alerts-akscluster)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針： AZURE KUBERNETES SERVICE (AKS) 使用 CoreDNS 專案進行叢集 DNS 管理和解決。

在您的 coredns-自訂 ConfigMap 中套用記載的設定，以啟用 DNS 查詢記錄。 

- [使用 Azure Kubernetes Service 自訂 CoreDNS](coredns-custom.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導** 方針：在 Azure Kubernetes Service 中使用命令列用戶端的 KUBECTL (AKS) 來管理 Kubernetes 叢集，並從 AKS 節點取得其記錄以供疑難排解之用。 如果您使用 Azure Cloud Shell，則已安裝 Kubectl。 若要在本機安裝 kubectl，請使用 ' Install-AzAksKubectl ' Cmdlet。

- [快速入門-使用 PowerShell 部署 Azure Kubernetes Service 叢集](kubernetes-walkthrough-powershell.md)

- [從 Azure Kubernetes Service (AKS) 叢集節點取得 kubelet 記錄](kubelet-logs.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： AZURE KUBERNETES SERVICE (AKS) 本身不會提供儲存一般使用者帳戶和密碼的身分識別管理解決方案。 使用 Azure Active Directory (Azure AD) 整合時，您可以授與使用者或群組存取權，以 Kubernetes 命名空間或整個叢集內的資源。 

執行臨機操作查詢，以使用 Azure AD PowerShell 模組來探索屬於 AKS 系統管理群組成員的帳戶

針對「取得受控 Kubernetes 叢集的存取認證」之類的作業使用 Azure CLI，以協助定期協調存取。 執行此程式以保留更新的服務帳戶清查，也就是 AKS 中的另一個主要使用者類型。 強制執行資訊安全中心的身分識別和存取管理建議。

- [如何整合 AKS 與 Azure AD](./azure-ad-integration-cli.md)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： AZURE KUBERNETES SERVICE (AKS) 不具有一般預設密碼的概念，也不提供可儲存一般使用者帳戶和密碼的身分識別管理解決方案。 使用 Azure Active Directory (Azure AD) 整合時，您可以授與以角色為基礎的存取權，以 AKS 命名空間或整個叢集內的資源。 

執行臨機操作查詢，以使用 Azure AD PowerShell 模組來探索屬於 AKS 系統管理群組成員的帳戶

- [瞭解 AKS 的存取和身分識別選項](concepts-identity.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：整合 AZURE KUBERNETES SERVICE (AKS) 叢集的使用者驗證，Azure Active Directory (Azure AD) 。 使用 Azure AD authentication 權杖登入 AKS 叢集。 設定 Kubernetes 角色型存取控制 (Kubernetes RBAC) 以管理存取 Kubernetes 設定 (kubeconfig) 資訊和許可權、命名空間和叢集資源。 

使用專用的系統管理帳戶來建立原則和程式。 執行資訊安全中心的身分識別和存取管理建議。

- [如何使用 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

- [控制對叢集資源的存取](azure-ad-rbac.md)

- [使用 Azure 角色型存取控制](control-kubeconfig-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：使用適用于 AZURE KUBERNETES SERVICE (AKS) 的單一登入，Azure Active Directory (Azure AD AKS 叢集的整合式驗證。

- [如何即時查看 Kubernetes 記錄、事件和 pod 計量](../azure-monitor/insights/container-insights-livedata-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：整合 AZURE KUBERNETES SERVICE (AKS) 的驗證與 Azure Active Directory (Azure AD) 。 

啟用 Azure AD Multi-Factor Authentication (MFA) ，並遵循資訊安全中心的身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md) 

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用特殊許可權存取工作站 (PAW) ，MULTI-FACTOR AUTHENTICATION (MFA) 設定為登入指定的 Azure Kubernetes Service (叢集和相關資源。
- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：使用 Azure Active Directory (Azure AD) AZURE AD Azure Kubernetes Service AKS (整合式驗證的安全性報告。 當環境中發生可疑或不安全的活動時，就會產生警示。 使用資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 資訊安全中心中監視使用者身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組存取 AZURE KUBERNETES SERVICE (AKS) 叢集。 這需要 AKS 的整合式驗證，Azure Active Directory (Azure AD) 。

限制只有一組有限的 IP 位址範圍存取 AKS API 伺服器，因為它會接收要求以在叢集中執行動作，以建立資源或調整節點數目。 

- [使用 Azure Kubernetes Service (AKS) 的授權 IP 位址範圍，安全地存取 API 伺服器 ](api-server-authorized-ip-ranges.md)

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure Active Directory (Azure AD) 做為 AZURE KUBERNETES SERVICE (AKS) 的中央驗證和授權系統。 Azure AD 使用強式加密來保護待用資料和傳輸中的資料，以及 salts、雜湊，以及安全地儲存使用者認證，藉此保護資料。

使用 AKS 的內建角色搭配 Azure 角色型存取控制 (Azure RBAC) -資源原則參與者和擁有者，以針對您的 Kubernetes 叢集進行原則指派操作

- [Azure 原則概觀](../governance/policy/overview.md)

- [如何整合 Azure AD 與 AKS](./azure-ad-integration-cli.md) 

- [整合 AKS 管理的 Azure AD](managed-aad.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針：使用 Azure Active Directory (Azure AD) AZURE AD Azure Kubernetes Service AKS (整合式驗證的安全性報告。 搜尋 Azure AD 記錄檔，以協助探索過時的帳戶。 

執行 Azure 身分識別存取審核，以有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 從資訊安全中心修復身分識別和存取建議。

請留意用於支援或疑難排解用途的角色。 例如，Microsoft 支援服務所採取的任何叢集動作 (與使用者同意) 會以名稱 aks-支援-rolebinding 的內建 Kubernetes 「編輯」角色來進行。 此角色已啟用 AKS 支援，以編輯叢集設定和資源，以針對叢集問題進行疑難排解及診斷。 不過，此角色無法修改許可權，也無法建立角色或角色系結。 只有在使用即時支援票證的情況下，才會啟用此角色存取與即時 (JIT) 存取。
 
- [Azure Kubernetes Service (AKS) 的存取與身分識別選項](concepts-identity.md)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

- [如何在 Azure 資訊安全中心中監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：整合 AZURE KUBERNETES SERVICE (AKS) 與 Azure Active Directory (Azure AD) 的使用者驗證。 建立 Azure AD 的診斷設定，將審核和登入記錄傳送到 Azure Log Analytics 工作區。 設定所需的警示 (例如，當停用的帳戶嘗試登入 Azure Log Analytics 工作區中的) 時。
- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何使用 Azure 監視器建立、查看和記錄管理警示](../azure-monitor/platform/alerts-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：整合 AZURE KUBERNETES SERVICE (AKS) 與 Azure Active Directory (Azure AD) 的使用者驗證。 使用 Azure AD 的風險偵測和 Identity Protection 功能，對偵測到與使用者身分識別相關的可疑動作設定自動回應。 將資料內嵌至 Azure Sentinel，以根據商務需求進一步調查。

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：不適用 AZURE KUBERNETES SERVICE (AKS) ，因為客戶加密箱不支援此功能。
- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導** 方針：使用 AZURE KUBERNETES SERVICE (AKS) 部署相關資源上的標籤，以協助追蹤儲存或處理機密資訊的 Azure 資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

- [更新受控叢集的標記](/rest/api/aks/managedclusters/updatetags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：利用 AZURE KUBERNETES SERVICE (AKS) 以邏輯方式隔離相同叢集中的小組和工作負載，以提供最少的許可權，範圍設定為每個小組所需的資源。 

使用 Kubernetes 中的命名空間來建立邏輯隔離界限。 請考慮為隔離和多租使用者（例如，排程、網路、驗證/授權和容器）執行額外的 Kubernetes 功能。

針對開發、測試和生產環境，執行不同的訂用帳戶和/或管理群組。 將 AKS 叢集部署至不同的虛擬網路（已適當標記），以將其與網路區隔開。

- [瞭解 AKS 中群集隔離的最佳作法](operator-best-practices-cluster-isolation.md)

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [瞭解 AKS 中網路連線能力和安全性的最佳作法](operator-best-practices-network.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：使用協力廠商解決方案，從網路周邊的 Azure Marketplace，監視是否有未經授權的機密資訊傳輸並封鎖這類傳輸，同時警示資訊安全專業人員。

Microsoft 會管理基礎平臺，並將所有客戶內容視為機密資料，並將其視為絕佳的長度，以防止客戶資料遺失和公開。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

- [AKS 功能所需的埠、位址和功能變數名稱清單](limit-egress-traffic.md)

- [如何設定 Azure 防火牆的診斷設定](../firewall/firewall-diagnostics.md)

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：建立 HTTPS 輸入控制器並使用您自己的 TLS 憑證 (或者，您也可以選擇將 AZURE KUBERNETES SERVICE (AKS) 部署的) 加密。 

Kubernetes 輸出流量預設會透過 HTTPS/TLS 加密。 檢查 AKS 實例中任何可能的未加密輸出流量，以進行其他監視。 這可能包括 NTP 流量、DNS 流量、在某些情況下用來取得更新的 HTTP 流量。 

- [如何在 AKS 上建立 HTTPS 輸入控制器，並使用您自己的 TLS 憑證](ingress-own-tls.md)

- [如何使用 Let's Encrypt 在 AKS 上建立 HTTPS 輸入控制器](ingress-tls.md)

- [AKS 所使用的潛在輸出埠和通訊協定清單](limit-egress-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。
Microsoft 會管理基礎平臺，並將所有客戶內容視為機密資料，並將其視為絕佳的長度，以防止客戶資料遺失和公開。 

為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 來管理資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 以 Azure Resource Manager 為基礎的授權系統，以提供更細緻的 azure 資源存取管理。

設定 Azure Kubernetes Service (AKS) 使用 Azure Active Directory (Azure AD) 進行使用者驗證。 使用此設定 Azure AD 驗證權杖來登入 AKS 叢集。 

使用 AKS 內建角色搭配 Azure RBAC-資源原則參與者和擁有者，以針對您的 AKS 叢集指派原則指派作業

- [如何在 AKS 中使用 Azure RBAC 和 Azure AD 身分識別來控制對叢集資源的存取](azure-ad-rbac.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。
Microsoft 會管理基礎平臺，並將所有客戶內容視為機密資料，並將其視為絕佳的長度，以防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：為 AZURE KUBERNETES SERVICE (AKS) 中的磁片區提供的兩種主要儲存體類型，是由 Azure 磁片或 Azure 檔案儲存體支援。 這兩種類型的儲存體都會使用 Azure 儲存體服務加密 (SSE) ，這會將待用資料加密以提升安全性。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。

使用客戶管理的金鑰進行待用加密可用於將 AKS 叢集上的 OS 和資料磁片加密，以對加密金鑰進行額外控制。 客戶擁有金鑰管理活動（例如金鑰備份和輪替）的責任。 目前磁碟無法在 AKS 節點層級使用 Azure 磁碟加密進行加密。

- [在 Azure Kubernetes Service (AKS) 中進行儲存和備份的最佳做法](operator-best-practices-storage.md)

- [在 Azure Kubernetes Service (AKS 中使用 Azure 磁片 (BYOK) 攜帶您自己的金鑰) ](azure-disk-customer-managed-keys.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用容器的 Azure 監視器來監視部署至裝載于 AZURE KUBERNETES SERVICE (AKS) 上之受控 Kubernetes 叢集的容器工作負載效能。 

設定警示，以在節點或容器上的 CPU 和記憶體使用率超過定義的閾值時，或在叢集的基礎結構或節點健全狀況匯總時，進行主動式通知或記錄建立。 

使用 Azure 活動記錄來監視您的 AKS 叢集和相關資源（高階）。 與 Prometheus 整合，以使用查詢建立自訂警示、儀表板和詳細的執行詳細分析，來查看它從節點和 Kubernetes 收集的應用程式和工作負載計量。

- [瞭解容器的 Azure 監視器](../azure-monitor/insights/container-insights-overview.md)

- [如何啟用容器的 Azure 監視器](../azure-monitor/insights/container-insights-onboard.md)

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：使用「安全性中心」來監視您的 Azure Container Registry，包括 AZURE KUBERNETES SERVICE (AKS) 實例的弱點。 在 [安全性中心] 中啟用容器登錄套件組合，以確保「安全性中心」準備好掃描推送至登錄的映射。

當安全性中心使用 Qualys 掃描映射後發現問題時，在 [安全性中心] 儀表板中收到通知。 容器登錄套件組合功能可讓您更深入瞭解以 Azure Resource Manager 為基礎的登錄中所使用之映射的弱點。 

針對每個弱點，使用「安全性中心」可採取動作的建議。 這些建議包括安全性分類，以及補救的指引。 

- [Azure Kubernetes Services (AKS) 中的容器映像管理與安全性最佳做法](../security-center/defender-for-container-registries-introduction.md)

- [瞭解 AKS 中容器映射管理和安全性的最佳作法](operator-best-practices-container-image-management.md)

- [瞭解 container Registry 與 Azure 資訊安全中心的整合](../security-center/defender-for-container-registries-introduction.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針：安全性更新會自動套用至 Linux 節點，以保護客戶 AZURE KUBERNETES SERVICE (AKS) 叢集。 這些更新包括 OS 安全性修正或核心更新。 

請注意，將 Windows Server 節點保持在最新狀態的程式與執行 Linux 的節點不同，因為 Windows server 節點不會收到每日更新。 相反地，客戶需要在其 AKS 叢集中的 Windows Server 節點集區上執行升級，這會使用 Azure 控制台或 Azure CLI，以最新的基底視窗伺服器映射和修補程式來部署新節點。 這些更新包含 AKS 的安全性或功能改進。

- [瞭解如何將更新套用至執行 Linux 的 AKS 叢集節點](node-updates-kured.md)

- [如何針對使用 Windows Server 節點的 AKS 叢集升級 AKS 節點集區](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Azure Kubernetes Service (AKS) 節點映射升級](node-image-upgrade.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導** 方針：執行手動程式以確保 AZURE KUBERNETES SERVICE (AKS) 叢集節點的協力廠商應用程式在叢集存留期間仍會保持修補。 這可能需要啟用自動更新、監視節點，或執行定期重新開機。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針：依一致的間隔匯出「安全性中心」掃描結果並比較結果，以確認已補救弱點。 

使用 PowerShell Cmdlet "AzSecurityTask" 將安全性工作的抓取自動化，讓安全性中心建議您執行這些工作，以加強安全性狀態及補救弱點掃描結果。

- [如何使用 PowerShell 來查看 Azure 資訊安全中心探索到的弱點](/powershell/module/az.security/get-azsecuritytask?view=azps-3.3.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：使用安全中心提供的嚴重性等級來優先補救弱點。 

如果使用 Azure (提供的內建弱點評定工具) （例如 Qualys 或 Rapid7），請使用常見的弱點評分系統 (CVSS)  (或掃描工具所提供的其他評分系統) 。

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢/探索所有資源 (例如訂用帳戶內) 的計算、儲存體、網路等等。 確定您有適當的 (讀取您租使用者中) 的許可權，而且能夠列舉訂用帳戶中的所有 Azure 訂用帳戶及資源。

雖然可以透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：使用中繼資料將標記套用至 Azure 資源，以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤資產。 

建立 Azure Kubernetes Service (AKS) 節點集區時，套用污點、標籤或標記。 該節點集區中的所有節點也會繼承該污點、標籤或標記。

污點、標籤或標籤可用來定期協調清查，並確保及時從訂用帳戶中刪除未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用者標記](../azure-resource-manager/management/tag-resources.md)

- [受控叢集-更新標記](/rest/api/aks/managedclusters/updatetags)

- [為節點集區指定污點、標籤或標記](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據組織的商務需求，定義經過核准的 Azure 資源清單以及計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：
-   不允許的資源類型 

-   允許的資源類型

使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 確定環境中的所有 Azure 資源都已根據組織商務需求核准。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：使用 Azure 自動化變更追蹤和清查功能，找出您的環境中安裝的軟體。 

在您的電腦上收集和查看軟體、檔案、Linux 守護程式、Windows 服務和 Windows 登錄機碼的清查，並監視未核准的軟體應用程式。 

追蹤電腦的設定，以協助找出環境中的操作問題，並進一步瞭解您電腦的狀態。

- [如何啟用 Azure 虛擬機器清查](../automation/automation-tutorial-installed-software.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：使用 Azure 自動化變更追蹤和清查功能，找出您的環境中安裝的軟體。 

在您的電腦上收集和查看軟體、檔案、Linux 守護程式、Windows 服務和 Windows 登錄機碼的清查，並監視未核准的軟體應用程式。 

追蹤電腦的設定，以協助找出環境中的操作問題，並進一步瞭解您電腦的狀態。

- [如何啟用 Azure 虛擬機器清查](../automation/automation-tutorial-installed-software.md)

- [如何使用檔案完整性監視](../security-center/security-center-file-integrity-monitoring.md)

- [瞭解 Azure 變更追蹤](../automation/change-tracking/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針：使用 Azure 自動化變更追蹤和清查功能，找出您的環境中安裝的軟體。 

在您的電腦上收集和查看軟體、檔案、Linux 守護程式、Windows 服務和 Windows 登錄機碼的清查，並監視未核准的軟體應用程式。 

追蹤電腦的設定，以協助找出環境中的操作問題，並進一步瞭解您電腦的狀態。

針對存在於您環境中的應用程式，在安全中心啟用適應性應用程式分析。

- [如何啟用 Azure 虛擬機器清查](../automation/automation-tutorial-installed-software.md)

 
如何使用 Azure 資訊安全中心適應性應用程式
- [控制項](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型

- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導** 方針：使用 Azure 原則，對可使用內建原則定義在訂用帳戶中建立的資源類型施加限制。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。
- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制使用者在計算資源中執行腳本的能力

**指導** 方針： AZURE KUBERNETES SERVICE (AKS) 本身不會提供儲存一般使用者帳戶和密碼的身分識別管理解決方案。 相反地，請使用 Azure Active Directory (Azure AD) 作為 AKS 叢集的整合式身分識別解決方案。 

使用 Azure AD 整合，將存取權授與使用者或群組，以 Kubernetes 命名空間內或整個叢集的資源。 

使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於您 AKS 系統管理群組成員的帳戶，並使用它來定期協調存取。 使用 Azure CLI 來進行作業，例如「取得受控 Kubernetes 叢集的存取認證」。 執行資訊安全中心的身分識別和存取管理建議。

- [使用 Azure CLI 管理 AKS](/cli/azure/aks?view=azure-cli-latest)

- [瞭解 AKS 與 Azure AD 整合](concepts-identity.md)

- [如何整合 AKS 與 Azure AD](./azure-ad-integration-cli.md)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：使用 AZURE KUBERNETES SERVICE (AKS) 功能，以邏輯方式隔離相同叢集中的小組和工作負載，以取得最少的許可權，範圍設定為每個小組所需的資源。 

在 Kubernetes 中執行命名空間，以建立邏輯隔離界限。 使用 ">microsoft.containerservice" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Kubernetes Service (AKS) 實例的設定進行審核或強制執行。 

檢查並執行隔離和多租使用者的其他 Kubernetes 功能和考慮，以包含下列各項：排程、網路、驗證/授權和容器。 也請使用不同的訂用帳戶和/或管理群組進行開發、測試和生產環境。 將 AKS 叢集與虛擬網路、子網適當標記，並使用 Web 應用程式防火牆保護 (WAF) 。

- [瞭解 AKS 中群集隔離的最佳作法](operator-best-practices-cluster-isolation.md)

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [瞭解 AKS 中網路連線能力和安全性的最佳作法](operator-best-practices-network.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：在 ">microsoft.containerservice" 命名空間中使用 Azure 原則別名來建立自訂原則，以對 AZURE KUBERNETES SERVICE (AKS) 實例的設定進行審核或強制執行。 使用內建的 Azure 原則定義。

AKS 內建原則定義的範例包括：

•在 Kubernetes 叢集中強制使用 HTTPS 輸入

• Kubernetes 服務上應定義授權 IP 範圍

• Kubernetes 服務上應使用角色型存取控制 (RBAC) 

•確定 Kubernetes 叢集中只有允許的容器映射

使用 Azure Resource Manager 在 JavaScript 物件標記法 (JSON) 中匯出 AKS 設定的範本。 定期檢查，以確保這些設定符合您組織的安全性需求。 使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。 

- [如何設定及管理 AKS pod 安全性原則](use-pod-security-policies.md)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導** 方針： Azure Kubernetes 叢集 (AKS) 叢集部署在具有安全性優化作業系統的主機虛擬機器上。 主機 OS 已將額外的安全性強化步驟納入其中，以減少攻擊面，並以安全的方式允許部署容器。 

Azure 會套用每日修補程式 (包括安全性修補程式) AKS 虛擬機器主機，以及需要重新開機的一些修補程式。 客戶須負責依據需求排程 AKS 虛擬機器主機重新開機。 

- [AKS 代理程式節點主機 OS 的安全性強化](security-hardened-vm-host-image.md)

- [瞭解 AKS 虛擬機器主機中的安全性強化](security-hardened-vm-host-image.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 pod 安全性原則來保護 AZURE KUBERNETES SERVICE (AKS) 叢集。  限制可排程的 pod，以改善叢集的安全性。 

要求不允許資源的 pod 無法在 AKS 叢集中執行。 

此外，也請使用 Azure 原則 [拒絕] 和 [部署（如果不存在）] 效果來強制執行與您的 AKS (部署相關之 Azure 資源的安全設定，例如虛擬網路、子網、Azure 防火牆、儲存體帳戶等等) 。 

使用下列命名空間的別名建立自訂 Azure 原則定義： 

• Microsoft. >microsoft.containerservice

• Microsoft 網路

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導** 方針： AZURE KUBERNETES SERVICE (AKS) 叢集部署在具有安全性優化 OS 的主機虛擬機器上。 主機 OS 已將額外的安全性強化步驟納入其中，以減少攻擊面，並以安全的方式允許部署容器。 

請參閱主機作業系統內建的 (CIS) 控制項的網際網路安全性中心清單。  

- [AKS 代理程式節點主機 OS 的安全性強化](security-hardened-vm-host-image.md)

- [瞭解 AKS 叢集的狀態設定](concepts-clusters-workloads.md#control-plane)

- [瞭解 AKS 虛擬機器主機中的安全性強化](security-hardened-vm-host-image.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的設定。 匯出 Azure Kubernetes Service (AKS) 設定的範本，JavaScript 物件標記法 (使用) 的 JSON Azure Resource Manager。 定期審核，以確保設定符合您組織的安全性需求。 

執行協力廠商解決方案（例如 Terraform）來建立設定檔，以宣告 Kubernetes 叢集的資源。 您可以藉由執行安全性最佳作法，並將您的設定以程式碼的形式儲存在安全的位置，來強化您的 AKS 部署。

- [定義 Kubernetes 叢集](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

AKS 代理程式節點主機 OS 的安全性強化

security-hardened-vm-host-image.md

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導** 方針：不適用 AZURE KUBERNETES SERVICE (AKS) 。 根據預設，AKS 會提供安全性優化主機作業系統 (OS) 。 目前沒有選項可選取替代或自訂的作業系統。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 Azure 原則來限制可使用內建原則定義在訂用帳戶中建立的資源類型，以及 ">microsoft.containerservice" 命名空間中 Azure 原則別名的限制。 

建立自訂原則來進行審核，並強制執行系統設定。 開發用於管理原則例外狀況的處理常式和管線。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導** 方針： AZURE KUBERNETES SERVICE (AKS) 叢集部署在具有安全性優化 OS 的主機虛擬機器上。 主機 OS 已將額外的安全性強化步驟納入其中，以減少攻擊面，並以安全的方式允許部署容器。 

請參閱 AKS 主機內建的網際網路安全性中心清單 (CIS) 控制項。  

- [AKS 代理程式節點主機 OS 的安全性強化](security-hardened-vm-host-image.md)

- [瞭解 AKS 虛擬機器主機中的安全性強化](security-hardened-vm-host-image.md)

- [瞭解 AKS 叢集的狀態設定](concepts-clusters-workloads.md#control-plane)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用 [安全性中心] 針對 AZURE KUBERNETES SERVICE (AKS) 部署的相關資源執行基準掃描。 範例資源包括但不限於 AKS 叢集本身、部署 AKS 叢集的虛擬網路、用來追蹤 Terraform 狀態的 Azure 儲存體帳戶，或是用於 AKS 叢集作業系統和資料磁片之加密金鑰的 Azure Key Vault 實例。

- [如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導** 方針：使用 [計算應用程式] 區段下的 [安全性中心] 容器建議 &amp; ，為 Azure Kubernetes Service (AKS) 叢集執行基準掃描。 發現設定問題或弱點時，在 [安全性中心] 儀表板中收到通知。 這需要啟用選用的容器登錄套件組合，讓安全性中心可以掃描映射。  

- [了解 Azure 資訊安全中心容器建議](../security-center/container-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：使用 FlexVolume 磁片磁碟機整合 Azure Key Vault 與 AZURE KUBERNETES SERVICE (AKS) 叢集。 使用 Azure Key Vault 儲存和定期輪替秘密，例如認證、儲存體帳戶金鑰或憑證。 FlexVolume 驅動程式可讓 AKS 叢集從 Key Vault 原生擷取認證，並只會將認證安全地提供給提出要求的 Pod。 使用 pod 受控身分識別來要求 Key Vault 的存取權，並透過 FlexVolume 驅動程式取得所需的認證。 確保已啟用 Key Vault 虛刪除。 

不要在應用程式程式碼中定義認證，以限制認證暴露。 

請避免使用固定或共用的認證。 

- [Azure Kubernetes Service (AKS) 中應用程式和叢集的安全性概念](concepts-security.md) (機器翻譯)

- [如何搭配 AKS 叢集使用 Key Vault](developer-best-practices-pod-security.md#limit-credential-exposure)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：請勿在您的應用程式程式碼中定義認證，作為安全性最佳作法。 使用適用于 Azure 資源的受控識別，讓 pod 針對 Azure 中支援它的任何服務（包括 Azure Key Vault）進行自我驗證。 系統會將 Azure 身分識別指派給 pod，以驗證 Azure Active Directory (Azure AD) 並接收可提供給其他 Azure 服務的數位權杖，以檢查 pod 是否已獲授權存取服務並執行必要的動作。 

請注意，Pod 受控識別僅適用于 Linux Pod 和容器映射。 布建 Azure Key Vault，以儲存及取出數位金鑰和認證。 金鑰（例如用來加密作業系統磁片的金鑰）、AKS 叢集資料可以儲存在 Azure Key Vault 中。

服務主體也可以在 AKS 叢集中使用。 不過，使用服務主體的叢集最終可能會達到必須更新服務主體以保持叢集運作的狀態。 管理服務主體會增加複雜度，因此更容易使用受控識別。 相同的許可權需求適用于服務主體和受控識別。

- [使用 Azure Kubernetes Service (AKS 來瞭解受控識別和 Key Vault) ](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Azure Active Directory Pod 身分識別](https://github.com/Azure/aad-pod-identity)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault 與建議。

不要在應用程式程式碼中定義認證，以限制認證暴露。 並避免使用共用認證。 Azure Key Vault 應該用來儲存和取出數位金鑰和認證。 使用 Azure 資源的受控身分識別，可讓 Pod 要求存取其他資源。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [適用于 pod 安全性的開發人員最佳做法](developer-best-practices-pod-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導** 方針： AKS 管理代理程式節點的生命週期和作業，代表您修改與代理程式節點相關聯的 IaaS 資源，不受支援。 不過，針對 Linux 節點，您可以使用 daemon 集合來安裝自訂軟體，例如反惡意程式碼解決方案。

- [安全性警示參考指南](../security-center/alerts-reference.md)

- [容器的警示-Azure Kubernetes Service 叢集](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 共同責任和 Daemon 集合](support-policies.md#shared-responsibility)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：預先掃描即將上傳至 AKS 資源的任何檔案。 如果使用 Azure 儲存體帳戶作為資料存放區，或追蹤 AKS 叢集的 Terraform 狀態，請使用資料服務的安全中心威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。 

- [瞭解 Azure 資訊安全中心的資料服務威脅偵測](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3：確定已更新反惡意程式碼軟體和簽章

**指導** 方針： AKS 管理代理程式節點的生命週期和作業，代表您修改與代理程式節點相關聯的 IaaS 資源，不受支援。 不過，針對 Linux 節點，您可以使用 daemon 集合來安裝自訂軟體，例如反惡意程式碼解決方案。

- [安全性警示參考指南](../security-center/alerts-reference.md)

- [容器的警示-Azure Kubernetes Service 叢集](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 共同責任和 Daemon 集合](support-policies.md#shared-responsibility)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：使用適用于儲存體類型的適當工具來備份您的資料，例如 Velero，它可以備份永久性磁片區以及額外的叢集資源和設定。 定期驗證這些備份的完整性和安全性。 

請先從您的應用程式移除狀態，再進行備份。 在無法完成這項作業的情況下，請從持續性磁片區備份資料，並定期測試還原作業，以確認資料完整性以及所需的處理常式。

- [AKS 中儲存和備份的最佳作法](operator-best-practices-storage.md)

- [AKS 中的商務持續性和嚴重損壞修復的最佳作法](operator-best-practices-multi-region.md)

- [瞭解 Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [如何在 Azure 上設定 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：使用適用于儲存體類型的適當工具來備份您的資料，例如 Velero，它可以備份永久性磁片區以及額外的叢集資源和設定。 

使用 PowerShell 命令執行 Key Vault 憑證、金鑰、受控儲存體帳戶和密碼的定期自動備份。 

例如：

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [如何備份 Key Vault 憑證](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [如何備份 Key Vault 金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [如何備份受管理的儲存體帳戶 Key Vault](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [如何備份 Key Vault 秘密](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [如何啟用 Azure 備份](../backup/index.yml)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：定期執行 Velero 備份內的內容資料還原。 如有必要，請測試還原至隔離的虛擬網路。

使用 PowerShell 命令定期執行 Key Vault 憑證、金鑰、受控儲存體帳戶和密碼的資料還原。 

例如：

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [如何還原 Key Vault 憑證](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [如何還原 Key Vault 金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [如何還原 Key Vault 受控儲存體帳戶](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [如何還原 Key Vault 秘密](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [如何從 Azure 虛擬機器備份復原檔案](../backup/backup-azure-restore-files-from-vm.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：使用適用于儲存體類型的適當工具來備份您的資料，例如 Velero，它可以備份永久性磁片區以及額外的叢集資源和設定。 

如果 Azure Key Vault 用於 Azure Kubernetes Service (AKS) 部署，請在 Key Vault 中啟用 Soft-Delete，以防止意外或惡意刪除的金鑰。

- [瞭解 Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)

- [如何在 Key Vault 中啟用虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

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

**指導** 方針：排定必須先調查哪些警示，並將安全性中心指派的嚴重性設為警示。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。
清楚地將訂用帳戶標示 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 如有必要，請參閱 NIST 的發行集，以找出弱點和差距和修訂計畫。

- [適用于 IT 方案和功能的測試、訓練和練習程式指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出安全性中心警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您也可以選擇資訊安全中心資料連線器，根據組織的商務需求，將警示串流至 Azure Sentinel。

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

- [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)