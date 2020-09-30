---
title: 通話 (語音/視訊) 和聊天的定價案例
titleSuffix: An Azure Communication Services concept document
description: 了解通訊服務定價模型。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0b82d7dc8f6aaaa28a5293966440f058fbf42dc1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460929"
---
# <a name="pricing-scenarios"></a>定價案例

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Azure 通訊服務的價格是以隨用隨付模型為基礎，無預付費用。 您只需支付耗用量和使用服務的費用。

## <a name="voicevideo-calling-and-screen-sharing"></a>語音/視訊通話和螢幕畫面分享

Azure 通訊服務可讓您將語音/視訊通話和螢幕畫面分享新增至應用程式。 您可以使用 JavaScript、Objective-C (Apple)、Java (Android) 或 .NET 用戶端程式庫，將體驗內嵌至您的應用程式。 請參閱我們的[可用用戶端程式庫的完整清單](./sdk-options.md)。

### <a name="pricing"></a>定價

通話和螢幕畫面分享服務的計費方式為每個參與者每分鐘 $0.004，以進行群組通話。 若要了解各種可能的通話流程，請參閱[此頁面](./call-flows.md)。

通話的每個參與者都會在其連線到通話的每分鐘計費。 無論使用者是視訊通話、語音通話或螢幕畫面分享，都是如此。

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>價格範例：使用 JS 和 iOS 用戶端程式庫來分組音訊/視訊通話

Alice 與同事 Bob 和 Charlie 進行群組通話。 Alice 和 Bob 使用 JS 用戶端程式庫，Charlie 使用 iOS 用戶端程式庫。

- 通話總共持續 60 分鐘。
- Alice 和 Bob 參與了整個通話。 Alice 開啟視訊五分鐘，並且分享螢幕畫面 23 分鐘。 Bob 在整個通話 (60 分鐘) 都開啟視訊，並且分享螢幕畫面 12 分鐘。
- Charlie 在 43 分鐘之後離開通話。 Charlie 在參與的期間 (43 分鐘) 使用了音訊和視訊。

**成本計算**

- 2 位參與者 x 60 分鐘 x 每個參與者每分鐘 $0.004 = $0.48 [視訊和音訊都是以相同的費率收費]
- 1 位參與者 x 43 分鐘 x 每個參與者每分鐘 $0.004 = $0.172 [視訊和音訊都是以相同的費率收費]

**群組通話的總成本**：$0.48 + $0.172 = $0.652

## <a name="chat"></a>聊天

透過通訊服務，您可以透過在 2 個以上的使用者之間傳送和接收聊天訊息的功能來增強應用程式。 聊天用戶端程式庫適用於 JavaScript、.NET、Python 和 JAVA。 請參閱[此頁面以了解用戶端程式庫](./sdk-options.md)

### <a name="price"></a>價格

- 每個傳送的聊天訊息都會向您收取 $0.0008。

### <a name="pricing-example-chat-between-two-users"></a>價格範例：兩個使用者之間的聊天 

Geeta 開始與 Emily 的聊天對話，分享更新並且傳送 5 則訊息。 聊天持續 10 分鐘，期間 Geeta 和 Emily 各自傳送 15 則訊息。

**成本計算** 
- 已傳送的訊息數目 (5 + 15 + 15) x $0.0008 = $0.028

### <a name="pricing-example-group-chat-with-multiple-users"></a>價格範例：具有多個使用者的群組聊天 

Charlie 開始與他的朋友 Casey 和 Jasmine 的聊天對話，規劃假期。 他們通話好一陣子，期間 Charlie、Casey 和 Jasmine 分別傳送了 20、30 和 18 則訊息。 他們發現他們的朋友 Rose 可能有興趣參加旅行，所以將她新增至聊天對話，並且與她分享所有訊息歷程記錄。 

Rose 看到訊息並且開始聊天。 此時 Casey 有來電，因此決定稍後再跟上交談。 Charlie、Jasmine 和 Rose 決定旅行的日期，並且分別傳送其他 30、25、35 則訊息。

**成本計算** 

- 已傳送的訊息數目 (20 + 30 + 18 + 30 + 25 + 35) x $0.0008 = $0.1264
