---
title: 啟用 HPC Vm 上的 InifinBand-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何在 Azure HPC Vm 上啟用 [未使用]。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 88f1c120ac4578e077e1c51f59bcaf53b1de2083
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538892"
---
# <a name="enable-infiniband"></a>啟用 InfiniBand

[支援 RDMA](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 會透過低延遲和高頻寬的網路來進行通訊。 這類互連的 RDMA 功能對於提升分散式節點 HPC 和 AI 工作負載的擴充性和效能非常重要。 未啟用的 H 系列和 N 系列 Vm 會在非封鎖的 fat 樹狀結構中連接，並具有適用于優化和一致 RDMA 效能的低直徑設計。

有各種不同的方式可讓您在支援的 VM 大小上進行未使用。

## <a name="vm-images-with-infiniband-drivers"></a>具有不會駕駛之驅動程式的 VM 映射
請參閱[VM 映射](configure.md#vm-images)以取得 Marketplace 上支援的 vm 映射清單，其預先載入了不限時的驅動程式（適用于 sr-iov 或非 sr-iov vm），或可以使用適當的驅動程式進行設定。
對於支援 SR-IOV 的[RDMA vm](../../sizes-hpc.md#rdma-capable-instances)，在 Marketplace 中[CentOS-HPC 7.6 版或更新](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)版本的 vm 映射是開始使用的最簡單方式。
您可以使用[這裡的指示](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，使用適用于 sr-iov 和非 sr-iov 功能的 vm 的正確驅動程式來設定 Ubuntu VM 映射。

## <a name="infiniband-driver-vm-extensions"></a>不駕駛的驅動程式 VM 延伸模組
在 Linux 上，您可以使用[INFINIBANDDRIVERLINUX VM 擴充](../../extensions/hpc-compute-infiniband-linux.md)功能來安裝 Mellanox OFED 驅動程式，並在啟用 Sr-iov 的 H 和 N 系列 vm 上啟用「自動」。

在 Windows 上， [INFINIBANDDRIVERWINDOWS VM 擴充](../../extensions/hpc-compute-infiniband-windows.md)功能會安裝 Windows 網路直接驅動程式（在非 sr-iov vm 上）或 Mellanox OFED 驅動程式（在 sr-iov vm 上）以進行 RDMA 連線。 在 A8 和 A9 實例的某些部署中，會自動新增 HpcVmDrivers 擴充功能。 請注意，HpcVmDrivers VM 擴充功能即將淘汰;它將不會更新。

若要將 VM 擴充功能新增至 VM，您可以使用 [Azure PowerShell](/powershell/azure/) Cmdlet。 如需詳細資訊，請參閱[虛擬機器擴充功能和功能](../../extensions/overview.md)。 您也可以針對已在[傳統部署模型](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 使用擴充功能。

## <a name="manual-installation"></a>手動安裝
[Mellanox OpenFabrics 驅動程式（OFED）](https://www.mellanox.com/products/InfiniBand-VPI-Software)可以手動安裝在[支援 sr-iov](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 上。

### <a name="linux"></a>Linux
您可以使用下列範例來安裝[適用于 Linux 的 OFED 驅動程式](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)。 雖然這裡的範例適用于 RHEL/CentOS，但這些步驟是一般的，而且可以用於任何相容的 Linux 作業系統，例如 Ubuntu （16.04、18.04 19.04、20.04）和 SLES （12 SP4 和15）。 收件匣驅動程式也能正常運作，但 Mellanox OFED 驅動程式提供更多功能。

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optinally verify checksum
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
針對 Windows，請下載並安裝[適用于 windows 驅動程式的 MELLANOX OFED](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)。

## <a name="enable-ip-over-infiniband-ib"></a>啟用 IP over 不會（IB）
如果您打算執行 MPI 作業，通常不需要 IPoIB。 MPI 程式庫會使用適用于 IB 通訊的動詞介面（除非您明確地使用 MPI 程式庫的 TCP/IP 通道）。 但是，如果您的應用程式使用 TCP/IP 進行通訊，而您想要透過 IB 執行，則可以透過 IB 介面使用 IPoIB。 使用下列命令（適用于 RHEL/CentOS）來啟用透過不通過的 IP。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何在 Vm 上安裝各種[支援的 MPI 程式庫](setup-mpi.md)及其最佳設定。
- 請參閱[HB 系列總覽](hb-series-overview.md)和[HC 系列總覽](hc-series-overview.md)，以瞭解如何針對效能和擴充性，以最佳方式設定工作負載。
- 閱讀[Azure 計算技術小組的 blog](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)，瞭解最新的公告和一些 HPC 範例和結果。
- 如需執行 HPC 工作負載的較高層級架構視圖，請參閱[Azure 上的高效能運算（HPC）](/azure/architecture/topics/high-performance-computing/)。
