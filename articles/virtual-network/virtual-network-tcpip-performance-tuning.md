---
title: 適用于 Azure Vm 的 TCP/IP 效能微調 |Microsoft Docs
description: 瞭解各種常見的 TCP/IP 效能微調技術，以及其與 Azure Vm 的關係。
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: 67b635f09cb9407279e89b5f7b8526dab3c08946
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87068522"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>適用于 Azure Vm 的 TCP/IP 效能微調

本文討論常見的 TCP/IP 效能微調技術，以及當您將它們用於 Azure 上執行的虛擬機器時，應考慮的一些事項。 它將提供基本的技術總覽，並探索其微調方式。

## <a name="common-tcpip-tuning-techniques"></a>常見 TCP/IP 微調技術

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU、片段和大型傳送卸載

#### <a name="mtu"></a>MTU

最大傳輸單位（MTU）是可透過網路介面傳送的最大大小框架（封包）（以位元組為單位）。 MTU 是可設定的設定。 在 Azure Vm 上使用的預設 MTU 和全域網路裝置上的預設設定是1500個位元組。

#### <a name="fragmentation"></a>分割

傳送封包超過網路介面的 MTU 時，就會發生片段。 TCP/IP 堆疊會將封包分割成較小的片段（片段），以符合介面的 MTU。 分散發生在 IP 層，而且與基礎通訊協定（例如 TCP）無關。 當2000位元組封包透過具有 MTU 1500 的網路介面傳送時，封包將會細分成 1 1500 位元組封包和 1 500 位元組封包。

來源與目的地之間的網路裝置可以捨棄超過 MTU 的封包，或將封包分成較小的片段。

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>IP 封包中的不片段位

「不片段」（DF）位是 IP 通訊協定標頭中的旗標。 DF 位表示寄件者與接收者之間的路徑上的網路裝置不得分段封包。 基於許多原因，可能會設定此位。 （如需其中一個範例，請參閱本文的「路徑 MTU 探索」一節）。當網路裝置收到的封包未設定「不」片段位，且該封包超過裝置的介面 MTU 時，標準行為是讓裝置捨棄封包。 裝置會將 [需要的 ICMP 片段] 訊息傳送回封包的原始來源。

#### <a name="performance-implications-of-fragmentation"></a>片段的效能影響

片段可能會對效能造成負面影響。 對效能造成影響的主要原因之一，就是封包的分散和重組的 CPU/記憶體影響。 當網路裝置需要分割封包時，必須配置 CPU/記憶體資源來執行片段。

封包重組時，就會發生相同的情況。 網路裝置必須先儲存所有片段，才會收到它們，以便將它們重新組合成原始封包。 這種分散和重組的程式也可能造成延遲。

另一個可能的負面效能含意是分散的封包可能不會按順序抵達。 當封包未按順序接收時，某些類型的網路裝置可以捨棄它們。 發生這種情況時，必須重新傳輸整個封包。

片段通常會由網路防火牆之類的安全性裝置卸載，或網路裝置的接收緩衝區耗盡時。 當網路裝置的接收緩衝區耗盡時，網路裝置會嘗試重新組裝分散的封包，但沒有資源可儲存和 reassume 封包。

片段可視為負面作業，但當您透過網際網路連線不同的網路時，就必須支援片段化。

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>修改 MTU 的優點和後果

一般來說，您可以藉由增加 MTU 來建立更有效率的網路。 傳輸的每個封包都有新增至原始封包的標頭資訊。 當片段建立更多封包時，會有更多的標頭額外負荷，讓網路的效率更低。

以下是範例。 Ethernet 標頭大小為14個位元組，加上4個位元組的框架檢查順序，以確保框架的一致性。 如果傳送 1 2000 位元組封包，網路上會新增18個位元組的 Ethernet 額外負荷。 如果封包分成1500位元組封包和500位元組封包，則每個封包都會有18個位元組的 Ethernet 標頭，總共36個位元組。

請記住，增加 MTU 並不一定會建立更有效率的網路。 如果應用程式只傳送500位元組封包，則不論 MTU 是1500位元組或9000位元組，都將會有相同的標頭額外負荷。 只有當網路使用的封包大小受到 MTU 的影響時，才會變得更有效率。

#### <a name="azure-and-vm-mtu"></a>Azure 和 VM MTU

Azure Vm 的預設 MTU 是1500個位元組。 Azure 虛擬網路 stack 會嘗試在1400個位元組分段封包。

請注意，虛擬網路堆疊原本就沒有效率，因為它會將封包片段在1400個位元組，即使 Vm 的 MTU 為1500也一樣。 大量的網路封包比1400或1500個位元組小很多。

#### <a name="azure-and-fragmentation"></a>Azure 和片段

虛擬網路堆疊設定成會卸載「順序不足」的片段，也就是不會以原始分散順序抵達的分散封包。 這些封包主要是因為2018年11月宣佈的網路安全性弱點，稱為 FragmentSmack。

FragmentSmack 是 Linux 核心處理分割的 IPv4 與 IPv6 封包之方式的缺陷。 遠端攻擊者可以使用此缺陷來觸發昂貴的片段重組作業，這可能會導致在目標系統上增加 CPU 和拒絕服務。

#### <a name="tune-the-mtu"></a>調整 MTU

您可以設定 Azure VM MTU，如同您在任何其他作業系統中所能使用的一樣。 但當您設定 MTU 時，您應該考慮在 Azure 中發生的片段（如上所述）。

我們不鼓勵客戶增加 VM 的 Mtu。 這項討論的目的是要說明 Azure 如何實行 MTU 並執行片段的詳細資料。

> [!IMPORTANT]
>不知道增加的 MTU 會改善效能，而且可能會對應用程式效能產生負面影響。
>
>

#### <a name="large-send-offload"></a>大型傳送卸載

大型的傳送卸載（LSO）可以藉由將封包分割分散到 Ethernet 介面卡，來改善網路效能。 啟用 LSO 時，TCP/IP 堆疊會建立大型 TCP 封包，並將其傳送至 Ethernet 介面卡進行分割，然後再轉送。 LSO 的優點是可以將 CPU 從分割的封包釋放成符合 MTU 的大小，並將該處理常式卸載到硬體中執行的 Ethernet 介面。 若要深入瞭解 LSO 的優點，請參閱[支援大型傳送](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)卸載。

啟用 LSO 時，Azure 客戶在執行封包捕獲時可能會看到大型的框架大小。 這些大型的框架大小可能會導致某些客戶覺得分散發生，或在不是使用大型 MTU 的情況。 使用 LSO 時，乙太網路介面卡可以將較大的區段大小（MSS）公告至 TCP/IP 堆疊，以建立較大的 TCP 封包。 這整個非分段的框架會轉送給 Ethernet 介面卡，並在 VM 上執行的封包捕獲中顯示。 但是根據 Ethernet 介面卡的 MTU，乙太網路介面卡會將封包分成許多較小的框架。

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS 視窗縮放和 PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP 區段大小上限

TCP 區段大小上限（MSS）是限制 TCP 區段大小的設定，可避免 TCP 封包的片段。 作業系統通常會使用此公式來設定 [MSS]：

`MSS = MTU - (IP header size + TCP header size)`

IP 標頭和 TCP 標頭分別為20個位元組，或40個位元組的總計。 因此，MTU 為1500的介面會有 MSS 1460。 但是 MSS 是可設定的。

當來源和目的地之間設定了 TCP 會話時，此設定在 TCP 三向交握中會被同意。 兩端都會傳送 MSS 值，而兩者中的較低則用於 TCP 連線。

請記住，來源和目的地的 Mtu 不是決定 MSS 值的唯一因素。 中繼網路裝置（例如 VPN 閘道，包括 Azure VPN 閘道）可以獨立調整來源和目的地的 MTU，以確保達到最佳的網路效能。

#### <a name="path-mtu-discovery"></a>路徑 MTU 探索

[MSS] 已協商，但可能不會指出可以使用的實際 MSS。 這是因為來源與目的地之間的路徑中的其他網路裝置，其 MTU 值可能低於來源和目的地。 在此情況下，其 MTU 小於封包的裝置將會捨棄封包。 裝置會傳回所需的 ICMP 片段（類型3，代碼4），其中包含其 MTU。 此 ICMP 訊息可讓來源主機適當地減少其路徑 MTU。 此程式稱為路徑 MTU 探索（PMTUD）。

PMTUD 程式沒有效率，而且會影響網路效能。 傳送封包超過網路路徑的 MTU 時，必須以較低的 MSS 重新傳輸封包。 如果傳送者未收到「需要 ICMP 分散」訊息，可能是因為路徑中的網路防火牆（通常稱為*PMTUD blackhole*），傳送者並不知道它是否需要降低 MSS，並會持續重新傳輸封包。 這就是為什麼我們不建議增加 Azure VM MTU 的原因。

#### <a name="vpn-and-mtu"></a>VPN 和 MTU

如果您使用執行封裝的 Vm （例如 IPsec Vpn），則會有關于封包大小和 MTU 的一些其他考慮。 Vpn 會將更多標頭新增至封包，這樣會增加封包大小，而且需要較小的 MSS。

針對 Azure，建議您將 TCP MSS 固定設為1350個位元組，並將通道介面 MTU 設定為1400。 如需詳細資訊，請參閱[VPN 裝置和 IPSec/IKE 參數頁面](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>延遲、來回時間和 TCP 視窗調整

#### <a name="latency-and-round-trip-time"></a>延遲和來回時間

網路延遲是由光纖網路的光線速度所控制。 TCP 的網路輸送量也會受到兩個網路裝置之間的來回行程時間（RTT）的有效控管。

| 路由 | 距離 | 單向時間 | RTT |
| ----- | -------- | ------------ | --- |
|紐約至三藩市|4148公里|21毫秒|42毫秒|
|紐約到倫敦|5585公里|28毫秒|56毫秒|
|紐約至悉尼|15993公里|80毫秒|160毫秒|

下表顯示兩個位置之間的直線距離。 在網路中，距離通常比直線距離長。 以下是一個簡單的公式，可根據光線的速度來計算最小 RTT：

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

您可以使用200來取得傳播速度。 這是光線在1毫秒的距離（以公里為單位）。

讓我們以紐約到三藩市的例子做為範例。 直線距離為4148公里。 將該值插入方程式中，我們會取得下列內容：

`Minimum RTT = 2 * (4,148 / 200)`

方程式的輸出（以毫秒為單位）。

如果您想要取得最佳的網路效能，則邏輯選項是選取具有最短距離的目的地。 您也應該設計虛擬網路，以將流量的路徑優化並減少延遲。 如需詳細資訊，請參閱本文的「網路設計考慮」一節。

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>TCP 上的延遲和來回時間效果

來回時間對最大 TCP 輸送量有直接的影響。 在 [TCP 通訊協定] 中，[*視窗大小]* 是可以透過 TCP 連線傳送的最大流量量，然後才需要接收來自接收者的認可。 如果 TCP MSS 設定為1460，且 TCP 視窗大小設為65535，則傳送者可以在必須接收來自接收者的認可之前，先傳送45封包。 如果傳送者沒有收到通知，則會重新傳輸資料。 公式如下：

`TCP window size / TCP MSS = packets sent`

在此範例中，65535/1460 會無條件進位到45。

這項「等待認可」狀態是確保資料可靠傳遞的機制，這會導致 RTT 影響 TCP 輸送量。 傳送者等候認可的時間愈久，傳送更多資料之前需要等待的時間就越長。

以下是計算單一 TCP 連線的最大輸送量的公式：

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

下表顯示單一 TCP 連線的最大 mb/每秒輸送量。 （為了方便閱讀，測量單位會使用 mb）。

| TCP 視窗大小（位元組） | RTT 延遲（毫秒） | 最大 mb/秒輸送量 | 最大百萬位元/秒輸送量 |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|73|5.83|
|65,535|120|55|4.37|

如果封包遺失，TCP 連線的最大輸送量將會減少，而寄件者會重新傳輸其已傳送的資料。

#### <a name="tcp-window-scaling"></a>TCP 視窗調整

TCP 視窗調整是一種技術，可動態增加 TCP 視窗大小，以允許在需要通知之前傳送更多資料。 在上述範例中，會在需要通知之前傳送45封包。 如果您增加了在需要通知之前可以傳送的封包數目，您就可以減少寄件者等待認可的次數，這會增加 TCP 的最大輸送量。

下表說明這些關聯性：

| TCP 視窗大小（位元組） | RTT 延遲（毫秒） | 最大 mb/秒輸送量 | 最大百萬位元/秒輸送量 |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65,535|30|2.18|17.48|
|131070|30|4.37|34.95|
|262140|30|8.74|69.91|
|524280|30|17.48|139.81|

但是 TCP 視窗大小的 TCP 標頭值只是2個位元組長，這表示接收視窗的最大值是65535。 為了增加視窗大小上限，引進了 TCP 視窗調整因數。

「縮放比例」也是您可以在作業系統中設定的一項設定。 以下是使用縮放因數來計算 TCP 視窗大小的公式：

`TCP window size = TCP window size in bytes \* (2^scale factor)`

以下是適用于 windows 調整因數3的計算，而視窗大小為65535：

`65,535 \* (2^3) = 262,140 bytes`

擴展因素14會導致 TCP 視窗大小為14（允許的最大位移）。 TCP 視窗大小將是1073725440個位元組（8.5 gb）。

#### <a name="support-for-tcp-window-scaling"></a>TCP 視窗調整的支援

Windows 可以針對不同的連線類型設定不同的縮放比例。 （連接的類別包括資料中心、網際網路等等）。您可以使用 `Get-NetTCPConnection` PowerShell 命令來查看視窗調整連線類型：

```powershell
Get-NetTCPConnection
```

您可以使用 `Get-NetTCPSetting` PowerShell 命令來查看每個類別的值：

```powershell
Get-NetTCPSetting
```

您可以使用 PowerShell 命令，在 Windows 中設定初始 TCP 視窗大小和 TCP 調整因數 `Set-NetTCPSetting` 。 如需詳細資訊，請參閱[NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)。

```powershell
Set-NetTCPSetting
```

以下是的有效 TCP 設定 `AutoTuningLevel` ：

| AutoTuningLevel | 調整因數 | 縮放乘數 | 公式為<br/>計算視窗大小上限 |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|已停用|無|無|視窗大小|
|受限制|4|2 ^ 4|視窗大小 * （2 ^ 4）|
|高度限制|2|2 ^ 2|視窗大小 * （2 ^ 2）|
|正常|8|2 ^ 8|視窗大小 * （2 ^ 8）|
|實驗|14|2 ^ 14|視窗大小 * （2 ^ 14）|

這些設定最有可能會影響 TCP 效能，但請記住，除了 Azure 的控制之外，跨網際網路的其他許多因素也會影響 TCP 效能。

#### <a name="increase-mtu-size"></a>增加 MTU 大小

因為較大的 MTU 表示較大的 MSS，所以您可能會想要增加 MTU 是否可以增加 TCP 效能。 大概沒有。 封包大小除了 TCP 流量外，還有優缺點。 如先前所述，影響 TCP 輸送量效能的最重要因素是 TCP 視窗大小、封包遺失和 RTT。

> [!IMPORTANT]
> 我們不建議 Azure 客戶變更虛擬機器上的預設 MTU 值。
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>加速網路和接收端調整

#### <a name="accelerated-networking"></a>加速網路

虛擬機器網路功能在來賓 VM 和虛擬機器/主機上的 CPU 耗用過高。 透過主機傳輸的每個封包都是由主機 CPU 在軟體中處理，包括所有虛擬網路封裝和 decapsulation。 因此，經過主機的流量越多，CPU 負載就愈高。 而且，如果主機 CPU 與其他作業忙碌，這也會影響網路輸送量和延遲。 Azure 會透過加速網路解決此問題。

加速網路會透過 Azure 的內部可程式化硬體和 SR-IOV 這類技術，提供一致的 ultralow 網路延遲。 加速網路會將許多 Azure 軟體定義的網路堆疊從 Cpu 移到 FPGA 型 SmartNICs。 這項變更可讓使用者應用程式回收計算週期，這會降低 VM 上的負載，減少抖動和延遲中的不一致。 換句話說，效能可能更具決定性。

加速網路會藉由允許來賓 VM 略過主機並直接與主機的 SmartNIC 建立資料路徑，來改善效能。 以下是加速網路的一些優點：

- **較低的延遲/較高的每秒封包數（pps）**：從資料路徑移除虛擬交換器可排除主機中的封包在處理原則時所花的時間，並增加可在 VM 中處理的封包數目。

- **減少抖動**：虛擬交換器處理取決於需要套用的原則數量，以及正在進行處理的 CPU 工作負載。 將原則強制執行卸載到硬體會藉由直接將封包傳遞至 VM 來移除該變異數，以排除主機對 VM 的通訊以及所有的軟體插斷和內容切換。

- **降低 CPU 使用率**：略過主機中的虛擬交換器會導致處理網路流量的 CPU 使用率降低。

若要使用加速網路，您必須在每個適用的 VM 上明確加以啟用。 如需相關指示，請參閱[使用加速網路建立 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。

#### <a name="receive-side-scaling"></a>接收端調整

接收端調整（RSS）是一種網路驅動程式技術，可將接收處理分散到多處理器系統中的多個 Cpu，以更有效率地分散接收網路流量。 簡單來說，RSS 可讓系統處理更多已接收的流量，因為它會使用所有可用的 Cpu，而不是只有一個。 如需 RSS 的更多技術討論，請參閱[接收端調整簡介](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)。

若要在 VM 上啟用加速網路時獲得最佳效能，您必須啟用 RSS。 RSS 也可以在不使用加速網路的 Vm 上提供權益。 如需如何判斷是否已啟用 RSS 和如何加以啟用的總覽，請參閱將[Azure 虛擬機器的網路輸送量優化](https://aka.ms/FastVM)。

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT 和 TIME_WAIT assassination

TCP TIME_WAIT 是另一個會影響網路和應用程式效能的常見設定。 在一般作業 TCP 期間，開啟和關閉許多通訊端（作為用戶端或伺服器（來源 IP：來源埠 + 目的地 IP：目的地埠））的忙碌 Vm，指定的通訊端可能會在一段很長的時間後，以 TIME_WAIT 狀態結束。 TIME_WAIT 狀態的目的是要在關閉通訊端之前，先將任何額外的資料傳遞給它。 因此，TCP/IP 堆疊通常會藉由以無訊息方式卸載用戶端的 TCP SYN 封包，來避免重複使用通訊端。

通訊端在 TIME_WAIT 中的時間量可設定。 其範圍可以從30秒到240秒。 通訊端是有限的資源，而且可以在任何指定時間使用的通訊端數目是可設定的。 （可用的通訊端數目通常大約是30000）。如果使用了可用的通訊端，或用戶端和伺服器的 TIME_WAIT 設定不相符，而且 VM 嘗試重複使用 TIME_WAIT 狀態的通訊端，則新連線將會失敗，因為 TCP SYN 封包會以無訊息方式卸載。

輸出通訊端的埠範圍值通常可在作業系統的 TCP/IP 堆疊中進行設定。 對於 TCP TIME_WAIT 設定和通訊端重複使用也是如此。 變更這些數位可能會改善擴充性。 但是根據情況而定，這些變更可能會導致互通性問題。 如果您變更這些值，應該小心。

您可以使用 TIME_WAIT assassination 來解決這項調整限制。 TIME_WAIT assassination 可在特定情況下重複使用通訊端，例如新連線的 IP 封包序號超過上一個連線的最後一個封包序號。 在此情況下，作業系統會允許建立新的連接（它會接受新的 SYN/ACK），並強制關閉先前處於 TIME_WAIT 狀態的連線。 Azure 中的 Windows Vm 支援這項功能。 若要深入瞭解其他 Vm 的支援，請洽詢作業系統廠商。

若要瞭解如何設定 TCP TIME_WAIT 設定和來源埠範圍，請參閱[可修改的設定，以改善網路效能](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)。

## <a name="virtual-network-factors-that-can-affect-performance"></a>可能會影響效能的虛擬網路因素

### <a name="vm-maximum-outbound-throughput"></a>VM 的輸出輸送量上限

Azure 提供各種不同的 VM 大小和類型，各有不同的效能功能組合。 其中一項功能是網路輸送量（或頻寬），以每秒 mb （Mbps）來測量。 因為虛擬機器是裝載在共用硬體上，所以網路容量需要在使用相同硬體的虛擬機器之間共用。 較小的虛擬機器配置比小型虛擬機器更多的頻寬。

配置給每個虛擬機器的網路頻寬是依據從虛擬機器的流出 (輸出) 流量來計量。 所有離開虛擬機器的網路流量都會計算到配置的限制，不會因為目的地不同而有差異。 例如，如果虛擬機器具有 1000-Mbps 的限制，則不論輸出流量是在相同虛擬網路中的另一部虛擬機器或 Azure 外部的虛擬機器，都適用此限制。

輸入則不會直接計量或限制。 但是還有其他因素（例如 CPU 和儲存體限制）可能會影響虛擬機器處理傳入資料的能力。

加速網路是設計來改善網路效能，包括延遲、輸送量和 CPU 使用率。 加速網路可以改善虛擬機器的輸送量，但只能達到虛擬機器配置的頻寬。

Azure 虛擬機器至少有一個連接的網路介面。 他們可能會有數個。 配置給虛擬機器的頻寬是連接至電腦之所有網路介面上所有輸出流量的總和。 換句話說，頻寬是根據每個虛擬機器來配置，而不論連接至電腦的網路介面有多少個。

[Azure 中的 Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)大小有預期的輸出輸送量，以及每個 VM 大小所支援的網路介面數目。 若要查看最大輸送量，請選取類型（例如 **[一般用途**]），然後在產生的頁面上尋找關於大小數列的區段（例如，"Dv2-series"）。 針對每個數列，有一個表格會在最後一個資料行中提供網路規格，其標題為「最大 Nic/預期的網路頻寬（Mbps）」。

輸送量限制會套用至虛擬機器。 輸送量不會受到下列因素的影響：

- **網路介面數目**：頻寬限制適用于來自虛擬機器的所有輸出流量總和。

- **加速網路**：雖然這項功能有助於達到已發佈的限制，但不會變更限制。

- **流量目的地**：所有目的地都會計入輸出限制。

- **通訊協定**：通過所有通訊協定的所有輸出流量都會計入限制。

如需詳細資訊，請參閱[虛擬機器網路頻寬](https://aka.ms/AzureBandwidth)。

### <a name="internet-performance-considerations"></a>網際網路效能考慮

如本文所述，網際網路上和 Azure 控制外部的因素可能會影響網路效能。 以下是其中一些因素：

- **延遲**：兩個目的地之間的來回時間可能會受到中繼網路上的問題影響，由不採用「最短」距離路徑的流量，以及「次佳對等互連」路徑。

- 封**包遺失**：封包遺失可能是網路擁塞、實體路徑問題和表現不佳網路裝置所造成。

- **MTU 大小/片段**：沿著路徑分割可能會導致資料抵達或封包傳入順序不好的延遲，這可能會影響封包的傳遞。

追蹤路由是一個很好的工具，可用於測量來源裝置與目的地裝置之間的網路效能特性（例如封包遺失和延遲）。

### <a name="network-design-considerations"></a>網路設計考慮

除了本文稍早所討論的考慮，虛擬網路的拓撲也會影響網路效能。 例如，將流量全域 backhauls 到單一中樞虛擬網路的中樞和輪輻設計會引進網路延遲，這會影響整體的網路效能。

網路流量通過的網路裝置數目也會影響整體延遲。 例如，在中樞和輪輻設計中，如果流量通過輪輻網路虛擬裝置和中樞虛擬裝置，再傳輸到網際網路，網路虛擬裝置可能會導致延遲。

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure 區域、虛擬網路和延遲

Azure 區域是由存在於一般地理區域中的多個資料中心所組成。 這些資料中心可能不會在彼此的實體上。 在某些情況下，它們會以10公里隔開。 虛擬網路是 Azure 實體資料中心網路上的邏輯重迭。 虛擬網路不表示資料中心內有任何特定的網路拓撲。

例如，位於相同虛擬網路和子網中的兩個 Vm 可能位於不同的機架、資料列，甚至是資料中心。 它們可以用光纖纜線的墊腳或光纖纜線的公里來分隔。 這種變化可能會在不同的 Vm 之間引進變數延遲（幾毫秒的差異）。

Vm 的地理位置，以及兩個 Vm 之間可能產生的延遲，可能會受到可用性設定組和可用性區域的設定影響。 但區域中的資料中心之間的距離是區域特定的，而且主要會受到區域中的資料中心拓撲影響。

### <a name="source-nat-port-exhaustion"></a>來源 NAT 埠耗盡

Azure 中的部署可以與公用網際網路和/或公用 IP 空間中的 Azure 外部的端點進行通訊。 當實例起始輸出連線時，Azure 會動態地將私人 IP 位址對應至公用 IP 位址。 在 Azure 建立此對應之後，輸出產生流量的傳回流量也會到達流程來源的私人 IP 位址。

針對每個輸出連線，Azure Load Balancer 需要在一段時間內維護此對應。 有了 Azure 的多租使用者本質，為每個 VM 的每個輸出流程維護此對應，可能會耗用大量資源。 因此，有一些限制是根據 Azure 虛擬網路的設定而定。 或者，若要更精確地說，Azure VM 只能在指定的時間建立特定數目的輸出連線。 達到這些限制時，VM 將無法進行更多的輸出連線。

但此行為是可設定的。 如需 SNAT 和 SNAT 埠耗盡的詳細資訊，請參閱[這篇文章](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。

## <a name="measure-network-performance-on-azure"></a>測量 Azure 上的網路效能

本文中有一些效能上限，與兩個 Vm 之間的網路延遲/來回行程時間（RTT）相關。 本節提供如何測試延遲/RTT，以及如何測試 TCP 效能和 VM 網路效能的一些建議。 您可以使用本節所述的技術，微調和效能測試稍早所討論的 TCP/IP 和網路值。 您可以將延遲、MTU、MSS 和視窗大小值插入先前提供的計算，並比較理論上的最大值與您在測試期間觀察到的實際值。

### <a name="measure-round-trip-time-and-packet-loss"></a>測量來回時間和封包遺失

TCP 效能高度依賴 RTT 和封包遺失。 Windows 和 Linux 中提供的 PING 公用程式提供最簡單的方式來測量 RTT 和封包遺失。 PING 的輸出會顯示來源與目的地之間的最小/最大/平均延遲。 它也會顯示封包遺失。 PING 預設會使用 ICMP 通訊協定。 您可以使用 PsPing 來測試 TCP RTT。 如需詳細資訊，請參閱[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)。

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>測量 TCP 連線的實際輸送量

NTttcp 是用來測試 Linux 或 Windows VM 之 TCP 效能的工具。 您可以變更各種 TCP 設定，然後使用 NTttcp 來測試優點。 如需詳細資訊，請參閱這些資源：

- [頻寬/輸送量測試（NTttcp）](https://aka.ms/TestNetworkThroughput)

- [NTttcp 公用程式](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>測量虛擬機器的實際頻寬

您可以使用稱為 iPerf 的工具來測試不同 VM 類型、加速網路等的效能。 iPerf 也可在 Linux 和 Windows 上取得。 iPerf 可以使用 TCP 或 UDP 來測試整體網路輸送量。 iPerf TCP 輸送量測試會受到本文中討論的因素（例如延遲和 RTT）所影響。 因此，如果您只想要測試最大輸送量，UDP 可能會產生較好的結果。

如需詳細資訊，請參閱下列文章：

- [針對 Expressroute 網路效能進行疑難排解](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [如何驗證傳輸到虛擬網路的 VPN 輸送量](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>偵測效率不佳的 TCP 行為

在封包捕獲中，Azure 客戶可能會看到具有 TCP 旗標（SACK、DUP 通知、重新傳輸和快速重新傳輸）的 TCP 封包，這可能表示網路效能問題。 這些封包特別指出封包遺失所造成的網路效率不佳。 但封包遺失不一定是由 Azure 效能問題所造成。 效能問題可能是應用程式問題、作業系統問題或其他可能與 Azure 平臺無關的問題所造成。

此外，請記住，網路上的某些重新傳輸和重複 Ack 是正常的。 TCP 通訊協定是為了可靠而建立的。 封包捕獲中這些 TCP 封包的辨識項不一定表示有系統的網路問題，除非是過度的。

儘管如此，這些封包類型也會指出 TCP 輸送量並未達到其最大效能，原因是本文其他章節中所討論的原因。

## <a name="next-steps"></a>後續步驟

既然您已瞭解 Azure Vm 的 TCP/IP 效能微調，您可能會想要閱讀[規劃虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)的其他考慮，或[深入瞭解如何連線和設定虛擬網路](https://docs.microsoft.com/azure/virtual-network/)。
