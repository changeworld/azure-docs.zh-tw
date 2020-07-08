---
title: 在 Azure 虛擬網路中測試 Azure 虛擬機器的網路延遲 |Microsoft Docs
description: 瞭解如何測試虛擬網路上 Azure 虛擬機器之間的網路延遲
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 77ea14097538f722569acb5a0371674776aac8e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84687798"
---
# <a name="test-vm-network-latency"></a>測試 VM 網路延遲

若要達到最精確的結果，請使用專為工作設計的工具來測量您的 Azure 虛擬機器（VM）網路延遲。 公開可用的工具（例如 SockPerf （適用于 Linux）和 latte.exe （適用于 Windows）可以隔離並測量網路延遲，同時排除其他類型的延遲，例如應用程式延遲。 這些工具著重于影響應用程式效能的網路流量類型（亦即，傳輸控制通訊協定 [TCP] 和使用者資料包協定 [UDP] 流量）。 

其他常見的連線工具（例如 Ping）可能會測量延遲，但其結果可能不代表實際工作負載中所使用的網路流量。 這是因為大部分的這些工具都採用網際網路控制訊息通訊協定（ICMP），其處理方式不同于應用程式流量，而其結果可能不適用於使用 TCP 和 UDP 的工作負載。 

針對大部分應用程式所使用的通訊協定，若要進行精確的網路延遲測試，SockPerf （適用于 Linux）和 latte.exe （適用于 Windows）會產生最相關的結果。 本文涵蓋這兩種工具。

## <a name="overview"></a>總覽

藉由使用兩個 Vm （一個作為傳送者，另一個做為接收者），您可以建立雙向通道。 使用這種方法，您可以雙向傳送和接收封包，並測量來回時間（RTT）。

您可以使用這種方法來測量兩部 Vm 之間的網路延遲，甚至是兩部實體電腦之間的延遲。 延遲測量有助於下列案例：

- 建立已部署 Vm 之間網路延遲的基準測試。
- 比較在進行相關變更之後，網路延遲變更的影響：
  - 作業系統（OS）或網路堆疊軟體，包括設定變更。
  - VM 部署方法，例如部署至可用性區域或鄰近放置群組（PPG）。
  - VM 屬性，例如加速網路或大小變更。
  - 虛擬網路，例如路由或篩選變更。

### <a name="tools-for-testing"></a>測試控管
若要測量延遲，您有兩個不同的工具選項：

* Windows 型系統： [latte.exe （windows）](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* 針對以 Linux 為基礎的系統： [SockPerf （Linux）](https://github.com/mellanox/sockperf)

藉由使用這些工具，您可以協助確保只有 TCP 或 UDP 承載傳遞時間會經過測量，而不是應用程式使用的 ICMP （Ping）或其他封包類型，而且不會影響其效能。

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>建立最佳 VM 設定的秘訣

當您建立 VM 設定時，請記住下列建議：
- 使用最新版的 Windows 或 Linux。
- 啟用加速網路以獲得最佳結果。
- 使用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)來部署 vm。
- 較大的 Vm 通常會比較小的 Vm 執行效能更佳。

### <a name="tips-for-analysis"></a>分析的秘訣

當您要分析測試結果時，請記住下列建議：

- 在部署、設定和優化完成後，儘早建立基準。
- 請一律比較新的結果與基準，或從一個測試到另一個具有受控制變更的測試。
- 每當觀察到或規劃變更時，重複測試。


## <a name="test-vms-that-are-running-windows"></a>測試正在執行 Windows 的 Vm

### <a name="get-latteexe-onto-the-vms"></a>取得 Vm 上的 latte.exe

下載[最新版本的 latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)。

請考慮將 latte.exe 放在不同的資料夾中，例如*c:\tools*。

### <a name="allow-latteexe-through-windows-defender-firewall"></a>允許透過 Windows Defender 防火牆 latte.exe

在*接收者*上，于 Windows Defender 防火牆上建立允許規則，以允許 latte.exe 流量抵達。 最簡單的方式是依名稱允許整個 latte.exe 程式，而不是允許特定的 TCP 埠輸入。

藉由執行下列命令，允許透過 Windows Defender 防火牆進行 latte.exe：

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

例如，如果您將 latte.exe 複製到*c:\tools*資料夾，這會是命令：

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>執行延遲測試

* 在*接收者*上，啟動 latte.exe （從 CMD 視窗執行，而不是從 PowerShell）：

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    大約65000反覆運算的時間夠長，可傳回代表的結果。

    任何可用的埠號碼都沒問題。

    如果 VM 的 IP 位址是10.0.0.4，此命令看起來會像這樣：

    `latte -a 10.0.0.4:5005 -i 65100`

* 在 [*寄件者*] 上，啟動 latte.exe （從 CMD 視窗執行，而不是從 PowerShell）：

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    產生的命令與接收者相同，但新增 &nbsp; *-c*表示這是*用戶端*或*寄件者*：

    `latte -c -a 10.0.0.4:5005 -i 65100`

等候結果。 根據 Vm 的距離，測試可能需要幾分鐘的時間才能完成。 在執行較長的測試之前，請考慮以較少的反覆運算開始測試是否成功。

## <a name="test-vms-that-are-running-linux"></a>測試正在執行 Linux 的 Vm

若要測試執行 Linux 的 Vm，請使用[SockPerf](https://github.com/mellanox/sockperf)。

### <a name="install-sockperf-on-the-vms"></a>在 Vm 上安裝 SockPerf

在 Linux Vm （「傳送者 *」和「* *接收者*」）上，執行下列命令以在 vm 上準備 SockPerf。 主要散發版本會提供命令。

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>針對 Red Hat Enterprise Linux （RHEL）/Centos 作為基礎

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

#### <a name="for-ubuntu"></a>適用于 Ubuntu

執行下列命令：

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>所有散發版本的

根據下列步驟複製、編譯及安裝 SockPerf：

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

### <a name="run-sockperf-on-the-vms"></a>在 Vm 上執行 SockPerf

SockPerf 安裝完成之後，Vm 就準備好執行延遲測試。 

首先，在*接收者*上啟動 SockPerf。

任何可用的埠號碼都沒問題。 在此範例中，我們使用埠12345：

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

既然伺服器正在接聽，用戶端就可以開始將封包傳送到它所接聽之埠上的伺服器（在此案例中為12345）。

大約100秒夠長，可傳回代表結果，如下列範例所示：

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

等候結果。 視 Vm 的距離而定，反復專案的數目會有所不同。 若要在執行較長的測試之前測試是否成功，請考慮從較短的測試開始，大約5秒。

這個 SockPerf 範例會使用350位元組的訊息大小，這通常是針對平均封包。 您可以調整較高或較低的大小，以達到更精確表示在 Vm 上執行之工作負載的結果。


## <a name="next-steps"></a>後續步驟
* 使用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)來改善延遲。
* 瞭解如何針對您的案例將[vm 的網路功能優化](../virtual-network/virtual-network-optimize-network-bandwidth.md)。
* 瞭解[如何將頻寬配置給虛擬機器](../virtual-network/virtual-machine-network-throughput.md)。
* 如需詳細資訊，請參閱[Azure 虛擬網路常見問題](../virtual-network/virtual-networks-faq.md)。
