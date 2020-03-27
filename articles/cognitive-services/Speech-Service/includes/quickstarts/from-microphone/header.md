---
title: 快速入門：從麥克風辨識語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75467777"
---
在本快速入門中，您會使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，以互動方式辨識來自麥克風輸入的語音，以及從擷取的音訊取得文字轉譯。 您可以輕鬆地將此功能整合到您的應用程式或裝置中，以便進行一般辨識工作，例如轉譯交談。 此功能也可用來進行更複雜的整合，例如使用 Bot Framework 搭配語音 SDK 來建置語音助理。

在滿足幾個必要條件之後，從麥克風辨識語音只需採取幾個步驟：

> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 `SpeechConfig` 物件。
> * 使用上面的 `SpeechConfig` 物件來建立 `SpeechRecognizer` 物件。
> * 使用 `SpeechRecognizer` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 `SpeechRecognitionResult`。
