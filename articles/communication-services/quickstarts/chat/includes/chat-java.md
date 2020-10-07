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
ms.openlocfilehash: 1675ad802f3a117c26ee10218f3b3bedad9b0a00
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779778"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 第 8 版或更新版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 已部署的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../../create-communication-resource.md)。
- [使用者存取權杖](../../access-tokens.md)。 請務必將範圍設定為 "chat"，並記下 token 字串和 userId 字串。


## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>建立新的 Java 應用程式

開啟您的終端機或命令視窗，然後瀏覽至您想要在其中建立 JAVA 應用程式的目錄。 執行下列命令，以從 maven-archetype-quickstart 範本產生 JAVA 專案。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

您會發現 'generate' 目標建立了與 artifactId 同名的目錄。 在此目錄下，`src/main/java directory` 包含專案原始程式碼，`src/test/java` 目錄包含測試來源，而 pom.xml 檔案是專案的「專案物件模型」(簡稱 [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html))。

將應用程式的 POM 檔案更新為使用 JAVA 8 或更高版本：

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>新增聊天用戶端程式庫的套件參考

在您的 POM 檔案中，參考聊天 API 的 `azure-communication-chat` 套件：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

若要進行驗證，您的用戶端必須參考 `azure-communication-common` 套件：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>

```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務的聊天用戶端程式庫的一些主要 JAVA 功能。

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 這是聊天功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來建立、取得、刪除對話。 |
| ChatAsyncClient | 這是非同步聊天功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來建立、取得、刪除對話。 |
| ChatThreadClient | 這是聊天的對話功能所需的類別。 您可以透過 ChatClient 取得執行個體，並使用它來傳送/接收/更新/刪除訊息、新增/移除/取得使用者、傳送輸入通知和讀信回條。 |
| ChatThreadAsyncClient | 這是非同步聊天的對話功能所需的類別。 您可以透過 ChatAsyncClient 取得執行個體，並使用它來傳送/接收/更新/刪除訊息、新增/移除/取得使用者、傳送輸入通知和讀信回條。 |

## <a name="create-a-chat-client"></a>建立聊天用戶端
若要建立聊天用戶端，您將使用 Azure 通訊服務端點，以及在必要條件步驟中所產生的存取權杖。 使用者存取權杖可讓您建立直接向 Azure 通訊服務進行驗證的用戶端應用程式。 一旦您在伺服器上產生這些權杖，請將其傳回給用戶端裝置。 您必須使用一般用戶端程式庫中的 CommunicationUserCredential 類別，將權杖傳遞給您的聊天用戶端。 

新增匯入陳述式時，請務必只能從com.azure.communication.chat 和 com.azure.communication.chat.models 命名空間新增匯入，不可從 com.azure.communication.chat.implementation 命名空間。 在透過 Maven 所產生的 App.java 檔案中，您可以從下列程式碼開始：

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>開始聊天對話

使用 `createChatThread` 方法來建立聊天對話。
`createChatThreadOptions` 是用來描述對話要求。

- 使用 `topic` 來提供對話主題；在使用 `UpdateThread` 函式建立聊天對話之後，可以更新主題。
- 使用 `members` 列出要新增至對話的對話成員。 `ChatThreadMember` 會取得您在[使用者存取權杖](../../access-tokens.md)快速入門中建立的使用者。

回應 `chatThreadClient` 是用來在建立的聊天對話上執行作業：將成員新增至聊天對話、傳送訊息、刪除訊息等等。其中的 `chatThreadId` 屬性，是聊天對話的唯一識別碼。 此屬性可由公用方法 .getChatThreadId() 取得。

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>將訊息傳送至聊天對話

使用 `sendMessage` 方法將訊息傳送至您剛才建立的對話 (以 chatThreadId 識別)。
`sendChatMessageOptions` 是用來描述聊天訊息要求。

- 使用 `content` 來提供聊天的訊息內容。
- 使用 `priority` 指定聊天的訊息優先順序等級，例如「一般」或「高」。這個屬性可用來在應用程式中當做收件者使用者的 UI 指標，以將注意力帶到訊息或執行自訂商務邏輯。
- 使用 `senderDisplayName` 指定寄件者的顯示名稱。

回應 `sendChatMessageResult` 中的 `id`，是訊息的唯一識別碼。

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>取得聊天對話用戶端

`getChatThreadClient` 方法會傳回現有對話的對話用戶端。 其可以用來在建立的對話上執行作業：新增成員、傳送訊息等等。`chatThreadId` 是現有聊天對話的唯一識別碼。

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>從聊天對話接收聊天訊息

您可以依指定的間隔，在聊天對話用戶端上輪詢 `listMessages` 方法，以取出聊天訊息。

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` 會傳回訊息的最新版本，包括使用 .editMessage() 和 .deleteMessage() 對訊息所做的任何編輯或刪除。 若為已刪除的訊息，`chatMessage.getDeletedOn()` 會傳回 datetime 值，指出刪除該訊息的時間。 若為編輯過的訊息，`chatMessage.getEditedOn()` 會傳回 datetime 值，指出編輯該訊息的時間。 可以使用 `chatMessage.getCreatedOn()` 來存取訊息建立的原始時間，且可以用來排序訊息。

`listMessages` 會傳回不同訊息類型 (可用 `chatMessage.getType()` 識別)。 這些類型包括：

- `Text`:由對話成員傳送的一般聊天訊息。

- `ThreadActivity/TopicUpdate`:指出主題已更新的系統訊息。

- `ThreadActivity/AddMember`:指出已將一個或多個成員新增至聊天對話的系統訊息。

- `ThreadActivity/DeleteMember`:指出已從聊天對話中移除成員的系統訊息。

如需詳細資訊，請參閱[訊息類型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>將使用者新增為聊天對話的成員

聊天對話一旦建立，您就可以在其中新增和移除使用者。 新增使用者也會同時授與他們存取權，使他們可以將訊息傳送至聊天對話，以及新增/移除其他成員。 您必須從取得該使用者的新存取權杖和身分識別開始。 在呼叫 addMembers 方法之前，請確定您已取得該使用者的新存取權杖和身分識別。 使用者將需要該存取權杖，才能初始化其聊天用戶端。

使用 `addMembers` 方法，將對話成員新增至以 threadId 識別的對話。

- 使用 `members` 列出要新增至聊天對話的成員。
- 必要的 `user` 是您在[使用者存取權杖](../../access-tokens.md)快速入門中，使用 CommunicationIdentityClient 建立的 CommunicationUser。
- (選擇性) `display_name` 是對話成員的顯示名稱。
- (選擇性) `share_history_time` 是與成員共用聊天記錄的時間。 若要在聊天對話一開始就共用記錄，請將此屬性設定為等於或小於對話建立時間的任何日期。 若要在新增成員時不共用先前的記錄，請將其設定為目前的日期。 若要共用部分記錄，請將其設定為所需的日期。

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>從聊天對話中移除使用者

類似將使用者新增至對話，您可以從聊天對話中移除使用者。 若要這麼做，您必須追蹤您所新增成員的使用者身分識別。

使用 `removeMember`，其中 `user` 是您已建立的 CommunicationUser。

```Java
chatThreadClient.removeMember(user);
```

## <a name="run-the-code"></a>執行程式碼

瀏覽至包含 *pom.xml* 檔案的目錄，然後使用下列 `mvn` 命令來編譯專案。

```console
mvn compile
```

接著，建置封裝。

```console
mvn package
```

執行下列 `mvn` 命令以執行應用程式。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
