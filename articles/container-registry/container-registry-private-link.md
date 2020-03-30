---
title: 設置專用連結
description: 在容器註冊表上設置專用終結點，並在本地虛擬網路中啟用專用連結
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498925"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>為 Azure 容器註冊表配置 Azure 專用連結 

可以為 Azure 容器註冊表設置[專用終結點](../private-link/private-endpoint-overview.md)，以便 Azure 虛擬網路上的用戶端通過[專用連結](../private-link/private-link-overview.md)安全地訪問註冊表。 專用終結點使用來自虛擬網路位址空間的 IP 位址進行註冊表。 虛擬網路上的用戶端和註冊表之間的網路流量遍歷虛擬網路和 Microsoft 骨幹網路上的專用鏈路，從而消除了公共 Internet 的暴露。

您可以為專用終結點[配置 DNS 設置](../private-link/private-endpoint-overview.md#dns-configuration)，以便這些設置解析為註冊表分配的私人 IP 位址。 使用 DNS 配置，網路中的用戶端和服務可以繼續訪問註冊表的完全限定功能變數名稱，如*myregistry.azurecr.io*。

此功能在**高級**容器註冊表服務層中可用。 有關註冊表服務層和限制的資訊，請參閱[Azure 容器註冊表 SKU](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能當前處於預覽狀態，並應用一些[限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="preview-limitations"></a>預覽限制

* 目前，您無法在[異地複製的註冊表](container-registry-geo-replication.md)上設置具有私有終結點的專用連結。 

## <a name="prerequisites"></a>Prerequisites

* 要使用本文中的 Azure CLI 步驟，建議使用 Azure CLI 版本 2.2.0 或更高版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。 或在[Azure 雲外殼](../cloud-shell/quickstart.md)中運行。
* 如果還沒有容器註冊表，請創建一個（需要高級層）並推送示例映射（如`hello-world`Docker Hub）。 例如，使用[Azure 門戶][quickstart-portal]或[Azure CLI][quickstart-cli]創建註冊表。
* 如果要使用其他 Azure 訂閱中的專用連結配置註冊表訪問，則需要在該訂閱中註冊 Azure 容器註冊表的資來源提供者。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

本文中的 Azure CLI 示例使用以下環境變數。 替換適合您的環境的值。 所有示例都為 Bash 外殼設置格式：

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>創建啟用 Docker 的虛擬機器

出於測試目的，請使用啟用 Docker 的 Ubuntu VM 訪問 Azure 容器註冊表。 要對註冊表使用 Azure 活動目錄身份驗證，請在 VM 上安裝[Azure CLI。][azure-cli] 如果您已有 Azure 虛擬機器，請跳過此創建步驟。

您可以對虛擬機器和容器註冊表使用相同的資源組。 此設置簡化了清理，但不需要。 如果選擇為虛擬機器和虛擬網路創建單獨的資源組，請運行[az 組創建][az-group-create]：

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

現在部署預設的 Ubuntu Azure 虛擬機器，該虛擬機器具有[az vm 創建][az-vm-create]。 下面的示例創建名為*myDockerVM*的 VM。

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

運行以下命令以在 Ubuntu VM 上安裝 Docker：

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

退出 SSH 連接。

## <a name="set-up-private-link---cli"></a>設置專用鏈路 - CLI

### <a name="get-network-and-subnet-names"></a>獲取網路和子網名稱

如果還沒有虛擬網路和子網的名稱來設置專用連結。 在此示例中，對 VM 和註冊表的專用終結點使用相同的子網。 但是，在許多情況下，您將在單獨的子網中設置終結點。 

創建 VM 時，Azure 預設情況下在同一資源組中創建虛擬網路。 虛擬網路的名稱基於虛擬機器的名稱。 例如，如果您命名虛擬機器*myDockerVM，* 則預設虛擬網路名稱為*myDockerVMVNET，* 其子網名為*myDockerVMSubnet*。 通過運行[az 網路 vnet 清單][az-network-vnet-list]命令在環境變數中設置這些值：

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

### <a name="disable-network-policies-in-subnet"></a>禁用子網中的網路原則

[禁用網路原則](../private-link/disable-private-endpoint-network-policy.md)，如專用終結點的子網中的網路安全性群組。 使用 az 網路[vnet 子網更新子網][az-network-vnet-subnet-update]配置：

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>配置專用 DNS 區域

為私有 Azure 容器註冊表域創建專用 DNS 區域。 在後面的步驟中，您將在此 DNS 區域內為註冊表域創建 DNS 記錄。

要使用私人區域覆蓋 Azure 容器註冊表的預設 DNS 解析，必須**privatelink.azurecr.io**命名該區域。 運行以下[az 網路專用 dns 區域創建][az-network-private-dns-zone-create]命令以創建私人區域：

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>創建關聯連結

運行[az 網路專用 dns 鏈路 vnet 創建][az-network-private-dns-link-vnet-create]以將私人區域與虛擬網路關聯。 本示例創建一個名為*myDNSLink*的連結。

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>創建專用註冊表終結點

在本節中，在虛擬網路中創建註冊表的專用終結點。 首先，獲取註冊表的資源識別碼：

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

運行[az 網路專用終結點創建][az-network-private-endpoint-create]命令以創建註冊表的私有終結點。

下面的示例創建終結點*myPrivate終結點*和服務連接*myConnect*。 要為終結點指定容器註冊表資源，請傳遞`--group-ids registry`：

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

### <a name="get-private-ip-addresses"></a>獲取私人 IP 位址

運行[az 網路專用終結點顯示][az-network-private-endpoint-show]以查詢網路介面 ID 的終結點：

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

與網路介面關聯的是容器註冊表的兩個私人 IP 位址：一個用於註冊表本身，另一個用於註冊表的資料終結點。 運行以下[az 資源顯示][az-resource-show]命令以獲取容器註冊表和註冊表的資料終結點的私人 IP 位址：

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

### <a name="create-dns-records-in-the-private-zone"></a>在私人區域中創建 DNS 記錄

以下命令在註冊表終結點及其資料終結點的私人區域中創建 DNS 記錄。 例如，如果您在*西歐*區域有名為`myregistry.azurecr.io`*myregistry*的註冊表，則終結點名稱為 和`myregistry.westeurope.data.azurecr.io`。 

首先運行[az 網路專用 dns 記錄集創建，][az-network-private-dns-record-set-a-create]以創建註冊表終結點和資料終結點的空 A 記錄集：

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

運行[az 網路專用 dns 記錄集一個附加記錄][az-network-private-dns-record-set-a-add-record]命令，為註冊表終結點和資料終結點創建 A 記錄：

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

專用鏈路現已配置完畢，可供使用。

## <a name="set-up-private-link---portal"></a>設置專用連結 - 門戶

以下步驟假定您已經設置了虛擬網路和子網，並設置了 VM 進行測試。 您還可以[創建新的虛擬網路和子網](../virtual-network/quick-create-portal.md)。

### <a name="create-a-private-endpoint"></a>建立私人端點

1. 在門戶中，導航到容器註冊表。
1. 在 **"設置"** 下，選擇**專用終結點連接（預覽）。**
1. 選擇 **= 專用終結點**。
1. 在 **"基礎知識"** 選項卡中，輸入或選擇以下資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 輸入現有組的名稱或創建新組。|
    | **實例詳細資訊** |  |
    | 名稱 | 輸入唯一名稱。 |
    |區域|選取區域。|
    |||
5. 選擇 **"下一步"：資源**。
6. 輸入或選擇以下資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選擇 **"連接到目錄中的 Azure 資源**"。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選擇**微軟.集裝箱註冊/註冊**。 |
    | 資源 |選擇註冊表的名稱|
    |目標子資源 |選擇**註冊表**|
    |||
7. 選擇 **"下一步"：配置**。
8. 輸入或選擇資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路功能**| |
    | 虛擬網路| 選擇部署虛擬機器的虛擬網路，例如*myDockerVMVNET*。 |
    | 子網路 | 選擇子網，例如部署虛擬機器的*myDockerVM Subnet。* |
    |**專用 DNS 集成**||
    |與私人 DNS 區域整合 |選取 [是]****。 |
    |私人 DNS 區域 |選擇 *（新建）privatelink.azurecr.io* |
    |||

1. 選擇 **"審閱" = 創建**。 您會移至 [檢閱 + 建立]**** 頁面，其中 Azure 會驗證您的設定。 
2. 當您看到**驗證傳遞**的消息時，選擇 **"創建**"。

創建專用終結點後，私人區域中的 DNS 設置將顯示在終結點的 **"概述"** 頁上。

![端點 DNS 設置](./media/container-registry-private-link/private-endpoint-overview.png)

您的專用連結現已配置完畢，可供使用。

## <a name="validate-private-link-connection"></a>驗證專用鏈路連接

應驗證專用終結點子網中的資源是否通過私人 IP 位址連接到註冊表，並且具有正確的專用 DNS 區域集成。

要驗證專用鏈路連接，SSH 到您在虛擬網路中設置的虛擬機器。

運行命令`nslookup`通過專用鏈路解析註冊表的 IP 位址：

```bash
nslookup $registryName.azurecr.io
```

示例輸出顯示子網的位址空間中的註冊表的 IP 位址：

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

將此結果與公共終結點上同一註冊表`nslookup`的輸出中的公共 IP 位址進行比較：

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>通過專用連結的註冊表操作

還要驗證是否可以從子網中的虛擬機器執行註冊表操作。 使 SSH 連接到虛擬機器，並運行[az acr 登錄][az-acr-login]以登錄到註冊表。 根據 VM 配置，您可能需要使用 首碼以下命令`sudo`。

```bash
az acr login --name $registryName
```

執行註冊表操作，例如`docker pull`從註冊表中提取示例映射。 替換為`hello-world:v1`適合您的註冊表的映射和標記，該名稱以註冊表登錄伺服器名稱（所有小寫）為預綴：

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功將映射拉到 VM。

## <a name="manage-private-endpoint-connections"></a>管理專用終結點連接

使用 Azure 門戶或使用[az acr 私有終結點連接][az-acr-private-endpoint-connection]命令組中的命令管理註冊表的專用終結點連接。 操作包括批准、刪除、列出、拒絕或顯示註冊表的專用終結點連接的詳細資訊。

例如，要列出註冊表的專用終結點連接，運行[az acr 私有終結點連接清單][az-acr-private-endpoint-connection-list]命令。 例如：

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

使用本文中的步驟設置專用終結點連接時，註冊表會自動接受來自對註冊表具有 RBAC 許可權的用戶端和服務的連接。 您可以設置終結點以需要手動批准連接。 有關如何批准和拒絕專用終結點連接的資訊，請參閱[管理專用終結點連接](../private-link/manage-private-endpoint.md)。

## <a name="clean-up-resources"></a>清除資源

如果在同一資源組中創建了所有 Azure 資源，並且不再需要它們，則可以選擇使用單個[az 組刪除](/cli/azure/group)命令刪除資源：

```azurecli
az group delete --name $resourceGroup
```

要清理門戶中的資源，請導航到資源組。 載入資源組後，按一下"**刪除資源組"** 以刪除資源組和存儲的資源。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關私有連結的更多詳細資訊，請參閱[Azure 專用連結](../private-link/private-link-overview.md)文檔。
* 私有鏈路的替代方法是設置網路訪問規則以限制註冊表訪問。 要瞭解更多資訊，請參閱[使用 Azure 虛擬網路或防火牆規則限制對 Azure 容器註冊表的訪問](container-registry-vnet.md)。

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
