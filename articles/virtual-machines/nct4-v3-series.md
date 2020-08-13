---
title: NCas T4 v3 系列
description: NCas T4 v3 系列 Vm 的規格。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: d07da12ecef7dfc6cf1a6df67f6beae01c4573d9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168111"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 系列 

NCasT4_v3 系列的虛擬機器由[Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 和 AMD EPYC 7V12 提供技術支援， (羅馬) cpu。 Vm 最多可以有4個 NVIDIA T4 Gpu，每個都有 16 GB 的記憶體，最多64個非多執行緒 AMD EPYC 7V12 (羅馬) 處理器核心，以及 440 GiB 的系統記憶體。 這些虛擬機器適合用來執行使用 CUDA、TensorFlow、Pytorch、Caffe 和其他架構的 ML 和 AI 工作負載，或使用 NVIDIA GRID 技術的圖形工作負載。 NCasT4_v3 系列是執行推斷工作負載的理想選擇。

<br>

ACU：230-260

進階儲存體：支援

進階儲存體快取：支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NV8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NV16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NV64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

若要利用執行 Windows 或 Linux 的 Azure NCasT4_v3 系列 Vm 的 GPU 功能，必須安裝 Nvidia GPU 驅動程式。

若要手動安裝 Nvidia GPU 驅動程式，請參閱適用于[Windows 的 N 系列 GPU 驅動程式設定](./windows/n-series-driver-setup.md)，以瞭解支援的作業系統、驅動程式、安裝和驗證步驟。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
