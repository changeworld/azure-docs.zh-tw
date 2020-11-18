---
title: 快速入門 - 使用 Azure CLI 建立 Azure 私人端點
description: 使用本快速入門了解如何使用 Azure CLI 建立私人端點。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: bba912930a9dff0a79e0b0d81025b7524c238db0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368673"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>快速入門：使用 Azure CLI 建立私人端點

藉由使用私人端點來安全地連線到 Azure Web 應用程式，以開始使用 Azure Private Link。

在本快速入門中，您將建立 Azure Web 應用程式的私人端點，並部署虛擬機器來測試私人連線。  

您可以針對不同類型的 Azure 服務 (例如，Azure SQL 和 Azure 儲存體) 建立私人端點。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在 Azure 訂用帳戶中部署具有 **PremiumV2 層** 或更高層級 App Service 方案的 Azure Web 應用程式。  
    * 如需詳細資訊和範例，請參閱[快速入門：在 Azure 中建立 ASP.NET Core Web 應用程式](../app-service/quickstart-dotnetcore.md)。 
    * 如需關於建立 Web 應用程式和端點的詳細教學課程，請參閱[教學課程：使用 Azure 私人端點連線至 Web 應用程式](tutorial-private-endpoint-webapp-portal.md)。
* 登入 Azure 入口網站，並執行 `az login` 檢查您的訂用帳戶是否有效。
* 執行 `az --version`，在終端機或命令視窗中檢查您的 Azure CLI 版本。 如需最新版本，請參閱[最新版本資訊](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。
  * 如果您沒有最新版本，請遵循[適用於您作業系統或平台的安裝指南](/cli/azure/install-azure-cli)，更新您的安裝。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#az_group_create) 來建立資源群組：

* 具名的 **CreatePrivateEndpointQS-rg**。 
* 在 **eastus** 位置中。

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>建立虛擬網路和堡壘主機

在本節中，您會建立虛擬網路、子網路和堡壘主機。 

堡壘主機將用來安全地連線到虛擬機器，以測試私人端點。

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 建立虛擬網路

* 具名 **myVNet**。
* 位址前置詞 **10.0.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* 子網路前置詞 **10.0.0.0/24**。
* 在 **CreatePrivateEndpointQS-rg** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)，更新子網路以停用私人端點的私人端點網路原則：

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立堡壘主機的公用 IP 位址：

* 建立名為 **myBastionIP** 的標準區域備援公用 IP 位址。
* 在 **CreatePrivateEndpointQS-rg** 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 建立 Bastion 子網路：

* 命名為 **AzureBastionSubnet**。
* 位址前置詞 **10.0.1.0/24**。
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreatePrivateEndpointQS-rg** 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 建立堡壘主機：

* 命名為 **myBastionHost**。
* 在 **CreatePrivateEndpointQS-rg** 中。
* 與公用 IP **myBastionIP** 相關聯。
* 與虛擬網路 **myVNet** 相關聯。
* 在 **eastus** 位置中。

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主機需要幾分鐘的時間。

## <a name="create-test-virtual-machine"></a>建立測試虛擬機器

在本節中，您將建立將用來測試私人端點的虛擬機器。

使用  [az vm create](/cli/azure/vm#az_vm_create) 建立 VM。 出現提示時，請提供密碼以作為 VM 的認證：

* 具名的 **myVM**。
* 在 **CreatePrivateEndpointQS-rg** 中。
* 在虛擬網路 **myVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 伺服器映像 **Win2019Datacenter**。

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>建立私人端點

在本節中，您會建立私人端點。

使用 [az Webapp list](/cli/azure/webapp#az_webapp_list) 將您先前建立之 Web 應用程式的資源識別碼放入殼層變數中。

使用 [az network private-endpoint create立](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 建立端點和連線：

* 具名的 **myPrivateEndpoint**。
* 在資源群組 **CreatePrivateEndpointQS-rg** 中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 名為 **myConnection** 的連線。
* 您的 Webapp **\<webapp-resource-group-name>** 。

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域

在本節中，您會使用 [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)建立和設定私人 DNS 區域。  

您將使用 [az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) 建立 DNS 區域的虛擬網路連結。

您將會使用 [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)建立 DNS 區域群組。

* 名為 **privatelink.azurewebsites.net** 的區域
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreatePrivateEndpointQS-rg** 中。
* 名為 **myDNSLink** 的 DNS 連結。
* 與 **myPrivateEndpoint** 相關聯。
* 名為 **MyZoneGroup** 的區域群組。

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的 SQL 伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com) 
 
2. 在左側瀏覽窗格中，選取 [資源群組]。

3. 選取 [CreatePrivateEndpointQS-rg]。

4. 選取 [myVM]。

5. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

6. 選取藍色 [使用堡壘] 按鈕。

7. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

8. 連線之後，在伺服器上開啟 Windows PowerShell。

9. 輸入 `nslookup <your-webapp-name>.azurewebsites.net`。 以您在上一個步驟中建立的 Web 應用程式名稱取代 **\<your-webapp-name>** 。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    針對 Web 應用程式名稱，會傳回 **10.0.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。

10. 在與 **myVM** 的堡壘連線中，開啟 Internet Explorer。

11. 輸入 Web 應用程式的 URL：**https://\<your-webapp-name>.azurewebsites.net**.。

12. 如果您尚未部署應用程式，則會收到預設的 Web 應用程式頁面：

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="預設 Web 應用程式頁面。" border="true":::

13. 關閉與 **myVM** 的連線。

## <a name="clean-up-resources"></a>清除資源 
在私人端點和 VM 使用完畢後，請使用 [az group delete](/cli/azure/group#az_group_delete) 移除資源群組及其包含的所有資源：

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：

* 建立虛擬網路和堡壘主機。
* 虛擬機器。
* Azure Web 應用程式的私人端點。

您已使用虛擬機器在私人端點上安全地測試與 Web 應用程式的連線。

若要深入了解可支援私人端點的服務，請參閱：
> [!div class="nextstepaction"]
> [Private Link 可用性](private-link-overview.md#availability)
