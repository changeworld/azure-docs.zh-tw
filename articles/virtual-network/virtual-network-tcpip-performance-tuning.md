---
title: Azure VM 的 TCP/IP 性能調優 |微軟文檔
description: 瞭解各種常見的 TCP/IP 性能調優技術及其與 Azure VM 的關係。
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
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297775"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>適用於 Azure VM 的 TCP/IP 效能微調

本文討論了常見的 TCP/IP 性能調優技術，以及在 Azure 上運行虛擬機器時需要考慮的一些事項。 它將提供技術的基本概述，並探討如何調整它們。

## <a name="common-tcpip-tuning-techniques"></a>常見的 TCP/IP 調優技術

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU、碎片和大發送卸載

#### <a name="mtu"></a>MTU

最大傳輸單元 （MTU） 是最大大小幀（資料包），以位元組為單位指定，可以通過網路介面發送。 MTU 是可配置的設置。 Azure VM 上使用的預設 MTU 以及全域大多數網路設備上的預設設置為 1，500 位元組。

#### <a name="fragmentation"></a>分割

當發送的資料包超過網路介面的 MTU 時，就會發生碎片。 TCP/IP 堆疊會將資料包分解為符合介面 MTU 的較小部分（片段）。 碎片發生在 IP 層，並且獨立于基礎協定（如 TCP）。 當通過 MTU 為 1，500 的網路介面發送 2，000 位元組資料包時，該資料包將分解為一個 1，500 位元組資料包和一個 500 位元組資料包。

源和目標之間的路徑中的網路設備可以丟棄超出 MTU 的資料包，也可以將資料包分割成更小的部分。

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>IP 資料包中的"不碎片位"

"不碎片 （DF）"位是 IP 協定標頭中的標誌。 DF 位指示發送方和接收方之間路徑上的網路設備不得分割資料包。 此位可能由於多種原因設置。 （有關一個示例，請參閱本文的"路徑 MTU 發現"部分。當網路設備收到具有"不碎片"位集的資料包，並且該資料包超過設備的介面 MTU 時，標準行為是設備丟棄資料包。 設備將 ICMP 碎片所需消息發送回資料包的原始源。

#### <a name="performance-implications-of-fragmentation"></a>碎片的性能影響

碎片可能會對性能產生負面影響。 影響性能的主要原因之一是資料包碎片和重新組裝對 CPU/記憶體的影響。 當網路設備需要分割資料包時，它將不得不分配 CPU/記憶體資源以執行碎片。

重新組裝資料包時也會發生同樣的事情。 網路設備必須存儲所有片段，直到收到它們，以便它可以將它們重新組裝到原始資料包中。 這種碎片和重新組裝過程也可能導致延遲。

碎片的另一個可能的負面性能影響是碎片資料包可能到達順序不一。 當資料包接收失序時，某些類型的網路設備可能會丟棄它們。 發生這種情況時，必須重新傳輸整個資料包。

片段通常被網路防火牆等安全設備丟棄，或者網路設備的接收緩衝區耗盡時丟棄。 當網路設備的接收緩衝區耗盡時，網路設備將嘗試重新組合分散的資料包，但沒有資源來存儲和重新假定資料包。

碎片可以被視為一種負操作，但當您通過互聯網連接不同的網路時，有必要支援碎片。

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>修改 MTU 的好處和後果

一般來說，您可以通過增加 MTU 來創建更高效的網路。 傳輸的每個資料包都有添加到原始資料包的標頭資訊。 當碎片創建更多資料包時，標頭開銷會更多，這會降低網路的效率。

範例如下。 乙太網頭大小為 14 位元組加上 4 位元組幀檢查序列，以確保幀一致性。 如果發送了一個 2，000 位元組資料包，則在網路上添加 18 位元組的乙太網開銷。 如果資料包被分割成 1，500 位元組資料包和 500 位元組資料包，則每個資料包將具有 18 位元組的乙太網標頭，總共 36 個位元組。

請記住，增加 MTU 不一定能創建更高效的網路。 如果應用程式僅發送 500 位元組資料包，則無論 MTU 是 1，500 位元組還是 9，000 位元組，都將存在相同的標頭開銷。 只有當網路使用受 MTU 影響的較大資料包大小時，網路才會變得更加高效。

#### <a name="azure-and-vm-mtu"></a>Azure 和 VM MTU

Azure VM 的預設 MTU 為 1，500 位元組。 Azure 虛擬網路堆疊將嘗試以 1，400 位元組分段資料包。

請注意，虛擬網路堆疊本質上並非效率低下，因為它以 1，400 位元組分段資料包，即使 VM 的 MTU 為 1，500。 很大一部分網路資料包小於 1，400 或 1，500 位元組。

#### <a name="azure-and-fragmentation"></a>Azure 和碎片

虛擬網路堆疊設置為刪除"順序外片段"，即未按原始碎片順序到達的分散資料包。 這些資料包被丟棄的主要原因是 2018 年 11 月宣佈的名為"碎片"的網路安全性漏洞。

碎片Smack是Linux內核處理碎片IPv4和IPv6資料包重新組裝方式的缺陷。 遠端攻擊者可能使用此缺陷觸發昂貴的片段重新組裝操作，這可能導致 CPU 增加和目標系統上的拒絕服務。

#### <a name="tune-the-mtu"></a>調整 MTU

您可以配置 Azure VM MTU，就像在任何其他作業系統中一樣。 但是，在配置 MTU 時，應考慮 Azure 中發生的碎片，如上所述。

我們不鼓勵客戶增加 VM M數。 此討論旨在解釋 Azure 如何實現 MTU 並執行碎片的詳細資訊。

> [!IMPORTANT]
>增加 MTU 並不已知可以提高性能，並且可能會對應用程式性能產生負面影響。
>
>

#### <a name="large-send-offload"></a>大發送卸載

大型發送卸載 （LSO） 可以通過將資料包的分段卸載到乙太網配接器來提高網路性能。 啟用 LSO 後，TCP/IP 堆疊將創建一個大型 TCP 資料包，並將其發送到乙太網配接器進行分段，然後再轉發它。 LSO 的好處是，它可以將 CPU 從資料包分割到符合 MTU 的大小中，並將該處理卸載到乙太網介面，在硬體中執行。 要瞭解有關 LSO 的優勢，請參閱[支援大型發送卸載](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)。

啟用 LSO 後，Azure 客戶在執行資料包捕獲時可能會看到較大的幀大小。 這些大型幀大小可能導致一些客戶認為碎片正在發生，或者大型 MTU 正在使用時不使用。 使用 LSO，乙太網配接器可以將更大的最大段大小 （MSS） 通告給 TCP/IP 堆疊以創建更大的 TCP 資料包。 然後，整個非分段幀將轉發到乙太網配接器，並在 VM 上執行的資料包捕獲中可見。 但是，根據乙太網配接器的 MTU，資料包將被乙太網配接器分解成許多較小的幀。

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS 視窗縮放和 PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP 最大段大小

TCP 最大段大小 （MSS） 是一種限制 TCP 段大小的設置，可避免 TCP 資料包的碎片化。 作業系統通常使用此公式來設置 MSS：

`MSS = MTU - (IP header size + TCP header size)`

IP 標頭和 TCP 標頭各為 20 個位元組，或總計 40 位元組。 因此，MTU 為 1，500 的介面的 MSS 為 1，460。 但 MSS 是可配置的。

在 TCP 三向握手中同意在源和目標之間設置 TCP 會話時同意此設置。 兩側都發送一個 MSS 值，兩個值的下部用於 TCP 連接。

請記住，源和目標 MM 並不是決定 MSS 值的唯一因素。 中間網路設備（如 VPN 閘道（包括 Azure VPN 閘道）可以獨立于源和目標調整 MTU，以確保最佳的網路性能。

#### <a name="path-mtu-discovery"></a>路徑 MTU 發現

MSS 已協商，但它可能未指示可以使用的實際 MSS。 這是因為源和目標之間的路徑中的其他網路設備的 MTU 值可能低於源和目標。 在這種情況下，MTU 小於資料包的設備將丟棄資料包。 設備將發送回包含其 MTU 的 ICMP 碎片（類型 3，代碼 4）消息。 此 ICMP 消息允許源主機適當地減少其路徑 MTU。 該過程稱為路徑 MTU 發現 （PMTUD）。

PMTUD 過程效率低下，會影響網路性能。 當發送的資料包超過網路路徑的 MTU 時，需要用較低的 MSS 重新傳輸資料包。 如果發送方未收到 ICMP 碎片所需的消息，可能是因為路徑中的網路防火牆（通常稱為*PMTUD 黑洞*），發送方不知道它需要降低 MSS 並持續重新傳輸資料包。 這就是為什麼我們不建議增加 Azure VM MTU 的原因。

#### <a name="vpn-and-mtu"></a>VPN 和 MTU

如果使用執行封裝的 VM（如 IPsec VPN），則有關資料包大小和 MTU 還有其他注意事項。 VPN 向資料包添加更多標頭，這增加了資料包大小，並且需要更小的 MSS。

對於 Azure，我們建議您將 TCP MSS 夾緊設置為 1，350 位元組，隧道介面 MTU 設置為 1，400。 有關詳細資訊，請參閱 VPN[設備和 IPSec/IKE 參數頁](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>延遲、往返時間和 TCP 視窗縮放

#### <a name="latency-and-round-trip-time"></a>延遲和往返時間

網路延遲受光纖網路光速控制。 TCP 的網路輸送量還受兩個網路設備之間的往返時間 （RTT） 的有效控制。

| | | | |
|-|-|-|-|
|**路線**|**Distance**|**單程時間**|**RTT**|
|紐約到三藩市|4，148 公里|21 毫秒|42 毫秒|
|紐約到倫敦|5，585 公里|28 毫秒|56 毫秒|
|紐約到悉尼|15，993 公里|80 毫秒|160 毫秒|

此表顯示兩個位置之間的直線距離。 在網路中，距離通常比直線距離長。 下面是一個簡單的公式，用於計算受光速控制的最小 RTT：

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

您可以使用 200 進行傳播速度。 這是光在1毫秒內傳播的距離，以米為單位。

讓我們以紐約到三藩市為例。 直線距離為4，148公里。 將該值插入方程中，我們得到以下內容：

`Minimum RTT = 2 * (4,148 / 200)`

方程的輸出以毫秒為單位。

如果要獲得最佳的網路性能，邏輯選項是選擇它們之間的距離最短的目標。 您還應設計虛擬網路以優化流量路徑並減少延遲。 有關詳細資訊，請參閱本文的"網路設計注意事項"部分。

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>對 TCP 的延遲和往返時間影響

往返時間對最大 TCP 輸送量有直接影響。 在 TCP 協定中，*視窗大小*是發送方需要接收接收方確認之前可以通過 TCP 連接發送的最大流量。 如果 TCP MSS 設置為 1，460，並且 TCP 視窗大小設置為 65，535，則發送方可以在接收接收接收方的確認之前發送 45 個數據包。 如果寄件者未收到確認，它將重新傳輸資料。 公式如下：

`TCP window size / TCP MSS = packets sent`

在此示例中，65，535 / 1，460 四捨五入為 45。

這種"等待確認"狀態是保證可靠傳遞資料的一種機制，是 RTT 影響 TCP 輸送量的原因。 發送方等待確認的時間越長，在發送更多資料之前需要等待的時間越長。

以下是計算單個 TCP 連接的最大輸送量的公式：

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

此表顯示單個 TCP 連接的最大百萬位元組/秒輸送量。 （對於可讀性，百萬位元組用於度量單位。

| | | | |
|-|-|-|-|
|**TCP 視窗大小（位元組）**|**RTT 延遲 （毫秒）**|**最大百萬位元組/秒輸送量**|**最大百萬位元/秒輸送量**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

如果資料包丟失，發送方重新傳輸已發送的資料時，TCP 連接的最大輸送量將降低。

#### <a name="tcp-window-scaling"></a>TCP 視窗縮放

TCP 視窗縮放是一種動態增加 TCP 視窗大小以允許在需要確認之前發送更多資料的技術。 在前面的示例中，在需要確認之前將發送 45 個數據包。 如果增加在需要確認之前可以發送的資料包數，則減少發送方等待確認的次數，這會增加 TCP 的最大輸送量。

下表說明了這些關係：

| | | | |
|-|-|-|-|
|**TCP 視窗大小（位元組）**|**RTT 延遲 （毫秒）**|**最大百萬位元組/秒輸送量**|**最大百萬位元/秒輸送量**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

但是 TCP 視窗大小的 TCP 標頭值只有 2 個位元組長，這意味著接收視窗的最大值為 65，535。 為了增加最大視窗大小，引入了 TCP 視窗比例因數。

縮放因數也是您可以在作業系統中配置的設置。 以下是使用比例因數計算 TCP 視窗大小的公式：

`TCP window size = TCP window size in bytes \* (2^scale factor)`

以下是視窗比例因數 3 和視窗大小為 65，535 的計算：

`65,535 \* (2^3) = 262,140 bytes`

比例因數 14 會導致 TCP 視窗大小為 14（允許的最大偏移）。 TCP 視窗大小將為 1，073，725，440 位元組（8.5 十億位元）。

#### <a name="support-for-tcp-window-scaling"></a>支援 TCP 視窗縮放

Windows 可以為不同的連線類型設置不同的縮放因數。 （連接類別包括資料中心、互聯網等。您可以使用`Get-NetTCPConnection`PowerShell 命令查看視窗縮放連線類型：

```powershell
Get-NetTCPConnection
```

您可以使用`Get-NetTCPSetting`PowerShell 命令查看每個類的值：

```powershell
Get-NetTCPSetting
```

您可以使用`Set-NetTCPSetting`PowerShell 命令在 Windows 中設置初始 TCP 視窗大小和 TCP 縮放因數。 有關詳細資訊，請參閱[設置網路CP設置](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)。

```powershell
Set-NetTCPSetting
```

這些是`AutoTuningLevel`： 的有效 TCP 設置：

| | | | |
|-|-|-|-|
|**自動調諧級別**|**縮放因數**|**縮放乘數**|**<br/>計算最大視窗大小的公式**|
|已停用|None|None|視窗大小|
|Restricted|4|2×4|視窗大小 = （2⁄4）|
|高度受限|2|2×2|視窗大小 = （2⁄2）|
|正常|8|2×8|視窗大小 = （2+8）|
|Experimental|14|2×14|視窗大小 = （2+14）|

這些設置最有可能影響 TCP 性能，但請記住，Internet 上的許多其他因素（Azure 控制之外）也會影響 TCP 性能。

#### <a name="increase-mtu-size"></a>增加 MTU 大小

因為較大的 MTU 意味著更大的 MSS，因此您可能想知道增加 MTU 是否會提高 TCP 性能。 大概沒有。 資料包大小除了 TCP 流量之外，還有優點和缺點。 如前所述，影響 TCP 輸送量性能的最重要因素是 TCP 視窗大小、資料包丟失和 RTT。

> [!IMPORTANT]
> 我們不建議 Azure 客戶更改虛擬機器上的預設 MTU 值。
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>加速網路並接收側擴展

#### <a name="accelerated-networking"></a>加速網路

虛擬機器網路功能歷來在來賓 VM 和虛擬機器管理程式/主機上都是 CPU 密集型功能。 通過主機傳輸的每個資料包都由主機 CPU 在軟體中處理，包括所有虛擬網路封裝和取消封裝。 因此，通過主機的流量越多，CPU 負載就越高。 如果主機 CPU 忙於其他操作，這也會影響網路輸送量和延遲。 Azure 通過加速網路解決此問題。

通過 Azure 的內部可程式設計硬體和 SR-IOV 等技術，加速網路提供一致的超低網路延遲。 加速網路將 Azure 軟體定義的網路堆疊從 CPU 中移出，並移動到基於 FPGA 的 SmartPGA 中。 此更改使最終使用者應用程式能夠回收計算週期，從而減少 VM 的負載，減少抖動和延遲的不一致。 換句話說，性能可以更確定。

通過允許來賓 VM 繞過主機並直接使用主機的 SmartNIC 建立資料路徑，加速網路提高了性能。 以下是加速網路的一些好處：

- **更低的延遲/每秒更高的資料包 （pps）：** 從資料路徑中刪除虛擬交換器消除了資料包在主機中用於策略處理所花費的時間，並增加了可在 VM 中處理的資料包數。

- **減少抖動**：虛擬交換器處理取決於需要應用的策略量和執行處理的 CPU 的工作負載。 將策略強制卸載到硬體可消除這種可變性，將資料包直接傳遞到 VM，從而消除了主機到 VM 的通信以及所有軟體插斷和上下文切換。

- **CPU 利用率降低**：繞過主機中的虛擬交換器會導致處理網路流量的 CPU 利用率降低。

要使用加速網路，您需要在每個適用的 VM 上顯式啟用它。 有關說明[，請參閱創建具有加速網路的 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。

#### <a name="receive-side-scaling"></a>接收側縮放

接收側擴展 （RSS） 是一種網路驅動程式技術，通過在多處理器系統中跨多個 CPU 分發接收處理，從而更有效地分配網路流量的接收。 簡單地說，RSS 允許系統處理更多接收的流量，因為它使用所有可用的 CPU，而不是僅一個 CPU。 有關 RSS 的更多技術討論，請參閱[介紹以接收邊縮放](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)。

為了在 VM 上啟用加速網路時獲得最佳性能，您需要啟用 RSS。 RSS 還可以為不使用加速網路的 VM 提供優勢。 有關如何確定是否啟用 RSS 以及如何啟用 RSS 的概述，請參閱[優化 Azure 虛擬機器的網路輸送量](https://aka.ms/FastVM)。

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCPTIME_WAIT和TIME_WAIT暗殺

TCP TIME_WAIT是另一個影響網路和應用程式性能的常見設置。 在打開和關閉許多通訊端（源 IP：源埠 + 目標 IP：目標埠）的繁忙 VM 上，給定的通訊端可能長時間處於TIME_WAIT狀態。 TIME_WAIT狀態用於允許在關閉通訊端之前在通訊端上傳遞任何其他資料。 因此，TCP/IP 堆疊通常通過靜默丟棄用戶端的 TCP SYN 資料包來阻止通訊端的重用。

通訊端處於TIME_WAIT的時間是可配置的。 它可以範圍從30秒到240秒。 通訊端是有限資源，可在任何給定時間使用的通訊端數量是可配置的。 （可用通訊端的數量通常約為 30，000 個。如果使用可用的通訊端，或者用戶端和伺服器TIME_WAIT設置不匹配，並且 VM 嘗試在TIME_WAIT狀態下重用通訊端，則新連接將失敗，因為 TCP SYN 資料包被靜默丟棄。

出站通訊端的埠範圍值通常在作業系統的 TCP/IP 堆疊中配置。 TCPTIME_WAIT設置和通訊端重用也是如此。 更改這些數位可能會提高可擴充性。 但是，根據具體情況，這些更改可能會導致互通性問題。 如果更改這些值，應小心謹慎。

您可以使用TIME_WAIT暗殺來解決這種縮放限制。 TIME_WAIT暗殺允許在某些情況下重用通訊端，例如新連接的 IP 資料包中的序號超過上一個連接中最後一個資料包的序號。 在這種情況下，作業系統將允許建立新連接（它將接受新的 SYN/ACK），並強制關閉處於TIME_WAIT狀態的上一個連接。 Azure 中的 Windows VM 支援此功能。 要瞭解其他 VM 中的支援，請諮詢作業系統供應商。

要瞭解如何配置 TCP TIME_WAIT設置和源埠範圍，請參閱[可修改以提高網路性能的設置](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)。

## <a name="virtual-network-factors-that-can-affect-performance"></a>可能影響性能的虛擬網路因素

### <a name="vm-maximum-outbound-throughput"></a>VM 最大出站輸送量

Azure 提供各種 VM 大小和類型，每種類型都有不同的性能組合。 這些功能之一是網路輸送量（或頻寬），以兆比特/秒 （Mbps） 為單位。 由於虛擬機器託管在共用硬體上，因此需要使用相同的硬體在虛擬機器之間公平共用網路容量。 比較小的虛擬機器分配更大的虛擬機器的頻寬更多。

配置給每個虛擬機器的網路頻寬是依據從虛擬機器的流出 (輸出) 流量來計量。 所有離開虛擬機器的網路流量都會計算到配置的限制，不會因為目的地不同而有差異。 例如，如果虛擬機器具有 1，000 Mbps 的限制，則無論出站流量是針對同一虛擬網路中的另一個虛擬機器還是 Azure 外部的虛擬機器，該限制都適用。

輸入則不會直接計量或限制。 但是，還有其他因素，如 CPU 和存儲限制，可能會影響虛擬機器處理傳入資料的能力。

加速網路旨在提高網路性能，包括延遲、輸送量和 CPU 利用率。 加速網路可以提高虛擬機器的輸送量，但它只能達到虛擬機器分配的頻寬。

Azure 虛擬機器至少附加了一個網路介面。 他們可能有幾個。 分配給虛擬機器的頻寬是連接到電腦的所有網路介面的所有出站流量的總和。 換句話說，頻寬是按虛擬機器分配的，而不管連接到電腦的網路介面數是多少。

Azure[中 Windows 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)詳細介紹了預期的出站輸送量和每個 VM 大小支援的網路介面數。 要查看最大輸送量，請選擇類型（如**通用**），然後在生成的頁面上查找有關大小系列的部分（例如，"Dv2 系列"）。 對於每個系列，有一個表，該表在最後一列中提供網路規範，標題為"最大 NIC/ 預期網路頻寬 （Mbps）。"

輸送量限制會套用至虛擬機器。 輸送量不受以下因素影響：

- **網路介面數**：頻寬限制適用于來自虛擬機器的所有出站流量的總和。

- **加速網路**：雖然此功能有助於達到已發佈的限制，但它不會改變限制。

- **流量目的地**：所有目的地都會計入輸出限制。

- **通訊協定**：通過所有通訊協定的所有輸出流量都會計入限制。

有關詳細資訊，請參閱[虛擬機器網路頻寬](https://aka.ms/AzureBandwidth)。

### <a name="internet-performance-considerations"></a>互聯網性能注意事項

正如本文中所述，Internet 上的因素和 Azure 控制之外的因素可能會影響網路性能。 以下是一些因素：

- **延遲**：兩個目的地之間的往返時間可能受中間網路問題、不採用"最短"距離路徑的流量以及次優對等路徑的影響。

- **資料包丟失**：資料包丟失可能由網路擁塞、實體路徑問題和網路設備性能不佳引起。

- **MTU 大小/碎片**：路徑上的碎片可能導致資料到達或資料包到達順序過大的延遲，從而影響資料包的傳遞。

跟蹤路由是測量源設備和目標設備之間每個網路路徑上網路性能特徵（如資料包丟失和延遲）的良好工具。

### <a name="network-design-considerations"></a>網路設計注意事項

除了本文前面討論的注意事項外，虛擬網路的拓撲可能會影響網路的性能。 例如，將全域流量回拉到單中心虛擬網路的集線器和分支設計將引入網路延遲，這將影響整體網路性能。

網路流量通過的網路設備數量也會影響總體延遲。 例如，在集線器和輻條設計中，如果流量在傳輸到 Internet 之前通過分支網路虛擬裝置和集線器虛擬裝置，則網路虛擬裝置可能會引入延遲。

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure 區域、虛擬網路和延遲

Azure 區域由存在於一般地理區域中的多個資料中心組成。 這些資料中心可能不是物理上的彼此。 在某些情況下，它們相隔多達 10 公里。 虛擬網路是 Azure 物理資料中心網路之上的邏輯疊加。 虛擬網路並不意味著資料中心內任何特定的網路拓撲。

例如，同一虛擬網路和子網中的兩個 VM 可能位於不同的機架、行甚至資料中心中。 它們可以由英尺的光纜或數公里的光纜隔開。 此變體可能會引入不同 VM 之間的可變延遲（幾毫秒差異）。

VM 的地理位置以及兩個 VM 之間可能產生的延遲可能受可用性集和可用性區域的配置的影響。 但是，區域中的資料中心之間的距離是特定于區域的，主要受該區域資料中心拓撲的影響。

### <a name="source-nat-port-exhaustion"></a>源 NAT 埠耗盡

Azure 中的部署可以在公共 Internet 和/或公共 IP 空間中與 Azure 外部的終結點進行通信。 當實例啟動出站連接時，Azure 會動態地將私人 IP 位址映射到公共 IP 位址。 Azure 創建此映射後，出站源流的返回流量也可以到達流源自的私人 IP 位址。

對於每個出站連接，Azure 負載等化器需要維護此映射一段時間。 使用 Azure 的多租戶特性，為每個 VM 維護此映射可能佔用大量資源。 因此，根據 Azure 虛擬網路的配置設置和設置有限制。 或者，更準確地說，Azure VM 只能在給定時間創建一定數量的出站連接。 達到這些限制後，VM 將無法建立更多的出站連接。

但是此行為是可配置的。 有關 SNAT 和 SNAT 埠耗盡的詳細資訊，請參閱[本文](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。

## <a name="measure-network-performance-on-azure"></a>在 Azure 上測量網路性能

本文中的許多性能最大值與兩個 VM 之間的網路延遲/往返時間 （RTT） 相關。 本節提供有關如何測試延遲/RTT 以及如何測試 TCP 性能和 VM 網路性能的一些建議。 您可以使用本節中描述的技術來調整和效能測試前面討論的 TCP/IP 和網路值。 您可以將延遲、MTU、MSS 和視窗大小值插入到前面提供的計算中，並將理論最大值與測試期間觀察到的實際值進行比較。

### <a name="measure-round-trip-time-and-packet-loss"></a>測量往返時間和資料包丟失

TCP 性能嚴重依賴 RTT 和資料包丟失。 Windows 和 Linux 中可用的 PING 實用程式提供了測量 RTT 和資料包丟失的最簡單方法。 PING 的輸出將顯示源和目標之間的最小/最大/平均延遲。 它還將顯示資料包丟失。 預設情況下，PING 使用 ICMP 協定。 您可以使用 PsPing 來測試 TCP RTT。 有關詳細資訊，請參閱[Psping](https://docs.microsoft.com/sysinternals/downloads/psping)。

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>測量 TCP 連接的實際輸送量

NTttcp 是測試 Linux 或 Windows VM 的 TCP 性能的工具。 您可以更改各種 TCP 設置，然後使用 NTttcp 測試優點。 如需詳細資訊，請參閱這些資源：

- [頻寬/輸送量測試（NTttcp）](https://aka.ms/TestNetworkThroughput)

- [NTttcp 實用程式](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>測量虛擬機器的實際頻寬

通過使用稱為 iPerf 的工具，可以測試不同 VM 類型、加速網路等的性能。 iPerf 在 Linux 和 Windows 上也可用。 iPerf 可以使用 TCP 或 UDP 測試總體網路輸送量。 iPerf TCP 輸送量測試受本文中討論的因素（如延遲和 RTT）的影響。 因此，如果您只想測試最大輸送量，UDP 可能會產生更好的結果。

如需詳細資訊，請參閱這些文章：

- [故障排除快速路由網路性能](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [如何驗證傳輸到虛擬網路的 VPN 輸送量](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>檢測低效的 TCP 行為

在資料包捕獲中，Azure 客戶可能會看到帶有 TCP 標誌（SACK、DUP ACK、RETRANSMIT 和快速 RETRANSMIT）的 TCP 資料包，這些資料包可能指示網路性能問題。 這些資料包特別表示資料包丟失導致的網路效率低下。 但是資料包丟失不一定是由 Azure 性能問題引起的。 性能問題可能是應用程式問題、作業系統問題或可能與 Azure 平臺沒有直接相關的其他問題造成的。

此外，請記住，一些重新傳輸和重複的 ACK 在網路上是正常的。 TCP 協定的構建是可靠的。 資料包捕獲中這些 TCP 資料包的證據並不一定表示存在系統網路問題，除非它們過多。

不過，這些資料包類型表明 TCP 輸送量沒有達到其最大性能，原因在本文的其他部分中討論。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解了 Azure VM 的 TCP/IP 性能調優，您可能需要瞭解[規劃虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)的其他注意事項，或[瞭解有關連接和配置虛擬網路的更多內容](https://docs.microsoft.com/azure/virtual-network/)。
