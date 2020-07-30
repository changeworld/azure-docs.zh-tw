---
title: H 系列 VM 上的高效能運算 - Azure 虛擬機器
description: 了解最適用於 HPC 的 H 系列 VM 功能。
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: 1f88496a6c474194089f20e128b2b92ec61e8559
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083424"
---
# <a name="high-performance-computing-on-h-series-vms"></a>H 系列 VM 上的高效能運算

HB 系列和 HC 系列 VM 上的高效能運算 (HPC) 能夠在任何 Azure VM 上實現最佳化的 HPC 效能。 HPC 最佳化 VM 可用來解決一些最困難的數學問題，例如：流體動力學、石油與天然氣模擬，以及天氣模型。

本文會說明 HB 系列和 HC 系列 VM 的一些重要功能、這些 VM 為何可在 HPC 案例中有良好效能，以及如何開始使用。

## <a name="features-and-capabilities"></a>功能

HB 系列和 HC 系列 VM 是設計來為 HPC 工作負載提供最佳的 HPC 效能、訊息傳遞介面 (MPI) 延展性和成本效益。

### <a name="message-passing-interface"></a>訊息傳遞介面

HB 系列和 HC 系列可支援幾乎所有的 MPI 類型和版本。 一些最常見的受支援 MPI 類型包括：OpenMPI、MVAPICH2、Platform MPI、Intel MPI 和所有遠端直接記憶體存取 (RDMA) 動詞。 如需詳細資訊，請參閱[設定 HPC 的訊息傳遞介面](setup-mpi.md)。

### <a name="rdma-and-infiniband"></a>RDMA 和 InfiniBand

RDMA 介面是 HB 系列和 HC 系列 VM 上的標準介面。 支援 RDMA 的執行個體會透過 InfiniBand 網路來通訊，會以增強的資料費率 (EDR) 來對 HB 系列和 HC 系列的虛擬機器營運。 支援 RDMA 的執行個體可以提高某些 MPI 應用程式的延展性和效能。

支援 HB 系列和 HC 系列 VM 的 InfiniBand 組態是非封鎖式的寬大樹狀結構，並有低直徑設計以獲得一致的 RDMA 效能。

請參閱[啟用 InfiniBand](enable-infiniband.md) 以深入了解如何在 HB 系列或 HC 系列 VM 上設定 InfiniBand。

## <a name="get-started"></a>開始使用

首先，請決定您要使用哪一個 H 系列 VM。 如需 HPC 最佳化 VM 的詳細資料，請參閱 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)。 規格則請參閱[高效能運算 VM 大小](../../sizes-hpc.md)。

為應用程式選取並建立 VM 後，您就必須藉由啟用 InfiniBand 來設定 VM。 若要了解如何在 Windows 和 Linux VM 上啟用 InfiniBand，請參閱[啟用 InfiniBand](enable-infiniband.md)。

HPC 工作負載的重要元件是 MPI。 HB 系列和 HC 系列可支援幾乎所有的 MPI 類型和版本。 如需詳細資訊，請參閱[設定 HPC 的訊息傳遞介面](setup-mpi.md)。

選擇好 VM 系列並設定了 Infiniband 和 MPI 後，您即可開始建置 HPC 工作負載。

## <a name="next-steps"></a>後續步驟

- 檢閱 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)來了解主要差異和規格。

- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
