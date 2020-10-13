---
title: Azure 通訊服務中的 SMS 概念
titleSuffix: An Azure Communication Services concept document
description: 了解通訊服務 SMS 概念。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 35398d60008ac52ba16dca0a0201f8c2f2101a0f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758552"
---
# <a name="sms-concepts"></a>SMS 概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure 通訊服務可讓您使用通訊服務 SMS 用戶端程式庫來傳送和接收 SMS 文字訊息。 這些用戶端程式庫可以用來支援客戶服務案例、約會提醒、雙因素驗證，以及其他即時通訊需求。 通訊服務 SMS 可讓您可靠地傳送訊息，同時公開有關行銷活動的傳遞能力和回應速率深入解析。

Azure 通訊服務 SMS 用戶端程式庫的主要功能包括：

-  **簡單**設定體驗，可將 SMS 功能新增至您的應用程式。
- **高速**訊息支援，透過免付費電話號碼為美國的 A2P (應用程式對人員) 使用案例提供。
- **雙向**交談，以支援客戶支援、警示和約會提醒等案例。
- **可靠的傳遞**，具備從您的應用程式所傳送訊息的即時傳遞報告。
- **分析**以追蹤您的使用模式和客戶參與。
- **退出**處理支援以自動偵測並尊重退出免付費電話號碼的選擇。 退出美國免付費號碼是由美國電訊廠商規定並強制執行。
  - STOP - 如果文字簡訊收件者想要退出，他們可以將 ‘STOP’ 傳送到免付費電話號碼。 電訊廠商會傳送 STOP 的下列預設回應： *「網路訊息：您已使用 "stop" 一字來回覆，這會封鎖從這個號碼傳送的所有文字。回傳 "unstop" 以重新接收訊息。」*
  - START/UNSTOP - 如果收件者想要重新訂閱來自免付費號碼的文字簡訊，他們可以將 ‘START’ 或 ‘UNSTOP’ 傳送至免付費電話號碼。 電訊廠商會傳送 START/UNSTOP 的下列預設回應： *「網路訊息：您已回復 “unstop”，並將從這個號碼再次開始接收訊息。」*
  - Azure 通訊服務會偵測到 STOP 訊息，並封鎖收件者的所有進一步訊息。 傳遞報告會指出傳遞失敗，狀態訊息為「寄件者已封鎖指定的收件者」。
  - STOP、UNSTOP 和 START 訊息會回覆給您。 Azure 通訊服務鼓勵您監視並實作這些退出，以確保不會針對已選擇退出通訊的收件者進行任何進一步的訊息傳送嘗試。


## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始傳送 SMS](../../quickstarts/telephony-sms/send.md)

您可能會對下列文件感興趣：

- 熟悉 [SMS 用戶端程式庫](../telephony-sms/sdk-features.md)
- 取得支援 SMS 的[電話號碼](../../quickstarts/telephony-sms/get-phone-number.md)
- [規劃您的 SMS 解決方案](../telephony-sms/plan-solution.md)
