---
title: 驗證 Microsoft Azure 虛擬網路的 VPN 輸送量
description: 本文可協助您驗證從內部部署資源到 Azure 虛擬機器的網路輸送量。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: d2347c0688ca58698831019a193d03fe2c6721e9
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398502"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>如何驗證傳輸到虛擬網路的 VPN 輸送量

VPN 閘道連線可讓您在 Azure 內的虛擬網路和內部部署 IT 基礎結構之間建立安全的跨單位連線。

本文章說明如何驗證從內部部署資源到 Azure 虛擬機器 (VM) 的網路輸送量。

> [!NOTE]
> 本文章旨在協助診斷和修正常見的問題。 如果您在使用下列資訊後仍無法解決問題，[請連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="overview"></a>概觀

VPN 閘道連線涉及下列元件：

* 內部部署 VPN 裝置 (查看已 [驗證的 vpn 裝置](vpn-gateway-about-vpn-devices.md#devicetable)清單。 ) 
* 公用網際網路
* Azure VPN 閘道
* Azure VM

下圖顯示內部部署網路透過 VPN 到 Azure 虛擬網路的邏輯連線能力。

![客戶網路使用 VPN 到 MSFT 網路的邏輯連線能力](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>計算最大預期輸入/輸出

1. 判斷您應用程式的基準輸送量需求。
1. 判斷您的 Azure VPN 閘道輸送量限制。 如需協助，請參閱 [關於 VPN 閘道](vpn-gateway-about-vpngateways.md#gwsku)的「閘道 sku」一節。
1. 判斷 VM 大小的 [Azure VM 輸送量指引](../virtual-machines/sizes.md)。
1. 決定您網際網路服務提供者 (ISP) 的頻寬。
1. 藉由取得 VM、VPN 閘道或 ISP 的最小頻寬來計算您預期的輸送量;這是以每秒百萬位元 (/) 除以八 (8) 來測量。

如果您的計算輸送量不符合您應用程式的基準輸送量需求，您必須增加您識別為瓶頸之資源的頻寬。 如果要調整 Azure VPN 閘道的大小，請參閱 [變更閘道 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。 如果調整虛擬機器的大小，請參閱 [調整 VM 的大小](../virtual-machines/windows/resize-vm.md)。 如果您未遇到預期的網際網路頻寬，也可以聯絡您的 ISP。

> [!NOTE]
> VPN 閘道輸送量是所有 Site-to-Site\VNET-to-VNET 或點對站連接的匯總。

## <a name="validate-network-throughput-by-using-performance-tools"></a>使用效能工具驗證網路輸送量

由於測試時的 VPN 通道輸送量飽和情況無法呈現準確的結果，因此應在非尖峰時段執行此驗證。

iPerf 是我們用於此測試的工作，分別在 Windows 與 Linux 上工作，並具有用戶端與伺服器模式。 它受限於適用于 Windows Vm 的3Gbps。

此工具不會對磁碟執行任何讀取/寫入作業。 此工具只會產生從一端到另一端自我產生的 TCP 流量。 它會根據測量用戶端和伺服器節點之間可用頻寬的測試來產生統計資料。 在兩個節點之間進行測試時，一個節點會作為伺服器，而另一個節點則做為用戶端。 完成此測試之後，建議您反轉節點的角色，以測試兩個節點上的上傳和下載輸送量。

### <a name="download-iperf"></a>下載 iPerf

下載 [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip)。 如需詳細資訊，請參閱 [iPerf 文件](https://iperf.fr/iperf-doc.php)。

 > [!NOTE]
 > 本文所討論的協力廠商產品是由獨立于 Microsoft 的公司製造。 Microsoft 對於這些產品的效能或可靠性不提供任何默示或其他保證。

### <a name="run-iperf-iperf3exe"></a>執行 iPerf (iperf3.exe)

1. 啟用允許流量的 NSG/ACL 規則 (適用於 Azure VM 上的公用 IP 位址測試)。

1. 在這兩個節點上，啟用連接埠 5001 的防火牆例外狀況。

   **Windows：** 以系統管理員身分執行下列命令。

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   如果要在測試完成時移除規則，請執行此命令：

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux：** Azure Linux 映射有寬鬆的防火牆。 如果有應用程式接聽連接埠，則允許通過流量。 受保護的自訂映像可能需要明確開啟連接埠。 常見的 Linux OS 層防火牆包括 `iptables`、`ufw` 或 `firewalld`。

1. 在伺服器節點上，請變更至將 iperf3.exe 解壓縮的目錄。 然後，在伺服器模式中執行 iPerf，並將它設定為在埠5001上接聽，作為下列命令：

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > 您可以自訂埠5001，以考慮環境中特定的防火牆限制。

1. 在用戶端節點上，變更至將 iperf 工具解壓縮的目錄，然後執行下列命令：

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   用戶端會將埠5001上的三十秒流量導向至伺服器。 旗標 '-P ' 表示我們正在進行32同時與伺服器節點的連接。

   下列畫面顯示此範例的輸出：

   ![輸出](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (選用) 如果要保留測試結果，請執行此命令：

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. 完成上述步驟之後，請以反轉的角色執行相同的步驟，讓伺服器節點現在會是用戶端節點，反之亦然。

> [!Note]
> Iperf 不是唯一的工具。 [NTTTCP 是測試的替代方案](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)。

## <a name="test-vms-running-windows"></a>測試執行 Windows 的 Vm

### <a name="load-latteexe-onto-the-vms"></a>將 Latte.exe 載入至 Vm

下載[Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)的最新版本

請考慮將 Latte.exe 放在不同的資料夾中，例如 `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>允許 Latte.exe 通過 Windows 防火牆

在接收者上，在 Windows 防火牆上建立允許規則，以允許 Latte.exe 的流量抵達。 最簡單的方式是依名稱允許整個 Latte.exe 程式，而不是允許特定的 TCP 埠輸入。

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>允許透過像這樣的 Windows 防火牆進行 Latte.exe

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

例如，如果您將 latte.exe 複製到 "c:\tools" 資料夾，這會是命令

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>執行延遲測試

在接收者上啟動 latte.exe， (從 CMD （而不是從 PowerShell) ）執行：

`latte -a <Receiver IP address>:<port> -i <iterations>`

大約65k 反覆運算的長度足以傳回具代表性的結果。

任何可用的埠號碼都沒問題。

如果 VM 的 IP 位址是10.0.0.4，它看起來會像這樣

`latte -c -a 10.0.0.4:5005 -i 65100`

在傳送者上啟動 latte.exe (從 CMD 執行，而不是從 PowerShell) 

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

產生的命令與接收者上的相同，但加上 "-c" 表示這是 "用戶端" 或寄件者。

`latte -c -a 10.0.0.4:5005 -i 65100`

等候結果。 視 Vm 的距離而定，可能需要幾分鐘的時間才能完成。 請考慮在執行較長的測試之前，先使用較少的反覆運算來測試是否成功。

## <a name="test-vms-running-linux"></a>測試執行 Linux 的 Vm

使用 [SockPerf](https://github.com/mellanox/sockperf) 來測試 vm。

### <a name="install-sockperf-on-the-vms"></a>在 Vm 上安裝 SockPerf

在 Linux Vm 上 (傳送者和接收者) ，請執行下列命令以在 Vm 上準備 SockPerf：

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL-安裝 GIT 和其他有用的工具

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu-安裝 GIT 和其他有用的工具

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash-全部

從 bash 命令列 (假設已安裝 git) 

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

執行速度較慢，可能需要幾分鐘的時間

`make`

讓安裝快速

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>在 Vm 上執行 SockPerf

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>安裝後的範例命令。 伺服器/接收者-假設伺服器的 IP 為10.0.0。4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>用戶端-假設伺服器的 IP 為10.0.0。4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> 請確定沒有中繼躍點 (例如虛擬裝置在 VM 與閘道之間的輸送量測試期間) 。
> 如果結果不佳 (的整體輸送量) 來自上述的 iPERF/NTTTCP 測試，請參閱下列文章，以瞭解問題可能根本原因背後的主要因素： https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

尤其是，在這些測試期間，從用戶端和伺服器並行收集的封包捕獲追蹤 (Wireshark/網路監視器) 會協助評估不良的效能。 這些追蹤可能包含封包遺失、高延遲、MTU 大小。 片段、TCP 0 視窗、順序不足的片段等等。

## <a name="address-slow-file-copy-issues"></a>處理檔案複製變慢的問題

即使以上述步驟評估的整體輸送量 (iPERF/NTTTCP/等等。) 是很好的，您可能會在使用 Windows 檔案總管或透過 RDP 會話拖放時遇到緩慢的檔案卸載。 此問題一般是因為下列一個或多個因素造成︰

* 如 Windows 檔案總管與 RDP 的檔案應用程式，並未在複製檔案時使用多執行緒。 為了提升效能，請使用如 [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) 等多執行緒的檔案複製應用程式，以 16 或 32 條執行緒複製檔案。 若要在 Richcopy 中變更檔案複製的執行緒號碼，請按一下 [**動作**  >  **複製選項**檔案  >  **複製**]。

   ![檔案複製變慢的問題](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > 並非所有應用程式都是相同的，而且並非所有的應用程式/程式都會利用所有線程。 如果您執行測試，您可能會看到部分執行緒是空的，而且不會提供精確的輸送量結果。
   > 若要檢查您的應用程式檔案傳輸效能，請連續或減少增加執行緒的數目來使用多執行緒，以找出應用程式或檔案傳輸的最佳輸送量。

* VM 磁碟讀取/寫入速度不足。 如需詳細資訊，請參閱 [Azure 儲存體疑難排解](../storage/common/storage-e2e-troubleshooting.md)。

## <a name="on-premises-device-external-facing-interface"></a>內部部署裝置的外部對應介面

提及您希望 Azure 透過局域網路閘道上的 VPN 達到的內部部署範圍子網。 同時，將 Azure 中的 VNET 位址空間定義為內部部署裝置。

* **路由型閘道**：路由式 vpn 的原則或流量選取器會設定為任何) 的 (或萬用字元。

* 以**原則為基礎的閘道**：根據內部部署網路與 Azure VNet 之間的位址首碼組合，以原則為基礎的 vpn 會透過 IPsec 通道來加密和導向封包。 原則 (或流量選取器) 通常定義為 VPN 組態中的存取清單。

* **UsePolicyBasedTrafficSelector** 連線：在連線上 $True ( "UsePolicyBasedTrafficSelectors" 將會設定 Azure VPN 閘道，以連線至內部部署以原則為基礎的 VPN 防火牆。 如果您啟用 PolicyBasedTrafficSelectors，您必須確定您的 VPN 裝置具有與內部部署網路的所有組合一起定義的相符流量選取器 (局域網路閘道) 首碼和 Azure 虛擬網路首碼之間，而不是任何對任意。

不適當的設定可能會導致通道、封包卸載、不正確的輸送量和延遲等頻繁地中斷連線。

## <a name="check-latency"></a>檢查延遲

您可以使用下列工具來檢查延遲：

* WinMTR
* TCPTraceroute
* `ping``psping`此外 (這些工具可以提供較好的 RTT 評估，但無法在所有情況下使用。 ) 

![檢查延遲](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

如果您在進入 MS 網路骨幹之前發現任何躍點有高延遲的尖峰，您可能會想要繼續進一步調查您的網際網路服務提供者。

如果從 "msn.net" 內的躍點發現大量、不尋常的延遲尖峰，請聯絡 MS 支援以進行進一步調查。

## <a name="next-steps"></a>接下來的步驟

如需詳細資訊或協助，請參閱下列連結：

* [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
