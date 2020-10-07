---
title: Azure 通訊服務中的語音和視訊概念
titleSuffix: An Azure Communication Services concept document
description: 了解通訊服務通話類型。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 97a90284ad8d195751eb5dd90675822d00243ea8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665203"
---
# <a name="voice-and-video-concepts"></a>語音和視訊概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

您可以使用 Azure 通訊服務來進行及接收語音和視訊通話、分析通話中繼資料、記錄通話，甚至是建置通話樹狀結構。 您的通話可以對其他連線網際網路的裝置和單純電話執行。 您可以使用通訊服務 JavaScript、Android 或 iOS 用戶端程式庫來建置應用程式，讓您的使用者可以在私人對話或群組討論中彼此交談。 Azure 通訊服務支援服務或 Bot 之間的通話。

## <a name="call-types-in-azure-communication-services"></a>Azure 通訊服務中的通話類型

您可以在 Azure 通訊服務中進行多種類型的通話。 您所執行的通話類型會決定您的訊號結構描述、媒體流量和定價模型。

### <a name="voice-over-ip-voip"></a>Voice Over IP (VoIP) 

當您的應用程式使用者透過網際網路或資料連線呼叫您應用程式的其他使用者時，會透過 Voice Over IP (VoIP) 進行通話。 在此情況下，訊號和媒體會透過網際網路傳送。

### <a name="public-switched-telephone-network-pstn"></a>公用交換電話網路 (PSTN)

當您的使用者與傳統電話號碼互動時，通話會由 PSTN (公用交換電話網路) 語音電話來輔助進行。 若要進行和接收 PSTN 通話，您需要將電話語音功能新增至您的 Azure 通訊服務資源。 在此情況下，訊號和媒體會使用以 IP 為基礎和以 PSTN 為基礎的技術組合來連線您的使用者。

### <a name="one-to-one-call"></a>一對一通話

當您的其中一個使用者使用我們的其中一個用戶端程式庫與另一個使用者連線時，就會在 Azure 通訊服務上進行一對一通話。 通話可以是 VoIP 或 PSTN。

### <a name="group-call"></a>群組通話

當三個或多個參與者彼此連線時，就會在 Azure 通訊服務上進行群組通話。 VoIP 與 PSTN 連線使用者的任何組合都可以出現在群組通話上。 您可以藉由將更多參與者新增至通話中，將一對一通話轉換成群組通話。 其中一個參與者可以是 Bot。

### <a name="supported-video-standards"></a>支援的視訊標準
我們支援 H.264 (MPEG-4) 

### <a name="video-quality"></a>視訊品質
我們在原生 (iOS、Android) SDK 上支援 Full HD 1080p。 針對 Web (JS) SDK，我們支援標準 Standard HD 720p。 品質取決於可用的頻寬。  

### <a name="rooms-concept"></a>會議室概念
會議室是一組 API 和 SDK，可讓您輕鬆地將音訊、視訊、螢幕畫面分享、PSTN 和 SMS 互動新增至您的網站或原生應用程式。
在預覽期間，您可以使用群組識別碼來加入相同的交談。 您可以視需要建立任意數量的群組識別碼，並依據「會議室」來分隔使用者。 未來將會引進更多關於「會議室」的控制項 

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始使用通話](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

如需詳細資訊，請參閱下列文章：
- 熟悉一般[通話流程](../call-flows.md)
- [規劃您的 PSTN 解決方案](../telephony-sms/plan-solution.md)
- 了解[通話用戶端程式庫功能](../voice-video-calling/calling-sdk-features.md)
