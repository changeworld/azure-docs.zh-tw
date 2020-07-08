---
title: Azure 服務匯流排主題篩選 | Microsoft Docs
description: 本文說明「訂閱者」如何藉由指定篩選器來定義想要從主題接收的訊息。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: b722c040248c199782f6c8dea020ae582762e102
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85339971"
---
# <a name="topic-filters-and-actions"></a>主題篩選和動作

訂閱者可以定義他們想要接收某個主題的哪些訊息。 這些訊息會以一或多個具名訂用帳戶規則的形式來指定。 每條規則都由一個選取特定訊息的條件，以及一個為選定訊息加上註解的動作所組成。 對於每個比對規則條件，訂用帳戶會產生一份能針對每條比對規則加上不同註解的訊息。

每個新建立的主題訂用帳戶都有最初的預設訂用帳戶規則。 如果您未明確指定規則的篩選條件，套用的篩選是讓所有訊息都能選入訂用帳戶的 **true** 篩選。 預設規則沒有任何關聯的註解動作。

服務匯流排支援三個篩選條件：

-   布林篩選** - **TrueFilter** 和 **FalseFilter** 能讓您針對訂用帳戶選取所有抵達的訊息 (**true**)，或所有抵達的訊息都不選取 (**false**)。

-   SQL 篩選** - **SqlFilter** 保留類似 SQL 的條件運算式，系統會在訊息代理程式中根據抵達訊息的使用者定義屬性和系統屬性加以評估。 條件運算式中的所有系統屬性都必須加上 `sys.` 前置詞。 [篩選準則的 SQL 語言子集](service-bus-messaging-sql-filter.md)會測試屬性是否存在（ `EXISTS` ）、null 值（ `IS NULL` ）、邏輯 NOT/AND/OR、關係運算子、簡單數值算術，以及與相符的簡單文字模式 `LIKE` 。

-   相互關聯篩選** - **CorrelationFilter** 能保留一組條件，比對抵達訊息中一或多個使用者或系統屬性。 常見的用法是比對**CorrelationId**屬性，但應用程式也可以選擇比對下列屬性：

    - **ContentType**
     - **標籤**
     - **Id**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **若要**
     - 任何使用者定義的屬性。 
     
     當抵達訊息的某個屬性值等於在相互關聯篩選中指定的值時，代表一個相符項目。 字串運算式的比較會區分大小寫。 指定多個比對屬性時，篩選會將它們結合為邏輯 AND 條件，表示所有條件必須相符才算是篩選出相符項目。

所有篩選都會評估訊息屬性。 篩選準則無法評估訊息本文。

複雜的篩選規則需要處理容量。 特別是，使用 SQL 篩選規則會導致訂用帳戶、主題和命名空間層級的整體訊息輸送量降低。 可能的話，應用程式應該選擇類似 SQL 的相互關聯篩選，因為它們的處理效率較高，而且對輸送量的影響較小。

## <a name="actions"></a>動作

使用 SQL 篩選條件時，您可以定義動作來透過新增、移除或取代屬性和屬性值為訊息加入註解。 動作[使用類似 SQL 的運算式](service-bus-messaging-sql-filter.md)，其大致上仰賴 SQL UPDATE 陳述式語法。 動作會在訊息已比對之後，以及在將訊息選取到訂用帳戶之前完成。 訊息屬性的變更僅涉及複製到訂用帳戶中的訊息。

## <a name="usage-patterns"></a>使用模式

最簡單的主題使用案例是每個訂用帳戶都讓每則訊息傳送一份到主題，實現廣播模式。

篩選和動作進一步實現兩組模式：分割和路由。

透過可預測和互斥的方式，分割能使用篩選將訊息散發到數個現有的主題訂用帳戶。 對於功能相同且每個都容納整體資料中某個子集的區間，當使用者為了處理其中許多不同的內容而向外延展系統時，便會使用分割模式。 透過分割，發行者不需要了解分割模型就能將訊息提交到主題。 接著，系統會將訊息移動到正確的訂用帳戶，供分割的訊息處理常式擷取。

路由能透過可預測但不一定排斥的方式，使用篩選將訊息散發到主體訂用帳戶。 搭配[自動轉送](service-bus-auto-forwarding.md)功能，主題篩選能用來在服務匯流排命名空間內建立複雜的路由圖表，於 Azure 區域中散發訊息。 有了 Azure Functions 或 Azure Logic Apps 作為 Azure 服務匯流排命名空間之間的橋樑，您可以透過與企業營運應用程式的直接整合建立複雜的全域拓撲。


> [!NOTE]
> 因為 Azure 入口網站現在支援服務匯流排 Explorer 功能，所以可以從入口網站建立或編輯訂用帳戶篩選器。 

## <a name="next-steps"></a>後續步驟
請參閱下列範例： 

- [.NET-包含篩選的基本傳送和接收教學課程](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-主題篩選](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager 範本](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


