---
title: 使用 Node.js 從 Azure 事件中樞傳送或接收事件 (舊版)
description: 本文將逐步解說如何建立 Node.js 應用程式，以使用舊有的 azure/event-hubs 第 2 版套件對 Azure 事件中樞傳送事件或接收事件。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 9aa2418657c2d3bcab9ef8883e5bd57422ce5e29
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899886"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs-azureevent-hubs-version-2"></a>快速入門：使用 Node.js 將事件傳送至 Azure 事件中樞或從中接收事件 (@azure/event-hubs 第 2 版)

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何建立 Node.js 應用程式，以將事件傳送至事件中樞或從中接收事件。

> [!WARNING]
> 本快速入門適用於第 2 版的 Azure 事件中樞 Java Script SDK。 建議您將程式碼[遷移](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)至 [Java Script SDK 第 5 版](get-started-node-send-v2.md)。 



## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列必要條件：

- 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Node.js 8.x 版和更新版本。 從 [https://nodejs.org](https://nodejs.org) 下載最新的 LTS 版本。
- Visual Studio Code (建議採用) 或任何其他的 IDE
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和「事件中樞」，請依照[這篇文章](event-hubs-create.md)中的程序操作，然後繼續進行此教學課程中的下列步驟。 然後，依照下列文章中的指示，取得事件中樞命名空間的連接字串：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您稍後會在本教學課程中使用連接字串。


### <a name="install-npm-package"></a>安裝 npm 套件
若要安裝[事件中樞的 npm 套件](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)，請開啟在路徑中有 `npm` 的命令提示字元，並將目錄切換至您要用來存放範例的資料夾，然後執行下列命令

```shell
npm install @azure/event-hubs@2
```

若要安裝[事件處理器主機的 npm 套件](https://www.npmjs.com/package/@azure/event-processor-host)，請改為執行下列命令

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>傳送事件

本節說明如何建立可將事件傳送至事件中樞的 Node.js 應用程式。 

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 和 `EventHubName` 字串，然後執行。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com)。 
2. 建立名為 `send.js` 的檔案，並在其中貼上下列程式碼。 遵循以下文章中的指示，取得事件中樞命名空間的連接字串：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入事件中樞的連接字串和名稱
4. 然後，在命令提示字元中執行命令 `node send.js`，以執行此檔案。 這會將 100 個事件傳送至事件中樞

恭喜！ 您現在已將事件傳送至事件中樞。


## <a name="receive-events"></a>接收事件

本節說明如何建立 Node.js 應用程式，以從事件中樞的預設取用者群組中的單一分割區接收事件。 

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com)。 
2. 建立名為 `receive.js` 的檔案，並在其中貼上下列程式碼。
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入事件中樞的連接字串和名稱。
4. 然後，在命令提示字元中執行命令 `node receive.js`，以執行此檔案。 這會從事件中樞的預設取用者群組中的一個分割區接收事件

恭喜！ 您現在已從事件中樞接收事件。

## <a name="receive-events-using-event-processor-host"></a>使用事件處理器主機接收事件

本節說明如何在 Node.js 應用程式中，使用 Azure [EventProcessorHost](event-hubs-event-processor-host.md) 從事件中樞接收事件。 EventProcessorHost (EPH) 可藉由在事件中樞取用者群組中的所有分割區上建立接收者，協助您有效率地從事件中樞接收事件。 它會在 Azure 儲存體 Blob 中，定期針對所接收訊息的中繼資料進行檢查點檢查。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com)。 
2. 建立名為 `receiveAll.js` 的檔案，並在其中貼上下列程式碼。
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. 在上述程式碼中，輸入事件中樞的連接字串和名稱，以及 Azure Blob 儲存體的連接字串
4. 然後，在命令提示字元中執行命令 `node receiveAll.js`，以執行此檔案。

恭喜！ 您現在已使用事件處理器主機從事件中樞接收事件。 這會從事件中樞的預設取用者群組中的所有分割區接收事件

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)
- 在 GitHub 上查看[事件中樞](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples)和[事件處理器主機](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples)的其他 Node.js 範例
