---
title: 快速入門：翻譯語音轉換語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981199"
---
在此快速入門中，您將會使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 來以互動方式將某一種語言的語音翻譯成另一種語言的語音。 在滿足幾個先決條件之後，翻譯語音轉換語音只需要採取六個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechTranslationConfig```` 物件。
> * 更新 ````SpeechTranslationConfig```` 物件以指定來源和目標語言。
> * 更新 ````SpeechTranslationConfig```` 物件以指定語音輸出語音名稱。
> * 使用上面的 ````SpeechTranslationConfig```` 物件來建立 ````TranslationRecognizer```` 物件。
> * 使用 ````TranslationRecognizer```` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 ````TranslationRecognitionResult````。
