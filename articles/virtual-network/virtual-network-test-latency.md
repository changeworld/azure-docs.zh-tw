---
title: 在 Azure 虛擬網路中測試 Azure 虛擬機器網路延遲 |微軟文檔
description: 瞭解如何在虛擬網路上的 Azure 虛擬機器之間測試網路延遲
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896365"
---
# <a name="test-vm-network-latency"></a>測試 VM 網路延遲

要獲得最準確的結果，請使用專為任務設計的工具測量 Azure 虛擬機器 （VM） 網路延遲。 公開可用的工具，如 SockPerf（適用于 Linux）和拿鐵.exe（適用于 Windows）可以隔離和測量網路延遲，同時排除其他類型的延遲，如應用程式延遲。 這些工具側重于影響應用程式性能的網路流量類型（即傳輸控制協定 [TCP] 和使用者資料包協定 [UDP] 流量）。 

其他常見的連接工具（如 Ping）可能會測量延遲，但它們的結果可能並不代表實際工作負載中使用的網路流量。 這是因為大多數這些工具採用 Internet 控制訊息協定 （ICMP），該協定可以區別對待應用程式流量，其結果可能不適用於使用 TCP 和 UDP 的工作負載。 

為了準確測試大多數應用程式使用的協定，SockPerf（用於 Linux）和拿鐵.exe（用於 Windows）會產生最相關的結果。 本文將介紹這兩種工具。

## <a name="overview"></a>總覽

通過使用兩個 VM（一個作為發送方，一個用作接收方），可以創建一個雙向通信通道。 使用這種方法，您可以雙向發送和接收資料包，並測量往返時間 （RTT）。

可以使用此方法測量兩個 VM 之間甚至兩台物理電腦之間的網路延遲。 延遲測量可用於以下方案：

- 建立部署 VM 之間的網路延遲基準。
- 比較進行相關更改後網路延遲更改的影響， 以：
  - 作業系統 （OS） 或網路堆疊軟體，包括配置更改。
  - VM 部署方法，例如部署到可用性區域或鄰近放置組 （PPG）。
  - VM 屬性，如加速網路或大小更改。
  - 虛擬網路，如路由或篩選更改。

### <a name="tools-for-testing"></a>用於測試的工具
要測量延遲，您有兩種不同的工具選項：

* 對於基於 Windows 的系統：[拿鐵.exe（Windows）](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* 對於基於 Linux 的系統：[襪子Perf（Linux）](https://github.com/mellanox/sockperf)

通過使用這些工具，有助於確保僅測量 TCP 或 UDP 有效負載交付時間，而不是測量不測量 ICMP （Ping） 或其他資料包類型，這些類型不是應用程式使用且不會影響其性能的。

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>創建最佳 VM 配置的提示

創建 VM 配置時，請記住以下建議：
- 使用最新版本的 Windows 或 Linux。
- 啟用加速網路以獲得最佳效果。
- 使用[Azure 接近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)部署 VM。
- 較大的 VM 通常比較小的 VM 性能更好。

### <a name="tips-for-analysis"></a>分析技巧

在分析測試結果時，請記住以下建議：

- 一旦部署、配置和優化完成，立即建立基線。
- 始終將新結果與基線進行比較，否則，通過受控更改將新結果從一個測試進行比較到另一個測試。
- 每當觀察到或計畫更改時重複測試。


## <a name="test-vms-that-are-running-windows"></a>測試回合 Windows 的 VM

### <a name="get-latteexe-onto-the-vms"></a>將拿鐵.exe 連接到 VM

下載[最新版本的拿鐵.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

請考慮將拿鐵.exe 放在單獨的資料夾中，例如*c：\工具*。

### <a name="allow-latteexe-through-windows-defender-firewall"></a>允許拿鐵.exe通過 Windows 防禦器防火牆

在*接收器*上，在 Windows 防禦器防火牆上創建允許規則，以允許拿鐵.exe 流量到達。 最簡單的方法是允許整個拿鐵.exe 程式的名稱，而不是允許特定的 TCP 埠入站。

通過運行以下命令，通過 Windows 防禦器防火牆允許拿鐵.exe：

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

例如，如果將拿鐵.exe 複製到*c：\tools*資料夾，則該命令是：

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>運行延遲測試

* 在*接收器*上，啟動拿鐵.exe（從 CMD 視窗運行，而不是從 PowerShell 運行）：

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    大約 65，000 次反覆運算足夠長，足以返回具有代表性的結果。

    任何可用的埠號都正常。

    如果 VM 的 IP 位址為 10.0.0.4，則命令如下所示：

    `latte -a 10.0.0.4:5005 -i 65100`

* 在*發送方*上，啟動拿鐵.exe（從 CMD 視窗運行，而不是從 PowerShell 運行）：

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    生成的命令與接收方相同，除非&nbsp;添加 *-c*以指示這是*用戶端*或*發送方*：

    `latte -c -a 10.0.0.4:5005 -i 65100`

等候結果。 根據 VM 之間的距離，測試可能需要幾分鐘才能完成。 考慮從較少的反覆運算開始測試成功，然後再運行更長的測試。

## <a name="test-vms-that-are-running-linux"></a>測試回合 Linux 的 VM

要測試回合 Linux 的 VM，請使用[SockPerf](https://github.com/mellanox/sockperf)。

### <a name="install-sockperf-on-the-vms"></a>在 VM 上安裝 SockPerf

在 Linux VM 上，*發送方*和*接收方*都運行以下命令，以便為 VM 上的 SockPerf 做好準備。 為主要發行版本提供了命令。

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>對於紅帽企業 Linux （RHEL）/CentOS

執行下列命令：

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>對於烏本圖

執行下列命令：

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>對於所有發行版本

根據以下步驟複製、編譯和安裝 SockPerf：

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>在 VM 上運行襪子Perf

SockPerf 安裝完成後，VM 已準備好運行延遲測試。 

首先，在*接收器*上啟動 SockPerf。

任何可用的埠號都正常。 在此示例中，我們使用埠 12345：

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

現在伺服器正在偵聽，用戶端就可以開始向正在偵聽的埠上的伺服器發送資料包（在本例中為 12345）。

大約 100 秒足夠返回具有代表性的結果，如下例所示：

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

等候結果。 根據 VM 之間的距離，反覆運算次數會有所不同。 要在運行較長測試之前測試成功，請考慮從大約 5 秒的較短測試開始。

此 SockPerf 示例使用 350 位元組的消息大小，這是平均資料包的典型大小。 您可以調整大小更高或更低，以實現更準確地表示 VM 上運行的工作負載的結果。


## <a name="next-steps"></a>後續步驟
* 使用 Azure[接近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)提高延遲。
* 瞭解如何為方案[優化 VM 網路](../virtual-network/virtual-network-optimize-network-bandwidth.md)。
* 閱讀有關如何[為虛擬機器分配頻寬](../virtual-network/virtual-machine-network-throughput.md)。
* 有關詳細資訊，請參閱[Azure 虛擬網路常見問題解答](../virtual-network/virtual-networks-faq.md)。
