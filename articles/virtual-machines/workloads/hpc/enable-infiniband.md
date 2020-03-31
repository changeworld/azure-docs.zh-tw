---
title: 使用 SR-IOV 啟用 InifinBand - Azure 虛擬機器 |微軟文檔
description: 瞭解如何使用 SR-IOV 啟用英菲尼班。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196773"
---
# <a name="enable-infiniband-with-sr-iov"></a>使用 SR-IOV 啟用英菲尼班

Azure NC、ND 和 H 系列 VM 都由專用的 InfiniBand 網路支援。 所有支援 RDMA 的尺寸都能夠使用英特爾 MPI 利用該網路。 某些 VM 系列通過 SR-IOV 擴展了對所有 MPI 實現和 RDMA 謂詞的支援。 支援 RDMA 的 VM 包括[GPU 優化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)和[高性能計算 （HPC）](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) VM。

## <a name="choose-your-installation-path"></a>選擇安裝路徑

要開始，最簡單的選項是使用為 InfiniBand 預配置的平臺映射（如果可用）：

- **HPC IaaS VM** – 要開始使用適用于 HPC 的 IaaS VM，最簡單的解決方案是使用[CentOS-HPC 7.6 VM OS 映射](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)，該映射已配置 InfiniBand。 由於此映射已配置 InfiniBand，因此無需手動設定它。 有關相容的 Windows 版本，請參閱[支援 Windows RDMA 的實例](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)。

- **GPU IaaS VM** – 除了[CentOS-HPC 7.6 VM OS 映射](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)外，目前沒有為 GPU 優化 VM 預配置任何平臺映射。 要使用 InfiniBand 配置自訂映射，請參閱[手動安裝 Mellanox OFED](#manually-install-mellanox-ofed)。

如果您使用的是自訂 VM 映射或[GPU 優化](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)的 VM，則應通過將 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 擴展添加到部署，使用 InfiniBand.Drive.Windows VM 擴展來配置它。 瞭解如何將這些 VM 擴展與[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)一起使用。

## <a name="manually-install-mellanox-ofed"></a>手動安裝梅拉諾克斯

要使用 SR-IOV 手動設定 InfiniBand，請使用以下步驟。 這些步驟中的示例顯示了 RHEL/CentOS 的語法，但這些步驟是常規的，可用於任何相容的作業系統，如 Ubuntu （16.04、 18.04 19.04） 和 SLES （12 SP4 和 15）。 收件匣驅動程式也工作，但 Mellanox OpenFabrics 驅動程式提供了更多功能。

有關梅拉諾克斯驅動程式支援的分佈的詳細資訊，請參閱最新的[Mellanox OpenFabrics 驅動程式](https://www.mellanox.com/page/products_dyn?product_family=26)。 有關 Mellanox OpenFabrics 驅動程式的詳細資訊，請參閱[梅拉諾克斯使用者指南](https://docs.mellanox.com/category/mlnxofedib)。

有關如何在 Linux 上配置 InfiniBand，請參閱以下示例：

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

對於 Windows，下載並安裝[適用于 Windows 驅動程式的 Mellanox OFED。](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ip-over-infiniband"></a>通過英菲尼班啟用 IP

使用以下命令通過 InfiniBand 啟用 IP。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>後續步驟

在 Azure 上瞭解有關[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多詳細資訊。
