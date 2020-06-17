---
title: 從受控映像佈建自訂集區
description: 從受控映像資源建立 Batch 集區，以佈建計算節點來為應用程式提供軟體和資料。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: fbb336ff9d3d53cc53004c577e291afdba7702f6
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847985"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>使用受控映像來建立虛擬機器的集區

若要為 Batch 集區的虛擬機器 (VM) 建立自訂映像，您可以使用受控映像來建立[共用映像庫](batch-sig-images.md)。 也支援只使用受控映像，但僅適用於截至 2019-08-01 (含) 為止的 API 版本。

> [!IMPORTANT]
> 在大部分情況下，您應該使用共用映像庫來建立自訂映像。 共用映像庫可讓您更快速佈建集區、調整更多 VM，以及更可靠地佈建 VM。 若要深入了解，請參閱[使用共用映像庫來建立自訂集區](batch-sig-images.md)。

## <a name="prerequisites"></a>Prerequisites

- **受控映像資源**。 若要使用自訂映像來建立虛擬機器集區，您必須在與 Batch 帳戶相同的 Azure 訂用帳戶和區域中，擁有或建立受控映像資源。 您應該從 VM 之 OS 磁碟 (以及視需要從其連結之資料磁碟) 的快照集建立該映像。
  - 針對您建立的每個集區使用唯一的自訂映像。
  - 若要使用 Batch API 以映像建立集區，請指定映像的**資源識別碼**，其形式為 `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`。
  - 受控映像資源應該在集區存留期內都存在，以供集區調升規模，並可在刪除集區之後移除。

- **Azure Active Directory (Azure AD) 驗證**。 Batch 用戶端 API 必須使用 Azure AD 驗證。 Azure Batch 對於 Azure AD 的支援記載於[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)中。

## <a name="prepare-a-custom-image"></a>準備自訂映像

在 Azure 中，您可以從下列來源準備受控映像：

- Azure VM OS 和資料磁碟的快照集
- 具有受控磁碟的一般化 Azure VM
- 已上傳至雲端的一般化內部部署 VHD

若要使用受控映像來可靠地調整 Batch 集區，建議「只」使用第一個方法建立受控映像：使用 VM 磁碟的快照集。 下列步驟說明如何準備 VM、建立快照集，然後從快照集建立受控映像。

### <a name="prepare-a-vm"></a>準備 VM

如果您要為映像建立新的 VM，請使用 Batch 支援的第一方 Azure Marketplace 映像，作為受控映像的基礎映像。 只有第一方映像可以作為基礎映像。 若要取得 Azure Batch 支援哪些 Azure Marketplace 映像參考的完整清單，請參閱[列出節點代理程式 SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 作業。

> [!NOTE]
> 您無法使用具有額外授權和購買條款的第三方映像作為您的基礎映像。 如需這些 Marketplace 映像的相關資訊，請參閱 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms)或 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) VM 的指導方針。

- 請確定以受控磁碟建立 VM。 當您建立 VM 時，這是預設的儲存體設定。
- 不要在 VM 上安裝 Azure 延伸模組，例如「自訂指令碼」延伸模組。 如果映像包含預先安裝的延伸模組，則 Azure 在部署 Batch 集區時可能會遇到問題。
- 使用連結的資料磁碟時，您必須在 VM 內掛接並格式化磁碟，才能使用這些磁碟。
- 確定您提供的基本 OS 映像使用預設的暫存磁碟機。 Batch 節點代理程式目前需要有預設的暫存磁碟機。
- 一旦 VM 開始執行之後，請透過 RDP (適用於 Windows) 或 SSH (適用於 Linux) 向它連線。 安裝任何必要的軟體或複製所需的資料。  

### <a name="create-a-vm-snapshot"></a>建立 VM 快照集

快照集是完整的 VHD 唯讀複本。 若要建立 VM 之 OS 或資料磁碟的快照集，您可以使用 Azure 入口網站或命令列工具。 如需了解建立快照集的步驟與選項，請參閱適用於 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 或 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 的指導方針。

### <a name="create-an-image-from-one-or-more-snapshots"></a>從一或多個快照集建立映像

若要從快照集建立受控映像，請使用 Azure 命令列工具，例如 [az image create](/cli/azure/image) 命令。 您可以指定 OS 磁碟快照集，以及視需要指定一或多個資料磁碟快照集，來建立映像。

## <a name="create-a-pool-from-a-custom-image"></a>從自訂映像建立集區

找到受控映像的資源識別碼之後，請從該映像建立自訂映像集區。 下列步驟示範如何使用 Batch 服務或 Batch 管理來建立自訂映像集區。

> [!NOTE]
> 請確定您用於 Azure AD 驗證的身分識別具有映像資源的權限。 請參閱[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)。
>
> 在集區的存留期內，受控映像的資源必須存在。 如果刪除基礎資源，則無法調整集區。

### <a name="batch-service-net-sdk"></a>Batch 服務 .NET SDK

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>Batch 管理 REST API

REST API URI

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

要求本文

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>大型集區的考量

如果您打算使用自訂映像來建立含有數百個或更多 VM 的集區，請務必依照先前的指導方針，使用從 VM 快照集建立的映像。

另請注意下列考量：

- **大小限制** - 當您使用自訂映像時，Batch 會將集區大小限制在 2500 個專用計算節點，或 1000 個低優先順序節點。

  如果您使用同一個映像 (或以相同基礎快照集為基礎的多個映像) 來建立多個集區，則集區中的計算節點總數不可超過上述限制。 我們不建議將一個映像或其基礎快照集用於多個集區。

  如果您使用[輸入 NAT 集區](pool-endpoint-configuration.md)來設定集區，則可能會降低限制。

- **調整逾時** - 如果您的集區包含固定數目的節點 (不自動調整規模)，請將集區的 resizeTimeout 屬性值提高到 20-30 分鐘之類的值。 如果您的集區在逾時期間內未達到其目標大小，請執行另一個[調整大小作業](/rest/api/batchservice/pool/resize)。

  如果您規劃的集區含有超過 300 個計算節點，您可能需要多次調整集區大小，才能達到目標大小。
  
[共用映像庫](batch-sig-images.md)可讓您使用自訂映像及更多的共用映像複本，建立較大的集區。 使用共用映像時，集區達到穩定狀態的時間可加快到 25%，且 VM 閒置延遲最多可縮短 30%。

## <a name="considerations-for-using-packer"></a>使用 Packer 的考量

只有透過使用者訂用帳戶模式 Batch 帳戶，才能使用 Packer 直接建立受控映像資源。 若為 Batch 服務模式帳戶，您必須先建立 VHD，然後將 VHD 匯入至受控映像資源。 根據集區配置模式而定 (使用者訂用帳戶或 Batch 服務)，建立受控映像資源的步驟會有所不同。

如有任何集區參考自訂映射，在這些集區的存留期內，請確定用來建立受控映像的資源始終存在。 否則，可能會導致集區配置失敗和/或調整大小失敗。

如果移除映像或基礎資源，您可能會收到類似下列的錯誤：`There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`。 如果您收到此錯誤，請確定未移除基礎資源。

如需使用 Packer 建立 VM 的詳細資訊，請參閱[使用 Packer 建立 Linux 映像](../virtual-machines/linux/build-image-with-packer.md)或[使用 Packer 組建 Windows 映像](../virtual-machines/windows/build-image-with-packer.md)。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[共用映像庫](batch-sig-images.md)來建立自訂集區。
- 如需更深入的 Batch 概觀，請參閱 [Batch 服務工作流程和資源](batch-service-workflow-features.md)。
