---
title: 容器儲存機制和映射
services: cognitive-services
author: aahill
manager: nitinme
description: 兩個數據表，代表所有認知服務供應專案的容器登錄、存放庫和映射名稱。
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 3f2611c9535b9721cccadf35e56bdd21a3020257
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677454"
---
### <a name="container-repositories-and-images"></a>容器儲存機制和映射

下表列出 Azure 認知服務所提供的可用容器映射。 如需所有可用容器映射名稱及其可用標記的完整清單，請參閱 [認知服務容器映射標記](../container-image-tags.md)。 

#### <a name="generally-available"></a>正式推出 

Microsoft Container Registry (MCR) syndicates 所有正式推出的認知服務容器。 您也可以直接從 [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)使用容器。

**LUIS**

| 容器 | Container Registry/存放庫/映射名稱 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

如需詳細資訊，請參閱 [如何執行和安裝 LUIS 容器](../../LUIS/luis-container-howto.md) 。

**文字分析**

| 容器 | Container Registry/存放庫/映射名稱 |
|--|--|
| 情感分析 v3 (英文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| 情感分析 v3 (西班牙文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| 情感分析 v3 (法文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| 情感分析 v3 (義大利文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| 情感分析 v3 (德文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| 情感分析 v3 (簡體中文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| 情感分析 v3 (繁體中文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| 情感分析 v3 (日文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| 情感分析 v3 (葡萄牙文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| 情感分析 v3 (荷蘭)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

如需詳細資訊，請參閱 [如何執行和安裝文字分析容器](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) 。

**異常偵測器** 

| 容器 | Container Registry/存放庫/映射名稱 |
|--|--|
| 異常偵測器 | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

如需詳細資訊，請參閱 [如何執行和安裝異常](../../anomaly-detector/anomaly-detector-container-howto.md) 偵測器容器。

**語音服務**

> [!NOTE]
> 若要使用語音容器，您將需要完成 [線上要求表單](https://aka.ms/csgate)。

| 容器 | Container Registry/存放庫/映射名稱 |
|--|--|
| [語音轉文字](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [自訂語音轉換文字](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [文字轉換語音](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"Ungated" 預覽 

以下是公開提供的預覽容器。 Microsoft Container Registry (MCR) syndicates 認知服務的所有公開可用 ungated 容器。 您也可以直接從 [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)使用容器。

| Service | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 關鍵片語擷取 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 語言偵測 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>「閘道」預覽

先前，閘道預覽容器裝載于存放 `containerpreview.azurecr.io` 庫。 自2020年9月22日起，這些容器 (除了健康情況) 的文字分析之外，還裝載于 Microsoft Container Registry (MCR) ，且不需要使用 docker login 命令就能下載。 若要使用容器，您將需要：

1. 使用您的 Azure 訂用帳戶識別碼和使用者案例完成 [要求表單](https://aka.ms/csgate) 。 
2. 核准時，從 MCR 下載容器。 
3. 使用來自適當 Azure 資源的金鑰和端點來驗證執行時間的容器。 

| Service | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [電腦視覺](../../Computer-vision/computer-vision-how-to-install-containers.md) | 閱讀文章2。0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [電腦視覺](../../Computer-vision/computer-vision-how-to-install-containers.md) | 讀取3.1 版 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | 空間分析 | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=ctts) | 自訂文字轉換語音 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=lid) | 語言偵測 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [語音服務 API](../../speech-service/speech-container-howto.md?tab=ntts) | 神經文字轉換語音 | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [健全狀況的文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | 健康情況的文字分析 | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

