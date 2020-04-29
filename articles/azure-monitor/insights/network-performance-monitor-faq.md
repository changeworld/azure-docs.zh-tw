---
title: 常見問題集：Azure 中的網路效能監控解決方案 | Microsoft Docs
description: 本文會針對 Azure 中的網路效能監控，提供常見問題。 網路效能監控（NPM）可協助您近乎即時地監視網路的效能，並偵測並找出網路效能瓶頸。
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 443e4b44633e949dd9bd55df1ec7d18ca93d6e04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79096219"
---
# <a name="network-performance-monitor-solution-faq"></a>網路效能監控解決方案常見問題集

![網路效能監控符號](media/network-performance-monitor-faq/npm-symbol.png)

本文集結了一些 Azure 內網路效能監控 (NPM) 相關的常見問題 (常見問題集)。

[網路效能監控](/azure/networking/network-monitoring-overview)是以雲端為基礎的[混合式網路監視](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md)解決方案，可協助您監視網路基礎結構中各點之間的網路效能。 也可協助您監視[服務和應用程式端點](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md)的網路連線能力，以及[監視 Azure ExpressRoute 的效能](../../azure-monitor/insights/network-performance-monitor-expressroute.md)。 

網路效能監視器會偵測網路問題，例如流量黑洞、路由錯誤，以及傳統網路監視方法無法偵測的問題。 此解決方案會在違反網路連結的臨界值時產生警示並通知您。 它可確實而及時地偵測網路效能問題，並將問題的來源限縮於特定網路區段或裝置。 

您可以在線上深入了解 [網路效能監視器](https://docs.microsoft.com/azure/networking/network-monitoring-overview) 所支援的各種功能。

## <a name="set-up-and-configure-agents"></a>安裝及設定代理程式

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>NPM 做為監控之用的節點有哪些平台需求？
下面列出了 NPM 各種功能的平台需求：

- NPM 的效能監控和服務連線能力監視功能同時支援 Windows server 和 Windows 桌上型電腦/用戶端作業系統。 支援的 Windows server 作業系統版本為 2008 SP1 或更新版本。 支援的 windows 桌面/用戶端版本包括 Windows 10、Windows 8.1、Windows 8 和 Windows 7。 
- NPM 的 ExpressRoute 監控功能僅支援 Windows 伺服器 (2008 SP1 或更高版本) 作業系統。

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>我可以在 NPM 中使用 Linux 機器作為監視節點嗎？
使用以 Linux 為基礎的節點來監視網路的功能目前為預覽狀態。 若要深入了解，請連絡您的帳戶管理員。 Linux 代理程式的監控能力僅限於 NPM 的效能監控功能，不適用於「服務連線能力監視」和「ExpressRoute 監視」功能

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>NPM 做為監控之用的節點的大小需求如何？
若要在節點虛擬機器上執行 NPM 解決方案以監控網路，節點至少需要具有 500 MB 記憶體和一個核心。 您不需要使用個別節點來執行 NPM。 執行該解決方案的節點同時也可以執行其他工作負載。 如果此解決方案使用超過5% 的 CPU，則其功能可以停止監視進程。

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>若要使用 NPM，我是否應該以直接代理程式連線節點，還是透過 System Center Operations Manager 連線節點？
「效能監視器」和「服務連線能力監視」功能都支援[以直接代理程式連接](../../azure-monitor/platform/agent-windows.md)並[透過 Operations Manager 連線](../../azure-monitor/platform/om-agents.md)的節點。

至於 ExpressRoute 監視的功能，請僅以直接代理程式連線 Azure 節點。 不支援透過 Operations Manager 連線的 Azure 節點。 針對內部部署節點，可支援連線為直接代理程式和透過 Operations Manager 的節點，以監視 ExpressRoute 線路。

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>應該選擇監視 TCP 還是 ICMP 通訊協定？
如果您使用以 Windows server 為基礎的節點監視網路，建議您使用 TCP 作為監視通訊協定，因為它提供更好的精確度。 

建議將 ICMP 用於以 Windows 桌面/用戶端作業系統為基礎的節點。 此平臺不允許透過原始通訊端傳送 TCP 資料，NPM 會使用它來探索網路拓撲。

您可以在[此處](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol)取得有關各種通訊協定彼此優勢所在的詳細資料。

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>如何使用 TCP 通訊協定設定節點以支援監視？
對於支援使用 TCP 通訊協定進行監視的節點： 
* 請確定節點平台為 Windows Server (2008 SP1 或更新版本)。
* 執行節點上的 [EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershell 指令碼。 如需詳細資料，請參閱[指示](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring)。


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>如何變更 NPM 用於監視的 TCP 通訊埠？
可以執行 [EnableRules.ps1](https://aka.ms/npmpowershellscript) 指令碼，變更 NPM 用於監視的 TCP 通訊埠。 需輸入要做為參數使用的連接埠號碼。 例如，若要在連接埠 8060 上啟用 TCP，請執行 `EnableRules.ps1 8060`。 請確定用於監視的所有節點上使用的是相同的 TCP 通訊埠。

指令碼只會在本機設定 Windows 防火牆。 如果您有網路防火牆或網路安全性群組 (NSG) 規則，請確定其允許傳送到 NPM 所使用的 TCP 通訊埠流量。

### <a name="how-many-agents-should-i-use"></a>應該使用多少代理程式？
要監視的每個子網路應使用至少一個代理程式。

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>我可以使用的代理程式數目上限，或我看到錯誤」。您已達到設定限制」？
NPM 將 IP 數目限制為每個工作區 5000 個 IP。 如果節點同時有 IPv4 和 IPv6 位址，這會當作該節點有 2 個 IP 來計算。 因此，5000 IP 的這項限制會決定代理程式數目上限。 您可以從 NPM 中的 [節點] 索引標籤 >> [設定] 刪除非使用中的代理程式。 NPM 也會維護所有已指派給裝載代理程式之 VM 的 Ip 歷程記錄，而且每個 ip 會計算為對該上限（5000個 ip）貢獻的個別 IP。 若要釋出工作區的 Ip，您可以使用 [節點] 頁面來刪除未使用的 Ip。

## <a name="monitoring"></a>監視

### <a name="how-are-loss-and-latency-calculated"></a>如何計算遺失和延遲
來源代理程式會定期向目的地 IP 傳送 TCP SYN 要求 (如果選擇 TCP 做為監視用的通訊協定)，或傳送 ICMP ECHO 要求 (如果選擇 ICMP 做為監視用的通訊協定)，以確保來源到目的地的 IP 組合之間的所有路徑均在涵蓋範圍內。 會測量接收的封包百分比和封包往返時間，以計算每個路徑的遺失和延遲。 此資料會在輪詢間隔和所有路徑上彙總，以針對特定輪詢間隔的 IP 組合取得遺失和延遲的彙總值。

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>來源代理程式將封包傳送至目的地進行監視的頻率是多久？
對於效能監控和 ExpressRoute 監視功能，來源會每 5 秒傳送一次封包，並記錄網路測量資料。 此資料會在 3 分鐘的輪詢間隔內彙總，以計算遺失和延遲的平均值和尖峰值。 對於服務連線能力監視功能，傳送網路測量封包的頻率取決於使用者在設定測試時所輸入的測試專用頻率。

### <a name="how-many-packets-are-sent-for-monitoring"></a>會傳送多少封包進行監視？
來源代理程式在輪詢中傳送到目的地的封包數量是彈性的，視專屬的演算法而定，不同的網路拓撲可能會有所差異。 來源到目的地 IP 組合之間的網路路徑數量越多，傳送的封包數量就越多。 系統可確保涵蓋來源到目的地 IP 組合之間的所有路徑。

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>NPM 如何探索來源和目的地之間的網路拓撲？
NPM 使用專用演算法來探索來源和目的地之間的所有路徑和躍點，這種演算法是以追蹤路由為基礎。

### <a name="does-npm-provide-routing-and-switching-level-info"></a>NPM 是否會提供路由和交換層級的資訊 
雖然 NPM 可以偵測來源代理程式與目的地之間的所有可能路由，但使用者無法看出特定工作負載傳送的封包所採用之路由。 此解決方案可協助找出導致比預期中延遲更久的路徑和基礎網路躍點。

### <a name="why-are-some-of-the-paths-unhealthy"></a>為什麼有些路徑狀況不良？
來源和目的地 IP 之間有不同的網路路徑，而且每個路徑可能有不同的遺失值和延遲值。 若路徑的遺失及/或延遲的值大於監視組態中設定的相應閾值，NPM 會將這些路徑標示為狀況不良 (以紅色表示)。

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>紅色躍點在網路拓撲地圖中表示什麼？
如果躍點為紅色，則表示其是至少一條狀況不良路徑的一部分。 NPM 只會將路徑標示為狀況不良，不會隔離每條路徑的健康情況狀態。 若要找出問題躍點，請逐一檢視每個躍點的延遲，並隔離導致延遲超出預期的躍點。

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>效能監控中的故障定位如何運作？
NPM 會根據其所屬之狀況不良的路徑數量，運用機率機制將故障機率指派給每個網路路徑、網路區段和組成網路躍點。 由於網路區段和躍點成為更多狀況不良路徑的一部分，與其相關聯的錯誤機率也隨之增加。 當您有許多 NPM 代理程式互相連接的節點時，此演算法的效果最佳，因為這會增加資料點以計算錯誤機率。

### <a name="how-can-i-create-alerts-in-npm"></a>如何在 NPM 中建立警示？
如需逐步說明，請參閱[說明文件中的警示章節](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts)。

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>警示的預設 Log Analytics 查詢為何
效能監視器查詢

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
服務連線能力監視查詢

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
ExpressRoute 監視查詢：線路查詢

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

私人對等互連

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Microsoft 對等互連

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

一般查詢   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>NPM 是否可以將路由器和伺服器當做個別裝置進行監控？
NPM 只會識別來源 IP 和目的地 IP 之間的基礎網路躍點 (交換器、路由器、伺服器等) 的 IP 和主機名稱， 也也會識別這些已識別躍點之間的延遲， 但不會個別監控這些基礎躍點。

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM 是否可用來監控 Azure 與 AWS 之間的網路連線？
是。 如需詳細資料，請參閱[使用 NPM 監控 Azure、AWS 和內部部署網路](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)一文 (英文)。

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>ExpressRoute 頻寬的用量是傳入還是傳出？
頻寬用量是傳入和傳出頻寬的總和， 以位元/秒表示。

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>是否可以取得 ExpressRoute 的傳入和傳出的頻寬資訊？
可以擷取主要和次要頻寬的傳入和傳出值。

如需 MS 對等互連層級資訊，請在記錄搜尋中使用下列所述的查詢

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
如需私用對等互連層級資訊，請在記錄搜尋中使用下列所述的查詢

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
如需線路層級資訊，請在記錄搜尋中使用下列所述的查詢

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>NPM 的效能監控支援哪些區域？
NPM 可以從其中一個[支援區域](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)中裝載的工作區中，監控世界各地網路之間的連線情形

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>NPM 服務連線能力監視支援哪些區域？
NPM 可使用在其中一個[支援區域](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)裝載的工作區，監視世界各地服務的連線情形

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>NPM 的 ExpressRoute 監視可支援哪些區域？
NPM 可以監控位於任何 Azure 區域的 ExpressRoute 線路。 若要開始使用 NPM，需要一個裝載在其中一個[支援區域](/azure/expressroute/how-to-npm)中的 Log Analytics 工作區

## <a name="troubleshoot"></a>疑難排解

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>為什麼網路拓撲檢視中的某些躍點標示為無法辨識？
NPM 使用追蹤路由的修改版本，來探索從來源代理程式到目的地的拓撲。 無法辨識的躍點代表網路躍點未回應來源代理程式的追蹤路由要求。 如果有三個連續的網路躍點未回應代理程式的追蹤路由，解決方案會將沒有回應的躍點標示為無法辨識，而且不會嘗試探索更多躍點。

在以下一或多個案例中，躍點可能無法回應追蹤路由：

* 路由器已設定為不顯示其身分識別。
* 網路裝置不允許 ICMP_TTL_EXCEEDED 流量。
* 防火牆阻止來自網路裝置的 ICMP_TTL_EXCEEDED 回應。

當其中一個端點位於 Azure 中時，追蹤路由會顯示無法辨識的躍點，因為 Azure 基礎結構不會向追蹤路由顯示身分識別。 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>我收到狀況不良測試的警示，但我在 NPM 的遺失和延遲圖表中看不到高的值。 如何? 檢查什麼狀況不良？
如果來源與目的地之間的端對端延遲超出其之間任何路徑的臨界值，NPM 就會引發警示。 有些網路有多個路徑連接相同的來源和目的地。 NPM 引發警示是任何路徑狀況不良。 圖表中顯示的遺失和延遲是所有路徑的平均值，因此它可能不會顯示單一路徑的確切值。 若要瞭解臨界值被違反的位置，請尋找警示中的「子類型」資料行。 如果問題是由路徑所造成，則會 NetworkPath 子類型值（適用于效能監控測試）、EndpointPath （適用于服務連線能力監視測試）和 ExpressRoutePath （適用于 ExpressRotue 監視器測試）。 

尋找的範例查詢是 path 狀況不良：

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>為什麼我的測試顯示狀況不良，但拓撲不會 
NPM 會以不同的間隔監視端對端遺失、延遲和拓撲。 系統會每隔5秒測量一次遺失和延遲，並每隔三分鐘匯總一次（針對「效能監視器」和「Express Route 監視器」），同時使用追蹤路由每隔10分鐘計算一次。 例如，在3:44 與4:04 之間，拓撲可能會更新三次（3:44、3:54、4:04），但遺失和延遲會更新大約七次（3:44、3:47、3:50、3:53、3:56、3:59、4:02）。 3:54 產生的拓撲會針對在3:56、3:59 和4:02 計算的遺失和延遲呈現。 假設您收到一則警示，指出您的 ER 線路在3:59 狀況不良。 您登入 NPM，並嘗試將拓撲時間設定為3:59。 NPM 會轉譯在3:54 產生的拓撲。 若要瞭解網路的最後已知拓撲，請比較 TimeProcessed （計算遺失和延遲的時間）和 TracerouteCompletedTime （計算拓撲的時間）的欄位。 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>NetworkMonitoring 資料表中 E2EMedianLatency 和 AvgHopLatencyList 欄位之間的差異為何
E2EMedianLatency 是在匯總 tcp ping 測試的結果之後每三分鐘更新一次的延遲，而 AvgHopLatencyList 會根據追蹤路由每隔10分鐘更新一次。 若要瞭解計算 E2EMedianLatency 的確切時間，請使用欄位 TimeProcessed。 若要瞭解追蹤路由完成和更新 AvgHopLatencyList 的確切時間，請使用欄位 TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>為什麼逐躍點的延遲數與 HopLatencyValues 不同 
HopLatencyValues 是來源到端點。
例如：躍點-A、B、C。 AvgHopLatency-10、15、20。 這表示來源到延遲 = 10，來源至 B 延遲 = 15，而來源到 C 的延遲為20。 UI 會將-B 躍點延遲計算為拓撲中的5

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>解決方案會顯示 100% 遺失，但來源和目的地之間仍存在連線
如果主機防火牆或中繼防火牆 (網路防火牆或 Azure NSG) 透過 NPM 監視的連接埠封鎖來源代理程式與目的地之間的通訊 (除非客戶改變連接埠，否則預設情況下，連接埠為 8084)，則會發生此情況。

* 若要驗證主機防火牆是否未封鎖所需連接埠上的通訊，請至此查看來源節點和目的地節點的健康情況狀態：[網路效能監控] -> [設定] -> [節點]。 
  如果節點狀況不良，請查看說明並採取修正措施。 如果節點狀況良好，請跳至步驟 b。 。
* 若要驗證中繼網路防火牆或 Azure NSG 是否未封鎖所需連接埠上的通訊，請依照以下說明使用第三方 PsPing 公用程式：
  * 可以在[這裡](https://technet.microsoft.com/sysinternals/psping.aspx)下載 psping 公用程式 
  * 從來源節點執行下列命令。
    * psping -n 15 \<destination node IPAddress\>:portNumber 依預設，NPM 使用 8084 連接埠。 如果已使用 EnableRules.ps1 指令碼明確變更了此設定，請輸入您正在使用的自訂連接埠號碼。 這是從 Azure 機器到內部部署的 Ping
* 檢查 Ping 是否成功。 如果未成功，則表示中繼網路防火牆或 Azure NSG 目前封鎖了此連接埠上的流量。
* 現在請從目的地節點到來源節點 IP 執行命令。


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>從節點 A 到 B 有遺失，但從節點 B 到 A 沒有遺失。為什麼會有這種情況？
因為 A 到 B 之間的網路路徑可能與 B 到 A 之間的網路路徑不同，所以會發現到不同的遺失和延遲值。

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>為何探索不到我所有的 ExpressRoute 線路和對等互連連線？
NPM 現在會在使用者有權存取的所有訂用帳戶中，探索 ExpressRoute 線路和對等互連連線。 選擇您 Express Route 資源所連結的所有訂用帳戶，並針對每個探索到的資源啟用監視。 NPM 會在探索私用對等互連時尋找連線物件，因此，請檢查 VNET 是否會與您的對等互連相關聯。

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>ER 監控功能出現「流量未通過任何線路」的診斷訊息。 這是什麼意思？

這種情況可能是，內部部署和 Azure 節點之間存在狀況良好的連線，但是流量並未流至由 NPM 監控的 ExpressRoute 線路。 

這會在符合下列情形時發生：

* ER 線路關閉。
* 因為路由篩選條件設定方式的關係，導致其優先順序高於欲使用的 ExpressRoute 線路上的其他路由 (例如 VPN 連線或其他 ExpressRoute 線路)。 
* 監視設定中，選擇用於監控 ExpressRoute 線路的內部部署和 Azure 節點，並未在欲使用的 ExpressRoute 線路上相互連線。 請確定您選擇的是正確的節點，而這些節點有透過您要監控的 ExpressRoute 線路相互連線。

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>在設定監視我的 ExpressRoute 線路時，並未偵測 Azure 節點。
如果透過 Operations Manager 連線 Azure 節點，則會發生此情況。 ExpressRoute 監控功能僅支援以直接代理程式連線的 Azure 節點。

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>我無法在 OMS 入口網站中，依 ExpressRoute 線路進行探索
雖然可以從 Azure 入口網站和 OMS 入口網站使用 NPM，但 ExpressRoute 監控功能中的線路探索僅適用於 Azure 入口網站。 透過 Azure 入口網站探索線路後，您就可以在兩個入口網站的其中一個使用該功能。 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>服務連線監能力功能中的服務回應時間、網路遺失以及延遲，皆顯示為 NA
如果下列一或多個條件為真，則會發生此情況：

* 服務已關閉。
* 用於檢查服務之網路連線的節點已關閉。
* 測試組態中輸入的目標不正確。
* 節點沒有任何網路連線。

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>在服務連線能力監視功能中，會顯示有效的服務回應時間，但網路遺失以及延遲皆顯示為 NA
 如果下列一或多個條件為真，則會發生此情況：

* 如果用於檢查服務之網路連線的節點為 Windows 用戶端機器，則目標服務會封鎖 ICMP 要求，或是網路防火牆會封鎖源自該節點的 ICMP 要求。
* [執行網路量測] 核取方塊在測試組態中為空白。

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>在服務連線能力監視功能中，服務回應時間為 NA，但網路遺失以及延遲皆有效
如果目標服務不是 Web 應用程式，但測試設定為 Web 測試，則會發生此情況。 編輯測試組態，然後選擇 [網路] (而不是 [Web]) 作為測試類型。

## <a name="miscellaneous"></a>其他

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>是否會影響到用於監視的節點的效能？
NPM 流程設定為當其所使用的主機 CPU 資源用量超過 5%，即會停止。 這是為了確保可以繼續將節點用於其一般工作負載，而不會影響效能。

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>NPM 是否會編輯防火牆規則以進行監控？
NPM 僅會在執行 EnableRules.ps1 Powershell 指令碼的節點上建立本機 Windows 防火牆規則，以允許代理程式在指定的連接埠上互相建立 TCP 連線。 解決方案不會修改任何網路防火牆或網路安全性群組 (NSG) 規則。

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>如何檢查用於監視之節點的健康情況？
您可以至此查看用於監視之節點的健康情況狀態：[網路效能監控] -> [設定] -> [節點]。 如果節點狀況不良，請檢視錯誤詳細資料，並採取建議的動作。

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>NPM 能否以微秒為單位報告延遲數？
NPM 會在 UI 中四捨五入延遲數，並以毫秒為單位。 相同的資料會以較高的細微性儲存 (有時會高達四個小數位)。

## <a name="next-steps"></a>後續步驟

- 如需深入了解網路效能監控，請參閱 [Azure 中的網路效能監控解決方案](../../azure-monitor/insights/network-performance-monitor.md)。
