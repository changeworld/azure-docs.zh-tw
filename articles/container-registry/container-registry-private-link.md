---
title: 設定私人連結
description: 在容器登錄上設定私用端點，並啟用本機虛擬網路中的私人連結
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498925"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>設定 azure container registry 的 Azure 私人連結 

您可以設定 Azure container registry 的[私人端點](../private-link/private-endpoint-overview.md)，讓 azure 虛擬網路上的用戶端透過[私人連結](../private-link/private-link-overview.md)安全地存取登錄。 私人端點會使用您登錄的虛擬網路位址空間中的 IP 位址。 虛擬網路上的用戶端與登錄之間的網路流量會流經虛擬網路和 Microsoft 骨幹網路上的私人連結，以消除公開網際網路的風險。

您可以設定私人端點的[DNS 設定](../private-link/private-endpoint-overview.md#dns-configuration)，如此一來，設定就會解析為登錄的已配置私人 IP 位址。 透過 DNS 設定，網路中的用戶端和服務可以繼續存取登錄的完整功能變數名稱（例如*myregistry.azurecr.io*）的登錄。

這項功能適用于**Premium** container registry 服務層級。 如需登錄服務層和限制的相關資訊，請參閱[Azure Container Registry sku](container-registry-skus.md)。

> [!IMPORTANT]
> 這項功能目前為預覽狀態，並適用一些[限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="preview-limitations"></a>預覽限制

* 目前，您無法在[異地](container-registry-geo-replication.md)複寫的登錄上設定私用端點的私人連結。 

## <a name="prerequisites"></a>先決條件

* 若要使用本文中的 Azure CLI 步驟，建議您 Azure CLI 版本2.2.0 或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。 或在[Azure Cloud Shell](../cloud-shell/quickstart.md)中執行。
* 如果您還沒有容器登錄，請建立一個（需要進階層），並`hello-world`從 Docker Hub 推送範例映射（例如）。 例如，使用[Azure 入口網站][quickstart-portal]或[Azure CLI][quickstart-cli]來建立登錄。
* 如果您想要使用不同 Azure 訂用帳戶中的私人連結來設定登錄存取，您必須在該訂用帳戶中註冊 Azure Container Registry 的資源提供者。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

本文中的 Azure CLI 範例使用下列環境變數。 替代適用于您環境的值。 所有範例都是針對 Bash shell 進行格式化：

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>建立具備 Docker 功能的虛擬機器

基於測試目的，請使用已啟用 Docker 的 Ubuntu VM 來存取 Azure container registry。 若要對登錄使用 Azure Active Directory 驗證，請同時在 VM 上安裝[Azure CLI][azure-cli] 。 如果您已經有 Azure 虛擬機器，請略過此建立步驟。

您可以將相同的資源群組用於虛擬機器和容器登錄。 此安裝程式會在結束時簡化清理，但不需要。 如果您選擇為虛擬機器和虛擬網路建立個別的資源群組，請執行[az group create][az-group-create]：

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

現在，使用[az vm create][az-vm-create]部署預設的 Ubuntu Azure 虛擬機器。 下列範例會建立名為*myDockerVM*的 VM。

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

系統需要花幾分鐘的時間來建立 VM。 命令完成之後，請記下 Azure CLI 所顯示的 `publicIpAddress`。 請使用此位址與 VM 建立 SSH 連線。

### <a name="install-docker-on-the-vm"></a>在 VM 上安裝 Docker

在 VM 執行之後，與 VM 建立 SSH 連線。 以您 VM 的公用 IP 位址取代 *publicIpAddress*。

```bash
ssh azureuser@publicIpAddress
```

執行下列命令以在 Ubuntu VM 上安裝 Docker：

```bash
sudo apt-get update
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

請遵循[使用 apt 安裝 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步驟在您的 Ubuntu 虛擬機器上安裝 Azure CLI。 例如：

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

結束 SSH 連線。

## <a name="set-up-private-link---cli"></a>設定私用連結-CLI

### <a name="get-network-and-subnet-names"></a>取得網路和子網名稱

如果您還沒有這些專案，您將需要虛擬網路和子網的名稱，才能設定私用連結。 在此範例中，您會對 VM 和登錄的私用端點使用相同的子網。 不過，在許多情況下，您會在不同的子網中設定端點。 

當您建立 VM 時，Azure 預設會在相同的資源群組中建立虛擬網路。 虛擬網路的名稱是以虛擬機器的名稱為基礎。 例如，如果您將虛擬機器命名為*myDockerVM*，預設的虛擬網路名稱是*myDockerVMVNET*，子網名為*myDockerVMSubnet*。 藉由執行[az network vnet list][az-network-vnet-list]命令，在環境變數中設定這些值：

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>停用子網中的網路原則

在私人端點的子網中[停用網路原則](../private-link/disable-private-endpoint-network-policy.md)，例如網路安全性群組。 使用[az network vnet subnet update][az-network-vnet-subnet-update]來更新您的子網設定：

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域

建立私密金鑰 Azure container registry 網域的私人 DNS 區域。 在後續步驟中，您會在此 DNS 區域內建立登錄網域的 DNS 記錄。

若要使用私人區域來覆寫 Azure container registry 的預設 DNS 解析，區域必須命名為**privatelink.azurecr.io**。 執行下列[az network private-dns zone create][az-network-private-dns-zone-create]命令來建立私人區域：

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>建立關聯連結

執行[az network private-dns link vnet create][az-network-private-dns-link-vnet-create] ，將私人區域與虛擬網路建立關聯。 這個範例會建立名為*myDNSLink*的連結。

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>建立私用登錄端點

在本節中，請在虛擬網路中建立登錄的私用端點。 首先，取得登錄的資源識別碼：

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

執行[az network 私用端點 create][az-network-private-endpoint-create]命令來建立登錄的私用端點。

下列範例會建立端點*myPrivateEndpoint*和服務連接*myConnection*。 若要指定端點的 container registry 資源，請傳遞`--group-ids registry`：

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>取得私人 IP 位址

執行[az network 私用端點 show][az-network-private-endpoint-show]來查詢端點的網路介面識別碼：

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

與網路介面相關聯的是容器登錄的兩個私人 IP 位址：一個用於登錄本身，另一個用於登錄的資料端點。 執行下列[az resource show][az-resource-show]命令，以取得容器登錄和登錄資料端點的私人 IP 位址：

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>在私人區域中建立 DNS 記錄

下列命令會在私人區域中為登錄端點及其資料端點建立 DNS 記錄。 例如，如果您在*westeurope*區域中有名為`myregistry.azurecr.io` *myregistry*的登錄，則端點名稱為和`myregistry.westeurope.data.azurecr.io`。 

第一次執行[az network private-dns record-設定 create][az-network-private-dns-record-set-a-create] ，為登錄端點和資料端點建立空的 a 記錄集：

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

執行[az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record]命令，以建立登錄端點和資料端點的 a 記錄：

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

私人連結現在已設定並可供使用。

## <a name="set-up-private-link---portal"></a>設定私人連結-入口網站

下列步驟假設您已使用 VM 設定虛擬網路和子網以進行測試。 您也可以[建立新的虛擬網路和子網](../virtual-network/quick-create-portal.md)。

### <a name="create-a-private-endpoint"></a>建立私人端點

1. 在入口網站中，流覽至您的 container registry。
1. 在 [**設定**] 下，選取 **[私人端點連接（預覽）**]。
1. 選取 [ **+ 私用端點**]。
1. 在 [**基本**] 索引標籤中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 輸入現有群組的名稱，或建立一個新的組名。|
    | **執行個體詳細資料** |  |
    | Name | 輸入唯一名稱。 |
    |區域|選取區域。|
    |||
5. 完成時，選取 [下一步:  資源]。
6. 輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 **[連線到我的目錄中的 Azure 資源]**。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [ **ContainerRegistry]/**[登錄]。 |
    | 資源 |選取您的登錄名稱|
    |目標 subresource |選取**登錄**|
    |||
7. 完成時，選取 [下一步:  組態]。
8. 輸入或選取資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路功能**| |
    | 虛擬網路| 選取您的虛擬機器部署所在的虛擬網路，例如*myDockerVMVNET*。 |
    | 子網路 | 選取子網，例如您的虛擬機器部署所在的*myDockerVMSubnet* 。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]  。 |
    |私人 DNS 區域 |選取 *（新增） privatelink.azurecr.io* |
    |||

1. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。 
2. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

建立私用端點之後，私人區域中的 DNS 設定會出現在端點的 **[總覽**] 頁面上。

![端點 DNS 設定](./media/container-registry-private-link/private-endpoint-overview.png)

您的私用連結現在已設定並可供使用。

## <a name="validate-private-link-connection"></a>驗證私人連結連線

您應該驗證私人端點的子網內的資源會透過私人 IP 位址連線到您的登錄，並具有正確的私人 DNS 區域整合。

若要驗證私人連結連線，請透過 SSH 連線到您在虛擬網路中設定的虛擬機器。

執行`nslookup`命令，以透過私人連結來解析登錄的 IP 位址：

```bash
nslookup $registryName.azurecr.io
```

範例輸出會在子網的位址空間中顯示登錄的 IP 位址：

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

針對相同的登錄，透過公用端點， `nslookup`將此結果與輸出中的公用 IP 位址進行比較：

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>透過私人連結的登錄作業

也請確認您可以從子網中的虛擬機器執行登錄作業。 建立與虛擬機器的 SSH 連線，並執行[az acr login][az-acr-login]以登入您的登錄。 視您的 VM 設定而定，您可能需要在下列命令前面`sudo`加上。

```bash
az acr login --name $registryName
```

執行登錄作業，例如`docker pull` ，從登錄中提取範例映射。 以`hello-world:v1`適用于您登錄的映射和標籤取代，並在前面加上登錄登入伺服器名稱（全部小寫）：

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功將映射提取到 VM。

## <a name="manage-private-endpoint-connections"></a>管理私人端點連接

使用 Azure 入口網站，或使用[az acr private-endpoint-connection][az-acr-private-endpoint-connection]命令群組中的命令來管理登錄的私人端點連線。 作業包括 [核准]、[刪除]、[清單]、[拒絕] 或 [顯示登錄的私人端點連線詳細資料]。

例如，若要列出登錄的私用端點連接，請執行[az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list]命令。 例如：

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

當您使用本文中的步驟來設定私用端點連線時，登錄會自動接受來自具有登錄之 RBAC 許可權的用戶端和服務的連接。 您可以設定端點以要求手動核准連接。 如需如何核准和拒絕私人端點連線的詳細資訊，請參閱[管理私人端點](../private-link/manage-private-endpoint.md)連線。

## <a name="clean-up-resources"></a>清除資源

如果您已在相同的資源群組中建立所有 Azure 資源，但不再需要它們，您可以使用單一[az group delete](/cli/azure/group)命令，選擇性地刪除資源：

```azurecli
az group delete --name $resourceGroup
```

若要在入口網站中清除資源，請流覽至您的資源群組。 載入資源群組後，按一下 [**刪除資源群組**] 以移除資源群組和儲存在該處的資源。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解私人連結，請參閱[Azure 私人連結](../private-link/private-link-overview.md)檔。
* 私人連結的替代方法是設定網路存取規則，以限制登錄存取。 若要深入瞭解，請參閱[使用 azure 虛擬網路或防火牆規則來限制對 Azure container registry 的存取](container-registry-vnet.md)。

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
