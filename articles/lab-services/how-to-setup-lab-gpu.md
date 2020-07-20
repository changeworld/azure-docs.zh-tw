---
title: 在 Azure 實驗室服務中設定具有 Gpu 的實驗室 |Microsoft Docs
description: 瞭解如何使用圖形處理單元（GPU）虛擬機器來設定實驗室。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 63b7a8c58bb6d277233268ed4fc0bc870fdd337f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443395"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>使用 GPU 虛擬機器設定實驗室

本文示範如何執行下列工作：

- 選擇 [*視覺效果*] 和 [*計算*圖形處理器（gpu）]。
- 請確定已安裝適當的 GPU 驅動程式。

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>選擇視覺效果和計算 GPU 大小
在 [實驗室建立嚮導] 的第一頁上，于 [**您需要哪一種虛擬機器大小？** ] 下拉式清單中，選取您的類別所需的 vm 大小。  

![[新增實驗室] 窗格的螢幕擷取畫面，用於選取 VM 大小](./media/how-to-setup-gpu/lab-gpu-selection.png)

在此流程中，您可以選擇選取 [**視覺效果**] 或 [**計算**gpu]。  請務必選擇以您的學生將使用的軟體為基礎的 GPU 類型。  

如下表所述，*計算*GPU 大小適用于計算密集型應用程式。  例如，[自然語言處理類別類型中的深度學習](./class-type-deep-learning-natural-language-processing.md)會使用**小型 GPU （計算）** 大小。  計算 GPU 適用于這種類型的類別，因為學生會使用[資料科學虛擬機器影像](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)所提供的深度學習架構和工具，以大型資料集來定型深度學習模型。

| 大小 | 核心 | RAM | 描述 | 
| ---- | ----- | --- | ----------- | 
| 小型 GPU (計算) | -&nbsp;6個 &nbsp; 核心<br>-&nbsp;56 &nbsp; GB 的 &nbsp; RAM  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |此大小最適合需要大量電腦的應用程式，例如人工智慧（AI）和深度學習。 |

*視覺效果*GPU 大小適用于需要大量圖形的應用程式。  例如， [SolidWorks 工程類別類型](./class-type-solidworks.md)會顯示使用**小型 GPU （視覺效果）** 大小。  視覺效果 GPU 適用于這種類型的類別，因為學生會與 SolidWorks 3D 電腦輔助設計（CAD）環境互動，以建立穩固物件的模型並將其視覺化。

| 大小 | 核心 | RAM | 描述 | 
| ---- | ----- | --- | ----------- | 
| 小型 GPU (視覺化) | -&nbsp;6個 &nbsp; 核心<br>-&nbsp;56 &nbsp; GB 的 &nbsp; RAM  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 此大小最適合用於使用 OpenGL 和 DirectX 這類架構的遠端視覺效果、串流、遊戲和編碼。 |
| 中型 GPU (視覺化) | -&nbsp;12個 &nbsp; 核心<br>-&nbsp;112 &nbsp; GB 的 &nbsp; RAM  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最適合用於使用 OpenGL 和 DirectX 這類架構的遠端視覺效果、串流、遊戲和編碼。 |

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>確定已安裝適當的 GPU 驅動程式
若要利用實驗室 Vm 的 GPU 功能，請確定已安裝適當的 GPU 驅動程式。  在 [實驗室建立嚮導] 中，當您選取 GPU VM 大小時，您可以選取 [**安裝 gpu 驅動程式**] 選項。  

![「新實驗室」的螢幕擷取畫面，其中顯示 [安裝 GPU 驅動程式] 選項](./media/how-to-setup-gpu/lab-gpu-drivers.png)

如上圖所示，預設會啟用此選項，以確保安裝*最新*的驅動程式適用于您所選取的 GPU 和映射類型。
- 當您選取*計算*GPU 大小時，您的實驗室 vm 是由[NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU 提供技術支援。  在此情況下，會安裝最新的[計算整合裝置架構（CUDA）](https://www.nvidia.com/object/io_69526.html)驅動程式，以啟用高效能運算。
- 當您選取*視覺效果*GPU 大小時，您的實驗室 vm 是由[NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和[方格技術](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)提供技術支援。  在此情況下，會安裝最新的方格驅動程式，這可讓您使用需要大量圖形的應用程式。

### <a name="install-the-drivers-manually"></a>手動安裝驅動程式
您可能需要安裝最新版本以外的驅動程式版本。  本節說明如何根據您使用的是*計算*gpu 或*視覺效果*gpu，手動安裝適當的驅動程式。

#### <a name="install-the-compute-gpu-drivers"></a>安裝計算 GPU 驅動程式

若要手動安裝計算 GPU 大小的驅動程式，請執行下列動作：

1. 在 [實驗室建立嚮導] 中，當您[建立實驗室](./how-to-manage-classroom-labs.md)時，請停用 [**安裝 GPU 驅動程式**] 設定。

1. 建立實驗室之後，請連線至範本 VM 以安裝適當的驅動程式。

   ![[NVIDIA 驅動程式下載] 頁面的螢幕擷取畫面](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. 在瀏覽器中，移至 [ [NVIDIA 驅動程式下載] 頁面](https://www.nvidia.com/Download/index.aspx)。  
   b. 將**產品類型**設定為**Tesla**。  
   c. 將 [**產品系列**] 設定為**K 系列**。  
   d. 根據您在建立實驗室時選取的基底映射類型來設定**作業系統**。  
   e. 將**CUDA 工具**組設定為您所需的 CUDA 驅動程式版本。  
   f. 選取 [**搜尋**] 以尋找您的驅動程式。  
   如 選取 [**下載**] 以下載安裝程式。  
   h. 執行安裝程式，以便將驅動程式安裝在範本 VM 上。  
1. 遵循[驗證已安裝的驅動程式](how-to-setup-lab-gpu.md#validate-the-installed-drivers)一節中的指示，確認驅動程式已正確安裝。 
1. 在您安裝了類別所需的驅動程式和其他軟體之後，請選取 [**發佈**] 來建立您的學生 vm。

> [!NOTE]
> 如果您使用的是 Linux 映射，在下載安裝程式之後，請遵循在[Linux 上安裝 CUDA 驅動程式](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms)中的指示來安裝驅動程式。

#### <a name="install-the-visualization-gpu-drivers"></a>安裝視覺效果 GPU 驅動程式

若要手動安裝視覺效果 GPU 大小的驅動程式，請執行下列動作：

1. 在 [實驗室建立嚮導] 中，當您[建立實驗室](./how-to-manage-classroom-labs.md)時，請停用 [**安裝 GPU 驅動程式**] 設定。
1. 建立實驗室之後，請連線至範本 VM 以安裝適當的驅動程式。
1. 依照您作業系統的指示，在範本 VM 上安裝 Microsoft 所提供的格線驅動程式：
   -  [Windows NVIDIA GRID 驅動程式](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID 驅動程式](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. 重新開機範本 VM。
1. 遵循[驗證已安裝的驅動程式](how-to-setup-lab-gpu.md#validate-the-installed-drivers)一節中的指示，確認驅動程式已正確安裝。
1. 在您安裝了類別所需的驅動程式和其他軟體之後，請選取 [**發佈**] 來建立您的學生 vm。

### <a name="validate-the-installed-drivers"></a>驗證已安裝的驅動程式
本節說明如何驗證您的 GPU 驅動程式已正確安裝。

#### <a name="windows-images"></a>Windows 映像
1.  依照在執行[Windows 的 N 系列 vm 上安裝 NVIDIA GPU 驅動](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)程式的「確認驅動程式安裝」一節中的指示操作。
1.  如果您使用的是*視覺效果*GPU，您也可以：
    - 在 NVIDIA 控制台中，查看並調整您的 GPU 設定。 若要這麼做，請在**Windows [控制台**] 中，選取 [**硬體**]，然後選取 [ **NVIDIA 控制台**]。

      ![Windows 控制台的螢幕擷取畫面，其中顯示 NVIDIA 控制台連結](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - 使用 [**工作管理員**] 來查看您的 GPU 效能。  若要這麼做，請選取 [**效能**] 索引標籤，然後選取 [ **GPU** ] 選項。

       ![顯示 [工作管理員 GPU 效能] 索引標籤的螢幕擷取畫面](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > NVIDIA 控制台設定只能針對*視覺效果*gpu 進行存取。  如果您嘗試開啟計算 GPU 的 NVIDIA 控制台，您會收到下列錯誤：「無法使用 NVIDIA 顯示設定。  您目前未使用連接到 NVIDIA GPU 的顯示器。」  同樣地，[工作管理員] 中的 GPU 效能資訊只會提供給視覺效果 Gpu。

#### <a name="linux-images"></a>Linux 映像
依照在執行[Linux 的 N 系列 vm 上安裝 NVIDIA GPU 驅動](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation)程式的「確認驅動程式安裝」一節中的指示操作。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [建立和管理教室實驗室](how-to-manage-classroom-labs.md)
- [SolidWorks 電腦輔助設計（CAD）類別類型](class-type-solidworks.md)
- [MATLAB （矩陣實驗室）類別類型](class-type-matlab.md)



