---
title: 快速入門：建立內部負載平衡器 - Azure CLI
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure CLI 建立內部負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: dc6abc51fd0e5669126f1d2f1a977194479db5f5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269874"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>快速入門：使用 Azure CLI 來建立內部負載平衡器以平衡 VM 的負載

使用 Azure CLI 建立公用負載平衡器和三部虛擬機器，以開始使用 Azure Load Balancer。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure CLI 或 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果您選擇在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 來建立資源群組：

* 具名 **myResourceGroupLB**。 
* 在 **eastus** 位置中。

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**標準 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

## <a name="configure-virtual-network"></a>設定虛擬網路

請先建立支援的虛擬網路資源，才可部署 VM 及部署您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) 建立虛擬網路：

* 具名 **myVNet**。
* 位址前置詞 **10.1.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* **10.1.0.0/24** 的子網路前置詞。
* 在 **myResourceGroupLB** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>建立網路安全性群組

如果是標準負載平衡器，後端位址中的 VM 都需要有屬於網路安全性群組的網路介面。 

使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 建立網路安全性群組：

* 具名 **myNSG**。
* 在 **myResourceGroupLB** 資源群組中。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>建立網路安全性群組規則

使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* 在上一個步驟中建立的網路安全性群組 **myNSG** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 通訊協定 **(*)** 。
* 方向**輸入**。
* 來源 **(*)** 。
* 目的地 **(*)** 。
* 目的地連接埠**連接埠 80**。
* 存取**允許**。
* 優先順序 **200**。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
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

### <a name="create-network-interfaces-for-the-virtual-machines"></a>建立虛擬機器的網路介面

使用 [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) 建立兩個網路介面：

#### <a name="vm1"></a>VM1

* 具名 **myNicVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* 具名 **myNicVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>建立後端伺服器

在這個小節中，您會建立：

* 名為 **cloud-init.txt** 的雲端設定檔，可供伺服器設定之用。
* 兩個虛擬機器，作為負載平衡器的後端伺服器。

### <a name="create-cloud-init-configuration-file"></a>建立 Cloud-init 組態檔

使用 cloud-init 組態檔來安裝 NGINX，並在 Linux 虛擬機器上執行 'Hello World' Node.js 應用程式。 

在您目前的殼層中，建立名為 cloud-init.txt 的檔案。 將下列組態複製並貼到殼層中。 請確定您正確複製整個 cloud-init 檔案，特別是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立虛擬機器：

#### <a name="vm1"></a>VM1
* 具名 **myVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM1**。
* 虛擬機器映像 **UbuntuLTS**。
* 您在上述步驟中建立的組態檔 **cloud-init.txt**。
* 在 **區域 1** 中。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* 具名 **myVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM2**。
* 虛擬機器映像 **UbuntuLTS**。
* 您在上述步驟中建立的組態檔 **cloud-init.txt**。
* 在 **區域 2** 中。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

可能需要幾分鐘的時間部署 VM。

## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-the-load-balancer-resource"></a>建立負載平衡器資源

使用 [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) 建立公用負載平衡器：

* 具名 **myLoadBalancer**。
* 名為 **myFrontEnd** 的前端集區。
* 名為 **myBackEndPool** 的後端集區。
* 與虛擬網路 **myVNet** 相關聯。
* 與後端子網路 **myBackendSubnet** 相關聯。

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
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

使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) 建立健康狀態探查：

* 監視虛擬機器的健康情況。
* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 監視**連接埠 80**。

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
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

使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) 建立負載平衡器規則：

* 已命名為 **myHTTPRule**
* 接聽前端集區 **myFrontEnd** 中的**連接埠 80**。
* 使用**連接埠 80** 將負載平衡的網路流量傳送到後端位址集區 **myBackEndPool**。 
* 使用健康情況探查 **MyHealthProbe**。
* 通訊協定 **TCP**。
* 使用前端 IP 位址啟用輸出來源網路位址轉譯 (SNAT)。

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
>[!NOTE]
>後端集區中的虛擬機器將不會有使用此設定的輸出網際網路連線。 </br> 如需有關提供輸出連線的詳細資訊，請參閱： </br> **[Azure 中的輸出連線](load-balancer-outbound-connections.md)**</br> 提供連線的選項： </br> **[僅輸出負載平衡器組態](egress-only.md)** </br> **[什麼是虛擬網路 NAT？](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>將虛擬機器新增至負載平衡器後端集區

使用 [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) 將虛擬機器新增至後端集區：


#### <a name="vm1"></a>VM1
* 在後端位址集區 **myBackEndPool** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 與網路介面 **myNicVM1** 和 **ipconfig1** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* 在後端位址集區 **myBackEndPool** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 與網路介面 **myNicVM2** 和 **ipconfig1** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer SKU](skus.md)** 。

## <a name="configure-virtual-network"></a>設定虛擬網路

請先建立支援的虛擬網路資源，才可部署 VM 及部署您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) 建立虛擬網路：

* 具名 **myVNet**。
* 位址前置詞 **10.1.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* **10.1.0.0/24** 的子網路前置詞。
* 在 **myResourceGroupLB** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>建立網路安全性群組

如果是標準負載平衡器，後端位址中的 VM 都需要有屬於網路安全性群組的網路介面。 

使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 建立網路安全性群組：

* 具名 **myNSG**。
* 在 **myResourceGroupLB** 資源群組中。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>建立網路安全性群組規則

使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* 在上一個步驟中建立的網路安全性群組 **myNSG** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 通訊協定 **(*)** 。
* 方向**輸入**。
* 來源 **(*)** 。
* 目的地 **(*)** 。
* 目的地連接埠**連接埠 80**。
* 存取**允許**。
* 優先順序 **200**。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
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

### <a name="create-network-interfaces-for-the-virtual-machines"></a>建立虛擬機器的網路介面

使用 [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) 建立兩個網路介面：

#### <a name="vm1"></a>VM1

* 具名 **myNicVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* 具名 **myNicVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>建立後端伺服器

在這個小節中，您會建立：

* 名為 **cloud-init.txt** 的雲端設定檔，可供伺服器設定之用。 
* 虛擬機器的可用性設定組
* 兩個虛擬機器，作為負載平衡器的後端伺服器。

若要確認已成功建立負載平衡器，可在虛擬機器上安裝 NGINX。

### <a name="create-cloud-init-configuration-file"></a>建立 Cloud-init 組態檔

使用 cloud-init 組態檔來安裝 NGINX，並在 Linux 虛擬機器上執行 'Hello World' Node.js 應用程式。 

在您目前的殼層中，建立名為 cloud-init.txt 的檔案。 將下列組態複製並貼到殼層中。 請確定您正確複製整個 cloud-init 檔案，特別是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-availability-set-for-virtual-machines"></a>建立虛擬機器的可用性設定組

使用 [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) 建立可用性設定組：

* 具名 **myAvSet**。
* 在 **myResourceGroupLB** 資源群組中。
* 位置 **eastus**。

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立虛擬機器：

#### <a name="vm1"></a>VM1
* 具名 **myVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM1**。
* 虛擬機器映像 **UbuntuLTS**。
* 您在上述步驟中建立的組態檔 **cloud-init.txt**。
* 在可用性設定組 **myAvSet** 中。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* 具名 **myVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM2**。
* 虛擬機器映像 **UbuntuLTS**。
* 您在上述步驟中建立的組態檔 **cloud-init.txt**。
* 在 **區域 2** 中。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

可能需要幾分鐘的時間部署 VM。

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-the-load-balancer-resource"></a>建立負載平衡器資源

使用 [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) 建立公用負載平衡器：

* 具名 **myLoadBalancer**。
* 名為 **myFrontEnd** 的前端集區。
* 名為 **myBackEndPool** 的後端集區。
* 與虛擬網路 **myVNet** 相關聯。
* 與後端子網路 **myBackendSubnet** 相關聯。

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
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

使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) 建立健康狀態探查：

* 監視虛擬機器的健康情況。
* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 監視**連接埠 80**。

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
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

使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) 建立負載平衡器規則：

* 已命名為 **myHTTPRule**
* 接聽前端集區 **myFrontEnd** 中的**連接埠 80**。
* 使用**連接埠 80** 將負載平衡的網路流量傳送到後端位址集區 **myBackEndPool**。 
* 使用健康情況探查 **MyHealthProbe**。
* 通訊協定 **TCP**。

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>將虛擬機器新增至負載平衡器後端集區

使用 [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) 將虛擬機器新增至後端集區：


#### <a name="vm1"></a>VM1
* 在後端位址集區 **myBackEndPool** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 與網路介面 **myNicVM1** 和 **ipconfig1** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* 在後端位址集區 **myBackEndPool** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 與網路介面 **myNicVM2** 和 **ipconfig1** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>測試負載平衡器

### <a name="create-azure-bastion-public-ip"></a>建立 Azure Bastion 公用 IP

使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 建立堡壘主機的公用 IP 位址：

* 建立名為 **myBastionIP** 的標準區域備援公用 IP 位址。
* 在 **myResourceGroupLB**。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>建立 Azure Bastion 子網路

使用 [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) 建立子網路：

* 命名為 **AzureBastionSubnet**。
* 位址前置詞 **10.1.1.0/24**。
* 在虛擬網路 **MyVNet** 中。
* 在 **myResourceGroupLB** 資源群組中。

```azurecli-interactive
  az network vnet subnet create \
    --resource-group myResourceGroupLB \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>建立 Azure Bastion 主機
使用 [az network bastion create](https://docs.microsoft.com/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) 建立堡壘主機：

* 命名為 **myBastionHost**
* 在 **myResourceGroupLB**中
* 與公用 IP **myBastionIP** 相關聯。
* 與虛擬網路 **myVNet** 相關聯。
* 在 **eastus** 位置中。

```azurecli-interactive
  az network bastion create \
    --resource-group myResourceGroupLB \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
部署堡壘主機需要幾分鐘的時間。

### <a name="create-test-virtual-machine"></a>建立測試虛擬機器

使用 [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) 建立網路介面：

* 命名為 **myNicTestVM**。
* 在 **myResourceGroupLB** 資源群組中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立虛擬機器：

* 命名為 **myTestVM**。
* 在 **myResourceGroupLB** 資源群組中。
* 已連結至網路介面 **myNicTestVM**。
* 虛擬機器映像 **Win2019Datacenter**。
* 選擇 **\<adminpass>** 和 **\<adminuser>** 的值。
  

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
部署虛擬機器可能需要幾分鐘的時間。

### <a name="test"></a>測試

1. [登入](https://portal.azure.com) Azure 入口網站。

1. 在 [概觀] 畫面上尋找負載平衡器的私人 IP 位址。 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後選取 [myLoadBalancer]。

2. 在 **myLoadBalancer** 的**概觀**中，記下或複製**私人 IP 位址** 旁的位址。

3. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單選取 **myResourceGroupLB** 資源群組中的 [myTestVM]。

4. 在 [概觀] 頁面上，選取 [連線]，然後選 [Bastion]。

6. 輸入在 VM 建立期間輸入的使用者名稱和密碼。

7. 在 **myTestVM** 上開啟 **Internet Explorer**。

8. 在瀏覽器的網址列中，輸入上一個步驟中的 IP 位址。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="建立標準內部負載平衡器" border="true":::
   
若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令來移除資源群組、負載平衡器和所有相關資源。

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>後續步驟
在本快速入門中

* 在您建立了標準或公用負載平衡器
* 連結的虛擬機器。 
* 設定了負載平衡器流量規則和健康情況探查。
* 測試了負載平衡器。

如需 Azure Load Balancer 的詳細資訊，請繼續參閱[什麼是 Azure Load Balancer？](load-balancer-overview.md)和 [Load Balancer 常見問題集](load-balancer-faqs.md)。

深入了解 [Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。
