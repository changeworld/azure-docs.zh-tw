---
title: 在 HPC Vm 上啟用 InifinBand-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何在 Azure HPC Vm 上啟用不限。
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b5f4cbd2fc09b2c42c232eafd63edb1217e4dacb
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996320"
---
# <a name="enable-infiniband"></a>啟用 InfiniBand

[支援 RDMA](../../sizes-hpc.md#rdma-capable-instances) 的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 會透過低延遲和高頻寬的網路進行通訊。 這類互連的 RDMA 功能對於提升分散式節點 HPC 和 AI 工作負載的可擴縮性和效能非常重要。 啟用 InfiniBand 的 H 系列和 N 系列的 VM 是非封鎖式的寬大樹狀結構，並具備低直徑設計，以獲得最佳且一致的 RDMA 效能。

有多種方式可讓您在可用的 VM 大小上進行無調整。

## <a name="vm-images-with-infiniband-drivers"></a>具有不使用設備磁碟機的 VM 映射
請參閱 [VM 映射](configure.md#vm-images) 以取得 Marketplace 上支援的 vm 映射清單，這些映射已預先載入 sr-iov 或非 sr-iov vm (的自動駕駛映射，) 或可使用適當的驅動程式進行設定。
針對 SR-IOV 啟用 RDMA 的 [vm](../../sizes-hpc.md#rdma-capable-instances)，Marketplace 中的 [CentOS-HPC 7.6 版或更新](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 版本 VM 映射是開始使用的最簡單方式。
您可以使用 [此處的指示](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，使用適用于 sr-iov 和非 Sr-iov 的 vm 的正確驅動程式來設定 Ubuntu VM 映射。

## <a name="infiniband-driver-vm-extensions"></a>未駕駛的驅動程式 VM 延伸模組
在 Linux 上， [INFINIBANDDRIVERLINUX VM 延伸](../../extensions/hpc-compute-infiniband-linux.md) 模組可以用來安裝 Mellanox OFED 驅動程式，並在啟用 Sr-iov 的 H 和 N 系列 vm 上啟用自動感知。

在 Windows 上， [INFINIBANDDRIVERWINDOWS VM 延伸](../../extensions/hpc-compute-infiniband-windows.md) 模組會在非 sr-iov vm 上安裝 Windows 網路直接驅動程式 () 或在 sr-iov vm 上 (的 Mellanox OFED 驅動程式) 進行 RDMA 連線。 在 A8 和 A9 實例的某些部署中，會自動加入 HpcVmDrivers 延伸模組。 請注意，HpcVmDrivers VM 延伸模組已被取代;它將不會更新。

若要將 VM 擴充功能新增至 VM，您可以使用 [Azure PowerShell](/powershell/azure/) Cmdlet。 如需詳細資訊，請參閱[虛擬機器擴充功能和功能](../../extensions/overview.md)。 您也可以針對已在[傳統部署模型](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 使用擴充功能。

## <a name="manual-installation"></a>手動安裝
您可以手動將[Mellanox OpenFabrics 驅動程式 (OFED) ](https://www.mellanox.com/products/InfiniBand-VPI-Software)安裝在[啟用 sr-iov](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 上。

### <a name="linux"></a>Linux
您可以使用下列範例來安裝 [適用于 Linux 的 OFED 驅動程式](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) 。 雖然這裡的範例適用于 RHEL/CentOS，但這些步驟都是一般步驟，可用於任何相容的 Linux 作業系統，例如 Ubuntu (16.04、18.04 19.04、20.04) 和 SLES (12 SP4 和 15) 。 其他散發版本的其他範例位於 [azhpc 映射](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh)存放庫。 收件匣驅動程式也可以運作，但 Mellanox OFED 驅動程式提供更多功能。

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
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
若是 Windows，請下載並安裝 [適用于 windows 驅動程式的 MELLANOX OFED](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)。

## <a name="enable-ip-over-infiniband-ib"></a> (IB) 啟用 IP over 的 IP
如果您打算執行 MPI 作業，通常不需要 IPoIB。 MPI 程式庫將使用動詞介面進行 IB 通訊 (除非您明確地使用 MPI 程式庫) 的 TCP/IP 通道。 但是，如果您的應用程式使用 TCP/IP 進行通訊，而您想要透過 IB 執行，您可以透過 IB 介面使用 IPoIB。 針對 RHEL/CentOS) 使用下列命令 (，以透過未經過的 IP 來啟用 IP。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>接下來的步驟

- 深入瞭解如何在 Vm 上安裝各種 [支援的 MPI 程式庫](setup-mpi.md) 及其最佳設定。
- 請檢閱 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)，了解如何以最佳方式設定工作負載以獲得效能和可擴縮性。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
