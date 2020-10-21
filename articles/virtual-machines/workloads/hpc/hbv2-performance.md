---
title: HBv2 系列 VM 大小效能
description: 瞭解 Azure 中 HBv2 系列 VM 大小的效能測試結果。
services: virtual-machines
author: vermagit
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 96c70936d6025ad5c1686f5ebae054d01ae05d07
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332626"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>HBv2 系列虛擬機器大小

已在 [HBv2 系列](../../hbv2-series.md) 大小的 vm 上執行數個效能測試。 以下是此效能測試的一些結果。


| 工作負載                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| 資料流程三角理論                                    | 每個 CCX) 350 GB/秒 (21-23 GB/秒                                     |
| High-Performance Linpack (HPL)                   | 4 TeraFLOPS (Rpeak、FP64) 、8 TeraFLOPS (Rmax、FP32)                |
| RDMA 延遲 & 頻寬                        | 1.2 微秒、190 Gb/秒                                        |
| 在本機 NVMe SSD 上的 FIO                           | 2.7 GB/s 讀取、1.1 GB/s 寫入;102k IOPS 讀取，115 IOPS 寫入 |
| IOR on 8 * Azure 進階 SSD (P40 受控磁碟，RAID0) * *  | 1.3 GB/s 讀取、2.5 GB/寫入;101k IOPS 讀取，105k IOPS 寫入 |


## <a name="mpi-latency"></a>MPI 延遲

從 OSU microbenchmark suite 執行 MPI 延遲測試。 範例腳本位於 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)上。


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="Azure HB 上的 MPI 延遲。":::


## <a name="mpi-bandwidth"></a>MPI 頻寬

從 OSU microbenchmark suite 執行 MPI 頻寬測試。 範例腳本位於 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)上。


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Azure HB 上的 MPI 延遲。":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 套件](https://community.mellanox.com/s/article/perftest-package)具有許多未測試的測試，例如延遲 (ib_send_lat) 和頻寬 (ib_send_bw) 。 以下是範例命令。 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>後續步驟

- 深入瞭解最新的公告，以及一些高效能運算 (HPC) 範例和結果，請參閱 [Azure 計算 Tech 社區 blog](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)。
- 如需執行 HPC 工作負載的更高層級架構視圖，請參閱 [Azure 上的高效能運算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。