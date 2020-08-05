---
title: 快速入門：將自訂事件傳送至 Web 端點 - 事件方格、Azure 入口網站
description: 快速入門：使用 Azure 事件方格和 Azure 入口網站發佈自訂主題，以及訂閱該主題的事件。 事件是由 Web 應用程式處理。
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 592e2d6b7393da8cb55a457b022d6c2358048cfe
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421037"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>快速入門：使用 Azure 入口網站和事件方格，將自訂事件路由傳送至 Web 端點

Azure Event Grid 是一項雲端事件服務。 在本文中，您可使用 Azure 入口網站建立自訂主題、訂閱自訂主題，以及觸發事件來檢視結果。 通常，您會將事件傳送至可處理事件資料及採取行動的端點。 不過，若要簡化這篇文章，您可將事件傳送至可收集及顯示訊息的 Web 應用程式。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>建立自訂主題

Event Grid 主題會提供使用者定義的端點，作為您發佈事件的目的地。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在主題的搜尋列中，輸入**事件方格主題**，然後從下拉式清單中選取 [事件方格主題]。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="搜尋並選取事件方格主題":::
3. 在 [事件方格主題] 頁面上，選取工具列上的 [+ 新增]。 

    :::image type="content" source="./media/custom-event-quickstart-portal/add-event-grid-topic-button.png" alt-text="新增事件方格主題按鈕":::
4. 在 [建立主題] 頁面上，遵循下列步驟：
    1. 選取您的 Azure **訂用帳戶**。
    2. 選取現有的資源群組或選取 [新建]，然後針對 [資源群組] 輸入 [名稱]。
    3. 提供自訂主題的唯一 [名稱]。 主題名稱必須是唯一的，因為它由 DNS 項目表示。 請勿使用影像中顯示的名稱。 而是建立您自己的名稱 - 它必須介於 3 到 50 個字元之間，且只包含值 a-z、A-Z、0-9 和 "-"。
    4. 選取事件方格主題的 [位置]。
    5. 選取頁面底部的 [檢閱 + 建立]。 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="建立主題頁面":::
    6. 在 [建立主題] 頁面的 [檢閱 + 建立] 索引標籤上，選取 [建立]。 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="檢閱設定並建立":::
5. 部署成功之後，請在搜尋列中再次輸入**事件方格主題**，然後如同之前一樣，從下拉式清單中選取 [事件方格主題]。 
6. 從清單中選取您建立的主題。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topic.png" alt-text="從清單中選取您的主題":::

7. 您會看到您主題的 [事件方格主題] 頁面。 讓此頁面保持開啟。 您稍後會在快速入門中使用此值。 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="事件方格主題首頁":::

## <a name="create-a-message-endpoint"></a>建立訊息端點
建立自訂主題的訂閱之前，請建立事件訊息的端點。 通常，端點會根據事件資料採取動作。 若要簡化此快速入門，請部署[預先建置的 Web 應用程式](https://github.com/Azure-Samples/azure-event-grid-viewer)以顯示事件訊息。 已部署的解決方案包含 App Service 方案、App Service Web 應用程式，以及 GitHub 中的原始程式碼。

1. 在文章頁面中，選取 [部署至 Azure]，將解決方案部署至您的訂用帳戶。 在 Azure 入口網站中，提供參數的值。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. 部署需要幾分鐘的時間才能完成。 成功部署之後，檢視 Web 應用程式，確定它正在執行。 在網頁瀏覽器中，瀏覽至：`https://<your-site-name>.azurewebsites.net`

    如果部署失敗，請檢查錯誤訊息。 這可能是因為已經採用網站名稱。 再次部署範本，並為網站選擇不同的名稱。 
1. 您看到網站，但其中尚未發佈任何事件。

   ![檢視新網站](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>訂閱自訂主題

您可訂閱事件方格主題，告知事件方格您想要追蹤的事件，以及要將事件傳送至何處。

1. 現在，在您自訂主題的 [事件方格主題] 頁面上，選取工具列上的 [+ 事件訂閱]。

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="新增事件訂用帳戶按鈕":::
2. 在 [建立事件訂閱] 頁面上，遵循下列步驟：
    1. 輸入事件訂閱的 [名稱]。
    3. 選取 [Web Hook] 作為 [端點類型]。 
    4. 選擇 [選取端點]。 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="提供事件訂用帳戶值":::
    5. 針對 Web Hook 端點，提供您的 Web 應用程式 URL，並將 `api/updates` 新增至首頁 URL。 選取 [確認選取項目]。

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="提供端點 URL":::
    6. 回到 [建立事件訂閱] 頁面，選取 [建立]。

3. 再次檢視 Web 應用程式，並注意訂用帳戶的驗證事件已傳送給它。 選取眼睛圖示來展開事件資料。 Event Grid 會傳送驗證事件，以便端點確認它要接收事件資料。 Web 應用程式包含用來驗證訂用帳戶的程式碼。

    ![訂用訂用帳戶事件](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>將事件傳送至主題

現在，讓我們觸發事件以了解 Event Grid 如何將訊息散發至您的端點。 使用 Azure CLI 或 PowerShell 將測試事件傳送至您的自訂主題。 一般而言，應用程式或 Azure 服務就會傳送事件資料。

第一個範例會使用 Azure CLI。 它會取得自訂主題的 URL 和金鑰，以及範例事件資料。 將您的自訂主題名稱用於 `<topic name>`。 這樣會建立範例事件資料。 JSON 的 `data` 元素是您的事件承載。 任何語式正確的 JSON 都可以進入這個欄位。 您也可以使用主體欄位進行進階路由傳送或篩選。 CURL 是可傳送 HTTP 要求的公用程式。


### <a name="azure-cli"></a>Azure CLI
1. 在 Azure 入口網站中選取 [Cloud Shell]。 Cloud Shell 會在網頁瀏覽器的底部窗格中開啟。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="選取 Cloud Shell 圖示":::
1. 在 Cloud Shell 視窗的左上角，選取 [Bash]。 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. 執行下列命令以取得主題的**端點**：在您複製並貼上命令之後，請先更新 [主題名稱] 和 [資源群組名稱]，再執行命令。 您會將範例事件發佈到這個主題端點。 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. 執行下列命令以取得主題的**索引鍵**：在您複製並貼上命令之後，請先更新 [主題名稱] 和 [資源群組名稱]，再執行命令。 這是事件方格主題的主索引鍵。 若要從 Azure 入口網站取得此金鑰，請切換至 [事件方格主題] 頁面的 [存取金鑰] 索引標籤。 若要能夠將事件張貼到自訂主題，您需要存取金鑰。 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. 複製下列陳述式與事件定義，然後按 **ENTER**。 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. 執行下列 **Curl** 命令來張貼事件：在命令中，`aeg-sas-key` 標頭會設定為您稍早取得的存取金鑰。 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
第二個範例會使用 PowerShell 執行類似的步驟。

1. 在 Azure 入口網站中選取 [Cloud Shell] (或者，移至 `https://shell.azure.com/`)。 Cloud Shell 會在網頁瀏覽器的底部窗格中開啟。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="選取 Cloud Shell 圖示":::
1. 在 **Cloud Shell** 中，選取 Cloud Shell 視窗左上角的 [PowerShell]。 請參閱 Azure CLI 區段中的範例 **Cloud Shell** 視窗映像。
2. 設定下列變數。 在您複製並貼上每個命令之後，請先更新 [主題名稱] 和 [資源群組名稱]，再執行命令：

    **資源群組**：
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **事件方格主題名稱**：    
    ```powershell
    $topicName = "<topic name>"
    ```
3. 執行下列命令以取得主題的**端點**和**金鑰**：

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. 準備事件。 在 Cloud Shell 視窗中複製並執行陳述式。 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. 使用 **Invoke-WebRequest** Cmdlet 來傳送事件。 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>在事件方格檢視器中驗證
您已觸發此事件，而 Event Grid 會將訊息傳送至您在訂閱時設定的端點。 檢視您的 Web 應用程式以查看剛傳送的事件。

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="事件方格檢視器":::

## <a name="clean-up-resources"></a>清除資源
如果您打算繼續使用此事件，請勿清除在本文中建立的資源。 否則，請刪除您在本文建立的資源。

1. 選取左功能表上的 [資源群組]。 如果您未在左側功能表上看到它，請選取左側功能表上的 [所有服務]，然後選取 [資源群組]。 

    ![資源群組](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. 選取資源群組以啟動 [資源群組] 頁面。 
1. 選取工具列上的 [刪除資源群組]。 
1. 輸入資源群組的名稱並選取 [刪除]，以確認刪除。 

    您在映像中看到的其他資源群組是由 Cloud Shell 視窗建立和使用。 如果您不打算稍後使用 Cloud Shell 視窗，請將它刪除。 

## <a name="next-steps"></a>後續步驟

您現在知道如何建立自訂主題和事件訂閱，深入了解 Event Grid 可協助您：

- [關於 Event Grid](overview.md)
- [將 Blob 儲存體事件路由至自訂的 Web 端點](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [使用 Azure Event Grid 和 Logic Apps 監視虛擬機器變更](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [將巨量資料串流處理至資料倉儲](event-grid-event-hubs-integration.md)
