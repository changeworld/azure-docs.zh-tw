---
title: 搭配 Batch 使用計算密集型 Azure Vm
description: 如何利用 Azure Batch 集區中的 HPC 和 GPU 虛擬機器大小。 瞭解 OS 相依性，並查看數個案例範例。
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: 674ee6c5b96c7aaf2926b51824488d03fc56d0a6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115953"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>在 Batch 集區中使用 RDMA 或 GPU 執行個體

為了執行特定 Batch 作業，您可以利用專門設計來進行大規模計算的 Azure VM 大小。 例如：

* 若要執行多執行個體的 [MPI 工作負載](batch-mpi.md)，請選擇 H 系列，或擁有網路介面可進行遠端直接記憶體存取 (RDMA) 的其他大小。 這些大小會連線到 InfiniBand 網路來進行節點間通訊，以加速 MPI 應用程式的運作。 

* 若是 CUDA 應用程式，請選擇 N 系列大小，因為這些大小包含 NVIDIA Tesla 圖形處理器 (GPU) 顯示卡。

本文會就如何在 Batch 集區中使用某些 Azure 特製大小提供指導方針與範例。 若要了解規格及背景，請參閱：

* 高效能計算 VM 大小 ([Linux](../virtual-machines/linux/sizes-hpc.md)、[Windows](../virtual-machines/windows/sizes-hpc.md)) 

* 已啟用 GPU 功能的 VM 大小 ([Linux](../virtual-machines/linux/sizes-gpu.md)、[Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> 在您用來建立 Batch 帳戶的區域中，可能不會提供特定 VM 大小。 若要確認是否有提供某個大小，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)和[為 Batch 集區選擇 VM 大小](batch-pool-vm-sizes.md)。

## <a name="dependencies"></a>相依性

只有特定作業系統會支援 Batch 中需要大量計算之大小的 RDMA 或 GPU 功能 （支援的作業系統清單是以這些大小建立之虛擬機器所支援的作業系統子集）。視您建立 Batch 集區的方式而定，您可能需要在節點上安裝或設定額外的驅動程式或其他軟體。 下表摘要說明這些相依性。 如需詳細資訊，請參閱連結的文章。 若要了解用來設定 Batch 集區的選項，請參閱本文稍後的內容。

### <a name="linux-pools---virtual-machine-configuration"></a>Linux 集區 - 虛擬機器組態

| 大小 | 功能 | 作業系統 | 必要的軟體 | 集區設定 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/linux/sizes-hpc.md)<br/>[NC24r、NC24rs_v2、NC24rs_v3、ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS，或<br/>CentOS 型 HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA 驅動程式 | 啟用節點間通訊、停用並行工作執行 |
| [NC、NCv2、NCv3、NDv2 系列](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (依系列而有所不同) | Ubuntu 16.04 LTS，或<br/>CentOS 7.3 或 7.4<br/>(Azure Marketplace) | NVIDIA CUDA 或 CUDA Toolkit 驅動程式 | 不適用 | 
| [NV、NVv2 系列](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS，或<br/>CentOS 7.3<br/>(Azure Marketplace) | NVIDIA GRID 驅動程式 | 不適用 |

<sup>*</sup>具備 RDMA 功能的 N 系列大小也包含 NVIDIA Tesla Gpu

### <a name="windows-pools---virtual-machine-configuration"></a>Windows 集區 - 虛擬機器組態

| 大小 | 功能 | 作業系統 | 必要的軟體 | 集區設定 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r、NC24rs_v2、NC24rs_v3、ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016、2012 R2 或<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 或更新版本，或<br/> Intel MPI 5<br/><br/>Windows RDMA 驅動程式 | 啟用節點間通訊、停用並行工作執行 |
| [NC、NCv2、NCv3、ND、NDv2 系列](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (依系列而有所不同) | Windows Server 2016 或 <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA 或 CUDA Toolkit 驅動程式| 不適用 | 
| [NV、NVv2 系列](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 或<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID 驅動程式 | 不適用 |

<sup>*</sup>具備 RDMA 功能的 N 系列大小也包含 NVIDIA Tesla Gpu

### <a name="windows-pools---cloud-services-configuration"></a>Windows 集區 - 雲端服務組態

> [!NOTE]
> 具有雲端服務組態的 Batch 集區不支援 N 系列大小。
>

| 大小 | 功能 | 作業系統 | 必要的軟體 | 集區設定 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016、2012 R2、2012 或<br/>2008 R2 (客體作業系統系列) | Microsoft MPI 2012 R2 或更新版本，或<br/>Intel MPI 5<br/><br/>Windows RDMA 驅動程式 | 啟用節點間通訊、<br/> 停用並行工作執行 |

## <a name="pool-configuration-options"></a>集區組態選項

為了對 Batch 集區設定特製 VM 大小，有數個選項可供您安裝必要的軟體或驅動程式：

* 針對虛擬機器設定中的集區，選擇預先設定的 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) VM 映像，其中已預先安裝驅動程式和軟體。 範例： 

  * [CentOS 型 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - 包含 RDMA 驅動程式和 Intel MPI 5.1

  * 適用於 Linux 或 Windows 的[資料科學虛擬機器](../machine-learning/data-science-virtual-machine/overview.md) - 包含 NVIDIA CUDA 驅動程式

  * 適用於 Batch 容器工作負載的 Linux 映像 (也包含 GPU 和 RDMA 驅動程式)：

    * [適用於 Azure Batch 容器集區的 CentOS (包含 GPU 和 RDMA 驅動程式)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [適用於 Azure Batch 容器集區的 Ubuntu Server (包含 GPU 和 RDMA 驅動程式)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* 建立[自訂 Windows 或 Linux VM 映像](batch-sig-images.md)，並在其中安裝 VM 大小所需的驅動程式、軟體或其他設定。 

* 從壓縮的驅動程式或應用程式安裝程式建立 Batch [應用程式套件](batch-application-packages.md)，然後設定 Batch 將套件部署至集區節點，並在建立每個節點之後安裝一次。 例如，如果該應用程式套件是安裝程式，請建立[啟動工作](batch-api-basics.md#start-task)命令列，以在所有集區節點上以無訊息方式安裝應用程式。 如果您的工作負載相依於特定驅動程式版本，請考慮使用應用程式套件和集區啟動工作。

  > [!NOTE] 
  > 啟動工作必須以提升的 (系統管理員) 權限來執行，而且必須等候到成功。 長時間執行的工作會增加佈建 Batch 集區的時間。
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) 會自動將 GPU 和 RDMA 驅動程式設定為對 Azure Batch 上的容器化工作負載透明地進行處理。 Batch Shipyard 完全是透過組態檔來驅動。 有許多可用的範例配方設定可啟用 GPU 和 RDMA 工作負載，例如 [CNTK GPU 配方](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)，此配方會在 N 系列 VM 上預先設定 GPU 驅動程式，並以 Docker 映像的形式載入 Microsoft Cognitive Toolkit 軟體。


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>範例： Windows NC VM 集區上的 NVIDIA GPU 驅動程式

若要在 Windows NC 節點所構成的集區上執行 CUDA 應用程式，您必須在安裝 NVDIA GPU 驅動程式。 下列範例步驟使用應用程式套件來安裝 NVIDIA GPU 驅動程式。 如果您的工作負載相依於特定 GPU 驅動程式版本，您可以選擇此選項。

1. 從 [NVIDIA 網站](https://www.nvidia.com/Download/index.aspx)下載 Windows Server 2016 版 GPU 驅動程式的安裝套件 - 例如 [411.82 版](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe)。 使用 *GPUDriverSetup.exe* 之類的簡短名稱將檔案儲存在本機。
2. 建立該套件的 zip 檔案。
3. 將套件上傳至您的 Batch 帳戶。 如需相關步驟，請參閱[應用程式套件](batch-application-packages.md)指引。 指定 [應用程式識別碼]，例如*GPUDriver*，以及*411.82*之類的版本。
1. 使用 Batch API 或 Azure 入口網站，在虛擬機器設定中建立具有所需節點數目和規模大小的集區。 下表顯示使用啟動工作以無訊息方式安裝 NVIDIA GPU 驅動程式的範例設定：

| 設定 | 值 |
| ---- | ----- | 
| **映像類型** | Marketplace (Linux/Windows) |
| **發行者** | MicrosoftWindowsServer |
| **供應項目** | WindowsServer |
| **限量** | 2016-Datacenter |
| **節點大小** | NC6 標準 |
| **應用程式套件參考** | GPUDriver，版本 411.82 |
| **已啟用啟動工作** | True<br>**命令列** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**使用者身分識別** - 集區的自動使用者、系統管理員<br/>**等待成功** - True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>範例： Linux NC VM 集區上的 NVIDIA GPU 驅動程式

若要在 Linux NC 節點所構成的集區上執行 CUDA 應用程式，您必須從 CUDA Toolkit 安裝必要的 NVIDIA Tesla GPU 驅動程式。 下列範例步驟會建立和部署具有 GPU 驅動程式的自訂 Ubuntu 16.04 LTS 映像：

1. 部署執行 Ubuntu 16.04 LTS 的 Azure NC 系列 VM。 例如，在美國中南部區域建立 VM。 
2. 使用 Azure 入口網站、連線到 Azure 訂用帳戶的用戶端電腦或 Azure Cloud Shell，將 [NVIDIA GPU 驅動程式延伸模組](../virtual-machines/extensions/hpccompute-gpu-linux.md
)新增至 VM。 或者，遵循步驟來手動連線到 VM 並[安裝 CUDA 驅動程式](../virtual-machines/linux/n-series-driver-setup.md)。
3. 請遵循步驟來建立 Batch 的[共用映射資源庫映射](batch-sig-images.md)。
4. 在支援 NC VM 的區域建立 Batch 帳戶。
5. 使用 Batch Api 或 Azure 入口網站，[使用自訂映射](batch-sig-images.md)建立集區，並以所需的節點數目和規模進行調整。 下表顯示該映像的集區設定範例：

| 設定 | 值 |
| ---- | ---- |
| **映像類型** | 自訂映像 |
| **自訂映射** | *映像的名稱* |
| **節點代理程式 SKU** | batch.node.ubuntu 16.04 |
| **節點大小** | NC6 標準 |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>範例： Windows H16r VM 集區上的 Microsoft MPI

若要在 Azure H16r VM 節點所構成的集區上執行 Windows MPI 應用程式，您必須設定 HpcVmDrivers 延伸模組並安裝 [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi)。 以下的範例步驟可供您部署具有必要驅動程式和軟體的自訂 Windows Server 2016 映像：

1. 部署執行 Windows Server 2016 的 Azure H16r VM。 例如，在美國西部區域建立 VM。 
2. 從連線到 Azure 訂用帳戶的用戶端電腦[執行 Azure PowerShell 命令](../virtual-machines/sizes-hpc.md)，或使用 Azure Cloud Shell，將 HpcVmDrivers 延伸模組新增至 VM。 
1. 建立對 VM 的遠端桌面連線。
1. 下載適用於最新版 Microsoft MPI 的[安裝套件](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe)，然後安裝 Microsoft MPI。
1. 請遵循步驟來建立 Batch 的[共用映射資源庫映射](batch-sig-images.md)。
1. 使用 Batch Api 或 Azure 入口網站，[使用共用映射資源庫](batch-sig-images.md)和所需的節點數目和規模來建立集區。 下表顯示該映像的集區設定範例：

| 設定 | 值 |
| ---- | ---- |
| **映像類型** | 自訂映像 |
| **自訂映射** | *映像的名稱* |
| **節點代理程式 SKU** | batch.node.windows amd64 |
| **節點大小** | H16r 標準 |
| **已啟用節點間通訊** | True |
| **每個節點的工作數上限** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>範例： Linux H16r VM 集區上的 Intel MPI

若要在 Linux H 系列節點所構成的集區上執行 MPI 應用程式，其中一個選項是從 Azure Marketplace 使用 [CentOS 型 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) 映像。 已預先安裝 Linux RDMA 驅動程式和 Intel MPI。 此映像也支援 Docker 容器工作負載。

使用 Batch API 或 Azure 入口網站，透過此映像建立具有所需節點數目和規模大小的集區。 下表顯示範例集區設定：

| 設定 | 值 |
| ---- | ---- |
| **映像類型** | Marketplace (Linux/Windows) |
| **發行者** | OpenLogic |
| **供應項目** | CentOS-HPC |
| **限量** | 7.4 |
| **節點大小** | H16r 標準 |
| **已啟用節點間通訊** | True |
| **每個節點的工作數上限** | 1 |

## <a name="next-steps"></a>後續步驟

* 若要在 Azure Batch 集區上執行 MPI 作業，請參閱 [Windows](batch-mpi.md) 或 [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) 範例。

* 如需 Batch 上之 GPU 工作負載的範例，請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard/) 配方。
