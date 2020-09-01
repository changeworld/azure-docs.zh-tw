---
title: 使用 Azure CLI 部署和設定 Azure 防火牆
description: 在本文中，您將瞭解如何使用 Azure CLI 來部署和設定 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 7f00b57edb37cc5bb5c8340663d619e526c2eacb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075421"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>使用 Azure CLI 部署和設定 Azure 防火牆

控制輸出網路存取是整體網路安全性計畫的重要部分。 例如，您可以限制對網站的存取。 或者，限制可存取的輸出 IP 位址和連接埠。

要控制從 Azure 子網路的輸出網路存取，使用 Azure 防火牆是可行的方式之一。 透過 Azure 防火牆，您可以設定：

* 應用程式規則，用以定義可從子網路存取的完整網域名稱 (FQDN)。 FQDN 也可以 [包含 SQL 實例](sql-fqdn-filtering.md)。
* 網路規則，用以定義來源位址、通訊協定、目的地連接埠和目的地位址。

當您將網路流量路由傳送到防火牆作為子網路預設閘道時，網路流量必須遵守設定的防火牆規則。

在本文中，您會建立具有三個子網的簡化單一 VNet，以方便部署。 針對生產部署，建議使用[中樞與輪輻模型](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 防火牆位於自己的 VNet 中。 工作負載伺服器位於相同區域中的對等互連 VNet，其中包含一個或多個子網路。

* **AzureFirewallSubnet** - 防火牆位於此子網路。
* **Workload-SN** - 工作負載伺服器位於此子網路。 此子網路的網路流量會通過防火牆。
* **Jump-SN** - 跳板機位於此子網路。 跳板機具有公用 IP 位址，您可以使用遠端桌面與其連線。 接著，您可以從此處 (使用其他的遠端桌面) 連線到工作負載伺服器。

![教學課程網路基礎結構](media/tutorial-firewall-rules-portal/Tutorial_network.png)

在本文中，您將學會如何：

* 設定測試網路環境
* 部署防火牆
* 建立預設路由
* 設定允許存取 www.google.com 的應用程式規則
* 設定允許存取外部 DNS 伺服器的網路規則
* 測試防火牆

如果您想要的話，可以使用 [Azure 入口網站](tutorial-firewall-deploy-portal.md) 或 [Azure PowerShell](deploy-ps.md)來完成此程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

### <a name="azure-cli"></a>Azure CLI

如果您選擇在本機安裝和使用 CLI，請執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 **az--version**。 如需安裝或升級的相關資訊，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

安裝 Azure 防火牆延伸模組：

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>設定網路

首先，請建立資源群組，以包含部署防火牆所需的資源。 接著建立 VNet、子網路，和測試伺服器。

### <a name="create-a-resource-group"></a>建立資源群組

資源群組包含部署的所有資源。

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>建立 VNet

此虛擬網路有三個子網。

> [!NOTE]
> AzureFirewallSubnet 子網路的大小是 /26。 如需有關子網路大小的詳細資訊，請參閱 [Azure 防火牆的常見問題集](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)。

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>建立虛擬機器

現在建立跳板和工作負載虛擬機器，並將它們放在適當的子網路中。
出現提示時，輸入虛擬機器的密碼。

建立 Srv 跳躍虛擬機器。

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



建立適用于 Srv 的 NIC-使用特定的 DNS 伺服器 IP 位址，而不使用公用 IP 位址來進行測試。

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

現在，請建立工作負載虛擬機器。
出現提示時，輸入虛擬機器的密碼。

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>部署防火牆

現在將防火牆部署到虛擬網路中。

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

請記下私人 IP 位址。 稍後當您建立預設路由時將使用到它。

## <a name="create-a-default-route"></a>建立預設路由

建立已停用 BGP 路由傳播的資料表

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

建立路由。

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

將路由表與子網產生關聯

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>設定應用程式規則

應用程式規則允許對 www.google.com 的輸出存取。

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Azure 防火牆包含內建的規則集合，適用於依預設允許的基礎結構 FQDN。 這些 FQDN 是平台特定的，且無法用於其他用途。 如需詳細資訊，請參閱[基礎結構 FQDN](infrastructure-fqdns.md)。

## <a name="configure-a-network-rule"></a>設定網路規則

網路規則允許輸出存取埠 53 (DNS) 的兩個 IP 位址。

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>測試防火牆

現在請測試防火牆，以確認其運作符合預期。

1. 請注意 **Srv 工作** 虛擬機器的私人 IP 位址：

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. 將遠端桌面連線到 **Srv-Jump** 虛擬機器，然後登入。 從該處開啟與 **Srv 工作** 私人 IP 位址的遠端桌面連線，然後登入。

3. 在 **SRV 工作**上，開啟 PowerShell 視窗並執行下列命令：

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   這兩個命令都應該會傳回答案，顯示您的 DNS 查詢正在通過防火牆。

1. 執行下列命令：

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   `www.google.com`要求應該會成功，且 `www.microsoft.com` 要求應該會失敗。 這會示範您的防火牆規則如預期般運作。

因此，現在您已確認防火牆規則正在運作：

* 您可以使用設定的外部 DNS 伺服器來解析 DNS 名稱。
* 您可以瀏覽至允許 FQDN 的防火牆規則，但不可瀏覽至任何其他的防火牆規則。

## <a name="clean-up-resources"></a>清除資源

您可以保留防火牆資源供下一個教學課程，或如果不再需要，請刪除 **測試 FW-RG** 資源群組，以刪除所有防火牆相關資源：

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>後續步驟

* [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
