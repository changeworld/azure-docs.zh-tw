---
title: H 系列 - Azure 虛擬機器
description: H 系列 VM 的規格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470498"
---
# <a name="h-series"></a>H 系列

H 系列 VM 針對由高 CPU 頻率或每個核心要求的大記憶體驅動的應用進行了優化。 H 系列 VM 具有 8 或 16 英特爾至強 E5 2667 v3 處理器內核，每個 CPU 內核高達 14 GB 的 RAM，並且沒有超執行緒。 H 系列採用 56 Gb/秒 Mellanox FDR InfiniBand，採用非阻塞脂肪樹配置，具有一致的 RDMA 性能。 H 系列 VM 支援英特爾 MPI 5.x 和 MS-MPI。

ACU：290 - 300

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/s | 基本 CPU 頻率 （GHz） | 全核頻率（GHz，峰值） | 單核頻率（GHz，峰值） | RDMA 性能（Gb/s） | MPI 支援 | 臨時存儲 （GB） | 最大資料磁碟 | 最大乙太網 NIC |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | 英特爾強盛 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | 英特爾 5.x， MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | 英特爾強盛 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | 英特爾 5.x， MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | 英特爾強盛 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | 英特爾 5.x， MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | 英特爾強盛 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | 英特爾 5.x， MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | 英特爾強盛 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | 英特爾 5.x， MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | 英特爾強盛 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | 英特爾 5.x， MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>對於 MPI 應用程式，FDR InfiniBand 網路啟用專用 RDMA 後端網路。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>支援的作業系統映射（Linux）
 
Azure 應用商店有許多支援 RDMA 連接的 Linux 發行版本：
  
* **基於 CentOS 的 HPC** - 對於啟用非 SR-IOV 的 VM、基於 CentOS 的 6.5 HPC 版本或更高版本，最多適用于 7.5。 對於 H 系列 VM，建議版本 7.1 到 7.5。 已在 VM 上安裝 RDMA 驅動程式和 Intel MPI 5.1。
  對於 SR-IOV VM，CentOS-HPC 7.6 進行了優化並預載入了 RDMA 驅動程式和安裝的各種 MPI 套裝軟體。
  對於其他 RHEL/CentOS VM 映射，添加 InfiniBandLinux 擴展以啟用 InfiniBand。 此 Linux VM 擴展安裝 Mellanox OFED 驅動程式（在 SR-IOV VM 上）用於 RDMA 連接。 以下 PowerShell Cmdlet 在現有支援 RDMA 的 VM 上安裝 InfiniBandDriveLinux 擴展的最新版本（版本 1.0）。 支援 RDMA 的 VM 名為*myVM，* 部署在*美國西部*區功能變數名稱為*myResourceGroup*的資源組中，如下所示：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  或者，VM 擴展可以包含在 Azure 資源管理器範本中，以便使用以下 JSON 元素輕鬆部署：
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  以下命令在名為*myVMSS*的現有虛擬機器規模集中的所有支援 RDMA 的 VM 上安裝最新版本 1.0 InfiniBandDriveLinux 擴展，該組合部署在名為*myResourceGroup*的資源組中：
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > 在 CentOS 型 HPC 映像上， **yum** 組態檔中已停用核心更新。 這是因為 Linux RDMA 驅動程式作為 RPM 包分發，如果內核更新，驅動程式更新可能不起作用。
  >
  

* **SUSE Linux 企業伺服器**- SLES 12 SP3 用於 HPC，SLES 12 SP3 用於 HPC（高級）、SLES 12 SP1 用於 HPC（高級）、SLES 12 SP1（高級）、SLES 12 SP4 和 SLES 15。 已在 VM 上安裝 RDMA 驅動程式並散發 Intel MPI 套件。 執行下列命令來安裝 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu 伺服器 16.04 LTS，18.04 LTS。 設定 VM 上的 RDMA 驅動程式，並向 Intel 註冊以下載 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  有關啟用 InfiniBand、設置 MPI 的更多詳細資訊，請參閱[啟用 InfiniBand](./workloads/hpc/enable-infiniband.md)。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
