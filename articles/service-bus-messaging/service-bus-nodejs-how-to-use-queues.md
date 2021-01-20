---
title: 如何在 JavaScript 中使用 azure/service-bus 佇列
description: 了解如何撰寫 JavaScript 程式，以使用 @azure/service-bus 套件的最新預覽版本，在服務匯流排的佇列中傳送和接收訊息。
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: ac24d84176f27170648545bc8044c5dcbc77781a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180008"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>在 Azure 服務匯流排佇列 (JavaScript) 中傳送和接收訊息
在本教學課程中，您將了解如何在 JavaScript 程式中使用 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 套件傳送訊息，並接收來自服務匯流排的訊息。

## <a name="prerequisites"></a>先決條件
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或是[註冊免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有可用的佇列，請執行[使用 Azure 入口網站建立服務匯流排佇列](service-bus-quickstart-portal.md)一文中的步驟，以建立佇列。 請記下服務匯流排命名空間的 **連接字串**，以及您所建立的 **佇列** 名稱。

> [!NOTE]
> - 本教學課程使用您可以透過 [Nodejs](https://nodejs.org/) 來複製和執行的範例。 如需有關建立 Node.js 應用程式的指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站](../app-service/quickstart-nodejs.md)或 [Node.js 雲端服務 (使用 Windows PowerShell)](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node Package Manager (NPM) 安裝封裝
若要安裝服務匯流排的 npm 套件，請開啟在路徑中有 `npm` 的命令提示字元，並將目錄切換至您要用來存放範例的資料夾，然後執行下列命令。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列
下列範例程式碼示範如何將訊息傳送至佇列。

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)。
2. 建立名為 `send.js` 的檔案，並在其中貼上下列程式碼。 此程式碼會將訊息傳送至您的佇列。 訊息具有標籤 (科學家) 和主體 (愛因斯坦)。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. 將 `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` 取代為服務匯流排命名空間的連接字串。
1. 將 `<QUEUE NAME>` 取代為佇列名稱。 
1. 然後，在命令提示字元中執行命令，以執行此檔案。

    ```console
    node send.js 
    ```
1. 您應該會看見下列輸出。

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `receive.js` 的檔案，並將以下程式碼貼在該檔案內。

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. 將 `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` 取代為服務匯流排命名空間的連接字串。
1. 將 `<QUEUE NAME>` 取代為佇列名稱。 
1. 然後，在命令提示字元中執行命令，以執行此檔案。

    ```console
    node receive.js
    ```
1. 您應該會看見下列輸出。

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

在 Azure 入口網站中服務匯流排命名空間的 **概觀** 頁面上，您可以看到 **傳入** 和 **傳出** 訊息計數。 請等候一分鐘左右，然後重新整理頁面以查看最新的值。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="傳入和傳出訊息計數":::

選取此 **概觀** 頁面上的佇列，瀏覽至 **服務匯流排佇列** 頁面。 您也會在此頁面上看到 **傳入** 和 **傳出** 訊息計數。 您也會看到其他資訊，例如佇列的 **目前大小**、**大小上限**、**作用中訊息計數** 等等。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="佇列詳細資料":::
## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例： 

- [適用於 JS 的 Azure 服務匯流排用戶端程式庫](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples)。 **javascript** 資料夾具有 JavaScript 範例，而 **typescript** 具有 TypeScript 範例。 
- [azure-servicebus 參考文件](/javascript/api/overview/azure/service-bus)
