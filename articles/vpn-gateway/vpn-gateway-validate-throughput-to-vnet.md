---
title: 驗證 Microsoft Azure 虛擬網路的 VPN 輸送量
description: 本文件的目的是在協助使用者驗證從其內部部署資源到 Azure 虛擬機器的網路輸送量。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: dcf86deda32069bf9711dbeb733dc9361e22a771
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631778"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>如何驗證傳輸到虛擬網路的 VPN 輸送量

VPN 閘道連線可讓您在 Azure 內的虛擬網路和內部部署 IT 基礎結構之間建立安全的跨單位連線。

本文章說明如何驗證從內部部署資源到 Azure 虛擬機器 (VM) 的網路輸送量。

> [!NOTE]
> 本文章旨在協助診斷和修正常見的問題。 如果您在使用下列資訊後仍無法解決問題，[請連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="overview"></a>概觀

VPN 閘道連線涉及下列元件：

* 本地 VPN 裝置(查看[已驗證 VPN 設備](vpn-gateway-about-vpn-devices.md#devicetable)的清單。
* 公共互聯網
* Azure VPN 閘道
* Azure VM

下圖顯示內部部署網路透過 VPN 到 Azure 虛擬網路的邏輯連線能力。

![客戶網路使用 VPN 到 MSFT 網路的邏輯連線能力](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>計算最大預期輸入/輸出

1. 判斷您應用程式的基準輸送量需求。
1. 判斷您的 Azure VPN 閘道輸送量限制。 有關説明,請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md#gwsku)的「閘道」的「閘道」部分。
1. 判斷 VM 大小的 [Azure VM 輸送量指引](../virtual-machines/virtual-machines-windows-sizes.md)。
1. 決定您網際網路服務提供者 (ISP) 的頻寬。
1. 通過佔用 VM、VPN 閘道或 ISP 的最小頻寬來計算預期輸送量;以兆位/秒 (/) 除以八 (8) 為單位進行測量。

如果計算的輸送量不符合應用程序的基準輸送量要求,則必須增加您確定為瓶頸的資源的頻寬。 如果要調整 Azure VPN 閘道的大小，請參閱 [變更閘道 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。 如果調整虛擬機器的大小，請參閱 [調整 VM 的大小](../virtual-machines/virtual-machines-windows-resize-vm.md)。 如果您沒有遇到預期的 Internet 頻寬,您也可以聯繫您的 ISP。

> [!NOTE]
> VPN 閘道輸送量是所有網站到網站的聚合,即從 VNET 到 VNET 或點對點連接。

## <a name="validate-network-throughput-by-using-performance-tools"></a>使用效能工具驗證網路輸送量

由於測試時的 VPN 通道輸送量飽和情況無法呈現準確的結果，因此應在非尖峰時段執行此驗證。

iPerf 是我們用於此測試的工作，分別在 Windows 與 Linux 上工作，並具有用戶端與伺服器模式。 對於 Windows VM,它限制為 3Gbps。

此工具不會對磁碟執行任何讀取/寫入作業。 此工具只會產生從一端到另一端自我產生的 TCP 流量。 它基於測量客戶端和伺服器節點之間可用頻寬的實驗生成統計資訊。 在兩個節點之間測試時,一個節點充當伺服器,另一個節點充當用戶端。 完成此測試後,我們建議您反轉節點的角色,以測試兩個節點上的上載和下載輸送量。

### <a name="download-iperf"></a>下載 iPerf

下載[iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip)。 如需詳細資訊，請參閱 [iPerf 文件](https://iperf.fr/iperf-doc.php)。

 > [!NOTE]
 > 本文中討論的第三方產品由獨立於 Microsoft 的公司製造。 Microsoft 對於這些產品的效能或可靠性不提供任何默示或其他保證。

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

   **Azure Linux:** Azure Linux 映射具有允許的防火牆。 如果有應用程式接聽連接埠，則允許通過流量。 受保護的自訂映像可能需要明確開啟連接埠。 常見的 Linux OS 層防火牆包括 `iptables`、`ufw` 或 `firewalld`。

1. 在伺服器節點上，請變更至將 iperf3.exe 解壓縮的目錄。 然後在伺服器模式下運行 iPerf,並將其設置為偵聽埠 5001 作為以下命令:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > 埠 5001 可自定義,以考慮環境中的特定防火牆限制。

1. 在用戶端節點上，變更至將 iperf 工具解壓縮的目錄，然後執行下列命令：

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   用戶端正在將埠 5001 上的 30 秒流量定向到伺服器。 標誌"-P"表示我們同時與伺服器節點進行 32 次連接。

   下列畫面顯示此範例的輸出：

   ![輸出](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (選用) 如果要保留測試結果，請執行此命令：

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. 完成上述步驟后,執行相同的步驟,並反轉角色,以便伺服器節點現在將成為用戶端節點,反之亦然。

> [!Note]
> Iperf不是唯一的工具。 [NTTTCP 是測試的替代解決方案](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)。

## <a name="test-vms-running-windows"></a>測試執行 Windows 的 VM

### <a name="load-latteexe-onto-the-vms"></a>將拿鐵.exe 載入到 VM 上

下載最新版本的[拿鐵.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

請考慮將拿鐵.exe 放在單獨的資料夾中,例如`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>允許拿鐵.exe 通過 Windows 防火牆

在接收器上,在 Windows 防火牆上創建允許規則,以允許拿鐵.exe 流量到達。 最簡單的方法是允許整個拿鐵程式的名稱,而不是允許特定的TCP埠入站。

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>允許拿鐵.exe通過這樣的 Windows 防火牆

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

例如,如果將拿鐵.exe 複製到"c:\tools"資料夾,則該命令

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>執行延遲測試

在 RECEIVER 上啟動拿鐵.exe(從 CMD 運行,而不是從 PowerShell 執行):

`latte -a <Receiver IP address>:<port> -i <iterations>`

大約 65k 反覆運算足夠長,可以返回具有代表性的結果。

任何可用的埠號都正常。

如果 VM 的 IP 位址為 10.0.0.4,則如下所示

`latte -c -a 10.0.0.4:5005 -i 65100`

在寄送器上啟動拿鐵.exe(從 CMD 執行,而不是從 PowerShell 執行)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

產生的指令與接收者相同,除非新增「-c」以指示這是「用戶端」或發送者

`latte -c -a 10.0.0.4:5005 -i 65100`

等候結果。 根據 VM 之間的距離,可能需要幾分鐘才能完成。 考慮從較少的反覆運算開始測試成功,然後再運行更長的測試。

## <a name="test-vms-running-linux"></a>測試執行 Linux 的 VM

使用[SockPerf](https://github.com/mellanox/sockperf)測試 VM。

### <a name="install-sockperf-on-the-vms"></a>在 VM 安裝 SockPerf

在 Linux VM(SENDER 和 RECEIVER)上,執行這些指令以在 VM 上準備 SockPerf:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - 安裝 GIT 和其他有用的工具

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - 安裝 GIT 和其他用的工具

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>巴什 - 全部

從 bash 命令列 (假定已安裝 git)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

製作速度較慢,可能需要幾分鐘時間

`make`

使安裝快速

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>在 VM 上執行襪子Perf

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>安裝後的示例命令。 伺服器/接收器 ─假定伺服器的 IP 為 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>用戶端 - 假定伺服器的 IP 為 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> 確保在 VM 和閘道之間的輸送量測試期間沒有中間躍點(例如虛擬裝置)。
> 如果上述 iPERF/NTTTCP 測試的結果不佳(總體輸送量),請參閱以下文章以瞭解問題可能的根本原因背後的關鍵因素:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

特別是,在這些測試期間,對從用戶端和伺服器並行收集的數據包捕獲跟蹤(Wireshark/網路監視器)進行分析將有助於評估不良性能。 這些跟蹤可能包括資料包丟失、高延遲、MTU 大小。 碎片、TCP 0 視窗、訂單外片段等。

## <a name="address-slow-file-copy-issues"></a>處理檔案複製變慢的問題

即使使用前面的步驟(iPERF/NTTTCP/等)評估的總體輸送量良好,在使用 Windows 資源管理器或透過 RDP 工作階段進行拖放時,您可能會遇到緩慢的檔案應對速度。 此問題一般是因為下列一個或多個因素造成︰

* 如 Windows 檔案總管與 RDP 的檔案應用程式，並未在複製檔案時使用多執行緒。 為了提升效能，請使用如 [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) 等多執行緒的檔案複製應用程式，以 16 或 32 條執行緒複製檔案。 要更改「富複製」中檔副本的線程程式,請單擊**操作** > **複製選項** > **「檔案副本**」。

   ![檔案複製變慢的問題](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > 並非所有應用程式都工作相同,並非所有應用程式/進程都利用所有線程。 如果運行測試,則可以看到某些線程為空,並且無法提供準確的輸送量結果。
   > 要檢查應用程式檔案傳輸性能,請使用多線程,連續增加線程的 + 或減少,以找到應用程式或檔案傳輸的最佳輸送量。

* VM 磁碟讀取/寫入速度不足。 如需詳細資訊，請參閱 [Azure 儲存體疑難排解](../storage/common/storage-e2e-troubleshooting.md)。

## <a name="on-premises-device-external-facing-interface"></a>內部部署裝置的外部對應介面

提到您希望 Azure 透過本地網路閘道上的 VPN 存取本機範圍的子網路。 同時,將 Azure 中的 VNET 位址空間定義到本地設備。

* **基於路由的閘道**:基於路由的 VPN 的策略或流量選擇器配置為任意到任意(或通配符)。

* **基於策略的閘道**:基於策略的 VPN 根據本地網路和 Azure VNet 之間的位址前綴組合,透過 IPsec 隧道加密和直接資料包。 原則 (或流量選取器) 通常定義為 VPN 組態中的存取清單。

* **使用基於策略的流量選擇器**連接:("使用基於策略的流量選擇器"以$True連接時將配置 Azure VPN 閘道以在內部連接到基於策略的 VPN 防火牆。 如果啟用策略基於流量選擇器,則需要確保 VPN 裝置具有與本地網路(本地網路閘道)首碼的前置元件一起定義的匹配流量選擇器,而不是任意到任意。

配置不當可能導致隧道內頻繁斷開連接、數據包丟棄、輸送量錯誤和延遲。

## <a name="check-latency"></a>檢查延遲

您可以使用以下工具檢查延遲:

* 溫地鐵
* TCP 追蹤路由
* `ping`和`psping`(這些工具可以提供 RTT 的良好估計值,但不能在所有情況下使用。

![檢查延遲](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

如果您在進入 MS 網路主幹網之前發現任何躍點出現高延遲峰值,則可能需要與 Internet 服務提供者展開進一步調查。

如果在「msn.net」內的躍點中注意到一個較大的異常延遲峰值,請聯繫 MS 支援部門進行進一步調查。

## <a name="next-steps"></a>後續步驟

有關詳細資訊或説明,請查看以下連結:

* [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
