---
title: 訂購設備連接事件 fr Azure IoT 中心（帶 Azure 宇宙資料庫）
description: 本文說明如何使用 Azure Cosmos DB 排序和記錄來自 Azure IoT 中樞的裝置連線事件，以維持最新的連接狀態
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954260"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 排序來自 Azure IoT 中樞的裝置連線事件

Azure 事件方格可協助您建置以事件為基礎的應用程式，並輕鬆將 IoT 事件整合至您的商業解決方案中。 本文將逐步引導您完成可用來在 Cosmos DB 中追蹤和儲存最新裝置連線狀態的設定。 我們將使用「裝置連線」和「裝置中斷連線」事件中的可用序號，並將最新狀態儲存在 Cosmos DB 中。 我們將使用預存程序，也就是對 Cosmos DB 中的集合執行的應用程式邏輯。

序號是十六進位數字的字串表示法。 您可以使用字串比較來識別較大的號碼。 如果您想要將字串轉換為十六進位，號碼將是 256 位元的數字。 序號會持續增加，且最新事件的號碼會高於其他事件。 如果您的裝置經常連線和中斷連線，而您想要確保一律會以最新的事件來觸發下游動作，這將有其效用，因為 Azure 事件方格不支援事件的排序。

## <a name="prerequisites"></a>Prerequisites

* 使用中的 Azure 帳戶。 如果沒有，您可以創建[一個免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* 使用中的 Azure Cosmos DB SQL API 帳戶。 如果您尚未建立此帳戶，請參閱[建立資料庫帳戶](../cosmos-db/create-sql-api-java.md#create-a-database-account)以取得逐步解說。

* 您資料庫中的集合。 請參閱[新增集合](../cosmos-db/create-sql-api-java.md#add-a-container)以取得逐步解說。 創建集合時，請使用`/id`分區鍵。

* Azure 中的 IoT 中樞。 若尚未建立，請參閱[開始使用 IoT 中樞](iot-hub-csharp-csharp-getstarted.md)的逐步解說。

## <a name="create-a-stored-procedure"></a>建立預存程序

首先，建立預存程序並加以設定，使其執行會比較傳入事件的序號，並將每個裝置的最新事件記錄在資料庫中的邏輯。

1. 在 Cosmos DB SQL API 中，選擇**資料資源管理器** > **項** > **"新的預存程序**"。

   ![建立預存程序](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. 輸入預存程序 ID**的最新設備連接狀態**，並在**預存程序正文**中粘貼以下內容。 請注意，此程式碼應取代預存程序主體中任何現有的程式碼。 此程式碼會為每個裝置識別碼維護一個資料列，並藉由識別最高的序號，來記錄該裝置識別碼的最新連線狀態。

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. 儲存預存程序：

    ![儲存預存程序](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>建立邏輯應用程式

首先，建立邏輯應用程式，並新增可監視您虛擬機器資源群組的 Event Grid 觸發程序。

### <a name="create-a-logic-app-resource"></a>建立邏輯應用程式資源

1. 在[Azure 門戶](https://portal.azure.com)中，選擇 **"創建資源**"，選擇 **"集成**"，然後選擇**邏輯應用**。

   ![建立邏輯應用程式](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. 在訂用帳戶中為邏輯應用程式指定唯一的名稱，然後選取與您的 IoT 中樞相同的訂用帳戶、資源群組和位置。

   ![新邏輯應用](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. 選取 [建立]**** 以建立邏輯應用程式。

   您現在已經為您的應用程式邏輯建立一項 Azure 資源。 在 Azure 部署您的邏輯應用程式之後，Logic Apps 設計工具會顯示常見模式的範本，以便您更快開始使用。

   > [!NOTE]
   > 要再次查找和打開邏輯應用，請選擇 **"資源組"** 並選擇用於此"方法"的資源組。 然後選擇新的邏輯應用。 這將打開邏輯應用設計器。

4. 在邏輯應用設計器中，向右滾動，直到看到常見觸發器。 在**範本**下，選擇**空白邏輯應用**，以便您可以從頭開始構建邏輯應用。

### <a name="select-a-trigger"></a>選取觸發程序

觸發程序是啟動邏輯應用程式的特定事件。 在本教學課程中，啟動工作流程的觸發程序會透過 HTTP 接收要求。

1. 在連接器和觸發器搜索欄中，鍵入**HTTP**並點擊 Enter。

2. 選取 [要求 - 收到 HTTP 要求時]**** 作為觸發程序。

   ![選取 HTTP 要求觸發程序](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. 選取 [使用範例承載來產生結構描述]****。

   ![使用範例承載來產生結構描述](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. 將下列 JSON 程式碼範例貼到文字方塊中，然後選取 [完成]****：

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![粘貼示例 JSON 有效負載](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. 您可能會收到快顯通知，指出**請務必在您的要求中加入 Content-Type 標頭，並將其設為 application/json。** 您可以放心地忽略這項建議，並移至下一節。

### <a name="create-a-condition"></a>建立條件

在您的邏輯應用程式工作流程中，條件有助於在特定條件通過後，執行特定動作。 一旦符合條件，就可以定義所需的動作。 在本教學課程中，條件是要檢查 eventType 是裝置連線還是中斷連線。 此動作將會在您的資料庫中執行預存程序。

1. 選擇 **= "新建步驟**"，然後**內置**，然後查找並選擇 **"條件**"。 按一下"**選擇值**"，然後彈出一個框，顯示"動態內容"-可以選擇的欄位。 填寫如下所示的欄位，以便僅對設備已連接事件和設備斷開連接事件執行此操作：

   * 選擇值：**事件種類**-- 從按一下此欄位時顯示的動態內容中的欄位中選擇此選項。
   * 將"等於"更改為**以 結尾**。
   * 選擇值：**分項**。

     ![填入條件](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. 在 **"如果為 true"** 對話方塊中，按一下"**添加操作**"。
  
   ![若為 true 則新增動作](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. 搜索宇宙資料庫並選擇**Azure 宇宙 DB - 執行預存程序**

   ![搜尋 CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. 填寫**連接名稱**的**cosmosdb 連接**，然後選擇表中的條目，然後選擇 **"創建**"。 您將看到 **"執行預存程序"** 面板。 輸入欄位的值：

   **資料庫 ID**： 待辦事項

   **集合 ID**： 專案

   **Sproc ID**： 最新設備連接狀態

5. 選擇 **"添加新參數**"。 在顯示的下拉清單中，選中**預存程序**的**分區鍵**和參數旁邊的核取方塊，然後按一下螢幕上的任意位置;它添加分區鍵值的欄位和預存程序的參數欄位。

   ![填入邏輯應用程式動作](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. 現在輸入分區鍵值和參數，如下所示。 請務必放在括弧和雙引號中，如圖所示。 您可能需要按一下 **"添加動態內容**"才能獲取可在此處使用的有效值。

   ![填入邏輯應用程式動作](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. 在窗格的頂部，上面寫著 **"每個"，** 在 **"從以前步驟中選擇輸出**"下，請確保選擇 **"正文**"。

   ![為每個填充邏輯應用](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. 儲存您的邏輯應用程式。

### <a name="copy-the-http-url"></a>複製 HTTP URL

離開 Logic Apps 設計工具之前，為觸發程序複製邏輯應用程式正在接聽的 URL。 您可以使用此 URL 來設定 Event Grid。

1. 按一下以展開 [收到 HTTP 要求時]**** 觸發程序設定方塊。

2. 選取 [HTTP POST URL]**** 旁的複製按鈕來複製其值。

   ![複製 HTTP POST URL](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. 儲存這個 URL，以便在下一節參考它。

## <a name="configure-subscription-for-iot-hub-events"></a>設定 IoT 中樞事件的訂用帳戶

在本節中，您可將 IoT 中樞設定為在事件發生時進行發佈。

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。

2. 選取 [事件]****。

   ![開啟 Event Grid 詳細資料](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. 選擇 **= 事件訂閱**。

   ![建立新的事件訂用帳戶](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. 填寫**事件訂閱詳細資訊**：提供描述性名稱並選擇**事件網格架構**。

5. 填寫 **"事件種類"** 欄位。 在下拉清單中，僅選擇 **"設備已連接****"和"設備**從功能表斷開連接"。 按一下螢幕上的任意位置以關閉清單並保存您的選擇。

   ![設置要查找的事件種類](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. 對於**終結點詳細資訊**，選擇"終結點類型"作為**Web 掛鉤**，然後按一下選擇終結點並粘貼從邏輯應用複製的 URL 並確認選擇。

   ![選擇終結點 URL](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. 表單現在應類似于以下示例：

   ![事件訂用帳戶表單範例](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   選取 [建立]**** 以儲存事件訂用帳戶。

## <a name="observe-events"></a>觀察事件

現在您已設定事件訂用帳戶，接下來我們將連接裝置以進行測試。

### <a name="register-a-device-in-iot-hub"></a>在 IoT 中樞登錄裝置

1. 從您的 IoT 中樞，選取 [IoT 裝置]****。

2. 選擇 **"在**窗格頂部添加"。

3. 在 [裝置識別碼]**** 中，輸入 `Demo-Device-1`。

4. 選取 [儲存]****。

5. 您可以使用不同的裝置識別碼來新增多個裝置。

   ![添加到集線器的設備](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. 再次按一下設備;現在將填充連接字串和鍵。 複製**連接字串 -- 主索引鍵**，以供稍後使用。

   ![設備連接字串](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>啟動 Raspberry Pi 模擬器

我們將使用 Raspberry Pi Web 模擬器來模擬裝置連線。

[啟動 Raspberry Pi 模擬器](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>在 Raspberry Pi Web 模擬器上執行範例應用程式

這將會觸發裝置連線事件。

1. 在編碼區域中，將第 15 行中的預留位置替換為在上一節末尾保存的 Azure IoT 中心設備連接字串。

   ![粘貼到設備連接字串中](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. 通過選擇 **"運行"來運行**應用程式。

您將看到類似于以下輸出的內容，該輸出顯示感應器資料和發送到 IoT 中心的消息。

   ![執行應用程式](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   按一下 [停止]**** 以停止模擬器，然後觸發 [裝置中斷連線]**** 事件。

現在，您已執行範例應用程式以收集感應器資料，並將其傳送至 IoT 中樞。

### <a name="observe-events-in-cosmos-db"></a>觀察 Cosmos DB 中的事件

您可以在 Cosmos DB 文件中查看執行預存程序的結果。 結果看起來就像下面這樣。 每個資料列分別包含每個裝置的最新裝置連線狀態。

   ![操作結果](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

您不必使用 [Azure 入口網站](https://portal.azure.com)，可改用 Azure CLI 來完成 IoT 中樞步驟。 如需詳細資訊，請參閱 Azure CLI 頁面：[建立事件訂用帳戶](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription)和[建立 IoT 裝置](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)。

## <a name="clean-up-resources"></a>清除資源

本教學課程使用了會使您的 Azure 訂用帳戶產生費用的資源。 完成教學課程試用和結果測試時，請停用或刪除不想保留的資源。

如果不想遺失您在應用程式邏輯上所做的工作，請將它停用，而非刪除它。

1. 瀏覽至您的邏輯應用程式。

2. 在 [概觀]**** 刀鋒刀鋒視窗上選取 [刪除]**** 或 [停用]****。

    每個訂用帳戶都可以有一個免費的 IoT 中樞。 如果您為本教學課程建立了免費中樞，則不必為了避免費用而將它刪除。

3. 瀏覽至您的 IoT 中樞。

4. 在 [概觀]**** 刀鋒視窗上選取 [刪除]****。

    即使要保留 IoT 中樞，您也可以刪除您所建立的事件訂用帳戶。

5. 在 IoT 中樞中，選取 [Event Grid]****。

6. 選取您想要移除的事件訂用帳戶。

7. 選擇 **"刪除**"。

若要從 Azure 入口網站中移除 Azure Cosmos DB 帳戶，請以滑鼠右鍵按一下帳戶名稱，然後按一下 [刪除帳戶]****。 請參閱[刪除 Azure Cosmos DB 帳戶](https://docs.microsoft.com/azure/cosmos-db/manage-account)中的詳細指示。

## <a name="next-steps"></a>後續步驟

* 深入了解如何[使用 Event Grid 來觸發動作以回應 IoT 中樞事件](../iot-hub/iot-hub-event-grid.md)

* [試試 IoT 中樞事件教學課程](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* 了解您還可使用[事件方格](../event-grid/overview.md)執行哪些作業