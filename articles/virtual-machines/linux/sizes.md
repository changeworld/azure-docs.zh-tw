---
title: Azure 中的 Linux VM 大小
description: 列出 Azure 中 Linux 虛擬機器的不同可用大小。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: jonbeck
ms.openlocfilehash: c16ff8fed19340811578ccb39cbac7242b7c5861
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905735"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Azure 中的 Linux 虛擬機器大小
本文說明可用於執行 Linux 應用程式與工作負載之 Azure 虛擬機器的可用大小及選項。 同時也提供當您規劃使用這些資源時所需注意的部署考量。 本文也適用於 [Windows 虛擬機器](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


| 類型                     | 大小           |    說明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [一般用途](sizes-general.md)          | B、Dsv3、Dv3、Dasv4、Dav4、DSv2、Dv2、Av2、DC  | CPU/記憶體比例平均。 很適合測試與開發、小型至中型資料庫及低至中流量網頁伺服器。 |
| [計算最佳化](sizes-compute.md)        | Fsv2           | CPU/記憶體比例高。 適用於中流量網頁伺服器、網路設備、批次處理及應用程式伺服器。        |
| [記憶體最佳化](sizes-memory.md)         | Esv3、Ev3、Easv4、Eav4、Mv2、M、DSv2、Dv2  | 記憶體與 CPU 的比例高。 適用於關聯式資料庫伺服器、中型至大型快取及記憶體內部分析。                 |
| [儲存體最佳化](sizes-storage.md)        | Lsv2                | 較高的磁碟輸送量和 IO 適用於巨量資料、SQL、NoSQL 資料庫、資料倉儲和大型交易資料庫。  |
| [GPU](sizes-gpu.md)            | NC、NCv2、NCv3、ND、NDv2 （預覽）、NV、NVv3  | 特製化的虛擬機器，其目標是大量的圖形轉譯和視訊編輯，以及搭配深入學習運作的模型訓練和推斷 (ND)。 有單一或多個 GPU 可供使用。       |
| [高效能計算](sizes-hpc.md) | HB、HC、H | 速度最快、功能最強的 CPU 虛擬機器，搭載選配高輸送量網路介面 (RDMA)。 |

<br>

- 如需各式大小的定價，請參閱 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。 
- 如需了解 Azure 區域中的 VM 大小可用性，請參閱 [依區域提供的產品](https://azure.microsoft.com/regions/services/)。
- 若要查看 Azure VM 的一般限制，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。


## <a name="rest-api"></a>REST API

如需使用 REST API 來查詢 VM 大小的資訊，請參閱下列各項：

- [列出調整大小的可用虛擬機器大小](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [列出訂用帳戶的可用虛擬機器大小](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [列出可用性設定組中可用的虛擬機器大小](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。

## <a name="benchmark-scores"></a>基準測試分數

深入了解如何使用 [CoreMark 基準分數](compute-benchmark-scores.md)來計算 Linux VM 的效能。

## <a name="next-steps"></a>後續步驟

深入了解可用的不同 VM 大小：
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- 查看[前一代](sizes-previous-gen.md)頁面，以取得基本和標準、高效能的 A8-A11、D/DSv1 （D1-4 和 D11-14 v1）、F/Fsv1、G/GS、Lsv1 和 NVv2 系列。



