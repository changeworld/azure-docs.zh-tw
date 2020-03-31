---
title: 快速入門：建立應用程式 HA 的設定檔 - Azure CLI - Azure 流量管理員
description: 本快速入門文章會說明如何建立流量管理員設定檔，以建置高可用性的 Web 應用程式。
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: rohink
ms.openlocfilehash: e19850243498fc24c9a726f4603590df15f3a046
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531510"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>快速入門：使用 Azure CLI 建立流量管理員設定檔以獲得高可用性的 Web 應用程式

本快速入門會說明如何建立流量管理員設定檔，以便為 Web 應用程式提供高可用性。

在本快速入門中，您會建立 Web 應用程式的兩個執行個體。 每個執行個體會在不同的 Azure 區域中執行。 您會建立以[端點優先順序](traffic-manager-routing-methods.md#priority-traffic-routing-method)為基礎的流量管理員設定檔。 此設定檔會將使用者流量導向執行 Web 應用程式的主要網站。 流量管理員會持續監視 Web 應用程式。 如果主要網站無法使用，它會提供自動容錯移轉至備份網站。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，在執行本教學課程時，您必須執行 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組
使用 [az group create](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus  位置建立名為 myResourceGroup  的資源群組：

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

使用 [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) 建立流量管理員設定檔，以根據端點優先順序引導使用者流量。

在下列範例中，以唯一的流量管理員設定檔名稱取代 **<profile_name>** 。

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>建立 Web 應用程式

在本快速入門中，您必須在不同的 Azure 區域 (美國東部  和西歐  ) 中部署 Web 應用程式的兩個執行個體。 每個執行個體都會作為流量管理員的主要和容錯移轉端點。

### <a name="create-web-app-service-plans"></a>建立 Web App Service 方案
針對您將在兩個不同 Azure 區域中部署的兩個 Web 應用程式執行個體，使用 [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) 建立 Web App Service 方案。

在下列範例中，以唯一的 App Service 方案名稱取代 **<appspname_eastus>** 和 **<appspname_westeurope>**

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>在 App Service 方案中建立 Web 應用程式
使用 [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)，在「美國東部」  和「西歐」  Azure 區域的 App Service 方案中建立 Web 應用程式的兩個執行個體。

在下列範例中，以唯一的應用程式名稱取代 **<app1name_eastus>** 和 **<app2name_westeurope>** ，並以上一節中用來建立 App Service 方案的名稱取代 **<appspname_eastus>** 和 **<appspname_westeurope>** 。

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>新增流量管理員端點
使用 [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create)，將兩個 Web 應用程式當作流量管理員端點新增到流量管理員設定檔，如下所示：

- 決定 Web 應用程式識別碼，並將位於「美國東部」  Azure 區域的 Web 應用程式新增為主要端點，以便路由傳送所有的使用者流量。 
- 決定 Web 應用程式識別碼，並將位於「西歐」  Azure 區域的 Web 應用程式新增為容錯移轉端點。 

當主要端點無法使用時，流量就會自動路由傳送到容錯移轉端點。

在下列範例中，以上一節中為每個區域建立的應用程式名稱取代 **<app1name_eastus>** 和 **<app2name_westeurope>** ，以上一節中用來建立 App Service 方案的名稱取代 **<appspname_eastus>** 和 **<appspname_westeurope>** ，並以上一節中使用的設定檔名稱取代 **<profile_name>** 。 

**美國東部端點**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

記下輸出中顯示的識別碼，並在下列命令中使用來新增端點：

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**西歐端點**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

記下輸出中顯示的識別碼，並在下列命令中使用來新增端點：

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>測試流量管理員設定檔

在本節中，您會檢查流量管理員設定檔的網域名稱。 您也會將主要端點設定為無法使用。 最後，您可以看到 Web 應用程式仍可使用。 這是因為流量管理員將流量傳送至容錯移轉端點。

在下列範例中，以上一節中為每個區域建立的應用程式名稱取代 **<app1name_eastus>** 和 **<app2name_westeurope>** ，以上一節中用來建立 App Service 方案的名稱取代 **<appspname_eastus>** 和 **<appspname_westeurope>** ，並以上一節中使用的設定檔名稱取代 **<profile_name>** 。

### <a name="determine-the-dns-name"></a>確定 DNS 名稱

使用 [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show)，判斷流量管理員設定檔的 DNS 名稱。

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

複製 **RelativeDnsName** 值。 流量管理員設定檔的 DNS 名稱為 *http://<* relativednsname *>.trafficmanager.net*。 

### <a name="view-traffic-manager-in-action"></a>檢視流量管理員的運作
1. 在網頁瀏覽器中，輸入流量管理員設定檔的 DNS 名稱 (*http://<* relativednsname *>.trafficmanager.net*)，以檢視 Web 應用程式的預設網站。

    > [!NOTE]
    > 在此快速入門案例中，所有要求都會路由傳送至主要端點。 它會設定為 [優先順序 1]  。
2. 若要檢視進行中的流量管理員容錯移轉，請使用 [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) 來停用主要網站。

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. 複製流量管理員設定檔的 DNS 名稱 (*http://<* relativednsname *>.trafficmanager.net*)，以在新的網頁瀏覽器工作階段中檢視網站。
4. 確認 Web 應用程式仍可使用。

## <a name="clean-up-resources"></a>清除資源

完成時，請使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 刪除資源群組、Web 應用程式和所有相關資源。

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立流量管理員設定檔，以便為 Web 應用程式提供高可用性。 若要深入了解如何路由傳送流量，請繼續進行流量管理員的教學課程。

> [!div class="nextstepaction"]
> [流量管理員教學課程](tutorial-traffic-manager-improve-website-response.md)
