---
title: 快速入門：建立自訂語音助理 - 語音服務
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241863"
---
在本快速入門中，您將使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 建立自訂語音助理應用程式，以連線至您已撰寫並設定的 Bot。 如果您需要建立 Bot，請參閱[相關的教學課程](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md)，以取得更完整的指南。

符合幾項必要條件後，只需執行幾個步驟即可連線至您的自訂語音助理：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 `BotFrameworkConfig` 物件。
> * 使用上面的 `BotFrameworkConfig` 物件來建立 `DialogServiceConnector` 物件。
> * 使用 `DialogServiceConnector` 物件，開始對單一語句進行聽取程序。
> * 檢查所傳回的 `ActivityReceivedEventArgs`。
