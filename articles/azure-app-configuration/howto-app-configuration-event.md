---
title: 使用 Azure 應用程式組態將事件傳送至 web 端點
description: 瞭解如何使用 Azure 應用程式組態事件訂閱，將索引鍵/值修改事件傳送至 web 端點
services: azure-app-configuration
author: lisaguthrie
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: da64f22981cc33772783093cfe75daa3eac5cef1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78672148"
---
# <a name="route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>使用 Azure CLI 將 Azure 應用程式組態事件路由傳送至 Web 端點

在本文中，您將瞭解如何設定 Azure 應用程式組態事件訂用帳戶，將索引鍵/值修改事件傳送至 web 端點。 Azure 應用程式組態使用者可以訂閱每次修改索引鍵/值時所發出的事件。 這些事件可以觸發 web 勾點、Azure Functions、Azure 儲存體的佇列，或 Azure 事件方格所支援的任何其他事件處理常式。 通常，您會將事件傳送至可處理事件資料及採取行動的端點。 不過，若要簡化這篇文章，您可將事件傳送至可收集及顯示訊息的 Web 應用程式。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶-[免費建立一個](https://azure.microsoft.com/free/)。 您可以選擇性地使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行最新版的 Azure CLI （2.0.70 或更新版本）。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

如果您未使用 Cloud Shell，必須先使用 `az login` 登入。

## <a name="create-a-resource-group"></a>建立資源群組

Event Grid 為 Azure 資源，必須放入 Azure 資源群組中。 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 

下列範例會在 westus *`<resource_group_name>` 位置建立名為 * 的資源群組。  以資源群組的唯一名稱取代 `<resource_group_name>`。

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

請以您的設定 `<appconfig_name>` 存放區的唯一名稱取代，並 `<resource_group_name>` 以您稍早建立的資源群組取代。 此名稱必須是唯一的，因為它會當作 DNS 項目使用。

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>建立訊息端點

訂閱主題之前，讓我們建立事件訊息的端點。 通常，端點會根據事件資料採取動作。 若要簡化此快速入門，請部署[預先建置的 Web 應用程式](https://github.com/Azure-Samples/azure-event-grid-viewer)以顯示事件訊息。 已部署的解決方案包含 App Service 方案、App Service Web 應用程式，以及 GitHub 中的原始程式碼。

以 Web 應用程式的唯一名稱取代 `<your-site-name>`。 Web 應用程式名稱必須是唯一的，因為它是 DNS 項目的一部分。

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

部署需要幾分鐘的時間才能完成。 成功部署之後，檢視 Web 應用程式，確定它正在執行。 在網頁瀏覽器中，瀏覽至：`https://<your-site-name>.azurewebsites.net`

您應會看到網站目前未顯示任何訊息。

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>訂閱您的應用程式組態存放區

您可訂閱主題，告知 Event Grid 您想要追蹤的事件，以及要將事件傳送至何處。 下列範例會訂閱您所建立的應用程式組態，從 Web 應用程式傳遞 URL 作為事件通知的端點。 以事件訂用帳戶的名稱來取代 `<event_subscription_name>`。 對於 `<resource_group_name>` 和 `<appconfig_name>`，使用您稍早建立的值。

Web 應用程式的端點必須包含的尾碼 `/api/updates/`。

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

再次檢視 Web 應用程式，並注意訂用帳戶的驗證事件已傳送給它。 選取眼睛圖示來展開事件資料。 Event Grid 會傳送驗證事件，以便端點確認它要接收事件資料。 Web 應用程式包含用來驗證訂用帳戶的程式碼。

![訂用訂用帳戶事件](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>觸發應用程式組態事件

現在，讓我們觸發事件以了解 Event Grid 如何將訊息散發至您的端點。 使用稍早的 `<appconfig_name>` 建立索引鍵/值。

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

您已觸發此事件，而 Event Grid 會將訊息傳送至您在訂閱時設定的端點。 檢視您的 Web 應用程式以查看剛傳送的事件。

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="clean-up-resources"></a>清除資源
如果您打算繼續使用此應用程式組態和事件訂用帳戶，請勿清除在本文中建立的資源。 如果您不打算繼續，請使用下列命令來刪除您在本文建立的資源。

以您在上面建立的資源群組取代 `<resource_group_name>`。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>後續步驟

您現在知道如何建立主題和事件訂用帳戶，請深入了解索引鍵/值事件以及 Event Grid 如何協助您：

- [回應索引鍵/值事件](concept-app-configuration-event.md)
- [關於 Event Grid](../event-grid/overview.md)
- [Azure 事件方格處理常式](../event-grid/event-handlers.md)
