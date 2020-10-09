---
title: 容器工作負載
description: 了解如何在 Azure Batch 上透過容器映像執行和縮放應用程式。 建立由支援執行容器工作的計算節點所組成的集區。
ms.topic: how-to
ms.date: 10/06/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9d8776ba8e683cd14c766fead1e7238a6c24d000
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843442"
---
# <a name="run-container-applications-on-azure-batch"></a>在 Azure Batch 上執行容器應用程式

Azure Batch 可讓您在 Azure 上執行及縮放大量批次運算作業。 Batch 工作可以直接在 Batch 集區中的虛擬機器 (節點) 上執行，但您也可以將 Batch 集區設定為在節點上的 Docker 相容容器中執行工作。 本文會示範如何建立可支援執行中容器工作的運算節點集區，以及如何在集區上執行容器工作。

此處的程式碼範例會使用 Batch .NET 和 Python Sdk。 您也可以使用其他 Batch SDK 和工具，包括 Azure 入口網站，來建立啟用容器的 Batch 集區，並執行容器工作。

## <a name="why-use-containers"></a>為何要使用容器？

使用容器可讓您輕鬆地執行 Batch 工作，無須管理環境和相依性，即可執行應用程式。 容器會將應用程式部署為輕量、可攜且自給自足的單位，以便在多種不同的環境中執行。 例如，在本機建置及測試容器，然後將容器映像上傳至 Azure 或其他位置中的登錄。 容器部署模型可確保您應用程式的執行階段環境一律會正確地安裝和設定 (無論您的應用程式裝載在何處)。 Batch 中容器型的工作也可利用非容器工作的功能，包括應用程式套件及資源檔和輸出檔案的管理。

## <a name="prerequisites"></a>Prerequisites

您應該要熟悉容器概念，以及如何建立 Batch 集區與作業。

- **SDK 版本**：自以下版本起的 Batch SDK 可支援容器映像：
  - Batch REST API 2017-09-01.6.0 版
  - Batch .NET SDK 8.0.0 版
  - Batch Python SDK 4.0 版
  - Batch Java SDK 3.0 版
  - Batch Node.js SDK 3.0 版

- **帳戶**：在您的 Azure 訂用帳戶中，您需要建立 Batch 帳戶，並選擇性地建立 Azure 儲存體帳戶。

- **支援的虛擬機器映像**：只有從下一節＜支援的虛擬機器映像＞中所述的映像，使用虛擬機器設定建立的集區，才可支援容器。 如果您提供了自訂映像，請參閱下一節中的考量，以及[使用受控自訂映像來建立虛擬機器的集區](batch-custom-images.md)中的需求。

請記住下列限制：

- Batch 只會對 Linux 集區上執行的容器提供 RDMA 支援。

- 針對 Windows 容器工作負載，建議您為集區選擇多核心 VM 大小。

## <a name="supported-virtual-machine-images"></a>支援的虛擬機器映像

請使用下列其中一個支援的 Windows 或 Linux 映像，來建立適用於容器工作負載的虛擬機器計算節點集區。 如需與 Batch 相容之 Marketplace 映射的詳細資訊，請參閱 [虛擬機器映射的清單](batch-linux-nodes.md#list-of-virtual-machine-images)。

### <a name="windows-support"></a>Windows 支援

Batch 支援有表明支援容器的 Windows 伺服器映像。 這些映像 SKU 名稱的尾端通常是 `-with-containers` 或 `-with-containers-smalldisk`。 此外，[使用 API 列出 Batch 支援的所有映像](batch-linux-nodes.md#list-of-virtual-machine-images)時，如果映像支援 Docker 容器，則會指出 `DockerCompatible` 功能。

您也可以從 Windows 上執行 Docker 的虛擬機器中建立自訂映像。

### <a name="linux-support"></a>Linux 支援

針對 Linux 容器工作負載，Batch 目前支援以下由 Microsoft Azure Batch 在 Azure Marketplace 中發佈的 Linux 映像，而不需要自訂映像。

#### <a name="vm-sizes-without-rdma"></a>無 RDMA 的 VM 大小

- 發行者：`microsoft-azure-batch`
  - 供應項目：`centos-container`
  - 供應項目：`ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>有 RDMA 的 VM 大小

- 發行者：`microsoft-azure-batch`
  - 供應項目：`centos-container-rdma`
  - 供應項目：`ubuntu-server-container-rdma`

這些映像僅支援在 Azure Batch 集區中使用，且適用於 Docker 容器執行。 它們具有：

- 預先安裝的 Docker 相容 [Moby](https://github.com/moby/moby) 容器執行階段

- 預先安裝的 NVIDIA GPU 驅動程式和 NVIDIA 容器執行階段，可簡化 Azure N 系列 VM 上的部署

- 預先安裝/預先設定的映像，對於以 `-rdma` 結尾的映像，支援 Infiniband RDMA VM 大小。 這些映像目前不支援 SR-IOV IB/RDMA VM 大小。

您也可以從與 Batch 相容的其中一個 Linux 發佈上執行 Docker 的虛擬機器建立自訂映像。 如果您選擇提供您自己自訂的 Linux 映像，請參閱[使用受控自訂映像來建立虛擬機器的集區](batch-custom-images.md)中的指示。

如需自訂映像上的 Docker 支援，請安裝 [Docker Community Edition (CE)](https://www.docker.com/community-edition) 或 [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition)。

使用自訂 Linux 映像的其他考量：

- 若要在使用自訂映像時利用 Azure N 系列大小的 GPU 效能，請預先安裝 NVIDIA 驅動程式。 此外，您需要安裝適用於 NVIDIA GPU 的 Docker 引擎公用程式 [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)。

- 若要存取 Azure RDMA 網路，請使用具備 RDMA 功能的虛擬機器大小。 必要的 RDMA 驅動程式會安裝在 Batch 支援的 CentOS HPC 和 Ubuntu 映像上。 若要執行 MPI 工作負載，則可能需要其他設定。 請參閱[在 Batch 集區中使用具備 RDMA 功能或已啟用 GPU 功能的執行個體](batch-pool-compute-intensive-sizes.md)。

## <a name="container-configuration-for-batch-pool"></a>Batch 集區的容器設定

若要啟用 Batch 集區以執行容器工作負載，您必須在集區的 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) 物件中指定 [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 設定。 (本文提供 Batch .NET API 參考的連結。 對應設定位於 [Batch Python](/python/api/overview/azure/batch) API。)

不論是否有預先擷取的容器映像，您都可以建立啟用容器的集區，如下列範例所示。 提取 (或預先擷取) 程序可讓您從 Docker Hub 或網際網路上的另一個容器登錄預先載入容器映像。 為了達到最佳效能，請使用與 Batch 帳戶位於相同區域中的 [Azure 容器登錄](../container-registry/container-registry-intro.md)。

預先擷取容器映像的好處是，當工作第一次開始執行時，不必等待容器映像下載。 容器設定會在集區建立時將容器映像提取到虛擬機器中。 然後，在集區上執行的工作就可以參考容器映像清單和容器執行選項。

### <a name="pool-without-prefetched-container-images"></a>沒有預先擷取容器映像的集區

若要在未預先提取容器映射的情況下設定啟用容器的集區，請定義 `ContainerConfiguration` 和 `VirtualMachineConfiguration` 物件，如下列範例所示。 這些範例會使用 Ubuntu Server 來 Azure Batch Marketplace 中的容器集區映射。

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>為容器設定預先擷取映像

若要在集區上預先擷取容器映像，請將容器映像清單 (`container_image_names`，在 Python 中) 新增至 `ContainerConfiguration`。

下列基本 Python 範例示範如何從 [Docker 中樞](https://hub.docker.com)預先擷取標準 Ubuntu 容器映像。

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

下列 C# 範例假設您想要從 [Docker 中樞](https://hub.docker.com)預先擷取 TensorFlow 映像。 此範例包含在集區節點上 VM 主機中執行的啟動工作。 舉例而言，您可以在主機中執行啟動工作，以掛接可以從容器存取的檔案伺服器。

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>從私人容器登錄中預先擷取映像

您也可以透過驗證私人容器登錄伺服器，來預先擷取容器映像。 在下列範例中， `ContainerConfiguration` 和 `VirtualMachineConfiguration` 物件會從私用 Azure container registry 預先提取私人 TensorFlow 映射。 映像參考與先前範例相同。

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>工作的容器設定

若要在已啟用容器的集區上執行容器工作，請指定容器專屬設定。 設定包括要使用的映像、登錄及容器執行選項。

- 使用工作類別的 `ContainerSettings` 屬性來設定容器專屬設定。 這些設定會由 [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) 類別定義。 請注意，`--rm` 容器選項不需要額外的 `--runtime` 選項，因為這是由 Batch 負責。

- 如果您在容器映像上執行工作，[雲端工作](/dotnet/api/microsoft.azure.batch.cloudtask)和[作業管理員工作](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)會需要容器設定。 但是，[啟動工作](/dotnet/api/microsoft.azure.batch.starttask)、[作業準備工作](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)和[作業解除工作](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)不需要容器設定 (也就是這些工作可以在容器內容中執行或直接在節點上執行)。

- 若為 Windows，必須將 [ElevationLevel](/rest/api/batchservice/task/add#elevationlevel) 設定為來執行工作 `admin` 。 

- 針對 Linux，Batch 會將使用者/群組的許可權對應至容器。 如果容器內任何資料夾的存取權都需要系統管理員許可權，您可能需要以系統管理員提高許可權層級的集區範圍來執行工作。 這可確保 Batch 在容器內容中以 root 的方式執行工作。 否則，非系統管理員的使用者可能無法存取這些資料夾。

- 如果容器集區具有已啟用 GPU 功能的硬體，Batch 將會自動啟用適用于容器工作的 GPU，因此您不應該包含 `–gpus` 引數。

### <a name="container-task-command-line"></a>容器工作命令列

當您執行容器工作時，Batch 會自動使用 [docker create](https://docs.docker.com/engine/reference/commandline/create/) \(英文\) 命令，利用工作中指定的映像來建立容器。 Batch 接著會控制容器中的工作執行。

就像使用非容器的 Batch 工作，您要針對容器工作設定命令列。 由於 Batch 會自動建立容器，因此，命令列只會指定將在容器中執行的一或多個命令。

如果已使用 [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) \(英文\) 指令碼來設定 Batch 工作的容器映像，您可以設定命令列來使用預設進入點或覆寫它：

- 若要使用容器映像的預設進入點，請將工作命令列設為空字串 `""`。

- 若要覆寫預設進入點，或者映像沒有進入點，則設定適用於容器的命令列，例如 `/app/myapp` 或 `/bin/sh -c python myscript.py`。

選擇性的 [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) 是您提供給 `docker create` 命令的額外引數，Batch 會使用此命令來建立並執行容器。 例如，若要設定容器的工作目錄，請設定 `--workdir <directory>` 選項。 如需其他選項，請參閱 [docker create](https://docs.docker.com/engine/reference/commandline/create/) \(英文\) 參考。

### <a name="container-task-working-directory"></a>容器工作工作目錄

Batch 容器工作會在容器的工作目錄中執行，與 Batch 為一般 (非容器) 工作設定的目錄非常類似。 請注意，如果此工作目錄設定於映像中，則不同於 [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) \(英文\)，否則為預設的容器工作目錄 (Windows 容器上的 `C:\` 或 Linux 容器上的 `/`)。

針對 Batch 容器工作：

- 以遞迴方式位於主機節點 (Azure Batch 目錄的根目錄) 上 `AZ_BATCH_NODE_ROOT_DIR` 下方的所有目錄都會對應至容器
- 所有工作環境變數都會對應至容器
- 節點上工作的工作目錄 `AZ_BATCH_TASK_WORKING_DIR` 會設定為與一般工作相同，並對應至容器。

這些對應可讓您以與非容器工作相同的方式來使用容器工作。 例如，使用應用程式封裝安裝應用程式、從 Azure 儲存體存取資源檔、使用工作環境設定，以及在容器停止之後保存工作輸出檔案。

### <a name="troubleshoot-container-tasks"></a>進行容器工作的疑難排解

如果您的容器工作並未如預期般執行，您可能需要取得容器映像的 WORKDIR 或進入點設定的相關資訊。 若要查看設定，請執行 [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/) \(英文\) 命令。

視需要根據映像來調整容器工作的設定：

- 在工作命令列中指定絕對路徑。 如果針對工作命令列使用映像的預設進入點，請確定會設定絕對路徑。
- 在工作的容器執行選項中，變更工作目錄以符合映像中的 WORKDIR。 例如，設定 `--workdir /app`。

## <a name="container-task-examples"></a>容器工作範例

下列 Python 程式碼片段顯示的基本命令列，會在從提取自 Docker 中樞之虛擬映像建立的容器中執行。 在這裡，`--rm` 容器選項會在工作完成之後移除容器，而 `--workdir` 選項會設定工作目錄。 此命令列會使用簡單殼層命令來覆寫容器進入點，以將小型檔案寫入至主機上工作的工作目錄。

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

下列 C# 範例會示範雲端工作的基本容器設定：

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>後續步驟

- 若要透過 [Shipyard 配方](https://github.com/Azure/batch-shipyard/tree/master/recipes)輕鬆地在 Azure Batch 上部署容器工作負載，請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 工具組。
- 如需有關在 Linux 上安裝和使用 Docker CE 的詳細資訊，請參閱 [docker](https://docs.docker.com/engine/installation/) 檔。
- 瞭解如何 [使用受控自訂映射來建立虛擬機器的集](batch-custom-images.md)區。
- 深入了解 [Moby 專案](https://mobyproject.org/)，這是一個用來建立容器型系統的架構。
