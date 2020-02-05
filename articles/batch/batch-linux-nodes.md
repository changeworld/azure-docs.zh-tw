---
title: 在虛擬機器計算節點上執行 Linux - Azure Batch | Microsoft Docs
description: 了解如何在 Azure Batch 中處理您的 Linux 虛擬機器集區的平行計算工作負載。
services: batch
documentationcenter: python
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3691790b2e47ef43c6742fa912aff8d7777900f8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023696"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>在 Batch 集區中佈建 Linux 計算節點

您可以使用 Azure Batch 同時在 Linux 和 Windows 虛擬機器上執行平行計算工作負載。 本文詳細說明如何同時使用[Batch Python][py_batch_package]和[batch .net][api_net]用戶端程式庫，在 Batch 服務中建立 Linux 計算節點的集區。

> [!NOTE]
> 在 2017 年 7 月 5 日之後建立的所有 Batch 集區都支援應用程式套件。 只有在使用雲端服務設定建立集區時，在 2016 年 3 月 10 日與 2017 年 7 月 5 日之間所建立的 Batch 集區上才支援應用程式套件。 在 2016 年 3 月 10 日之前建立的 Batch 集區不支援應用程式套件。 如需使用應用程式套件將應用程式部署至 Batch 節點的詳細資訊，請參閱[使用 Batch 應用程式套件將應用程式部署至計算節點](batch-application-packages.md)。
>
>

## <a name="virtual-machine-configuration"></a>虛擬機器設定
在 Batch 中建立計算節點集區時，有兩個選項可選取節點大小和作業系統︰雲端服務組態和虛擬機器組態。

**雲端服務組態**「只」提供 Windows 計算節點。 可用的計算節點大小列於[雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)，而可用的作業系統則列於 [Azure 客體 OS 版次與 SDK 相容性矩陣](../cloud-services/cloud-services-guestos-update-matrix.md)。 在建立包含 Azure 雲端服務節點的集區時，您要指定節點大小和其 OS 系列，先前提到的文章中會說明這些內容。 針對 Windows 計算節點的集區，最常使用的是雲端服務。

**虛擬機器組態** 可提供適用於計算節點的 Linux 和 Windows 映像。 可用的計算節點大小列於 [Azure 中的虛擬機器大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) 和 [Azure 中的虛擬機器大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)。 在建立包含虛擬機器組態節點的集區時，您必須指定節點大小、虛擬機器映像參考以及要在節點上安裝的 Batch 節點代理程式 SKU。

### <a name="virtual-machine-image-reference"></a>虛擬機器映像參考

Batch 服務使用[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)來提供虛擬機器設定中的計算節點。 您可以指定[Azure Marketplace][vm_marketplace]中的映射，或提供您已備妥的自訂映射。 如需自訂映射的詳細資訊，請參閱[使用共用映射資源庫建立集](batch-sig-images.md)區。

設定虛擬機器映像參考時，您會指定虛擬機器映像的屬性。 建立虛擬機器映像參考時，會需要下列屬性︰

| **映像參考屬性** | **範例** |
| --- | --- |
| 發佈者 |Canonical |
| 供應項目 |UbuntuServer |
| SKU |14.04.4-LTS |
| 版本 |latest |

> [!TIP]
> 您可以在[使用 CLI 或 PowerShell 在 Azure 中巡覽並選取 Linux 虛擬機器映像](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中深入了解這些屬性，以及如何列出 Marketplace 映像。 請注意，並非所有 Marketplace 映像目前都與 Batch 相容。 如需詳細資訊，請參閱 [節點代理程式 SKU](#node-agent-sku)。
>
>

### <a name="node-agent-sku"></a>節點代理程式 SKU
Batch 節點代理程式是一項程式，會在集區中的每個節點上執行，並在節點與 Batch 服務之間提供命令和控制介面。 節點代理程式對不同作業系統有不同的實作方式，稱為 SKU。 基本上，建立虛擬機器組態時，您必須先指定虛擬機器映像參考，然後指定要在其上安裝映像的代理程式節點。 一般而言，每個節點代理程式可與多個虛擬機器映像 SKU 相容。 以下是一些節點代理程式的 SKU 的範例︰

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> 並非 Marketplace 中所有可用的虛擬機器映像都與目前可用的 Batch 節點代理程式相容。 使用 Batch SDK 來列出可用的節點代理程式 SKU 和其相容的虛擬機器映像。 如需詳細資訊及如何在執行階段擷取有效映像清單的範例，請參閱本文稍後的[虛擬機器映像清單](#list-of-virtual-machine-images)。
>
>

## <a name="create-a-linux-pool-batch-python"></a>建立 Linux 集區︰Batch Python
下列程式碼片段顯示如何使用[適用于 Python 的 Microsoft Azure Batch 用戶端程式庫][py_batch_package]，來建立 Ubuntu Server 計算節點的集區範例。 您可以在 azure 上找到 Batch Python 模組的參考檔。如需取得 batch[套件][py_batch_docs]，請參閱閱讀檔。

此程式碼片段會明確建立[ImageReference][py_imagereference] ，並指定其每一個屬性（發行者、供應專案、SKU、版本）。 不過，在實際執行的程式碼中，建議您使用[list_node_agent_skus][py_list_skus]方法，在執行時間判斷並選取可用的映射和節點代理程式 SKU 組合。

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
vm_size = "STANDARD_A1"
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
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

如先前所述，我們建議您不要明確地建立[ImageReference][py_imagereference] ，而是使用[list_node_agent_skus][py_list_skus]方法，從目前支援的節點代理程式/Marketplace 映射組合中動態選取。 下列 Python 程式碼片段說明這個方法的使用方式。

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>建立 Linux 集區︰Batch .NET
下列程式碼片段顯示如何使用[Batch .net][nuget_batch_net]用戶端程式庫來建立 Ubuntu Server 計算節點集區的範例。 您可以在 docs.microsoft.com 上找到[Batch .net 參考檔][api_net]。

下列程式碼片段會使用[PoolOperations][net_pool_ops]。要從目前支援的 Marketplace 映射和節點代理程式 SKU 組合清單中選取的[ListNodeAgentSkus][net_list_skus]方法。 這項技術最理想，因為支援的組合清單可能會隨著時間變更。 最常見的是新增支援的組合。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

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

雖然先前的程式碼片段會使用[PoolOperations][net_pool_ops]。[ListNodeAgentSkus][net_list_skus]方法可動態列出並從支援的映射和節點代理程式 SKU 組合中選取（建議使用），您也可以明確地設定[ImageReference][net_imagereference] ：

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>虛擬機器映像的清單
下表列出本文最後一次更新時，與可用 Batch 節點代理程式相容的 Marketplace 虛擬機器映像。 請務必注意，此清單並非永久不變，因為可能隨時新增或移除映像和節點代理程式。 我們建議您的 Batch 應用程式和服務一律使用[list_node_agent_skus][py_list_skus] （Python）或[ListNodeAgentSkus][net_list_skus] （Batch .net），以判斷並從目前可用的 sku 中選取。

> [!WARNING]
> 下列清單可能會隨時變更。 一律使用 Batch API 中提供的 **清單節點代理程式 SKU** 方法，在執行 Batch 作業時，列出相容的虛擬機器和節點代理程式的 SKU。
>
>

| **發行者** | **供應項目** | **映像 SKU** | **版本** | **節點代理程式 SKU 識別碼** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| batch | rendering-centos73 | 轉譯 | latest | batch.node.centos 7 |
| batch | rendering-windows2016 | 轉譯 | latest | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | latest | batch.node.debian 9 |
| Credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | latest | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>使用 SSH 連線至 Linux 節點
在開發期間或問題進行疑難排解時，您可能會發現需要登入您的集區中的節點。 不同於 Windows 計算節點，您無法使用遠端桌面通訊協定 (RDP) 連線到 Linux 節點。 相反地，Batch 服務可讓您遠端連線的每個節點上的 SSH 存取。

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

以下是包含四個 Linux 節點之集區的先前程式碼的範例輸出︰

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

在節點上建立使用者時，不要使用密碼，而是可以指定 SSH 公開金鑰。 在 Python SDK 中，使用[ComputeNodeUser][py_computenodeuser]上的**ssh_public_key**參數。 在 .NET 中，使用[ComputeNodeUser][net_computenodeuser]。[SshPublicKey][net_ssh_key]屬性。

## <a name="pricing"></a>定價
Azure Batch 採用 Azure 雲端服務和 Azure 虛擬機器技術。 Batch 服務本身為免費提供，這表示您僅需支付 Batch 解決方案所使用的計算資源。 當您選擇**雲端服務**設定時，會根據[雲端服務定價][cloud_services_pricing]結構向您收費。 當您選擇 [**虛擬機器**設定] 時，會根據[虛擬機器定價][vm_pricing]結構向您收費。 

如果您是使用[應用程式套件](batch-application-packages.md)將應用程式部署到您的 Batch 節點，也需要支付應用程式套件使用的 Azure 儲存體資源。 一般情況下，Azure 儲存體成本很低。 

## <a name="next-steps"></a>後續步驟

GitHub 上的[azure 批次範例][github_samples]存放庫中的[Python 程式碼範例][github_samples_py]包含可示範如何執行一般批次作業的腳本，例如集區、作業和工作建立。 Python 範例隨附的[讀我檔案][github_py_readme]包含如何安裝必要套件的詳細資料。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
