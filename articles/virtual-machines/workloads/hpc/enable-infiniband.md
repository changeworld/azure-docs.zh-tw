---
title: 使用 SR-IOV 啟用 InifinBand-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何使用 SR-IOV 來啟用不限。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74196773"
---
# <a name="enable-infiniband-with-sr-iov"></a>使用 SR-IOV 啟用不限

Azure NC、ND 和 H 系列的 Vm 都是由專用的「未受使用網路」所支援。 所有啟用 RDMA 的大小都能夠使用 Intel MPI 來運用該網路。 有些 VM 系列透過 SR-IOV 擴充了所有 MPI 執行和 RDMA 動詞的支援。 支援 RDMA 的 Vm 包括[GPU 優化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)和[高效能計算（HPC）](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) vm。

## <a name="choose-your-installation-path"></a>選擇您的安裝路徑

若要開始使用，最簡單的選項是使用預先設定的平臺映射來進行不限，其中提供：

- **Hpc IaaS vm** -若要開始使用適用于 HPC 的 IaaS vm，最簡單的解決方案是使用[CentOS-HPC 7.6 VM OS 映射](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)，其已設定為 [未使用]。 因為此映射已設定為不使用，所以您不需要手動進行設定。 如需相容的 Windows 版本，請參閱[支援 WINDOWS RDMA 的實例](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)。

- **Gpu IaaS vm** -目前尚未針對 GPU 優化 vm 預先設定任何平臺映射，但[CentOS-HPC 7.6 VM OS 映射](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)除外。 若要設定具有「不確定」的自訂映射，請參閱[手動安裝 MELLANOX OFED](#manually-install-mellanox-ofed)。

如果您使用自訂 VM 映射或[GPU 優化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)vm，您應該將 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 擴充功能新增至您的部署，以讓它以不受歡迎的方式進行設定。 瞭解如何搭配[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)使用這些 VM 擴充功能。

## <a name="manually-install-mellanox-ofed"></a>手動安裝 Mellanox OFED

若要以 SR-IOV 手動設定「未使用」，請使用下列步驟。 這些步驟中的範例會顯示 RHEL/CentOS 的語法，但這些步驟是一般的，而且可以用於任何相容的作業系統，例如 Ubuntu （16.04、18.04 19.04）和 SLES （12 SP4 和15）。 收件匣驅動程式也能正常運作，但 Mellanox OpenFabrics 驅動程式提供更多功能。

如需有關支援的 Mellanox 驅動程式發行版本的詳細資訊，請參閱最新的[Mellanox OpenFabrics 驅動程式](https://www.mellanox.com/page/products_dyn?product_family=26)。 如需有關 Mellanox OpenFabrics 驅動程式的詳細資訊，請參閱《 [mellanox 使用者指南》](https://docs.mellanox.com/category/mlnxofedib)。

請參閱下列範例，以瞭解如何在 Linux 上設定「未執行」：

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

針對 Windows，請下載並安裝[適用于 windows 驅動程式的 MELLANOX OFED](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)。

## <a name="enable-ip-over-infiniband"></a>啟用透過未通過的 IP

使用下列命令來啟用透過未通過的 IP。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 上的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 。
