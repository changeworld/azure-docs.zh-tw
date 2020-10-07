---
title: 啟用 InfiniBand 的 H 系列和 N 系列 VM 上的高效能運算 - Azure 虛擬機器
description: 了解啟用 InfiniBand 的 H 系列和 N 系列 VM 針對 HPC 最佳化的各項特性和功能。
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms-subservice: hpc
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/29/2020
ms.openlocfilehash: f9a672777fbc8a75cb6af10ba25232f8a274c894
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87543719"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>H 系列和 N 系列 VM 上啟用 InfiniBand 的高效能運算

Azure 已啟用 InfiniBand 的 H 系列和 N 系列 VM 的設計訴求是為各種真實世界的 HPC 和 AI 工作負載提供領導層級的效能、訊息傳遞介面 (MPI) 可擴縮性和成本效益。 這些經過高效能運算 (HPC) 最佳化的 VM 是用來解決科學和工程領域中一些最需要密集運算的問題，例如流體動力學、地球建模、氣象模擬等等。

這些文章說明如何在 Azure 上開始使用已啟用 InfiniBand 的 H 系列和 N 系列 VM，以及如何在 VM 上以最佳方式設定 HPC 和 AI 工作負載以達成可擴縮性。

## <a name="features-and-capabilities"></a>功能

啟用 InfiniBand 的 H 系列和 N 系列 VM 的設計訢求是為 HPC 工作負載提供最佳的 HPC 效能、MPI 可擴縮性和成本效益。 若要深入了解 VM 的特性和功能，請參閱 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM。

### <a name="rdma-and-infiniband"></a>RDMA 和 InfiniBand

[支援 RDMA](../../sizes-hpc.md#rdma-capable-instances) 的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 會透過低延遲和高頻寬的網路進行通訊。 這類互連的 RDMA 功能對於提升分散式節點 HPC 和 AI 工作負載的可擴縮性和效能非常重要。 啟用 InfiniBand 的 H 系列和 N 系列的 VM 是非封鎖式的寬大樹狀結構，並具備低直徑設計，以獲得最佳且一致的 RDMA 效能。
請參閱[啟用 InfiniBand](enable-infiniband.md) 以深入了解如何在啟用 InfiniBand 的 VM 上設定 InfiniBand。

### <a name="message-passing-interface"></a>訊息傳遞介面

啟用 SR-IOV 的 H 系列和 N 系列支援幾乎所有 MPI 程式庫和版本。 一些最常見的受支援 MPI 程式庫包括：Intel MPI、OpenMPI、MPICH、MVAPICH2、Platform MPI 和所有遠端直接記憶體存取 (RDMA) 動詞。
請參閱[設定 MPI](setup-mpi.md)，以深入了解如何安裝各種支援的 MPI 程式庫及其最佳組態。

## <a name="get-started"></a>開始使用

第一個步驟是針對以 VM 規格為基礎的工作負載和 [RDMA 功能](../../sizes-hpc.md#rdma-capable-instances)，選取已進行最佳化的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 類型。
第二，啟用 InfiniBand 來設定 VM。 有各種方法可以執行這項作業，包括使用最佳化的 VM 映像搭配驅動程式內建聲場模擬。如需詳細資訊，請參閱 [Linux 優化](configure.md)和[啟用 InfiniBand](enable-infiniband.md)。
第三，針對分散式節點工作負載，選擇和設定 MPI 是很重要的。 如需詳細資訊，請參閱[設定 MPI](setup-mpi.md)。
第四，為獲得效能和可擴縮性，請遵循 VM 系列的特定指引來以最佳方式設定工作負載，例如 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)。

## <a name="next-steps"></a>後續步驟

- 了解[設定和最佳化](configure.md)已啟用 InfiniBand 的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM。
- 請檢閱 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)，了解如何以最佳方式設定工作負載以獲得效能和可擴縮性。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
