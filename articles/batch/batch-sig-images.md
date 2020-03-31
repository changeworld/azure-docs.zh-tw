---
title: 使用共用映射庫創建自訂池 - Azure 批次處理 |微軟文檔
description: 使用共用映射庫創建批次處理池，以將自訂映射預配到包含應用程式所需的軟體和資料的節點。 自訂映像是設定計算節點以執行 Batch 工作負載的有效方式。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 2cff6a0e48fc7bf58a642f509fcda6b114e002ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022931"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>使用共用圖像庫創建自訂池

當您使用虛擬機器設定建立 Azure Batch 集區時，需指定 VM 映像，以提供集區中每個計算節點的作業系統。 您可以使用受支援的 Azure 應用商店映射創建虛擬機器池，也可以使用[共用映射庫](../virtual-machines/windows/shared-image-galleries.md)創建自訂映射。

## <a name="benefits-of-the-shared-image-gallery"></a>共用圖像庫的優勢

當您對自訂映射使用共用映射庫時，您可以控制作業系統類型和配置以及資料磁片的類型。 共用映射可以包括應用程式和參考資料，這些資料在預配後立即在所有 Batch 池節點上可用。

您還可以根據環境需要有多個版本的映射。 使用映射版本創建 VM 時，映射版本用於為 VM 創建新磁片。

使用共用映射可節省準備池的計算節點以運行 Batch 工作負荷的時間。 預配後，可以在每個計算節點上使用 Azure 應用商店映射並安裝軟體，但使用共用映射通常更高效。 此外，您可以為共用映射指定多個副本，因此，當您創建具有多個 VM（超過 600 個 VM）的池時，可以節省創建池的時間。

使用為方案配置的共用映射可以提供以下幾個優勢：

* **跨區域使用相同的圖像。** 您可以在不同區域創建共用映射副本，以便所有池都使用相同的映射。
* **配置作業系統 （OS）。** 您可以自訂映像作業系統磁碟的設定。
* **預先安裝應用程式。** 在 OS 磁片上預先安裝應用程式比在將計算節點預配到啟動任務後更高效、更不容易出錯。
* **複製大量資料一次。** 通過將靜態資料複製到託管映射的資料磁片，使靜態資料成為託管共用映射的一部分。 這只需要執行一次，就能將資料提供給集區的每個節點。
* **將池長到更大的大小。** 使用共用圖像庫，您可以使用自訂映射以及更多共用圖像複本建立更大的池。
* **性能優於自訂映射。** 使用共用映射，池達到穩定狀態所需的時間可快 25%，VM 空閒延遲最多縮短 30%。
* **圖像版本控制和分組，便於管理。** 圖像分組定義包含有關創建映射的原因、映射的作業系統以及有關使用圖像的資訊。 對圖像進行分組可以簡化圖像管理。 有關詳細資訊，請參閱[圖像定義](../virtual-machines/windows/shared-image-galleries.md#image-definitions)。

## <a name="prerequisites"></a>Prerequisites

* **Azure Batch 帳戶**。 要創建批次處理帳戶，請參閱使用[Azure 門戶](quick-create-portal.md)或[Azure CLI](quick-create-cli.md)的批次處理快速入門。

* **共用圖像庫圖像**。 要創建共用映射，需要擁有或創建託管映射資源。 您應該從 VM 之 OS 磁碟 (以及視需要從其連結之資料磁碟) 的快照集建立該映像。 有關詳細資訊，請參閱[準備託管映射](#prepare-a-managed-image)。

> [!NOTE]
> 您的共用映射必須與批次處理帳戶處於同一訂閱中。 共用映射可以位於不同的區域，只要它具有與 Batch 帳戶相同的區域的副本。

## <a name="prepare-a-managed-image"></a>準備託管映射

在 Azure 中，可以從：

* Azure VM 的作業系統和資料磁片的快照
* 具有託管磁片的通用 Azure VM
* 上傳到雲的通用本地 VHD

若要使用自訂映像來可靠地調整 Batch 集區，建議您「只」** 使用第一個方法：使用 VM 磁碟的快照集。 請參閱下列步驟來準備 VM、建立快照集，然後從快照集建立映像。

### <a name="prepare-a-vm"></a>準備 VM

如果要為映射創建新 VM，請使用 Batch 支援的第一方 Azure 應用商店映射作為託管映射的基本映射。 只有第一方圖像才能用作基本映射。 要獲取 Azure Batch 支援的 Azure 應用商店映射引用的完整清單，請參閱[清單節點代理 SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)操作。

> [!NOTE]
> 您無法使用具有額外授權和購買條款的第三方映像作為您的基礎映像。 如需這些 Marketplace 映像的相關資訊，請參閱 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)或 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM 的指導方針。

* 確保使用託管磁片創建 VM。 當您建立 VM 時，這是預設的儲存體設定。
* 不要在 VM 上安裝 Azure 延伸模組，例如「自訂指令碼」延伸模組。 如果映像包含預先安裝的延伸模組，則 Azure 在部署 Batch 集區時可能會遇到問題。
* 使用附加的資料磁片時，您需要從 VM 中裝載和格式化磁片才能使用它們。
* 確定您提供的基本 OS 映像使用預設的暫存磁碟機。 Batch 節點代理程式目前需要有預設的暫存磁碟機。
* 一旦 VM 開始執行之後，請透過 RDP (適用於 Windows) 或 SSH (適用於 Linux) 向它連線。 安裝任何必要的軟體或複製所需的資料。  

### <a name="create-a-vm-snapshot"></a>建立 VM 快照集

快照集是完整的 VHD 唯讀複本。 若要建立 VM 之 OS 或資料磁碟的快照集，您可以使用 Azure 入口網站或命令列工具。 如需了解建立快照集的步驟與選項，請參閱適用於 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 或 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 的指導方針。

### <a name="create-an-image-from-one-or-more-snapshots"></a>從一或多個快照集建立映像

若要從快照集建立受控映像，請使用 Azure 命令列工具，例如 [az image create](/cli/azure/image) 命令。 通過指定 OS 磁片快照和可選一個或多個資料磁片快照來創建映射。

### <a name="create-a-shared-image-gallery"></a>建立共用映像庫

成功創建託管映射後，需要創建共用映射庫以使自訂映射可用。 要瞭解如何為映射創建共用圖像庫，請參閱使用 Azure [CLI 創建共用圖像庫](../virtual-machines/linux/shared-images.md)或使用[Azure 門戶創建共用圖像庫](../virtual-machines/linux/shared-images-portal.md)。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>使用 Azure CLI 從共用映射創建池

要使用 Azure CLI 從共用映射創建池，請使用`az batch pool create`命令。 在`--image`欄位中指定共用圖像 ID。 確保作業系統類型和 SKU 與`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>使用 C 從共用映射創建池#

或者，您可以使用 C# SDK 從共用映射創建池。

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>使用 Azure 門戶從共用映射創建池

使用以下步驟從 Azure 門戶中的共用映射創建池。

1. 打開[Azure 門戶](https://portal.azure.com)。
1. 轉到**批次處理帳戶**並選擇您的帳戶。
1. 選擇 **"池**"，然後 **"添加"** 以創建新池。
1. 在 **"圖像類型"** 部分中，選擇 **"共用圖像庫**"。
1. 使用有關託管映射的資訊完成其餘部分。
1. 選取 [確定]****。

![使用門戶從共用映射創建具有池。](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>大型集區的考量

如果計畫使用共用映射創建具有數百或數千個 VM 或更多人的池，請使用以下指南。

* **共用圖像庫副本編號。**  對於每個最多 600 個實例的池，我們建議您至少保留一個副本。 例如，如果要創建具有 3000 個 VM 的池，則應保留至少 5 個映射副本。 我們總是建議保留比最低要求更多的副本，以更好的性能。

* **調整超時大小。** 如果池包含固定數量的節點（如果不自動縮放），則根據池大小增加池`resizeTimeout`的屬性。 對於每 1000 個 VM，建議的調整超時至少為 15 分鐘。 例如，對於具有 2000 個 VM 的池，建議的調整超時時間至少為 30 分鐘。

## <a name="next-steps"></a>後續步驟

* 如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。
