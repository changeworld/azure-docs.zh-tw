---
title: HB 系列 VM 大小效能
description: 瞭解 Azure 中 HB 系列 VM 大小的效能測試結果。
author: vermagit
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.openlocfilehash: 34e9ef3ab46f2ce11500aa87db9676635d3e9b4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016277"
---
# <a name="hb-series-virtual-machine-sizes"></a>HB 系列虛擬機器大小

HB 系列的大小已執行數項效能測試。 以下是此效能測試的一些結果。

| 工作負載                                        | HB                    |
|-------------------------------------------------|-----------------------|
| 資料流程三角理論                                    | 每個 CCX) 260 GB/秒 (32-33 GB/秒  |
| High-Performance Linpack (HPL)                   | 1000 GigaFLOPS (Rpeak) ，860 GigaFLOPS (Rmax)  |
| RDMA 延遲 & 頻寬                        | 1.27 微秒、99.1 Gb/秒   |
| 在本機 NVMe SSD 上的 FIO                           | 1.7 GB/s 讀取，1.0 GB/秒寫入      |  
| IOR on 4 * Azure 進階 SSD (P30 受控磁碟，RAID0) * *  | 725 MB/s 讀取、780 MB/寫入   |


## <a name="mpi-latency"></a>MPI 延遲

從 OSU microbenchmark suite 執行 MPI 延遲測試。 範例腳本位於 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="Azure HB 上的 MPI 延遲。":::

## <a name="mpi-bandwidth"></a>MPI 頻寬

從 OSU microbenchmark suite 執行 MPI 頻寬測試。 範例腳本位於 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="Azure HB 上的 MPI 延遲。":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 套件](https://community.mellanox.com/s/article/perftest-package)具有許多未測試的測試，例如延遲 (ib_send_lat) 和頻寬 (ib_send_bw) 。 以下是範例命令。

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解最新的公告，以及一些高效能運算 (HPC) 範例和結果，請參閱 [Azure 計算 Tech 社區 blog](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)。
- 如需執行 HPC 工作負載的更高層級架構視圖，請參閱 [Azure 上的高效能運算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。
