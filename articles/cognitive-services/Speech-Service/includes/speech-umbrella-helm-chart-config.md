---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細說明語音傘式掌舵圖配置選項。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874330"
---
### <a name="speech-umbrella-chart"></a>演講(傘圖)

頂級「保護傘」圖表中的值將覆蓋相應的子圖表值。 因此,應在此處添加所有本地自定義值。

|參數|描述|預設|
| -- | -- | -- | -- |
| `speechToText.enabled` | 是否啟用**語音到文字**服務。 | `true` |
| `speechToText.verification.enabled` | 是否啟用`helm test`**語音到文字**服務的功能。 | `true` |
| `speechToText.verification.image.registry` | `helm test`測試**語音到文字**服務的 docker 映像儲存庫。 Helm 在群集內創建單獨的窗格以進行測試,並從該註冊表中拉出*測試使用*映射。 | `docker.io` |
| `speechToText.verification.image.repository` | `helm test`測試**語音到文字**服務的 docker 映像儲存庫。 Helm 測試窗格使用此存儲庫來拉*取測試使用*映射。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 用於`helm test`**語音到文字**服務的 docker 映像標記。 Helm 測試窗格使用此標記來拉*取測試使用*圖像。 | `latest` |
| `speechToText.verification.image.pullByHash` | *測試使用*Docker 映像是否由哈希拉。 如果`true``speechToText.verification.image.hash`應添加 ,應添加有效的圖像哈希值。 | `false` |
| `speechToText.verification.image.arguments` | 用於執行*測試使用*docker 映像的參數。 Helm 測試窗格在運行`helm test`時 將這些參數傳遞給容器。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | 是否啟用**文字到語音轉換**服務。 | `true` |
| `textToSpeech.verification.enabled` | 是否啟用`helm test`**語音到文字**服務的功能。 | `true` |
| `textToSpeech.verification.image.registry` | `helm test`測試**語音到文字**服務的 docker 映像儲存庫。 Helm 在群集內創建單獨的窗格以進行測試,並從該註冊表中拉出*測試使用*映射。 | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test`測試**語音到文字**服務的 docker 映像儲存庫。 Helm 測試窗格使用此存儲庫來拉*取測試使用*映射。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 用於`helm test`**語音到文字**服務的 docker 映像標記。 Helm 測試窗格使用此標記來拉*取測試使用*圖像。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | *測試使用*Docker 映像是否由哈希拉。 如果`true``textToSpeech.verification.image.hash`應添加 ,應添加有效的圖像哈希值。 | `false` |
| `textToSpeech.verification.image.arguments` | 使用*測試使用*docker 映像執行的參數。 在運行時`helm test`,helm 測試窗格將這些參數傳遞給容器。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |