---
title: 設定和優化未啟用的 H 系列和 N 系列 Azure 虛擬機器
description: 瞭解如何為 HPC 設定和優化未啟用的 H 系列和 N 系列 Vm。
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
ms.openlocfilehash: c49286f370691c39c3d14d589f2657d6e0bb3c04
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542326"
---
# <a name="configure-and-optimize-vms"></a>設定和優化 Vm

本文會分享已知的技術，以設定及優化適用于 HPC 的未啟用的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm。

## <a name="vm-images"></a>VM 映像
在未啟用的虛擬機器上，必須要有適當的驅動程式，才能啟用 RDMA。 在 Linux 上，Marketplace 中的 CentOS-HPC VM 映射已預先設定適當的驅動程式。 您可以使用[這裡的指示](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，利用正確的驅動程式來設定 Ubuntu VM 映射。 此外，也建議您使用適當的驅動程式和設定來建立[自訂 VM 映射](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)，並重複使用這些 recurringly。

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM 映射
對於啟用[RDMA 功能](../../sizes-hpc.md#rdma-capable-instances)的非 Sr-iov 型 vm，CENTOS-HPC 6.5 版或更新版本，最多可達7.5 （在 Marketplace 中）。 例如，針對[H16 系列 vm](../../h-series.md)，建議使用7.1 到7.5 版。 這些 VM 映射已預先載入 RDMA 和 Intel MPI 5.1 版的網路直接驅動程式。

  若為支援 SR-IOV 的[RDMA vm](../../sizes-hpc.md#rdma-capable-instances)，則適用于 Marketplace 中的[CentOS-HPC 7.6 版或更新版本的](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)vm 映射。 這些 VM 映射已針對 RDMA 的 OFED 驅動程式和各種常用的 MPI 程式庫和科學計算套件進行優化和預先載入，而且是開始使用的最簡單方式。

  從基底 CentOS Marketplace 映射建立 CentOS-HPC 7.6 版和更新版本的 VM 映射時使用的腳本範例位於[azhpc 映射](https://github.com/Azure/azhpc-images/tree/master/centos)存放庫。

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM 映射
您可以設定 Marketplace 上的 RHEL 或 CentOS 為基礎的非 HPC VM 映射，以在支援 SR-IOV 的[RDMA 虛擬機器](../../sizes-hpc.md#rdma-capable-instances)上使用。 深入瞭解如何在 Vm 上[啟用](enable-infiniband.md)「未處理」和「[設定 MPI](setup-mpi.md) 」。

  從基底 CentOS Marketplace 映射建立 CentOS-HPC 7.6 版和更新版本的 VM 映射時使用的腳本範例位於[azhpc 映射](https://github.com/Azure/azhpc-images/tree/master/centos)存放庫。

### <a name="ubuntu-vm-images"></a>Ubuntu VM 映射
適用于 SR-IOV 和非 SR-IOV [RDMA 的 vm](../../sizes-hpc.md#rdma-capable-instances)支援 Ubuntu SERVER 16.04 LTS、18.04 LTS 和 20.04 LTS vm 映射。 深入瞭解如何在 Vm 上[啟用](enable-infiniband.md)「未處理」和「[設定 MPI](setup-mpi.md) 」。

  [Azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc)存放庫中可用於建立以 UBUNTU 18.04 LTS 為基礎的 HPC VM 映射的腳本範例。

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM 映射
支援 SLES 12 SP3 for hpc、SLES 12 SP3 for HPC （Premium）、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC （Premium）、SLES 12 SP4 和 SLES 15 VM images （在 Marketplace 中）。 這些 VM 映射已預先載入 RDMA 和 Intel MPI 5.1 版的網路直接驅動程式。 深入瞭解如何在 Vm 上[設定 MPI](setup-mpi.md) 。

## <a name="optimize-vms"></a>優化 Vm

以下是一些選擇性的優化設定，可提升 VM 的效能。

### <a name="update-lis"></a>更新 .LIS

如需功能或效能的必要，可以在支援的 OS 散發版本上安裝或更新[Linux Integration Services （.lis）驅動程式](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)，特別是使用自訂映射或較舊的 OS 版本（例如 CENTOS/RHEL 6.x 或較早版本的7.x）進行部署。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>回收記憶體

藉由自動回收記憶體來避免遠端記憶體存取，以改善效能。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

若要在 VM 重新開機之後保存此內容：

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
（選擇性）您可以將 WALinuxAgent 停用為前置作業步驟，並啟用 HPC 工作負載的最大 VM 資源可用性的後置作業。


## <a name="next-steps"></a>後續步驟

- 深入瞭解如何在已啟用無功能的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 上[啟用](enable-infiniband.md)「無作用」。
- 深入瞭解如何在 Vm 上安裝各種[支援的 MPI 程式庫](setup-mpi.md)及其最佳設定。
- 請參閱[HB 系列總覽](hb-series-overview.md)和[HC 系列總覽](hc-series-overview.md)，以瞭解如何針對效能和擴充性，以最佳方式設定工作負載。
- 閱讀[Azure 計算技術小組的 blog](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)，瞭解最新的公告和一些 HPC 範例和結果。
- 如需執行 HPC 工作負載的較高層級架構視圖，請參閱[Azure 上的高效能運算（HPC）](/azure/architecture/topics/high-performance-computing/)。
