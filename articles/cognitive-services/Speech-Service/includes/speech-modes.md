---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422044"
---
## <a name="speech-modes"></a>語音模式

**互動**
- 用於命令和控制方案。
- 分段超時值為 X。
- 在一個可識別的話語結束時,服務將停止處理來自該請求 ID 的音訊並結束轉彎。 連接未關閉。
- 識別的最大限制為 20s。
- 呼叫的典型碳呼叫是`RecognizeOnceAsync`。

**談話**
- 用於較長的運行識別。
- 分段超時值為 Y.(Y != X)
- 將處理多個完整的話語,而不會結束回合。
- 會因為太多的沉默而結束。
- 碳將繼續與新的請求 ID 和根據需要重播音訊。
- 在語音辨識 10 分鐘后,該服務將強制斷開。
- 碳將重新連接並重播未確認的音訊。
- 呼叫在碳與`StartContinuousRecognition`。

**聽寫**
- 允許用戶通過說出標點符號來指定標點符號。
- 通過在物件上指定`EnableDictation`啟動識別`SpeechConfig`的 API 調用在碳中調用。
- <sup>第</sup>1 方`speech.fragment`群集傳回消息以進行中間結果`speech.hypothesis`,<sup>第</sup>3 方返回消息。