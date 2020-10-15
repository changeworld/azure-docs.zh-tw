---
title: 教學課程 - 使用 IoT 中樞事件來觸發 Azure Logic Apps
description: 本教學課程說明如何使用 Azure 事件方格的事件路由服務，建立自動化程序以根據 IoT 中樞事件執行 Azure Logic Apps 動作。
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5092aa0b5b23f04af1f49933bca234815f03f454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604527"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>教學課程：使用 Logic Apps 和 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知

Azure Event Grid 可讓您在下游商務應用程式中觸發動作，進而回應 IoT 中樞中的事件。

本文逐步解說使用 IoT 中樞與事件方格的範例組態。 結束時，您會將 Azure 邏輯應用程式設定為在每次裝置與 IoT 中樞連線或中斷連線時傳送通知電子郵件。 事件方格可用來取得重要裝置中斷連線的及時通知。 計量和診斷需要數分鐘 (也就是 20 分鐘以上 -- 雖然我們不想要提及數字) 才會在記錄/警示中顯示。 這對於重要的基礎結構可能無法接受。

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶。 如果您沒有訂用帳戶，則可以建立[免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* Azure Logic Apps 支援的任何電子郵件提供者 (如 Office 365 Outlook 或 Outlook.com) 所提供的電子郵件帳戶。 這個電子郵件帳戶用來傳送事件通知。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

您可以使用入口網站中的 Azure Cloud Shell 終端，快速建立新的 IoT 中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選取頁面右上方的 [Cloud Shell] 按鈕。

   ![[Cloud Shell] 按鈕](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. 執行下列命令以建立新的資源群組：

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. 執行以下命令來建立 IoT 中樞：

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. 將 Cloud Shell 終端最小化。 您在本教學課程稍後會回到殼層。

## <a name="create-a-logic-app"></a>建立邏輯應用程式

接下來，建立邏輯應用程式，並新增 HTTP 事件方格觸發程序來處理來自 IoT 中樞的要求。 

### <a name="create-a-logic-app-resource"></a>建立邏輯應用程式資源

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]，然後在搜尋方塊中鍵入「邏輯應用程式」並選取 Return。 從結果中選取 [邏輯應用程式]。

   ![建立邏輯應用程式](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. 在下一個畫面上，選取 [建立]。 

1. 在訂用帳戶中為邏輯應用程式指定唯一的名稱，然後選取與您的 IoT 中樞相同的訂用帳戶、資源群組和位置。 

   ![可供建立邏輯應用程式的欄位](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. 選取 [檢閱 + 建立]。

1. 確認您的設定，然後選取 [建立]。

1. 建立資源之後，選取 [前往資源]。 

1. 在 Logic Apps 設計工具中，向下捲動一頁以查看 [範本]。 選擇 [空白邏輯應用程式]，以便從頭建置邏輯應用程式。

### <a name="select-a-trigger"></a>選取觸發程序

觸發程序是啟動邏輯應用程式的特定事件。 在本教學課程中，啟動工作流程的觸發程序會透過 HTTP 接收要求。  

1. 在連接器和觸發程序搜尋列中，輸入 **HTTP**。

1. 瀏覽結果並且選取 [要求 - 收到 HTTP 要求時] 作為觸發程序。 

   ![選取 HTTP 要求觸發程序](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. 選取 [使用範例承載來產生結構描述]。 

   ![使用範例承載](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. 將「裝置連線事件結構描述」JSON 貼入文字方塊中，然後選取 [完成]：

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   此事件會在裝置連線至 IoT 中樞時發佈。

> [!NOTE]
> 您可能會收到快顯通知，指出**請務必在您的要求中加入 Content-Type 標頭，並將其設為 application/json。** 您可以放心地忽略這項建議，並移至下一節。 

### <a name="create-an-action"></a>建立動作

動作是在觸發程序啟動邏輯應用程式工作流程之後發生的任何步驟。 在本教學課程中，從電子郵件提供者傳送電子郵件通知就是一個動作。 

1. 選取 [新增步驟]。 這會開啟 [選擇動作] 的視窗。

1. 搜尋 **Outlook**。

1. 根據您的電子郵件提供者，尋找並選取相符的連接器。 本教學課程使用 **Outlook.com**。 其他電子郵件提供者的步驟很類似。 

   ![選取電子郵件提供者連接器](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. 選取 [傳送電子郵件 \(V2\)]**** 動作。 

1. 選取 [登入] 並登入您的電子郵件帳戶。 選取 [是] 讓應用程式存取您的資訊。

1. 建置電子郵件範本。 

   * **收件者**：輸入要接收通知電子郵件的電子郵件地址。 在此教學課程中，請使用您可以存取的電子郵件帳戶來進行測試。 

   * **主體**：填入主旨的文字。 當您按一下 [主旨] 文字方塊時，可以選取要包含的動態內容。 例如，本教學課程會使用 `IoT Hub alert: {eventType}`。 如果您看不到動態內容，請選取 [新增動態內容] 超連結 -- 這會將其切換為開啟或關閉。

   * **主體**：撰寫電子郵件內容。 從選取器工具選取 JSON 屬性，以根據事件資料納入動態內容。 如果您看不到動態內容，請選取 [主體] 文字方塊下方的 [新增動態內容] 超連結。 如果它未顯示您想要的欄位，請按一下 [動態內容] 畫面中的 [其他]，以包含上一個動作中的欄位。

   您的電子郵件範本可能類似此範例：

   ![填妥電子郵件資訊](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. 在 Logic Apps 設計工具中選取 [儲存]。  

### <a name="copy-the-http-url"></a>複製 HTTP URL

離開 Logic Apps 設計工具之前，為觸發程序複製邏輯應用程式正在接聽的 URL。 您可以使用此 URL 來設定 Event Grid。 

1. 按一下以展開 [收到 HTTP 要求時] 觸發程序設定方塊。 

1. 選取 [HTTP POST URL] 旁的複製按鈕來複製其值。 

   ![複製 HTTP POST URL](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. 儲存這個 URL，以便在下一節參考它。 

## <a name="configure-subscription-for-iot-hub-events"></a>設定 IoT 中樞事件的訂用帳戶

在本節中，您可將 IoT 中樞設定為在事件發生時進行發佈。 

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。 若要這麼做，請選取 [資源群組]，接著選取本教學課程的資源群組，然後從資源清單中選取您的 IoT 中樞。

1. 選取 [事件]。

   ![開啟 Event Grid 詳細資料](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. 選取 [事件訂用帳戶]。 

   ![建立新的事件訂用帳戶](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. 使用下列值來建立事件訂用帳戶： 

   1. 在 [事件訂用帳戶詳細資料] 區段中：
      1. 提供事件訂閱的**名稱**。 
      2. 針對 [事件結構描述]，選取 [事件方格結構描述]。 
   2. 在 [主題詳細資料] 區段中：
      1. 確認 [主題類型] 已設為 [IoT 中樞]。 
      2. 確認 IoT 中樞的名稱已設為 [來源資源] 欄位的值。 
      3. 為要替您建立的**系統主題**輸入名稱。 若要了解系統主題，請參閱[系統主題概觀](system-topics.md)。
   3. 在 [事件類型] 區段中：
      1. 選取 [篩選至事件類型] 下拉式清單。
      1. 取消選取 [建立的裝置] 和 [刪除的裝置] 核取方塊，只保留選取 [連線的裝置] 和 [中斷連線的裝置] 核取方塊。

         ![選取訂用帳戶事件種類](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. 在 [端點詳細資料] 區段中： 
       1. 針對 [端點類型]選取 [Web Hook]。
       2. 按一下 [選取端點]，貼上您從邏輯應用程式複製的 URL，然後確認選取。

         ![選取端點 URL](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         當您完成時，窗格看起來應如下列範例所示： 

         ![事件訂用帳戶表單範例](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  選取 [建立]。

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>模擬連線和傳送遙測的新裝置

藉由使用 Azure CLI 快速模擬裝置連線，來測試邏輯應用程式。 

1. 選取 [Cloud Shell] 按鈕以重新開啟您的終端。

1. 執行下列命令以建立模擬的裝置身分識別：
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. 執行下列命令以模擬將您的裝置連線到 IoT 中樞並且傳送遙測：

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. 當模擬的裝置連線到 IoT 中樞時，您會收到一封電子郵件，通知您有「DeviceConnected」事件。

1. 當模擬完成，您會收到一封電子郵件，通知您有「DeviceDisconnected」事件。 

    ![範例警示郵件](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>清除資源

本教學課程使用了會使您的 Azure 訂用帳戶產生費用的資源。 完成教學課程試用和結果測試時，請停用或刪除不想保留的資源。 

若要刪除您在本教學課程中建立的所有資源，請刪除資源群組。 

1. 選取 [資源群組]，然後選取您為此教學課程建立的資源群組。

2. 在 [資源群組] 窗格中，選取 [刪除資源群組]。 系統會提示您輸入資源群組名稱，而後您可將其刪除。 其中包含的所有資源也會一併移除。

## <a name="next-steps"></a>後續步驟

* 深入了解[使用 Event Grid 來觸發動作以回應 IoT 中樞事件](../iot-hub/iot-hub-event-grid.md)。
* [了解如何排序裝置連線和中斷連線事件](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* 了解您可使用 [Event Grid](overview.md) 執行哪些作業。

如需支援的完整邏輯應用程式連接器清單，請參閱[連接器概觀](/connectors/)。
