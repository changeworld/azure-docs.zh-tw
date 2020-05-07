---
title: 將容器群組部署至 Azure 虛擬網路
description: 瞭解如何使用 Azure 命令列介面，將容器群組部署至新的或現有的 Azure 虛擬網路。
ms.topic: article
ms.date: 04/29/2020
ms.author: danlep
ms.openlocfilehash: 7e54690efc7955eaaa88ca87a6f7a086dd3e19a4
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583648"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>將容器執行個體部署至 Azure 虛擬網路

[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)為您的 azure 和內部部署資源提供安全的私用網路。 透過將容器群組部署至 Azure 虛擬網路，您的容器可在虛擬網路中安全地與其他資源通訊。

本文說明如何使用 Azure CLI 中的[az container create][az-container-create]命令，將容器群組部署至新的虛擬網路或現有的虛擬網路。 

如需網路案例和限制，請參閱[Azure 容器實例的虛擬網路案例和資源](container-instances-virtual-network-concepts.md)。

> [!IMPORTANT]
> 在大部分可使用 Azure 容器實例的區域中，對虛擬網路的容器群組部署已正式推出。 如需詳細資訊，請參閱[區域和資源可用性](container-instances-virtual-network-concepts.md#where-to-deploy)。 

本文中的範例會針對 Bash shell 進行格式化。 如果您慣用其他殼層，例如 PowerShell 或命令提示字元，請相應調整行接續字元。


## <a name="deploy-to-new-virtual-network"></a>部署至新的虛擬網路

若要部署至新的虛擬網路並讓 Azure 自動為您建立網路資源，請在執行 [az container create][az-container-create] 時指定下列項目：

* 虛擬網路名稱
* CIDR 格式的虛擬網路位址首碼
* 子網路名稱
* CIDR 格式的子網路位址首碼

虛擬網路及子網路位址首碼分別指定了虛擬網路及子網路的位址空間。 這些值會使用無類別網域間路由選擇 (CIDR) 標記法呈現，例如：`10.0.0.0/16`。 如需使用子網路的詳細資訊，請參閱[新增、變更或刪除虛擬網路子網路](../virtual-network/virtual-network-manage-subnet.md)。

當您利用此方法部署了第一個容器群組後，就可以透過指定虛擬網路及子網路名稱，或是 Azure 自動為您建立的網路設定檔，來部署至同一個子網路。 因為 Azure 將該子網路委派至 Azure 容器執行個體，所以您「只」** 能將容器群組部署至子網路。

### <a name="example"></a>範例

下列[az container create][az-container-create]命令會指定新的虛擬網路和子網的設定。 提供在虛擬網路中的容器群組部署[可供使用](container-instances-region-availability.md#availability---virtual-network-deployment)的區域中所建立的資源組名。 此命令會部署公用 Microsoft [aci-helloworld][aci-helloworld]容器，其會執行小型 node.js web 伺服器以提供靜態網頁。 在下一節中，您會對同一個子網路部署第二個容器群組，並測試兩個容器執行個體之間的通訊。

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

當您使用此方法部署至新的虛擬網路時，部署可能會花費幾分鐘建立網路資源。 初始部署之後，對相同子網的其他容器群組部署會更快速完成。

## <a name="deploy-to-existing-virtual-network"></a>部署至現有的虛擬網路

將容器群組部署至現有的虛擬網路：

1. 在現有的虛擬網路內建立子網、使用已部署容器群組的現有子網，或使用現有的子網清空*所有*其他資源
1. 使用 [az container create][az-container-create] 部署容器群組，並指定下列其中一項：
   * 虛擬網路名稱及子網路名稱
   * 虛擬網路資源識別碼與子網路資源識別碼，其允許使用不同資源群組中的虛擬網路
   * 網路設定檔名稱或識別碼，您可使用 [az network profile list][az-network-profile-list] 取得

### <a name="example"></a>範例

下列範例會將第二個容器群組部署至先前建立的相同子網，並驗證兩個容器實例之間的通訊。

首先，取得您部署的第一個容器群組 IP 位址 *appcontainer*：

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

輸出會顯示私人子網中容器群組的 IP 位址。 例如：

```console
10.0.0.4
```

現在，將 `CONTAINER_GROUP_IP` 設定為您用 `az container show` 命令擷取的 IP，然後執行下列 `az container create` 命令。 第二個容器 *commchecker*會執行以 Alpine Linux 為基礎的映像，並對第一個容器群組的私人子網路 IP 位址執行 `wget`。

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

當第二個容器部署完成之後，請提取其記錄，您便可以看見其執行的 `wget` 命令輸出：

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

如果第二個容器成功與第一個容器通訊，輸出類似于：

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

記錄輸出應會顯示，`wget` 可以使用其區域子網路的私人 IP 位址從第一個容器連線及下載索引檔案。 兩個容器群組之間的網路流量會保留在虛擬網路中。

### <a name="example---yaml"></a>範例-YAML

您也可以使用 YAML 檔案、 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)或其他程式設計方法（例如 Python SDK），將容器群組部署至現有的虛擬網路。 

例如，使用 YAML 檔案時，您可以部署至具有委派給 Azure 容器實例之子網的虛擬網路。 指定下列屬性：

* `ipAddress`：容器群組的私人 IP 位址設定。
  * `ports`：要開啟的連接埠 (如果有)。
  * `protocol`：已開啟連接埠的通訊協定 (TCP 或 UDP)。
* `networkProfile`：虛擬網路和子網的網路設定。
  * `id`：`networkProfile` 的完整 Resource Manager 資源識別碼。

若要取得網路設定檔的識別碼，請執行[az network profile list][az-network-profile-list]命令，並指定包含您的虛擬網路和委派子網的資源組名。

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

範例輸出：

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

當您取得網路設定檔識別碼後，請將下列 YAML 複製到名為 *vnet-deploy-aci.yaml* 的新檔案。 在 `networkProfile` 之下，用您剛擷取的識別碼來取代 `id` 值，然後儲存檔案。 此 YAML 會在虛擬網路中建立名為 *appcontaineryaml* 的容器群組。

```YAML
apiVersion: '2018-10-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

使用 [az container create][az-container-create] 命令來部署容器群組，並為 `--file` 參數指定 YAML 檔案名稱：

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

部署完成後，請執行[az container show][az-container-show]命令來顯示其狀態。 範例輸出：

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>清除資源

### <a name="delete-container-instances"></a>刪除容器執行個體

當您使用完所建立的容器執行個體後，請使用下列命令將其刪除：

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>刪除網路資源

這項功能目前需要數個額外的命令來刪除您稍早建立的網路資源。 如果您使用了本文前幾節的範例命令來建立虛擬網路及子網路，您可以使用下列指令碼來刪除這些網路資源。 此腳本假設您的資源群組包含具有單一網路設定檔的單一虛擬網路。

在執行指令碼之前，請將變數 `RES_GROUP` 設定為包含應刪除虛擬網路及子網路的資源群組名稱。 如果您未使用稍早建議的`aci-vnet`名稱，請更新虛擬網路的名稱。 此指令碼會針對 Bash 殼層加以格式化。 如果您慣用其他殼層，例如 PowerShell 或是命令提示字元，您需要相應調整變數指派及存取子。

> [!WARNING]
> 此指令碼會刪除資源！ 它會刪除虛擬網路及內含的所有子網路。 在執行此指令碼之前，請先確認您已不再需要虛擬網路中的「任何」** 資源，包括內含的任何子網路。 一旦您刪除後，**這些資源就無法復原**。

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>後續步驟

若要使用 Resource Manager 範本部署新的虛擬網路、子網路、網路設定檔及容器群組，請參閱 [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) (使用 VNet 建立 Azure 容器群組)。

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
