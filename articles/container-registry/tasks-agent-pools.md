---
title: 使用專用集區來執行工作（task）
description: 在登錄中設定 (代理程式組件區) 專用的計算集區，以執行 Azure Container Registry 工作。
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: eeb9a71854f52da5c1a9f4befae93c377ad67b05
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920302"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>在專用的代理程式組件區上執行 ACR 工作

設定 Azure 管理的 VM 集區 (*代理程式* 集區) ，以在專用計算環境中執行您的 [Azure Container Registry][acr-tasks] 工作。 在登錄中設定一或多個集區之後，您可以選擇一個集區來執行工作，以取代服務的預設計算環境。

代理程式組件區提供：

- **虛擬網路支援** -將代理程式組件區指派給 Azure vnet，以提供對 VNet 中資源的存取權，例如容器登錄、金鑰保存庫或儲存體。
- **視需要調整** -增加代理程式組件區中的實例數目以進行計算密集型工作，或調整為零。 計費是以集區配置為基礎。 如需詳細資訊，請參閱 [定價](https://azure.microsoft.com/pricing/details/container-registry/)。
- **彈性的選項** -從不同的 [集區層](#pool-tiers) 和調整選項中進行選擇，以符合您的工作負載需求。
- **Azure 管理** -工作集區會由 azure 修補及維護，並提供保留配置，而不需要維護個別 vm。

**進階** 容器登錄服務層級中提供這項功能。 如需登錄服務層和限制的相關資訊，請參閱 [Azure Container Registry sku][acr-tiers]。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>

## <a name="preview-limitations"></a>預覽限制

- 工作代理程式組件區目前支援 Linux 節點。 目前不支援 Windows 節點。
- 工作代理程式組件區在以下區域提供預覽：美國西部2、美國中南部、美國東部2、美國東部、美國中部、美國政府亞利桑那州、美國政府德克薩斯州和美國政府弗吉尼亞州。
- 針對每個登錄，預設總 vCPU (core) 配額為16（適用于所有標準代理程式組件區），而針對隔離的代理程式組件區則為0。 開啟 [支援要求][open-support-ticket] 以進行其他配置。
- 您目前無法取消在代理程式組件區上執行的工作。

## <a name="prerequisites"></a>先決條件

* 若要使用本文中的 Azure CLI 步驟，需要 Azure CLI 2.3.1 或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。 或在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中執行。
* 如果您還沒有容器登錄，請在預覽區域中 [建立一個][create-reg-cli] (進階層) 所需的一層。

## <a name="pool-tiers"></a>集區層

代理程式組件區層會在集區中的每個實例提供下列資源。

|服務層級    | 類型  |  CPU  |記憶體 (GB)  |
|---------|---------|---------|---------|
|S1     |  標準    | 2       |    3     |
|S2     |  標準    | 4       |    8     |
|S3     |  標準    | 8       |   16     |
|I6     |  外掛式    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>建立和管理工作代理程式組件區

### <a name="set-default-registry-optional"></a> (選用) 設定預設登錄

若要簡化遵循的 Azure CLI 命令，請執行 [az configure][az-configure] 命令來設定預設的登錄：

```azurecli
az configure --defaults acr=<registryName>
```

下列範例假設您已設定預設登錄。 如果沒有，請 `--registry <registryName>` 在每個命令中傳遞參數 `az acr` 。

### <a name="create-agent-pool"></a>建立代理程式組件區

使用 [az acr agentpool create][az-acr-agentpool-create] 命令建立代理程式組件區。 下列範例會建立 (4 個 CPU/實例) 的層 S2 集區。 依預設，集區包含1個實例。

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> 建立代理程式組件區和其他集區管理作業需要幾分鐘的時間才能完成。

### <a name="scale-pool"></a>調整集區

使用 [az acr agentpool update][az-acr-agentpool-update] 命令，向上或向下調整集區大小。 下列範例會將集區調整為2個實例。 您可以調整為0個實例。

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>在虛擬網路中建立集區

### <a name="add-firewall-rules"></a>新增防火牆規則

工作代理程式組件區需要存取下列 Azure 服務。 您必須將下列防火牆規則新增到任何現有的網路安全性群組或使用者定義的路由。

| 方向 | 通訊協定 | 來源         | 來源連接埠 | Destination          | 目的地埠 | 已使用    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| 輸出  | TCP      | VirtualNetwork | 任意         | AzureKeyVault        | 443       | 預設 |
| 輸出  | TCP      | VirtualNetwork | 任意         | 儲存體              | 443       | 預設 |
| 輸出  | TCP      | VirtualNetwork | 任意         | EventHub             | 443       | 預設 |
| 輸出  | TCP      | VirtualNetwork | 任意         | AzureActiveDirectory | 443       | 預設 |
| 輸出  | TCP      | VirtualNetwork | 任意         | AzureMonitor         | 443       | 預設 |

> [!NOTE]
> 如果您的工作需要來自公用網際網路的其他資源，請新增對應的規則。 例如，需要額外的規則來執行 docker 組建工作，以從 Docker Hub 提取基礎映射，或還原 NuGet 套件。

### <a name="create-pool-in-vnet"></a>在 VNet 中建立集區

下列範例會在網路 *myvnet* 的 *>mysubnet* 子網中建立代理程式組件區：

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>在代理程式組件區上執行工作

下列範例示範如何在佇列工作時指定代理程式組件區。

> [!NOTE]
> 若要在 ACR 工作中使用代理程式組件區，請確認集區至少包含1個實例。
>

### <a name="quick-task"></a>快速工作

使用 [az acr build][az-acr-build] 命令，將代理程式組件區上的快速工作排入佇列，並傳遞 `--agent-pool` 參數：

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>自動觸發的工作

例如，在具有 [az acr task create][az-acr-task-create]的代理程式組件區上建立排程工作，並傳遞 `--agent-pool` 參數。

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

若要確認工作設定，請執行 [az acr task run][az-acr-task-run]：

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>查詢集區狀態

若要找出代理程式組件區上目前排程的執行數目，請執行 [az acr agentpool show][az-acr-agentpool-show]。

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>後續步驟

如需雲端中容器映射組建和維護的更多範例，請參閱 [ACR 工作的教學課程系列](container-registry-tutorial-quick-task.md)。



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
