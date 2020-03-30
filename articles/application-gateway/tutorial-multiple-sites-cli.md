---
title: 使用 CLI 託管多個網站
titleSuffix: Azure Application Gateway
description: 了解如何使用 Azure CLI，以建立裝載多個網站的應用程式閘道。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0a92d0f7d17f6bb83efbe94434c25072975dbe57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047348"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>使用 Azure CLI 建立裝載多個網站的應用程式閘道

您可以使用 Azure CLI，在建立[應用程式閘道](overview.md)時設定[裝載多個網站](multiple-site-overview.md)。 在本文中，您可以使用虛擬機器擴展集來定義後端位址集區。 接著，您可以根據擁有的網域來設定接聽程式和規則，確保網路流量會抵達集區中的適當伺服器。 本文假設您擁有多個網域，並使用 *www\.contoso.com* 和 *www\.fabrikam.com* 範例。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 設定網路
> * 建立應用程式閘道
> * 建立後端接聽程式
> * 建立路由規則
> * 建立包含後端集區的虛擬機器擴展集
> * 在網域中建立 CNAME 記錄

![多站台路由範例](./media/tutorial-multiple-sites-cli/scenario.png)

如果您想要的話，可以使用 [Azure PowerShell](tutorial-multiple-sites-powershell.md) 完成本程序。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 CLI，則本文要求您運行 Azure CLI 版本 2.0.4 或更高版本。 若要尋找版本，請執行 `az --version`。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 [az group create](/cli/azure/group) 建立資源群組。

下列範例會在 eastus** 位置建立名為 myResourceGroupAG** 的資源群組。

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>建立網路資源

使用 [az network vnet create](/cli/azure/network/vnet) 建立虛擬網路以及名為 myAGSubnet** 的子網路。 然後您可以使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet) 新增後端伺服器所需的子網路。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立名為 myAGPublicIPAddress** 的公用 IP 位址。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

您可以使用 [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) 來建立應用程式閘道。 當您使用 Azure CLI 建立應用程式閘道時，需要指定設定資訊，例如容量、SKU 和 HTTP 設定。 應用程式閘道會指派給您先前建立的 myAGSubnet** 和 myAGPublicIPAddress**。 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

可能需要幾分鐘的時間來建立應用程式閘道。 建立應用程式閘道後，您可以看到它的這些新功能：

- appGatewayBackendPool** - 應用程式閘道必須至少有一個後端位址集區。
- appGatewayBackendHttpSettings** - 指定以連接埠 80 和 HTTP 通訊協定來進行通訊。
- appGatewayHttpListener** - 與 appGatewayBackendPool** 相關聯的預設接聽程式。
- appGatewayFrontendIP** - 將 myAGPublicIPAddress** 指派給 appGatewayHttpListener**。
- rule1** - 與 appGatewayHttpListener** 相關聯的預設路由規則。

### <a name="add-the-backend-pools"></a>新增後端集區

添加使用[az 網路應用程式閘道位址池創建](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create)來包含後端伺服器所需的後端池
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-backend-listeners"></a>新增後端接聽程式

使用 [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create)，以新增路由流量時所需的後端接聽程式。

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>新增路由規則

規則按列出的連續處理。 無論具體性如何，流量都使用匹配的第一個規則定向。 例如，如果您在相同的連接埠上同時使用基本接聽程式的規則和多站台接聽程式的規則，則必須將多站台接聽程式的規則列於基本接聽程式的規則之前，多站台規則才能如預期般運作。 

在此示例中，您將創建兩個新規則，並刪除部署應用程式閘道時創建的預設規則。 您可以使用 [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) 來新增規則。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>建立虛擬機器擴展集

在此範例中，您會建立三個虛擬機器擴展集，以支援應用程式閘道中的三個後端集區。 您所建立的擴展集名為 myvmss1**、myvmss2** 和 myvmss3**。 每個擴展集都會包含兩個您安裝 IIS 的虛擬機器執行個體。

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>安裝 NGINX

```azurecli-interactive
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>在網域中建立 CNAME 記錄

在以公用 IP 位址建立應用程式閘道之後，您可以取得 DNS 位址並用以在網域中建立 CNAME 記錄。 您可以使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 來取得應用程式閘道的 DNS 位址。 複製 DNSSettings 的 *fqdn* 值，並用來作為所建立 CNAME 記錄的值。 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

不建議使用 A 記錄，因為 VIP 可能會在應用程式閘道重新開機時更改。

## <a name="test-the-application-gateway"></a>測試應用程式閘道

在瀏覽器的網址列中輸入您的網域名稱。 例如，http:\//www.contoso.com。

![在應用程式閘道中測試 contoso 網站](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

將位址變更為您的另一個網域，您應該會看到類似下列的範例：

![測試應用程式閘道中的 fabrikam 網站](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，可移除資源群組、應用程式閘道和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>後續步驟

[建立包含 URL 路徑型路由規則的應用程式閘道](./tutorial-url-route-cli.md)
