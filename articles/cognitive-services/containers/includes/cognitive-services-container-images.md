---
title: 容器存放庫和映射
services: cognitive-services
author: aahill
manager: nitinme
description: 兩個數據表代表所有認知服務供應專案的容器登錄、存放庫和映射名稱。
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876811"
---
### <a name="container-repositories-and-images"></a>容器存放庫和映射

下表列出 Azure 認知服務所提供的可用容器映射。 如需所有可用容器映射名稱及其可用標記的完整清單，請參閱[認知服務容器映射](../container-image-tags.md)標籤。 目前沒有任何認知服務的容器正式推出（GA）。 就時間而言，在進行進一步的公告之前，容器可以是*公用 Ungated*或*公開閘道預覽*。

 - *公用 Ungated*：容器可公開使用，而不需要控制機制。
 - *公用網關預覽*：容器可公開使用，但首先需要存取容器登錄的正式要求。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>公用 "Ungated" （container registry： `mcr.microsoft.com`）

Microsoft Container Registry （MCR）會 syndicates 所有公開可用的 "ungated" 容器以進行認知服務。 您也可以直接從[Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)取得容器。

| Service | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 關鍵片語擷取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 語言偵測 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 情感分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>公用「閘道」預覽（container registry `containerpreview.azurecr.io`：）

容器預覽登錄會裝載認知服務的所有公開可用「閘道」容器。 這些容器需要正式要求，才能透過其容器登錄來存取它們。

| Service | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [異常偵測器](../../anomaly-detector/anomaly-detector-container-howto.md) | 異常偵測器 | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [電腦視覺](../../Computer-vision/computer-vision-how-to-install-containers.md) | 讀取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [臉部](../../face/face-how-to-install-containers.md) | 臉部 | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [表單辨識器](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | 表單辨識器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=stt) | 語音轉文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=cstt) | 自訂語音轉換文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=tts) | 文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=ctts) | 自訂文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
