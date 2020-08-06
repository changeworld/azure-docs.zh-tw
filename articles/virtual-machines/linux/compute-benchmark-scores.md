---
title: Azure Linux Vm 的計算基準測試分數
description: 在執行 Linux 的 Azure VM 之間比較 CoreMark 計算基準測試分數。
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 04/29/2020
ms.reviewer: davberg
ms.openlocfilehash: efbd90eee8d072c1979565209c8c04a4ee5c55f9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830524"
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Linux VM 的計算基準測試分數
下列 CoreMark 基準測試分數顯示執行 Ubuntu 的 Azure 高效能 VM 產品陣容的計算效能。 也有 [Windows VM](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的計算基準測試分數。

## <a name="standard_das_v4"></a>Standard_Das_v4
 (12/11/2019 2:28:52 AM pbi 5851281) 

|VM 大小| CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
|---| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|Standard_D2as_v4| AMD EPYC 7452 32-核心處理器 | 2 | 1 | 7.8 | 29726 | 693 | 2.33% | 42 |
|Standard_D4as_v4| AMD EPYC 7452 32-核心處理器 | 4 | 1 | 15.7 | 59224 | 1595 | 2.69% | 42 |
|Standard_D8as_v4| AMD EPYC 7452 32-核心處理器 | 8 | 1 | 31.4 | 116412 | 3613 | 3.10% | 42 |
|Standard_D16as_v4| AMD EPYC 7452 32-核心處理器 | 16 | 2 | 62.9 | 229489 | 7209 | 3.14% | 35 |
|Standard_D32as_v4| AMD EPYC 7452 32-核心處理器 | 32 | 4 | 125.9 | 461916 | 6746 | 1.46% | 35 |

## <a name="standard_da_v4"></a>Standard_Da_v4
 (12/12/2019 12:01:48 AM pbi 5851281) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D2a_v4 | AMD EPYC 7452 32-核心處理器 | 2 | 1 | 7.8 | 30023 | 333 | 1.11% | 35 |
| Standard_D4a_v4 | AMD EPYC 7452 32-核心處理器 | 4 | 1 | 15.7 | 59685 | 1141 | 1.91% | 77 |
| Standard_D8a_v4 | AMD EPYC 7452 32-核心處理器 | 8 | 1 | 31.4 | 118346 | 1130 | 0.95% | 42 |
| Standard_D16a_v4 | AMD EPYC 7452 32-核心處理器 | 16 | 2 | 62.9 | 231131 | 3830 | 1.66% | 35 |
| Standard_D32a_v4 | AMD EPYC 7452 32-核心處理器 | 32 | 4 | 125.9 | 457266 | 10208 | 2.23% | 35 |
| Standard_D48a_v4 | AMD EPYC 7452 32-核心處理器 | 48 | 6 | 188.9 | 664078 | 17241 | 2.60% | 35 |
| Standard_D64a_v4 | AMD EPYC 7452 32-核心處理器 | 64 | 8 | 251.9 | 863911 | 24818 | 2.87% | 35 |
| Standard_D96a_v4 | AMD EPYC 7452 32-核心處理器 | 96 | 12 | 377.9 | 1290455 | 13640 | 1.06% | 35 |

## <a name="standard_eas_v4"></a>Standard_Eas_v4
 (12/11/2019 2:28:50 AM pbi 5851281) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2as_v4 | AMD EPYC 7452 32-核心處理器 | 2 | 1 | 15.7 | 29217 | 654 | 2.24% | 42 |
| Standard_E4as_v4 | AMD EPYC 7452 32-核心處理器 | 4 | 1 | 31.4 | 58356 | 480 | 0.82% | 42 |
| Standard_E8as_v4 | AMD EPYC 7452 32-核心處理器 | 8 | 1 | 62.9 | 115943 | 3526 | 3.04% | 35 |
| Standard_E16as_v4 | AMD EPYC 7452 32-核心處理器 | 16 | 2 | 125.9 | 227383 | 5619 | 2.47% | 35 |
| Standard_E32as_v4 | AMD EPYC 7452 32-核心處理器 | 32 | 4 | 251.9 | 454609 | 12746 | 2.80% | 35 |
| Standard_E48as_v4 | AMD EPYC 7452 32-核心處理器 | 48 | 6 | 377.9 | 682769 | 9257 | 1.36% | 35 |
| Standard_E64as_v4 | AMD EPYC 7452 32-核心處理器 | 64 | 8 | 503.9 | 881311 | 28357 | 3.22% | 35 |
| Standard_E96as_v4 | AMD EPYC 7452 32-核心處理器 | 96 | 12 | 661.4 | 1299233 | 14997 | 1.15% | 70 |

##  <a name="standard_ea_v4"></a>Standard_Ea_v4
 (12/11/2019 2:29:06 AM pbi 5851281) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2a_v4 | AMD EPYC 7452 32-核心處理器 | 2 | 1 | 15.7 | 29561 | 422 | 1.43% | 42 |
| Standard_E4a_v4 | AMD EPYC 7452 32-核心處理器 | 4 | 1 | 31.4 | 58303 | 1280 | 2.20% | 42 |
| Standard_E8a_v4 | AMD EPYC 7452 32-核心處理器 | 8 | 1 | 62.9 | 114650 | 2726 | 2.38% | 42 |
| Standard_E16a_v4 | AMD EPYC 7452 32-核心處理器 | 16 | 2 | 125.9 | 226947 | 4661 | 2.05% | 35 |
| Standard_E32a_v4 | AMD EPYC 7452 32-核心處理器 | 32 | 4 | 251.9 | 453666 | 10058 | 2.22% | 42 |
| Standard_E48a_v4 | AMD EPYC 7452 32-核心處理器 | 48 | 6 | 377.9 | 665200 | 18714 | 2.81% | 35 |
| Standard_E64a_v4 | AMD EPYC 7452 32-核心處理器 | 64 | 8 | 503.9 | 894718 | 25214 | 2.82% | 35 |
| Standard_E96a_v4 | AMD EPYC 7452 32-核心處理器 | 96 | 12 | 661.4 | 1298074 | 15948 | 1.23% | 35 |

## <a name="av2---general-compute"></a>Av2 - 一般計算
 (3/15/2019 12:06:55 AM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 1 | 1 | 1.9 | 6483 | 120 | 1.85% | 273 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 6059 | 208 | 3.43% | 217 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 6367 | 453 | 7.12% | 217 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 3.9 | 13161 | 194 | 1.48% | 266 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 12067 | 401 | 3.32% | 203 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 12527 | 797 | 6.37% | 238 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 15.7 | 13167 | 179 | 1.36% | 273 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 15.7 | 12133 | 336 | 2.77% | 210 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 15.7 | 12401 | 656 | 5.29% | 224 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 7.8 | 26307 | 231 | 0.88% | 231 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 7.8 | 24552 | 720 | 2.93% | 224 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 7.8 | 24963 | 1,625 | 6.51% | 252 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 31.4 | 26,238 | 292 | 1.11% | 259 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 31.4 | 24250 | 491 | 2.02% | 189 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 31.4 | 24725 | 1553 | 6.28% | 259 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 15.7 | 53237 | 687 | 1.29% | 266 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 15.7 | 49655 | 585 | 1.18% | 147 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 15.7 | 49005 | 2162 | 4.41% | 294 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 2 | 62.9 | 52627 | 902 | 1.71% | 266 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 62.9 | 49838 | 633 | 1.27% | 182 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 62.9 | 49123 | 2483 | 5.05% | 259 |

> [!NOTE]
> Av2 系列 Vm 可以部署在各種不同的硬體類型和處理器上， (如上) 所示。 Av2 系列 Vm 的 CPU 效能和記憶體設定最適合用於進入層級的工作負載，例如開發和測試。 大小會進行節流，為執行中的實例提供相對一致的處理器效能，而不論其部署所在的硬體為何;不過，利用特定較新處理器優化的軟體，可能會在處理器類型上看到更顯著的變化。

## <a name="b---burstable"></a>B-高載
 (3/15/2019 12:27:08 AM pbi 3897709)  (更新 6/14/2019 7:09:29 AM pbi 4777081) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_B1ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 13593 | 307 | 2.26% | 28 |
| Standard_B1ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 14069 | 495 | 3.52% | 672 |
| Standard_B1s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 0.9 | 13736 | 211 | 1.54% | 28 |
| Standard_B1s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 0.9 | 13965 | 457 | 3.27% | 672 |
| Standard_B2ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 7.8 | 27361 | 1110 | 4.06% | 28 |
| Standard_B2ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 7.8 | 27432 | 771 | 2.81% | 672 |
| Standard_B2s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 27488 | 822 | 2.99% | 28 |
| Standard_B2s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 27548 | 864 | 3.14% | 672 |
| Standard_B4ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 15.7 | 54951 | 1868 | 3.40% | 28 |
| Standard_B4ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 15.7 | 54051 | 1260 | 2.33% | 672 |
| Standard_B8ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 31.4 | 111929 | 1562 | 1.40% | 35 |
| Standard_B8ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 31.4 | 109537 | 1354 | 1.24% | 665 |
| Standard_B12ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 12 | 1 | 47.1 | 170777 | 3421 | 2.00% | 70 |
| Standard_B12ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 12 | 1 | 47.1 | 166676 | 1368 | 0.82% | 70 |
| Standard_B16ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 62.9 | 208373 | 30383 | 14.58% | 63 |
| Standard_B16ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 62.9 | 223203 | 1232 | 0.55% | 70 |
| Standard_B20ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 1 | 78.6 | 269561 | 25095 | 9.31% | 77 |
| Standard_B20ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 20 | 1 | 78.6 | 274007 | 3669 | 1.34% | 70 |


> [!NOTE]
> B 系列 Vm 適用于具有高載效能需求的工作負載。 VM 實例使用低於其基準時，會累積點數。 當 VM 累積點數時，VM 可以使用最多100% 來達到較短的 CPU 高載需求，高載高於基準。 高載時間取決於可用的點數，也就是 VM 大小和時間的功能。  
>
> CoreMark 是短期執行的測試，通常會在可用的高載點數內完成。  因此，上述數位通常代表 VM 的高載效能，反映在 B 系列上 (一般的 short、暴增、工作負載，) 效能通常會顯示。

## <a name="dsv3---general-compute--premium-storage"></a>DSv3 - 一般計算 + 進階儲存體
 (3/12/2019 6:52:03 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 7.8 | 20153 | 838 | 4.16% | 147 |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 7.8 | 20903 | 1324 | 6.33% | 553 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 15.7 | 39502 | 1257 | 3.18% | 189 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 15.7 | 40547 | 1,935 | 4.77% | 511 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 31.4 | 80191 | 1054 | 1.31% | 168 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 31.4 | 79884 | 3073 | 3.85% | 532 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 62.9 | 160319 | 1213 | 0.76% | 105 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 62.9 | 156325 | 2176 | 1.39% | 588 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 32 | 2 | 125.9 | 315457 | 2647 | 0.84% | 105 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 1 | 125.9 | 312058 | 1661 | 0.53% | 595 |
| Standard_D64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 251.9 | 627378 | 4447 | 0.71% | 700 |

## <a name="dv3---general-compute"></a>Dv3 - 一般計算
 (3/12/2019 6:54:27 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 7.8 | 20359 | 799 | 3.93% | 154 |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 7.8 | 20737 | 1,422 | 6.86% | 546 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 15.7 | 40095 | 1501 | 3.74% | 147 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 15.7 | 41147 | 2706 | 6.58% | 546 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 31.4 | 80383 | 1486 | 1.85% | 133 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 31.4 | 80511 | 3916 | 4.86% | 560 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 62.9 | 160932 | 2,200 | 1.37% | 140 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 62.9 | 158679 | 4550 | 2.87% | 560 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 32 | 2 | 125.9 | 314208 | 4250 | 1.35% | 189 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 1 | 125.9 | 312472 | 3173 | 1.02% | 511 |
| Standard_D64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 251.9 | 627470 | 9651 | 1.54% | 700 |

## <a name="dsv2---storage-optimized"></a>DSv2 - 儲存體最佳化
 (3/15/2019 12:53:13 AM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 3.4 | 14642 | 600 | 4.10% | 259 |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 3.4 | 14808 | 904 | 6.10% | 434 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 6.8 | 28654 | 877 | 3.06% | 301 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 6.8 | 29089 | 1421 | 4.89% | 406 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 13.7 | 57255 | 1633 | 2.85% | 238 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 13.7 | 57255 | 2265 | 3.96% | 462 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 27.5 | 116681 | 1097 | 0.94% | 231 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 27.5 | 112512 | 1261 | 1.12% | 462 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 55.0 | 225661 | 2370 | 1.05% | 189 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 55.0 | 229145 | 2878 | 1.26% | 21 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 55.0 | 226818 | 1797 | 0.79% | 497 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 13.7 | 28571 | 920 | 3.22% | 238 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 13.7 | 29049 | 1614 | 5.56% | 469 |
| Standard_DS11-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 13.7 | 14594 | 617 | 4.23% | 287 |
| Standard_DS11-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 13.7 | 14951 | 852 | 5.70% | 413 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 27.5 | 57503 | 1398 | 2.43% | 217 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 27.5 | 57082 | 2372 | 4.16% | 483 |
| Standard_DS12-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 27.5 | 14698 | 564 | 3.84% | 238 |
| Standard_DS12-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 27.5 | 15127 | 941 | 6.22% | 462 |
| Standard_DS12-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 27.5 | 28711 | 981 | 3.42% | 259 |
| Standard_DS12-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 27.5 | 29305 | 1241 | 4.24% | 441 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 55.0 | 116875 | 1286 | 1.10% | 203 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 55.0 | 112318 | 1356 | 1.21% | 504 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 55.0 | 29105 | 1154 | 3.97% | 224 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 55.0 | 29936 | 1720 | 5.75% | 483 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 55.0 | 56992 | 1814 | 3.18% | 280 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 55.0 | 57781 | 2122 | 3.67% | 427 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 110.2 | 224149 | 3450 | 1.54% | 196 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 110.2 | 227108 | 1,267 | 0.56% | 504 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 2 | 110.2 | 56211 | 2154 | 3.83% | 189 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 110.2 | 59651 | 2560 | 4.29% | 518 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 2 | 110.2 | 112280 | 4430 | 3.95% | 196 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 110.2 | 113375 | 1442 | 1.27% | 511 |
| Standard_DS15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 2 | 137.7 | 279359 | 4032 | 1.44% | 665 |

## <a name="dv2---general-compute"></a>Dv2 - 一般計算
 (3/12/2019 6:53:48 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 3.4 | 14730 | 663 | 4.50% | 385 |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 3.4 | 15057 | 1319 | 8.76% | 322 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 6.8 | 29395 | 1073 | 3.65% | 329 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 6.8 | 29564 | 2145 | 7.26% | 378 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 13.7 | 58150 | 1340 | 2.30% | 343 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 13.7 | 57820 | 2944 | 5.09% | 364 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 27.5 | 117448 | 1612 | 1.37% | 308 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 27.5 | 114082 | 3369 | 2.95% | 399 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 55.0 | 226370 | 4722 | 2.09% | 147 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 55.0 | 225035 | 5026 | 2.23% | 119 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 55.0 | 227883 | 3259 | 1.43% | 441 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 13.7 | 29260 | 1012 | 3.46% | 308 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 13.7 | 29306 | 1763 | 6.02% | 399 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 27.5 | 58322 | 1391 | 2.39% | 329 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 27.5 | 57999 | 3533 | 6.09% | 371 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 55.0 | 117218 | 1514 | 1.29% | 329 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 55.0 | 114344 | 3307 | 2.89% | 378 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 110.2 | 224348 | 5477 | 2.44% | 280 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 110.2 | 228221 | 2733 | 1.20% | 427 |
| Standard_D15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 2 | 137.7 | 281494 | 7976 | 2.83% | 672 |

## <a name="esv3---memory-optimized--premium-storage"></a>Esv3 - 記憶體最佳化 + 進階儲存體
 (3/12/2019 7:17:33 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 15.7 | 20957 | 1,200 | 5.73% | 672 |
| Standard_E4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 31.4 | 40420 | 1993 | 4.93% | 672 |
| Standard_E4-2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 31.4 | 20774 | 1133 | 5.45% | 672 |
| Standard_E8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 62.9 | 80153 | 3308 | 4.13% | 665 |
| Standard_E8-2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 62.9 | 21178 | 1334 | 6.30% | 665 |
| Standard_E8-4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 62.9 | 40614 | 2216 | 5.46% | 672 |
| Standard_E16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 125.9 | 156137 | 2160 | 1.38% | 672 |
| Standard_E16-4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 125.9 | 41950 | 2309 | 5.50% | 637 |
| Standard_E16-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 125.9 | 81196 | 3179 | 3.91% | 658 |
| Standard_E20s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 20 | 1 | 157.4 | 196619 | 1325 | 0.67% | 672 |
| Standard_E32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 251.9 | 304707 | 5719 | 1.88% | 672 |
| Standard_E32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 2 | 251.9 | 83576 | 3693 | 4.42% | 672 |
| Standard_E32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 2 | 251.9 | 158023 | 4317 | 2.73% | 672 |
| Standard_E64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 628540 | 3982 | 0.63% | 49 |
| Standard_E64-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 2 | 425.2 | 169611 | 3265 | 1.92% | 42 |
| Standard_E64-32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 425.2 | 307584 | 3569 | 1.16% | 56 |

## <a name="eisv3---memory-opt--premium-storage-isolated"></a>Eisv3-Memory Opt + 進階儲存體 (隔離) 
 (4/11/2019 10:07:29 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E64is_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 627745 | 4062 | 0.65% | 196 |

## <a name="ev3---memory-optimized"></a>Ev3 - 記憶體最佳化
 (3/12/2019 6:52:13 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 15.7 | 21171 | 1772 | 8.37% | 693 |
| Standard_E4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 31.4 | 41181 | 3148 | 7.64% | 700 |
| Standard_E8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 62.9 | 81211 | 5055 | 6.22% | 700 |
| Standard_E16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 125.9 | 158152 | 4033 | 2.55% | 700 |
| Standard_E20_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 20 | 1 | 157.4 | 197739 | 2731 | 1.38% | 693 |
| Standard_E32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 251.9 | 307286 | 8353 | 2.72% | 700 |
| Standard_E64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 628451 | 9235 | 1.47% | 707 |

## <a name="eiv3---memory-optimized-isolated"></a>Eiv3-記憶體優化 (隔離的) 
 (3/12/2019 6:57:51 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E64i_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 625855 | 4881 | 0.78% | 7 |
| Standard_E64i_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 629151 | 9756 | 1.55% | 217 |

## <a name="fsv2---compute--storage-optimized"></a>Fsv2 - 計算 + 儲存體最佳化
 (3/12/2019 6:51:35 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_F2s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 2 | 1 | 3.9 | 28219 | 1843 | 6.53% | 700 |
| Standard_F4s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 4 | 1 | 7.8 | 53911 | 1002 | 1.86% | 707 |
| Standard_F8s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 8 | 1 | 15.7 | 106467 | 1101 | 1.03% | 707 |
| Standard_F16s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 16 | 1 | 31.4 | 211311 | 1724 | 0.82% | 707 |
| Standard_F32s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 32 | 1 | 62.9 | 423175 | 4346 | 1.03% | 707 |
| Standard_F64s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 64 | 2 | 125.9 | 829537 | 21574 | 2.60% | 707 |
| Standard_F72s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 72 | 2 | 141.7 | 933800 | 26783 | 2.87% | 707 |

## <a name="fs---compute-and-storage-optimized"></a>Fs - 計算與儲存體最佳化
 (3/15/2019 12:12:51 AM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 14552 | 504 | 3.46% | 350 |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 14784 | 858 | 5.80% | 357 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 28664 | 895 | 3.12% | 245 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 29188 | 1228 | 4.21% | 455 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 7.8 | 57192 | 1700 | 2.97% | 259 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 7.8 | 57412 | 2215 | 3.86% | 448 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 15.7 | 117008 | 1139 | 0.97% | 259 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 15.7 | 112610 | 1595 | 1.42% | 441 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 31.4 | 225444 | 2328 | 1.03% | 210 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 31.4 | 228919 | 3380 | 1.48% | 28 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 31.4 | 227015 | 1543 | 0.68% | 462 |

## <a name="f---compute-optimized"></a>F - 計算最佳化
 (3/12/2019 6:53:59 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 14937 | 593 | 3.97% | 350 |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 15460 | 1326 | 8.58% | 350 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 29324 | 1196 | 4.08% | 343 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 29299 | 1908 | 6.51% | 364 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 7.8 | 58314 | 1245 | 2.14% | 364 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 7.8 | 58280 | 3581 | 6.14% | 336 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 15.7 | 117516 | 1460 | 1.24% | 308 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 15.7 | 114361 | 3868 | 3.38% | 399 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 31.4 | 226487 | 4140 | 1.83% | 154 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 31.4 | 226683 | 4723 | 2.08% | 133 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 31.4 | 228592 | 2371 | 1.04% | 392 |

## <a name="gs---storage-optimized"></a>GS - 儲存體最佳化
 (3/12/2019 10:22:33 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_GS1 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 2 | 1 | 27.5 | 28835 | 2222 | 7.71% | 287 |
| Standard_GS2 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 55.0 | 55568 | 3139 | 5.65% | 287 |
| Standard_GS3 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 110.2 | 106567 | 2188 | 2.05% | 287 |
| Standard_GS4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 220.4 | 210586 | 4130 | 1.96% | 287 |
| Standard_GS4-4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 220.4 | 58598 | 2,670 | 4.56% | 287 |
| Standard_GS4-8 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 220.4 | 108234 | 2392 | 2.21% | 287 |
| Standard_GS5 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 440.9 | 399835 | 8694 | 2.17% | 287 |
| Standard_GS5-8 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 2 | 440.9 | 116643 | 2354 | 2.02% | 287 |
| Standard_GS5-16 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 2 | 440.9 | 210984 | 2995 | 1.42% | 287 |

## <a name="g---compute-optimized"></a>G - 計算最佳化
 (3/12/2019 10:23:51 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_G1 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 2 | 1 | 27.5 | 32808 | 2679 | 8.17% | 287 |
| Standard_G2 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 55.0 | 62907 | 4465 | 7.10% | 287 |
| Standard_G3 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 110.2 | 113471 | 4346 | 3.83% | 287 |
| Standard_G4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 220.4 | 212092 | 2857 | 1.35% | 287 |
| Standard_G5 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 440.9 | 403315 | 6947 | 1.72% | 273 |

## <a name="h---high-performance-compute-hpc"></a>H - 高效能計算 (HPC)
 (3/12/2019 10:50:51 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_H8 | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 8 | 1 | 55.0 | 149859 | 734 | 0.49% | 175 |
| Standard_H8m | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 8 | 1 | 110.2 | 149931 | 657 | 0.44% | 147 |
| Standard_H16 | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 110.2 | 282083 | 6738 | 2.39% | 84 |
| Standard_H16m | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 220.4 | 282106 | 7013 | 2.49% | 84 |
| Standard_H16mr | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 220.4 | 282167 | 6889 | 2.44% | 84 |
| Standard_H16r | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 110.2 | 280837 | 6587 | 2.35% | 84 |

## <a name="lv2---storage-optimized"></a>Lv2-儲存體優化
 (3/14/2019 5:49:04 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_L8s_v2 | AMD EPYC 7551 32-核心處理器 | 8 | 1 | 62.9 | 80528 | 404 | 0.50% | 119 |
| Standard_L16s_v2 | AMD EPYC 7551 32-核心處理器 | 16 | 2 | 125.9 | 154829 | 3708 | 2.40% | 119 |
| Standard_L32s_v2 | AMD EPYC 7551 32-核心處理器 | 32 | 4 | 251.9 | 310811 | 7751 | 2.49% | 119 |
| Standard_L64s_v2 | AMD EPYC 7551 32-核心處理器 | 64 | 8 | 503.9 | 595140 | 14572 | 2.45% | 112 |
| Standard_L80s_v2 | AMD EPYC 7551 32-核心處理器 | 80 | 10 | 629.9 | 773171 | 19559 | 2.53% | 119 |

## <a name="ls---storage-optimized"></a>Ls - 儲存體最佳化
 (3/12/2019 10:22:29 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_L4s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 31.4 | 56488 | 2916 | 5.16% | 287 |
| Standard_L8s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 62.9 | 107017 | 2323 | 2.17% | 287 |
| Standard_L16s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 125.9 | 210865 | 3653 | 1.73% | 280 |
| Standard_L32s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 251.9 | 399963 | 9254 | 2.31% | 287 |

## <a name="m---memory-optimized"></a>M - 記憶體最佳化
 (4/11/2019 7:30:39 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_M8-2ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 2 | 1 | 215.2 | 22605 | 29 | 0.13% | 42 |
| Standard_M8-4ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 4 | 1 | 215.2 | 44488 | 183 | 0.41% | 42 |
| Standard_M16-4ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 4 | 1 | 430.6 | 44451 | 269 | 0.61% | 42 |
| Standard_M16-8ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 8 | 1 | 430.6 | 88238 | 1243 | 1.41% | 42 |
| Standard_M32-8ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 8 | 1 | 861.2 | 88521 | 1353 | 1.53% | 42 |
| Standard_M32-16ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 16 | 1 | 861.2 | 174674 | 3104 | 1.78% | 42 |
| Standard_M64 | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1007.9 | 683022 | 11929 | 1.75% | 42 |
| Standard_M64-16ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 16 | 2 | 1763.9 | 169386 | 4737 | 2.80% | 42 |
| Standard_M64-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 2 | 1763.9 | 337599 | 4738 | 1.40% | 42 |
| Standard_M64m | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1763.9 | 677466 | 14478 | 2.14% | 42 |
| Standard_M64ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1763.9 | 675342 | 16577 | 2.45% | 42 |
| Standard_M64s | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1007.9 | 674785 | 15983 | 2.37% | 42 |
| Standard_M128 | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 2015.9 | 1334218 | 21126 | 1.58% | 42 |
| Standard_M128-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 4 | 3831.1 | 334873 | 5005 | 1.49% | 42 |
| Standard_M128-64ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 4 | 3831.1 | 667808 | 18145 | 2.72% | 42 |
| Standard_M128m | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 3831.1 | 1335873 | 19642 | 1.47% | 42 |
| Standard_M128ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 3831.1 | 1329151 | 24295 | 1.83% | 42 |
| Standard_M128s | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 2015.9 | 1329923 | 20117 | 1.51% | 42 |
| Standard_M16ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 16 | 1 | 430.6 | 174686 | 2704 | 1.55% | 35 |
| Standard_M32ls | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 1 | 251.9 | 344069 | 3372 | 0.98% | 42 |
| Standard_M32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 1 | 861.2 | 343226 | 4884 | 1.42% | 84 |
| Standard_M32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 2 | 861.2 | 336526 | 4396 | 1.31% | 7 |
| Standard_M32ts | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 1 | 188.9 | 341112 | 5491 | 1.61% | 35 |
| Standard_M64ls | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 503.9 | 676026 | 18078 | 2.67% | 42 |
| Standard_M8ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 8 | 1 | 215.2 | 88066 | 1391 | 1.58% | 42 |

## <a name="ncsv3---gpu-enabled"></a>NCSv3-已啟用 GPU
 (3/21/2019 5:48:37 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NC6s_v3 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 6 | 1 | 110.2 | 106929 | 353 | 0.33% | 49 |
| Standard_NC12s_v3 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 12 | 1 | 220.4 | 213585 | 875 | 0.41% | 42 |
| Standard_NC24rs_v3 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 24 | 2 | 440.9 | 403554 | 6705 | 1.66% | 42 |
| Standard_NC24s_v3 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 24 | 2 | 440.9 | 403874 | 7603 | 1.88% | 42 |

## <a name="ncsv2---gpu-enabled"></a>NCSv2-已啟用 GPU
 (3/12/2019 11:19:19 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NC6s_v2 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 6 | 1 | 110.2 | 107115 | 321 | 0.30% | 63 |
| Standard_NC12s_v2 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 12 | 1 | 220.4 | 213814 | 656 | 0.31% | 63 |
| Standard_NC24rs_v2 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 24 | 2 | 440.9 | 401728 | 6995 | 1.74% | 63 |
| Standard_NC24s_v2 | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 24 | 2 | 440.9 | 402808 | 7923 | 1.97% | 63 |

## <a name="nc---gpu-enabled"></a>NC-已啟用 GPU
 (3/12/2019 11:08:03 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NC6 | Intel (R) )  (R CPU E5-2690 v3 @ 2.60 GHz | 6 | 1 | 55.0 | 102211 | 658 | 0.64% | 259 |
| Standard_NC12 | Intel (R) )  (R CPU E5-2690 v3 @ 2.60 GHz | 12 | 1 | 110.2 | 203523 | 2293 | 1.13% | 259 |
| Standard_NC24 | Intel (R) )  (R CPU E5-2690 v3 @ 2.60 GHz | 24 | 2 | 220.4 | 382897 | 8712 | 2.28% | 259 |
| Standard_NC24r | Intel (R) )  (R CPU E5-2690 v3 @ 2.60 GHz | 24 | 2 | 220.4 | 383171 | 9166 | 2.39% | 259 |

## <a name="nds--gpu-enabled"></a>NDs-已啟用 GPU
 (3/12/2019 11:19:10 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_ND6s | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 6 | 1 | 110.2 | 107095 | 353 | 0.33% | 63 |
| Standard_ND12s | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 12 | 1 | 220.4 | 212298 | 3457 | 1.63% | 63 |
| Standard_ND24rs | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 24 | 2 | 440.9 | 402749 | 7838 | 1.95% | 56 |
| Standard_ND24s | Intel (R)  (R) CPU E5-2690 v4 @ 2.60 GHz | 24 | 2 | 440.9 | 401822 | 7776 | 1.94% | 63 |

## <a name="nv---gpu-enabled"></a>NV-已啟用 GPU
 (3/12/2019 11:08:13 PM pbi 3897709) 

| VM 大小 | CPU | vCPU | NUMA 節點 | 記憶體 (GiB) | 平均分數 | 標準差 | Std Dev% | 執行數 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NV6 | Intel (R) )  (R CPU E5-2690 v3 @ 2.60 GHz | 6 | 1 | 55.0 | 101728 | 2094 | 2.06% | 259 |
| Standard_NV12 | Intel (R) )  (R CPU E5-2690 v3 @ 2.60 GHz | 12 | 1 | 110.2 | 203903 | 1724 | 0.85% | 252 |
| Standard_NV24 | Intel (R) )  (R CPU E5-2690 v3 @ 2.60 GHz | 24 | 2 | 220.4 | 379879 | 8737 | 2.30% | 259 |


## <a name="about-coremark"></a>關於 CoreMark
Linux 數字是藉由在 Ubuntu 上執行 [CoreMark](https://www.eembc.org/coremark/faq.php) 來計算出。 在 CoreMark 的設定中，執行緒的數目是設定為虛擬 CPU 的數目，而並行處理則是設定為 PThreads。 目標反覆運算次數已根據預期的效能進行調整，以提供一個至少 20 秒 (通常會更長) 的執行階段。 最終分數代表的是已完成的反覆運算次數除以執行測試所花費秒數後得出的值。 每個測試在每個 VM 上至少執行了七次。 測試執行日期如上方所示。 測試會於 VM 可支援的日期，在各 Azure 公用區域的多個 VM 上執行。 由於效能是變數，因此未顯示基本 A 和 B (Burstable) 數列。 N 數列不會顯示，因為他們以 GPU 為中心，且 Coremark 不會測量 GPU 效能。

## <a name="next-steps"></a>後續步驟
* 如需了解儲存體容量、磁碟詳細資料及其他選擇 VM 大小的考量，請參閱 [虛擬機器的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要在 Linux VM 上執行 CoreMark 指令碼，請下載 [CoreMark 指令碼套件](https://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)。