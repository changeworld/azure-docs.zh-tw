---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16a0fa29c067262f1794528b16abfce662d05605
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748764"
---
<!-- F-series, Fs-series* -->

計算優化 VM 大小具有高 CPU 對記憶體比例。 這些大小適用于中流量 web 伺服器、網路設備、批次處理，以及應用程式伺服器。 本文提供個 vcpu 數目、資料磁片和 Nic 的相關資訊。 其中也包含此群組中每個大小的儲存體輸送量和網路頻寬的相關資訊。

Fsv2 系列是以 Intel®級®白金8168處理器為基礎。 它的特色是將所有核心 Turbo 主頻的速度維持在 3.4 GHz，而最大單一核心 turbo 頻率為 3.7 g h z。 Intel® AVX-512 指示是 Intel 可擴充的處理器上的新技術。 這些指示可為單一和雙精確度浮點運算上的向量處理工作負載，提供最多2倍的效能提升。 換句話說，對於任何計算工作負載來說，這些都是非常快速的。

Fsv2 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。

## <a name="fsv2-series-sup1sup"></a>Fsv2 系列 <sup>1</sup>

ACU：195 - 210

進階儲存體：支援

進階儲存體快取：支援

| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 （768）        | 76800/1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2，&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |

<sup>1</sup> Fsv2 系列 Vm 功能 Intel®超執行緒技術。

<sup>2</sup>若使用超過64的 vCPU，則需要下列其中一種支援的客體作業系統：
- Windows Server 2016 或更新版本
- Ubuntu 16.04 LTS 或更新版本，並搭配 Azure 微調核心（4.15 核心或更新版本）
- SLES 12 SP2 或更新版本
- RHEL 或 CentOS 版本6.7 至6.10，並已安裝 Microsoft 提供的 .LIS 封裝4.3.1 （或更新版本）
- RHEL 或 CentOS 7.3 版，並已安裝 Microsoft 提供的 .LIS 封裝4.2.1 （或更新版本）
- RHEL 或 CentOS 7.6 版或更新版本
- 使用 UEK4 或更新版本的 Oracle Linux
- 具有反向移植核心 Debian 10 或更新版本的 Debian 9
- 使用4.14 核心或更新版本的 CoreOS

<sup>3</sup> 執行個體會隔離至單一客戶專用的硬體。
