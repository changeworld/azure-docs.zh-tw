---
title: 將 Batch 集區設定從雲端服務遷移至虛擬機器
description: 瞭解如何將您的集區設定更新為最新和建議的設定
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: b6f4184f7c4f133f74cb3157638b1621dad25fda
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97969022"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>將 Batch 集區設定從雲端服務遷移至虛擬機器

您可以使用 [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) 或 [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)來建立 Batch 集區。 ' virtualMachineConfiguration ' 是建議的設定，因為它支援所有批次功能。 ' cloudServiceConfiguration ' 集區不支援所有功能，也未規劃任何新功能。

如果您使用 ' cloudServiceConfiguration ' 集區，強烈建議您移至使用 ' virtualMachineConfiguration ' 集區。 這可讓您從所有批次功能獲益，例如，擴充 [的 VM 系列](batch-pool-vm-sizes.md)、Linux vm、 [容器](batch-docker-container-workloads.md)、 [Azure Resource Manager 虛擬網路](batch-virtual-network.md)和 [節點磁片加密](disk-encryption.md)選項。

本文說明如何遷移至 ' virtualMachineConfiguration '。

## <a name="new-pools-are-required"></a>需要新的集區

無法將現有的作用中集區從 ' cloudServiceConfiguration ' 更新為 ' virtualMachineConfiguration '，必須建立新的集區。 所有 Batch Api、命令列工具、Azure 入口網站和 Batch Explorer UI 都支援使用 ' virtualMachineConfiguration ' 建立集區。

[.Net](tutorial-parallel-dotnet.md)和[Python](tutorial-parallel-python.md)教學課程提供使用 ' virtualMachineConfiguration ' 建立集區的範例。

## <a name="pool-configuration-differences"></a>集區設定差異

更新集區設定時應考慮下列事項：

- ' cloudServiceConfiguration ' 集區節點一律為 Windows OS，' virtualMachineConfiguration ' 集區可以是 Linux 或 Windows 作業系統。
- 相較于 ' cloudServiceConfiguration ' 集區，' virtualMachineConfiguration ' 集區具有一組更豐富的功能，例如容器支援、資料磁片和磁片加密。
- ' virtualMachineConfiguration ' 集區節點使用受控作業系統磁片。 用於每個節點的 [受控磁片類型](../virtual-machines/disks-types.md) 取決於為集區選擇的 VM 大小。 如果為集區指定了 ' VM 大小，例如 ' Standard_D2s_v3 '，則會使用 premium SSD。 如果指定了「非 s」 VM 大小，例如「Standard_D2_v3」，則會使用標準 HDD。

   > [!IMPORTANT]
   > 如同虛擬機器和虛擬機器擴展集，用於每個節點的 OS 受控磁片會產生費用，這是 Vm 成本的額外成本。 因為在節點本機 SSD 上建立 OS 磁片，所以沒有 ' cloudServiceConfiguration ' 節點的 OS 磁片費用。

- 在 ' cloudServiceConfiguration ' 集區和 ' virtualMachineConfiguration ' 集區之間，集區和節點的啟動和刪除時間可能稍有不同。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [集](nodes-and-pools.md#configurations)區設定。
- 深入瞭解集區的 [最佳做法](best-practices.md#pools)。
- REST API 集區 [新增](https://docs.microsoft.com/rest/api/batchservice/pool/add) 和 [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)的參考。
