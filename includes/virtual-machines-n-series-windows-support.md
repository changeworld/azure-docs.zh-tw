---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135118"
---
## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) 驅動程式

只有下表所列的作業系統才支援 NC、NCv2、NCv3、ND 及 NDv2 系列 VM (針對 NV 系列為選擇性) 的 NVIDIA Tesla (CUDA) 驅動程式。 以下是本文章發行時的最新驅動程式下載連結。 如需最新的驅動程式，請瀏覽 [NVIDIA](https://www.nvidia.com/) 網站。

> [!TIP]
> 在 Windows Server VM 上手動安裝 CUDA 驅動程式的替代方案，就是部署 Azure [資料科學虛擬機器](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 適用於 Windows Server 2016 的 DSVM 版本會預先安裝 NVIDIA CUDA 驅動程式、CUDA 深度類神經網路程式庫和其他工具。


| OS | 驅動程式 |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驅動程式

Microsoft 為用作虛擬工作站或虛擬應用程式的 NV 和 NVv3 系列 VM 重新分發 NVIDIA GRID 驅動程式安裝程式。 僅在 Azure NV 系列 VM 上安裝這些 GRID 驅動程式，僅在下表中列出的作業系統上安裝這些驅動程式。 這些驅動程式包含在 Azure 的 GRID 虛擬 GPU 軟體的授權中。 您無需設置 NVIDIA vGPU 軟體許可證伺服器。

請注意，Nvidia 擴展將始終安裝最新的驅動程式。 我們在此處為依賴舊版本的客戶提供指向早期版本的連結。

對於 Windows 伺服器 2019、Windows 伺服器 2016 和 Windows 10（最多構建 1909）：
- [網格 10.1 （442.06）](https://go.microsoft.com/fwlink/?linkid=874181) （.exe）
- [網格 10.0 （441.66）](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) （.exe） 

對於 Windows 伺服器 2012 R2、Windows 伺服器 2008 R2、Windows 8 和 Windows 7： 
- [網格 10.1 （442.06）](https://go.microsoft.com/fwlink/?linkid=874184) （.exe）
- [網格 10.0 （441.66）](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) （.exe）  
