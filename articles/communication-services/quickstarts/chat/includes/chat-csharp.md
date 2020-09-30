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
ms.openlocfilehash: ecf9575f2ab469c9226ec18a4b3b4647e582d408
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377579"
---
## <a name="prerequisites"></a>必要條件
開始之前，請務必：
- 建立具有有效訂閱的 Azure 帳戶。 如需詳細資訊，請參閱[免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安裝 [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- 建立 Azure 通訊服務資源。 如需詳細資訊，請參閱[建立 Azure 通訊資源](../../create-communication-resource.md)。 您必須在本快速入門中記錄您的資源**端點**。
- [使用者存取權杖](../../access-tokens.md)。 請務必將範圍設定為 "chat"，並記下 token 字串和 userId 字串。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `ChatQuickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：**Program.cs**。

```console
dotnet new console -o ChatQuickstart
```

將您的目錄變更為新建立的應用程式資料夾，然後使用 `dotnet build` 命令來編譯您的應用程式。

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>安裝套件

安裝適用於 .NET 的 Azure 通訊聊天用戶端程式庫

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.1
``` 

## <a name="object-model"></a>物件模型

下列類別會處理 Azure 通訊服務的聊天用戶端程式庫的一些主要 C# 功能。

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 這是聊天功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來建立、取得、刪除對話。 |
| ChatThreadClient | 這是聊天對話功能所需的類別。 您可以透過 ChatClient 取得執行個體，並使用它來傳送/接收/更新/刪除訊息、新增/移除/取得使用者、傳送輸入通知和讀信回條。 |

## <a name="create-a-chat-client"></a>建立聊天用戶端

若要建立聊天用戶端，您將使用您的 Azure 通訊服務端點，以及在必要條件步驟中所產生的存取權杖。 您必須使用 `Administration` 用戶端程式庫中的 `CommunicationIdentityClient` 類別來建立使用者，並核發要傳遞至聊天用戶端的權杖。 深入了解[使用者存取權杖](../../access-tokens.md)。

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>開始聊天對話

使用 `createChatThread` 方法來建立聊天對話。
- 使用 `topic` 來提供對話主題；在使用 `UpdateThread` 函式建立聊天對話之後，可以更新主題。
- 使用 `members` 屬性來傳遞要加入聊天對話的 `ChatThreadMember` 物件清單。 `ChatThreadMember` 物件要以 `CommunicationUser` 物件初始化。 若要取得 `CommunicationUser` 物件，您必須將您依照[建立使用者](../../access-tokens.md#create-a-user)指示所建立的存取識別碼傳遞出來

回應 `chatThreadClient` 是用來在建立的聊天對話上執行作業：將成員新增至聊天對話、傳送訊息、刪除訊息等等。其中的 `Id` 屬性，是聊天對話的唯一識別碼。 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>取得聊天對話用戶端
`GetChatThreadClient` 方法會傳回現有對話的對話用戶端。 其可以用來在建立的對話上執行作業：新增成員、傳送訊息等等。threadId 是現有聊天對話的唯一識別碼。

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = await chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>將訊息傳送至聊天對話

使用 `SendMessage` 方法，將訊息傳送至以 threadId 識別的執行緒。

- 使用 `content` 來提供聊天訊息內容，這是必要的。
- 使用 `priority` 指定訊息優先順序等級，例如 'Normal' 或 'High'，如未指定，則會使用 'Normal'。
- 使用 `senderDisplayName` 指定寄件者的顯示名稱，如未指定，則會使用空白的名稱。

`SendChatMessageResult` 是傳送訊息時的傳回回應，它包含的識別碼就是訊息的唯一識別碼。

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>從聊天對話接收聊天訊息

您可以依指定的間隔，在聊天對話用戶端上輪詢 `GetMessages` 方法，以取出聊天訊息。

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` 會接收選擇性的 `DateTimeOffset` 參數。 如果有指定 offset，您就會收到在它之後接收、更新或刪除的訊息。 請注意，在 offset 時間之前收到，但在此時間之後被編輯或移除的訊息，也一樣會傳回。

`GetMessages` 會傳回訊息的最新版本，包括使用 `UpdateMessage` 和 `DeleteMessage` 對訊息所做的任何編輯或刪除。 若為已刪除的訊息，`chatMessage.DeletedOn` 會傳回 datetime 值，指出刪除該訊息的時間。 若為編輯過的訊息，`chatMessage.EditedOn` 會傳回 datetime 值，指出編輯該訊息的時間。 可以使用 `chatMessage.CreatedOn` 來存取訊息建立的原始時間，且可以用來排序訊息。

`GetMessages` 會傳回不同訊息類型 (可用 `chatMessage.Type` 識別)。 這些類型包括：

- `Text`:由對話成員傳送的一般聊天訊息。

- `ThreadActivity/TopicUpdate`:指出主題已更新的系統訊息。

- `ThreadActivity/AddMember`:指出已將一個或多個成員新增至聊天對話的系統訊息。

- `ThreadActivity/DeleteMember`:指出已從聊天對話中移除成員的系統訊息。

如需詳細資訊，請參閱[訊息類型](../../../concepts/chat/concepts.md#message-types)。

## <a name="update-a-message"></a>更新訊息

您可以藉由叫用 `ChatThreadClient`上的 `UpdateMessage`，來更新已傳送的訊息。

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>刪除訊息

您可以藉由叫用 `ChatThreadClient`上的 `DeleteMessage` 來刪除訊息。

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>將使用者新增為聊天對話的成員

對話一旦建立，您就可以在其中新增和移除使用者。 新增使用者也會同時授與他們存取權，使他們能夠將訊息傳送至對話，以及新增/移除其他成員。 在呼叫 `AddMembers` 之前，請確定您已取得該使用者的新存取權杖和身分識別。 使用者將需要該存取權杖，才能初始化其聊天用戶端。

使用 `AddMembers` 方法，將對話成員新增至以 threadId 識別的對話。

 - 使用 `members` 列出要新增至聊天對話的成員；
 - 必要的 `User` 是您取得的此新使用者的身分識別。
 - (選擇性) `DisplayName` 是對話成員的顯示名稱。
 - (選擇性) `ShareHistoryTime` 是與成員共用聊天記錄的時間。 若要在聊天對話一開始就共用記錄，請將其設為 DateTime.MinValue。 若要在新增成員時不共用先前的記錄，請將其設定為目前的時間。 若要共用部分記錄，請將其設為對話建立與目前時間之間的時間點。

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>從聊天對話中移除使用者

類似將使用者新增至對話，您可以從聊天對話中移除使用者。 若要這麼做，您必須追蹤您所新增成員的身分識別 (CommunicationUser)。

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>執行程式碼

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```console
dotnet run
```
