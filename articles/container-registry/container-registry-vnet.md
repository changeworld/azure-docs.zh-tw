---
title: 限制使用虛擬網路的訪問
description: 僅允許從 Azure 虛擬網路中的資源或公共 IP 位址範圍訪問 Azure 容器註冊表。
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454339"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>使用 Azure 虛擬網路或防火牆規則限制對 Azure 容器註冊表的訪問

[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)為 Azure 和本地資源提供安全的私人網路絡。 通過限制從 Azure 虛擬網路訪問專用 Azure 容器註冊表，可確保只有虛擬網路中的資源訪問註冊表。 對於跨界方案，還可以配置防火牆規則，以便僅允許從特定 IP 位址進行註冊表訪問。

本文演示了兩種在容器註冊表上配置入站網路訪問規則的方案：從部署在虛擬網路中的虛擬機器或從 VM 的公共 IP 位址。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>

如果需要為資源設置訪問規則才能從防火牆後面到達容器註冊表，請參閱[配置規則以訪問防火牆後面的 Azure 容器註冊表](container-registry-firewall-access-rules.md)。


## <a name="preview-limitations"></a>預覽限制

* 只能使用網路訪問規則配置**高級**容器註冊表。 有關註冊表服務層的資訊，請參閱[Azure 容器註冊表 SKU](container-registry-skus.md)。 

* 只能將[Azure 庫伯奈斯服務](../aks/intro-kubernetes.md)群集或 Azure[虛擬機器](../virtual-machines/linux/overview.md)用作主機來訪問虛擬網路中的容器註冊表。 *當前不支援其他 Azure 服務（包括 Azure 容器實例）。*

* 在虛擬網路中訪問的容器註冊表中，當前不支援[ACR 任務](container-registry-tasks-overview.md)操作。

* 每個註冊表最多支援 100 個虛擬網路規則。

## <a name="prerequisites"></a>Prerequisites

* 要使用本文中的 Azure CLI 步驟，需要 Azure CLI 版本 2.0.58 或更高版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

* 如果還沒有容器註冊表，請創建一個（需要高級 SKU）並推送示例映射（如`hello-world`從 Docker Hub）。 例如，使用[Azure 門戶][quickstart-portal]或[Azure CLI][quickstart-cli]創建註冊表。 

* 如果要限制使用其他 Azure 訂閱中的虛擬網路進行註冊表訪問，則需要在該訂閱中註冊 Azure 容器註冊表的資來源提供者。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>關於容器註冊表的網路規則

預設情況下，Azure 容器註冊表接受來自任何網路上的主機的 Internet 連接。 使用虛擬網路，只能允許 Azure 資源（如 AKS 群集或 Azure VM）安全地訪問註冊表，而無需跨越網路邊界。 您還可以配置網路防火牆規則，以僅允許特定的公共互聯網 IP 位址範圍。 

要限制對註冊表的訪問，首先更改註冊表的預設操作，以便它拒絕所有網路連接。 然後，添加網路訪問規則。 通過網路規則授予存取權限的用戶端必須繼續[對容器註冊表進行身份驗證](https://docs.microsoft.com/azure/container-registry/container-registry-authentication)，並有權訪問資料。

### <a name="service-endpoint-for-subnets"></a>子網的服務終結點

要允許從虛擬網路中的子網進行訪問，需要為 Azure 容器註冊表服務添加[服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)。 

多租戶服務（如 Azure 容器註冊表）對所有客戶使用一組 IP 位址。 服務終結點分配終結點以訪問註冊表。 此終結點為流量提供了通過 Azure 骨幹網到資源的最佳路由。 虛擬網路和子網路的身分識別也會隨著每項要求傳輸。

### <a name="firewall-rules"></a>防火牆規則

對於 IP 網路規則，使用 CIDR 符號（如*16.17.18.0/24）* 或單個 IP 位址（如*16.17.18.19）* 提供允許的互聯網位址範圍。 IP 網路規則僅適用于*公共*互聯網 IP 位址。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 RFC 1918 中所定義)。

## <a name="create-a-docker-enabled-virtual-machine"></a>創建啟用 Docker 的虛擬機器

在本文中，使用啟用 Docker 的 Ubuntu VM 訪問 Azure 容器註冊表。 要對註冊表使用 Azure 活動目錄身份驗證，請在 VM 上安裝[Azure CLI。][azure-cli] 如果您已有 Azure 虛擬機器，請跳過此創建步驟。

您可以對虛擬機器和容器註冊表使用相同的資源組。 此設置簡化了清理，但不需要。 如果選擇為虛擬機器和虛擬網路創建單獨的資源組，請運行[az 組創建][az-group-create]。 下面的示例*在中西部*位置創建名為*myResourceGroup*的資源組：

```azurecli
az group create --name myResourceGroup --location westus
```

現在部署預設的 Ubuntu Azure 虛擬機器，該虛擬機器具有[az vm 創建][az-vm-create]。 下面的示例創建名為*myDockerVM 的 VM*：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

系統需要花幾分鐘的時間來建立 VM。 命令完成之後，請記下 Azure CLI 所顯示的 `publicIpAddress`。 使用此位址可以使 SSH 連接到 VM，並可以選擇以後設置防火牆規則。

### <a name="install-docker-on-the-vm"></a>在 VM 上安裝 Docker

在 VM 執行之後，與 VM 建立 SSH 連線。 以您 VM 的公用 IP 位址取代 *publicIpAddress*。

```bash
ssh azureuser@publicIpAddress
```

運行以下命令以在 Ubuntu VM 上安裝 Docker：

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

請遵循[使用 apt 安裝 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步驟在您的 Ubuntu 虛擬機器上安裝 Azure CLI。 對於本文，請確保安裝版本 2.0.58 或更高版本。

退出 SSH 連接。

## <a name="allow-access-from-a-virtual-network"></a>允許從虛擬網路進行訪問

在本節中，將容器註冊表配置為允許從 Azure 虛擬網路中的子網進行訪問。 提供了使用 Azure CLI 和 Azure 門戶的等效步驟。

### <a name="allow-access-from-a-virtual-network---cli"></a>允許從虛擬網路訪問 - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>將服務終結點添加到子網

創建 VM 時，Azure 預設情況下在同一資源組中創建虛擬網路。 虛擬網路的名稱基於虛擬機器的名稱。 例如，如果您命名虛擬機器*myDockerVM，* 則預設虛擬網路名稱為*myDockerVMVNET，* 其子網名為*myDockerVMSubnet*。 在 Azure 門戶中或使用 az[網路 vnet 清單][az-network-vnet-list]命令驗證此情況：

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

使用[az 網路 vnet 子網更新][az-network-vnet-subnet-update]命令將**Microsoft.ContainerRegistry**服務終結點添加到子網。 在以下命令中替換虛擬網路和子網的名稱：

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

使用[az 網路 vnet 子網顯示][az-network-vnet-subnet-show]命令檢索子網的資源識別碼。 您需要在後面的步驟中配置網路訪問規則。

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

#### <a name="change-default-network-access-to-registry"></a>更改對註冊表的預設網路訪問

預設情況下，Azure 容器註冊表允許從任何網路上的主機進行連接。 要限制對所選網路的訪問，更改預設操作以拒絕訪問。 在以下[az acr 更新][az-acr-update]命令中替換註冊表的名稱：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>將網路規則添加到註冊表

使用[az acr 網路規則添加][az-acr-network-rule-add]命令向註冊表添加網路規則，允許從 VM 的子網進行訪問。 在以下命令中替換容器註冊表的名稱和子網的資源識別碼： 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

繼續[驗證對註冊表的訪問](#verify-access-to-the-registry)。

### <a name="allow-access-from-a-virtual-network---portal"></a>允許從虛擬網路訪問 - 門戶

#### <a name="add-service-endpoint-to-subnet"></a>將服務終結點添加到子網

創建 VM 時，Azure 預設情況下在同一資源組中創建虛擬網路。 虛擬網路的名稱基於虛擬機器的名稱。 例如，如果您命名虛擬機器*myDockerVM，* 則預設虛擬網路名稱為*myDockerVMVNET，* 其子網名為*myDockerVMSubnet*。

要將 Azure 容器註冊表的服務終結點添加到子網：

1. 在[Azure 門戶][azure-portal]頂部的搜索框中，輸入*虛擬網路*。 當搜尋結果中出現**虛擬網路**時加以選取。
1. 從虛擬網路清單中，選擇部署虛擬機器的虛擬網路，如*myDockerVMVNET*。
1. 在 **"設置"** 下，選擇**子網**。
1. 選擇部署虛擬機器的子網，例如*myDockerVM Subnet*。
1. 在**服務終結點**下，選擇**Microsoft.Container 註冊 。**
1. 選取 [儲存]****。

![將服務終結點添加到子網][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>為註冊表配置網路訪問

預設情況下，Azure 容器註冊表允許從任何網路上的主機進行連接。 要限制對虛擬網路的訪問，可以：

1. 在門戶中，導航到容器註冊表。
1. 在 **"設置"** 下，選擇**防火牆和虛擬網路**。
1. 預設情況下，要拒絕訪問，請選擇允許從**選定網路**進行訪問。 
1. 選擇 **"添加現有虛擬網路**"，然後選擇使用服務終結點配置的虛擬網路和子網。 選取 [加入]****。
1. 選取 [儲存]****。

![為容器註冊表配置虛擬網路][acr-vnet-portal]

繼續[驗證對註冊表的訪問](#verify-access-to-the-registry)。

## <a name="allow-access-from-an-ip-address"></a>允許從 IP 位址訪問

在本節中，將容器註冊表配置為允許從特定 IP 位址或範圍進行訪問。 提供了使用 Azure CLI 和 Azure 門戶的等效步驟。

### <a name="allow-access-from-an-ip-address---cli"></a>允許從 IP 位址訪問 - CLI

#### <a name="change-default-network-access-to-registry"></a>更改對註冊表的預設網路訪問

如果尚未這樣做，請更新註冊表配置以預設拒絕訪問。 在以下[az acr 更新][az-acr-update]命令中替換註冊表的名稱：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>從註冊表中刪除網路規則

如果以前添加了網路規則以允許從 VM 的子網進行訪問，請刪除子網的服務終結點和網路規則。 替換在[az acr 網路規則刪除][az-acr-network-rule-remove]命令中較早步驟中檢索的容器註冊表的名稱和子網的資源識別碼： 

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

#### <a name="add-network-rule-to-registry"></a>將網路規則添加到註冊表

使用[az acr 網路規則添加][az-acr-network-rule-add]命令向註冊表添加網路規則，允許從 VM 的 IP 位址進行訪問。 在以下命令中替換容器註冊表的名稱和 VM 的公共 IP 位址。

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

繼續[驗證對註冊表的訪問](#verify-access-to-the-registry)。

### <a name="allow-access-from-an-ip-address---portal"></a>允許從 IP 位址訪問 - 門戶

#### <a name="remove-existing-network-rule-from-registry"></a>從註冊表中刪除現有網路規則

如果以前添加了網路規則以允許從 VM 的子網進行訪問，請刪除現有規則。 如果要從其他 VM 訪問註冊表，請跳過此部分。

* 更新子網設置以刪除 Azure 容器註冊表的子網的服務終結點。 

  1. 在[Azure 門戶][azure-portal]中，導航到部署虛擬機器的虛擬網路。
  1. 在 **"設置"** 下，選擇**子網**。
  1. 選擇部署虛擬機器的子網。
  1. 在**服務終結點**下，刪除**Microsoft.Container 註冊處**的核取方塊。 
  1. 選取 [儲存]****。

* 刪除允許子網訪問註冊表的網路規則。

  1. 在門戶中，導航到容器註冊表。
  1. 在 **"設置"** 下，選擇**防火牆和虛擬網路**。
  1. 在 **"虛擬網路**"下，選擇虛擬網路的名稱，然後選擇 **"刪除**"。
  1. 選取 [儲存]****。

#### <a name="add-network-rule-to-registry"></a>將網路規則添加到註冊表

1. 在門戶中，導航到容器註冊表。
1. 在 **"設置"** 下，選擇**防火牆和虛擬網路**。
1. 如果尚未這樣做，請選擇允許從**選定網路**進行訪問。 
1. 在**虛擬網路**下，確保不選擇網路。
1. 在**防火牆**下，輸入 VM 的公共 IP 位址。 或者，在包含 VM IP 位址的 CIDR 標記法中輸入位址範圍。
1. 選取 [儲存]****。

![為容器註冊表配置防火牆規則][acr-vnet-firewall-portal]

繼續[驗證對註冊表的訪問](#verify-access-to-the-registry)。

## <a name="verify-access-to-the-registry"></a>驗證對註冊表的訪問

等待配置更新幾分鐘後，驗證 VM 是否可以訪問容器註冊表。 建立與 VM 的 SSH 連接，並運行[az acr 登錄][az-acr-login]命令以登錄到註冊表。 

```bash
az acr login --name mycontainerregistry
```

可以執行註冊表操作（如運行`docker pull`）從註冊表中提取示例映射。 替換適合您的註冊表的圖像和標記值，該值以註冊表登錄伺服器名稱（所有小寫）為預綴：

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功將映射拉到 VM。

此示例演示了您可以通過網路訪問規則訪問專用容器註冊表。 但是，無法從未配置網路訪問規則的不同登錄主機訪問註冊表。 如果嘗試使用`az acr login`命令或`docker login`命令從其他主機登錄，則輸出類似于以下內容：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>恢復預設註冊表訪問

要還原註冊表以預設允許訪問，請刪除配置的任何網路規則。 然後設置預設操作以允許訪問。 提供了使用 Azure CLI 和 Azure 門戶的等效步驟。

### <a name="restore-default-registry-access---cli"></a>恢復預設註冊表訪問 - CLI

#### <a name="remove-network-rules"></a>刪除網路規則

要查看為註冊表配置的網路規則清單，請運行以下[az acr 網路規則清單][az-acr-network-rule-list]命令：

```azurecli
az acr network-rule list--name mycontainerregistry 
```

對於配置的每個規則，運行 az [acr 網路規則刪除][az-acr-network-rule-remove]命令以將其刪除。 例如：

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

在以下[az acr 更新][az-acr-update]命令中替換註冊表的名稱：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>恢復預設註冊表訪問 - 門戶


1. 在門戶中，導航到容器註冊表並選擇**防火牆和虛擬網路**。
1. 在**虛擬網路**下，選擇每個虛擬網路，然後選擇 **"刪除**"。
1. 在 **"防火牆"** 下，選擇每個位址範圍，然後選擇"刪除"圖示。
1. 在 **"允許從 "訪問**"下，選擇 **"所有網路**"。 
1. 選取 [儲存]****。

## <a name="clean-up-resources"></a>清除資源

如果在同一資源組中創建了所有 Azure 資源，並且不再需要它們，則可以選擇使用單個[az 組刪除](/cli/azure/group)命令刪除資源：

```azurecli
az group delete --name myResourceGroup
```

要清理門戶中的資源，請導航到 myResourceGroup 資源組。 載入資源組後，按一下"**刪除資源組"** 以刪除資源組和存儲的資源。

## <a name="next-steps"></a>後續步驟

雖然本文簡短，但仍討論了幾項虛擬網路資源及功能。 Azure 虛擬網路文件涵蓋這些主題的詳細說明：

* [虛擬網路](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [子](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [服務終結點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

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
