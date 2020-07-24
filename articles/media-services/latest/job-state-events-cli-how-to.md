---
title: 在 CLI 中使用事件方格監視 Azure 媒體服務事件 | Microsoft Docs
description: 本文說明如何訂閱事件方格以監視 Azure 媒體服務事件。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 276cb3e6078fec915f266c5eb9aa557f92973dd7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001157"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>在 Azure CLI 中使用事件方格建立和監視媒體服務事件

Azure Event Grid 是一項雲端事件服務。 此服務會使用[事件訂閱](../../event-grid/concepts.md#event-subscriptions)將事件訊息路由傳送至訂閱者。 媒體事件包含了回應資料變更時所需的一切資訊。 因為 eventType 屬性開頭為 “Microsoft.Media”，所以您可以藉此識別出媒體服務事件。 如需詳細資訊，請參閱[媒體服務事件結構描述](media-services-event-schemas.md)。

在本文中，您會使用 Azure CLI 訂閱 Azure 媒體服務帳戶的事件。 然後，您會觸發事件以檢視結果。 通常，您會將事件傳送至可處理事件資料及採取行動的端點。 在本文中，您會將事件傳送至可收集及顯示訊息的 Web 應用程式。

## <a name="prerequisites"></a>必要條件

- 有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 在本機安裝和使用 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，並非所有[媒體服務 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中運作。 建議在本機使用 CLI。

- [建立媒體服務帳戶](./create-account-howto.md)。

    請務必記住您用於資源群組名稱和「媒體服務」帳戶名稱的值。

## <a name="create-a-message-endpoint"></a>建立訊息端點

在訂閱媒體服務帳戶的事件之前，我們要先建立事件訊息的端點。 通常，端點會根據事件資料採取動作。 在本文中，您會部署[預先建置的 Web 應用程式](https://github.com/Azure-Samples/azure-event-grid-viewer)以顯示事件訊息。 已部署的解決方案包含 App Service 方案、App Service Web 應用程式，以及 GitHub 中的原始程式碼。

1. 選取 [部署至 Azure]，將解決方案部署至您的訂用帳戶。 在 Azure 入口網站中，提供參數的值。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. 部署需要幾分鐘的時間才能完成。 成功部署之後，檢視 Web 應用程式，確定它正在執行。 在網頁瀏覽器中，瀏覽至：`https://<your-site-name>.azurewebsites.net`

如果您切換到「Azure 事件方格檢視器」網站，您會看到其中還沒有任何事件。
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>設定 Azure 訂用帳戶

在下列命令中，提供您要用於媒體服務帳戶的 Azure 訂用帳戶識別碼。 您可以瀏覽至[訂閱](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，以查看可以存取的訂用帳戶。

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>訂閱媒體服務事件

您訂閱文章，告訴事件方格您想要追蹤的事件。下列範例會訂閱您所建立的媒體服務帳戶，並從您建立的網站傳遞 URL 作為事件通知的端點。 

將 `<event_subscription_name>` 換成事件訂用帳戶的唯一名稱。 針對 `<resource_group_name>` 和 `<ams_account_name>`，請使用您建立「媒體服務」帳戶時所使用的值。 針對 `<endpoint_URL>`，提供您的 Web 應用程式 URL，並將 `api/updates` 新增至首頁 URL。 藉由在訂閱時指定端點，事件方格即可將事件路由至該端點。 

1. 取得資源識別碼

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    例如：

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. 訂閱事件

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    例如：

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > 您可能會收到驗證交握警告。 請稍候幾分鐘，交握就應該會完成驗證。

現在，我們將觸發事件以查看事件方格如何將訊息散發至您的端點。

## <a name="send-an-event-to-your-endpoint"></a>將事件傳送至端點

您可以藉由執行編碼作業來觸發媒體服務帳戶的事件。 您可以依照[本快速入門](stream-files-dotnet-quickstart.md)的說明為檔案編碼，並開始傳送事件。 

再次檢視 Web 應用程式，並注意訂用帳戶的驗證事件已傳送給它。 Event Grid 會傳送驗證事件，以便端點確認它要接收事件資料。 端點必須將 `validationResponse` 設定為 `validationCode`。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](../../event-grid/security-authentication.md)。 您可以檢視 Web 應用程式的程式碼，以查看其驗證訂用帳戶的方式。

> [!TIP]
> 選取眼睛圖示來展開事件資料。 如果您要檢視所有事件，請不要重新整理頁面。

![訂用訂用帳戶事件](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>接下來的步驟

[上傳、編碼和串流](stream-files-tutorial-with-api.md)
