---
title: 教學課程：建立 NAT 閘道 - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: 本快速入門說明如何使用 Azure CLI 建立 NAT 閘道。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 016e187c2ae41958b9527b88b06517b5f5544c7b
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707900"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli"></a>教學課程：使用 Azure CLI 建立 NAT 閘道

本教學課程說明如何使用 Azure 虛擬網路 NAT 服務。 您將建立 NAT 閘道，為 Azure 中的虛擬機器提供輸出連線能力。 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 完成本教學課程，或在本機執行個別命令。  如果您從未使用過 Azure Cloud Shell，請[立即登入](https://shell.azure.com)以進行初始設定。
如果您選擇在本機執行這些命令，則必須安裝 CLI。  在本教學課程中，您必須執行 Azure CLI 2.0.71 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。


## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 **eastus2** 位置建立名為 myResourceGroupNAT**** 的資源群組：

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>建立 NAT 閘道

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

若要存取公用網際網路，您需要有一個或多個適用於 NAT 閘道的公用 IP 位址。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)，在 **myResourceGroupNAT** 中建立名為 **myPublicIP** 的公用 IP 位址資源。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>建立公用 IP 前置詞

您可以搭配 NAT 閘道使用一或多個公用 IP 位址資源、公用 IP 前置詞或兩者。 我們將在此案例中新增公用 IP 前置詞資源進行示範。   使用 [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)，在 **myResourceGroupNAT** 中建立名為 **myPublicIPprefix** 的公用 IP 前置詞資源。

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>建立 NAT 閘道資源

本節將詳細說明如何使用 NAT 閘道資源建立及設定下列 NAT 服務元件：
  - 公用 IP 集區和公用 IP 前置詞，將用於 NAT 閘道資源所轉譯的輸出流量。
  - 將閒置逾時時間從預設的 4 分鐘變更為 10 分鐘。

使用 [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) 建立全域 Azure NAT 閘道，其名稱為 **myNATgateway**。 此命令會使用公用 IP 位址 **myPublicIP** 和公用 IP 前置詞 **myPublicIPprefix**。 此命令會將閒置逾時變更為 **10** 分鐘。

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

此時，NAT 閘道可正常運作，而現在唯一缺少的就是設定虛擬網路的哪些子網路應使用此閘道。

## <a name="configure-virtual-network"></a>設定虛擬網路

在您部署 VM 並可使用 NAT 閘道之前，我們必須先建立虛擬網路。

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet)，在 **myResourceGroupNAT** 中建立名為 **myVnet** 虛擬網路，其具有名為 **mySubnet** 的子網路。  虛擬網路的 IP 位址空間為 **192.168.0.0/16**。 虛擬網路內的子網路為 **192.168.0.0/24**。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>設定來源子網路的 NAT 服務

我們將在虛擬網路 **myVnet** 中設定來源子網路 **mySubnet**，以使用特定 NAT 閘道資源 **myNATgateway** 搭配 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)。  此命令將會在指定的子網路上啟用 NAT 服務。

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

所有連結到網際網路目的地的輸出流量現在都會使用 NAT 閘道。  您不需要設定 UDR。

## <a name="create-a-vm-to-use-the-nat-service"></a>建立 VM 以使用 NAT 服務

我們現在會建立 VM 以使用 NAT 服務。  此 VM 具有公用 IP，可作為執行個體層級的公用 IP，讓您能夠存取 VM。  NAT 服務可感知流量方向，並將取代您子網路中的預設網際網路目的地。 VM 的公用 IP 位址不會用於輸出連線。

### <a name="create-public-ip-for-source-vm"></a>建立來源 VM 的公用 IP

我們會建立用來存取 VM 的公用 IP。  使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)，在 **myResourceGroupNAT** 中建立名為 **myPublicIPVM** 的公用 IP 位址資源。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>建立 VM 的 NSG

因為標準公用 IP 位址屬於「預設保護」，所以我們需要建立 NSG 來允許 SSH 的輸入存取。 使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)，在 **myResourceGroupNAT** 中建立名為 **myNSG** 的 NSG 資源。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>公開來源 VM 上的 SSH 端點

我們會在 NSG 中建立規則，以供對來源 VM 進行 SSH 存取。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)，在 **myResourceGroupNAT** 中名為 **myNSG** 的 NSG 中建立名為 **ssh** 的 NSG 規則。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>建立 VM 的 NIC

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 建立網路介面，並使其與公用 IP 位址和網路安全性群組產生關聯。 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>建立 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器。  我們會為此 VM 產生 SSH 金鑰，並儲存私密金鑰以便稍後使用。

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

等待 VM 進行部署，然後繼續進行其餘的步驟。

## <a name="discover-the-ip-address-of-the-vm"></a>探索 VM 的 IP 位址

首先，我們需要探索您所建立 VM 的 IP 位址。 若要擷取 VM 的公用 IP 位址，請使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便將其用來存取 VM。

### <a name="sign-in-to-vm"></a>登入 VM

在上一個作業中，SSH 認證應該已儲存在您的 Cloud Shell 中。  在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。

```bash
ssh <ip-address-destination>
```

您現在已經準備好使用 NAT 服務。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組及其內含的所有資源。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 NAT 閘道以及要使用該閘道的 VM。 

接著請檢閱 Azure 監視器中的計量，以了解 NAT 服務的運作。 診斷可用 SNAT 連接埠的資源耗盡相關問題。  新增額外的公用 IP 位址資源或公用 IP 前置詞資源 (或兩者)，都可以解決 SNAT 連接埠的資源耗盡問題。


- 了解 [Azure 虛擬網路 NAT](./nat-overview.md)
- 深入了解 [NAT 閘道資源](./nat-gateway-resource.md)。
- [使用 Azure CLI 部署 NAT 閘道資源](./quickstart-create-nat-gateway-cli.md)的快速入門。
- [使用 Azure PowerShell 部署 NAT 閘道資源](./quickstart-create-nat-gateway-powershell.md)的快速入門。
- [使用 Azure 入口網站部署 NAT 閘道資源](./quickstart-create-nat-gateway-portal.md)的快速入門。
> [!div class="nextstepaction"]

