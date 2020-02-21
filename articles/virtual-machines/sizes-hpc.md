---
title: Azure VM 大小-HPC |Microsoft Docs
description: 列出 Azure 中適用于高效能運算虛擬機器的不同大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: dc2086223dea9bff311aac9e7d4771b5273f0e91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493549"
---
# <a name="high-performance-compute-vm-sizes"></a>高效能運算 VM 大小

Azure H 系列虛擬機器（Vm）的設計目的是針對各種實際的 HPC 工作負載，提供領導層級的效能、MPI 擴充性和成本效益。

[HBv2 系列](hbv2-series.md)Vm 的功能 200 Gb/秒的 Mellanox HDR 不會，而 HB 和 HC 系列 Vm 則具備 100 Gb/秒的 Mellanox EDR 功能。 這些 VM 類型的每一個都是在非封鎖的 fat 樹狀目錄中進行連線，以獲得優化且一致的 RDMA 效能。 HBv2 Vm 支援調適型路由和動態連線傳輸（DCT，也就是標準 RC 和 UD 傳輸的額外功能）。 這些功能可加強應用程式效能、擴充性和一致性，並強烈建議使用它們。

[HB 系列](hb-series.md)Vm 已針對記憶體頻寬所驅動的應用程式優化，例如流體 dynamics、明確有限元素分析和氣象模型。 HB Vm 功能 60 AMD EPYC 7551 處理器核心，每個 CPU 核心 4 GB RAM，且無超執行緒。 AMD EPYC 平臺提供超過每秒 260 GB 的記憶體頻寬。

[HC 系列](hc-series.md)Vm 已針對密集計算所驅動的應用程式優化，例如隱含有限元素分析、分子 dynamics 和計算化學。 HC Vm 功能 44 Intel 更強白金8168處理器核心，每個 CPU 核心 8 GB RAM，無超執行緒。 Intel 的「最強」白金平臺支援 Intel 豐富的軟體工具生態系統，例如 Intel 數學核心程式庫。

[H 系列](h-series.md)Vm 已針對高 CPU 頻率驅動的應用程式，或每個核心需求的大型記憶體優化。 H 系列 Vm 的功能為8或 16 Intel （r） E5 2667 v3 處理器核心、每個 CPU 核心7或 14 GB RAM，且無超執行緒。 H 系列功能 56 Gb/秒 Mellanox FDR 會在非封鎖的 fat 樹狀結構設定中，使 RDMA 效能一致。 H 系列 Vm 支援 Intel MPI 5.x 和 MS-MPI。

## <a name="deployment-considerations"></a>部署考量因素

- **Azure 訂用帳戶** – 若要部署的不只是少數的計算密集執行個體，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

- **價格與可用性** - 這些 VM 大小僅在 [標準] 定價層提供。 如需了解 Azure 區域中的可用性，請查看 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/) 。
- **核心配額** – 您可能需要從預設值增加 Azure 訂用帳戶的核心配額。 您的訂用帳戶可能也會限制您可以在特定 VM 大小系列 (包括 H 系列) 中部署的核心數目。 若要要求增加配額，可免費[開啟線上客戶支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 (預設限制會視您的訂用帳戶類別而有所不同。)

  > [!NOTE]
  > 如果您有大規模的容量需求，請連絡 Azure 支援。 Azure 配額為信用額度，而不是容量保證。 無論您的配額有多少，您只需針對您使用的核心付費。
  
- **虛擬網路** – 使用計算密集型執行個體時，並不需要 Azure [虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/) 。 不過，您可能需要至少一個以雲端為基礎的 Azure 虛擬網路來處理許多部署，或者如果您需要存取內部部署資源，則需要站對站連線。 如有需要，請建立新的虛擬網路來部署執行個體。 不支援將計算密集型 VM 新增至同質群組中的虛擬網路。
- **調整大小** – 因為其特殊硬體，所以您只能夠在相同大小系列內重新調整計算密集型執行個體的大小 (H 系列或計算密集型 A 系列)。 例如，您只能將 H 系列 VM 的大小，從某一個 H 系列大小重新調整為另一個大小。 此外，不支援從非計算密集型大小重新調整為計算密集型大小。  
## <a name="rdma-capable-instances"></a>支援 RDMA 的執行個體

計算密集型實例（A8、A9、H16r、H16mr、HB 和 HC）的子集，功能是遠端直接記憶體存取（RDMA）連線的網路介面。 使用 ' r ' （如 NC24rs 設定，NC24rs_v2 和 NC24rs_v3）指定的 N 系列大小也可以支援 RDMA。 這是可供其他 VM 大小使用之標準 Azure 網路介面的額外界面。

此介面可讓支援 RDMA 的實例透過「未使用」（IB）網路進行通訊，並以 EDR 速率針對 H16r、H16mr 和具備 RDMA 功能的 N 系列虛擬機器進行 HB、HC、FDR 速率的作業，以及 A8 和 A9 虛擬機器的 QDR 速率。 這些 RDMA 功能可以提高特定訊息傳遞介面 (MPI) 應用程式的延展性和效能。 如需速度的詳細資訊，請參閱此頁面上的表格中的詳細資料。

> [!NOTE]
> 在 Azure 中，只有在已啟用 SR-IOV 的 Vm （SR-IOV，目前為 HB 和 HC）才支援透過 IB 的 IP。 所有具備 RDMA 功能的實例都支援透過 IB 的 RDMA。
- **作業系統**-所有上述 HPC 系列 vm 上的 Windows Server 2016。 Windows server 2012 R2 的 Windows Server 2012 也支援非 SR-IOV 啟用的 Vm （因此不包括 HB 和 HC）。

- **Mpi** -在 Azure 上啟用 SR-IOV 的 VM 大小（HB、HC）幾乎可讓 MPI 的任何一種類別與 Mellanox OFED 搭配使用。
在非 SR-IOV 啟用的 Vm 上，支援的 MPI 執行會使用 Microsoft Network Direct （ND）介面，在實例之間進行通訊。 因此，只支援 Microsoft MPI （MS-MPI） 2012 R2 或更新版本和 Intel MPI 5.x 版本。 Intel MPI 執行時間程式庫的較新版本（2017、2018）可能會與 Azure RDMA 驅動程式不相容。

- **INFINIBANDDRIVERWINDOWS VM 擴充**功能-在支援 RDMA 的 vm 上，新增 InfiniBandDriverWindows 延伸模組以啟用「未使用」。 此 Windows VM 延伸模組會安裝 Windows 網路直接驅動程式（在非 SR-IOV Vm 上）或 Mellanox OFED 驅動程式（在 SR-IOV Vm 上）以進行 RDMA 連線。
在 A8 和 A9 實例的某些部署中，會自動新增 HpcVmDrivers 擴充功能。 請注意，HpcVmDrivers VM 擴充功能即將淘汰;它將不會更新。 若要將 VM 擴充功能新增至 VM，您可以使用 [Azure PowerShell](/powershell/azure/overview) Cmdlet。 

  下列命令會在*美國西部*區域中名為*myResourceGroup*的資源群組中，于名為*myVM*的現有具備 RDMA 功能的 VM 上安裝最新版本 1.0 InfiniBandDriverWindows 延伸模組：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  或者，您可以在 Azure Resource Manager 範本中包含 VM 擴充功能，以使用下列 JSON 元素進行輕鬆部署：

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  下列命令會在名為*myResourceGroup*的資源群組中名為*MYVMSS*的現有 VM 擴展集內的所有支援 RDMA 的 vm 上，安裝最新版本 1.0 InfiniBandDriverWindows 延伸模組：

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  如需詳細資訊，請參閱[虛擬機器擴充功能和功能](/extensions/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 您也可以針對已在[傳統部署模型](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 使用擴充功能。

- **RDMA 網路位址空間** - Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/16。 若要在 Azure 虛擬網路中已部署的執行個體上執行 MPI 應用程式，請確定虛擬網路位址空間不會與 RDMA 網路重疊。

## <a name="cluster-configuration-options"></a>叢集組態選項

Azure 提供數個選項來建立 Windows HPC VM 的叢集，而這些 VM 可以使用 RDMA 網路進行通訊，包括： 

- **虛擬機器** - 在相同的可用性設定組中部署支援 RDMA 的 HPC VM (當您使用 Azure Resource Manager 部署模型時)。 如果您使用傳統部署模型，請將 VM 部署在相同的雲端服務中。 

- **虛擬機器擴展集**-在虛擬機器擴展集中，請確定您將部署限制為單一放置群組。 例如，在 Resource Manager 範本中，將 `singlePlacementGroup` 屬性設定為 `true`。 

- **虛擬機器之間的 mpi** -如果虛擬機器（vm）之間需要 mpi 通訊，請確定 vm 位於相同的可用性設定組或虛擬機器相同的擴展集內。

- **Azure CycleCloud** - 在 [Azure CycleCloud](/azure/cyclecloud/) 中建立 HPC 叢集，以在 Windows 節點上執行 MPI 作業。

- **Azure Batch** -建立 [Azure Batch](/azure/batch/)集區以在 Windows Server 計算節點上執行 MPI 工作負載。 如需詳細資訊，請參閱[在 Batch 集區中使用支援 RDMA 或已啟用 GPU 功能的執行個體](../batch/batch-pool-compute-intensive-sizes.md)。 另請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 專案，以便在 Batch 上執行以容器為基礎的工作負載。

- **MICROSOFT Hpc pack** - [hpc pack](https://docs.microsoft.com/powershell/high-performance-computing/overview)包含適用于 ms-chap 的執行時間環境，其會在支援 RDMA 的 Linux vm 上部署時使用 Azure RDMA 網路。 如需部署的範例，請參閱[使用 HPC Pack 設定 LINUX RDMA 叢集以執行 MPI 應用程式](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 如需在 Windows Server 上搭配使用計算密集型實例和 HPC Pack 的檢查清單，請參閱使用[Hpc Pack 設定 LINUX RDMA 叢集以執行 MPI 應用程式](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

- 若要在以 Azure Batch 執行 MPI 應用程式時使用計算密集型執行個體，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。

- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。