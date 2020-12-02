---
title: 快速入門：使用 Azure Front Door 設定高可用性 - Azure CLI
description: 本快速入門將說明如何使用 Azure Front Door，利用 Azure CLI 來建立高可用性和高效能的全域 web 應用程式。
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: e01b56f88270348167ef1eef9d913e65074d9247
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327231"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>快速入門：使用 Azure CLI 為高可用性的全域 Web 應用程式建立 Front Door

使用 Azure CLI 來建立高度可用且高效能的全域 web 應用程式，以開始使用 Azure Front Door。

Front Door 會將網路流量導向後端集區中的特定資源。 您已定義前端網域、將資源新增至後端集區，以及建立路由規則。 本文使用具有兩個 Web 應用程式資源的一個後端集區簡單設定，以及使用符合 "/*" 預設路徑的單一路由規則。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure CLI 或 Azure Cloud Shell
- 確定已將 front-door 延伸模組新增至您的 Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 您可以使用現有的資源群組，或建立一個新的群組。

在本快速入門中，您需要兩個資源群組。 其中一個在「美國中部」，第二個在「美國中南部」。

使用 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true) 來建立資源群組：

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>建立 Web 應用程式的兩個執行個體

本快速入門需要兩個在不同 Azure 區域中執行的 Web 應用程式執行個體。 這兩個 Web 應用程式執行個體都會以「主動/主動」模式執行，因此其中任一個都可以服務流量。

如果您還沒有 Web 應用程式，請使用下列指令碼來設定兩個 Web 應用程式範例。

### <a name="create-app-service-plans"></a>建立 App Service 方案

建立 Web 應用程式之前，您需要兩個 App Service 方案，一個在「美國中部」，而第二個在「美國中南部」。

使用 [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create&preserve-view=true) 來建立 App Service 方案：

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
--resource-group myRGFDSouthCentral
```

### <a name="create-web-apps"></a>建立 Web 應用程式

執行下列命令將會在上一個步驟的每個 App Service 方案中建立 Web 應用程式。 Web 應用程式名稱必須是全域唯一的。

使用 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create&preserve-view=true) 建立 Web 應用程式：

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

記下每個 Web 應用程式的預設主機名稱，讓您可以在下一個步驟中部署 Front Door 時定義後端位址。

## <a name="create-the-front-door"></a>建立 Front Door

執行下列動作，以建立具有預設負載平衡設定、健全狀態探查和路由規則的基本 Front Door：

使用 [az network front-door create](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext_front_door_az_network_front_door_create&preserve-view=true) 建立 Front Door：

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--resource-group：** 指定您要在其中部署 Front Door 的資源群組。

**--name：** 輸入 Azure Front Door 的全域唯一名稱。 

**--accepted-protocols：** 接受的值為 **http** 和 **https**。 如果您想要同時使用這兩個，請以空格分隔方式具體指定。

**--backend-address**：在此處定義兩個 Web 應用程式主機名稱，並以空格分隔。

成功完成部署後，請記下 frontEndpoints 區段中的主機名稱。

## <a name="test-the-front-door"></a>測試 Front Door

開啟網頁瀏覽器，並輸入從命令取得的主機名稱。 Front Door 會將您的要求導向其中一個後端資源。

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door 測試頁面":::

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為 Front Door 建立的資源時，請刪除這兩個資源群組。 在刪除資源群組時，將同時刪除 Front Door 及其所有相關資源。 

若要刪除資源群組，請使用 [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete&preserve-view=true)：

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：
* Front Door
* 兩個 Web 應用程式

若要了解如何將自訂網域新增至您的 Front Door，請繼續進行 Front Door 教學課程。

> [!div class="nextstepaction"]
> [新增自訂網域](front-door-custom-domain.md)
