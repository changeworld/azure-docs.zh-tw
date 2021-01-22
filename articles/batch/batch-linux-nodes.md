---
title: 在虛擬機器計算節點上執行 Linux
description: 瞭解如何在 Azure Batch 的 Linux 虛擬機器集區上處理平行計算工作負載。
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683695"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>在 Batch 集區中佈建 Linux 計算節點

您可以使用 Azure Batch 同時在 Linux 和 Windows 虛擬機器上執行平行計算工作負載。 本文將詳細說明如何使用 [Batch Python](https://pypi.python.org/pypi/azure-batch) 和 [Batch .NET](/dotnet/api/microsoft.azure.batch) 用戶端程式庫，在 Batch 服務中建立 Linux 計算節點的集區。 

## <a name="virtual-machine-configuration"></a>虛擬機器設定

在 Batch 中建立計算節點集區時，有兩個選項可選取節點大小和作業系統︰雲端服務組態和虛擬機器組態。 [虛擬機器](nodes-and-pools.md#virtual-machine-configuration) 設定集區是由 Azure vm 所組成，可從 Linux 或 Windows 映像建立。 當您使用虛擬機器設定建立集區時，您會指定 [可用的計算節點大小](../virtual-machines/sizes.md)、要安裝在節點上的虛擬機器映射參考，以及 Batch-節點代理程式 SKU (在每個節點上執行的程式，並提供節點與 Batch 服務) 之間的介面。

### <a name="virtual-machine-image-reference"></a>虛擬機器映像參考

Batch 服務使用[虛擬機器擴展集](../virtual-machine-scale-sets/overview.md)來提供虛擬機器設定中的計算節點。 您可以從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)指定映射，或 [使用共用映射庫來準備自訂映射](batch-sig-images.md)。

當您建立虛擬機器映射參考時，您必須指定下列屬性：

| **影像參考屬性** | **範例** |
| --- | --- |
| 發行者 |Canonical |
| 供應項目 |UbuntuServer |
| SKU |18.04-LTS |
| 版本 |最新 |

> [!TIP]
> 您可以深入瞭解這些屬性，以及如何 [使用 Azure CLI 在 Azure Marketplace 的 Find LINUX VM 映射](../virtual-machines/linux/cli-ps-findimage.md)中指定 Marketplace 映射。 請注意，某些 Marketplace 映射目前不與 Batch 相容。

### <a name="list-of-virtual-machine-images"></a>虛擬機器映像的清單

並非所有 Marketplace 映射都與目前可用的 Batch-節點代理程式相容。 若要列出 Batch 服務及其對應節點代理程式 Sku 的所有支援的 Marketplace 虛擬機器映射，請使用 [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python) 、 [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .net) ，或另一個語言 SDK 中的對應 API。

### <a name="node-agent-sku"></a>節點代理程式 SKU

[Batch-節點代理](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)程式是在集區中的每個節點上執行的程式，並在節點與 Batch 服務之間提供命令和控制介面。 節點代理程式對不同作業系統有不同的實作方式，稱為 SKU。 基本上，建立虛擬機器組態時，您必須先指定虛擬機器映像參考，然後指定要在其上安裝映像的代理程式節點。 一般而言，每個節點代理程式可與多個虛擬機器映像 SKU 相容。 以下是一些節點代理程式的 SKU 的範例︰

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

## <a name="create-a-linux-pool-batch-python"></a>建立 Linux 集區：Batch Python

下列程式碼片段舉例示範如何使用 [Python 適用的 Microsoft Azure Batch 用戶端程式庫](https://pypi.python.org/pypi/azure-batch)來建立 Ubuntu Server 計算節點的集區。 如需 Batch Python 模組的詳細資訊，請參閱 [參考檔](/python/api/overview/azure/batch)。

此程式碼片段會明確建立 [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference)，並指定其每一個屬性 (發行者、供應項目、SKU、版本)。 不過，在實際執行的程式碼中，我們建議您在執行時間使用 [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) 方法，從可用的映射和節點代理程式 SKU 組合中進行選取。

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

如先前所述，我們建議使用 [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) 方法，從目前支援的節點代理程式/Marketplace 映射組合中動態選取 (而不是明確地) 建立 [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) 。 下列 Python 程式碼片段說明這個方法的使用方式。

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>建立 Linux 集區：Batch .NET

下列程式碼片段舉例示範如何使用 [Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) 用戶端程式庫來建立 Ubuntu Server 計算節點的集區。 如需 Batch .NET 的詳細資訊，請參閱 [參考檔](/dotnet/api/microsoft.azure.batch)。

下列程式碼片段會使用 [>batchclient.pooloperations.createpool. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) 方法，從目前支援的 Marketplace 映射和節點代理程式 SKU 組合清單中選取。 這項技術是建議的作法，因為支援的組合清單可能會在一段時間內變更。 最常見的是新增支援的組合。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

雖然先前的程式碼片段會使用 [>batchclient.pooloperations.createpool. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) 方法，從支援的映射和節點代理程式 SKU 組合進行動態清單和選取， (建議的) ，您也可以明確地設定 [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) ：

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>使用 SSH 連線至 Linux 節點

在開發期間或問題進行疑難排解時，您可能會發現需要登入您的集區中的節點。 不同于 Windows 計算節點，您無法使用遠端桌面通訊協定 (RDP) 連接到 Linux 節點。 相反地，Batch 服務可讓您遠端連線的每個節點上的 SSH 存取。

下列 Python 程式碼片段會在集區中的每個節點上建立使用者 (遠端連線所需)。 接著，它會列印每個節點的安全殼層 (SSH) 連線資訊。

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

此程式碼會有類似下列範例的輸出。 在此情況下，集區包含四個 Linux 節點。

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

在節點上建立使用者時，不要使用密碼，而是可以指定 SSH 公開金鑰。 在 Python SDK 中，使用 [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser) 上的 **ssh_public_key** 參數。 在 .NET 中，請使用 [>computenodeuser SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) 屬性。

## <a name="pricing"></a>定價

Azure Batch 採用 Azure 雲端服務和 Azure 虛擬機器技術。 Batch 服務本身為免費提供，這表示您僅需支付 Batch 解決方案所使用的計算資源 (及所需的相關費用)。 選擇 **虛擬機器組態** 時，將會根據 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)結構向您收費。

如果您是使用[應用程式套件](batch-application-packages.md)將應用程式部署到您的 Batch 節點，也需要支付應用程式套件使用的 Azure 儲存體資源。

## <a name="next-steps"></a>後續步驟

- 探索[azure batch 範例 GitHub 存放庫](https://github.com/Azure/azure-batch-samples)中的[Python 程式碼範例](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)，以瞭解如何執行一般的批次作業，例如集區、作業和工作建立。 Python 範例隨附的[讀我檔案](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md)會詳細說明如何安裝必要的套件。
- 瞭解如何搭配 Batch 使用 [低優先順序 vm](batch-low-pri-vms.md) 。
