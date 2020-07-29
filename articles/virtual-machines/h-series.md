---
title: H 系列-Azure 虛擬機器
description: H 系列 Vm 的規格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: 34f89656fc56b27edafeff0e60874a00139266dd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268946"
---
# <a name="h-series"></a>H 系列

H 系列 Vm 已針對高 CPU 頻率或每個核心需求的大型記憶體所驅動的應用程式優化。 H 系列 Vm 的功能為8或 16 Intel （r） E5 2667 v3 處理器核心，每個 CPU 核心最多 14 GB RAM，且無超執行緒。 H 系列功能 56 Gb/秒 Mellanox FDR 會在非封鎖的 fat 樹狀結構設定中，使 RDMA 效能一致。 H 系列 Vm 支援 Intel MPI 5.x 和 MS-MPI。

ACU：290 - 300

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（Gb/s） | MPI 支援 | 暫存儲存體（GB） | 最大資料磁碟 | 最大磁碟輸送量︰IOPS | 最大乙太網路 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel 強 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel 強 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel 強 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel 強 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel 強 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 強 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup>針對 MPI 應用程式，FDR 未受的網路會啟用專用的 RDMA 後端網路。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>支援的 OS 映射（Linux）
 
Azure Marketplace 有許多支援 RDMA 連線能力的 Linux 散發套件：
  
* **CentOS 為基礎的 hpc** -針對非 sr-iov 啟用的 Vm，CentOS 型版本 6.5 HPC 或更新版本，最多可達7.5。 針對 H 系列 Vm，建議使用7.1 到7.5 版。 已在 VM 上安裝 RDMA 驅動程式和 Intel MPI 5.1。
  針對 SR-IOV Vm，CentOS-HPC 7.6 已針對 RDMA 驅動程式和安裝的各種 MPI 套件進行優化和預先載入。
  若為其他 RHEL/CentOS VM 映射，請新增 InfiniBandLinux 延伸模組以啟用 [未使用]。 此 Linux VM 擴充功能會安裝適用于 RDMA 連線的 Mellanox OFED 驅動程式（在 SR-IOV Vm 上）。 下列 PowerShell Cmdlet 會在現有具備 RDMA 功能的 VM 上安裝 InfiniBandDriverLinux 延伸模組的最新版本（版本1.0）。 具備 RDMA 功能的 VM 會命名為*myVM* ，並部署在*美國西部*區域中名為*myResourceGroup*的資源群組中，如下所示：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  或者，您可以將 VM 擴充功能包含在 Azure Resource Manager 範本中，以便使用下列 JSON 元素進行輕鬆部署：
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  下列命令會在名為*myResourceGroup*的資源群組中部署且名為*myVMSS*的現有虛擬機器擴展集內的所有支援 RDMA 的 vm 上，安裝最新版本 1.0 InfiniBandDriverLinux 延伸模組：
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > 在 CentOS 型 HPC 映像上， **yum** 組態檔中已停用核心更新。 這是因為 Linux RDMA 驅動程式是以 RPM 套件的形式散發，如果核心已更新，驅動程式更新可能無法正常執行。
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 for HPC、SLES 12 SP3 for Hpc （premium）、SLES 12 SP1 for HPC、SLES 12 SP1 for Hpc （Premium）、SLES 12 SP4 和 sles 15。 已在 VM 上安裝 RDMA 驅動程式並散發 Intel MPI 套件。 執行下列命令來安裝 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu SERVER 16.04 LTS、18.04 LTS。 設定 VM 上的 RDMA 驅動程式，並向 Intel 註冊以下載 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  如需有關啟用「未[使用](./workloads/hpc/enable-infiniband.md)」的詳細資訊，請參閱設定 MPI。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
