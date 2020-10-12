---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422044"
---
## <a name="speech-modes"></a>語音模式

**互動式**
- 適用于命令和控制案例。
- 有 X 的分割超時值。
- 在一個可辨識的語句結束時，服務會停止處理該要求識別碼的音訊，並結束回合。 連接未關閉。
- 辨識的最大限制為20s。
- 調用的一般碳呼叫為 `RecognizeOnceAsync` 。

**談話**
- 適用于長時間執行的表彰。
- 具有 Y 的分割超時值。 (Y！ = X) 
- 將會處理多個完整語句，而不結束回合。
- 會結束回合太多無回應。
- 碳將繼續使用新的要求識別碼，並視需要重新執行音訊。
- 服務會在語音辨識的10分鐘之後強制中斷連線。
- 碳會重新連接並重新執行未確認的音訊。
- 以碳的方式叫用 `StartContinuousRecognition` 。

**聽寫**
- 允許使用者透過說話來指定標點符號。
- 藉由在 `EnableDictation` 物件上指定， `SpeechConfig` 而不論開始辨識的 API 呼叫為何。
- 1個<sup>st</sup>合作物件叢集會傳回 `speech.fragment` 中繼結果的訊息，<sup>rd</sup>而協力廠商會傳回 `speech.hypothesis` 訊息。