---
title: 快速入門：翻譯語音轉換文字 - 語音服務
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
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74981107"
---
在本快速入門中，您將會使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以互動方式來將某一種語言的語音翻譯成另一種語言的文字。 在滿足幾個必要條件之後，翻譯語音轉換文字只需要採取五個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechConfig```` 物件。
> * 更新 ````SpeechConfig```` 物件以指定來源和目標語言。
> * 使用上面的 ````SpeechConfig```` 物件來建立 ````TranslationRecognizer```` 物件。
> * 使用 ````TranslationRecognizer```` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 ````TranslationRecognitionResult````。
