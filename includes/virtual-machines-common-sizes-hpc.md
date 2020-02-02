---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 29e453dd6a9ea7ac83d84adb7eb0f3d998c1eaaf
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961334"
---
Azure HPC 優化虛擬機器（Vm）的設計目的是為各種真實世界的應用程式提供領導層級的效能、MPI 擴充性和成本效益。
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>大規模 HPC 的不適用網路
HBv2 Vm 功能 200 Gb/秒的 Mellanox HDR 不會，而 HB 和 HC Vm 的功能 100 Gb/秒的 Mellanox EDR 不會。 這些 VM 類型的每一個都是在非封鎖的 fat 樹狀目錄中進行連線，以獲得優化且一致的 RDMA 效能。

HBv2 Vm 支援調適型路由和動態連線傳輸（DCT，也就是標準 RC 和 UD 傳輸的額外功能）。 這些功能可加強應用程式效能、擴充性和一致性，並強烈建議使用它們。  

HBv2、HB 和 HC Vm 支援標準的 Mellanox/OFED 驅動程式。 因此，所有 RDMA 動詞和 MPI 類型都受到支援。 這兩種 VM 類型也支援 MPI 集團的硬體型卸載，以提高應用程式效能。
 
原始 H 系列 Vm 功能 56 Gb/秒 Mellanox FDR 已不會。 若要利用 H 系列上的未使用的介面，客戶必須從 Azure Marketplace 使用此 VM 類型特有的正式支援映射進行部署。 


## <a name="hbv2-series"></a>HBv2 系列
HBv2 系列 Vm 已針對記憶體頻寬所驅動的應用程式優化，例如流體 dynamics、有限元素分析和容器模擬。 HBv2 Vm 功能 120 AMD EPYC 7742 處理器核心，每個 CPU 核心 4 GB RAM，且不會同時進行多執行緒處理。 每個 HBv2 VM 最多可提供每秒 340 GB 的記憶體頻寬，以及最多4次 teraflop 的 FP64 計算。 

進階儲存體：支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（Gb/s） | MPI 支援 | 暫存儲存體（GB） | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | 所有 | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB 系列
HB 系列 VM 針對記憶體頻寬需求高的應用程式 (例如流體動力學、顯式有限元素分析和天氣模型) 最佳化。 HB Vm 功能 60 AMD EPYC 7551 處理器核心，每個 CPU 核心 4 GB RAM，且不會同時進行多執行緒處理。 HB VM 最多可提供每秒 260 GB 的記憶體頻寬。  

ACU：199-216

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 處理器 | 記憶體 (GiB) | 記憶體頻寬 GiB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（GiB/s） | MPI 支援 | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 所有 | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC 系列
HC 系列 Vm 已針對密集計算所驅動的應用程式優化，例如隱含有限元素分析、分子 dynamics 和計算化學。 HC VM 提供 44 個 Intel Xeon Platinum 8168 處理器核心，每個 CPU 核心 8 GB RAM，且無超執行緒。 Intel 的「最強」白金平臺支援 Intel 豐富的軟體工具生態系統，例如 Intel 數學核心程式庫。 

ACU：297-315

進階儲存體：支援

進階儲存體快取：支援


| 大小 | vCPU | 處理器 | 記憶體 (GiB) | 記憶體頻寬 GiB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（GiB/s） | MPI 支援 | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel 8168 技術白金 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 所有 | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H 系列
H 系列 Vm 已針對高 CPU 頻率或每個核心需求的大型記憶體所驅動的應用程式優化。 H 系列 Vm 的功能為8或 16 Intel （r） E5 2667 v3 處理器核心，每個 CPU 核心最多 14 GB RAM，且無超執行緒。 H 系列 VM 最多可提供每秒 80 GB 的記憶體頻寬。

ACU：290 - 300

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GiB) | 記憶體頻寬 GiB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（GiB/s） | MPI 支援 | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel 強 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel 強 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel 強 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel 強 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel 強 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 強 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>針對 MPI 應用程式，FDR 未受的網路會啟用專用的 RDMA 後端網路。

<br>
