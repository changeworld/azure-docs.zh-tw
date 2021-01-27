---
title: 選擇 VM 大小和集區的映射
description: 如何針對 Azure Batch 集區中的計算節點，從可用的 VM 大小和 OS 版本中選擇
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: seodec18
ms.openlocfilehash: 59218361c5fff51b33131fdd2455c599dd12b06a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897488"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>選擇 Azure Batch 集區中計算節點的 VM 大小和映射

當您為 Azure Batch 集區選取節點大小時，可以從 Azure 中幾乎所有可用的 VM 大小進行選擇。 針對不同的工作負載，Azure 提供許多適用於 Linux 和 Windows VM 的大小。

## <a name="supported-vm-series-and-sizes"></a>支援的 VM 系列和大小

為 Batch 集區選擇 VM 大小有一些例外和限制：

- Batch 不支援某些 VM 系列或 VM 大小。
- 有些 VM 大小會有限制，而且必須特別啟用後才能配置。

### <a name="pools-in-virtual-machine-configuration"></a>虛擬機器組態中的集區

虛擬機器設定中的 Batch 集區幾乎支援所有 [VM 大小](../virtual-machines/sizes.md)。 請參閱下表，以深入了解支援的大小和限制。

| VM 系列  | 支援的大小 |
|------------|---------|
| 基本 A | Basic_A0 (A0)「以外」的所有大小 |
| A | Standard_A0「以外」的所有大小 |
| Av2 | 所有大小 |
| B | 不支援 |
| DC | 不支援 |
| Dv2、DSv2 | 所有大小 |
| Dv3、Dsv3 | 所有大小 |
| Dav4 | 所有大小 |
| Dasv4 | 所有大小 |
| Ddv4, Ddsv4 |  所有大小 |
| Dv4, Dsv4 | 不支援 |
| Ev3、Esv3 | 所有大小（E64is_v3 除外） |
| Eav4 | 所有大小 |
| Easv4 | 所有大小 |
| Edv4, Edsv4 |  所有大小 |
| Ev4, Esv4 | 不支援 |
| F、Fs | 所有大小 |
| Fsv2 | 所有大小 |
| G、Gs | 所有大小 |
| H | 所有大小 |
| HB | 所有大小 |
| HBv2 | 所有大小 |
| Hc | 所有大小 |
| Ls | 所有大小 |
| Lsv2 | 所有大小 |
| M | 所有大小 |
| Mv2<sup>1</sup> | 所有大小 |
| NC | 所有大小 |
| NCv2 | 所有大小 |
| NCv3 | 所有大小 |
| NCasT4_v3 | 無 - 尚未提供 |
| ND | 所有大小 |
| NDv2 | 無 - 尚未提供 |
| NV | 所有大小 |
| NVv3 | 所有大小 |
| NVv4 | 所有大小 |
| SAP HANA | 不支援 |

<sup>1</sup> 這些 vm 系列只能搭配第2代 vm 映射使用。

### <a name="using-generation-2-vm-images"></a>使用第2代 VM 映射

某些 VM 系列（例如 [Mv2](../virtual-machines/mv2-series.md)）只能搭配 [第2代 vm 映射](../virtual-machines/generation-2.md)使用。 第2代 VM 映射的指定方式與任何 VM 映射相同，使用 [' imageReference '](/rest/api/batchservice/pool/add#imagereference) 設定的 ' sku ' 屬性;' sku ' 字串具有尾碼，例如 "-g2" 或 "-gen2"。 若要取得 Batch 所支援的 VM 映射清單（包括第2代映射），請使用「 [列出支援的映射](/rest/api/batchservice/account/listsupportedimages) 」 API、 [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)或 [Azure CLI](/cli/azure/batch/pool/supported-images)。

### <a name="pools-in-cloud-service-configuration"></a>雲端服務組態中的集區

雲端服務設定中的 Batch 集區支援[雲端服務的所有 VM 大小](../cloud-services/cloud-services-sizes-specs.md)，但「不包括」下列各項：

| VM 系列  | 不支援的大小 |
|------------|-------------------|
| A 系列   | 超小型       |
| Av2 系列 | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>大小考量

- **應用程式需求** - 請考量將於節點上執行之應用程式的特性和需求。 應用程式是否為多執行緒以及需要使用多少記憶體之類的層面，有助於決定最適合且具成本效益的節點大小。 針對多重執行個體的 [MPI 工作負載](batch-mpi.md)或 CUDA 應用程式，請考慮分別使用特殊 [HPC](../virtual-machines/sizes-hpc.md) 或[已啟用 GPU](../virtual-machines/sizes-gpu.md) 的 VM 大小。 如需詳細資訊，請參閱[在 Batch 集區中使用支援 RDMA 或已啟用 GPU 功能的執行個體](batch-pool-compute-intensive-sizes.md)。

- **每個節點的工作** - 在選取節點大小時，通常會假設每次在節點上執行一項工作。 不過，在作業執行期間有多項工作 (因而有多個應用程式執行個體) 在計算節點上[以平行方式執行](batch-parallel-node-tasks.md)，也可能有好處。 在此情況下，通常會選擇多核心節點大小，以因應增加的平行工作執行需求。

- **不同工作的負載層級** - 集區中的所有節點都是相同大小。 如果您打算執行具有不同系統需求和/或負載層級的應用程式，建議使用不同的集區。

- **區域可用性** - 在用來建立 Batch 帳戶的區域中，可能不會提供某個 VM 系列或大小。 若要確認是否有提供某個大小，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。

- **配額** - Batch 帳戶中的 [核心配額](batch-quota-limit.md#resource-quotas)可能會限制您可以新增至 Batch 集區的指定大小節點數目。 您可以視需要 [要求增加配額](batch-quota-limit.md#increase-a-quota)。

- **集區組態** - 一般來說，如果您在虛擬機器組態中建立集區，您可選擇的 VM 大小會比雲端服務組態多。

## <a name="supported-vm-images"></a>支援的 VM 映射

使用下列其中一個 Api 來傳回 Batch 目前支援的 Windows 和 Linux VM 映射清單，包括每個映射的節點代理程式 SKU 識別碼：

- Batch 服務 REST API： [列出支援的映射](/rest/api/batchservice/account/listsupportedimages)
- PowerShell： [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI： [az batch pool 支援-映射](/cli/azure/batch/pool/supported-images)

## <a name="next-steps"></a>後續步驟

- 了解 [Batch 服務工作流程和主要資源](batch-service-workflow-features.md)，例如集區、節點、作業和工作。
- 如需使用計算密集型 VM 大小的相關資訊，請參閱[在 Batch 集區中使用具備 RDMA 功能或已啟用 GPU 功能的執行個體](batch-pool-compute-intensive-sizes.md)。
