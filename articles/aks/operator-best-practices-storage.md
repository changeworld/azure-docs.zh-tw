---
title: 儲存和備份的最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解叢集操作員在 Azure Kubernetes Service (AKS) 中進行儲存、資料加密及備份時的最佳做法
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 90abf2e36fd46c707904d87f00362091fe931743
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668107"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中進行儲存和備份的最佳做法

隨著您在 Azure Kubernetes Service (AKS) 中建立及管理叢集，您的應用程式通常會需要儲存體。 請務必了解 Pod 的效能需求和存取方法，以為應用程式提供適當的儲存體。 AKS 節點大小可能會影響對這些儲存體的選擇。 您也應該針對附加的儲存體規劃備份方法並測試還原程序。

這個最佳做法文章著重在適用於叢集操作員的儲存體考量。 在本文中，您將了解：

> [!div class="checklist"]
> * 有哪些可用的儲存體類型
> * 如何針對儲存體效能正確評估 AKS 節點大小
> * 磁碟區的動態佈建和靜態佈建之間的差異
> * 備份並保護資料磁碟區的方法

## <a name="choose-the-appropriate-storage-type"></a>選擇適當的儲存體類型

**最佳做法指引** - 了解應用程式的需求以挑選正確的儲存體。 針對生產工作負載使用高效能、以 SSD 備份的儲存體。 在有多個並行連線之需求的情況下，針對網路型儲存體進行規劃。

應用程式通常會需要不同類型及速度的儲存體。 您的應用程式是否需要會連線至個別 Pod 的儲存體，或是需要會在多個 Pod 之間共用的儲存體？ 儲存體是否是要用來對資料進行唯讀存取，還是要用來寫入大量結構化資料？ 這些儲存體需求會決定應使用的最適當儲存體類型。

下表會概述可用的儲存體類型及其功能：

| 使用案例 | 磁碟區外掛程式 | 單次讀取/寫入 | 多次唯讀 | 多次讀取/寫入 | Windows 伺服器容器支援 |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| 共用設定       | Azure 檔案   | 是 | 是 | 是 | 是 |
| 結構化應用程式資料        | Azure 磁碟   | 是 | 否  | 否  | 是 |
| 非結構化資料、檔案系統作業 | [BlobFuse][blobfuse] | 是 | 是 | 是 | 否 |

AKS 中針對磁碟區所提供的兩個主要儲存體類型，是由 Azure 磁碟或 Azure 檔案所支援。 為了提升安全性，這兩種儲存體預設都會使用 Azure 儲存體服務加密 (SSE) 來對待用資料進行加密。 目前磁碟無法在 AKS 節點層級使用 Azure 磁碟加密進行加密。

Azure 檔案目前可於「標準」效能層級取得。 Azure 磁碟可於「標準」和「進階」效能層級取得：

- 「進階」** 磁碟是由高效能固態硬碟 (SSD) 所支援。 針對所有生產環境工作負載，都建議使用進階磁碟。
- 「標準」** 磁碟是由一般磁碟 (HDD) 所支援，且適用於封存或不常存取的資料。

藉由了解應用程式效能需求和存取模式，來選擇適當的儲存層。 如需受控磁碟大小和效能的詳細資訊，請參閱 [Azure 受控磁碟概觀][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>建立及使用儲存體類別來定義應用程式需求

您所使用的儲存體類型，是使用 Kubernetes「儲存體類別」** 所定義。 儲存體類別接著會參考於 Pod 或部署規格中。 這些定義會一起運作以建立適當的儲存體，並將它連線至 Pod。 如需詳細資訊，請參閱 [AKS 中的儲存體類別][aks-concepts-storage-classes]。

## <a name="size-the-nodes-for-storage-needs"></a>針對儲存體需求評估節點大小

**最佳做法指引** - 每個節點大小都有其所能支援的磁碟數目上限。 不同的節點大小也會提供不同的本機儲存體大小和網路頻寬。 請針對您的應用程式需求進行規劃，以部署適當的節點大小。

AKS 節點會以 Azure VM 的形式執行。 有不同類型和大小的 VM 可供使用。 每個 VM 大小都會提供不同數量的核心資源，例如 CPU 和記憶體。 這些 VM 大小具有可附加之磁碟數目上限的限制。 不同的 VM 大小在本機和附加磁碟 IOPS (每秒輸入/輸出作業) 上限上，也具有儲存體效能上的差異。

如果您的應用程式需要使用 Azure 磁碟作為其儲存體解決方案，請規劃並選擇適當的節點 VM 大小。 在您選擇 VM 大小時，CPU 和記憶體量並非唯一的考量因素。 儲存體功能也非常重要。 例如，*Standard_B2ms* 和 *Standard_DS2_v2* VM 大小都包含類似的 CPU 和記憶體資源量。 但其潛在的儲存體效能則是不同的，如下表所示：

| 節點類型和大小 | vCPU | 記憶體 (GiB) | 最大資料磁碟 | 最大未快取磁碟 IOPS | 最大未快取輸送量 (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

在這裡，*Standard_DS2_v2* 能允許兩倍的附加磁碟數目，並提供三到四倍的 IOPS 和磁碟輸送量。 如果您僅查看核心運算資源並比較成本，您可能會選擇 *Standard_B2ms* VM 大小，以及其較為不佳的儲存體效能和限制。 請與您的應用程式開發團隊一起合作，以了解他們的儲存體容量和效能需求。 針對 AKS 節點選擇適當的 VM 大小，以符合或超越他們的效能需求。 定期對應用程式進行基準評估，以視需求調整 VM 大小。

如需可用 VM 大小的詳細資訊，請參閱 [Azure 中的 Linux 虛擬機器大小][vm-sizes]。

## <a name="dynamically-provision-volumes"></a>動態佈建磁碟區

**最佳做法指引** - 若要減少管理負擔並允許調整規模，請不要以靜態方式建立並指派永續性磁碟區。 請使用動態佈建。 在您的儲存體類別中定義適當的收回原則，以將刪除 Pod 後的非必要儲存體成本降到最低。

當您需要將儲存體附加到 Pod 時，請使用永續性磁碟區。 這些永續性磁碟區可以透過手動或動態方式建立。 手動建立永續性磁碟區會增加管理負擔，並限制調整規模的能力。 使用動態永續性磁碟區佈建以簡化儲存體管理，並允許您的應用程式視需求成長並調整規模。

![Azure Kubernetes Service (AKS) 叢集中的永續性磁碟區宣告](media/concepts-storage/persistent-volume-claims.png)

永續性磁碟區宣告 (PVC) 可讓您視需求以動態方式建立儲存體。 基礎的 Azure 磁碟會在 Pod 要求它們時建立。 在 pod 定義中,您請求建立卷並將其附加到指定的裝載路徑。

如需如何動態建立及使用磁碟區的概念，請參閱[永續性磁碟區宣告][aks-concepts-storage-pvcs]。

若要查看這些磁碟區實際運作的方式，請參閱如何搭配 [Azure 磁碟][dynamic-disks]或 [Azure 檔案][dynamic-files]以動態方式建立及使用永續性磁碟區。

作為您磁碟區類別定義的一部分，請設定適當的 *reclaimPolicy*。 此 reclaimPolicy 可控制基礎 Azure 儲存體資源在 Pod 刪除後和不再需要永續性磁碟區時的行為。 基礎儲存體資源可以刪除或保留供未來的 Pod 使用。 reclaimPolicy 可以被設定為 [保留]** 或 [刪除]**。 了解您的應用程式需求，並對保留的儲存體實作定期檢查，以將不需要但是被使用且計費的儲存體量降到最低。

如需儲存體類別選項的詳細資訊，請參閱[儲存體回收原則][reclaim-policy]。

## <a name="secure-and-back-up-your-data"></a>保護並備份您的資料

**最佳實務指南**- 使用適合儲存類型的工具(如 Velero 或 Azure 網站恢復)備份資料。 驗證那些備份的完整性及安全性。

當您的應用程式儲存及使用保存在磁碟上或檔案中的資料時，您必須對該資料進行定期備份或擷取快照集。 Azure 磁碟可以使用內建的快照集技術。 在執行快照操作之前,可能需要查找應用程式以刷新寫入磁碟。 [Velero][velero]可以備份持久性卷以及其他群集資源和配置。 如果您無法[從應用程式移除狀態][remove-state]，請備份來自永續性磁碟區的資料並定期測試還原作業，以確認資料完整性及必要的處理程序。

請了解不同資料備份方法的限制，以及您是否需要在擷取快照集之前使資料靜止。 資料備份並不一定能讓您還原您的叢集部署應用程式環境。 如需那些案例的詳細資訊，請參閱 [AKS 中商務持續性和災害復原的最佳做法][best-practices-multi-region]。

## <a name="next-steps"></a>後續步驟

本文著重於 AKS 中的儲存體最佳做法。 如需 Kubernetes 中儲存體基本概念的詳細資訊，請參閱 [AKS 中適用於應用程式的儲存體概念][aks-concepts-storage]。

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
