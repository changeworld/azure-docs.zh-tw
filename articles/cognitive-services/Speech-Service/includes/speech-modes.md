---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422044"
---
## <a name="speech-modes"></a>語音模式

**Interactive (互動式)**
- 適用于命令和控制項案例。
- 具有 X 的分割時間輸出值。
- 在一個可辨識的語句結束時，服務會停止從該要求識別碼處理音訊並結束回合。 連接未關閉。
- 辨識的最大限制為 .20s。
- 對叫用的一般碳`RecognizeOnceAsync`呼叫是。

**轉換**
- 適用于長時間執行的辨識。
- 具有 Y 的分割時間輸出值（Y！ = X）
- 會處理多個完整的語句，而不會結束回合。
- 會結束太多無聲的回合。
- 碳將會以新的要求識別碼繼續，並視需要重新執行音訊。
- 服務會在經過10分鐘的語音辨識後，強制中斷連接。
- 碳會重新連線並重新執行未認可的音訊。
- 以碳中的`StartContinuousRecognition`叫用。

**聽寫**
- 可讓使用者藉由說話來指定標點符號。
- 在`EnableDictation` `SpeechConfig`物件上指定，而不論開始辨識的 API 呼叫為何，在碳中叫用。
- 1<sup>st</sup>合作物件叢集會`speech.fragment`傳回中繼結果的訊息，而<sup>第三</sup>方`speech.hypothesis`則傳回訊息。