---
title: Azure 通訊服務中的聊天概念
titleSuffix: An Azure Communication Services concept document
description: 了解通訊服務聊天概念。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: af07894fcbfae386849d32492be9d2718a3adcc3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943770"
---
# <a name="chat-concepts"></a>聊天概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

您可以使用 Azure 通訊服務聊天用戶端程式庫，將即時文字聊天新增至您的應用程式。 此頁面將摘要說明重要的聊天概念和功能。

如需特定用戶端程式庫語言和功能的詳細資訊，請參閱[通訊服務聊天的用戶端程式庫概觀](./sdk-features.md)。

## <a name="chat-overview"></a>聊天概觀 

聊天會在對話中進行。 聊天對話可以包含許多訊息和許多使用者。 每則訊息都屬於單一對話，而使用者可以加入一個或多個對話。 

聊天對話中的每個使用者都稱為成員。 一個聊天對話中最多可以有 250 個成員。 只有聊天對話成員可以在對話中傳送和接收訊息或新增/移除成員。 允許的訊息大小上限大約是 28KB。 通訊服務會儲存聊天記錄，直到您在聊天對話上執行刪除作業為止。 您可以使用 `List/Get Messages` 作業來擷取聊天對話中的所有訊息。

如果聊天對話中的成員超過 20 位，則會停用讀取回條和輸入指示器功能。 

## <a name="chat-architecture"></a>聊天架構

聊天架構有兩個核心部分：1) 信任的服務和 2) 用戶端應用程式。

:::image type="content" source="../../media/chat-architecture.png" alt-text="顯示通訊服務聊天架構的圖表。":::

 - **信任的服務：** 若要正確管理聊天會話，您需要可協助您使用資源連接字串來連線到通訊服務的服務。 此服務負責建立聊天對話、管理對話成員資格，以及提供存取權杖給使用者。 如需存取權杖的詳細資訊，請參閱我們的[存取權杖](../../quickstarts/access-tokens.md)快速入門。

 - **用戶端應用程式：** 用戶端應用程式會連線到您信任的服務，並接收用來直接連線到通訊服務的存取權杖。 建立此連線之後，您的用戶端應用程式就可以開始傳送和接收訊息。
    
## <a name="message-types"></a>訊息類型

通訊服務聊天會共用使用者及系統產生的訊息，這稱為**對話活動**。 當聊天對話更新時，就會產生對話活動。 當您在聊天對話上呼叫 `List Messages` 或 `Get Messages` 時，結果會包含使用者產生的文字訊息及系統訊息 (依時間順序排列)。 這可協助您識別何時新增或移除成員，或何時更新了聊天對話主題。 支援的訊息類型為：  

 - `Text`:使用者在聊天對話中撰寫及傳送的實際訊息。 
 - `ThreadActivity/AddMember`:指出已將一個或多個成員新增至聊天對話的系統訊息。 例如：

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`:指出已從聊天對話中移除成員的系統訊息。 例如：

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`:指出主題已更新的系統訊息。 例如：

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>即時信號 

聊天 JavaScript 用戶端程式庫包含即時信號。 這可讓用戶端接聽聊天對話的即時更新和傳入訊息，而不需要輪詢 API。 可用的事件包括：

 - `ChatMessageReceived` - 當新訊息傳送至使用者所屬的聊天對話時。 在上一個主題中所討論的自動產生系統訊息中，不會傳送此事件。  
 - `ChatMessageEdited` - 在使用者所屬的聊天對話中編輯訊息時。 
 - `ChatMessageDeleted` - 當使用者所屬的聊天對話中有訊息遭到刪除時。 
 - `TypingIndicatorReceived` - 當另一個成員在使用者所屬的聊天對話中輸入訊息時。 
 - `ReadReceiptReceived` - 當另一個成員已讀取使用者在聊天對話中傳送的訊息時。 

## <a name="chat-events"></a>聊天事件 

即時信號功能可讓您的使用者進行即時聊天。 您的服務可以使用 Azure 事件方格來訂閱聊天相關事件。 如需詳細資訊，請參閱[事件處理概念](../event-handling.md)。

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>使用認知服務與聊天用戶端程式庫來啟用智慧型功能

您可以使用 [Azure 認知 API](https://docs.microsoft.com/azure/cognitive-services/) 與聊天用戶端程式庫，將智慧型功能新增至您的應用程式。 例如，您可以：

- 讓使用者能夠以不同的語言互相聊天。 
- 藉由偵測客戶所傳入問題的負面情緒，協助支援專員安排票證的優先順序。
- 分析傳入訊息以進行重點偵測和實體辨識，並根據訊息內容在應用程式中提示使用者相關資訊。

達成此目標的其中一種方式是讓信任的服務作為聊天對話的成員。 假設您想要啟用語言翻譯。 這項服務會負責接聽其他成員 [1] 所交換的訊息、呼叫認知 API 以將內容翻譯為所需的語言 [2、3]，並以訊息的形式將翻譯的結果傳送到聊天對話中 [4]。 

如此一來，訊息記錄就會同時包含原始和翻譯的訊息。 在用戶端應用程式中，您可以新增邏輯來顯示原始或翻譯的訊息。 請參閱[本快速入門](https://docs.microsoft.com/azure/cognitive-services/translator/quickstart-translate)，了解如何使用認知 API，將文字翻譯成不同的語言。 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="顯示認知服務如何與通訊服務互動的圖表。":::

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始聊天](../../quickstarts/chat/get-started.md)

您可能會對下列文件感興趣：

- 熟悉[聊天用戶端程式庫](sdk-features.md)
