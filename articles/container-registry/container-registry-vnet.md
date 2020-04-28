---
title: 限制虛擬網路的存取
description: 僅允許從 Azure 虛擬網路中的資源或從公用 IP 位址範圍存取 Azure container registry。
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454339"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>使用 Azure 虛擬網路或防火牆規則來限制對 Azure container registry 的存取

[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)為您的 azure 和內部部署資源提供安全的私用網路。 藉由限制從 Azure 虛擬網路存取您的私用 Azure container registry，您可以確保只有虛擬網路中的資源會存取登錄。 針對跨單位案例，您也可以設定防火牆規則，只允許來自特定 IP 位址的登錄存取。

本文說明在容器登錄上設定輸入網路存取規則的兩個案例：從部署在虛擬網路中的虛擬機器，或從 VM 的公用 IP 位址。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>

如果您改為設定資源的存取規則，以從防火牆後方連線至容器登錄，請參閱設定[規則以存取防火牆後方的 Azure container registry](container-registry-firewall-access-rules.md)。


## <a name="preview-limitations"></a>預覽限制

* 只能使用網路存取規則來設定**Premium**容器登錄。 如需登錄服務層的詳細資訊，請參閱[Azure Container Registry sku](container-registry-skus.md)。 

* 只有[Azure Kubernetes Service](../aks/intro-kubernetes.md)叢集或 Azure[虛擬機器](../virtual-machines/linux/overview.md)可用來做為主機，才能存取虛擬網路中的容器登錄。 *目前不支援其他 Azure 服務，包括 Azure 容器實例。*

* 在虛擬網路中存取的容器登錄中，目前不支援[ACR 工作](container-registry-tasks-overview.md)作業。

* 每個登錄最多支援100個虛擬網路規則。

## <a name="prerequisites"></a>Prerequisites

* 若要使用本文中的 Azure CLI 步驟，需要 Azure CLI 版本2.0.58 或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

* 如果您還沒有容器登錄，請建立一個（需要 Premium SKU）並`hello-world`從 Docker Hub 推送範例映射（例如）。 例如，使用[Azure 入口網站][quickstart-portal]或[Azure CLI][quickstart-cli]來建立登錄。 

* 如果您想要使用不同 Azure 訂用帳戶中的虛擬網路來限制登錄存取，您必須在該訂用帳戶中註冊 Azure Container Registry 的資源提供者。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>關於容器登錄的網路規則

根據預設，Azure container registry 會接受從任何網路上的主機透過網際網路連接。 透過虛擬網路，您可以只允許 AKS 叢集或 Azure VM 之類的 Azure 資源，安全地存取登錄，而不需跨越網路界限。 您也可以設定網路防火牆規則，只允許特定的公用網際網路 IP 位址範圍。 

若要限制登錄的存取權，請先變更登錄的預設動作，使其拒絕所有的網路連線。 然後，新增網路存取規則。 透過網路規則授與存取權的用戶端必須繼續向[容器登錄進行驗證](https://docs.microsoft.com/azure/container-registry/container-registry-authentication)，並獲得授權來存取資料。

### <a name="service-endpoint-for-subnets"></a>子網的服務端點

若要允許從虛擬網路中的子網進行存取，您需要為 Azure Container Registry 服務新增[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 

多租使用者服務（例如 Azure Container Registry）會針對所有客戶使用一組 IP 位址。 服務端點會指派端點來存取登錄。 此端點可透過 Azure 骨幹網路，讓流量成為資源的最佳路由。 虛擬網路和子網路的身分識別也會隨著每項要求傳輸。

### <a name="firewall-rules"></a>防火牆規則

針對 IP 網路規則，請使用 CIDR 標記法（例如*16.17.18.0/24* ）或個別的 IP 位址（如*16.17.18.19 一類*）提供允許的網際網路位址範圍。 只有*公用*網際網路 ip 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 RFC 1918 中所定義)。

## <a name="create-a-docker-enabled-virtual-machine"></a>建立具備 Docker 功能的虛擬機器

在本文中，請使用已啟用 Docker 的 Ubuntu VM 來存取 Azure container registry。 若要對登錄使用 Azure Active Directory 驗證，請同時在 VM 上安裝[Azure CLI][azure-cli] 。 如果您已經有 Azure 虛擬機器，請略過此建立步驟。

您可以將相同的資源群組用於虛擬機器和容器登錄。 此安裝程式會在結束時簡化清理，但不需要。 如果您選擇為虛擬機器和虛擬網路建立個別的資源群組，請執行[az group create][az-group-create]。 下列範例會在*westcentralus*位置中建立名為*myResourceGroup*的資源群組：

```azurecli
az group create --name myResourceGroup --location westus
```

現在，使用[az vm create][az-vm-create]部署預設的 Ubuntu Azure 虛擬機器。 下列範例會建立名為*myDockerVM*的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

系統需要花幾分鐘的時間來建立 VM。 命令完成之後，請記下 Azure CLI 所顯示的 `publicIpAddress`。 使用此位址來建立 VM 的 SSH 連線，並選擇性地在稍後設定防火牆規則。

### <a name="install-docker-on-the-vm"></a>在 VM 上安裝 Docker

在 VM 執行之後，與 VM 建立 SSH 連線。 以您 VM 的公用 IP 位址取代 *publicIpAddress*。

```bash
ssh azureuser@publicIpAddress
```

執行下列命令以在 Ubuntu VM 上安裝 Docker：

```bash
sudo apt install docker.io -y
```

安裝之後，執行下列命令確認 Docker 在 VM 上正常執行：

```bash
sudo docker run -it hello-world
```

輸出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>安裝 Azure CLI

請遵循[使用 apt 安裝 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步驟在您的 Ubuntu 虛擬機器上安裝 Azure CLI。 在本文中，請確定您安裝的是2.0.58 或更新版本。

結束 SSH 連線。

## <a name="allow-access-from-a-virtual-network"></a>允許從虛擬網路存取

在本節中，請將您的容器登錄設定為允許從 Azure 虛擬網路中的子網進行存取。 系統會提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="allow-access-from-a-virtual-network---cli"></a>允許從虛擬網路存取-CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>將服務端點新增至子網

當您建立 VM 時，Azure 預設會在相同的資源群組中建立虛擬網路。 虛擬網路的名稱是以虛擬機器的名稱為基礎。 例如，如果您將虛擬機器命名為*myDockerVM*，預設的虛擬網路名稱是*myDockerVMVNET*，子網名為*myDockerVMSubnet*。 請在 Azure 入口網站中，或使用[az network vnet list][az-network-vnet-list]命令來確認：

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
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
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

繼續[驗證登錄的存取權](#verify-access-to-the-registry)。

### <a name="allow-access-from-a-virtual-network---portal"></a>允許從虛擬網路存取-入口網站

#### <a name="add-service-endpoint-to-subnet"></a>將服務端點新增至子網

當您建立 VM 時，Azure 預設會在相同的資源群組中建立虛擬網路。 虛擬網路的名稱是以虛擬機器的名稱為基礎。 例如，如果您將虛擬機器命名為*myDockerVM*，預設的虛擬網路名稱是*myDockerVMVNET*，子網名為*myDockerVMSubnet*。

若要將 Azure Container Registry 的服務端點新增至子網：

1. 在[Azure 入口網站][azure-portal]頂端的 [搜尋] 方塊中，輸入 [*虛擬網路*]。 當搜尋結果中出現**虛擬網路**時加以選取。
1. 從 [虛擬網路] 清單中，選取要部署虛擬機器的虛擬網路，例如*myDockerVMVNET*。
1. 在 [**設定**] 底下，選取 [**子網**]。
1. 選取您的虛擬機器部署所在的子網，例如*myDockerVMSubnet*。
1. 在 [**服務端點**] 底下，選取 [ **ContainerRegistry**]。
1. 選取 [儲存]  。

![將服務端點新增至子網][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>設定登錄的網路存取

根據預設，Azure container registry 允許從任何網路上的主機進行連接。 若要限制對虛擬網路的存取：

1. 在入口網站中，流覽至您的 container registry。
1. 在 [**設定**] 底下，選取 [**防火牆和虛擬網路**]。
1. 若要預設拒絕存取，請選擇允許從**選取的網路**進行存取。 
1. 選取 [**新增現有的虛擬網路**]，然後選取您使用服務端點設定的虛擬網路和子網。 選取 [新增]  。
1. 選取 [儲存]  。

![設定用於 container registry 的虛擬網路][acr-vnet-portal]

繼續[驗證登錄的存取權](#verify-access-to-the-registry)。

## <a name="allow-access-from-an-ip-address"></a>允許來自 IP 位址的存取

在本節中，請將您的容器登錄設定為允許來自特定 IP 位址或範圍的存取。 系統會提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="allow-access-from-an-ip-address---cli"></a>允許來自 IP 位址的存取-CLI

#### <a name="change-default-network-access-to-registry"></a>變更登錄的預設網路存取

如果您尚未這麼做，請將登錄設定更新為預設拒絕存取。 在下列[az acr update][az-acr-update]命令中，以您的登錄名稱取代：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>從登錄移除網路規則

如果您先前已新增網路規則，以允許從 VM 的子網進行存取，請移除子網的服務端點和網路規則。 以您在 [ [az acr network-rule remove][az-acr-network-rule-remove] ] 命令的先前步驟中所抓取之子網的容器登錄名稱和資源識別碼取代： 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

使用[az acr network-rule add][az-acr-network-rule-add]命令，將網路規則新增至您的登錄，以允許從 VM 的 IP 位址進行存取。 在下列命令中，以容器登錄的名稱和 VM 的公用 IP 位址取代。

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

繼續[驗證登錄的存取權](#verify-access-to-the-registry)。

### <a name="allow-access-from-an-ip-address---portal"></a>允許從 IP 位址存取-入口網站

#### <a name="remove-existing-network-rule-from-registry"></a>從登錄移除現有的網路規則

如果您先前已新增網路規則，以允許從 VM 的子網進行存取，請移除現有的規則。 如果您想要從不同的 VM 存取登錄，請略過本節。

* 更新子網設定，以移除 Azure Container Registry 的子網服務端點。 

  1. 在 [ [Azure 入口網站][azure-portal]中，流覽至虛擬機器部署所在的虛擬網路。
  1. 在 [**設定**] 底下，選取 [**子網**]。
  1. 選取您的虛擬機器部署所在的子網。
  1. 在 [**服務端點**] 下，移除**ContainerRegistry**的核取方塊。 
  1. 選取 [儲存]  。

* 移除允許子網存取登錄的網路規則。

  1. 在入口網站中，流覽至您的 container registry。
  1. 在 [**設定**] 底下，選取 [**防火牆和虛擬網路**]。
  1. 在 [**虛擬網路**] 底下，選取虛擬網路的名稱，然後選取 [**移除**]。
  1. 選取 [儲存]  。

#### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

1. 在入口網站中，流覽至您的 container registry。
1. 在 [**設定**] 底下，選取 [**防火牆和虛擬網路**]。
1. 如果您尚未這麼做，請選擇允許從**選取的網路**進行存取。 
1. 在 [**虛擬網路**] 下，確定未選取任何網路。
1. 在 [**防火牆**] 底下，輸入 VM 的公用 IP 位址。 或者，以 CIDR 標記法輸入包含 VM IP 位址的位址範圍。
1. 選取 [儲存]  。

![設定 container registry 的防火牆規則][acr-vnet-firewall-portal]

繼續[驗證登錄的存取權](#verify-access-to-the-registry)。

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

這個範例示範您可以透過網路存取規則來存取私人容器登錄。 不過，無法從未設定網路存取規則的其他登入主機存取登錄。 如果您嘗試使用`az acr login`命令或`docker login`命令從另一部主機登入，輸出會如下所示：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>還原預設登錄存取

若要將登錄還原為預設允許存取，請移除任何已設定的網路規則。 然後將預設動作設定為 [允許存取]。 系統會提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="restore-default-registry-access---cli"></a>還原預設登錄存取-CLI

#### <a name="remove-network-rules"></a>移除網路規則

若要查看為您的登錄設定的網路規則清單，請執行下列[az acr network-rule list][az-acr-network-rule-list]命令：

```azurecli
az acr network-rule list--name mycontainerregistry 
```

針對每個已設定的規則，執行[az acr network-rule remove][az-acr-network-rule-remove]命令將其移除。 例如：

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>允許存取

在下列[az acr update][az-acr-update]命令中，以您的登錄名稱取代：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>還原預設登錄存取-入口網站


1. 在入口網站中，流覽至您的容器登錄，然後選取 [**防火牆和虛擬網路**]。
1. 在 [**虛擬網路**] 底下，選取每個虛擬網路，然後選取 [**移除**]。
1. 在 [**防火牆**] 底下，選取每個位址範圍，然後選取 [刪除] 圖示。
1. 在 [**允許存取來源**] 底下，選取 [**所有網路**]。 
1. 選取 [儲存]  。

## <a name="clean-up-resources"></a>清除資源

如果您已在相同的資源群組中建立所有 Azure 資源，但不再需要它們，您可以使用單一[az group delete](/cli/azure/group)命令，選擇性地刪除資源：

```azurecli
az group delete --name myResourceGroup
```

若要在入口網站中清除資源，請流覽至 myResourceGroup 資源群組。 載入資源群組後，按一下 [**刪除資源群組**] 以移除資源群組和儲存在該處的資源。

## <a name="next-steps"></a>後續步驟

雖然本文簡短，但仍討論了幾項虛擬網路資源及功能。 Azure 虛擬網路文件涵蓋這些主題的詳細說明：

* [虛擬網路](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [子網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

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
