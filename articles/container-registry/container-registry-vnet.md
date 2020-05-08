---
title: 使用服務端點限制存取
description: 使用 Azure 虛擬網路中的服務端點來限制對 Azure container registry 的存取
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: da5ab67d6658d8760565353e2a690c53d862d0ed
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982576"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>使用 Azure 虛擬網路中的服務端點來限制對容器登錄的存取

[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)為您的 azure 和內部部署資源提供安全的私用網路。 [服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)可讓您將容器登錄的公用 IP 位址只保護到您的虛擬網路。 此端點可透過 Azure 骨幹網路，讓流量成為資源的最佳路由。 虛擬網路和子網路的身分識別也會隨著每項要求傳輸。

本文說明如何在虛擬網路中設定 container registry 服務端點（預覽）。 

> [!IMPORTANT]
> Azure Container Registry 現在支援[Azure 私人連結](container-registry-private-link.md)，讓虛擬網路中的私人端點可以放在登錄上。 您可以使用私人 IP 位址，從虛擬網路內部存取私人端點。 我們建議在大部分的網路案例中使用私人端點，而不是服務端點。

設定登錄服務端點**可在高階 container registry**服務層級中取得。 如需登錄服務層和限制的相關資訊，請參閱[Azure Container Registry 層](container-registry-skus.md)。

## <a name="preview-limitations"></a>預覽限制

* 目前未規劃 Azure Container Registry 的服務端點的未來開發。 我們建議改用[私用端點](container-registry-private-link.md)。
* 您無法使用 Azure 入口網站來設定登錄上的服務端點。
* 只有[Azure Kubernetes Service](../aks/intro-kubernetes.md)叢集或 Azure[虛擬機器](../virtual-machines/linux/overview.md)可以做為主機使用服務端點來存取容器登錄。 *不支援包含 Azure 容器實例的其他 Azure 服務。*
* 每個登錄最多支援100網路存取規則。

## <a name="prerequisites"></a>Prerequisites

* 若要使用本文中的 Azure CLI 步驟，需要 Azure CLI 版本2.0.58 或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

* 如果您還沒有容器登錄，請建立一個（需要 Premium SKU）並`hello-world`從 Docker Hub 推送範例映射（例如）。 例如，使用[Azure 入口網站][quickstart-portal]或[Azure CLI][quickstart-cli]來建立登錄。 

* 如果您想要使用不同 Azure 訂用帳戶中的服務端點來限制登錄存取，請在該訂用帳戶中註冊 Azure Container Registry 的資源提供者。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>設定登錄的網路存取

在本節中，請將您的容器登錄設定為允許從 Azure 虛擬網路中的子網進行存取。 系統會提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="allow-access-from-a-virtual-network---cli"></a>允許從虛擬網路存取-CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>將服務端點新增至子網

當您建立 VM 時，Azure 預設會在相同的資源群組中建立虛擬網路。 虛擬網路的名稱是以虛擬機器的名稱為基礎。 例如，如果您將虛擬機器命名為*myDockerVM*，預設的虛擬網路名稱是*myDockerVMVNET*，子網名為*myDockerVMSubnet*。 請在 Azure 入口網站中，或使用[az network vnet list][az-network-vnet-list]命令來確認：

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

輸出：

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

使用[az network vnet subnet update][az-network-vnet-subnet-update]命令，將**ContainerRegistry**服務端點新增至您的子網。 在下列命令中，以您的虛擬網路和子網的名稱取代：

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

使用[az network vnet subnet show][az-network-vnet-subnet-show]命令來取出子網的資源識別碼。 您在稍後的步驟中需要用到此設定網路存取規則。

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

輸出：

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>變更登錄的預設網路存取

根據預設，Azure container registry 允許從任何網路上的主機進行連接。 若要限制對所選網路的存取，請將預設動作變更為 [拒絕存取]。 在下列[az acr update][az-acr-update]命令中，以您的登錄名稱取代：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

使用[az acr network-rule add][az-acr-network-rule-add]命令，將網路規則新增至您的登錄，以允許從 VM 的子網進行存取。 在下列命令中，以容器登錄的名稱和子網的資源識別碼取代： 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>驗證登錄的存取權

等候幾分鐘的時間讓設定更新之後，請確認 VM 可以存取容器登錄。 建立 VM 的 SSH 連線，並執行[az acr login][az-acr-login]命令以登入您的登錄。 

```bash
az acr login --name mycontainerregistry
```

您可以執行登錄作業（例如`docker pull` [執行]），從登錄中提取範例映射。 以適用于您登錄的映射和標籤值取代，並在前面加上登錄登入伺服器名稱（全部小寫）：

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功將映射提取到 VM。

這個範例示範您可以透過網路存取規則來存取私人容器登錄。 不過，無法從未設定網路存取規則的登入主機存取登錄。 如果您嘗試使用`az acr login`命令或`docker login`命令從另一部主機登入，輸出會如下所示：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>還原預設登錄存取

若要將登錄還原為預設允許存取，請移除任何已設定的網路規則。 然後將預設動作設定為 [允許存取]。 系統會提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="restore-default-registry-access---cli"></a>還原預設登錄存取-CLI

#### <a name="remove-network-rules"></a>移除網路規則

若要查看為您的登錄設定的網路規則清單，請執行下列[az acr network-rule list][az-acr-network-rule-list]命令：

```azurecli
az acr network-rule list --name mycontainerregistry 
```

針對每個已設定的規則，執行[az acr network-rule remove][az-acr-network-rule-remove]命令將其移除。 例如：

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="allow-access"></a>允許存取

在下列[az acr update][az-acr-update]命令中，以您的登錄名稱取代：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>清除資源

如果您已在相同的資源群組中建立所有 Azure 資源，但不再需要它們，您可以使用單一[az group delete](/cli/azure/group)命令，選擇性地刪除資源：

```azurecli
az group delete --name myResourceGroup
```

若要在入口網站中清除資源，請流覽至 myResourceGroup 資源群組。 載入資源群組後，按一下 [**刪除資源群組**] 以移除資源群組和儲存在該處的資源。

## <a name="next-steps"></a>後續步驟

* 若要使用虛擬網路中的私人端點來限制對登錄的存取，請參閱[設定 azure container registry 的 Azure 私人連結](container-registry-private-link.md)。
* 如果您需要從用戶端防火牆後方設定登錄存取規則，請參閱設定[規則以存取防火牆後方的 Azure container registry](container-registry-firewall-access-rules.md)。


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
