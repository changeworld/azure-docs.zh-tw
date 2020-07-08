---
title: Azure Linux VM 中的 DPDK | Microsoft Docs
description: 了解如何在 Linux 虛擬機器中設定 DPDK。
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: ec117172a367d014f634106b5d2405892b80ffa7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84689260"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>在 Linux 虛擬機器中設定 DPDK

Azure 上的資料平面開發套件 (DPDK) 針對效能密集應用程式，提供更快速的使用者空間封包處理架構。 此架構會略過虛擬機器的核心網路堆疊。

在使用核心網路堆疊的傳統封包處理中，程序是插斷導向的。 當網路介面接收連入封包時，會有一個核心插斷可處理從核心空間到使用者空間的封包與環境切換。 DPDK 可免除環境切換與插斷導向方法，改為使用利用輪詢模式驅動程式的使用者空間實作，而達到快速的封包處理。

DPDK 由一組使用者空間程式庫組成，可提供對低層級資源的存取。 這些資源包括硬體、邏輯核心、記憶體管理與網路介面卡的輪詢模式驅動程式。

DPDK 可以在 Azure 虛擬機器中執行，並支援多個作業系統散發套件。 DPDK 在驅使網路功能虛擬化實作方面有關鍵效能差異。 這些實作可為網路虛擬設備 (NVA) 形式，例如虛擬路由器、防火牆、VPN、負載平衡器、進化型封包核心與阻斷服務 (DDoS) 應用程式。

## <a name="benefit"></a>優點

**較高的每秒封包數 (PPS)** ：略過核心並掌控使用者空間中的封包，因為不需要切換環境，可減少週期計數。 它也可以改善 Azure Linux 虛擬機器中每秒處理的封包速率。


## <a name="supported-operating-systems"></a>支援的作業系統

支援以下來自 Azure Marketplace 的散發套件：

| Linux 作業系統     | 核心版本               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.27-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**自訂核心支援**

針對未列出的任何 Linux 核心版本，請參閱[用於建置經 Azure 調整之 Linux 核心的補充程式](https://github.com/microsoft/azure-linux-kernel)。 如需詳細資訊，您也可以連絡 [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)。 

## <a name="region-support"></a>區域支援

所有 Azure 區域都支援 DPDK。

## <a name="prerequisites"></a>Prerequisites

必須在 Linux 虛擬機器上啟用加速網路。 虛擬機器應該有至少兩個網路介面，其中一個介面用於管理。 了解如何[建立已啟用加速網路的 Linux 虛擬機器](create-vm-accelerated-networking-cli.md)。

## <a name="install-dpdk-dependencies"></a>安裝 DPDK 相依項目

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure 核心**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**預設核心**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>設定虛擬機器環境 (一次)

1. [下載最新的 DPDK](https://core.dpdk.org/download)。 Azure 需要 18.11 LTS 或 19.11 LTS 版本。
2. 使用 `make config T=x86_64-native-linuxapp-gcc` 建置預設組態。
3. 使用 `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` 在產生的組態中啟用 Mellanox PMD。
4. 使用 `make` 編譯。
5. 使用 `make install DESTDIR=<output folder>` 安裝。

## <a name="configure-the-runtime-environment"></a>設定執行階段環境

重新啟動之後，執行下列命令一次：

1. Hugepage

   * 針對每個 numa 節點執行一次下列命令，以設定 Hugepage：

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * 使用 `mkdir /mnt/huge` 建立可供掛接的目錄。
   * 使用 `mount -t hugetlbfs nodev /mnt/huge` 掛接 Hugepage。
   * 使用 `grep Huge /proc/meminfo` 檢查 Hugepage 是否已保留。

     > [注意] 您可以依照 DPDK 的[指示](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment)來修改 grub 檔案，以便在開機時保留 Hugepage。 指示位於頁面底部。 當您使用 Azure Linux 虛擬機器時，請改為修改 **/etc/config/grub.d** 下的檔案，以在重新開機期間保留 Hugepage。

2. MAC & IP 位址：使用 `ifconfig –a` 來檢視網路介面的 MAC 和 IP 位址。 *VF* 網路介面和 *NETVSC* 網路介面都有相同的 MAC 位址，但僅限於具有 IP 位址的 *NETVSC* 網路介面。 *VF* 介面會當作 *NETVSC* 介面的從屬介面執行。

3. PCI 位址

   * 使用 來了解哪個 PCI 位址要用於 `ethtool -i <vf interface name>`*VF*。
   * 如果 *eth0* 已啟用網路加速，請確定 testpmd 不會意外接管 *eth0* 的 *VF* pci 裝置。 若 DPDK 應用程式意外取代管理網路介面並導致您的 SSH 連線中斷，請使用序列主控台來停止 DPDK 應用程式。 您也可以使用序列主控台來停止或啟動虛擬機器。

4. 在每次開機時使用 `modprobe -a ib_uverbs` 載入 *ibuverbs*。 僅限於 SLES 15，也可使用 `modprobe -a mlx4_ib` 載入 *mlx4_ib*。

## <a name="failsafe-pmd"></a>保全 PMD

DPDK 應用程式必須透過在 Azure 中公開的保全 PMD 執行。 如果應用程式直接透過 *VF* PMD 執行，則不會收到**所有**傳送到 VM 的封包，因為某些封包會透過綜合介面顯示。 

若透過具備故障保險功能的 PMD 執行 DPDK 應用程式，它可以保證應用程式會接受傳送給它的所有封包。 它也會確定應用程式持續在 DPDK 模式中執行，即使 VF 已在系統為主機提供服務時被叫用也一樣。 如需有關故障保險 PMD 的詳細資訊，請參閱[故障保險輪詢模式驅動程式庫](https://doc.dpdk.org/guides/nics/fail_safe.html)。

## <a name="run-testpmd"></a>執行 testpmd

若要在 root 模式中執行 testpmd，請在 *testpmd* 命令之前使用 `sudo`。

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>基本：例行性檢查、故障保險介面卡初始化

1. 執行下列命令來啟動單一連接埠 testpmd 應用程式：

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. 執行下列命令來啟動雙重連接埠 testpmd 應用程式：

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   若使用兩個 NIC 執行 testpmd，`--vdev` 引數會遵循此模式：`net_vdev_netvsc<id>,iface=<vf’s pairing eth>`。

3.  啟動後，執行 `show port info all` 來檢查連接埠資訊。 您應該會看到一或兩個 net_failsafe (不是 *net_mlx4*) 的 DPDK 連接埠。
4.  使用 `start <port> /stop <port>` 來啟動流量。

前述命令會在互動模式中啟動 *testpmd* (建議做法)，以試用 testpmd 命令。

### <a name="basic-single-sendersingle-receiver"></a>基本：單一傳送者/單一接收者

下列命令會定期列印每秒封包數的統計資料：

1. 在 TX 端上，執行下列命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. 在 RX 端上，執行下列命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

當您在虛擬機器上執行前述命令時，變更 `app/test-pmd/txonly.c` 中的 *IP_SRC_ADDR* 與 *IP_DST_ADDR*，以在編譯前符合虛擬機器的實際 IP 位址。 否則，封包會在到達接收者之前捨棄。

### <a name="advanced-single-sendersingle-forwarder"></a>進階：單一傳送者/單一轉送者
下列命令會定期列印每秒封包數的統計資料：

1. 在 TX 端上，執行下列命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. 在 FWD 端上，執行下列命令：

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

當您在虛擬機器上執行前述命令時，變更 `app/test-pmd/txonly.c` 中的 *IP_SRC_ADDR* 與 *IP_DST_ADDR*，以在編譯前符合虛擬機器的實際 IP 位址。 否則，封包會在到達轉寄者之前捨棄。 您將無法讓第三部電腦接收轉送的流量，因為 *testpmd* 轉送者不會修改第 3 層位址 (除非您變更一些程式碼)。

## <a name="references"></a>參考

* [EAL 選項](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd 命令](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
