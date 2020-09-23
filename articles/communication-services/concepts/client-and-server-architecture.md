---
title: 用戶端和伺服器架構
titleSuffix: An Azure Communication Services concept document
description: 瞭解通訊服務的架構。
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 38d6b131c972b4fd890af53624fdd6a36b3ecb6c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934039"
---
# <a name="client-and-server-architecture"></a>用戶端和伺服器架構

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

每個 Azure 通訊服務應用程式都會有使用**服務**的**用戶端應用程式**，以促進人員對人員的連線能力。 此頁面說明各種案例中的常見架構元素。

## <a name="user-access-management"></a>使用者存取管理

Azure 通訊服務用戶端程式庫需要 `user access tokens` 安全地存取通訊服務資源。 `User access tokens` 由於權杖的敏感性本質和產生它們所需的連接字串，因此應該由受信任的服務產生和管理。 如果無法正確管理存取權杖，可能會因為誤用資源而導致額外的費用。 強烈建議您利用受信任的服務來進行使用者管理。 受信任的服務將會產生權杖，並使用適當的加密將它們傳遞回用戶端。 您可以在下面找到範例架構流程：

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="顯示使用者存取權杖架構的圖表。":::

如需其他資訊，請參閱 [最佳身分識別管理作法](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)

## <a name="browser-communication"></a>瀏覽器通訊

Azure 通訊 JavaScript 用戶端程式庫可讓 web 應用程式使用豐富的文字、語音和影片互動。 應用程式會透過用戶端程式庫直接與 Azure 通訊服務互動，以存取資料平面並提供即時文字、語音和影片通訊。 您可以在下面找到範例架構流程：

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="顯示瀏覽器的通訊服務瀏覽器架構圖表。":::

## <a name="native-app-communication"></a>原生應用程式通訊

許多案例都是原生應用程式的最佳服務。 Azure 通訊服務支援瀏覽器對應用程式和應用程式對應用程式之間的通訊。  當您建立原生應用程式體驗時，擁有推播通知可讓使用者在應用程式未執行時接收呼叫。 Azure 通訊服務可讓您輕鬆地將推播通知整合至 Google Firebase、Apple Push Notification Service 和 Windows 推播通知。 您可以在下面找到範例架構流程：

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="此圖顯示原生應用程式通訊的通訊服務架構。":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>透過公用切換電話語音網路的語音和 SMS (PSTN) 

透過電話系統進行通訊，可以大幅提高應用程式的範圍。 為了支援 PSTN 語音和 SMS 案例，Azure 通訊服務可協助您直接從 Azure 入口網站 [取得電話號碼](../quickstarts/telephony-sms/get-phone-number.md) ，或使用 REST api 和用戶端程式庫。 一旦取得電話號碼，就可以在輸入和輸出案例中，用來觸及使用 PSTN 呼叫和 SMS 的客戶。 您可以在下面找到範例架構流程：

> [!Note]
> 在公開預覽期間，提供美國電話號碼給美國和加拿大境內帳單位址的客戶。 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="顯示通訊服務 PSTN 架構的圖表。":::

如需有關 PSTN 和 SMS 解決方案的詳細資訊，請參閱 [規劃 pstn 和 sms 解決方案](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>人們與 bot 和其他服務通訊

Azure 通訊服務可透過文字和語音通道支援人類對系統的通訊，並提供可直接存取 Azure 通訊服務資料平面的服務。 例如，您可以讓 bot 接聽撥入電話或參與網路聊天。 Azure 通訊服務提供用戶端程式庫，可讓這些案例進行呼叫和聊天。 您可以在下面找到範例架構流程：

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="顯示通訊服務 Bot 架構的圖表。":::

## <a name="networking"></a>網路

您可能想要在使用者之間交換任意資料，例如，同步處理共用的混合現實或遊戲體驗。 用於文字、語音和影片通訊的即時資料平面，可直接透過兩種方式提供給您：

- 呼叫**用戶端程式庫**-呼叫中的裝置可以存取 api，以便透過呼叫通道來傳送和接收資料。 這是將資料通訊新增至現有互動的最簡單方式。
- **STUN/turn** -Azure 通訊服務會讓符合標準的 STUN 和服務可供您使用。 這可讓您在這些標準化基本專案之上建立高度自訂的傳輸層。 您可以撰寫自己符合標準的用戶端，或使用開放原始碼程式庫（例如 [WinRTC](https://github.com/microsoft/winrtc)）。

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [建立使用者存取權杖](../quickstarts/access-tokens.md)

如需詳細資訊，請參閱下列文章：

- 瞭解 [驗證](../concepts/authentication.md)
- 瞭解 [PSTN 和 SMS 解決方案](../concepts/telephony-sms/plan-solution.md)

- [將聊天新增至您的應用程式](../quickstarts/chat/get-started.md)
- [將語音電話新增至您的應用程式](../quickstarts/voice-video-calling/getting-started-with-calling.md)
