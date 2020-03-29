---
title: Azure VM 大小 - GPU |微軟文檔
description: 列出 Azure 中可用於虛擬機器的不同 GPU 優化大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913577"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 最佳化的虛擬機器大小

GPU 最佳化的 VM 大小，為搭配單一或多個 NVIDIA GPU 提供的特製化虛擬機器。 這些大小是專門針對計算密集型、圖形密集型及視覺效果的工作負載所設計。 本文章提供有關 GPU、vCPU、資料磁碟及 NIC 之數量和類型的資訊。 另說明此群組中每個大小的輸送量和網路頻寬。

- [NC 系列](nc-series.md)[、NCv2 系列](ncv2-series.md)[、NCv3 系列](ncv3-series.md)尺寸針對計算密集型和網路密集型應用和演算法進行了優化。 例如 CUDA 和基於 OpenCL 的應用程式和模擬、AI 和深度學習。 NCv3 系列著重於高效能運算工作負載，採用 NVIDIA 的 Tesla V100 GPU。 NC 系列使用英特爾至強 E5-2690 v3 2.60GHz v3 （哈斯韋爾） 處理器，NCv2 系列和 NCv3 系列 VM 使用英特爾至強 E5-2690 v4 （Broadwell） 處理器。

- [ND系列](nd-series.md)和[NDv2系列](ndv2-series.md)尺寸側重于深度學習的訓練和推理方案。 他們使用NVIDIA特斯拉P40 GPU和英特爾至強E5-2690 v4（布羅德韋爾）處理器。 NDv2 系列使用英特爾 Xeon 白金 8168（天湖）處理器。

- [NV 系列](nv-series.md)和[NVv3 系列](nvv3-series.md)尺寸經過優化設計，適用于使用 OpenGL 和 DirectX 等框架的遠端視覺化、流式處理、遊戲、編碼和 VDI 方案。 這些 VM 是由 NVIDIA Tesla M60 GPU 提供支援。

- [NVv4 系列](nvv4-series.md)針對 VDI 和遠端視覺化優化和設計的 VM 大小。 NVv4 具有分區 GPU，可為需要較小 GPU 資源的工作負載提供合適的大小。 這些 VM 由 AMD Radeon 本能 MI25 GPU 支援。 NVv4 VM 目前僅支援 Windows 客體作業系統。

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

要利用 Azure N 系列 VM 的 GPU 功能，必須安裝 NVIDIA GPU 驅動程式。

[NVIDIA GPU 驅動程式擴充功能](/azure/virtual-machines/extensions/hpccompute-gpu-windows)會在 N 系列 VM 上安裝適當的 NVIDIA CUDA 或 GRID 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需支援的作業系統和部署步驟，請參閱 [NVIDIA GPU 驅動程式擴充功能文件](/azure/virtual-machines/extensions/hpccompute-gpu-windows)。 如需有關虛擬機器擴充功能的一般資訊，請參閱 [Azure 虛擬機器擴充功能和功能](/azure/virtual-machines/extensions/overview)。

如果選擇手動安裝 NVIDIA GPU 驅動程式，請參閱適用于 Windows 的[N 系列 GPU 驅動程式設置](/azure/virtual-machines/windows/n-series-driver-setup)，或針對 Linux 的 N 系列 GPU[驅動程式設置](/azure/virtual-machines/linux/n-series-driver-setup)，瞭解支援的作業系統、驅動程式、安裝和驗證步驟。

## <a name="deployment-considerations"></a>部署考量因素

- 如需了解 N 系列 VM 的可用性，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。

- N 系列 VM 只能部署在 Resource Manager 部署模型。

- N 系列 VM 對其磁碟支援的 Azure 儲存體類型有所不同。 NC 和 NV VM 只支援標準磁碟儲存體 (HDD) 所支持的 VM 磁碟。 NCv2、NCv3、ND、NDv2 和 NVv2 只支援進階磁碟儲存體 (SSD) 所支持的 VM 磁碟。

- 如果您想要部署不只一些 N 系列 VM ，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

- 您可能需要增加 Azure 訂用帳戶的核心配額 (依地區)，以及增加 NC、NCv2、NCv3、ND、NDv2、NV 或 NVv2 核心的個別配額。 要請求增加配額，[請免費打開線上客戶支援請求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 預設限制會視您的訂用帳戶類別而有所不同。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [高效能計算](sizes-hpc.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
