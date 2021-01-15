---
title: 測試 Azure 虛擬網路中的 Azure 虛擬機器網路延遲 |Microsoft Docs
description: 瞭解如何在虛擬網路上的 Azure 虛擬機器之間測試網路延遲
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
ms.openlocfilehash: f8f167a7947c42ce837ec83b336ae636f593f2e4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219253"
---
# <a name="test-vm-network-latency"></a>測試 VM 網路延遲

若要達到最精確的結果，請使用針對工作所設計的工具來測量 Azure 虛擬機器 (VM) 網路延遲。 適用于 Linux) 的 SockPerf (以及適用于 Windows) 的 latte.exe (的公開可用工具，可以隔離和測量網路延遲，同時排除其他類型的延遲，例如應用程式延遲。 這些工具著重于影響應用程式效能的網路流量類型 (也就是傳輸控制通訊協定 [TCP] 和使用者資料包協定 [UDP] 流量) 。 

其他常見的連接工具（例如 Ping）可能會測量延遲，但其結果可能不代表實際工作負載中使用的網路流量。 這是因為大部分的工具都採用網際網路控制訊息通訊協定 (ICMP) ，其處理方式與應用程式流量不同，而其結果可能不適用於使用 TCP 和 UDP 的工作負載。 

針對大部分應用程式所使用的通訊協定進行精確的網路延遲測試，適用于 Linux 的 SockPerf () 和 Windows) 的 latte.exe (會產生最相關的結果。 本文涵蓋這兩個工具。

## <a name="overview"></a>概觀

藉由使用兩個 Vm （一個做為寄件者，另一個作為接收者），您可以建立雙向通訊通道。 使用這個方法時，您可以雙向傳送和接收封包，並測量 (RTT) 的往返時間。

您可以使用此方法來測量兩部 Vm 之間的網路延遲，甚至兩部實體電腦之間的網路延遲。 延遲測量可能適用于下列案例：

- 針對部署的 Vm 之間的網路延遲建立基準測試。
- 比較變更之後網路延遲的影響：
  - 作業系統 (作業系統) 或網路堆疊軟體，包括設定變更。
  - VM 部署方法，例如部署至可用性區域或鄰近放置群組， (PPG) 。
  - VM 屬性，例如加速網路或大小變更。
  - 虛擬網路，例如路由或篩選變更。

### <a name="tools-for-testing"></a>測試控管
若要測量延遲，您有兩個不同的工具選項：

* 若是 Windows 系統： [latte.exe (windows) ](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Linux 型系統： [SockPerf (linux) ](https://github.com/mellanox/sockperf)

藉由使用這些工具，您可以協助確保只測量 TCP 或 UDP 承載傳遞時間，而非 ICMP (Ping) 或應用程式未使用的其他封包類型，而且不會影響其效能。

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>建立最佳 VM 設定的秘訣

當您建立 VM 設定時，請記住下列建議：
- 使用最新版的 Windows 或 Linux。
- 啟用加速網路以獲得最佳結果。
- 使用 [Azure 鄰近放置群組](../virtual-machines/co-location.md)部署 vm。
- 較大的 Vm 通常執行的效能優於較小的 Vm。

### <a name="tips-for-analysis"></a>分析秘訣

當您要分析測試結果時，請記住下列建議：

- 在部署、設定和優化完成後，儘早建立基準。
- 一律將新的結果與基準進行比較，或從某個測試到另一個具有控制變更的測試。
- 每當觀察或規劃變更時，重複測試。


## <a name="test-vms-that-are-running-windows"></a>測試執行 Windows 的 Vm

### <a name="get-latteexe-onto-the-vms"></a>在 Vm 上取得 latte.exe

下載 [latte.exe的最新版本 ](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)。

請考慮將 latte.exe 放在不同的資料夾中，例如 *c:\tools*。

### <a name="allow-latteexe-through-windows-defender-firewall"></a>允許 latte.exe 至 Windows Defender 防火牆

在 *接收者* 上，在 Windows Defender 防火牆上建立允許規則，讓 latte.exe 的流量抵達。 最簡單的方式是依名稱允許整個 latte.exe 程式，而不是允許特定的 TCP 埠輸入。

藉由執行下列命令，允許 latte.exe 至 Windows Defender 防火牆：

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

例如，如果您將 latte.exe 複製到 *c:\tools* 資料夾，則這會是命令：

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>執行延遲測試

* 在 *接收者* 上，啟動 latte.exe (從 CMD 視窗執行它，而不是從 PowerShell) ：

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    大約65000反覆運算的長度足以傳回代表性結果。

    任何可用的埠號碼都沒問題。

    如果 VM 的 IP 位址是10.0.0.4，命令看起來會像這樣：

    `latte -a 10.0.0.4:5005 -i 65100`

* *在傳送者上，啟動* latte.exe (從 CMD 視窗執行它，而不是從 PowerShell) ：

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    產生的命令與接收者上的相同，但加上 &nbsp; *-c* 表示這是 *用戶端* 或傳送者： 

    `latte -c -a 10.0.0.4:5005 -i 65100`

等候結果。 根據 Vm 之間的距離，測試可能需要幾分鐘的時間才能完成。 請考慮在執行較長的測試之前，先使用較少的反覆運算來測試是否成功。

## <a name="test-vms-that-are-running-linux"></a>測試執行 Linux 的 Vm

若要測試執行 Linux 的 Vm，請使用 [SockPerf](https://github.com/mellanox/sockperf)。

### <a name="install-sockperf-on-the-vms"></a>在 Vm 上安裝 SockPerf

在 Linux Vm （傳送者 *和**接收者*）上，執行下列命令以在 vm 上準備 SockPerf。 針對主要散發版本提供命令。

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>針對 Red Hat Enterprise Linux (RHEL) /Centos 作為基礎

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

#### <a name="for-all-distros"></a>適用于所有散發版本

根據下列步驟複製、編譯和安裝 SockPerf：

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

SockPerf 安裝完成之後，Vm 就可以開始執行延遲測試。 

首先，在 *接收者* 上啟動 SockPerf。

任何可用的埠號碼都沒問題。 在此範例中，我們使用埠12345：

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

現在伺服器正在接聽，用戶端可以開始將封包傳送到接聽的埠上的伺服器 (在此案例中為 12345) 。

大約100秒夠長，足以傳回具代表性的結果，如下列範例所示：

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

等候結果。 根據 Vm 之間的距離，反覆運算次數會有所不同。 若要在執行較長的測試之前測試是否成功，請考慮從較短的5秒測試開始。

此 SockPerf 範例會使用350位元組的訊息大小，這對平均封包而言是正常的。 您可以調整較高或較低的大小，以達到更精確地表示在 Vm 上執行之工作負載的結果。


## <a name="next-steps"></a>後續步驟
* 使用 [Azure 鄰近位置群組](../virtual-machines/co-location.md)來改善延遲。
* 瞭解如何針對您的案例將 [vm 的網路功能優化](../virtual-network/virtual-network-optimize-network-bandwidth.md) 。
* 瞭解 [如何將頻寬配置給虛擬機器](../virtual-network/virtual-machine-network-throughput.md)。
* 如需詳細資訊，請參閱 [Azure 虛擬網路常見問題](../virtual-network/virtual-networks-faq.md)。