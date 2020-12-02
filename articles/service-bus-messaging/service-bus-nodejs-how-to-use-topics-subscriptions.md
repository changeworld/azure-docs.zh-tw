---
title: 搭配主題和訂用帳戶使用預覽 JavaScript azure/service-bus
description: 了解如何撰寫 JavaScript 程式，以使用 @azure/service-bus 套件的最新預覽版本，將訊息傳送至服務匯流排主題並從主題訂用帳戶中接收訊息。
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: aac3b6339c318c76e9b0c9abd0bc3778f2563a6f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498688"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>快速入門：透過 Node.js 和預覽 azure/service-bus 套件使用服務匯流排主題和訂用帳戶
在本教學課程中，您將了解如何以 JavaScript 程式使用 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 套件，將訊息傳送至服務匯流排主題，以及接收來自主題服務匯流排訂用帳戶的訊息。

## <a name="prerequisites"></a>先決條件
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或是[註冊免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 依照下列快速入門中的步驟操作：[快速入門：使用 Azure 入口網站建立服務匯流排主題和主題的訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)。 記下連接字串、主題名稱和訂用帳戶名稱。 在本快速入門中，您只會使用一個訂用帳戶。 

> [!NOTE]
> - 本教學課程使用您可以透過 [Nodejs](https://nodejs.org/) 來複製和執行的範例。 如需關於建立 Node.js 應用程式的指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站](../app-service/quickstart-nodejs.md)或 [Node.js 雲端服務 (使用 Windows PowerShell)](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node Package Manager (NPM) 安裝封裝
若要安裝服務匯流排的 npm 套件，請開啟在路徑中有 `npm` 的命令提示字元，並將目錄切換至您要用來存放範例的資料夾，然後執行下列命令。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題
下列範例程式碼示範如何將訊息批次傳送至服務匯流排主題。 如需詳細資訊，請參閱程式碼註解。 

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `sendtotopic.js` 的檔案，並在其中貼上下列程式碼。 此程式碼會將訊息傳送至您的主題。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. 將 `<SERVICE BUS NAMESPACE CONNECTION STRING>` 取代為服務匯流排命名空間的連接字串。
1. 將 `<TOPIC NAME>` 取代為主題名稱。 
1. 然後，在命令提示字元中執行命令，以執行此檔案。

    ```console
    node sendtotopic.js 
    ```
1. 您應該會看見下列輸出。

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息
1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 **receivefromsubscription.js** 的檔案，並將以下程式碼張貼在該檔案內。 如需詳細資訊，請參閱程式碼註解。 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. 將 `<SERVICE BUS NAMESPACE CONNECTION STRING>` 取代為命名空間的連接字串。 
1. 將 `<TOPIC NAME>` 取代為主題名稱。 
1. 將 `<SUBSCRIPTION NAME>` 取代為主題訂用帳戶的名稱 。 
1. 然後，在命令提示字元中執行命令，以執行此檔案。

    ```console
    node receivefromsubscription.js
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

在 Azure 入口網站中，瀏覽至您的服務匯流排命名空間，然後選取底部窗格中的主題，以查看主題的 **服務匯流排主題** 頁面。 在此頁面上，您應該會在 **訊息** 圖表中看到三個傳入和三個傳出訊息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="傳入和傳出訊息":::

如果您執行「下次僅傳送應用程式」，在 **服務匯流排主題** 頁面上，您會看到六個傳入訊息 (3 個新訊息)，但有三個傳出訊息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="已更新的主題頁面":::

如果您在此頁面上選取訂用帳戶，您會進入 **服務匯流排訂用帳戶** 頁面。 您可以在此頁面上看到作用中的訊息計數、寄不出的信件訊息計數等等。 在此範例中，收件者尚未收到三個作用中的訊息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="作用中訊息計數":::

## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例： 

- [適用於 Python 的 Azure 服務匯流排用戶端程式庫](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples)。 **javascript** 資料夾具有 JavaScript 範例，而 **typescript** 具有 TypeScript 範例。 
- [azure-servicebus 參考文件](/javascript/api/overview/azure/service-bus)