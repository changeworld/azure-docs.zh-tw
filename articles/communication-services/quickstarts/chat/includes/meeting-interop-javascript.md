---
title: 快速入門 - 加入 Teams 會議
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 780ef2bbb7851d8bef5fc52a51421a7938043ecb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932325"
---
## <a name="join-the-meeting-chat"></a>加入會議交談 

一旦啟用 Teams 的互通性之後，通訊服務使用者就可以使用通話用戶端程式庫，以來賓使用者身分加入 Teams 通話。 加入通話也會將他們新增為會議交談的參與者，讓他們可以與通話中的其他使用者傳送及接收訊息。 使用者無法存取加入通話之前已傳送的交談訊息。 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>為通訊服務使用者取得 Teams 會議交談的對話

首先，針對會議交談的對話具現化 `ChatThreadClient`。 剖析會議連結，或使用具有會議識別碼的圖形 API 來取得對話識別碼。 

- Teams 會議連結看起來像這樣：`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`。 對話識別碼會是 `meeting_chat_thread_id` 在該連結中的位置。 
- 如果您有會議識別碼，可以使用 [圖形 API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) 來取得對話識別碼。 [GET API](/graph/api/onlinemeeting-get?tabs=http%22+%5c&view=graph-rest-beta) 回應會有一個包含 `threadID` 的 `chatInfo` 物件。 

獲得交談的對話識別碼之後，您就可以使用 JavaScript 交談用戶端程式庫來取得交談對話用戶端： 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient` 可用來列出交談對話中的成員、取得聊天記錄，以及傳送訊息。  

## <a name="send-and-receive-messages"></a>傳送及接收訊息  

使用 `SendMessage` 將訊息傳送至會議交談。 在接收內送訊息方面，則不支援訂閱 `chatMessageReceived` 之類的事件，因為此案例尚未啟用即時信號。 若要取得最新訊息，您可以輪詢 `ListMessages` API。 在互通性案例中，`ListMessages` API 現在支援傳回三種新的訊息類型：
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

如需訊息類型的詳細資訊，請參閱[這裡](../../../concepts/chat/concepts.md)。 

**附註** - Teams 的互通性案例目前僅支援傳送及接收訊息。 尚未支援其他功能，例如輸入指示器和通訊服務使用者在 Teams 會議中新增或移除其他使用者的功能。  

