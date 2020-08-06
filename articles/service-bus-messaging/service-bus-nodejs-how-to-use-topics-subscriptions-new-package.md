---
title: 透過 Node.js 使用 Azure/服務匯流排主題和訂用帳戶
description: 快速入門：了解如何從 Node.js 應用程式，在 Azure 中使用服務匯流排主題和訂用帳戶。
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: 531322c49a772eaf416fadf1bb4f9a5fb6bf1ff6
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430611"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>快速入門：如何透過 Node.js 和 azure/service-bus 套件使用服務匯流排主題和訂用帳戶
在本教學課程中，您將了解如何撰寫 Node.js 程式，以使用新的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 套件將訊息傳送至服務匯流排主題，以及接收來自服務匯流排訂用帳戶的訊息。 此套件使用較快速的 [AMQP 1.0 通訊協定](service-bus-amqp-overview.md)，而舊版的 [azure-sb](https://www.npmjs.com/package/azure-sb) 套件則使用[服務匯流排 REST 執行階段 API](/rest/api/servicebus/service-bus-runtime-rest)。 相關範例是以 JavaScript 撰寫的。

## <a name="prerequisites"></a>Prerequisites
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或是[註冊免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有可用的主題和訂用帳戶，請執行[使用 Azure 入口網站建立服務匯流排主題和訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)一文中的步驟，加以建立。 請記下服務匯流排執行個體的連接字串，以及您所建立的主題和訂用帳戶的名稱。 我們將在範例中使用這些值。

> [!NOTE]
> - 本教學課程使用您可以透過 [Nodejs](https://nodejs.org/) 來複製和執行的範例。 如需關於建立 Node.js 應用程式的指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站](../app-service/app-service-web-get-started-nodejs.md)或 [Node.js 雲端服務 (使用 Windows PowerShell)](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。
> - 新的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 套件尚不支援建立主題和訂用帳戶的作業。 如果您想要以程式設計方式加以建立，請使用 [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) 套件。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node Package Manager (NPM) 安裝封裝
若要安裝服務匯流排的 npm 套件，請開啟在路徑中有 `npm` 的命令提示字元，並將目錄切換至您要用來存放範例的資料夾，然後執行下列命令。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題
與服務匯流排主題的互動時，會先具現化 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 類別，並使用它來具現化 [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) 類別。 當您擁有主題用戶端之後，您即可建立傳送者，並對其使用 [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) 或 [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) 方法來傳送訊息。

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `send.js` 的檔案，並在其中貼上下列程式碼。 此程式碼會將 10 則訊息傳送至您的主題。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
        try {
            for (let i = 0; i < 10; i++) {
              const message= {
                body: `Hello world! ${i}`,
                label: `test`,
                userProperties: {
                    myCustomPropertyName: `my custom property value ${i}`
                }
              };
              console.log(`Sending message: ${message.body}`);
              await sender.send(message);
            }

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入主題的連接字串和名稱。
4. 然後，在命令提示字元中執行命令 `node send.js`，以執行此檔案。 

恭喜！ 您已將訊息傳送至服務匯流排佇列。

訊息具有一些可在傳送時設定的標準屬性，例如 `label` 和 `messageId`。 如果您想要設定任何自訂屬性，請使用 `userProperties`，這是一個 JSON 物件，可保存自訂資料的索引鍵/值組。

服務匯流排主題支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有限制。 此主題大小會在建立時定義，上限是 5 GB。 如需有關配額的詳細資訊，請參閱 [服務匯流排配額](service-bus-quotas.md)。

## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息
與服務匯流排訂用帳戶的互動時，會先具現化 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 類別，並使用它來具現化 [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) 類別。 擁有訂用帳戶用戶端之後，您即可建立接收者，並對其使用 [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) 或 [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) 方法來接收訊息。

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `recieve.js` 的檔案，並在其中貼上下列程式碼。 此程式碼會嘗試從您的訂用帳戶接收 10 則訊息。 您實際收到的計數取決於訂用帳戶中的訊息數目和網路延遲。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入主題和訂用帳戶的連接字串和名稱。
4. 然後，在命令提示字元中執行命令 `node receiveMessages.js`，以執行此檔案。

恭喜！ 您已從服務匯流排訂用帳戶接收訊息。

[createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) 方法會採用 `ReceiveMode`，這是一個具有 [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) 和 [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations) 值的列舉。 如果您在訊息上使用了任何 `complete()`、`abandon()`、`defer()` 或 `deadletter()` 方法藉以使用 `PeekLock` 模式，請務必[結算您的訊息](message-transfers-locks-settlement.md#settling-receive-operations)。

## <a name="subscription-filters-and-actions"></a>訂用帳戶篩選和動作
服務匯流排支援[訂用帳戶的篩選和動作](topic-filters.md)，這可讓您篩選傳入訂用帳戶的訊息，以及編輯其屬性。

當您擁有 `SubscriptionClient` 的執行個體之後，您即可對其使用下列方法來取得、新增和移除訂用帳戶的規則，以控制篩選和動作。

- getRules
- addRule
- removeRule

每個訂用帳戶都有一個預設規則，會使用 true 篩選允許所有傳入的訊息。 當您新增規則時，請記得移除預設篩選，讓新規則中的篩選能夠正常運作。 如果訂用帳戶沒有任何規則，則不會接收任何訊息。

> [!NOTE]
> 您可以使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/)來管理服務匯流排資源。 服務匯流排總管可讓使用者連線到服務匯流排命名空間，並以簡便的方式管理傳訊實體。 此工具提供進階的功能 (例如匯入/匯出功能) 或測試主題、佇列、訂用帳戶、轉送服務、通知中樞和事件中樞的能力。 

## <a name="next-steps"></a>後續步驟
若要深入了解，請參閱下列資源。

- [佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
- [在 GitHub 上查看服務匯流排的其他 Nodejs 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 開發人員中心](https://azure.microsoft.com/develop/nodejs/)


