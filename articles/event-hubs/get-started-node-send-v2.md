---
title: 使用 Node.js 從 Azure 事件中樞傳送或接收事件 (最新版)
description: 本文將逐步解說如何建立 Node.js 應用程式，以使用最新的 azure/event-hubs 第 5 版套件對 Azure 事件中樞傳送事件或從中接收事件。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906364"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>使用 Node.js (azure/event-hubs 第 5 版) 將事件傳送至事件中樞或從中接收事件

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需詳細資訊，請參閱[事件中樞概觀](event-hubs-about.md)及[事件中樞功能](event-hubs-features.md)。

本快速入門說明如何建立可將事件傳送至事件中樞或從中接收事件的 Node.js 應用程式。

> [!IMPORTANT]
> 本快速入門使用第 5 版的 Azure 事件中樞 Java 指令碼 SDK。 如需使用第 2 版 JavaScript SDK 的快速入門，請參閱[這篇文章](event-hubs-node-get-started-send.md)。 

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您必須符合下列必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。  
- Node.js 8.x 版或更新版本， 下載最新的[長期支援 (LTS) 版本](https://nodejs.org)。  
- Visual Studio Code (建議) 或任何其他整合式開發環境 (IDE)。  
- 作用中的事件中樞命名空間和事件中樞。 若要建立這些項目，請執行下列步驟： 

   1. 在 [Azure 入口網站](https://portal.azure.com)中，建立「事件中樞」  類型的命名空間，然後取得您的應用程式與事件中樞進行通訊所需的管理認證。 
   1. 若要建立命名空間和事件中樞，請遵循[快速入門：使用 Azure 入口網站建立事件中樞](event-hubs-create.md)。
   1. 遵循本快速入門中的指示以繼續。 
   1. 若要取得事件中樞命名空間的連接字串，請依照[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的指示進行。 記下連接字串，以便稍後於本快速入門中使用。

### <a name="install-the-npm-package"></a>安裝 npm 套件
若要安裝[適用於事件中樞的 Node Package Manager (npm) 套件](https://www.npmjs.com/package/@azure/event-hubs)，請開啟在路徑中有 npm  的命令提示字元，並將目錄切換至您要用來存放範例的資料夾，然後執行下列命令：

```shell
npm install @azure/event-hubs
```

針對接收端，您需要再安裝兩個套件。 在本快速入門中，您會使用 Azure Blob 儲存體來保存檢查點，讓程式不用再讀取已讀取過的事件。 該儲存體會在 Blob 中定期對接收的訊息執行中繼資料檢查點檢查。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

執行下列命令：

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>傳送事件

在本節中，您會建立可將事件傳送至事件中樞的 Node.js 應用程式。

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com)。
1. 建立名為 send.js  的檔案，並將以下程式碼張貼在該檔案內：

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. 在程式碼中，請使用實際值來取代下列各項：
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. 執行 `node send.js` 來執行此檔案。 此命令會將一個內含三個事件的批次傳送至您的事件中樞。
1. 在 Azure 入口網站中，確認事件中樞是否已收到訊息。 在 [計量]  區段中，切換至 [訊息]  檢視。 請重新整理頁面來更新圖表。 可能需要幾秒鐘的時間，頁面才會顯示已收到訊息。

    [![確認事件中樞已收到訊息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 如需完整的原始程式碼 (包括其他資訊註解)，請移至 [GitHub sendEvents.js 頁面](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)。

恭喜！ 您現在已將事件傳送至事件中樞。


## <a name="receive-events"></a>接收事件
在本節中，您會使用 Node.js 應用程式中的 Azure Blob 儲存體檢查點存放區，從事件中樞接收事件。 其會在 Azure 儲存體 Blob 中，定期對接收的訊息執行中繼資料檢查點檢查。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>建立 Azure 儲存體帳戶和 Blob 容器
若要建立 Azure 儲存體帳戶及其中的 Blob 容器，請執行下列動作：

1. [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [在儲存體帳戶中建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [取得儲存體帳戶的連接字串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

請務必記下連接字串和容器名稱，以便稍後在接收程式碼中使用。

### <a name="write-code-to-receive-events"></a>撰寫程式碼來接收事件

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com)。
1. 建立名為 receive.js  的檔案，並將以下程式碼張貼在該檔案內：

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. 在程式碼中，請使用實際值來取代下列值：
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. 在命令提示字元中執行 `node receive.js` 來執行這個檔案。 視窗應該會顯示已接收事件的相關訊息。

    > [!NOTE]
    > 如需完整的原始程式碼 (包括其他資訊註解)，請移至 [GitHub receiveEventsUsingCheckpointStore.js 頁面](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)。

恭喜！ 您現在已從事件中樞接收事件。 接收者程式會從事件中樞的所有預設取用者群組分割區接收事件。

## <a name="next-steps"></a>後續步驟
在 GitHub 上查看這些範例：

- [JavaScript 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript) \(英文\)
- [TypeScript 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
