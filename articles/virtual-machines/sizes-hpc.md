---
title: Azure VM 大小 - HPC |微軟文件
description: 列出 Azure 中可用於高性能計算虛擬機器的不同大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
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
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420862"
---
# <a name="high-performance-computing-vm-sizes"></a>高性能計算 VM 大小

Azure H 系列虛擬機器 (VM) 旨在為各種實際 HPC 工作負載提供領先性能、MPI 可擴充性和成本效益。

[HBv2 系列](hbv2-series.md)VM 具有 200 Gb/秒 Mellanox HDR InfiniBand,而 HB 和 HC 系列 VM 均具有 100 Gb/秒 Mellanox EDR InfiniBand。 這些 VM 類型中的每一種都連接到非阻塞的胖樹中,以優化和一致的 RDMA 性能。 HBv2 VM 支援自適應路由和動態連接傳輸(DCT,除了標準 RC 和 UD 傳輸)。 這些功能增強了應用程式性能、可擴展性和一致性,強烈建議使用這些功能。

[HB 系列](hb-series.md)VM 針對由記憶體頻寬驅動的應用進行了優化,例如流體動力學、顯式有限元分析和天氣建模。 HB VM 具有 60 個 AMD EPYC 7551 處理器內核、每個 CPU 內核 4 GB 的 RAM 以及無超線程讀取功能。 AMD EPYC 平臺提供超過 260 GB/秒的記憶體頻寬。

[HC 系列](hc-series.md)VM 針對密集計算驅動的應用進行了優化,例如隱式有限元分析、分子動力學和計算化學。 HC VM 具有 44 個英特爾至強白金 8168 處理器內核、每個 CPU 內核 8 GB 的 RAM,並且沒有超線程。 英特爾到強白金平臺支援英特爾豐富的軟體工具生態系統,如英特爾數學內核庫。

[H 系列](h-series.md)VM 針對由高 CPU 頻率或每個核心要求的大記憶體驅動的應用進行了優化。 H 系列 VM 具有 8 或 16 英特爾至強 E5 2667 v3 處理器內核、每個 CPU 內核 7 或 14 GB 的 RAM,並且沒有超線程。 H 系列採用 56 Gb/秒 Mellanox FDR InfiniBand,採用非阻塞脂肪樹配置,具有一致的 RDMA 性能。 H 系列 VM 支援英特爾 MPI 5.x 和 MS-MPI。

> [!NOTE]
> A8 + A11 VM 計劃於 2021 年 3 月 3 日停用。 有關詳細資訊,請參閱[HPC 遷移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

## <a name="rdma-capable-instances"></a>支援 RDMA 的執行個體

大多數 HPC VM 大小(HBv2、HB、HC、H16r、H16mr、A8 和 A9)具有用於遠端直接記憶體訪問 (RDMA) 連接的網路介面。 選取的 [Nhttps://docs.microsoft.com/azure/virtual-machines/nc-series)系列] (使用"r"指定的大小(如 NC24rs 配置(NC24rs_v3、NC24rs_v2和 NC24r)也支援 RDMA。 此介面是其他 VM 大小中可用的標準 Azure 網路介面的補充。

此介面允許支援 RDMA 的實例透過 InfiniBand (IB) 網路進行通訊,HBv2 的 HDR 速率、HB 的 EDR 速率、H16r、H16mr 和 RDMA 的 VDR 速率以及 A8 和 A9 VM 的 QDR 速率。 這些 RDMA 功能可以提高特定訊息傳遞介面 (MPI) 應用程式的延展性和效能。 有關速度的詳細資訊,請參閱此頁面表中的詳細資訊。

> [!NOTE]
> 在 Azure HPC 中,有兩類 VM,具體取決於它們是否為 InfiniBand 啟用了 SR-IOV。 目前,適用於英菲尼班德的 SR-IOV 支援 VM 是:HBv2、HB、HC 和 NCv3。 啟用 InfiniBand 的其餘 VM 未啟用 SR-IOV。
> 所有支援 RDMA 的 VM 都支援 IB 的 RDMA。
> 支援 SR-IOV 的 VM 上僅支援 IB 上的 IP。

- **操作系統**- Linux 非常適合 HPC VM,CentOS、RHEL、Ubuntu、SUSE 等發行版很常見。 關於 Windows 支援,所有 HPC 系列 VM 都支援 Windows Server 2016。 支援啟用 SR-IOV 的 VM 上還支援 Windows 伺服器 2012 R2、Windows Server 2012。

- **MPI** - Azure 上啟用 SR-IOV 的 VM 大小(HBv2、HB、HC、NCv3)允許幾乎任何版本的 MPI 與 Mellanox OFED 一起使用。
在啟用 SR-IOV 的 VM 上,支援的 MPI 實現使用 Microsoft 網路直接 (ND) 介面在 VM 之間通訊。 因此,僅支援 Microsoft MPI (MS-MPI) 2012 R2 或更高版本和英特爾 MPI 5.x 版本。 英特爾 MPI 運行時庫的更高版本(2017、2018)可能與 Azure RDMA 驅動程式相容,也可能不相容。

- **英菲尼班德<Linux*Windows> VM 延伸**- 在支援 RDMA 的 VM 上,新增 InfiniBandDriver<Linux*視窗>擴展,以啟用 InfiniBand。 在 Linux 上,InfiniBandDriverLinux VM 擴展安裝 Mellanox OFED 驅動程式(在 SR-IOV VM 上)用於 RDMA 連接。 在 Windows 上,InfiniBandDriverWindows VM 擴展安裝 Windows 網路直接驅動程式(在非 SR-IOV VM 上)或 Mellanox OFED 驅動程式(在 SR-IOV VM 上)用於 RDMA 連接。
在某些 A8 和 A9 實體部署中,將自動添加 HpcVmDrivers 擴展。 請注意,正在棄用 HpcVm 驅動程式 VM 擴展;它將不會更新。
若要將 VM 擴充功能新增至 VM，您可以使用 [Azure PowerShell](/powershell/azure/overview) Cmdlet。 

  以下命令將最新版本 1.0 InfiniBandDriverWindows 安裝在現有的支援 RDMA 的 VM 上安裝,該 VM 名為*myVM,* 部署在*美國西部*區域名為*myResourceGroup*的資源組中:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  或者,VM 擴展可以包含在 Azure 資源管理器範本中,以便輕鬆部署,並包含以下 JSON 元素:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  以下命令在名為*myVMSS*的現有虛擬機器規模集中的所有支援 RDMA 的 VM 上安裝最新版本 1.0 InfiniBandDriverWindows 擴充,該組合部署在名為*myResourceGroup*的資源組中:

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

- **虛擬機**- 在同一規模集或可用性集中部署支援 RDMA 的 HPC VM(使用 Azure 資源管理員部署模型時)。 如果您使用傳統部署模型，請將 VM 部署在相同的雲端服務中。

- **虛擬機器縮放集**─在虛擬機器規模集 (VMSS) 中,請確保將部署限制為單個放置組。 例如，在 Resource Manager 範本中，將 `singlePlacementGroup` 屬性設定為 `true`。 請注意,默認情況下,可以使用`singlePlacementGroup``true`屬性 旋轉到的最大 VMSS 大小上限為 100 VM。 如果您的 HPC 工作規模需求高於單個 VMSS 租戶中的 100 個 VM,您可以請求增加,[免費開啟線上客戶支援請求](../azure-supportability/how-to-create-azure-support-request.md)。

- **虛擬機器之間的 MPI** - 如果虛擬機器 (VM) 之間需要 RDMA(例如使用 MPI 通訊),請確保 VM 位於相同的虛擬機器規模集或可用性集中。

- **Azure 循環雲**- 在[Azure 迴圈雲](/azure/cyclecloud/)中創建 HPC 叢集以運行 MPI 作業。

- **Azure 批次處理**- 創建[Azure 批次處理](/azure/batch/)的 MPI 工作負荷。 若要在以 Azure Batch 執行 MPI 應用程式時使用計算密集型執行個體，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。

- **Microsoft HPC 包** - [HPC 包](https://docs.microsoft.com/powershell/high-performance-computing/overview)包括 MS-MPI 的執行時環境,該環境在部署在支援 RDMA 的 Linux VM 上時使用 Azure RDMA 網路。 例如部署,請參閱使用[HPC Pack 設定 Linux RDMA 叢集以執行 MPI 應用程式](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

## <a name="deployment-considerations"></a>部署考量因素

- **Azure 訂用帳戶** – 若要部署的不只是少數的計算密集執行個體，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

- **價格與可用性** - 這些 VM 大小僅在 [標準] 定價層提供。 如需了解 Azure 區域中的可用性，請查看 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/) 。

- **核心配額** – 您可能需要從預設值增加 Azure 訂用帳戶的核心配額。 您的訂用帳戶可能也會限制您可以在特定 VM 大小系列 (包括 H 系列) 中部署的核心數目。 要要求增加配額,[請免費開啟線上客戶支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 (預設限制會視您的訂用帳戶類別而有所不同。)

  > [!NOTE]
  > 如果您有大規模的容量需求，請連絡 Azure 支援。 Azure 配額為信用額度，而不是容量保證。 無論您的配額有多少，您只需針對您使用的核心付費。
  
- **虛擬網路** – 使用計算密集型執行個體時，並不需要 Azure [虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/) 。 不過，您可能需要至少一個以雲端為基礎的 Azure 虛擬網路來處理許多部署，或者如果您需要存取內部部署資源，則需要站對站連線。 如有需要，請建立新的虛擬網路來部署執行個體。 不支援將計算密集型 VM 新增至同質群組中的虛擬網路。

- **調整大小** – 因為其特殊硬體，所以您只能夠在相同大小系列內重新調整計算密集型執行個體的大小 (H 系列或計算密集型 A 系列)。 例如，您只能將 H 系列 VM 的大小，從某一個 H 系列大小重新調整為另一個大小。 此外，不支援從非計算密集型大小重新調整為計算密集型大小。  


## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 瞭解有關為 Azure 優化 HPC 應用程式以及 [HPC 工作負荷] 的一些範例(https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
