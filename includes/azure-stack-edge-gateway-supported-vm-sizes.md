---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: 9ea5fb26a52c967c5296f1a83976e748c86c9e18
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763767"
---
VM 大小會決定可供 VM 使用的計算資源 (例如 CPU、GPU 和記憶體) 數量。 虛擬機器必須適用於工作負載的 VM 大小來建立。 即使所有電腦都在相同的硬體上執行，電腦大小還是會有不同的磁片存取限制，這可協助您管理整個 Vm 的整體磁片存取。 如果工作負載增加，可以調整現有虛擬機器的大小。

支援在 Azure Stack Edge 裝置上建立下列 Vm。

### <a name="dv2-series"></a>Dv2 系列
|大小     |vCPU     |記憶體 (GiB) | 資源磁片大小 (GiB)   | OS 磁片大小 (GiB)  | 最大資料磁碟 | 最大 NIC |
|-------------------|----|----|-----|----|------|------------|
|**Standard_D1_v2** |1   |3.5 |50   |1000| 4    |2 |
|**Standard_D2_v2** |2   |7   |100  |1000| 8    |4 |
|**Standard_D3_v2** |4   |14  |200  |1000| 16  |4 |
|**Standard_D4_v2** |8   |28  |400  |1000| 32  |8 |
|**Standard_D5_v2** |16  |56  |800  |1000| 64  |8 |
|**Standard_D11_v2** |2   |14  |100  |1000| 8     |2 |
|**Standard_D12_v2** |4   |28  |200  |1000| 16   |4 |
|**Standard_D13_v2** |8   |56  |400  |1000| 32  |8 |

### <a name="dsv2-series"></a>DSv2 系列
|大小     |vCPU     |記憶體 (GiB) |  資源磁片大小 (GiB)   | OS 磁片大小 (GiB)  | 最大資料磁碟| 最大 NIC |
|--------------------|----|----|----|-----|------|-------------|
|**Standard_DS1_v2** |1   |3.5 |7  |4000  |1000 |4  |2 |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |4 |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |4 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |8 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |8 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |2 |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |4 |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |8 |


如需詳細資訊，請移至 [一般用途 VM 大小的 Dv2 系列](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series)。

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 系列 (預覽) 

您裝置上的 GPU Vm 支援這些大小，並且已針對計算密集型 GPU 加速應用程式進行優化。 此系列著重于具有 Nvidia Tesla T4 GPU 的推斷工作負載。 

|大小     |vCPU     |記憶體 (GiB) | 資源磁片大小 (GiB)   |OS 磁片大小 (GiB) | GPU | GPU 記憶體 (GiB)  | 最大 NIC |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

如需詳細資訊，請移至 [NCasT4_v3 系列的 GPU 優化 VM 大小](../articles/virtual-machines/nct4-v3-series.md)。

### <a name="f-series"></a>F 系列

這些系列已針對計算工作負載優化，並在 Intel 的最高處理器上執行。 

| 大小 | vCPU | 記憶體：GiB |資源磁片大小 (GiB)  |OS 磁片大小 (GiB) |  最大資料磁碟 | 最大 NIC |
|---|---|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      |1000| 4  |  2 |
| Standard_F2 | 2  | 4 |32      |1000| 8  |  4 |
| Standard_F4  | 4  | 8 |64   |1000| 16 |  4 |
| Standard_F8 | 8 | 16  |132    |1000| 32 |  8 |
| Standard_F16 | 16 | 32  |262   |1000| 64 |  8 |
| Standard_F1s | 1 | 2  | 4  |1000| 4 | 1 |
| Standard_F2s | 2 | 4 |8   |1000| 8 | 4 |
| Standard_F4s | 4 | 8 |16 |1000| 16 |  4 |
| Standard_F8s | 8 | 16 |32 |1000| 32 |  8 |
| Standard_F16s | 16 | 32 |64 |1000| 64 |  8 |

如需詳細資訊，請移至 [Fsv2 系列計算優化的 VM 大小](../articles/virtual-machines/fsv2-series.md)。

