---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377327"
---
## <a name="prerequisites"></a>必要條件
開始之前，請務必：

- 建立具有有效訂閱的 Azure 帳戶。 如需詳細資訊，請參閱[免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安裝 [Node.js](https://nodejs.org/en/download/)、作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。
- 建立 Azure 通訊服務資源。 如需詳細資訊，請參閱[建立 Azure 通訊資源](../../create-communication-resource.md)。 您必須在本快速入門中記錄您的資源**端點**。
- [使用者存取權杖](../../access-tokens.md)。 請務必將範圍設定為 "chat"，並記下 token 字串和 userId 字串。

## <a name="setting-up"></a>設定

### <a name="create-a-new-web-application"></a>建立新的 Web 應用程式

首先，開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
執行 `npm init -y` 以使用預設設定建立 **package.json** 檔案。

```console
npm init -y
```

使用文字編輯器，在專案根目錄中建立名為 **start-chat.js** 的檔案。 您會在下列各節中，將本快速入門的所有原始程式碼新增至此檔案。

### <a name="install-the-packages"></a>安裝套件

使用 `npm install` 命令來安裝適用於 JavaScript 的下列通訊服務用戶端程式庫。

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save` 選項會在您的 **package.json** 檔案中，將程式庫列為相依性。

### <a name="set-up-the-app-framework"></a>設定應用程式架構

本快速入門會使用 webpack 來組合應用程式資產。 執行下列命令來安裝 webpack、webpack-cli 和 webpack-dev-server 套件，並將這些套件列為 **package.json** 中的開發相依性：

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

在專案的根目錄中建立 **index.html** 檔案。 我們會使用此檔案作為範本，使用適用於 JavaScript 的 Azure 通訊聊天用戶端程式庫來新增聊天功能。

程式碼如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
在專案的根目錄中建立名為 **client.js** 的檔案，以包含本快速入門的應用程式邏輯。 

### <a name="create-a-chat-client"></a>建立聊天用戶端

若要在 Web 應用程式中建立聊天用戶端，您將使用 Azure 通訊服務端點，以及在必要條件步驟中所產生的存取權杖。 使用者存取權杖可讓您建立直接向 Azure 通訊服務進行驗證的用戶端應用程式。 一旦您在伺服器上產生這些權杖，請將其傳回給用戶端裝置。 您需要使用 `Common client library` 中的 `AzureCommunicationUserCredential` 類別，將權杖傳遞給您的聊天用戶端。

在專案的根目錄中建立 **client.js** 檔案。 我們會使用此檔案，使用適用於 JavaScript 的 Azure 通訊聊天用戶端程式庫來新增聊天功能。

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
根據[建立 Azure 通訊資源](../../create-communication-resource.md)文件，以之前建立的端點取代 **ENDPOINT**。
將 **USER_ACCESS_TOKEN** 取代為根據[使用者存取權杖](../../access-tokens.md)文件所發行的權杖。
將此程式碼新增至 **client.js** 檔案


### <a name="run-the-code"></a>執行程式碼
使用 `webpack-dev-server` 來建置並執行您的應用程式。 執行下列命令，在本機 Web 伺服器上組合應用程式主機：
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
開啟瀏覽器並巡覽至 http://localhost:8080/。
在您瀏覽器內的開發人員工具主控台中，您應該會看到下列內容：

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>物件模型 
下列類別和介面會處理 Azure 通訊服務的聊天用戶端程式庫的一些主要 JavaScript 功能。

| Name                                   | 描述                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | 這是聊天功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來建立、取得、刪除對話。 |
| ChatThreadClient | 這是聊天對話功能所需的類別。 您可以透過 ChatClient 取得執行個體，並用其來傳送/接收/更新/刪除訊息、新增/移除/取得使用者、傳送輸入通知和讀信回條，以及訂閱聊天事件。 |


## <a name="start-a-chat-thread"></a>開始聊天對話

使用 `createThread` 方法來建立聊天對話。

`createThreadRequest` 會用來描述對話要求：

- 使用 `topic` 來提供對話主題；在使用 `UpdateThread` 函式建立聊天對話之後，可以更新主題。 
- 使用 `members` 列出要新增至聊天對話的成員；

經過解析後，`createChatThread` 方法會傳回 `threadId`，以用來在新建的聊天對話上執行作業，例如將成員加入聊天對話、傳送訊息、刪除訊息等等。

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

將 **USER_ID_FOR_JACK** 和 **USER_ID_FOR_GEETA** 取代為上一個步驟取得的使用者識別碼 (建立使用者並發行[使用者存取權杖](../../access-tokens.md))

當您重新整理瀏覽器索引標籤時，您應該會在主控台中看到下列內容
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>取得聊天對話用戶端

`getChatThreadClient` 方法會傳回現有對話的 `chatThreadClient`。 其可以用來在建立的對話上執行作業：新增成員、傳送訊息等等。threadId 是現有聊天對話的唯一識別碼。

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
新增此程式碼來取代 **client.js** 中的 `<CREATE CHAT THREAD CLIENT>` 註解，重新整理您的瀏覽器索引標籤並查看主控台，您應該會看到：
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>將訊息傳送至聊天對話

使用 `sendMessage` 方法將聊天訊息傳送至您剛才建立的對話 (以 threadId 來識別)。

`sendMessageRequest` 會描述聊天訊息要求的必要欄位：

- 使用 `content` 來提供聊天訊息內容；

`sendMessageOptions` 會描述聊天訊息要求的選擇性欄位：

- 使用 `priority` 指定聊天的訊息優先順序等級，例如「一般」或「高」。這個屬性可用來在應用程式中當做收件者使用者的 UI 指標，以將注意力帶到訊息或執行自訂商務邏輯。   
- 使用 `senderDisplayName` 指定傳送者的顯示名稱；

`sendChatMessageResult` 回應會包含「識別碼」，這是該訊息的唯一識別碼。

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
新增此程式碼來取代 **client.js** 中的 `<SEND MESSAGE TO A CHAT THREAD>` 註解，重新整理您的瀏覽器索引標籤並查看主控台。
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>從聊天對話接收聊天訊息

使用即時信號時，您可以訂閱並接聽新的傳入訊息，並據以更新記憶體中的目前訊息。 Azure 通訊服務支援[您可以訂閱的事件清單](../../../concepts/chat/concepts.md#real-time-signaling)。

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
新增此程式碼來取代 **client.js**中的 `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` 註解。
重新整理瀏覽器索引標籤，您應該會在主控台中看到 `Notification chatMessageReceived` 訊息；

或者，您可以依指定的間隔，透過輪詢 `listMessages` 方法來擷取聊天訊息。 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
新增此程式碼來取代 **client.js** 中的 `<LIST MESSAGES IN A CHAT THREAD>` 註解。
重新整理索引標籤，您應該會在主控台中找到此聊天對話中傳送的訊息清單。


`listMessages` 會傳回訊息的最新版本，包括使用 `updateMessage` 和 `deleteMessage` 對訊息所做的任何編輯或刪除。
若為已刪除的訊息，`chatMessage.deletedOn` 會傳回 datetime 值，指出刪除該訊息的時間。 若為編輯過的訊息，`chatMessage.editedOn` 會傳回 datetime 值，指出編輯該訊息的時間。 用來排序訊息的 `chatMessage.createdOn` 可用來存取訊息建立的原始時間。

`listMessages` 會傳回不同訊息類型 (可用 `chatMessage.type` 識別)。 這些類型包括：

- `Text`:由對話成員傳送的一般聊天訊息。

- `ThreadActivity/TopicUpdate`:指出主題已更新的系統訊息。

- `ThreadActivity/AddMember`:指出已將一個或多個成員新增至聊天對話的系統訊息。

- `ThreadActivity/RemoveMember`:指出已從聊天對話中移除成員的系統訊息。

如需詳細資訊，請參閱[訊息類型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>將使用者新增為聊天對話的成員

聊天對話一旦建立，您就可以在其中新增和移除使用者。 新增使用者也會同時授與他們存取權，使他們可以將訊息傳送至聊天對話，以及新增/移除其他成員。 在呼叫 `addMembers` 方法之前，請確定您已取得該使用者的新存取權杖和身分識別。 使用者將需要該存取權杖，才能初始化其聊天用戶端。

`addMembersRequest` 會描述要求物件，其中 `members` 會列出要加入聊天對話的成員；
- (必要) `user` 是要新增至聊天對話的通訊使用者。
- (選擇性) `displayName` 是對話成員的顯示名稱。
- (選擇性) `shareHistoryTime` 是與成員共用聊天記錄的時間。 若要在聊天對話一開始就共用記錄，請將此屬性設定為等於或小於對話建立時間的任何日期。 若要在新增成員時不共用先前的記錄，請將其設定為目前的日期。 若要共用部分記錄，請將其設定為您選擇的日期。

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
將 **NEW_MEMBER_USER_ID** 取代為[新的使用者識別碼](../../access-tokens.md)。新增此程式碼來取代 **client.js** 中的 `<ADD NEW MEMBER TO THREAD>`

## <a name="list-users-in-a-chat-thread"></a>列出聊天對話中的使用者
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
新增此程式碼來取代 **client.js** 中的 `<LIST MEMBERS IN A THREAD>` 註解，重新整理您的瀏覽器索引標籤並查看主控台，您應該會看到對話中使用者的相關資訊。

## <a name="remove-user-from-a-chat-thread"></a>從聊天對話中移除使用者

類似於新增成員，您可以從聊天對話中移除成員。 若要移除，您必須追蹤已加入成員的識別碼。

使用 `removeMember` 方法，其中 `member` 是要從對話中移除的通訊使用者。

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
將 **MEMBER_ID** 取代為上一個步驟中使用的使用者識別碼 (<NEW_MEMBER_USER_ID>)。
新增此程式碼來取代 **client.js** 中的 `<REMOVE MEMBER FROM THREAD>` 註解，
