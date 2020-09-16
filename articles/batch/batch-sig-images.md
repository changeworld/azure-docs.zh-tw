---
title: 使用共用映射庫來建立自訂映射集區
description: 自訂映射集區是設定計算節點以執行 Batch 工作負載的有效方式。
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 31fcbff50a2a66aec1643f1bac351e0401205861
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605187"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>使用共用映射庫來建立自訂映射集區

當您使用虛擬機器設定建立 Azure Batch 集區時，需指定 VM 映像，以提供集區中每個計算節點的作業系統。 您可以使用支援的 Azure Marketplace 映射來建立虛擬機器集區，或使用 [共用映射庫映射](../virtual-machines/windows/shared-image-galleries.md)建立自訂映射。

## <a name="benefits-of-the-shared-image-gallery"></a>共用映像庫的優點

為您的自訂映像使用共用映像庫時，您可以控制作業系統類型和設定，以及資料磁碟的類型。 共用映像可能包含應用程式和參考資料，只要將其佈建在所有 Batch 集區節點上便可使用。

您也可以視需要為環境準備多個映像版本。 當您使用映像版本來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。

使用共用映像可以節省準備集區計算節點以執行 Batch 工作負載的時間。 您可以使用 Azure Marketplace 映像，並且在佈建後於每個節點上安裝軟體，但使用共用映像通常更有效率。 此外，您可以為共用映像指定多個複本，如此一來，當您建立具有許多 VM 的集區 (超過 600 個 VM) 時，就可以節省建立集區的時間。

使用專為您的案例設定的共用映像有幾個優點：

- **跨區域使用相同映像。** 您可以跨不同區域來建立共用映像複本，讓您的所有集區都能使用相同映像。
- **設定作業系統 (OS)** 。 您可以自訂映像作業系統磁碟的設定。
- **預先安裝應用程式。** 在 OS 磁碟上預先安裝應用程式，與在佈建計算節點之後使用開始工作來安裝應用程式相比，這樣更有效率且更不容易出錯。
- **一次複製大量資料。** 藉由將靜態資料複製到受控映像的資料磁碟，使靜態資料成為受控共用映像的一部分。 這只需要執行一次，就能將資料提供給集區的每個節點。
- **讓集區變大。** 使用共用映像庫時，您可以使用自訂映像及更多的共用映像複本來建立較大的集區。
- **比僅使用受控映射作為自訂映射的效能更佳。** 針對共用映射自訂映射集區，達到穩定狀態的時間最多可快25%，而 VM 閒置延遲最多可達30%。
- **對映像進行版本控制和分組，以便管理。** 映像群組定義包含建立映像的原因、目標作業系統，以及使用映像的相關資訊。 群組映像可讓您更輕鬆地管理映像。 如需詳細資訊，請參閱[映像定義](../virtual-machines/windows/shared-image-galleries.md#image-definitions)。

## <a name="prerequisites"></a>Prerequisites

> [!NOTE]
> 您需要使用 Azure AD 進行驗證。 如果您使用共用金鑰驗證，則會收到驗證錯誤。  

- **Azure Batch 帳戶**。 若要建立 Batch 帳戶，請參閱使用 [Azure 入口網站](quick-create-portal.md)或 [Azure CLI](quick-create-cli.md) 的 Batch 快速入門。

- **共用映像庫的映像**。 若要建立共用映像，您必須擁有或建立受控映像資源。 您應該從 VM 之 OS 磁碟 (以及視需要從其連結之資料磁碟) 的快照集建立該映像。

> [!NOTE]
> 如果共用映射與 Batch 帳戶不在相同的訂用帳戶中，您必須為該訂用帳戶 [註冊 Microsoft.Batch 資源提供者](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) 。 這兩個訂用帳戶必須位於相同的 Azure AD 租使用者中。
>
> 映射可以位於不同的區域中，只要它的複本位於與您的 Batch 帳戶相同的區域中。

如果您使用 Azure AD 應用程式來建立具有共用映射庫映射的自訂映射集區，則該應用程式必須已被授與 [Azure 內建角色](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) ，讓它能夠存取共用映射。 您可以藉由流覽至共用映射、選取 [ **存取控制] (IAM) ** 並新增應用程式的角色指派，在 Azure 入口網站中授與此存取權。

## <a name="prepare-a-shared-image"></a>準備共用映射

在 Azure 中，您可以從受控映射準備共用映射，您可以從下列建立：

- Azure VM OS 和資料磁碟的快照集
- 具有受控磁碟的一般化 Azure VM
- 已上傳至雲端的一般化內部部署 VHD

> [!NOTE]
> Batch 目前僅支援一般化共用映像。 您目前無法從特製化的共用映像建立自訂映像集區。

下列步驟將說明如何準備 VM、建立快照集，然後從快照集建立映像。

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

若要從快照集建立受控映像，請使用 Azure 命令列工具，例如 [az image create](/cli/azure/image) 命令。 藉由指定 OS 磁碟快照集，以及視需要指定一或多個資料磁碟快照集，來建立映像。

### <a name="create-a-shared-image-gallery"></a>建立共用映像庫

成功建立受控映像後，您必須建立共用映像庫，好讓您的自訂映像可供使用。 若要了解如何為您的映像建立共用映像庫，請參閱[使用 Azure CLI 建立共用映像庫](../virtual-machines/shared-images-cli.md)，或[使用 Azure 入口網站建立共用映像庫](../virtual-machines/linux/shared-images-portal.md)。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>使用 Azure CLI 從共用映像建立集區

若要使用 Azure CLI 從共用映像建立集區，請使用 `az batch pool create` 命令。 在 [`--image`] 欄位中指定共用映像識別碼。 請確定 OS 類型和 SKU 符合 `--node-agent-sku-id` 所指定的版本

> [!NOTE]
> 您需要使用 Azure AD 進行驗證。 如果您使用共用金鑰驗證，則會收到驗證錯誤。  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>使用 C# 從共用映像建立集區

或者，您可以使用 C# SDK，從共用映像建立集區。

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

## <a name="create-a-pool-from-a-shared-image-using-python"></a>使用 Python 從共用映像建立集區

您也可以使用 Python SDK，從共用映像建立集區： 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>使用 Azure 入口網站從共用映像建立集區

在 Azure 入口網站中，您可以使用下列步驟從共用映像建立集區。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 移至 [Batch 帳戶] 並選取您的帳戶。
1. 依序選取 [集區] 和 [新增]，以建立新的集區。
1. 在 [映像類型] 區段中，選取 [共用映像庫]。
1. 使用受控映像的相關資訊來完成其餘各節。
1. 選取 [確定]。

![使用入口網站從共用映像建立集區。](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>大型集區的考量

如果您打算使用共用映像來建立具有數百部、數千部或更多 VM 的集區，請使用下列指導方針。

- **共用映像庫複本數目。**  針對具有最多 600 個執行個體的每個集區，建議您至少保留一個複本。 例如，如果您要建立包含 3000 部 VM 的集區，您應該至少保留 5 個映像複本。 為獲得更好的效能，我們一律建議您保留高於最低需求的複本數目。

- **調整大小逾時。** 如果您的集區包含固定數目的節點 (如果不進行自動調整)，請根據集區大小來增加集區的 `resizeTimeout` 屬性。 針對每 1000 部 VM，調整大小的建議逾時時間至少為 15 分鐘。 例如，針對具有 2000 部 VM 的集區，調整大小的建議逾時時間應至少為 30 分鐘。

## <a name="next-steps"></a>後續步驟

- 如需更深入的 Batch 概觀，請參閱 [Batch 服務工作流程和資源](batch-service-workflow-features.md)。
- 深入了解[共用映像庫](../virtual-machines/windows/shared-image-galleries.md)。
