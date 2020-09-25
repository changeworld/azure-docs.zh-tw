---
title: Azure 通訊服務中的 SMS 概念
titleSuffix: An Azure Communication Services concept document
description: 了解通訊服務 SMS 概念。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943682"
---
# <a name="sms-concepts"></a>SMS 概念

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure 通訊服務可讓您使用通訊服務 SMS 用戶端程式庫來傳送和接收 SMS 文字訊息。 這些用戶端程式庫可以用來支援客戶服務案例、約會提醒、雙因素驗證，以及其他即時通訊需求。 通訊服務 SMS 可讓您可靠地傳送訊息，同時公開有關行銷活動的傳遞能力和回應速率深入解析。

Azure 通訊服務 SMS 用戶端程式庫的主要功能包括：

-  **簡單**設定體驗，可將 SMS 功能新增至您的應用程式。
- **高速**訊息支援，透過免付費電話號碼為美國的 A2P (應用程式對人員) 使用案例提供。
- **雙向**交談，以支援客戶支援、警示和約會提醒等案例。
- **可靠的傳遞**，具備從您的應用程式所傳送訊息的即時傳遞報告。
- **分析**以追蹤您的使用模式和客戶參與。
- **退出**處理支援以自動偵測並尊重退出免付費電話號碼的選擇。 通訊服務會偵測 STOP 和 START 訊息，並將下列預設回應傳送給終端使用者： 
  - STOP - *「您已成功取消訂閱此號碼的訊息。回覆 START 以重新訂閱。」*
  - START - *「您已成功重新訂閱此號碼的訊息。回覆 STOP 以取消訂閱。」*
  - STOP 和 START 訊息會回覆給您。 Azure 通訊服務鼓勵您監視並實作這些退出，以確保不會有任何其他訊息傳送給已選擇退出通訊的收件者。


## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始傳送 SMS](../../quickstarts/telephony-sms/send.md)

您可能會對下列文件感興趣：

- 熟悉 [SMS 用戶端程式庫](../telephony-sms/sdk-features.md)
- 取得支援 SMS 的[電話號碼](../../quickstarts/telephony-sms/get-phone-number.md)
- [規劃您的 SMS 解決方案](../telephony-sms/plan-solution.md)
