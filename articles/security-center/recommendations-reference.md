---
title: Azure 資訊安全中心建議完整參考資料表
description: 本文列出 Azure 資訊安全中心的安全性建議，可協助保護您的資源。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2020
ms.author: memildin
ms.openlocfilehash: ff988dd382c5df5d764c7ab29e8a469f510ba7f9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529330"
---
# <a name="security-recommendations---a-reference-guide"></a>安全性建議 - 參考指南

本文列出您可能會在 Azure 資訊安全中心看到的建議。 在您的環境中顯示的建議，取決於您要保護的資源和自訂設定。

資訊安全中心的建議是以最佳做法為基礎。 有些會與**Azure 安全性基準測試**一致，這是 Microsoft 撰寫的 azure 特定指導方針，適用于以通用合規性架構為基礎的安全性和合規性最佳作法。 [深入瞭解 Azure 安全性基準測試](https://docs.microsoft.com/azure/security/benchmarks/introduction)。

若要了解如何回應這些建議，請參閱 [Azure 資訊安全中心的補救建議](security-center-remediate-recommendations.md)。

您的安全分數是根據您所完成的資訊安全中心建議數目而定。 若要判斷應優先處理哪些建議，請著眼於各項的嚴重性，以及該項目對您安全分數的潛在影響。

>[!TIP]
> 如果建議的描述顯示「無相關原則」，通常是因為該建議取決於其他建議及其原則。 例如「應補救失敗的端點保護健康情況...」這項建議，是取決於另一項負責檢查是否已安裝端點保護解決方案的建議 (「應安裝端點保護解決方案...」)。 基礎建議則確實具備原則。 將原則限制為只有基本建議，可簡化原則管理。

## <a name="network-recommendations"></a><a name="recs-network"></a>網路建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**應在網際網路對應虛擬機器中套用自適性網路強化建議**|當自適性網路強化功能找出過於寬鬆的 NSG 規則時，標準定價層的客戶會看見此建議。<br>(相關原則：應在網際網路對應的虛擬機器上套用自適性網路強化建議)|高|否|虛擬機器|
|**在與您 VM 建立關聯的 NSG 上應限制所有網路連接埠**|藉由限制現有允許規則的存取，強化網際網路對應 VM 的網路安全性群組。<br>當任何連接埠對所有來源開放時 (除了連接埠 22、3389、5985、5986、80 和 1443 以外)，會觸發此建議。<br>(相關原則：應限制透過網際網路對應端點進行存取)|高|否|虛擬機器|
|**應啟用 DDoS 保護標準**|藉由啟用 DDoS 保護服務標準，保護其中應用程式具有公用 IP 的虛擬網路。 DDoS 保護可降低網路大流量攻擊和通訊協定攻擊的風險。<br>(相關原則：應啟用 Azure DDoS 保護)|高|否|虛擬網路|
|**函式應用程式應只可經由 HTTPS 存取**|啟用函式應用程式的「僅限 HTTPS」存取。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。<br>(相關原則：函式應用程式應只可經由 HTTPS 存取)|中|**Y**|函式應用程式|
|**應使用網路安全性群組保護網際網路對應的虛擬機器**|啟用網路安全性群組以控制虛擬機器的網路存取。<br>(相關原則：應使用網路安全性群組保護網際網路對應的虛擬機器)|高/中|N|虛擬機器|
|**應使用網路安全性群組保護非網際網路對應的虛擬機器**|    藉由使用網路安全性群組（NSG）限制對您的非網際網路對向虛擬機器，避免潛在威脅。<br>Nsg 包含存取控制清單（ACL），而且可以指派給 VM 的 NIC 或子網。 ACL 規則可允許或拒絕已指派資源的網路流量。<br>（相關原則：非網際網路面向的虛擬機器應該使用網路安全性群組來保護）|低|N|虛擬機器|
|**應停用虛擬機器上的 IP 轉送**|停用 IP 轉送。 在虛擬機器的 NIC 上啟用 IP 轉送時，機器會收到傳送到其他目的地的流量。 IP 轉送是極少需要的功能 (例如將 VM 作為網路虛擬設備使用時)，因此這應經過網路安全性小組檢閱。<br>(相關原則：[預覽]：應停用虛擬機器上的 IP 轉送)|中|N|虛擬機器|
|**應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠**|套用 Just-in-Time (JIT) 虛擬機器 (VM) 存取控制，以永久鎖定對所選連接埠的存取，並讓授權使用者能透過 JIT 在一段有限時間內開啟這些連接埠。<br>(相關原則：應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠)|高|否|虛擬機器|
|**應關閉虛擬機器上的管理連接埠**|強化虛擬機器的網路安全性群組，以限制對管理連接埠的存取。<br>(相關原則：應關閉虛擬機器上的管理連接埠)|高|否|虛擬機器|
|**應啟用儲存體帳戶的安全傳輸**|啟用儲存體帳戶的安全傳輸。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。<br>(相關原則：應啟用儲存體帳戶的安全傳輸)|高|**Y**|儲存體帳戶|
|**子網路應該與網路安全性群組建立關聯**|啟用網路安全性群組以控制子網路中所部署資源的網路存取。<br>(相關原則：子網路應該與網路安全性群組建立關聯。<br>預設會停用此原則)|高/中|N|子網路|
|**Web 應用程式應只可經由 HTTPS 存取**|啟用 Web 應用程式的「僅限 HTTPS」存取。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。<br>(相關原則：Web 應用程式應只可經由 HTTPS 存取)|中|**Y**|Web 應用程式|
||||||


## <a name="container-recommendations"></a><a name="recs-containers"></a>容器建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**Kubernetes Services 上應定義授權 IP 範圍**|僅將 API 存取權授與特定範圍內的 IP 位址，以限制對 Kubernetes Service 管理 API 的存取。 建議設定授權 IP 範圍，僅限來自允許網路的應用程式存取叢集。<br>(相關原則：[預覽]：Kubernetes Services 上應定義授權 IP 範圍)|高|否|計算資源 (容器)|
|**應定義 Pod 安全性原則，藉由移除不必要的應用程式權限，減少攻擊媒介 (預覽)**|移除不必要的應用程式權限來定義 Pod 安全性原則，以減少攻擊媒介。 建議設定 Pod 安全性原則，如此 Pod 只能存取有權存取的資源。<br>(相關原則：[預覽]：Kubernetes Services 上應定義 Pod 安全性原則)|中|N|計算資源 (容器)|
|**應使用角色型存取控制來限制對 Kubernetes Service 叢集的存取**|若要提供使用者可執行動作的細微篩選，請使用角色型存取控制 (RBAC) 來管理 Kubernetes Service 叢集中的權限，並設定相關的授權原則。 如需詳細資訊，請參閱 [Azure 角色型存取控制](https://docs.microsoft.com/azure/aks/concepts-identity#role-based-access-controls-rbac)。<br>(相關原則：[預覽]：應在 Kubernetes Service 上使用角色型存取控制 (RBAC))|中|N|計算資源 (容器)|
|**應將 Kubernetes Service 升級為最新的 Kubernetes 版本**|將 Azure Kubernetes Service 叢集升級為最新的 Kubernetes 版本，以使用最新的弱點修補程式。 如需特定 Kubernetes 弱點的相關詳細資料，請參閱 [Kubernetes CVE](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=kubernetes)。<br>(相關原則：[預覽]：應將 Kubernetes Services 升級為不易受攻擊的 Kubernetes 版本)|高|否|計算資源 (容器)|
|**Azure Container Registry 映像中應予補救的弱點 (Qualys 技術提供)**|容器映像弱點評定會掃描您的登錄，以判斷每個推送的容器映像是否有安全性弱點，並公開每個映像的詳細結果。 解決這些弱點可以大幅改善容器的安全性狀態，並保護容器免於遭受攻擊。<br>(無相關原則)|高|否|計算資源 (容器)|
||||||


## <a name="app-service-recommendations"></a><a name="recs-appservice"></a>App Service 建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**Web 應用程式應只可經由 HTTPS 存取**|啟用 Web 應用程式的「僅限 HTTPS」存取。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。<br>(相關原則：Web 應用程式應只可經由 HTTPS 存取)|中|**Y**|App Service|
|**函式應用程式應只可經由 HTTPS 存取**|啟用函式應用程式的「僅限 HTTPS」存取。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。<br>(相關原則：函式應用程式應只可經由 HTTPS 存取)|中|**Y**|App Service|
|**API 應用程式應只可經由 HTTPS 存取**|僅限透過 HTTPS 存取 API Apps。<br>(相關原則：API 應用程式應只可經由 HTTPS 存取)|中|N|App Service|
|**應關閉 Web 應用程式的遠端偵錯**|如果您不再需要使用 Web 應用程式的偵錯功能，請將該功能關閉。 遠端偵錯需要在 Web 應用程式上開啟輸入連接埠。<br>(相關原則：應關閉 Web 應用程式的遠端偵錯)|低|**Y**|App Service|
|**應關閉函式應用程式的遠端偵錯**|如果您不再需要使用函數應用程式的偵錯功能，請將該功能關閉。 遠端偵錯需要在函式應用程式上開啟輸入連接埠。<br>(相關原則：應關閉函式應用程式的遠端偵錯)|低|**Y**|App Service|
|**應關閉 API 應用程式的遠端偵錯**|如果您不再需要使用 API 應用程式的偵錯功能，請將該功能關閉。 遠端偵錯需要在 API 應用程式上開啟輸入連接埠。<br>(相關原則：應關閉 API 應用程式的遠端偵錯)|低|**Y**|App Service|
|**CORS 不應允許每項資源存取您的 Web 應用程式**|請只允許必要網域與您的 Web 應用程式互動。 跨原始資源共用 (CORS) 不應允許所有網域存取 Web 應用程式。<br>(相關原則：CORS 不應允許每項資源存取您的 Web 應用程式)|低|**Y**|App Service|
|**CORS 不應允許每項資源存取函式應用程式**|請只允許必要網域與您的函數應用程式互動。 跨原始資源共用 (CORS) 不應允許所有網域存取函式應用程式。<br>(相關原則：CORS 不應允許每項資源存取函式應用程式)|低|**Y**|App Service|
|**CORS 不應允許每項資源存取您的 API 應用程式**|僅允許必要網域與您的 API 應用程式互動。 跨原始資源共用 (CORS) 不應允許所有網域存取您的 API 應用程式。<br>(相關原則：CORS 不應允許每項資源存取您的 API 應用程式)|低|**Y**|App Service|
|**應在 App Service 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應在應用程式服務中啟用診斷記錄)</span>|低|N|App Service|
||||||


## <a name="compute-and-app-recommendations"></a><a name="recs-computeapp"></a>計算和應用程式建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**應啟用 Azure 串流分析中的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應啟用 Azure 串流分析中的診斷記錄)|低|**Y**|計算資源 (串流分析)|
|**應啟用 Batch 帳戶中的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應啟用 Batch 帳戶中的診斷記錄)|低|**Y**|計算資源 (Batch)|
|**應啟用事件中樞內的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應啟用事件中樞內的診斷記錄)|低|**Y**|計算資源 (事件中樞)|
|**應在 Logic Apps 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應在 Logic Apps 中啟用診斷記錄)|低|**Y**|計算資源 (Logic Apps)|
|**應在搜尋服務中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應在搜尋服務中啟用診斷記錄)|低|**Y**|計算資源 (搜尋)|
|**應在服務匯流排中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應在服務匯流排中啟用診斷記錄)|低|**Y**|計算資源 (服務匯流排)|
|**Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證**|在 Service Fabric 中只透過 Azure Active Directory 來執行用戶端驗證。<br>(相關原則：Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證)|高|否|計算資源 (Service Fabric)|
|**Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign**|Service Fabric 使用主要叢集憑證，可為節點對節點的通訊提供三層保護 (None、Sign 及 EncryptAndSign)。 設定保護層級可確保所有節點對節點的訊息皆經過加密及數位簽署。<br>(相關原則：應將 Service Fabric 中的 ClusterProtectionLevel 屬性設為 EncryptAndSign)|高|否|計算資源 (Service Fabric)|
|**除了 RootManageSharedAccessKey 外，應從服務匯流排命名空間移除所有授權規則**|服務匯流排用戶端不應該使用提供命名空間中所有佇列及主題存取權的命名空間層級存取原則。 若要與最低權限資訊安全模型達成一致，您應在佇列和主題的實體層級建立存取原則，以提供僅限特定實體的存取權。<br>(相關原則：除了 RootManageSharedAccessKey 外，應從服務匯流排命名空間移除所有授權規則)|低|N|計算資源 (服務匯流排)|
|**除了 RootManageSharedAccessKey 外，應從事件中樞命名空間移除所有授權規則**|事件中樞用戶端不應該使用提供命名空間中所有佇列及主題存取權的命名空間層級存取原則。 若要與最低權限資訊安全模型達成一致，您應在佇列和主題的實體層級建立存取原則，以提供僅限特定實體的存取權。<br>(相關原則：除了 RootManageSharedAccessKey 外，應從事件中樞命名空間移除所有授權規則)|低|N|計算資源 (事件中樞)|
|**應對事件中樞實體定義授權規則**|稽核事件中樞實體上的授權規則，以只授與最低權限的存取權。<br>(相關原則：應對事件中樞實體定義授權規則)|低|N|計算資源 (事件中樞)|
|**請在您的虛擬機器上安裝監視代理程式**|安裝 Monitoring Agent，以在每部機器上啟用資料收集、更新掃描、基準掃描及端點保護。<br>(無相關原則)|高|**Y**|電腦|
|**Log Analytics 代理程式應該安裝在您的以 Windows 為基礎的 Azure Arc 機器上（預覽）**|資訊安全中心使用[Log Analytics 代理程式](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)（也稱為 MMA）從您的 Azure Arc 機器收集安全性事件。<br>（相關原則： [預覽]： Log Analytics 代理程式應該安裝在您的 Windows Azure Arc 機器上）|高|**Y**|Azure Arc 機器|
|**Log Analytics 代理程式應該安裝在您的以 Linux 為基礎的 Azure Arc 機器上（預覽）**|資訊安全中心使用[Log Analytics 代理程式](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)（也稱為 MMA）從您的 Azure Arc 機器收集安全性事件。<br>（相關原則： [預覽]： Log Analytics 代理程式應該安裝在您的 Linux Azure Arc 機器上）|高|**Y**|Azure Arc 機器|
|**來賓設定延伸模組應該安裝在 Windows 虛擬機器上（預覽）**|安裝來賓設定代理程式，以啟用電腦內的審核設定，例如：作業系統設定、應用程式設定或目前狀態、環境設定。 安裝之後，就可以使用來賓內的原則，例如「應啟用 Windows 惡意探索防護」。<br>（相關原則：在 Windows Vm 上啟用來賓設定原則的 Audit 必要條件）|高|**Y**|電腦|
|**應在您的電腦上啟用 Windows Defender 惡意探索防護（預覽）**|Windows Defender 惡意探索防護會利用 Azure 原則來賓設定代理程式。 「惡意探索防護」有四個元件，設計用來鎖定裝置，使其免于遭受惡意程式碼攻擊的各種攻擊和封鎖行為，同時讓企業能夠平衡他們的安全性風險和生產力需求（僅限 Windows）。<br>（相關原則：未啟用 Windows Defender 惡意探索防護功能的 Audit Windows Vm）|中|N|電腦|
|**應解決機器上的監視代理程式健康情況問題**|為了獲得完整的「資訊安全中心」保護，請依照《疑難排解指南》中的指示，解決機器上的監視代理程式問題<br>(無相關原則 - 取決於「請在您的虛擬機器上安裝監視代理程式」)|中|N|電腦|
|**應在虛擬機器上啟用自適性應用程式控制**|啟用應用程式控制，以控制哪些應用程式可在您位於 Azure 中的 VM 上執行。 這可協助強化您 VM 抵禦惡意程式碼的能力。 「資訊安全中心」會利用機器學習服務來分析在每個 VM 上執行的應用程式，並協助您利用此情報來套用允許規則。 此功能可簡化設定及維護應用程式允許規則的程序。<br>(相關原則：應在虛擬機器上啟用自適性應用程式控制)|高|N|電腦|
|**請在您的機器上安裝 Endpoint Protection 解決方案**|在 Windows 和 Linux 機器上安裝 Endpoint Protection 解決方案，以避免虛擬機器遭受威脅及弱點損害。<br>(相關原則：在 Azure 資訊安全中心中監視缺少的 Endpoint Protection)|中|N|電腦|
|**請在虛擬機器上安裝 Endpoint Protection 解決方案**|在虛擬機器上安裝 Endpoint Protection 解決方案，以避免虛擬機器遭受威脅及弱點損害。<br>(無相關原則)|中|N|電腦|
|**應更新雲端服務角色的作業系統版本**|將您雲端服務角色的作業系統 (OS) 版本更新至您 OS 系列可用的最新版本。<br>(無相關原則)|高|N|電腦|
|**您應在機器上安裝系統更新**|安裝缺少的系統安全性與重大更新，以保護您的 Windows 及 Linux 虛擬機器與電腦<br>(相關原則：您應在機器上安裝系統更新)|高|N|電腦|
|**應在 Linux 虛擬機器上安裝網路流量資料收集代理程式 (預覽)**|資訊安全中心使用 Microsoft Monitoring Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，如網路對應上的流量視覺效果、網路強化建議與特定網路威脅。<br>(相關原則：[預覽]：應在 Linux 虛擬機器上安裝網路流量資料收集代理程式)|中|**Y**|電腦|
|**應在 Windows 虛擬機器上安裝網路流量資料收集代理程式 (預覽)**|資訊安全中心使用 Microsoft Monitoring Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，如網路對應上的流量視覺效果、網路強化建議與特定網路威脅。<br>(相關原則：[預覽]：應在 Windows 虛擬機器上安裝網路流量資料收集代理程式)|中|**Y**|電腦|
|**在虛擬機器上啟用內建弱點評定解決方案**|安裝 Qualys 代理程式 (內建 Azure 資訊安全中心標準層供應項目)，以在您的虛擬機器上啟用最優秀的弱點評定解決方案。<br>(相關原則：[預覽] 應在虛擬機器上啟用弱點評定)|中|**Y**|電腦|
|**補救在您的虛擬機器上發現的弱點 (Qualys 技術提供)**|監視 Azure 資訊安全中心內建弱點評定解決方案在您虛擬機器上發現的弱點 (Qualys 技術提供)。<br>(相關原則：[預覽] 應在虛擬機器上啟用弱點評定)|低|N|電腦|
|**應重新啟動機器以套用系統更新**|重新啟動機器，以套用系統更新及避免機器受到弱點損害。<br>(無相關原則 - 取決於「您應在機器上安裝系統更新」)|中|N|電腦|
|**應加密自動化帳戶變數**|儲存敏感性資料時，啟用自動化帳戶變數資產加密。<br>(相關原則：應對自動化帳戶變數啟用加密)|高|否|計算資源 (自動化帳戶)|
|**應在虛擬機器上套用磁碟加密**|使用「Azure 磁碟加密」為 Windows 和 Linux 虛擬機器磁碟加密虛擬機器磁碟。 「Azure 磁碟加密」(ADE) 利用 Windows 的業界標準 BitLocker 功能及 Linux 的 DM-Crypt 功能來提供 OS 和資料磁碟加密，以協助您在客戶 Azure Key Vault 中保護資料並滿足組織的安全性與合規性承諾。 當您的合規性與安全性需求要求您使用加密金鑰來進行端對端資料加密 (包括加密暫時磁碟 (本機連結的臨時磁碟)) 時，請使用「Azure 磁碟加密」。 或者，系統預設會使用「Azure 儲存體服務加密」將待用的「受控磁碟」加密，其中加密金鑰為 Azure 中的 Microsoft 受控金鑰。 如果這符合您的合規性與安全性需求，您便可以利用預設的受控磁碟加密來滿足您的需求。<br>(相關原則：應在虛擬機器上套用磁碟加密)|高|N|電腦|
|**虛擬機器應遷移到新的 Azure Resource Manager 資源**|在您的虛擬機器使用 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 Resource Manager 為基礎的部署及治理、受控身分識別的存取、金鑰保存庫的祕密存取、以 Azure AD 為基礎的驗證，並支援標籤和資源群組，讓安全性管理更加輕鬆。<br>(相關原則：虛擬機器應遷移至新的 Azure Resource Manager 資源)|低|N|電腦|
|**虛擬機器上應安裝弱點評定解決方案**|在您的虛擬機器上安裝弱點評定解決方案<br>(相關原則：弱點評定解決方案應修復弱點)|中|N|電腦|
|**弱點評量解決方案應修復弱點**|針對已部署弱點評定第三方解決方案的虛擬機器，持續評定是否有應用程式和 OS 弱點。 每當找到這類弱點時，都會隨著建議一併提供這些弱點的相關詳細資訊。<br>(相關原則：弱點評定解決方案應修復弱點)|高|否|電腦|
|**您應在機器上修復安全性組態的弱點**|修復機器上安全性設定中的弱點，以避免這些機器遭受攻擊。<br>(相關原則：您應在機器上修復安全性組態的弱點)|低|N|電腦|
|**應補救容器安全性設定中的弱點**|修複已安裝 Docker 之機器上安全性設定中的弱點，以避免這些機器遭受攻擊。<br>(相關原則：應補救容器安全性設定中的弱點)|高|否|電腦|
|**應解決您機器上端點保護健康情況的問題**|為了獲得完整的「資訊安全中心」保護，請依照《疑難排解指南》中的指示，解決機器上的監視代理程式問題。<br>(此建議取決於「請在您的機器上安裝端點保護解決方案」的建議及其原則)|中|N|電腦|
||||||


## <a name="virtual-machine-scale-set-recommendations"></a><a name="recs-vmscalesets"></a>虛擬機器擴展集建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**應在虛擬機器擴展集中啟用診斷記錄**|啟用記錄並保留記錄最長一年。 這可讓您重新建立活動線索來進行調查。 當發生安全性事件或您的網路遭到入侵時，這非常有用。<br>(相關原則：應在虛擬機器擴展集中啟用診斷記錄)|低|N|虛擬機器擴展集|
|**應補救虛擬機器擴展集上失敗的端點保護健康情況**|修復虛擬機器擴展集的端點保護健康狀態失敗，避免其遭受威脅與弱點的傷害。<br>(無相關原則 - 取決於「應在虛擬機器擴展集上安裝端點保護解決方案」)|低|N|虛擬機器擴展集|
|**應在虛擬機器擴展集上安裝端點保護解決方案**|在虛擬機器擴展集上安裝端點保護解決方案，避免其遭受威脅及弱點的傷害。<br>(相關原則：應在虛擬機器擴展集上安裝端點保護解決方案)|高|N|虛擬機器擴展集|
|**應於虛擬機器擴展集上安裝監視代理程式**|資訊安全中心使用 Microsoft Monitoring Agent (MMA) 從 Azure 虛擬機器擴展集收集安全性事件。 您無法為 Azure 虛擬機器擴展集設定 MMA 的自動佈建。 若要在虛擬機器擴展集上部署 MMA (包括 Azure 受控服務使用的擴展集，例如 Azure Kubernetes Service 和 Azure Service Fabric)，請遵循修復步驟中的程序。|高|**Y**|虛擬機器擴展集|
|**應在虛擬機器擴展集上安裝系統更新**|安裝遺漏的系統安全性及重大更新，以保護您的 Windows 與 Linux 虛擬機器擴展集。<br>(相關原則：應在虛擬機器擴展集上安裝系統更新)|高|N|虛擬機器擴展集|
|**應修復虛擬機器擴展集上安全性組態的弱點**|修復虛擬機器擴展集之安全性設定中的弱點，避免其遭受攻擊。 <br>(相關原則：應修復虛擬機器擴展集上安全性組態的弱點)|高|N|虛擬機器擴展集|
||||||


## <a name="data-and-storage-recommendations"></a><a name="recs-datastorage"></a>資料和儲存體建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**應限制存取具防火牆與虛擬網路設定的儲存體帳戶**|稽核儲存體帳戶防火牆設定中不受限制的網路存取。 請改為設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，可將存取權授與來自特定 Azure 虛擬網路的流量，或授與公用網際網路 IP 位址範圍。<br>(相關原則：稽核不受限制的儲存體帳戶網路存取)|低|N|儲存體帳戶|
|**應在受控執行個體上啟用進階資料安全性**|進階資料安全性 (ADS) 是提供進階 SQL 安全性功能的整合套件。 其可探索和分類敏感性資料、找出潛在資料庫弱點並減低其風險，及偵測可能代表資料庫受威脅的異常活動。 廣告的費用為每個受控實例 $15。<br>（相關原則：您應該在 SQL 受控執行個體上啟用 Advanced data security）|高|**Y**|SQL|
|**應在 SQL 伺服器上啟用進階資料安全性**|進階資料安全性 (ADS) 是提供進階 SQL 安全性功能的整合套件。 其可探索和分類敏感性資料、找出潛在資料庫弱點並減低其風險，及偵測可能代表資料庫受威脅的異常活動。 ADS 的費用是每個 SQL 伺服器 $15 美元。<br>(相關原則：應在 SQL 伺服器上啟用進階資料安全性)|高|**Y**|SQL|
|**應為 SQL Database 布建 Azure Active Directory 系統管理員**|為您的 SQL Database 布建 Azure AD 系統管理員，以啟用 Azure AD 驗證。 Azure AD 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。<br>(相關原則：稽核 SQL 伺服器的 Azure Active Directory 管理員佈建)|高|否|SQL|
|**應該啟用 SQL Database 上的審核**|啟用 SQL Database 的審核。 <br>（相關原則：您應該針對伺服器的 advanced data security 設定，啟用 SQL Database 的審核）|低|**Y**|SQL|
|**應在 Azure Data Lake Store 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應在 Azure Data Lake Store 中啟用診斷記錄)|低|**Y**|Data Lake Store|
|**應在 Data Lake Analytics 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應在 Data Lake Analytics 中啟用診斷記錄)|低|**Y**|Data Lake 分析|
|**應該只允許對 Redis Cache 的安全連線**|只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。<br>(相關原則：應該只允許對 Azure Cache for Redis 的安全連線)|高|N|Redis|
|**應啟用儲存體帳戶的安全傳輸**|安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。<br>(相關原則：應啟用儲存體帳戶的安全傳輸)|高|否|儲存體帳戶|
|**應分類 SQL 資料庫中的敏感性資料**|Azure SQL Database 資料探索 & 分類可提供功能來探索、分類、標記和保護資料庫中的敏感性資料。 一旦您的資料經過分類，您就可以使用 Azure SQL Database 的審核來審核存取和監視敏感性資料。 Azure SQL Database 也會啟用先進的威脅防護功能，根據機密資料的存取模式變更來建立智慧型警示。<br>(相關原則：[預覽]：應分類 SQL 資料庫中的敏感性資料)|高|否|SQL|
|**儲存體帳戶應移轉至新的 Azure Resource Manager 資源**|在您的儲存體帳戶使用新的 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 Resource Manager 為基礎的部署及治理、受控身分識別的存取、金鑰保存庫的祕密存取，以及以 Azure AD 為基礎的驗證，還支援標記和資源群組，讓安全性管理更加輕鬆。<br>(相關原則：儲存體帳戶應移轉至新的 Azure Resource Manager 資源)|低|N|儲存體帳戶|
|**應在 SQL 資料庫上啟用透明資料加密**|啟用透明資料加密，以保護待用資料及符合合規要求。<br>(相關原則：應在 SQL 資料庫上啟用透明資料加密)|低|**Y**|SQL|
|**應該在 SQL Database 上啟用弱點評估**|弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。<br>(相關原則：弱點評估應於您的 SQL 伺服器上啟用)|高|**Y**|SQL|
|**應在 SQL 受控執行個體上啟用弱點評定**|弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。<br>（相關原則：弱點評估應該在 SQL 受控執行個體上啟用）|高|**Y**|SQL|
|**應補救機器上 SQL server 上的弱點**|SQL 弱點評定會掃描資料庫有無安全性弱點，並公開任何與最佳做法不同的差異，例如設定不正確、過多權限，以及未保護敏感性資料等等。 解決找到的弱點可以大幅改進資料庫的安全性等級。|高|否|SQL|
|**應修復 SQL 資料庫的弱點**|SQL 弱點評定會掃描資料庫有無安全性弱點，並公開任何與最佳做法不同的差異，例如設定不正確、過多權限，以及未保護敏感性資料等等。 解決找到的弱點可以大幅改進資料庫的安全性等級。<br>(相關原則：應修復 SQL 資料庫的弱點)|高|N|SQL|
||||||


## <a name="identity-and-access-recommendations"></a><a name="recs-identity"></a>身分識別與存取建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA**|為具有讀取權限的所有訂用帳戶啟用 Multi-Factor Authentication (MFA)，以避免發生帳戶或資源資料外洩。<br>(相關原則：應在您訂閱上具有讀取權限的帳戶上啟用 MFA)|高|N|訂用帳戶|
|**應在您訂閱上具有寫入權限的帳戶上啟用 MFA**|為具有寫入權限的所有訂用帳戶啟用 Multi-Factor Authentication (MFA)，以避免發生帳戶或資源資料外洩。<br>(相關原則：應在您訂閱上具有寫入權限的帳戶上啟用 MFA)|高|N|訂用帳戶|
|**應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA**|為具有擁有者權限的所有訂閱啟用 Multi-Factor Authentication (MFA)，以避免發生帳戶或資源資料外洩。<br>(相關原則：應在您訂閱上具有擁有者權限的帳戶上啟用 MFA)|高|N|訂用帳戶|
|**具有讀取權限的外部帳戶應該從您的訂用帳戶中移除**|從訂用帳戶中移除具有讀取權限的外部帳戶，以避免出現未受監視的存取。<br>(相關原則：具有讀取權限的外部帳戶應該從您的訂閱中移除)|高|N|訂用帳戶|
|**應從訂用帳戶移除具有寫入權限的外部帳戶**|從訂閱中移除具有讀取權限的外部帳戶，以避免出現未受監視的存取。<br>(相關原則：應從訂閱移除具有寫入權限的外部帳戶)|高|N|訂用帳戶|
|**具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除**|從訂閱中移除具有擁有者權限的外部帳戶，以避免出現未受監視的存取。<br>(相關原則：具有擁有者權限的外部帳戶應該從您的訂閱中移除)|高|N|訂用帳戶|
|**具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除**|從訂用帳戶中移除具有擁有者權限的已取代帳戶。<br>(相關原則：具有擁有者權限的已取代帳戶應該從您的訂閱中移除)|高|N|訂用帳戶|
|**已取代帳戶應該從您的訂用帳戶中移除**|從訂用帳戶中移除不再使用的帳戶，以僅允許目前的使用者存取。<br>(相關原則：已取代帳戶應該從您的訂閱中移除)|高|N|訂用帳戶|
|**應將一個以上的擁有者指派給您的訂用帳戶**|指定多位訂用帳戶擁有者，以擁有系統管理員存取備援。<br>(相關原則：應將一個以上的擁有者指派給您的訂閱)|高|N|訂用帳戶|
|**應針對您的訂用帳戶指定最多 3 位擁有者**|指定少於三位的訂閱擁有者，以降低遭入侵擁有者導致資料外洩的可能。<br>(相關原則：應針對您的訂閱指定最多 3 位擁有者)|高|N|訂用帳戶|
|**應啟用 Key Vault 中的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>(相關原則：應啟用 Key Vault 中的診斷記錄)|低|**Y**|Key Vault|
||||||


## <a name="deprecated-recommendations"></a>遭取代的建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**應限制對應用程式服務的存取**|藉由變更網路設定，限制對應用程式服務的存取，以拒絕範圍太廣的輸入流量。<br>(相關原則：[預覽]：應限制對應用程式服務的存取)|高|N|App Service|
|**應強化 IaaS NSG 上 Web 應用程式的規則**|若在您的虛擬機器上，執行的 Web 應用程式 NSG 規則就 Web 應用程式連接埠來說過於寬鬆，請強化虛擬機器的網路安全性群組 (NSG)。<br>(相關原則：應在 IaaS 上強化 Web 應用程式的 NSG 規則)|高|N|虛擬機器|



## <a name="next-steps"></a>後續步驟
若要深入了解建議，請參閱下列文章：

* [說明如何分析資訊安全中心所提出建議的 Microsoft Learn 單元](https://docs.microsoft.com/learn/modules/identify-threats-with-azure-security-center/)
* [Azure 資訊安全中心的安全性建議](security-center-recommendations.md)
* [保護您的機器與應用程式](security-center-virtual-machine-protection.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)