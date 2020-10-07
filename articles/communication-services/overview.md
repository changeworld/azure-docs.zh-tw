---
title: 什麼是 Azure 通訊服務？
description: 瞭解 Azure 通訊服務如何協助您利用即時通訊來開發豐富的使用者體驗。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1c79ffb02df4a64b2c506ef0863d9bb2da06db53
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91303364"
---
# <a name="what-is-azure-communication-services"></a>什麼是 Azure 通訊服務？

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Azure 通訊服務可讓您輕鬆地將即時多媒體語音、影片、IP 電話通訊功能加入您的應用程式。 Azure 通訊服務的用戶端程式庫也可讓您在通訊解決方案中新增聊天和 SMS 功能。

<br>

> [!VIDEO https://www.youtube.com/embed/49oshhgY6UQ]

<br>
<br>

您可以在各種情況下，使用 Azure 通訊服務來進行語音、影片、文字、資料通訊：

- 瀏覽器對瀏覽器、瀏覽器與應用程式、應用程式對應用程式的通訊
- 與聊天機器人或其他服務互動的使用者
- 透過公用切換電話語音網路進行互動的使用者和聊天機器人

也支援混合的案例。 例如，Azure 通訊服務應用程式可能同時有使用者分別從瀏覽器和傳統電話語音裝置說話。 Azure 通訊服務也可以與 Azure Bot Service 結合，建立 Bot 驅動的互動式語音回應 (IVR) 系統。

## <a name="common-scenarios"></a>常見案例

如果您不熟悉 Azure 通訊服務，下列資源是很好的起點：
<br>

| 資源                               |描述                           |
|---                                    |---                                   |
|**[建立通訊服務資源](./quickstarts/create-communication-resource.md)**|您可以使用 Azure 入口網站或通訊服務的系統管理用戶端程式庫來佈建您的第一個通訊服務資源，以開始使用 Azure 通訊服務。 一旦您有了「通訊服務」資源的連接字串，您就可以佈建您的第一個使用者存取權杖。|
|**[建立您的第一個使用者存取權杖](./quickstarts/access-tokens.md)**|使用者存取權杖是用來對您的 Azure 通訊服務資源驗證您的服務。 需使用通訊服務的系統管理用戶端程式庫來佈建和重新核發這些權杖。|
|**[取得手機號碼](./quickstarts/telephony-sms/get-phone-number.md)**|您可以使用 Azure 通訊服務來佈建及發行電話號碼。 這些電話號碼可用來起始外撥電話，並建立 SMS 通訊解決方案。|
|**[從您的應用程式傳送 SMS](./quickstarts/telephony-sms/send.md)**|Azure 通訊服務的 SMS 用戶端程式庫可讓您從 .NET 和 JavaScript 應用程式傳送和接收 SMS 訊息。|
|**[開始使用語音和視訊通話](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure 通訊服務可讓您使用通話用戶端程式庫，將語音和視訊通話加入您的應用程式。 此程式庫是由 WebRTC 提供，可讓您在應用程式中建立對等、多媒體、即時的通訊。|
|**[開始聊天](./quickstarts/chat/get-started.md)**|您可以使用 Azure 通訊服務的聊天用戶端程式庫，將即時聊天整合到您的應用程式。|


## <a name="samples"></a>範例

下列範例示範 Azure 通訊服務用戶端程式庫的端對端使用。 您可以隨意使用這些範例來開始您自己的通訊服務解決方案。
<br>

| 範例名稱                               | 描述                           |
|---                                    |---                                   |
|**[群組通話 Hero 範例](./samples/calling-hero-sample.md)**|瞭解如何使用 Azure 通訊服務的用戶端程式庫來建立群組通話體驗。|
|**[群組聊天 Hero 範例](./samples/chat-hero-sample.md)**|瞭解如何使用 Azure 通訊服務的用戶端程式庫來建立群組聊天體驗。|


## <a name="platforms-and-client-libraries"></a>平台和用戶端程式庫

下列資源可協助您瞭解 Azure 通訊服務的用戶端程式庫：

| 資源                               | 描述                           |
|---                                    |---                                   |
|**[用戶端程式庫和 REST API](./concepts/sdk-options.md)**|Azure 通訊服務功能在概念上分成六個區塊，每個都是由用戶端程式庫代表。 您可以根據您的即時通訊需求，決定要使用的用戶端程式庫。|
|**[通話用戶端程式庫概觀](./concepts/voice-video-calling/calling-sdk-features.md)**|請參閱通訊服務通話用戶端程式庫概觀。|
|**[聊天用戶端程式庫概觀](./concepts/chat/sdk-features.md)**|請參閱通訊服務聊天用戶端程式庫概觀。|
|**[SMS 用戶端程式庫概觀](./concepts/telephony-sms/sdk-features.md)**|請參閱通訊服務 SMS 用戶端程式庫概觀。|

## <a name="compare-azure-communication-services"></a>Azure 通訊服務的比較

您可以考慮使用另外兩個 Microsoft 通訊產品，它們目前與 Azure 通訊服務沒有直接互通：

 - [Microsoft Graph 雲端通訊 API](https://docs.microsoft.com/graph/cloud-communications-concept-overview) 可讓組織建立的通訊體驗繫系結至具有 M365 授權的 Azure Active Directory 使用者。 這適合用在繫結至 Azure Active Directory 的應用程式，或用於您想要在 Microsoft Teams 中擴充生產力體驗之處。 另外還有一些 API 可在 [Teams 體驗](https://docs.microsoft.com/microsoftteams/platform/?view=msteams-client-js-latest&preserve-view=true)中建立應用程式和自訂。

 - [Azure PlayFab Party](https://docs.microsoft.com/gaming/playfab/features/multiplayer/networking/) 簡化了在遊戲中加入低延遲聊天和資料通訊的程序。 雖然您可以使用通訊服務來增強遊戲的聊天和社交系統，但 PlayFab 是一種量身打造的選擇，在 Xbox 上免費提供。


## <a name="next-steps"></a>後續步驟

 - [建立通訊服務資源](./quickstarts/create-communication-resource.md)
