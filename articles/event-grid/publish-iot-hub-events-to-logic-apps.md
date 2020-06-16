---
title: 教學課程 - 使用 IoT 中樞事件來觸發 Azure Logic Apps
description: 本教學課程說明如何使用 Azure 事件方格的事件路由服務，建立自動化程序以根據 IoT 中樞事件執行 Azure Logic Apps 動作。
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: robinsh
ms.openlocfilehash: 2a0b25fc73bf0f549a199592d558c0097c2db8b8
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457060"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>教學課程：使用 Logic Apps 和 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知

Azure Event Grid 可讓您在下游商務應用程式中觸發動作，進而回應 IoT 中樞中的事件。

本文逐步解說使用 IoT 中樞與事件方格的範例組態。 結束時，您會將 Azure 邏輯應用程式設定為在每次裝置新增至 IoT 中樞時傳送通知電子郵件。 

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶。 如果您沒有訂用帳戶，則可以建立[免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* Azure Logic Apps 支援的任何電子郵件提供者 (如 Office 365 Outlook、Outlook.com 或 Gmail) 所提供的電子郵件帳戶。 這個電子郵件帳戶用來傳送事件通知。 如需支援的完整邏輯應用程式連接器清單，請參閱[連接器概觀](https://docs.microsoft.com/connectors/)。

  > [!IMPORTANT]
  > 使用 Gmail 之前，請先檢查您是否擁有 G-Suite 商務帳戶 (搭配自訂網域的電子郵件地址) 或 Gmail 取用者帳戶 (搭配 @gmail.com 或 @googlemail.com 的電子郵件地址)。 只有 G-Suite 商務帳戶可以在邏輯應用程式中搭配其他連接器使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用 Gmail 連接器搭配特定的 Google 核准服務，或者您可以[建立 Google 用戶端應用程式，以用來進行驗證](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)。 如需詳細資訊，請參閱 [Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

* Azure 中的 IoT 中樞。 若尚未建立，請參閱[開始使用 IoT 中樞](../iot-hub/iot-hub-csharp-csharp-getstarted.md)的逐步解說。

## <a name="create-a-logic-app"></a>建立邏輯應用程式

首先，建立邏輯應用程式，並新增可監視您虛擬機器資源群組的事件方格觸發程序。 

### <a name="create-a-logic-app-resource"></a>建立邏輯應用程式資源

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]，然後在搜尋方塊中鍵入「邏輯應用程式」並選取 Return。 從結果中選取 [邏輯應用程式]。

   ![建立邏輯應用程式](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. 在下一個畫面上，選取 [建立]。 

1. 在訂用帳戶中為邏輯應用程式指定唯一的名稱，然後選取與您的 IoT 中樞相同的訂用帳戶、資源群組和位置。 

   ![可供建立邏輯應用程式的欄位](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. 選取 [建立]。

1. 建立資源後，請瀏覽到您的邏輯應用程式。 若要這麼做，請選取 [資源群組]，然後選取您為此教學課程建立的資源群組。 然後，在資源清單中尋找邏輯應用程式並加以選取。 

1. 在 Logic Apps 設計工具中，向下捲動一頁以查看 [範本]。 選擇 [空白邏輯應用程式]，以便從頭建置邏輯應用程式。

### <a name="select-a-trigger"></a>選取觸發程序

觸發程序是啟動邏輯應用程式的特定事件。 在本教學課程中，啟動工作流程的觸發程序會透過 HTTP 接收要求。  

1. 在連接器和觸發程序搜尋列中，輸入 **HTTP**。

1. 選取 [要求 - 收到 HTTP 要求時] 作為觸發程序。 

   ![選取 HTTP 要求觸發程序](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. 選取 [使用範例承載來產生結構描述]。 

   ![選取 HTTP 要求觸發程序](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. 將下列 JSON 程式碼範例貼到文字方塊中，然後選取 [完成]：

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. 您可能會收到快顯通知，指出**請務必在您的要求中加入 Content-Type 標頭，並將其設為 application/json。** 您可以放心地忽略這項建議，並移至下一節。 

### <a name="create-an-action"></a>建立動作

動作是在觸發程序啟動邏輯應用程式工作流程之後發生的任何步驟。 在本教學課程中，從電子郵件提供者傳送電子郵件通知就是一個動作。 

1. 選取 [新增步驟]。 這會開啟 [選擇動作] 的視窗。

1. 搜尋**電子郵件**。

1. 根據您的電子郵件提供者，尋找並選取相符的連接器。 本教學課程使用 **Office 365 Outlook**。 其他電子郵件提供者的步驟很類似。 

   ![選取電子郵件提供者連接器](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. 選取 [傳送電子郵件] 動作。 

1. 如果出現提示，請登入您的電子郵件帳戶。 

1. 建立您的電子郵件範本。 

   * **收件者**：輸入要收到通知電子郵件的電子郵件地址。 在本教學課程中，使用您可以存取的電子郵件帳戶進行測試。 

   * **主體**：填入主旨的文字。 當您按一下 [主旨] 文字方塊時，可以選取要包含的動態內容。 例如，本教學課程會使用 `IoT Hub alert: {event Type}`。 如果您看不到動態內容，請選取 [新增動態內容] 超連結 -- 這會將其切換為開啟或關閉。

   * **主體**：撰寫電子郵件內容。 從選取器工具選取 JSON 屬性，以根據事件資料納入動態內容。 如果您看不到動態內容，請選取 [主體] 文字方塊下方的 [新增動態內容] 超連結。 如果它未顯示您想要的欄位，請按一下 [動態內容] 畫面中的 [其他]，以包含上一個動作中的欄位。

   您的電子郵件範本可能類似此範例：

   ![填妥電子郵件資訊](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. 儲存您的邏輯應用程式。 

### <a name="copy-the-http-url"></a>複製 HTTP URL

離開 Logic Apps 設計工具之前，為觸發程序複製邏輯應用程式正在接聽的 URL。 您可以使用此 URL 來設定 Event Grid。 

1. 按一下以展開 [收到 HTTP 要求時] 觸發程序設定方塊。 

1. 選取 [HTTP POST URL] 旁的複製按鈕來複製其值。 

   ![複製 HTTP POST URL](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. 儲存這個 URL，以便在下一節參考它。 

## <a name="configure-subscription-for-iot-hub-events"></a>設定 IoT 中樞事件的訂用帳戶

在本節中，您可將 IoT 中樞設定為在事件發生時進行發佈。 

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。 若要這麼做，請選取 [資源群組]，接著選取本教學課程的資源群組，然後從資源清單中選取您的 IoT 中樞。

2. 選取 [事件]。

   ![開啟 Event Grid 詳細資料](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. 選取 [事件訂用帳戶]。 

   ![建立新的事件訂用帳戶](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. 使用下列值來建立事件訂用帳戶： 

    1. 在 [事件訂閱詳細資料] 區段中，執行下列工作：
        1. 提供事件訂閱的**名稱**。 
        2. 針對 [事件結構描述]，選取 [事件方格結構描述]。 
   2. 在 [主題詳細資料] 區段中，執行下列工作：
       1. 確認 [主題類型] 已設為 [IoT 中樞]。 
       2. 確認 IoT 中樞的名稱已設為 [來源資源] 欄位的值。 
       3. 為要替您建立的**系統主題**輸入名稱。 若要了解系統主題，請參閱[系統主題概觀](system-topics.md)。
   3. 在 [事件類型] 區段中，執行下列工作： 
        1. 針對 [篩選至事件類型]，將 [已建立的裝置] 之外的所有選擇取消選取。

           ![訂用帳戶事件種類](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   4. 在 [端點詳細資料] 區段中，執行下列工作： 
       1. 針對 [端點類型]選取 [Web Hook]。
       2. 按一下 [選取端點]，貼上您從邏輯應用程式複製的 URL，然後確認選取。

         ![選取端點 URL](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         當您完成時，窗格看起來應如下列範例所示： 

        ![事件訂用帳戶表單範例](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. 您可以在這裡儲存事件訂用帳戶，並接收在 IoT 中樞內建立之每個裝置的通知。 但在此教學課程中，我們可使用選用欄位來篩選特定裝置。 選取窗格頂端的 [篩選]。

6. 選取 [新增篩選條件]。 在欄位中填入下列值：

   * **金鑰**：選取 `Subject`。

   * **操作員**：選取 `String begins with`。

   * **值**：輸入 `devices/Building1_` 以篩選建築物 1 中的裝置事件。
  
   使用下列值新增另一個篩選條件：

   * **金鑰**：選取 `Subject`。

   * **操作員**：選取 `String ends with`。

   * **值**：輸入 `_Temperature` 以篩選與溫度相關的裝置事件。

   事件訂用帳戶的 [篩選條件] 索引標籤現在看起來應該如下圖所示：

   ![將篩選條件新增至事件訂用帳戶](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. 選取 [建立] 以儲存事件訂用帳戶。

## <a name="create-a-new-device"></a>建立新的裝置

建立新裝置來觸發事件通知電子郵件，從而測試應用程式邏輯。 

1. 從您的 IoT 中樞，選取 [IoT 裝置]。 

2. 選取 [ **新增**]。

3. 在 [裝置識別碼] 中，輸入 `Building1_Floor1_Room1_Light`。

4. 選取 [儲存]。 

5. 您可以使用不同的裝置識別碼來新增多個裝置，以便測試事件訂用帳戶篩選條件。 請嘗試下列範例： 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   如果您已新增這四個範例，您的 IoT 裝置清單看起來應該如下圖所示：

   ![IoT 中樞裝置清單](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. 在 IoT 中樞內新增一些裝置後，請檢查您的電子郵件以查看哪些裝置觸發了邏輯應用程式。 

## <a name="use-the-azure-cli"></a>使用 Azure CLI

您不必使用 Azure 入口網站，可改用 Azure CLI 來完成 IoT 中樞步驟。 如需詳細資訊，請參閱 Azure CLI 頁面：[建立事件訂用帳戶](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription)和[建立 IoT 裝置](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity)。

## <a name="clean-up-resources"></a>清除資源

本教學課程使用了會使您的 Azure 訂用帳戶產生費用的資源。 完成教學課程試用和結果測試時，請停用或刪除不想保留的資源。 

若要刪除您在本教學課程中建立的所有資源，請刪除資源群組。 

1. 選取 [資源群組]，然後選取您為此教學課程建立的資源群組。

2. 在 [資源群組] 窗格中，選取 [刪除資源群組]。 系統會提示您輸入資源群組名稱，而後您可將其刪除。 其中包含的所有資源也會一併移除。

如果您不想要移除所有資源，您可以逐一管理它們。 

如果不想遺失您在應用程式邏輯上所做的工作，請將它停用，而非刪除它。 

1. 瀏覽至您的邏輯應用程式。

2. 在 [概觀] 刀鋒視窗上選取 [刪除]或 [停用]。 

每個訂用帳戶都可以有一個免費的 IoT 中樞。 如果您為本教學課程建立了免費中樞，則不必為了避免費用而將它刪除。

1. 瀏覽至您的 IoT 中樞。 

2. 在 [概觀] 刀鋒視窗上選取 [刪除]。 

即使要保留 IoT 中樞，您也可以刪除您所建立的事件訂用帳戶。 

1. 在 IoT 中樞中，選取 [Event Grid]。

2. 選取您想要移除的事件訂用帳戶。 

3. 選取 [刪除] 。 

## <a name="next-steps"></a>後續步驟

* 深入了解[使用 Event Grid 來觸發動作以回應 IoT 中樞事件](../iot-hub/iot-hub-event-grid.md)。
* [了解如何排序裝置連線和中斷連線事件](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* 了解您可使用 [Event Grid](overview.md) 執行哪些作業。