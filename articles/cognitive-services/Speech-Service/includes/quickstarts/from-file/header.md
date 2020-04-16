---
title: 快速入門：辨識來自音訊檔案的語音 - 語音服務
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400588"
---
在本快速入門中，您將使用[語言 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 辨識音訊檔案中的語音。 在滿足幾個必要條件之後，辨識檔案中的語音只需要幾個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 `SpeechConfig` 物件。
> * 建立指定 .WAV 檔案名稱的 `AudioConfig` 物件。
> * 使用上面的 `SpeechConfig` 和 `AudioConfig` 物件建立 `SpeechRecognizer` 物件。
> * 使用 `SpeechRecognizer` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 `SpeechRecognitionResult`。
