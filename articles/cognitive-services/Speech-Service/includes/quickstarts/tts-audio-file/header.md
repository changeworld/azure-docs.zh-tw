---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400276"
---
在本快速入門中，您將使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，將文字轉換為音訊檔案中的合成語音。 在[文字轉換語音語言支援](../../../language-support.md#text-to-speech)底下，文字轉換語音服務提供了許多合成語音的選項。 在滿足幾個必要條件之後，將語音合成為檔案只需要採取五個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 `SpeechConfig` 物件。
> * 建立指定 .WAV 檔案名稱的音訊組態物件。
> * 使用上述的設定物件建立 `SpeechSynthesizer` 物件。
> * 使用 `SpeechSynthesizer` 物件，將您的文字轉換為合成的語音，並將它儲存到指定的音訊檔案。
> * 檢查對於錯誤傳回的 `SpeechSynthesizer`。
