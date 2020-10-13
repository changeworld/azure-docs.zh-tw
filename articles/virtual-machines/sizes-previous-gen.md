---
title: Azure VM 大小-前幾代 |Microsoft Docs
description: 列出適用于 Azure 中虛擬機器的先前可用大小層代。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/02/2020
ms.author: jushiman
ms.openlocfilehash: 175d572e69dd34a09787f44cf14ae0336c8e95e0
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975598"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>前幾代的虛擬機器大小

本節提供舊版虛擬機器大小的相關資訊。 這些大小仍可使用，但有較新一代的大小可供使用。

## <a name="f-series"></a>F 系列

F 系列是以 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，在 Intel Turbo Boost Technology 2.0 的搭配下，時脈速度最高可達 3.1 GHz。 這是與 Dv2 系列 VM 同等級的 CPU 效能。  

對於需要較快的 CPU 但每一 vCPU 不需要太多記憶體或暫存儲存體的工作負載來說，F 系列 VM 是一個絕佳選擇。  分析、遊戲伺服器、Web 伺服器及批次處理之類的工作負載都將因 F 系列的實用性而受益。

ACU：210 - 250

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大資料磁片/輸送量： IOPS | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs 系列 <sup>1</sup>

Fs 系列可提供 F 系列的所有優點 (進階儲存體除外)。

ACU：210 - 250

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)     | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)     | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)   | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)   | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192)  | 51200/768 | 8/12000 |

MBps = 每秒 10^6 位元組，而 GiB = 1024^3 位元組。

<sup>1</sup> Fs 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。  如需詳細資訊，請參閱 [高效能的設計](premium-storage-performance.md)。

<br>



### <a name="basic-a"></a>基本 A  

**較新的大小建議**： [Av2 系列](av2-series.md)

進階儲存體：不支援

進階儲存體快取：不支援

基本層大小主要適用於開發工作負載，以及其他不需要負載平衡、自動調整或記憶體高用量虛擬機器的應用程式。

| 大小 - 大小\名稱 | vCPU | Memory|NIC (最大)| 暫存磁碟大小上限 | 最大 資料磁碟 (每個 1023 GB)| 最大 IOPS (每個磁碟 300) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1x300  |
| A1\Basic_A1 | 1 | 1.75 GB | 2 | 40 GB  | 2  | 2x300  |
| A2\Basic_A2 | 2 | 3.5 GB  | 2 | 60 GB  | 4  | 4x300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8x300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>使用 CLI 和 PowerShell 的標準 A0 - A4

在傳統部署模型中，部分 VM 大小名稱會與 CLI 和 PowerShell 中的稍有不同：

* Standard_A0 是「特小型」
* Standard_A1 是「小型」
* Standard_A2 是「中型」
* Standard_A3 是「大型」
* Standard_A4 是「特大型」

### <a name="a-series"></a>A 系列  

**較新的大小建議**： [Av2 系列](av2-series.md)

ACU：50 - 100

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (HDD)：GiB | 最大資料磁碟 | 最大資料磁碟輸送量︰IOPS | 最大 Nic/預期的網路頻寬 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0.768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8x500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16x500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8x500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16x500 | 4/2000 |

<sup>1</sup> A0 大小已在實體硬體上過度訂閱。 僅針對這個特定大小，其他客戶部署可能會影響您正在執行的工作負載的效能。 以下概述的相對效能為預期的基準，受限於近似變化性的 15%。

<br>

### <a name="a-series---compute-intensive-instances"></a>A 系列 - 大量計算執行個體  

**較新的大小建議**： [Av2 系列](av2-series.md)

ACU：225

進階儲存體：不支援

進階儲存體快取：不支援

A8-A11 和 H 系列大小也稱為 *計算密集型執行個體*。 執行這些大小的硬體是針對計算密集型和網路密集型應用程式 (包括高效能運算 (HPC) 叢集應用程式)、模型化及模擬而設計及最佳化的。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，而 H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。  

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (HDD)：GiB | 最大資料磁碟 | 最大資料磁碟輸送量︰IOPS | 最大 NIC|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup> 對於 MPI 應用程式，FDR InfiniBand 網路會啟用專用 RDMA 後端網路，以提供超低延遲和高頻寬。  

> [!NOTE]
> A8 – A11 VM 計劃於 2021 年 3 月淘汰。 強烈建議您不要建立任何新的 A8-A11 Vm。 請將任何現有的 A8 – A11 Vm 遷移至較新且強大的高效能運算 VM 大小，例如 H、HB、HC、HBv2，以及一般目的計算 VM 大小，例如 D、E 和 F，以獲得更好的價格效能。 如需詳細資訊，請參閱 [HPC 移轉指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

<br>

### <a name="d-series"></a>D 系列  

**較新的大小建議**： [Dav4 系列](dav4-dasv4-series.md)、 [Dv4 系列](dv4-dsv4-series.md) 和 [Ddv4 系列](ddv4-ddsv4-series.md)

ACU： 160-250 <sup>1</sup>

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大資料磁片/輸送量： IOPS | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|
| 標準_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| 標準_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| 標準_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> 個 VM 系列可在下列其中一項 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz Intel® e5-2673 V3 (Haswell) 或 2.3 g h z Intel® E5-2673 V4 (Broadwell)   

<br>

### <a name="d-series---memory-optimized"></a>D 系列 - 記憶體已最佳化  

**較新的大小建議**： [Dav4 系列](dav4-dasv4-series.md)、 [Dv4 系列](dv4-dsv4-series.md) 和 [Ddv4 系列](ddv4-ddsv4-series.md)

ACU： 160-250 <sup>1</sup>

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大資料磁片/輸送量： IOPS | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|
| 標準_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| 標準_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| 標準_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| 標準_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> 個 VM 系列可在下列其中一項 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz Intel® e5-2673 V3 (Haswell) 或 2.3 g h z Intel® E5-2673 V4 (Broadwell)   

<br>

## <a name="preview-dc-series"></a>預覽： DC 系列

**較新的大小建議**： [DCsv2 系列](dcv2-series.md)

進階儲存體：支援

進階儲存體快取：支援

DC 系列使用最新一代的 3.7 GHz Intel E-2176g (具處理器搭配 SGX 技術，而 Intel Turbo 加速技術最高可達 4.7 GHz。 

| 大小          | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> DC 系列 Vm 是 [第2代 vm](./generation-2.md#creating-a-generation-2-vm) ，且僅支援 `Gen2` 映射。


### <a name="ds-series"></a>DS 系列  

**較新的大小建議**： [Dasv4 系列](dav4-dasv4-series.md)、 [Dsv4 系列](dv4-dsv4-series.md) 和 [Ddsv4 系列](ddv4-ddsv4-series.md)

ACU： 160-250 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)     | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)     | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172)  | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344)  | 25600/256 | 8/4000 |

<sup>1</sup> 個 VM 系列可在下列其中一項 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz Intel® e5-2673 V3 (Haswell) 或 2.3 g h z Intel® E5-2673 V4 (Broadwell)   

<br>

### <a name="ds-series---memory-optimized"></a>DS 系列 - 記憶體已最佳化  

**較新的大小建議**： [Dasv4 系列](dav4-dasv4-series.md)、 [Dsv4 系列](dv4-dsv4-series.md) 和 [Ddsv4 系列](ddv4-ddsv4-series.md)

ACU： 160-250 <sup>1、2</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)     | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144)  | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288)  | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576)  | 51200/512 | 8/8000 |

<sup>1</sup> DS 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。  如需詳細資訊，請參閱 [高效能的設計](premium-storage-performance.md)。
<sup>2</sup> 個 VM 系列可在下列其中一項 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz Intel® e5-2673 V3 (Haswell) 或 2.3 GHz Intel® E5-2673 V4 (Broadwell)   

<br>

### <a name="ls-series"></a>Ls 系列

**較新的大小建議**： [Lsv2 系列](lsv2-series.md)

Ls 系列使用[Intel® Xeon® 處理器 E5 v3 系列](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)，提供最多 32 個 vCPU。 Ls 系列會取得與 G/GS 系列相同的 CPU 效能，而且每個 vCPU 會有 8 GiB 的記憶體。

Ls 系列不支援建立本機快取，來增加可由耐久資料磁碟達成的 IOPS。 本機磁片的高輸送量和 IOPS 讓 Ls 系列 Vm 適用于 NoSQL 存放區（例如 Apache Cassandra 和 MongoDB），可將資料複寫到多個 Vm，以在單一 VM 失敗時達到持續性。

ACU：180 - 240

進階儲存體：支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體 (GiB) | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大暫存儲存體輸送量 (IOPS/MBps)  | 最大未快取的磁片輸送量 (IOPS/MBps)  | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Ls 系列 VM 的最大磁碟輸送量，可能會受到任何連結磁碟的數量、大小和串接所限制。 如需詳細資訊，請參閱 [高效能的設計](premium-storage-performance.md)。

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。

### <a name="gs-series"></a>GS 系列

**較新的大小建議**： [Easv4 系列](eav4-easv4-series.md)、 [Esv4 系列](ev4-esv4-series.md)、 [Edsv4 系列](edv4-edsv4-series.md) 和 [M 系列](m-series.md)

ACU：180 - 240 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)   | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)   | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056)  | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112)  | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224)  | 80000/2000 | 8/20000 |

<sup>1</sup> GS 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。 如需詳細資訊，請參閱 [高效能的設計](premium-storage-performance.md)。

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

<sup>3</sup> 可用限制核心大小。

<br>

### <a name="g-series"></a>G 系列

**較新的大小建議**： [Eav4 系列](eav4-easv4-series.md)、 [Ev4 系列](ev4-esv4-series.md) 和 [Edv4 系列](edv4-edsv4-series.md) 與 [M 系列](m-series.md)

ACU：180 - 240

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大資料磁片/輸送量： IOPS | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。
<br>

## <a name="nv-series"></a>NV 系列
**較新的大小建議**： [NVv3 系列](nvv3-series.md) 和 [NVv4 系列](nvv4-series.md)

NV 系列虛擬機器是由 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) \(英文\) GPU 和 NVIDIA GRID 技術提供技術支援，適用於桌面加速應用程式和虛擬桌面，可供客戶將其資料或模擬視覺化。 使用者能夠在 NV 執行個體上，將其圖形密集型工作流程視覺化以獲得較佳的圖形功能，此外還能夠執行單精確度工作負載，例如編碼和轉譯。 NV 系列 Vm 也支援 Intel 2690 v3， (Haswell) Cpu。

NV 執行個體中的每個 GPU 均隨附 GRID 授權。 此授權可讓您彈性地使用 NV 執行個體作為單一使用者的虛擬工作站，或讓 25 位並行使用者可以針對某個虛擬應用程式案例連線至 VM。

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC | 虛擬工作站 | 虛擬應用程式 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = 1/2 M60 卡。
<br>


## <a name="nvv2-series"></a>NVv2 系列

**較新的大小建議**： [NVv3 系列](nvv3-series.md)

NVv2 系列的虛擬機器採用 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 以及具備 Intel Broadwell CPU 的 NVIDIA GRID 技術。 這些虛擬機器專用於 GPU 高速圖形應用程式以及虛擬桌面，客戶可用於將其資料視覺化、將結果模擬到檢視中、運用於 CAD 之上，或是轉譯內容及串流內容。 除此之外，這些虛擬機器可以執行單一的精密工作負載，像是編碼及轉譯。 NVv2 虛擬機器支援進階儲存體，同時相較於之前的 NV 系列，配備兩倍的系統記憶體 (RAM)。  

NVv2 執行個體中的每個 GPU 均隨附 GRID 授權。 此授權可讓您彈性地使用 NV 執行個體作為單一使用者的虛擬工作站，或讓 25 位並行使用者可以針對某個虛擬應用程式案例連線至 VM。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC | 虛擬工作站 | 虛擬應用程式 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

<br>

## <a name="nc-series"></a>NC 系列
**較新的大小建議**： [NC T4 v3 系列](nct4-v3-series.md)

NC 系列 Vm 是由 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) 卡和 Intel 2690 E5-v3 所支援， (Haswell) 處理器。 使用者可以藉由將 CUDA 用於能源探勘應用程式、當機模擬、光線追蹤轉譯、深度學習等等，更快速地處理資料。 NC24r 設定提供低延遲且高輸送量網路介面，最適合用於緊密結合的平行計算工作負載。

[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = 1/2 K80 卡。

*支援 RDMA


<br>


## <a name="ncv2-series"></a>NCv2 系列
**較新的大小建議**： [NC T4 V3 系列](nct4-v3-series.md) 和 [NC V100 v3 系列](ncv3-series.md)

NCv2 系列 VM 是由 NVIDIA Tesla P100 GPU 提供技術支援。 這些 GPU 可提供 NC 系列 2 倍以上的計算效能。 客戶可針對儲槽模型、DNA 定序、蛋白質分析、蒙地卡羅模擬等傳統 HPC 工作負載，善用這些更新過的 GPU。 除了 Gpu 之外，NCv2 系列的 Vm 也會由 Intel Broadwell E5-2690 v4 () Cpu 提供技術支援。

NC24rs v2 組態提供低延遲且高輸送量網路介面，最適合用於緊密結合的平行計算工作負載。

[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>

> 針對此 VM 系列，訂用帳戶中的 vCPU (core) 配額一開始會在每個區域中設定為0。 在[可用區域](https://azure.microsoft.com/regions/services/)中，為此系列[要求增加 vCPU 配額](../azure-portal/supportability/resource-manager-core-quotas-request.md)。
>
| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = 一張 P100 卡。

*支援 RDMA

<br>

## <a name="nd-series"></a>ND 系列
**較新的大小建議**： [NDv2 系列](ndv2-series.md) 和 [NC V100 v3 系列](ncv3-series.md)

ND 系列的虛擬機器是 GPU 系列的新成員，專為 AI 和深度學習工作負載所設計。 它們能為訓練和推斷提供絕佳效能。 ND 實例是由 [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Gpu 和 Intel 2690 E5-v4 所支援， (Broadwell) cpu。 這些執行個體為單精確度浮點數作業、使用 Microsoft Cognitive Toolkit 的 AI 工作負載、TensorFlow、Caffe 及其他架構，提供絕佳的效能。 ND 系列還能提供更大的 GPU 記憶體大小 (24 GB)，能夠用於更大的類神經網路模型。 如同 NC 系列，ND 系列透過 RDMA 提供具有次要低延遲且高輸送量網路的設定，以及 InfiniBand 連線能力，讓您能夠執行使用橫跨數個 GPU 的大規模訓練作業。

[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>

> 針對此 VM 系列，訂用帳戶中每個區域的 vCPU (core) 配額一開始會設定為0。 在[可用區域](https://azure.microsoft.com/regions/services/)中，為此系列[要求增加 vCPU 配額](../azure-portal/supportability/resource-manager-core-quotas-request.md)。
>
| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = 一張 P40 卡。

*支援 RDMA

<br>

## <a name="other-sizes"></a>其他大小

* [一般用途](sizes-general.md)
* [計算最佳化](sizes-compute.md)
* [記憶體最佳化](sizes-memory.md)
* [儲存體最佳化](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [高效能計算](sizes-hpc.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。