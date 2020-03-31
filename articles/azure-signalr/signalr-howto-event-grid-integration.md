---
title: 如何向事件網格發送 Azure SignalR 服務事件
description: 指南，用於演示如何為 SignalR 服務啟用事件網格事件，然後將連接/斷開連接事件的用戶端連接發送到應用程式範例。
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710825"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>如何將事件從 Azure SignalR Service 傳送至事件方格

Azure 事件網格是一個完全託管的事件路由服務，使用 pub-sub 模型提供統一的事件消耗。 在本指南中，使用 Azure CLI 創建 Azure SignalR 服務，訂閱連接事件，然後部署示例 Web 應用程式來接收事件。 最後，您可以連接和斷開連接，並在應用程式範例中查看事件裝載。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

本文中的 Azure CLI 命令已針對 **Bash** 殼層進行格式化。 如果您使用不同的殼層 (例如 PowerShell 或命令提示字元)，您可能需要據以調整行接續字元或變數指派行。 本文使用變數來將需要編輯的命令數量降至最低。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是一種邏輯容器，您可在其中部署與管理 Azure 資源。 下列 [az group create][az-group-create] 命令會在 *eastus* 區域中建立名為 *myResourceGroup* 的資源群組。 如果您想要針對資源群組使用不同名稱，請將 `RESOURCE_GROUP_NAME` 設定為不同的值。

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>建立 SignalR 服務

接下來，使用以下命令將 Azure 信號器服務部署到資源組中。
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

創建 SignalR 服務後，Azure CLI 將返回類似于以下內容的輸出：

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>建立事件端點

在本節中，您可以使用位於 GitHub 存放庫的 Resource Manager 範本，來將預先建置的範例 Web 應用程式部署到 Azure App Service。 您稍後會訂閱登錄的事件方格事件，並指定此應用程式作為要將事件傳送至其中的端點。

若要部署範例應用程式，請將 `SITE_NAME` 設為您 Web 應用程式的唯一名稱，然後執行下列命令。 網站名稱在 Azure 中必須是唯一的，因為它會構成 Web 應用程式完整網域名稱 (FQDN) 的一部分。 在後續小節中，您會在網頁瀏覽器中瀏覽至應用程式的 FQDN，以檢視您的登錄事件。

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

部署成功（可能需要幾分鐘時間），打開瀏覽器並導航到 Web 應用以確保它運行：

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>訂閱登錄事件

在事件方格中，您可以訂閱「主題」**，以告知它您想要追蹤的事件，以及要將它們傳送至何處。 以下[az 事件網格事件訂閱創建][az-eventgrid-event-subscription-create]命令訂閱您創建的 Azure SignalR 服務，並將 Web 應用的 URL 指定為它應該向其發送事件的終結點。 您在先前小節中所填入的環境變數會在此處重複使用，因此不需進行任何編輯。

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

完成訂閱時，您應該會看見類似下列內容的輸出：

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>觸發登錄事件

切換到服務模式並`Serverless Mode`設置到 SignalR 服務的用戶端連接。 您可以將[無伺服器示例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)作為參考。

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>檢視登錄事件

您現在已將用戶端連接到 SignalR 服務。 導航到事件網格檢視器 Web 應用，您應該會看到事件`ClientConnectionConnected`。 如果終止用戶端，您還將看到一個`ClientConnectionDisconnected`事件。

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
