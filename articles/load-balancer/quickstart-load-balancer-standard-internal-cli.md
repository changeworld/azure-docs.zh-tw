---
title: 快速入門：建立內部負載平衡器 - Azure CLI
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure CLI 建立內部負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 15060a367bba2d50d7054730321f7f20d4c25e46
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916672"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>快速入門：使用 Azure CLI 來建立內部負載平衡器以平衡 VM 的負載

使用 Azure CLI 建立內部負載平衡器和三部虛擬機器，以開始使用 Azure Load Balancer。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- 本快速入門需要 2.0.28 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#az_group_create) 來建立資源群組：

* 名為 **CreateIntLBQS-rg**。 
* 在 **eastus** 位置中。

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**標準 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="針對快速入門所建立的標準負載平衡器資源。" border="false":::

## <a name="configure-virtual-network---standard"></a>設定虛擬網路 - 標準

請先建立支援的虛擬網路資源，才可部署 VM 及部署您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 建立虛擬網路：

* 具名 **myVNet**。
* 位址前置詞 **10.1.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* **10.1.0.0/24** 的子網路前置詞。
* 位於 **CreateIntLBQS-rg** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立堡壘主機的公用 IP 位址：

* 建立名為 **myBastionIP** 的標準區域備援公用 IP 位址。
* 位於 **CreateIntLBQS-rg** 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>建立 Bastion 子網路

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 建立 Bastion 子網路：

* 命名為 **AzureBastionSubnet**。
* 位址前置詞 **10.1.1.0/24**。
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreateIntLBQS-rg** 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>建立 Bastion 主機

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 建立堡壘主機：

* 命名為 **myBastionHost**。
* 位於 **CreateIntLBQS-rg** 中。
* 與公用 IP **myBastionIP** 相關聯。
* 與虛擬網路 **myVNet** 相關聯。
* 在 **eastus** 位置中。

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
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
* 在資源群組 **CreateIntLBQS-rg** 中。

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>建立網路安全性群組規則

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* 在上一個步驟中建立的網路安全性群組 **myNSG** 中。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 通訊協定 **(*)** 。
* 方向 **輸入**。
* 來源 **(*)** 。
* 目的地 **(*)** 。
* 目的地連接埠 **連接埠 80**。
* 存取 **允許**。
* 優先順序 **200**。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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
* 在資源群組 **CreateIntLBQS-rg** 中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器：

* 名為 **myVM1**、**myVM2** 和 **myVM3**。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 已連結至網路介面 **myNicVM1**、**myNicVM2** 和 **myNicVM3**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **區域 1**、**區域 2** 和 **區域 3**。

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

可能需要幾分鐘的時間部署 VM。

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
* 與虛擬網路 **myVNet** 相關聯。
* 與後端子網路 **myBackendSubnet** 相關聯。

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
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
>[!NOTE]
>後端集區中的虛擬機器將不會有使用此設定的輸出網際網路連線。 </br> 如需有關提供輸出連線的詳細資訊，請參閱： </br> **[Azure 中的輸出連線](load-balancer-outbound-connections.md)**</br> 提供連線的選項： </br> **[僅輸出負載平衡器組態](egress-only.md)** </br> **[什麼是虛擬網路 NAT？](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>將虛擬機器新增至負載平衡器後端集區

使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) 將虛擬機器新增至後端集區：

* 在後端位址集區 **myBackEndPool** 中。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 已關聯至網路介面 **myNicVM1**、**myNicVM2** 和 **myNicVM3**。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer SKU](skus.md)** 。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="快速入門中建立的基本負載平衡器資源。" border="false":::

## <a name="configure-virtual-network---basic"></a>設定虛擬網路 - 基本

請先建立支援的虛擬網路資源，才可部署 VM 及部署您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) 建立虛擬網路：

* 具名 **myVNet**。
* 位址前置詞 **10.1.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* **10.1.0.0/24** 的子網路前置詞。
* 位於 **CreateIntLBQS-rg** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立堡壘主機的公用 IP 位址：

* 建立名為 **myBastionIP** 的標準區域備援公用 IP 位址。
* 位於 **CreateIntLBQS-rg** 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>建立 Bastion 子網路

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 建立 Bastion 子網路：

* 命名為 **AzureBastionSubnet**。
* 位址前置詞 **10.1.1.0/24**。
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreateIntLBQS-rg** 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>建立 Bastion 主機

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 建立堡壘主機：

* 命名為 **myBastionHost**。
* 位於 **CreateIntLBQS-rg** 中。
* 與公用 IP **myBastionIP** 相關聯。
* 與虛擬網路 **myVNet** 相關聯。
* 在 **eastus** 位置中。

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
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
* 在資源群組 **CreateIntLBQS-rg** 中。

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>建立網路安全性群組規則

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* 在上一個步驟中建立的網路安全性群組 **myNSG** 中。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 通訊協定 **(*)** 。
* 方向 **輸入**。
* 來源 **(*)** 。
* 目的地 **(*)** 。
* 目的地連接埠 **連接埠 80**。
* 存取 **允許**。
* 優先順序 **200**。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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
* 在資源群組 **CreateIntLBQS-rg** 中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>建立虛擬機器的可用性設定組

使用 [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) 建立可用性設定組：

* 名為 **myAvailabilitySet**。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 位置 **eastus**。

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器：

* 名為 **myVM1**、**myVM2** 和 **myVM3**。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 已連結至網路介面 **myNicVM1**、**myNicVM2** 和 **myNicVM3**。
* 虛擬機器映像 **win2019datacenter**。
* 在 **myAvailabilitySet** 中。


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
可能需要幾分鐘的時間部署 VM。

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
* 與虛擬網路 **myVNet** 相關聯。
* 與後端子網路 **myBackendSubnet** 相關聯。

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
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
* 在資源群組 **CreateIntLBQS-rg** 中。
* 已關聯至網路介面 **myNicVM1**、**myNicVM2** 和 **myNicVM3**。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>測試負載平衡器

### <a name="create-test-virtual-machine"></a>建立測試虛擬機器

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 建立網路介面：

* 命名為 **myNicTestVM**。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
使用 [az vm create](/cli/azure/vm#az-vm-create) 建立虛擬機器：

* 命名為 **myTestVM**。
* 在資源群組 **CreateIntLBQS-rg** 中。
* 已連結至網路介面 **myNicTestVM**。
* 虛擬機器映像 **Win2019Datacenter**。

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
部署虛擬機器可能需要幾分鐘的時間。

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>測試

1. [登入](https://portal.azure.com) Azure 入口網站。

2. 在 [概觀] 畫面上尋找負載平衡器的私人 IP 位址。 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後選取 [myLoadBalancer]。

3. 在 **myLoadBalancer** 的 **概觀** 中，記下或複製 **私人 IP 位址** 旁的位址。

4. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單選取 **CreateIntLBQS-rg** 資源群組中的 [myTestVM]。

5. 在 [概觀] 頁面上，選取 [連線]，然後選 [Bastion]。

6. 輸入在 VM 建立期間輸入的使用者名稱和密碼。

7. 在 **myTestVM** 上開啟 **Internet Explorer**。

8. 在瀏覽器的網址列中，輸入上一個步驟中的 IP 位址。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="建立標準內部負載平衡器" border="true":::
   
若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組、負載平衡器和所有相關資源。

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>後續步驟

在本快速入門中：

* 在您建立了標準或公用負載平衡器
* 連結的虛擬機器。 
* 設定了負載平衡器流量規則和健康情況探查。
* 測試了負載平衡器。

若要深入了解 Azure Load Balancer，請繼續：
> [!div class="nextstepaction"]
> [什麼是 Azure Load Balancer？](load-balancer-overview.md)