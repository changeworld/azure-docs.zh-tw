---
title: 可調整規模並為您的服務提供 HA 的健全狀況探查
titleSuffix: Azure Load Balancer
description: 在本文中，您將瞭解如何使用健康情況探查來監視實例後 Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 8e79f4c791d0252c719846da3aa8024b0e622dca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80477015"
---
# <a name="load-balancer-health-probes"></a>Load Balancer 健康情況探查

搭配 Azure Load Balancer 使用負載平衡規則時，您必須指定健全狀況探查，以允許 Load Balancer 偵測後端端點狀態。  健康情況探查和探查回應的設定會決定哪些後端集區實例會接收新的流程。 您可以使用健康情況探查來偵測後端端點上的應用程式失敗。 您也可以對健康情況探查產生自訂回應，並運用健康情況探查來控制流量，藉此管理負載或排定的停機時間。 當健康情況探查失敗時，Load Balancer 將會停止將新流量傳送至個別狀況不良的實例。 輸出連線不會受到影響，只會影響輸入連線能力。

健康情況探查支援多個通訊協定。 特定健康情況探查通訊協定的可用性會因 Load Balancer SKU 而有所不同。  此外，服務的行為會因 Load Balancer SKU 而有所不同，如下表所示：

| | 標準 SKU | 基本 SKU |
| --- | --- | --- |
| [探查類型](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [探查關閉行為](#probedown) | 關閉所有探查、繼續所有 TCP 流程。 | 所有探查關閉時，所有 TCP 流量都會過期。 | 


>[!IMPORTANT]
>完整閱讀此檔，包括下列重要的[設計指導](#design)方針，以建立可靠的服務。

>[!IMPORTANT]
>Load Balancer 健康情況探查源自於 IP 位址 168.63.129.16，而且不得封鎖探查以將您的執行個體標示為已啟動。  檢閱[探查來源 IP 位址](#probesource)，以取得詳細資料。

>[!IMPORTANT]
>不論設定的超時閾值為何，如果伺服器傳回任何不是 HTTP 200 正常的狀態碼，或是透過 TCP 重設來終止連線，則 HTTP （S） Load Balancer 健康情況探查將會自動探查實例。

## <a name="probe-configuration"></a><a name="probes"></a>探查設定

健康情況探查設定是由下列元素所組成：

- 個別探查之間的間隔持續時間
- 在探查轉換成不同狀態之前必須觀察的探查回應數目
- 探查的通訊協定
- 探查的埠
- 使用 HTTP （S）探查時用於 HTTP GET 的 HTTP 路徑

>[!NOTE]
>使用 Azure PowerShell、Azure CLI、範本或 API 時，不會強制或檢查探查定義。 只有在使用 Azure 入口網站時，才會進行探查驗證測試。

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>瞭解應用程式信號、偵測信號，以及平臺的反應

探查回應的數目適用于兩者

- 允許實例標示為已啟動的成功探查數目，以及
- 導致實例標示為關閉的超時探查數目。

指定的 timeout 和 interval 值會決定要將實例標示為啟動或關閉。  間隔的持續時間乘以探查回應的數目，會決定必須偵測到探查回應的持續時間。  而服務會在完成所需的探查之後做出反應。

我們可以使用範例進一步說明此行為。 如果您已將探查回應的數目設定為2，並將間隔設為5秒，這表示必須在10秒間隔內觀察2探查逾時錯誤。  由於當您的應用程式可能會變更狀態時，傳送探查的時間不會同步處理，因此我們可以將時間系結到兩個案例：

1. 如果您的應用程式在第一次探查抵達之前就開始產生超時探查回應，則這些事件的偵測需要10秒（2 x 5 秒的間隔），加上應用程式的持續時間，以通知第一個探查抵達的時間。  您可以假設此偵測會稍微超過10秒。
2. 如果您的應用程式在第一次探查抵達之後才開始產生超時探查回應，這些事件的偵測將不會開始，直到下一個探查抵達（和超時）再加上另一個10秒（2 x 5 秒的間隔）。  您可以假設此偵測只需要15秒的時間。

在此範例中，一旦發生偵測，平臺就會花很短的時間來回應這項變更。  這表示會根據 

1. 當應用程式開始變更狀態時，
2. 當偵測到這項變更並符合必要準則時（在指定的間隔傳送的探查數目）和
3. 當偵測已通過平臺通訊時 

您可以假設超時探查回應的反應會在最少10秒內完成，最多隻需要稍微超過15秒，以回應應用程式的信號變更。  提供此範例以說明發生了什麼事，不過，您無法預測超過上述範例所述之上述粗略指引的確切持續時間。

>[!NOTE]
>健康情況探查會探查後端集區中所有執行中的實例。 如果實例已停止，將不會進行探查，直到重新開機為止。

## <a name="probe-types"></a><a name="types"></a>探查類型

健康情況探查所使用的通訊協定可以設定為下列其中一項：

- [接聽程式](#tcpprobe)
- [HTTP 端點](#httpprobe)
- [HTTPS 端點](#httpsprobe)

可用的通訊協定取決於所使用的 Load Balancer SKU：

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| 標準 SKU |    &#9989; |   &#9989; |   &#9989; |
| 基本 SKU |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> TCP 探查

TCP 探查會利用定義的連接埠執行三向開放 TCP 交握，藉此初始化連線。  TCP 探查會終止與四向 TCP 交握的連線。

最小探查間隔為 5 秒，狀況不良的回應數目下限為 2。  所有間隔的總持續時間不能超過 120 秒。

在以下情況，TCP 探查會失敗：
* 執行個體上的 TCP 接聽程式在逾時期間完全沒有回應。  探查會根據超時探查要求的數目來標示為已關閉，而這些要求已設定為未接聽，然後才標示探查。
* 探查會從執行個體接收 TCP 重設。

以下說明如何在 Resource Manager 範本中表示這種探查設定：

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS 探查

>[!NOTE]
>HTTPS 探查僅適用於 [Standard Load Balancer](load-balancer-standard-overview.md)。

HTTP 和 HTTPS 探查建立在 TCP 探查的基礎上，並會發出含有指定路徑的 HTTP GET。 這兩個探查皆支援 HTTP GET 的相對路徑。 HTTPS 探查就是加入傳輸層安全性 (TLS，之前稱為 SSL) 包裝函式的 HTTP 探查。 當執行個體在逾時期限內以 HTTP 狀態 200 回應時，健康情況探查會標示為已啟動。  依預設，健康情況探查會每隔 15 秒嘗試檢查一次已設定的健康情況探查連接埠。 最小探查間隔為 5 秒。 所有間隔的總持續時間不能超過 120 秒。

如果探查埠也是服務本身的接聽程式，HTTP/HTTPS 探查也很適合用來執行您自己的邏輯，以從負載平衡器輪替中移除實例。 例如，如果執行個體使用超過 90% CPU，並傳回非 200 HTTP 狀態，您可以決定移除執行個體。 

> [!NOTE] 
> HTTPS 探查需要使用以整個鏈中具有最少簽章雜湊 SHA256 的憑證為基礎。

如果您使用雲端服務而且有使用 w3wp.exe 的 Web 角色，也能讓網站的監視自動化。 網站程式碼中的失敗，會將非 200 的狀態傳回給負載平衡器探查。

在以下情況，HTTP / HTTPS 探查會失敗：
* 探查端點會傳回 200 以外的 HTTP 回應碼 (例如，403、404 或 500)。 這會立即將健康情況探查標記為關閉。 
* 探查端點在探查間隔的最小值和30秒的超時期間都不會回應。 在探查被標示為非執行中之前，直到達到所有逾時時間間隔總和為止，這段時間內已有多個探查要求並未獲得回應。
* 探查端點會透過 TCP 重設關閉連線。

以下說明如何在 Resource Manager 範本中表示這種探查設定：

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>客體代理程式探查 (僅限傳統)

依預設，雲端服務角色 (背景工作角色和 Web 角色) 會使用客體代理程式進行探查監視。  客體代理程式探查設定是最後的手段。  請一律明確地將健康情況探查搭配 TCP 或 HTTP 探查使用。 在大部分的應用程式案例中，客體代理程式探查的效果不如明確定義的探查。

客體代理程式探查是虛擬機器內客體代理程式的檢查。 然後，只有在執行個體處於就緒狀態時，它才會接聽並以「HTTP 200 確定」回應。 (其他狀態為忙碌、正在回收或正在停止。)

如需詳細資訊，請查看[設定適用於健全狀況探查的服務定義檔案 (csdef)](https://msdn.microsoft.com/library/azure/ee758710.aspx) 或[開始為雲端服務建立公用負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services)。

如果客體代理程式無法以「HTTP 200 確定」回應，則負載平衡器會將執行個體標示為沒有回應。 然後，它會停止將流量傳送到該執行個體。 負載平衡器會繼續檢查執行個體。 

如果客體代理程式以 HTTP 200 回應，則負載平衡器會再次將新流量傳送到該執行個體。

使用 Web 角色時，網站程式碼通常會在不受 Azure 網狀架構或客體代理程式監視的 w3wp.exe 中執行。 在 w3wp.exe 中的失敗 (例如 HTTP 500 回應) 不會向客體代理程式報告。 因此，負載平衡器不會將該執行個體從循環中剔除。

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>探查行為

TCP、HTTP 和 HTTPS 健康狀態探查會視為狀況良好，並在下列情況下將後端端點標示為狀況良好：

* 健康情況探查在 VM 開機時就成功。
* 已達到指定的探查數目，必須將後端端點標示為狀況良好。

已達到狀況良好狀態的任何後端端點都有資格接收新的流程。  

> [!NOTE]
> 如果健康情況探查變動，負載平衡器會等待較長的時間，再讓後端端點回到狀況良好狀態。 此額外等候時間可保護使用者和基礎結構，且為刻意設計的原則。

## <a name="probe-down-behavior"></a><a name="probedown"></a>探查關閉行為

### <a name="tcp-connections"></a>TCP 連線

新的 TCP 連線會成功到剩餘的狀況良好後端端點。

如果後端端點的健康情況探查失敗，已建立與此後端端點的 TCP 連線會繼續。

如果後端集區中所有執行個體的所有探查都失敗，則不會有任何新流量傳送至後端集區。 Standard Load Balancer 將允許已建立的 TCP 流量繼續。  Basic Load Balancer 會終止後端集區的所有現有 TCP 流量。
 
Load Balancer 是一種穿透服務 (不會終止 TCP 連線)，且流程一律介於用戶端和虛擬機器的客體作業系統與應用程式之間。 所有探查關閉的集區會導致前端不會回應 TCP 連線開啟嘗試（SYN），因為沒有可接收流量的狀況良好後端端點，而且會以 SYN ACK 回應。

### <a name="udp-datagrams"></a>UDP 資料包

UDP 資料包會傳遞至狀況良好的後端端點。

UDP 是不需連線的，因此 UDP 沒有流程狀態追蹤。 如果任何後端端點的健康情況探查失敗，現有的 UDP 流量將會移至後端集區中另一個狀況良好的實例。

如果後端集區中所有執行個體的所有探查都失敗，則 Basic 和 Standard Load Balancer 的現有 UDP 流量將會終止。

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>探查來源 IP 位址

Load Balancer 會為其內部健康情況模型使用分散式探查服務。 探查服務駐留在 VM 所在的每個主機上，可以視需要透過程式設計方式來為每個客戶的組態產生健康情況探查。 健康情況探查流量會直接在產生健康情況探查的探查服務和客戶虛擬機器之間產生。 所有 Load Balancer 健康情況探查都源自作為其來源的 IP 位址 168.63.129.16。  您可以在非 RFC1918 空間的 VNet 內部使用 IP 位址空間。  使用全域保留且為 Microsoft 擁有的 IP 位址，會減少 IP 位址與您在 VNet 內所用的 IP 位址空間發生衝突的機會。  此 IP 位址在所有區域內都是相同的，不會改變，而且因為只有內部 Azure 平台元件會從此 IP 位址獲得封包，所以此位址並不會有安全性風險。 

AzureLoadBalancer 服務標籤會在您的[網路安全性群組](../virtual-network/security-overview.md)中識別此來源 IP 位址，且預設即會許可健康情況探查的流量。

除了 Load Balancer 健康情況探查以外，[下列作業會使用此 IP 位址](../virtual-network/what-is-ip-address-168-63-129-16.md)：

- 啟用 VM 代理程式來與平台通訊，藉此表示它處於「就緒」狀態
- 啟用與 DNS 虛擬伺服器的通訊，以提供篩選後的名稱解析給未定義自訂 DNS 伺服器的客戶。  此篩選可確保客戶只可以解析其部署的主機名稱。
- 允許 VM 從 Azure 中的 DHCP 服務取得動態 IP 位址。

## <a name="design-guidance"></a><a name="design"></a>設計指引

健康情況探查用於讓您的服務具有復原性，並讓服務得以調整級別。 錯誤的設定或不良的設計模式，都可能會影響您服務的可用性和延展性。 請詳讀整份文件，想一想當這此探查被標示為開啟或關閉時，會對您的案例造成何種影響，又會如何影響您應用程式的可用性。

當您設計應用程式的健康情況模型時，應該探查後端端點上的埠，以反映該實例的健全狀況 __，以及__您所提供的應用程式服務。  應用程式連接埠和探查連接埠不一定要相同。  在某些情況下，探查連接埠可能需要與您應用程式提供服務的連接埠不同。  

有時候對您的應用程式而言，產生健康情況探查回應不僅會偵測到應用程式的健康情況，且無論您的執行個體是否應接收新流量，還能將訊號直接發送給 Load Balancer，這點相當有用。  您可以運用探查回應讓應用程式得以建立背壓，並透過讓健康情況探查失敗，或是準備好對應用程式進行維護並開始清空案例的作法，將傳遞到執行個體的新流量加以節流。  使用 Standard Load Balancer 時，[探查失敗](#probedown)訊號一律會允許 TCP 流量繼續，直到閒置逾時或連線關閉為止。 

針對 UDP 負載平衡，您應該從後端端點產生自訂健康情況探查信號，並使用 TCP、HTTP 或 HTTPS 健康情況探查，並以對應的接聽程式為目標，以反映 UDP 應用程式的健全狀況。

使用 [HA 連接埠負載平衡規則](load-balancer-ha-ports-overview.md)搭配 [Standard Load Balancer](load-balancer-standard-overview.md) 時，所有連接埠都會進行負載平衡，而單一健康情況探查的回應則必須反映出整個執行個體的狀態。

請勿透過接受健康情況探查的執行個體，將健康情況探查轉化或通過 Proxy 處理至 VNet 中的另一個執行個體，因為此設定可能導致您的案例發生連鎖性失敗。  請設想一下以下狀況：有一組第三方設備部署在 Load Balancer 資源的後端集區中，藉以提供設備所需的規模和備援能力，且健康情況探查會設定為負責探查第三方設備通過 Proxy 處理或轉化至設備背後的其他虛擬機器時所使用的連接埠。  如果您探查的連接埠相同於您要用來將要求轉化或通過 Proxy 處理至設備背後其他虛擬機器的連接埠，任何來自設備背後單一虛擬機器的探查回應都會將設備本身標記為無作用。 這項設定可能會導致整個應用程式案例的階層式失敗，做為設備背後的單一後端端點所導致。  觸發程序可能會是間歇性探查失敗，而會造成 Load Balancer 將原始目的地 (應用程式執行個體) 標示為關閉，接著會停用您整個應用程式案例。 請改為探查設備本身的健康情況。 挑選要以何種探查來判定健康情況訊號，對網路虛擬設備 (NVA) 案例而言是一大考量，請務必諮詢您的應用程式供應商，確定什麼健康情況訊號適合此類案例。

如果您不允許防火牆原則中有此探查的[來源 IP](#probesource)，則健康情況探查將會失敗，因為它無法接觸您的執行個體。  接著，Load Balancer 會因為健康情況探查失敗而將您的執行個體標示為已關閉。  此種設定會造成負載已平衡的應用程式案例失敗。

為了讓 Load Balancer 的健康情況探查將您的執行個體標示為已開啟，您**必須**在任何 Azure [網路安全性群組](../virtual-network/security-overview.md)和本機防火牆原則中允許此 IP 位址。  預設中，每個網路安全性群組皆含有[服務標籤](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer，以許可健康情況探查流量。

如果您想要測試健康情況探查的失敗，或將個別的執行個體標示為已關閉，您可以使用[網路安全性群組](../virtual-network/security-overview.md)明確封鎖健康情況探查 (目的地連接埠或[來源 IP](#probesource))，並模擬探查失敗。

請勿使用 Microsoft 所擁有的 IP 位址範圍 (含 168.63.129.16 在內) 來設定您的 VNet。  這種設定會與健康情況探查的 IP 位址相衝突，而可能導致您的案例失敗。

如果您的 VM 上有多個介面，您必須確保在您接收探查的介面上進行回應。  您可能需要在每個介面上將來源網路位址在虛擬機器中轉換成此位址。

請勿啟用 [TCP 時間戳記](https://tools.ietf.org/html/rfc1323)。  啟用 TCP 時間戳記可能會導致健康情況探查失敗，因為 VM 的來賓 OS TCP 堆疊會卸載 TCP 封包，這會導致 Load Balancer 將個別端點標記在一起。  TCP 時間戳記依預設會在安全性強化的虛擬機器映像上定期啟用，而必須停用。

## <a name="monitoring"></a>監視

公用和內部[Standard Load Balancer](load-balancer-standard-overview.md)都會透過 Azure 監視器以多維度計量的形式，將每個端點和後端端點的健康情況探查狀態公開。 這些計量可供其他 Azure 服務或合作夥伴應用程式使用。 

基本公用 Load Balancer 會透過 Azure 監視器記錄，公開每個後端集區摘要的健康情況探查狀態。  內部基本負載平衡器無法使用 Azure 監視器記錄。  您可以使用[Azure 監視器記錄](load-balancer-monitor-log.md)來檢查公用負載平衡器探查健全狀況狀態和探查計數。 記錄可以與 Power BI 或 Azure Operation Insights 搭配使用，以提供負載平衡器健康狀態。

## <a name="limitations"></a>限制

- HTTPS 探查不支援使用用戶端憑證進行相互驗證。
- 您應該假設啟用 TCP 時間戳記時，健康情況探查將會失敗。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Standard Load Balancer](load-balancer-standard-overview.md)
- [開始使用 PowerShell 在資源管理員中建立公用負載平衡器](quickstart-create-standard-load-balancer-powershell.md)
- [適用於健康情況探查的 REST API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- 透過 [Load Balancer 的 Uservoice](https://aka.ms/lbuservoice) 要求新的健康情況探查功能
