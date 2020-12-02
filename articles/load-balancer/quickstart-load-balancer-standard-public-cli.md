---
title: 快速入門：建立公用負載平衡器 - Azure CLI
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure CLI 建立公用負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: b00d0c83758d0349fd3926e0c263b65af2e4dc92
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96021175"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>快速入門：使用 Azure CLI 建立公用負載平衡器以平衡 VM 的負載

使用 Azure CLI 建立公用負載平衡器和三部虛擬機器，以開始使用 Azure Load Balancer。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本快速入門需要 2.0.28 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#az-group-create) 來建立資源群組：

* 名為 **CreatePubLBQS-rg**。 
* 在 **eastus** 位置中。

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**標準 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

## <a name="configure-virtual-network---standard"></a>設定虛擬網路 - 標準

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) 建立虛擬網路：

* 具名 **myVNet**。
* 位址前置詞 **10.1.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* **10.1.0.0/24** 的子網路前置詞。
* 位於 **CreatePubLBQS-rg** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立堡壘主機的公用 IP 位址：

* 建立名為 **myBastionIP** 的標準區域備援公用 IP 位址。
* 在 **CCreatePubLBQS-rg** 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>建立 Bastion 子網路

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 建立 Bastion 子網路：

* 命名為 **AzureBastionSubnet**。
* 位址前置詞 **10.1.1.0/24**。
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreatePubLBQS-rg** 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>建立 Bastion 主機

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 建立堡壘主機：

* 命名為 **myBastionHost**。
* 位於 **CreatePubLBQS-rg** 中。
* 與公用 IP **myBastionIP** 相關聯。
* 與虛擬網路 **myVNet** 相關聯。
* 在 **eastus** 位置中。

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主機需要幾分鐘的時間。

### <a name="create-a-network-security-group"></a>建立網路安全性群組

如果是標準負載平衡器，後端位址中的 VM 都需要有屬於網路安全性群組的網路介面。 

使用 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) 建立網路安全性群組：

* 具名 **myNSG**。
* 在資源群組 **CreatePubLBQS-rg** 中。

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>建立網路安全性群組規則

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* 在上一個步驟中建立的網路安全性群組 **myNSG** 中。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 通訊協定 **(*)** 。
* 方向 **輸入**。
* 來源 **(*)** 。
* 目的地 **(*)** 。
* 目的地連接埠 **連接埠 80**。
* 存取 **允許**。
* 優先順序 **200**。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>建立後端伺服器 - 標準

在這個小節中，您會建立：

* 虛擬機器的三個網路介面。
* 三個虛擬機器，作為負載平衡器的後端伺服器。

### <a name="create-network-interfaces-for-the-virtual-machines"></a>建立虛擬機器的網路介面

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 建立三個網路介面：

* 名為 **myNicVM1**、**myNicVM2** 和 **myNicVM3**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器：

### <a name="vm1"></a>VM1
* 具名 **myVM1**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 連結到網路介面 **myNicVM1**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **區域 1** 中。

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* 具名 **myVM2**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 連結到網路介面 **myNicVM2**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **區域 2** 中。

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* 具名 **myVM3**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 連結到網路介面 **myNicVM3**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **區域 3** 中。

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
可能需要幾分鐘的時間部署 VM。

## <a name="create-a-public-ip-address---standard"></a>建立公用 IP 位址 - 標準

若要在網際網路上存取您的 Web 應用程式，您需要負載平衡器的公用 IP 位址。 

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 來：

* 建立名為 **myPublicIP** 的標準區域冗餘公用 IP 位址。
* 位於 **CreatePubLBQS-rg** 中。

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

若要在區域 1 中建立區域性冗餘公用 IP 位址：

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-the-load-balancer-resource"></a>建立負載平衡器資源

使用 [az network lb create](/cli/azure/network/lb#az-network-lb-create) 建立公用負載平衡器：

* 具名 **myLoadBalancer**。
* 名為 **myFrontEnd** 的前端集區。
* 名為 **myBackEndPool** 的後端集區。
* 與您在上一個步驟中建立的公用 IP 位址 **myPublicIP** 相關聯。 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>建立健康狀態探查

健全狀況探查會檢查所有虛擬機器執行個體，確認可以傳送網路流量。 

已從負載平衡器移除具有失敗探查檢查的虛擬機器。 解決失敗情況之後，系統會將虛擬機器新增回負載平衡器。

使用 [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) 建立健康狀態探查：

* 監視虛擬機器的健康情況。
* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 監視 **連接埠 80**。

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則定義：

* 傳入流量的前端 IP 設定。
* 接收流量的後端 IP 集區。
* 所需的來源和目的地連接埠。 

使用 [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) 建立負載平衡器規則：

* 已命名為 **myHTTPRule**
* 接聽前端集區 **myFrontEnd** 中的 **連接埠 80**。
* 使用 **連接埠 80** 將負載平衡的網路流量傳送到後端位址集區 **myBackEndPool**。 
* 使用健康情況探查 **MyHealthProbe**。
* 通訊協定 **TCP**。
* 閒置逾時時間為 **15 分鐘**。
* 啟用 TCP 重設。


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>將虛擬機器新增至負載平衡器後端集區

使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) 將虛擬機器新增至後端集區：

* 在後端位址集區 **myBackEndPool** 中。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>建立輸出規則設定
負載平衡器輸出規則會在後端集區中設定 VM 的輸出 SNAT。 

如需輸出連線詳細資訊，請參閱 [Azure 中的輸出連線](load-balancer-outbound-connections.md)。

公用 IP 或首碼可以用於輸出組態。

### <a name="public-ip"></a>公用 IP

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立輸出連線的單一 IP。  

* 具名 **myPublicIPOutbound**。
* 位於 **CreatePubLBQS-rg** 中。

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

若要在區域 1 中建立區域性冗餘公用 IP 位址：

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>公用 IP 首碼

使用 [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) 建立輸出連線的公用 IP 前置詞。

* 具名 **myPublicIPPrefixOutbound**。
* 位於 **CreatePubLBQS-rg** 中。
* 前置長度 **28**。

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
若要在區域 1 中建立區域性冗餘公用 IP 前置詞：

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

如需有關調整輸出 NAT 和輸出連線能力的詳細資訊，請參閱[使用多個 IP 位址調整輸出 NAT](load-balancer-outbound-connections.md)。

### <a name="create-outbound-frontend-ip-configuration"></a>建立輸出前端 IP 組態

使用 [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) 建立新的前端 IP 設定：

根據先前步驟中的決定，選取公用 IP 或公用 IP 前置詞命令。

#### <a name="public-ip"></a>公用 IP

* 具名 **myFrontEndOutbound**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 與公用 IP 位址 **myPublicIPOutbound** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>公用 IP 首碼

* 具名 **myFrontEndOutbound**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 與公用 IP 前置詞 **myPublicIPPrefixOutbound** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>建立輸出集區

使用 [az network lb address-pool create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) 建立新的輸出集區：

* 具名 **myBackendPoolOutbound**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>建立輸出規則

使用 [az network lb outbound-rule create](/cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-create) 為輸出後端集區建立新的輸出規則：

* 具名 **myOutboundRule**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 與負載平衡器 **myLoadBalancer** 相關聯
* 與前端 **myFrontEndOutbound** 相關聯。
* 通訊協定 **所有**。
* 閒置逾時 **15**。
* 輸出連接埠 **10000**。
* 與後端集區 **myBackEndPoolOutbound** 相關聯。

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>將虛擬機器新增至輸出集區

將虛擬機器新增至具有 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) 的輸出集區：


* 在後端位址集區 **myBackEndPoolOutbound** 中。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

## <a name="configure-virtual-network---basic"></a>設定虛擬網路 - 基本

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 建立虛擬網路：

* 具名 **myVNet**。
* 位址前置詞 **10.1.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* **10.1.0.0/24** 的子網路前置詞。
* 位於 **CreatePubLBQS-rg** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立堡壘主機的公用 IP 位址：

* 建立名為 **myBastionIP** 的標準區域備援公用 IP 位址。
* 位於 **CreatePubLBQS-rg** 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>建立 Bastion 子網路

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 建立 Bastion 子網路：

* 命名為 **AzureBastionSubnet**。
* 位址前置詞 **10.1.1.0/24**。
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreatePubLBQS-rg** 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>建立 Bastion 主機

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 建立堡壘主機：

* 命名為 **myBastionHost**。
* 位於 **CreatePubLBQS-rg** 中。
* 與公用 IP **myBastionIP** 相關聯。
* 與虛擬網路 **myVNet** 相關聯。
* 在 **eastus** 位置中。

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主機需要幾分鐘的時間。

### <a name="create-a-network-security-group"></a>建立網路安全性群組

如果是標準負載平衡器，後端位址中的 VM 都需要有屬於網路安全性群組的網路介面。 

使用 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) 建立網路安全性群組：

* 具名 **myNSG**。
* 在資源群組 **CreatePubLBQS-rg** 中。

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>建立網路安全性群組規則

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* 在上一個步驟中建立的網路安全性群組 **myNSG** 中。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 通訊協定 **(*)** 。
* 方向 **輸入**。
* 來源 **(*)** 。
* 目的地 **(*)** 。
* 目的地連接埠 **連接埠 80**。
* 存取 **允許**。
* 優先順序 **200**。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>建立後端伺服器 - 基本

在這個小節中，您會建立：

* 虛擬機器的三個網路介面。
* 虛擬機器的可用性設定組
* 三個虛擬機器，作為負載平衡器的後端伺服器。


### <a name="create-network-interfaces-for-the-virtual-machines"></a>建立虛擬機器的網路介面

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 建立三個網路介面：


* 名為 **myNicVM1**、**myNicVM2** 和 **myNicVM3**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>建立虛擬機器的可用性設定組

使用 [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) 建立可用性設定組：

* 具名 **myAvSet**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 位置 **eastus**。

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器：

### <a name="vm1"></a>VM1
* 具名 **myVM1**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 連結到網路介面 **myNicVM1**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **區域 1** 中。

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* 具名 **myVM2**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 連結到網路介面 **myNicVM2**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **區域 2** 中。

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* 具名 **myVM3**。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 連結到網路介面 **myNicVM3**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **區域 3** 中。

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
可能需要幾分鐘的時間部署 VM。

## <a name="create-a-public-ip-address---basic"></a>建立公用 IP 位址 - 基本

若要在網際網路上存取您的 Web 應用程式，您需要負載平衡器的公用 IP 位址。 

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 來：

* 建立名為 **myPublicIP** 的標準區域冗餘公用 IP 位址。
* 位於 **CreatePubLBQS-rg** 中。

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-the-load-balancer-resource"></a>建立負載平衡器資源

使用 [az network lb create](/cli/azure/network/lb#az-network-lb-create) 建立公用負載平衡器：

* 具名 **myLoadBalancer**。
* 名為 **myFrontEnd** 的前端集區。
* 名為 **myBackEndPool** 的後端集區。
* 與您在上一個步驟中建立的公用 IP 位址 **myPublicIP** 相關聯。 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>建立健康狀態探查

健全狀況探查會檢查所有虛擬機器執行個體，確認可以傳送網路流量。 

已從負載平衡器移除具有失敗探查檢查的虛擬機器。 解決失敗情況之後，系統會將虛擬機器新增回負載平衡器。

使用 [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) 建立健康狀態探查：

* 監視虛擬機器的健康情況。
* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 監視 **連接埠 80**。

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則定義：

* 傳入流量的前端 IP 設定。
* 接收流量的後端 IP 集區。
* 所需的來源和目的地連接埠。 

使用 [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) 建立負載平衡器規則：

* 已命名為 **myHTTPRule**
* 接聽前端集區 **myFrontEnd** 中的 **連接埠 80**。
* 使用 **連接埠 80** 將負載平衡的網路流量傳送到後端位址集區 **myBackEndPool**。 
* 使用健康情況探查 **MyHealthProbe**。
* 通訊協定 **TCP**。
* 閒置逾時時間為 **15 分鐘**。

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>將虛擬機器新增至負載平衡器後端集區

使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) 將虛擬機器新增至後端集區：

* 在後端位址集區 **myBackEndPool** 中。
* 在資源群組 **CreatePubLBQS-rg** 中。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>安裝 IIS

使用 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) 在虛擬機器上安裝 IIS，並將預設網站設為電腦名稱。

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>測試負載平衡器

若要取得負載平衡器的公用 IP 位址，請使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

將公用 IP 位址複製並貼到您瀏覽器的網址列。

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="測試負載平衡器" border="true":::

## <a name="clean-up-resources"></a>清除資源

若不再需要，使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組、負載平衡器和所有相關資源。

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>後續步驟

在本快速入門中

* 在您建立了標準或公用負載平衡器
* 連結的虛擬機器。 
* 設定了負載平衡器流量規則和健康情況探查。
* 測試了負載平衡器。

若要深入了解 Azure Load Balancer，請繼續：
> [!div class="nextstepaction"]
> [什麼是 Azure Load Balancer？](load-balancer-overview.md)