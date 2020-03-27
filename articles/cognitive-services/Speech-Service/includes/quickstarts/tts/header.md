---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76961533"
---
在本快速入門中，您將使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，將文字轉換為合成語音。 在[文字轉換語音語言支援](../../../language-support.md#text-to-speech)底下，文字轉換語音服務提供了許多合成語音的選項。 滿足幾個必要條件之後，將合成的語音轉譯為預設說話者只需要四個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 `SpeechConfig` 物件。
> * 使用上面的 `SpeechSynthesizer` 物件來建立 `SpeechConfig` 物件。
> * 使用 `SpeechSynthesizer` 物件讀出文字。
> * 檢查對於錯誤傳回的 `SpeechSynthesisResult`。
