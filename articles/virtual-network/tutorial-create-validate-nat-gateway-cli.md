---
title: 教學課程：建立和測試 NAT 閘道 - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: 本教學課程將說明如何使用 Azure CLI 建立 NAT 閘道，並測試 NAT 服務
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: b1ca26a63c910861d333f707d13946c5e046f599
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84340975"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>教學課程：使用 Azure CLI 建立 NAT 閘道並測試 NAT 服務

在本教學課程中，您將建立 NAT 閘道，為 Azure 中的虛擬機器提供輸出連線能力。 若要測試 NAT 閘道，請部署來源和目的地虛擬機器。 您將會藉由對公用 IP 位址進行輸出連線，來測試 NAT 閘道。 這些連線會從來源虛擬機器連結到目的地虛擬機器。 僅為了簡單起見，本教學課程會將來源和目的地部署在相同資源群組中的兩個不同虛擬網路中。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 完成本教學課程，或在本機執行個別命令。  如果您尚未使用 Azure Cloud Shell，應該[立即登入](https://shell.azure.com)。

如果您選擇在本機執行這些命令，則必須安裝 CLI。  在本教學課程中，您必須執行 Azure CLI 2.0.71 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。


## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 **eastus2** 位置建立名為 myResourceGroupNAT 的資源群組：

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>建立 NAT 閘道

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

若要存取公用網際網路，您需要有一個或多個適用於 NAT 閘道的公用 IP 位址。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)，在 **myResourceGroupNAT** 中建立名為 **myPublicIPsource** 的公用 IP 位址資源。

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>建立公用 IP 前置詞

您可以搭配 NAT 閘道使用一或多個公用 IP 位址資源、公用 IP 前置詞或兩者。 我們將在此案例中新增公用 IP 前置詞資源進行示範。   使用 [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create)，在 **myResourceGroupNAT** 中建立名為 **myPublicIPprefixsource** 的公用 IP 前置詞資源。

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>建立 NAT 閘道資源

本節將詳細說明如何使用 NAT 閘道資源建立及設定下列 NAT 服務元件：
  - 公用 IP 集區和公用 IP 前置詞，將用於 NAT 閘道資源所轉譯的輸出流量。
  - 將閒置逾時時間從預設的 4 分鐘變更為 10 分鐘。

使用 [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) 建立全域 Azure NAT 閘道，其名稱為 **myNATgateway**。 此命令會使用公用 IP 位址 **myPublicIP** 和公用 IP 前置詞 **myPublicIPprefix**。 此命令也會將閒置逾時變更為 10 分鐘。

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

此時，NAT 閘道可正常運作，而現在唯一缺少的就是設定虛擬網路的哪些子網路應使用此閘道。

## <a name="prepare-the-source-for-outbound-traffic"></a>準備輸出流量的來源

我們會引導您完成完整的測試環境設定。 您將使用開放原始碼工具來設定測試，以驗證 NAT 閘道。 我們將從來源開始，來源會使用我們先前建立的 NAT 閘道。

### <a name="configure-virtual-network-for-source"></a>設定來源的虛擬網路

在您部署 VM 並可測試 NAT 閘道之前，我們必須先建立虛擬網路。

使用 [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet)，在 **myResourceGroupNAT** 中建立名為 **myVnetsource** 的虛擬網路，其具有名為 **mySubnetsource** 的子網路。  虛擬網路的 IP 位址空間為 **192.168.0.0/16**。 虛擬網路內的子網路為 **192.168.0.0/24**。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>設定來源子網路的 NAT 服務

在虛擬網路 **myVnetsource** 中設定來源子網路 **mySubnetsource**，以使用特定 NAT 閘道資源 **myNATgateway** 搭配 [az network Microsoft Azure Virtual Network subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)。 此命令將會在指定的子網路上啟用 NAT 服務。

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

所有連結到網際網路目的地的輸出流量現在都會使用 NAT 服務。  您不需要設定 UDR。

在開始測試 NAT 閘道之前，我們必須先建立來源 VM。  我們會將公用 IP 位址資源指派為執行個體層級的公用 IP，以從外部存取此 VM。 此位址只會用來存取此 VM 以進行測試。  我們將示範如何讓 NAT 服務的優先順序高於其他輸出選項。

您也可以在不使用公用 IP 的情況下建立此 VM，並建立另一個 VM 作為 Jumpbox，而不需要公用 IP 來進行練習。

### <a name="create-public-ip-for-source-vm"></a>建立來源 VM 的公用 IP

我們會建立用來存取來源 VM 的公用 IP。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)，在 **myResourceGroupNAT** 中建立名為 **myPublicIPsourceVM** 的公用 IP 位址資源。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>建立來源 VM 的 NSG

因為標準公用 IP 位址屬於「預設保護」，所以我們需要建立 NSG 來允許 SSH 的輸入存取。  Azure NAT 服務可感知流量方向。 如果 NAT 閘道設定在相同子網路上，則此 NSG 將不會用於輸出。 使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)，在 **myResourceGroupNAT** 中建立名為 **myNSGsource** 的 NSG 資源。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>公開來源 VM 上的 SSH 端點

我們會在 NSG 中建立規則，以供對來源 VM 進行 SSH 存取。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 建立名為 **ssh**的 NSG 規則。 此規則將會在 **myResourceGroupNAT** 資源群組中建立於名為 **myNSGsource** 的 NSG 中。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>建立來源 VM 的 NIC

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 建立網路介面，並使其與公用 IP 位址和網路安全性群組產生關聯。 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>建立來源 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器。  我們會為此 VM 產生 SSH 金鑰，並儲存私密金鑰以便稍後使用。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

雖然命令會立即傳回，但可能需要幾分鐘的時間來部署 VM。

## <a name="prepare-destination-for-outbound-traffic"></a>準備輸出流量的目的地

我們現在會針對 NAT 服務所轉譯的輸出流量建立目的地，以便您進行測試。

### <a name="configure-virtual-network-for-destination"></a>設定目的地的虛擬網路

 我們需要建立將放置目的地虛擬機器的虛擬網路。  下列命令是與來源 VM 相同的步驟 (有些小變動)，用以公開目的地端點。

使用 [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet)，在 **myResourceGroupNAT** 中建立名為 **myVnetdestination** 的虛擬網路，其具有名為 **mySubnetdestination** 的子網路。  虛擬網路的 IP 位址空間為 **192.168.0.0/16**。 虛擬網路內的子網路為 **192.168.0.0/24**。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>建立目的地 VM 的公用 IP

我們會建立用來存取來源 VM 的公用 IP。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)，在 **myResourceGroupNAT** 中建立名為 **myPublicIPdestinationVM** 的公用 IP 位址資源。 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>建立目的地 VM 的 NSG

標準公用 IP 位址屬於「預設保護」，因此您需要建立 NSG 來允許 SSH 的輸入存取。 Azure NAT 服務可感知流量方向。 如果 NAT 閘道設定在相同子網路上，則此 NSG 將不會用於輸出。 使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)，在 **myResourceGroupNAT** 中建立名為 **myNSGdestination** 的 NSG 資源。

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>公開目的地 VM 上的 SSH 端點

我們會在 NSG 中建立規則，以供對目的地 VM 進行 SSH 存取。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 建立名為 **ssh**的 NSG 規則。 此規則將會在 **myResourceGroupNAT** 資源群組中建立於名為 **myNSGdestination** 的 NSG 中。

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>公開目的地 VM 上的 HTTP 端點

我們會在 NSG 中建立規則，以供對目的地 VM 進行 HTTP 存取。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)，在 **myResourceGroupNAT** 中名為 **myNSGdestination** 的 NSG 中建立名為 **http** 的 NSG 規則。

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>建立目的地 VM 的 NIC

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 建立網路介面，並使其與公用 IP 位址 **myPublicIPdestinationVM**和網路安全性群組 **myNSGdestination**產生關聯。 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>建立目的地 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器。  我們會為此 VM 產生 SSH 金鑰，並儲存私密金鑰以便稍後使用。

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
雖然命令會立即傳回，但可能需要幾分鐘的時間來部署 VM。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>在目的地 VM 上準備 Web 伺服器和測試承載

首先，我們需要探索目的地 VM 的 IP 位址。  若要取得目的地 VM 的公用 IP 位址，請使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便在後續步驟中使用。 指出這是目的地虛擬機器。

### <a name="sign-in-to-destination-vm"></a>登入目的地 VM

在上一個作業中，SSH 認證應該已儲存在您的 Cloud Shell 中。  在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。 

```bash
ssh <ip-address-destination>
```

當您登入之後，請複製並貼上下列命令。  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

這些命令會更新您的虛擬機器、安裝 nginx，並建立 100 Kb 的檔案。 系統會使用 NAT 服務從來源 VM 擷取此檔案。

關閉目的地 VM 的 SSH 工作階段。

## <a name="prepare-test-on-source-vm"></a>在來源 VM 上準備測試

首先，我們需要探索來源 VM 的 IP 位址。  若要取得來源 VM 的公用 IP 位址，請使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便在後續步驟中使用。 指出這是來源虛擬機器。

### <a name="sign-in-to-source-vm"></a>登入來源 VM

同樣地，SSH 認證會儲存在 Cloud Shell 中。 在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com) 的新索引標籤。  使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。 

```bash
ssh <ip-address-source>
```

複製並貼上下列命令，以準備測試 NAT 服務。

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

此命令會更新您的虛擬機器、安裝 go、從 GitHub 安裝 [hey](https://github.com/rakyll/hey)，以及更新您的殼層環境。

您現在已經準備好測試 NAT 服務。

## <a name="validate-nat-service"></a>驗證 NAT 服務

登入來源 VM 時，您可以使用 **curl** 和 **hey** 來產生目的地 IP 位址的要求。

使用 curl 來擷取 100 KB 的檔案。  以您先前複製的目的地 IP 位址，取代下列範例中的 **\<ip-address-destination>** 。  **--output** 參數表示將會捨棄擷取的檔案。

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

您也可以使用 **hey** 來產生一系列的要求。 再以您先前複製的目的地 IP 位址，取代 **\<ip-address-destination>** 。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

此命令將會產生 100 個要求 (一次 10 個)，並將逾期時間設為 30 秒。 TCP 連線不會重複使用。  每個要求都會擷取 100 KB。  在回合結束時，**hey** 會回報 NAT 服務執行效能的相關統計資料。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組及其內含的所有資源。

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立 NAT 閘道、建立來源和目的地 VM，然後測試了 NAT 閘道。

接著請檢閱 Azure 監視器中的計量，以了解 NAT 服務的運作。 診斷可用 SNAT 連接埠的資源耗盡相關問題。  新增額外的公用 IP 位址資源或公用 IP 前置詞資源 (或兩者)，都可以輕鬆地解決 SNAT 連接埠的資源耗盡問題。

- 了解[虛擬網路 NAT](./nat-overview.md)
- 深入了解 [NAT 閘道資源](./nat-gateway-resource.md)。
- [使用 Azure CLI 部署 NAT 閘道資源](./quickstart-create-nat-gateway-cli.md)的快速入門。
- [使用 Azure PowerShell 部署 NAT 閘道資源](./quickstart-create-nat-gateway-powershell.md)的快速入門。
- [使用 Azure 入口網站部署 NAT 閘道資源](./quickstart-create-nat-gateway-portal.md)的快速入門。

> [!div class="nextstepaction"]

