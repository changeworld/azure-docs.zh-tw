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
ms.openlocfilehash: 3c05596e16edc5243b8a97002a5cc5990c69ec43
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943839"
---
## <a name="prerequisites"></a>必要條件
開始之前，請務必：

- 建立具有有效訂閱的 Azure 帳戶。 如需詳細資訊，請參閱[免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安裝 [Python](https://www.python.org/downloads/)
- 建立 Azure 通訊服務資源。 如需詳細資訊，請參閱[建立 Azure 通訊資源](../../create-communication-resource.md)。 您必須在本快速入門中記錄您的資源**端點**
- [使用者存取權杖](../../access-tokens.md)。 請務必將範圍設定為 "chat"，並記下 token 字串和 userId 字串。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir chat-quickstart && cd chat-quickstart
```

使用文字編輯器，在專案根目錄中建立名為 **start-chat.py** 的檔案，然後新增程式的結構，包括基本例外狀況處理。 您會在下列各節中，將本快速入門的所有原始程式碼新增至此檔案。

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>安裝用戶端程式庫

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務聊天用戶端程式庫的一些主要 Python 功能。

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 這是聊天功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來建立、取得、刪除對話。 |
| ChatThreadClient | 這是聊天對話功能所需的類別。 您可以透過 ChatClient 取得執行個體，並使用它來傳送/接收/更新/刪除訊息、新增/移除/取得使用者、傳送輸入通知和讀信回條。 |

## <a name="create-a-chat-client"></a>建立聊天用戶端

若要建立聊天用戶端，您將使用通訊服務端點，以及在必要條件步驟中所產生的 `Access Token`。 深入了解[使用者存取權杖](../../access-tokens.md)。

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>開始聊天對話

使用 `create_chat_thread` 方法來建立聊天對話。

- 使用 `topic` 來提供對話主題；在使用 `update_thread` 函式建立聊天對話之後，可以更新主題。
- 使用 `members` 列出要新增至聊天對話的 `ChatThreadMember`，`ChatThreadMember` 會採用 `user` 作為 `CommunicationUser` 類型，這是您透過[建立使用者](../../access-tokens.md#create-a-user)來建立的結果

`chat_thread_client` 回應會用來在新建的聊天對話上執行作業，例如將成員加入聊天對話、傳送訊息、刪除訊息等等。其中的 `thread_id` 屬性，是聊天對話的唯一識別碼。

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>取得聊天對話用戶端
get_chat_thread_client 方法會傳回現有對話的對話用戶端。 其可以用來在建立的對話上執行作業：新增成員、傳送訊息等等。thread_id 是現有聊天對話的唯一識別碼。

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>將訊息傳送至聊天對話

使用 `send_message` 方法將訊息傳送至您剛才建立的聊天對話 (以 threadId 來識別)。

- 使用 `content` 來提供聊天訊息內容；
- 使用 `priority` 指定訊息優先順序等級，例如「一般」或「高」。這個屬性可用來在應用程式中當做收件者使用者的 UI 指標，以將注意力帶到訊息或執行自訂商務邏輯。
- 使用 `senderDisplayName` 指定傳送者的顯示名稱；

`SendChatMessageResult` 回應會包含「識別碼」，這是該訊息的唯一識別碼。

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>從聊天對話接收聊天訊息

您可以依指定的間隔，透過輪詢 `list_messages` 方法來擷取聊天訊息。

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` 會傳回訊息的最新版本，包括使用 `update_message` 和 `delete_message` 對訊息所做的任何編輯或刪除。 若為已刪除的訊息，`ChatMessage.deleted_on` 會傳回 datetime 值，指出刪除該訊息的時間。 若為編輯過的訊息，`ChatMessage.edited_on` 會傳回 datetime 值，指出編輯該訊息的時間。 用來排序訊息的 `ChatMessage.created_on` 可用來存取訊息建立的原始時間。

`list_messages` 會傳回不同訊息類型 (可用 `ChatMessage.type` 識別)。 這些類型包括：

- `Text`:由對話成員傳送的一般聊天訊息。

- `ThreadActivity/TopicUpdate`:指出主題已更新的系統訊息。

- `ThreadActivity/AddMember`:指出已將一個或多個成員新增至聊天對話的系統訊息。

- `ThreadActivity/DeleteMember`:指出已從聊天對話中移除成員的系統訊息。

如需詳細資訊，請參閱[訊息類型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>將使用者新增為聊天對話的成員

聊天對話一旦建立，您就可以在其中新增和移除使用者。 新增使用者也會同時授與他們存取權，使他們可以將訊息傳送至聊天對話，以及新增/移除其他成員。 在呼叫 `add_members` 方法之前，請確定您已取得該使用者的新存取權杖和身分識別。 使用者將需要該存取權杖，才能初始化其聊天用戶端。

使用 `add_members` 方法，將對話成員新增至以 threadId 識別的對話。

- 使用 `members` 列出要新增至聊天對話的成員；
- (必要) `user` 是您在[建立使用者](../../access-tokens.md#create-a-user)時，透過 `CommunicationIdentityClient` 所建立的 `CommunicationUser`
- (選擇性) `display_name` 是對話成員的顯示名稱。
- (選擇性) `share_history_time` 是與成員共用聊天記錄的時間。 若要在聊天對話一開始就共用記錄，請將此屬性設定為等於或小於對話建立時間的任何日期。 若要在新增成員時不共用先前的記錄，請將其設定為目前的日期。 若要共用部分記錄，請將其設定為中間的日期。

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>從聊天對話中移除使用者

類似於新增成員，您也可以從對話中移除成員。 若要移除，您必須追蹤已加入成員的識別碼。

使用 `remove_member` 方法，從以 threadId 識別的對話中移除對話成員。
- `user` 是要從對話中移除的 CommunicationUser。

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>執行程式碼

使用 `python` 命令從您的應用程式目錄執行應用程式。

```console
python start-chat.py
```
