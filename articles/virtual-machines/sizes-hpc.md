---
title: Azure VM 大小-HPC |Microsoft Docs
description: 列出 Azure 中適用于高效能運算虛擬機器的不同大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 2d52287d1c343ada58ed4f7e5e1d3e85a4e7162e
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850445"
---
# <a name="high-performance-computing-vm-sizes"></a>高效能計算 VM 大小

Azure H 系列虛擬機器（Vm）的設計目的是針對各種實際的 HPC 工作負載，提供領導層級的效能、MPI 擴充性和成本效益。

[HBv2 系列](hbv2-series.md)Vm 的功能 200 Gb/秒的 Mellanox HDR 不會，而 HB 和 HC 系列 Vm 則具備 100 Gb/秒的 Mellanox EDR 功能。 這些 VM 類型的每一個都是在非封鎖的 fat 樹狀目錄中進行連線，以獲得優化且一致的 RDMA 效能。 HBv2 Vm 支援調適型路由和動態連線傳輸（DCT，也就是標準 RC 和 UD 傳輸的額外功能）。 這些功能可加強應用程式效能、擴充性和一致性，並強烈建議使用它們。

[HB 系列](hb-series.md)Vm 已針對記憶體頻寬所驅動的應用程式優化，例如流體 dynamics、明確有限元素分析和氣象模型。 HB Vm 功能 60 AMD EPYC 7551 處理器核心，每個 CPU 核心 4 GB RAM，且無超執行緒。 AMD EPYC 平臺提供超過每秒 260 GB 的記憶體頻寬。

[HC 系列](hc-series.md)Vm 已針對密集計算所驅動的應用程式優化，例如隱含有限元素分析、分子 dynamics 和計算化學。 HC Vm 功能 44 Intel 更強白金8168處理器核心，每個 CPU 核心 8 GB RAM，無超執行緒。 Intel 的「最強」白金平臺支援 Intel 豐富的軟體工具生態系統，例如 Intel 數學核心程式庫。

[H 系列](h-series.md)Vm 已針對高 CPU 頻率驅動的應用程式，或每個核心需求的大型記憶體優化。 H 系列 Vm 的功能為8或 16 Intel （r） E5 2667 v3 處理器核心、每個 CPU 核心7或 14 GB RAM，且無超執行緒。 H 系列功能 56 Gb/秒 Mellanox FDR 會在非封鎖的 fat 樹狀結構設定中，使 RDMA 效能一致。 H 系列 Vm 支援 Intel MPI 5.x 和 MS-MPI。

> [!NOTE]
> A8 – A11 VM 計劃於 2021 年 3 月淘汰。 如需詳細資訊，請參閱 [HPC 移轉指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

## <a name="rdma-capable-instances"></a>支援 RDMA 的執行個體

大部分 HPC VM 大小（HBv2、HB、HC、H16r、H16mr、A8 和 A9）的功能都是遠端直接記憶體存取（RDMA）連線的網路介面。 使用 ' r ' （如 NC24rs 設定（NC24rs_v3、NC24rs_v2 和 NC24r）指定的[N 系列](https://docs.microsoft.com/azure/virtual-machines/nc-series)大小也是支援 RDMA 的。 除了其他 VM 大小中可用的標準 Azure 網路介面以外，此介面也是。

此介面可讓支援 RDMA 的實例透過「未使用」（IB）網路進行通訊，以 HBv2 的 HDR 速率操作、HB 的 EDR 費率、HC、H16r、H16mr 和具備 RDMA 功能的 N 系列虛擬機器，以及適用于 A8 和 A9 Vm 的 QDR 速率。 這些 RDMA 功能可以提高特定訊息傳遞介面 (MPI) 應用程式的延展性和效能。 如需速度的詳細資訊，請參閱此頁面上的表格中的詳細資料。

> [!NOTE]
> 在 Azure HPC 中，會有兩個類別的 Vm，取決於它們是否已啟用可進行不限的 sr-iov。 目前，已啟用不支援的 Vm 的 SR-IOV 為： HBv2、HB、HC、NCv3 和 NDv2。 已啟用不支援的 Vm 的其餘部分不會啟用 SR-IOV。
> 所有具備 RDMA 功能的 Vm 皆支援透過 IB 的 RDMA。
> 只有在已啟用 SR-IOV 的 Vm 上，才支援透過 IB 的 IP。

- **作業系統**-LINUX 對 HPC vm 的支援非常妥善;通常會使用散發版本（例如 CentOS、RHEL、Ubuntu、SUSE）。 關於 Windows 支援，所有 HPC 系列 Vm 都支援 Windows Server 2016 和更新版本。 Windows server 2012 R2、Windows Server 2012 也支援非 SR-IOV 啟用的 Vm （H16r、H16mr、A8 和 A9）。 請注意， [HBv2 和其他具有超過64（虛擬或實體）核心的 vm 不支援 Windows Server 2012 R2](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。

- **Mpi** -在 Azure 上啟用 SR-IOV 的 VM 大小（HBV2、HB、HC、NCv3、NDv2）幾乎可讓 MPI 的任何一種類別與 Mellanox OFED 搭配使用。
在非 SR-IOV 啟用的 Vm 上，支援的 MPI 執行會使用 Microsoft Network Direct （ND）介面在 Vm 之間進行通訊。 因此，只支援 Microsoft MPI （MS-MPI） 2012 R2 或更新版本和 Intel MPI 5.x 版本。 Intel MPI 執行時間程式庫的較新版本（2017、2018）可能會與 Azure RDMA 驅動程式不相容。

- **InfiniBandDriver<Linux |Windows> VM 擴充**功能-在支援 RDMA 的 vm 上，新增 InfiniBandDriver<Linux |Windows> 擴充功能，以啟用未使用。 在 Linux 上，InfiniBandDriverLinux VM 擴充功能會安裝適用于 RDMA 連線的 Mellanox OFED 驅動程式（在 SR-IOV Vm 上）。 在 Windows 上，InfiniBandDriverWindows VM 擴充功能會安裝 Windows 網路直接驅動程式（在非 SR-IOV Vm 上）或 Mellanox OFED 驅動程式（在 SR-IOV Vm 上）以進行 RDMA 連線。
在 A8 和 A9 實例的某些部署中，會自動新增 HpcVmDrivers 擴充功能。 請注意，HpcVmDrivers VM 擴充功能即將淘汰;它將不會更新。
若要將 VM 擴充功能新增至 VM，您可以使用 [Azure PowerShell](/powershell/azure/overview) Cmdlet。 

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

  下列命令會在名為*myResourceGroup*的資源群組中部署且名為*myVMSS*的現有虛擬機器擴展集內的所有支援 RDMA 的 vm 上，安裝最新版本 1.0 InfiniBandDriverWindows 延伸模組：

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  如需詳細資訊，請參閱[虛擬機器擴充功能和功能](./extensions/overview.md)。 您也可以針對已在[傳統部署模型](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 使用擴充功能。

- **RDMA 網路位址空間** - Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/16。 若要在 Azure 虛擬網路中已部署的執行個體上執行 MPI 應用程式，請確定虛擬網路位址空間不會與 RDMA 網路重疊。

## <a name="cluster-configuration-options"></a>叢集組態選項

Azure 提供數個選項來建立 Windows HPC VM 的叢集，而這些 VM 可以使用 RDMA 網路進行通訊，包括： 

- **虛擬機器**-在相同的擴展集或可用性設定組中部署支援 RDMA 的 HPC vm （當您使用 Azure Resource Manager 部署模型時）。 如果您使用傳統部署模型，請將 VM 部署在相同的雲端服務中。

- **虛擬機器擴展集**-在虛擬機器擴展集（VMSS）中，請確定您將部署限制為單一放置群組，以便在 VMSS 內進行不確定的通訊。 例如，在 Resource Manager 範本中，將 `singlePlacementGroup` 屬性設定為 `true`。 請注意，根據預設，可由屬性啟動的最大 VMSS 大小上限 `singlePlacementGroup` `true` 為 100 vm。 如果您的 HPC 作業級別需求高於單一 VMSS 租使用者中的 100 Vm，您可以要求增加、免費[開啟線上客戶支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 單一 VMSS 中的 Vm 數目限制可以增加至300。 請注意，使用可用性設定組部署 Vm 時，最大限制是每個可用性設定組200個 Vm。

- 虛擬機器之間的**MPI** -如果虛擬機器（vm）之間需要 RDMA （例如使用 MPI 通訊），請確定 vm 位於相同的虛擬機器擴展集或可用性設定組中。

- **Azure CycleCloud** -在[AZURE CYCLECLOUD](/azure/cyclecloud/)中建立 HPC 叢集以執行 MPI 工作。

- **Azure Batch** -建立[Azure Batch](/azure/batch/)集區以執行 MPI 工作負載。 若要在以 Azure Batch 執行 MPI 應用程式時使用計算密集型執行個體，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。

- **MICROSOFT HPC Pack**  - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview)包含適用于 ms-chap 的執行時間環境，它會在部署于支援 RDMA 的 Linux vm 上時使用 Azure RDMA 網路。 如需部署的範例，請參閱[使用 HPC Pack 設定 LINUX RDMA 叢集以執行 MPI 應用程式](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

## <a name="deployment-considerations"></a>部署考量因素

- **Azure 訂用帳戶** – 若要部署的不只是少數的計算密集執行個體，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

- **價格與可用性** - 這些 VM 大小僅在 [標準] 定價層提供。 如需了解 Azure 區域中的可用性，請查看 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/) 。

- **核心配額** – 您可能需要從預設值增加 Azure 訂用帳戶的核心配額。 您的訂用帳戶可能也會限制您可以在特定 VM 大小系列 (包括 H 系列) 中部署的核心數目。 若要要求增加配額，可免費[開啟線上客戶支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 (預設限制會視您的訂用帳戶類別而有所不同。)

  > [!NOTE]
  > 如果您有大規模的容量需求，請連絡 Azure 支援。 Azure 配額為信用額度，而不是容量保證。 無論您的配額有多少，您只需針對您使用的核心付費。
  
- **虛擬網路** – 使用計算密集型執行個體時，並不需要 Azure [虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/) 。 不過，您可能需要至少一個以雲端為基礎的 Azure 虛擬網路來處理許多部署，或者如果您需要存取內部部署資源，則需要站對站連線。 如有需要，請建立新的虛擬網路來部署執行個體。 不支援將計算密集型 VM 新增至同質群組中的虛擬網路。

- **調整大小** – 因為其特殊硬體，所以您只能夠在相同大小系列內重新調整計算密集型執行個體的大小 (H 系列或計算密集型 A 系列)。 例如，您只能將 H 系列 VM 的大小，從某一個 H 系列大小重新調整為另一個大小。 此外，不支援從非計算密集型大小重新調整為計算密集型大小。  


## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何將 Azure 的 HPC 應用程式優化，以及[Hpc 工作負載](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview)的一些範例 

- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
