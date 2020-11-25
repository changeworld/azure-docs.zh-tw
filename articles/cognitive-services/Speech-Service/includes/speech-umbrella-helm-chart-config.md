---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細說明語音傘 helm 圖表設定選項。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002259"
---
### <a name="speech-umbrella-chart"></a>語音 (傘圖) 

最上層「傘」圖表中的值會覆寫對應的子圖表值。 因此，您應該在這裡新增所有的內部部署自訂值。

|參數|描述|預設|
| -- | -- | -- | -- |
| `speechToText.enabled` | 是否啟用 **語音轉換文字** 服務。 | `true` |
| `speechToText.verification.enabled` | `helm test`**語音轉換文字** 服務的功能是否已啟用。 | `true` |
| `speechToText.verification.image.registry` | `helm test`用來測試 **語音轉換文字** 服務的 docker 映射存放庫。 Helm 會在叢集內建立不同的 pod 以進行測試，並從這個登錄中提取 *測試用* 映射。 | `docker.io` |
| `speechToText.verification.image.repository` | `helm test`用來測試 **語音轉換文字** 服務的 docker 映射存放庫。 Helm test pod 會使用此儲存機制來提取 *測試-使用* 映射。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 用於 `helm test` **語音轉換文字** 服務的 docker 映射標記。 Helm test pod 會使用此標記來提取 *測試-使用* 映射。 | `latest` |
| `speechToText.verification.image.pullByHash` | 是否要使用雜湊來提取 *測試使用* docker 映射。 如果 `true` `speechToText.verification.image.hash` 應該加入，則具有有效的影像雜湊值。 | `false` |
| `speechToText.verification.image.arguments` | 用來執行 *測試-使用* docker 映射的引數。 Helm test pod 會在執行時將這些引數傳遞至容器 `helm test` 。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | **文字轉換語音** 服務是否已啟用。 | `true` |
| `textToSpeech.verification.enabled` | `helm test`**語音轉換文字** 服務的功能是否已啟用。 | `true` |
| `textToSpeech.verification.image.registry` | `helm test`用來測試 **語音轉換文字** 服務的 docker 映射存放庫。 Helm 會在叢集內建立不同的 pod 以進行測試，並從這個登錄中提取 *測試用* 映射。 | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test`用來測試 **語音轉換文字** 服務的 docker 映射存放庫。 Helm test pod 會使用此儲存機制來提取 *測試-使用* 映射。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 用於 `helm test` **語音轉換文字** 服務的 docker 映射標記。 Helm test pod 會使用此標記來提取 *測試-使用* 映射。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | 是否要使用雜湊來提取 *測試使用* docker 映射。 如果 `true` `textToSpeech.verification.image.hash` 應該加入，則具有有效的影像雜湊值。 | `false` |
| `textToSpeech.verification.image.arguments` | 要搭配 *使用測試* 的 docker 映射執行的引數。 Helm test pod 會在執行時將這些引數傳遞至容器 `helm test` 。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |