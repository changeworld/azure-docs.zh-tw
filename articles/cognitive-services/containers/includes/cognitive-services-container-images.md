---
title: 容器儲存機制和映射
services: cognitive-services
author: aahill
manager: nitinme
description: 兩個數據表，代表所有認知服務供應專案的容器登錄、存放庫和映射名稱。
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: ff039d6d5879e036aecc63b46359d84673f84a0e
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424630"
---
### <a name="container-repositories-and-images"></a>容器儲存機制和映射

下表列出 Azure 認知服務所提供的可用容器映射。 如需所有可用容器映射名稱及其可用標記的完整清單，請參閱 [認知服務容器映射標記](../container-image-tags.md)。 

#### <a name="generally-available"></a>正式推出 

Microsoft Container Registry (MCR) syndicates 所有正式推出的認知服務容器。 您也可以直接從 [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)使用容器。

#### <a name="luis"></a>[LUIS](#tab/luis)

| LUIS 容器 | Container Registry/存放庫/映射名稱 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

如需詳細資訊，請參閱 [如何執行和安裝 LUIS 容器](../../LUIS/luis-container-howto.md) 。

#### <a name="text-analytics"></a>[文字分析](#tab/text-analytics)

| 文字分析容器 | Container Registry/存放庫/映射名稱 |
|--|--|
| 情感分析 v3 (英文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| 情感分析 v3 (西班牙文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| 情感分析 v3 (法文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| 情感分析 v3 (義大利文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| 情感分析 v3 (德文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| 情感分析 v3 (簡體中文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| 情感分析 v3 (繁體中文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| 情感分析 v3 (日文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| 情感分析 v3 (葡萄牙文)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| 情感分析 v3 (荷蘭)  | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

如需詳細資訊，請參閱 [如何執行和安裝文字分析容器](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) 。

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>公用 "Ungated" 預覽版 (container registry： `mcr.microsoft.com`) 

以下是公開提供的預覽容器。 Microsoft Container Registry (MCR) syndicates 認知服務的所有公開可用 ungated 容器。 您也可以直接從 [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)使用容器。

| 服務 | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 關鍵片語擷取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 語言偵測 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [異常偵測器](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>公用「閘道」預覽版 (container registry： `containerpreview.azurecr.io`) 

下列閘道預覽容器會裝載在容器預覽版登錄上，而且需要應用程式才能存取。 如需詳細資訊，請參閱下列容器文章。

| 服務 | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [電腦視覺](../../Computer-vision/computer-vision-how-to-install-containers.md) | 讀取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [臉部](../../face/face-how-to-install-containers.md) | 臉部 | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [表單辨識器](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | 表單辨識器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=stt) | 語音轉文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=cstt) | 自訂語音轉換文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=tts) | 文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=ctts) | 自訂文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=ntts) | 神經文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-neural-text-to-speech` |
| [健全狀況的文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | 健康情況的文字分析 | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
