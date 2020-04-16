---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422313"
---
在本快速入門中，您將使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 和 Language Understanding (LUIS)服務來辨識從麥克風擷取之音訊資料的意圖。 具體而言，您將使用語音 SDK 來擷取語音，並使用 LUIS 中預先建置的網域來識別家庭自動化的意圖，例如開燈和關燈。 

在滿足幾個必要條件之後，只需執行幾個步驟，就可以從麥克風辨識語音和識別意圖：

> [!div class="checklist"]
>
> * 從您的訂用帳戶金鑰和區域建立 `SpeechConfig` 物件。
> * 使用上面的 `SpeechConfig` 物件來建立 `IntentRecognizer` 物件。
> * 使用 `IntentRecognizer` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 `IntentRecognitionResult`。
