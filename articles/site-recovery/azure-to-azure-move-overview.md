---
title: 使用 Azure Site Recovery 將 Azure VM 移至另一個區域
description: 使用 Azure Site Recovery，將 Azure VM 從一個 Azure 區域移至另一個區域。
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 61d596c4b3a65c54e1a70682adad5b7328c384f8
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007361"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>將 Azure VM 移至另一個 Azure 區域

本文提供使用 [Azure Site Recovery](site-recovery-overview.md) 將 Azure VM 移至另一個 Azure 區域時的原因概觀和牽涉到的步驟。 


## <a name="reasons-to-move-azure-vms"></a>移動 Azure VM 的原因

您可能基於下列原因移動 VM：

- 您已經在某個區域中部署，而且已新增新區域的支援，且該區域較接近您應用程式或服務的使用者。 在此情況下，您就會想要將 VM 依原狀移至新區域，以降低延遲。 如果您想要合併訂用帳戶，或是有控管/組織規則要求您移動 VM，請使用相同的方法。
- 您的 VM 已部署為單一執行個體 VM，或作為可用性設定組的一部分。 如果您想要增加可用性 SLA，則可將 VM 移至可用性區域。

## <a name="move-vms-with-resource-mover"></a>使用 Resource Mover 移動 VM

您現在可以使用 [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) 將 VM 移至其他區域。 Resource Mover 處於公開預覽狀態，並且提供：
- 用於在區域之間移動資源的單一中樞。
- 減少移動時間和複雜度。 您需要的所有項目都在單一位置。
- 移動不同類型 Azure 資源的簡單且一致體驗。
- 一個簡單的方法，可識別您想要移動的資源之間的相依性。 這可協助您將相關的資源一起移動，以便在移動之後，所有項目在目標區域中都能如預期般運作。
- 自動清除來源區域中的資源 (如果您想要在移動之後將其刪除)。
- 測試。 如果您不想要進行完整移動，您可以嘗試移動，然後將其捨棄。



## <a name="move-vms-with-site-recovery"></a>使用 Site Recovery 移動 VM

使用 Site Recovery 移動 VM 牽涉到下列步驟：

1. 驗證必要條件。
2. 準備來源 VM。
3. 準備目標區域。
4. 將資料複製到目標區域。 使用 Azure Site Recovery 複寫技術，將資料從來源 VM 複製到目標區域。
5. 測試設定。 複寫完成後，對非生產網路執行測試容錯移轉，藉以測試設定。
6. 執行移動。
7. 捨棄來源區域中的資源。

> [!NOTE]
> 下列各節提供有關這些步驟的詳細資料。
> [!IMPORTANT]
> 目前 Azure Site Recovery 支援在不同的區域間移動 VM，但不支援在一個區域內移動。

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>多層式部署的一般架構

本節將說明 Azure 中適用於多層式應用程式的最常見部署架構。 此範例為一個具有公用 IP 的三層式應用程式。 每一層 (Web、應用程式和資料庫) 各有 2 個 VM，並透過 Azure 負載平衡器彼此連線。 資料庫層的 VM 之間會進行 SQL Server Always ON 複寫，以達到高可用性。

* **單一執行個體 VM 部署於不同層**：一層中的每個 VM 都會設定為單一執行個體 VM，並透過負載平衡器彼此連線。 此為最容易採用的設定。

     ![在不同層之間移動單一執行個體 VM 部署的選項](media/move-vm-overview/regular-deployment.png)

* **每層中的 VM 部署在可用性設定組間**：一層中的每個 VM 會設定於可用性設定組中。 [可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)可確保您在 Azure 上部署的 VM 會分散到叢集中多個各自獨立的硬體節點。 這可確保當 Azure 內發生硬體或軟體故障時，只有 VM 子集會受到影響，而您的整體解決方案則會維持可用且正常運作。

     ![可用性設定組間的 VM 部署](media/move-vm-overview/avset.png)

* **每層中的 VM 部署在可用性區域間**：一層中的每個 VM 都會設定於[可用性設定組](../availability-zones/az-overview.md) \(機器翻譯\) 間。 Azure 區域中的可用性區域是由容錯網域和更新網域組成。 例如，如果您在 Azure 區域中建立橫跨三個區域的三個 (或更多) VM，您的 VM 會有效地分散到三個容錯網域和三個更新網域。 Azure 平台會從更新網域中辨識此分佈，以確定不會同時更新不同區域中的 VM。

     ![可用性區域部署](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>將 VM 依原狀移至目標區域

根據前述的[架構](#typical-architectures-for-a-multi-tier-deployment)，以下將說明您執行依原狀移至目標區域的作業後，部署將呈現的樣貌。

* **單一執行個體 VM 部署於不同層**
* **每層中的 VM 部署在可用性設定組間**
* **每層中的 VM 部署在可用性區域間**

## <a name="move-vms-to-increase-availability"></a>移動 VM 以提高可用性

* **單一執行個體 VM 部署於不同層**

     ![單一執行個體 VM 部署於不同層](media/move-vm-overview/single-zone.png)

* **每層中的 VM 部署在可用性設定組間**：當您使用 Azure Site Recovery 來為 VM 啟用複寫時，可以在可用性設定組中將 VM 設定為個別的可用性區域。 完成移動作業後，可用性 SLA 將達到 99.99%。

     ![可用性設定組和可用性區域間的 VM 部署](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> 
> * [將 Azure VM 移至其他區域](azure-to-azure-tutorial-migrate.md)
> 
> * [將 Azure VM 移至可用性區域中](move-azure-vms-avset-azone.md)

