---
title: CLI：使用 Azure CLI 部署 Web 應用程式的私人端點
description: 了解如何使用 Azure CLI 來為您的 Web 應用程式部署私人端點
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 7246bb3453f6e4863faf15dbcbfdf8534481ddad
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561404"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>使用 Azure CLI 來建立 App Service 應用程式和部署私人端點

此範例指令碼會在 App Service 中建立應用程式及其相關資源，然後部署私人端點。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教學課程需要 2.0.28 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

建立任何資源之前，您必須先建立資源群組來裝載 Web 應用程式、虛擬網路，以及其他網路元件。 使用 [az group create](/cli/azure/group) 來建立資源群組。 此範例會在 *francecentral* 位置建立名為 *myResourceGroup* 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>建立 App Service 方案

您需要建立 App Service 方案來裝載 Web 應用程式。
使用 [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) 來建立 App Service 方案。
此範例會在 *francecentral* 位置中建立名為 *myAppServicePlan* 的 App Service 方案，其中含有 *P1V2* Sku 且只有一個背景工作角色： 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>建立 Web 應用程式

既然您有了 App Service 方案，就可以部署 Web 應用程式。
使用 [az appservice plan create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create 建立 Web 應用程式。
此範例會在名為 *myAppServicePlan* 的方案中建立名為 *mySiteName* 的 Web 應用程式

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>建立 VNet

使用 [az network vnet create](/cli/azure/network/vnet) 建立虛擬網路。 此範例會建立一個名為 *myVNet* 的預設虛擬網路，其中含有一個名為 mySubnet 的子網路：

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>設定子網路 

您需要更新子網，才能停用私人端點網路原則。 使用 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 來更新名為 mySubnet 的子網路設定：

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>建立私人端點

使用 [az network private-endpoint create](/cli/azure/network/private-endpoint)，為您的 Web 應用程式建立私人端點。 此範例會在子網路 *mySubnet* 的 VNet *myVNet* 中建立名為 *myPrivateEndpoint* 的私人端點，而連線至 Web App /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp 之資源 ID 的連線名為 *myConnectionName*，Web 應用程式類型的群組參數為 *sites*。 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>設定私人區域

最後，您必須建立名為 *privatelink.azurewebsites.net* 且連結到 VNet 的私人 DNS 區域，才能解析 Web 應用程式的 DNS 名稱。


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>後續步驟

- 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。
- 您可以在 [Azure App Service 文件](../samples-cli.md)中找到其他的 App Service CLI 指令碼範例。