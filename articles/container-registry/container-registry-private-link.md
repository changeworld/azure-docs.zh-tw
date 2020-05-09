---
title: 設定私人連結
description: 在容器登錄上設定私用端點，並透過本機虛擬網路中的私人連結啟用存取
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: 46ec816d85a528fd3208026ef76dff8470154767
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982415"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>設定 azure container registry 的 Azure 私人連結 

使用[Azure 私用連結](../private-link/private-link-overview.md)將虛擬網路私人 IP 位址指派給登錄端點，以限制登錄的存取權。 虛擬網路上的用戶端與登錄之間的網路流量會流經虛擬網路和 Microsoft 骨幹網路上的私人連結，以消除公開網際網路的風險。

您可以設定私人端點的[DNS 設定](../private-link/private-endpoint-overview.md#dns-configuration)，如此一來，設定就會解析為登錄的已配置私人 IP 位址。 透過 DNS 設定，網路中的用戶端和服務可以繼續存取登錄的完整功能變數名稱（例如*myregistry.azurecr.io*）的登錄。

這項功能適用于**Premium** container registry 服務層級。 如需登錄服務層和限制的相關資訊，請參閱[Azure Container Registry 層](container-registry-skus.md)。

## <a name="things-to-know"></a>須知事項

* 目前，使用 Azure 資訊安全中心的影像掃描不會出現在以私人端點設定的登錄中。

## <a name="prerequisites"></a>Prerequisites

* 若要使用本文中的 Azure CLI 步驟，建議您 Azure CLI 版本2.2.0 或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。 或在[Azure Cloud Shell](../cloud-shell/quickstart.md)中執行。
* 如果您還沒有容器登錄，請建立一個（需要高階層級）， [import](container-registry-import-images.md)然後`hello-world`從 Docker Hub 匯入範例映射（例如）。 例如，使用[Azure 入口網站][quickstart-portal]或[Azure CLI][quickstart-cli]來建立登錄。
* 若要使用不同 Azure 訂用帳戶中的私人連結來設定登錄存取，您必須在該訂用帳戶中註冊 Azure Container Registry 的資源提供者。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

本文中的 Azure CLI 範例使用下列環境變數。 替代適用于您環境的值。 所有範例都是針對 Bash shell 進行格式化：

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>設定私用連結-CLI

### <a name="get-network-and-subnet-names"></a>取得網路和子網名稱

如果您還沒有這些專案，您將需要虛擬網路和子網的名稱，才能設定私用連結。 在此範例中，您會對 VM 和登錄的私用端點使用相同的子網。 不過，在許多情況下，您會在不同的子網中設定端點。 

當您建立 VM 時，Azure 預設會在相同的資源群組中建立虛擬網路。 虛擬網路的名稱是以虛擬機器的名稱為基礎。 例如，如果您將虛擬機器命名為*myDockerVM*，預設的虛擬網路名稱是*myDockerVMVNET*，子網名為*myDockerVMSubnet*。 藉由執行[az network vnet list][az-network-vnet-list]命令，在環境變數中設定這些值：

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>停用子網中的網路原則

在私人端點的子網中[停用網路原則](../private-link/disable-private-endpoint-network-policy.md)，例如網路安全性群組。 使用[az network vnet subnet update][az-network-vnet-subnet-update]來更新您的子網設定：

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域

建立私人 Azure container registry 網域的私人 DNS 區域。 在後續步驟中，您會在此 DNS 區域中建立登錄網域的 DNS 記錄。

若要使用私人區域來覆寫 Azure container registry 的預設 DNS 解析，區域必須命名為**privatelink.azurecr.io**。 執行下列[az network private-dns zone create][az-network-private-dns-zone-create]命令來建立私人區域：

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>建立關聯連結

執行[az network private-dns link vnet create][az-network-private-dns-link-vnet-create] ，將私人區域與虛擬網路建立關聯。 這個範例會建立名為*myDNSLink*的連結。

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>建立私用登錄端點

在本節中，請在虛擬網路中建立登錄的私用端點。 首先，取得登錄的資源識別碼：

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

執行[az network 私用端點 create][az-network-private-endpoint-create]命令來建立登錄的私用端點。

下列範例會建立端點*myPrivateEndpoint*和服務連接*myConnection*。 若要指定端點的 container registry 資源，請傳遞`--group-ids registry`：

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>取得私人 IP 位址

執行[az network 私用端點 show][az-network-private-endpoint-show]來查詢端點的網路介面識別碼：

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

在此範例中，與網路介面相關聯的是容器登錄的兩個私人 IP 位址：一個用於登錄本身，另一個用於登錄的資料端點。 下列[az resource show][az-resource-show]命令會取得容器登錄和登錄資料端點的私人 IP 位址：

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> 如果您的登錄是[異地](container-registry-geo-replication.md)複寫，請查詢每個登錄複本的其他資料端點。

### <a name="create-dns-records-in-the-private-zone"></a>在私人區域中建立 DNS 記錄

下列命令會在私人區域中為登錄端點及其資料端點建立 DNS 記錄。 例如，如果您在*westeurope*區域中有名為`myregistry.azurecr.io` *myregistry*的登錄，則端點名稱為和`myregistry.westeurope.data.azurecr.io`。 

> [!NOTE]
> 如果您的登錄是[異地](container-registry-geo-replication.md)複寫，請為每個複本的資料端點 IP 建立額外的 DNS 記錄。

第一次執行[az network private-dns record-設定 create][az-network-private-dns-record-set-a-create] ，為登錄端點和資料端點建立空的 a 記錄集：

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

執行[az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record]命令，以建立登錄端點和資料端點的 a 記錄：

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

私人連結現在已設定並可供使用。

## <a name="set-up-private-link---portal"></a>設定私人連結-入口網站

當您建立登錄時設定私人連結，或將私人連結新增至現有的登錄。 下列步驟假設您已使用 VM 設定虛擬網路和子網以進行測試。 您也可以[建立新的虛擬網路和子網](../virtual-network/quick-create-portal.md)。

### <a name="create-a-private-endpoint---new-registry"></a>建立私用端點-新登錄

1. 在入口網站中建立登錄時，請在 [**基本**] 索引標籤的 [ **SKU**] 中選取 [ **Premium**]。
1. 選取 [**網路**] 索引標籤。
1. 在 [**網路**連線] 中，選取 [**私人端點** > **+ 新增**]。
1. 輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 輸入現有群組的名稱，或建立一個新的組名。|
    | 名稱 | 輸入唯一名稱。 |
    | 子資源 |選取**登錄**|
    | **網路功能** | |
    | 虛擬網路| 選取您的虛擬機器部署所在的虛擬網路，例如*myDockerVMVNET*。 |
    | 子網路 | 選取子網，例如您的虛擬機器部署所在的*myDockerVMSubnet* 。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]  。 |
    |私人 DNS 區域 |選取 *（新增） privatelink.azurecr.io* |
    |||
1. 設定其餘的登錄設定，然後選取 [**檢查 + 建立**]。

  ![建立具有私人端點的登錄](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>建立私用端點-現有的登錄

1. 在入口網站中，流覽至您的 container registry。
1. 在 [**設定**] 底下，選取 [**網路**]。
1. 在 [**私人端點**] 索引標籤上，選取 [ **+ 私用端點**]。
1. 在 [**基本**] 索引標籤中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 輸入現有群組的名稱，或建立一個新的組名。|
    | **執行個體詳細資料** |  |
    | 名稱 | 輸入名稱。 |
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

建立私用端點之後，私人區域中的 DNS 設定會出現在入口網站的 [**私人端點**] 頁面上：

1. 在入口網站中，流覽至您的容器登錄，然後選取 [設定] [ **> 網路**]。
1. 在 [**私人端點**] 索引標籤上，選取您建立的私人端點。
1. 在 [**總覽**] 頁面上，檢查 [連結設定] 和 [自訂 DNS 設定]。

  ![端點 DNS 設定](./media/container-registry-private-link/private-endpoint-overview.png)

您的私用連結現在已設定並可供使用。

## <a name="disable-public-access"></a>停用公用存取

在許多情況下，請停用公用網路的登錄存取。 此設定可防止虛擬網路外部的用戶端到達登錄端點。 若要使用入口網站停用公用存取：

1. 在入口網站中，流覽至您的容器登錄，然後選取 [設定] [ **> 網路**]。
1. 在 [**公用存取**] 索引標籤的 [**允許公用存取**] 中，選取 [**停用**]。 然後選取 [儲存]  。

## <a name="validate-private-link-connection"></a>驗證私人連結連線

您應該驗證私人端點的子網內的資源會透過私人 IP 位址連線到您的登錄，並具有正確的私人 DNS 區域整合。

若要驗證私人連結連線，請透過 SSH 連線到您在虛擬網路中設定的虛擬機器。

執行`nslookup`命令，以透過私人連結來解析登錄的 IP 位址：

```bash
nslookup $REGISTRY_NAME.azurecr.io
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
az acr login --name $REGISTRY_NAME
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
  --registry-name $REGISTRY_NAME 
```

當您使用本文中的步驟來設定私用端點連線時，登錄會自動接受來自具有登錄之 RBAC 許可權的用戶端和服務的連接。 您可以設定端點以要求手動核准連接。 如需如何核准和拒絕私人端點連線的詳細資訊，請參閱[管理私人端點](../private-link/manage-private-endpoint.md)連線。

## <a name="add-zone-records-for-replicas"></a>新增複本的區域記錄

如本文所示，當您將私人端點連線新增至登錄時[，會在](container-registry-geo-replication.md)複寫複寫的`privatelink.azurecr.io`區域中，建立該區域的 DNS 記錄及其資料端點。 

如果您稍後新增新的複本，您必須手動為該區域中的資料端點新增新的區域記錄。 例如，如果您在*northeurope*位置建立`myregistry.northeurope.data.azurecr.io` *myregistry*的複本，請新增的區域記錄。 如需相關步驟，請參閱本文[的在私人區域中建立 DNS 記錄](#create-dns-records-in-the-private-zone)。

## <a name="clean-up-resources"></a>清除資源

如果您已在相同的資源群組中建立所有 Azure 資源，但不再需要它們，您可以使用單一[az group delete](/cli/azure/group)命令，選擇性地刪除資源：

```azurecli
az group delete --name $RESOURCE_GROUP
```

若要在入口網站中清除資源，請流覽至您的資源群組。 載入資源群組後，按一下 [**刪除資源群組**] 以移除資源群組和儲存在該處的資源。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解私人連結，請參閱[Azure 私人連結](../private-link/private-link-overview.md)檔。
* 如果您需要從用戶端防火牆後方設定登錄存取規則，請參閱設定[規則以存取防火牆後方的 Azure container registry](container-registry-firewall-access-rules.md)。

<!-- LINKS - external -->
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
[az-acr-update]: /cli/azure/acr#az-acr-update
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
