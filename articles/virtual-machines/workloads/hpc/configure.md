---
title: 設定和優化已啟用的「已啟用」 H 系列和 N 系列 Azure 虛擬機器
description: 瞭解如何設定和優化適用于 HPC 的已啟用已啟用 H 系列和 N 系列 Vm。
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 08/07/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 9ecfe1df273834ae38bd6bb94980444f5e34f786
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994820"
---
# <a name="configure-and-optimize-vms"></a>設定和最佳化 VM

本文將共用已知的技術，以針對 HPC 設定和優化已啟用支援的 [H 系列](../../sizes-hpc.md) 和 [N 系列](../../sizes-gpu.md) vm。

## <a name="vm-images"></a>VM 映像
在啟用了已啟用功能的 Vm 上，需要有適當的驅動程式才能啟用 RDMA。 在 Linux 上，Marketplace 中的 CentOS-HPC VM 映射已預先設定適當的驅動程式。 您可以使用 [此處的指示](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，使用正確的驅動程式來設定 Ubuntu VM 映射。 也建議您使用適當的驅動程式和設定來建立 [自訂的 VM 映射](../../linux/tutorial-custom-images.md) ，並重複使用這些 recurringly。

> [!NOTE]
> 在已啟用 GPU 的 [N 系列](../../sizes-gpu.md) vm 上，還需要適當的 gpu 驅動程式，可透過 [VM 延伸](../../extensions/hpccompute-gpu-linux.md) 模組或 [手動](../../linux/n-series-driver-setup.md)新增。 Marketplace 上的部分 VM 映射也隨 Nvidia GPU 驅動程式預先安裝。

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM 映射

#### <a name="non-sr-iov-enabled-vms"></a>已啟用非 SR-IOV 的 Vm
針對非 SR-IOV 啟用 [RDMA 的虛擬機器](../../sizes-hpc.md#rdma-capable-instances)，CENTOS-HPC 6.5 版或更新版本，最高可達最高7.5 的 Marketplace。 例如，針對 [H16 系列 vm](../../h-series.md)，建議使用7.1 到7.5 版。 這些 VM 映射會預先載入 RDMA 和 Intel MPI 5.1 版的 Network Direct 驅動程式。

> [!NOTE]
> 針對非 SR-IOV 啟用的 Vm，這些以 CentOS 為基礎的 HPC 映射， **yum** 設定檔中已停用核心更新。 這是因為 NetworkDirect Linux RDMA 驅動程式是以 RPM 套件的形式散發，而如果核心更新，驅動程式更新可能無法運作。

#### <a name="sr-iov-enabled-vms"></a>啟用 SR-IOV 的 Vm
  若為 SR-IOV 啟用 [RDMA 的 vm](../../sizes-hpc.md#rdma-capable-instances)，則適用于 Marketplace 中的 [CentOS-HPC 7.6 版或更新](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 版本 VM 映射。 這些 VM 映射經過優化，並預先載入適用于 RDMA 的 OFED 驅動程式，以及各種常用的 MPI 程式庫和科學計算套件，且是開始使用的最簡單方式。

  從基底 CentOS Marketplace 映射建立 CentOS-HPC 7.6 版和更新版本 VM 映射時所使用的腳本範例，位於 [azhpc 映射](https://github.com/Azure/azhpc-images/tree/master/centos)存放庫中。

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM 映射
您可以在 Marketplace 上設定 RHEL 或 CentOS 為基礎的非 HPC VM 映射，以便在啟用 SR-IOV 的支援 [虛擬機器](../../sizes-hpc.md#rdma-capable-instances)上使用。 深入瞭解如何在 Vm 上 [啟用](enable-infiniband.md) 「不會」和「 [設定 MPI](setup-mpi.md) 」。

  從基底 CentOS Marketplace 映射建立 CentOS-HPC 7.6 版和更新版本 VM 映射時所使用的腳本範例，位於 [azhpc 映射](https://github.com/Azure/azhpc-images/tree/master/centos)存放庫中。

### <a name="ubuntu-vm-images"></a>Ubuntu VM 映射
適用于 SR-IOV 和非 SR-IOV [RDMA 的 vm](../../sizes-hpc.md#rdma-capable-instances)都支援 Ubuntu SERVER 16.04 LTS、18.04 LTS 和 20.04 LTS vm 映射。 深入瞭解如何在 Vm 上 [啟用](enable-infiniband.md) 「不會」和「 [設定 MPI](setup-mpi.md) 」。

  在 [azhpc 映射](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc)存放庫中，可以用來建立 UBUNTU 18.04 LTS 型 HPC VM 映射的腳本範例。

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM 映射
SLES 12 SP3 for hpc、SLES 12 SP3 for hpc (Premium) 、SLES 12 SP1 for HPC、SLES 12 SP1 for hpc (Premium) 、SLES 12 SP4 和 Marketplace 中的 SLES 15 VM 映射都受到支援。 這些 VM 映射會預先載入 RDMA 和 Intel MPI 5.1 版的 Network Direct 驅動程式。 深入瞭解如何在 Vm 上 [設定 MPI](setup-mpi.md) 。

## <a name="optimize-vms"></a>將 Vm 優化

以下是一些可改善 VM 效能的選擇性優化設定。

### <a name="update-lis"></a>更新 .LIS

如果功能或效能有必要，可以在支援的 OS 散發版本上安裝或更新 [Linux Integration Services (.lis) 驅動程式](../../linux/endorsed-distros.md) ，特別是使用自訂映射或較舊的 OS 版本（例如 CENTOS/RHEL 6.x 或較早版本的7.x）進行部署。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>回收記憶體

自動回收記憶體以避免遠端記憶體存取，以改善效能。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

若要在 VM 重新開機之後讓它保持不變：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>停用防火牆和 SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>停用 cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>設定 WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
（選擇性） WALinuxAgent 可能會被停用為預先作業步驟，並針對 HPC 工作負載的最大 VM 資源可用性啟用後置作業。


## <a name="next-steps"></a>接下來的步驟

- 深入瞭解如何在已啟用支援的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 上[啟用](enable-infiniband.md)「全像」。
- 深入瞭解如何在 Vm 上安裝各種 [支援的 MPI 程式庫](setup-mpi.md) 及其最佳設定。
- 請檢閱 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)，了解如何以最佳方式設定工作負載以獲得效能和可擴縮性。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
