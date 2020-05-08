---
title: 認知服務容器映像標籤
titleSuffix: Azure Cognitive Services
description: 所有認知服務容器映射標記的完整清單。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 850d7b9732ad547d502b042e7eaad5d3b471030e
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82925995"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure 認知服務容器映射標籤

Azure 認知服務提供許多容器映射。 容器登錄和對應的存放庫會因容器映射而有所不同。 每個容器映射名稱都會提供多個標記。 容器映射標記是將容器映射進行版本控制的機制。 本文旨在用來列出所有認知服務容器映射及其可用標記的完整參考。

> [!TIP]
> 使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)時，請密切注意容器登錄、存放庫、容器映射名稱和對應標記的大小寫，因為它們會區分**大小**寫。

## <a name="anomaly-detector"></a>異常偵測器

您[Anomaly Detector][ad-containers]可以在`containerpreview.azurecr.io` Container registry 上找到異常偵測器容器映射。 它位於存放`microsoft`庫中，且名`cognitive-services-anomaly-detector`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>電腦視覺

您[Computer Vision][cv-containers]可以在`containerpreview.azurecr.io`容器登錄中找到電腦視覺的容器映射。 它位於存放`microsoft`庫中，且名`cognitive-services-read`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-read`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>臉部

您[Face][fa-containers]可以在`containerpreview.azurecr.io`容器登錄中找到臉部容器映射。 它位於存放`microsoft`庫中，且名`cognitive-services-face`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-face`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>表單辨識器

您可以在`containerpreview.azurecr.io` container registry 上找到[表單辨識器][fr-containers]容器映射。 它位於存放`microsoft`庫中，且名`cognitive-services-form-recognizer`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>語言理解 (LUIS)

您[LUIS][lu-containers]可以在`mcr.microsoft.com`容器登錄整合中找到 LUIS 容器映射。 它位於存放`azure-cognitive-services`庫中，且名`luis`為。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/luis`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>自訂語音轉換文字

您可以在`containerpreview.azurecr.io`容器登錄中找到[自訂的語音轉換文字][sp-cstt]容器映射。 它位於存放`microsoft`庫中，且名`cognitive-services-custom-speech-to-text`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`。

此容器映射具有下列可用的標記：

| 影像標記            | 備忘錄 |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>自訂文字轉換語音

您可以在`containerpreview.azurecr.io`容器登錄中找到[自訂文字轉換語音][sp-ctts]容器映射。 它位於存放`microsoft`庫中，且名`cognitive-services-custom-text-to-speech`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`。

此容器映射具有下列可用的標記：

| 影像標記            | 備忘錄 |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>語音轉文字

[語音轉換文字][sp-stt]容器映射可在`containerpreview.azurecr.io`容器登錄中找到。 它位於存放`microsoft`庫中，且名`cognitive-services-speech-to-text`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`。

此容器映射具有下列可用的標記：

| 影像標記                  | 備忘錄                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | 具有地區設定的`en-US`容器映射。 |
| `2.2.0-amd64-ar-ae-preview` | 具有地區設定的`ar-AE`容器映射。 |
| `2.2.0-amd64-ar-eg-preview` | 具有地區設定的`ar-EG`容器映射。 |
| `2.2.0-amd64-ar-kw-preview` | 具有地區設定的`ar-KW`容器映射。 |
| `2.2.0-amd64-ar-qa-preview` | 具有地區設定的`ar-QA`容器映射。 |
| `2.2.0-amd64-ar-sa-preview` | 具有地區設定的`ar-SA`容器映射。 |
| `2.2.0-amd64-ca-es-preview` | 具有地區設定的`ca-ES`容器映射。 |
| `2.2.0-amd64-da-dk-preview` | 具有地區設定的`da-DK`容器映射。 |
| `2.2.0-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `2.2.0-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `2.2.0-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `2.2.0-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `2.2.0-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `2.2.0-amd64-en-nz-preview` | 具有地區設定的`en-NZ`容器映射。 |
| `2.2.0-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `2.2.0-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `2.2.0-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `2.2.0-amd64-fi-fi-preview` | 具有地區設定的`fi-FI`容器映射。 |
| `2.2.0-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `2.2.0-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `2.2.0-amd64-gu-in-preview` | 具有地區設定的`gu-IN`容器映射。 |
| `2.2.0-amd64-hi-in-preview` | 具有地區設定的`hi-IN`容器映射。 |
| `2.2.0-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `2.2.0-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `2.2.0-amd64-ko-kr-preview` | 具有地區設定的`ko-KR`容器映射。 |
| `2.2.0-amd64-mr-in-preview` | 具有地區設定的`mr-IN`容器映射。 |
| `2.2.0-amd64-nb-no-preview` | 具有地區設定的`nb-NO`容器映射。 |
| `2.2.0-amd64-nl-nl-preview` | 具有地區設定的`nl-NL`容器映射。 |
| `2.2.0-amd64-pl-pl-preview` | 具有地區設定的`pl-PL`容器映射。 |
| `2.2.0-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `2.2.0-amd64-pt-pt-preview` | 具有地區設定的`pt-PT`容器映射。 |
| `2.2.0-amd64-ru-ru-preview` | 具有地區設定的`ru-RU`容器映射。 |
| `2.2.0-amd64-sv-se-preview` | 具有地區設定的`sv-SE`容器映射。 |
| `2.2.0-amd64-ta-in-preview` | 具有地區設定的`ta-IN`容器映射。 |
| `2.2.0-amd64-te-in-preview` | 具有地區設定的`te-IN`容器映射。 |
| `2.2.0-amd64-th-th-preview` | 具有地區設定的`th-TH`容器映射。 |
| `2.2.0-amd64-tr-tr-preview` | 具有地區設定的`tr-TR`容器映射。 |
| `2.2.0-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `2.2.0-amd64-zh-hk-preview` | 具有地區設定的`zh-HK`容器映射。 |
| `2.2.0-amd64-zh-tw-preview` | 具有地區設定的`zh-TW`容器映射。 |
| `2.1.1-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `2.1.1-amd64-ar-ae-preview` | 具有地區設定的`ar-AE`容器映射。 |
| `2.1.1-amd64-ar-eg-preview` | 具有地區設定的`ar-EG`容器映射。 |
| `2.1.1-amd64-ar-kw-preview` | 具有地區設定的`ar-KW`容器映射。 |
| `2.1.1-amd64-ar-qa-preview` | 具有地區設定的`ar-QA`容器映射。 |
| `2.1.1-amd64-ar-sa-preview` | 具有地區設定的`ar-SA`容器映射。 |
| `2.1.1-amd64-ca-es-preview` | 具有地區設定的`ca-ES`容器映射。 |
| `2.1.1-amd64-da-dk-preview` | 具有地區設定的`da-DK`容器映射。 |
| `2.1.1-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `2.1.1-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `2.1.1-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `2.1.1-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `2.1.1-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `2.1.1-amd64-en-nz-preview` | 具有地區設定的`en-NZ`容器映射。 |
| `2.1.1-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `2.1.1-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `2.1.1-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `2.1.1-amd64-fi-fi-preview` | 具有地區設定的`fi-FI`容器映射。 |
| `2.1.1-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `2.1.1-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `2.1.1-amd64-gu-in-preview` | 具有地區設定的`gu-IN`容器映射。 |
| `2.1.1-amd64-hi-in-preview` | 具有地區設定的`hi-IN`容器映射。 |
| `2.1.1-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `2.1.1-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `2.1.1-amd64-ko-kr-preview` | 具有地區設定的`ko-KR`容器映射。 |
| `2.1.1-amd64-mr-in-preview` | 具有地區設定的`mr-IN`容器映射。 |
| `2.1.1-amd64-nb-no-preview` | 具有地區設定的`nb-NO`容器映射。 |
| `2.1.1-amd64-nl-nl-preview` | 具有地區設定的`nl-NL`容器映射。 |
| `2.1.1-amd64-pl-pl-preview` | 具有地區設定的`pl-PL`容器映射。 |
| `2.1.1-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `2.1.1-amd64-pt-pt-preview` | 具有地區設定的`pt-PT`容器映射。 |
| `2.1.1-amd64-ru-ru-preview` | 具有地區設定的`ru-RU`容器映射。 |
| `2.1.1-amd64-sv-se-preview` | 具有地區設定的`sv-SE`容器映射。 |
| `2.1.1-amd64-ta-in-preview` | 具有地區設定的`ta-IN`容器映射。 |
| `2.1.1-amd64-te-in-preview` | 具有地區設定的`te-IN`容器映射。 |
| `2.1.1-amd64-th-th-preview` | 具有地區設定的`th-TH`容器映射。 |
| `2.1.1-amd64-tr-tr-preview` | 具有地區設定的`tr-TR`容器映射。 |
| `2.1.1-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `2.1.1-amd64-zh-hk-preview` | 具有地區設定的`zh-HK`容器映射。 |
| `2.1.1-amd64-zh-tw-preview` | 具有地區設定的`zh-TW`容器映射。 |
| `2.1.0-amd64-ar-ae-preview` | 具有地區設定的`ar-AE`容器映射。 |
| `2.1.0-amd64-ar-eg-preview` | 具有地區設定的`ar-EG`容器映射。 |
| `2.1.0-amd64-ar-kw-preview` | 具有地區設定的`ar-KW`容器映射。 |
| `2.1.0-amd64-ar-qa-preview` | 具有地區設定的`ar-QA`容器映射。 |
| `2.1.0-amd64-ar-sa-preview` | 具有地區設定的`ar-SA`容器映射。 |
| `2.1.0-amd64-ca-es-preview` | 具有地區設定的`ca-ES`容器映射。 |
| `2.1.0-amd64-da-dk-preview` | 具有地區設定的`da-DK`容器映射。 |
| `2.1.0-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `2.1.0-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `2.1.0-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `2.1.0-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `2.1.0-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `2.1.0-amd64-en-nz-preview` | 具有地區設定的`en-NZ`容器映射。 |
| `2.1.0-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `2.1.0-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `2.1.0-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `2.1.0-amd64-fi-fi-preview` | 具有地區設定的`fi-FI`容器映射。 |
| `2.1.0-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `2.1.0-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `2.1.0-amd64-gu-in-preview` | 具有地區設定的`gu-IN`容器映射。 |
| `2.1.0-amd64-hi-in-preview` | 具有地區設定的`hi-IN`容器映射。 |
| `2.1.0-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `2.1.0-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `2.1.0-amd64-ko-kr-preview` | 具有地區設定的`ko-KR`容器映射。 |
| `2.1.0-amd64-mr-in-preview` | 具有地區設定的`mr-IN`容器映射。 |
| `2.1.0-amd64-nb-no-preview` | 具有地區設定的`nb-NO`容器映射。 |
| `2.1.0-amd64-nl-nl-preview` | 具有地區設定的`nl-NL`容器映射。 |
| `2.1.0-amd64-pl-pl-preview` | 具有地區設定的`pl-PL`容器映射。 |
| `2.1.0-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `2.1.0-amd64-pt-pt-preview` | 具有地區設定的`pt-PT`容器映射。 |
| `2.1.0-amd64-ru-ru-preview` | 具有地區設定的`ru-RU`容器映射。 |
| `2.1.0-amd64-sv-se-preview` | 具有地區設定的`sv-SE`容器映射。 |
| `2.1.0-amd64-ta-in-preview` | 具有地區設定的`ta-IN`容器映射。 |
| `2.1.0-amd64-te-in-preview` | 具有地區設定的`te-IN`容器映射。 |
| `2.1.0-amd64-th-th-preview` | 具有地區設定的`th-TH`容器映射。 |
| `2.1.0-amd64-tr-tr-preview` | 具有地區設定的`tr-TR`容器映射。 |
| `2.1.0-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `2.1.0-amd64-zh-hk-preview` | 具有地區設定的`zh-HK`容器映射。 |
| `2.1.0-amd64-zh-tw-preview` | 具有地區設定的`zh-TW`容器映射。 |
| `2.0.3-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `2.0.2-amd64-ar-ae-preview` | 具有地區設定的`ar-AE`容器映射。 |
| `2.0.2-amd64-ar-eg-preview` | 具有地區設定的`ar-EG`容器映射。 |
| `2.0.2-amd64-ar-kw-preview` | 具有地區設定的`ar-KW`容器映射。 |
| `2.0.2-amd64-ar-qa-preview` | 具有地區設定的`ar-QA`容器映射。 |
| `2.0.2-amd64-ar-sa-preview` | 具有地區設定的`ar-SA`容器映射。 |
| `2.0.2-amd64-ca-es-preview` | 具有地區設定的`ca-ES`容器映射。 |
| `2.0.2-amd64-da-dk-preview` | 具有地區設定的`da-DK`容器映射。 |
| `2.0.2-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `2.0.2-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `2.0.2-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `2.0.2-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `2.0.2-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `2.0.2-amd64-en-nz-preview` | 具有地區設定的`en-NZ`容器映射。 |
| `2.0.2-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `2.0.2-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `2.0.2-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `2.0.2-amd64-fi-fi-preview` | 具有地區設定的`fi-FI`容器映射。 |
| `2.0.2-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `2.0.2-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `2.0.2-amd64-gu-in-preview` | 具有地區設定的`gu-IN`容器映射。 |
| `2.0.2-amd64-hi-in-preview` | 具有地區設定的`hi-IN`容器映射。 |
| `2.0.2-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `2.0.2-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `2.0.2-amd64-ko-kr-preview` | 具有地區設定的`ko-KR`容器映射。 |
| `2.0.2-amd64-mr-in-preview` | 具有地區設定的`mr-IN`容器映射。 |
| `2.0.2-amd64-nb-no-preview` | 具有地區設定的`nb-NO`容器映射。 |
| `2.0.2-amd64-nl-nl-preview` | 具有地區設定的`nl-NL`容器映射。 |
| `2.0.2-amd64-pl-pl-preview` | 具有地區設定的`pl-PL`容器映射。 |
| `2.0.2-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `2.0.2-amd64-pt-pt-preview` | 具有地區設定的`pt-PT`容器映射。 |
| `2.0.2-amd64-ru-ru-preview` | 具有地區設定的`ru-RU`容器映射。 |
| `2.0.2-amd64-sv-se-preview` | 具有地區設定的`sv-SE`容器映射。 |
| `2.0.2-amd64-ta-in-preview` | 具有地區設定的`ta-IN`容器映射。 |
| `2.0.2-amd64-te-in-preview` | 具有地區設定的`te-IN`容器映射。 |
| `2.0.2-amd64-th-th-preview` | 具有地區設定的`th-TH`容器映射。 |
| `2.0.2-amd64-tr-tr-preview` | 具有地區設定的`tr-TR`容器映射。 |
| `2.0.2-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `2.0.2-amd64-zh-hk-preview` | 具有地區設定的`zh-HK`容器映射。 |
| `2.0.2-amd64-zh-tw-preview` | 具有地區設定的`zh-TW`容器映射。 |
| `2.0.1-amd64-ar-ae-preview` | 具有地區設定的`ar-AE`容器映射。 |
| `2.0.1-amd64-ar-eg-preview` | 具有地區設定的`ar-EG`容器映射。 |
| `2.0.1-amd64-ar-kw-preview` | 具有地區設定的`ar-KW`容器映射。 |
| `2.0.1-amd64-ar-qa-preview` | 具有地區設定的`ar-QA`容器映射。 |
| `2.0.1-amd64-ar-sa-preview` | 具有地區設定的`ar-SA`容器映射。 |
| `2.0.1-amd64-ca-es-preview` | 具有地區設定的`ca-ES`容器映射。 |
| `2.0.1-amd64-da-dk-preview` | 具有地區設定的`da-DK`容器映射。 |
| `2.0.1-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `2.0.1-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `2.0.1-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `2.0.1-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `2.0.1-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `2.0.1-amd64-en-nz-preview` | 具有地區設定的`en-NZ`容器映射。 |
| `2.0.1-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `2.0.1-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `2.0.1-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `2.0.1-amd64-fi-fi-preview` | 具有地區設定的`fi-FI`容器映射。 |
| `2.0.1-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `2.0.1-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `2.0.1-amd64-gu-in-preview` | 具有地區設定的`gu-IN`容器映射。 |
| `2.0.1-amd64-hi-in-preview` | 具有地區設定的`hi-IN`容器映射。 |
| `2.0.1-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `2.0.1-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `2.0.1-amd64-ko-kr-preview` | 具有地區設定的`ko-KR`容器映射。 |
| `2.0.1-amd64-mr-in-preview` | 具有地區設定的`mr-IN`容器映射。 |
| `2.0.1-amd64-nb-no-preview` | 具有地區設定的`nb-NO`容器映射。 |
| `2.0.1-amd64-nl-nl-preview` | 具有地區設定的`nl-NL`容器映射。 |
| `2.0.1-amd64-pl-pl-preview` | 具有地區設定的`pl-PL`容器映射。 |
| `2.0.1-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `2.0.1-amd64-pt-pt-preview` | 具有地區設定的`pt-PT`容器映射。 |
| `2.0.1-amd64-ru-ru-preview` | 具有地區設定的`ru-RU`容器映射。 |
| `2.0.1-amd64-sv-se-preview` | 具有地區設定的`sv-SE`容器映射。 |
| `2.0.1-amd64-ta-in-preview` | 具有地區設定的`ta-IN`容器映射。 |
| `2.0.1-amd64-te-in-preview` | 具有地區設定的`te-IN`容器映射。 |
| `2.0.1-amd64-th-th-preview` | 具有地區設定的`th-TH`容器映射。 |
| `2.0.1-amd64-tr-tr-preview` | 具有地區設定的`tr-TR`容器映射。 |
| `2.0.1-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `2.0.1-amd64-zh-hk-preview` | 具有地區設定的`zh-HK`容器映射。 |
| `2.0.1-amd64-zh-tw-preview` | 具有地區設定的`zh-TW`容器映射。 |
| `2.0.0-amd64-ar-eg-preview` | 具有地區設定的`ar-EG`容器映射。 |
| `2.0.0-amd64-ca-es-preview` | 具有地區設定的`ca-ES`容器映射。 |
| `2.0.0-amd64-da-dk-preview` | 具有地區設定的`da-DK`容器映射。 |
| `2.0.0-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `2.0.0-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `2.0.0-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `2.0.0-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `2.0.0-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `2.0.0-amd64-en-nz-preview` | 具有地區設定的`en-NZ`容器映射。 |
| `2.0.0-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `2.0.0-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `2.0.0-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `2.0.0-amd64-fi-fi-preview` | 具有地區設定的`fi-FI`容器映射。 |
| `2.0.0-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `2.0.0-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `2.0.0-amd64-hi-in-preview` | 具有地區設定的`hi-IN`容器映射。 |
| `2.0.0-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `2.0.0-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `2.0.0-amd64-ko-kr-preview` | 具有地區設定的`ko-KR`容器映射。 |
| `2.0.0-amd64-nb-no-preview` | 具有地區設定的`nb-NO`容器映射。 |
| `2.0.0-amd64-nl-nl-preview` | 具有地區設定的`nl-NL`容器映射。 |
| `2.0.0-amd64-pl-pl-preview` | 具有地區設定的`pl-PL`容器映射。 |
| `2.0.0-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `2.0.0-amd64-pt-pt-preview` | 具有地區設定的`pt-PT`容器映射。 |
| `2.0.0-amd64-ru-ru-preview` | 具有地區設定的`ru-RU`容器映射。 |
| `2.0.0-amd64-sv-se-preview` | 具有地區設定的`sv-SE`容器映射。 |
| `2.0.0-amd64-th-th-preview` | 具有地區設定的`th-TH`容器映射。 |
| `2.0.0-amd64-tr-tr-preview` | 具有地區設定的`tr-TR`容器映射。 |
| `2.0.0-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `2.0.0-amd64-zh-hk-preview` | 具有地區設定的`zh-HK`容器映射。 |
| `2.0.0-amd64-zh-tw-preview` | 具有地區設定的`zh-TW`容器映射。 |
| `1.2.0-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `1.2.0-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `1.2.0-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `1.2.0-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `1.2.0-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `1.2.0-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `1.2.0-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `1.2.0-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `1.2.0-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `1.2.0-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `1.2.0-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `1.2.0-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `1.2.0-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `1.2.0-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `1.1.3-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `1.1.3-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `1.1.3-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `1.1.3-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `1.1.3-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `1.1.3-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `1.1.3-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `1.1.3-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `1.1.3-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `1.1.3-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `1.1.3-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `1.1.3-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `1.1.3-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `1.1.3-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `1.1.2-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `1.1.2-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `1.1.2-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `1.1.2-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `1.1.2-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `1.1.2-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `1.1.2-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `1.1.2-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `1.1.2-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `1.1.2-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `1.1.2-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `1.1.2-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `1.1.2-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `1.1.2-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `1.1.1-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `1.1.1-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `1.1.1-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `1.1.1-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `1.1.1-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `1.1.1-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `1.1.1-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `1.1.1-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `1.1.1-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `1.1.1-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `1.1.1-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `1.1.1-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `1.1.1-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `1.1.1-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `1.1.0-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `1.1.0-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `1.1.0-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `1.1.0-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `1.1.0-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `1.1.0-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `1.1.0-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `1.1.0-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `1.1.0-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `1.1.0-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `1.1.0-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `1.1.0-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `1.1.0-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `1.1.0-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |
| `1.0.0-amd64-de-de-preview` | 具有地區設定的`de-DE`容器映射。 |
| `1.0.0-amd64-en-au-preview` | 具有地區設定的`en-AU`容器映射。 |
| `1.0.0-amd64-en-ca-preview` | 具有地區設定的`en-CA`容器映射。 |
| `1.0.0-amd64-en-gb-preview` | 具有地區設定的`en-GB`容器映射。 |
| `1.0.0-amd64-en-in-preview` | 具有地區設定的`en-IN`容器映射。 |
| `1.0.0-amd64-en-us-preview` | 具有地區設定的`en-US`容器映射。 |
| `1.0.0-amd64-es-es-preview` | 具有地區設定的`es-ES`容器映射。 |
| `1.0.0-amd64-es-mx-preview` | 具有地區設定的`es-MX`容器映射。 |
| `1.0.0-amd64-fr-ca-preview` | 具有地區設定的`fr-CA`容器映射。 |
| `1.0.0-amd64-fr-fr-preview` | 具有地區設定的`fr-FR`容器映射。 |
| `1.0.0-amd64-it-it-preview` | 具有地區設定的`it-IT`容器映射。 |
| `1.0.0-amd64-ja-jp-preview` | 具有地區設定的`ja-JP`容器映射。 |
| `1.0.0-amd64-pt-br-preview` | 具有地區設定的`pt-BR`容器映射。 |
| `1.0.0-amd64-zh-cn-preview` | 具有地區設定的`zh-CN`容器映射。 |

## <a name="text-to-speech"></a>文字轉換語音

[文字轉換語音][sp-tts]容器映射可在`containerpreview.azurecr.io`容器登錄中找到。 它位於存放`microsoft`庫中，且名`cognitive-services-text-to-speech`為。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`。

此容器映射具有下列可用的標記：

| 影像標記                                  | 備忘錄                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和`en-US` `en-US-JessaRUS`語音的容器映射。        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | 具有地區設定和`ar-EG` `ar-EG-Hoda`語音的容器映射。            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | 具有地區設定和`ar-SA` `ar-SA-Naayf`語音的容器映射。           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | 具有地區設定和`bg-BG` `bg-BG-Ivan`語音的容器映射。            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | 具有地區設定和`ca-ES` `ca-ES-HerenaRUS`語音的容器映射。       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | 具有地區設定和`cs-CZ` `cs-CZ-Jakub`語音的容器映射。           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | 具有地區設定和`da-DK` `da-DK-HelleRUS`語音的容器映射。        |
| `1.3.0-amd64-de-at-michael-preview`         | 具有地區設定和`de-AT` `de-AT-Michael`語音的容器映射。         |
| `1.3.0-amd64-de-ch-karsten-preview`         | 具有地區設定和`de-CH` `de-CH-Karsten`語音的容器映射。         |
| `1.3.0-amd64-de-de-hedda-preview`           | 具有地區設定和`de-DE` `de-DE-Hedda`語音的容器映射。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 具有地區設定和`de-DE` `de-DE-Hedda`語音的容器映射。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 具有地區設定和`de-DE` `de-DE-HeddaRUS`語音的容器映射。        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和`de-DE` `de-DE-Stefan-Apollo`語音的容器映射。   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | 具有地區設定和`el-GR` `el-GR-Stefanos`語音的容器映射。        |
| `1.3.0-amd64-en-au-catherine-preview`       | 具有地區設定和`en-AU` `en-AU-Catherine`語音的容器映射。       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和`en-AU` `en-AU-HayleyRUS`語音的容器映射。       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | 具有地區設定和`en-CA` `en-CA-HeatherRUS`語音的容器映射。      |
| `1.3.0-amd64-en-ca-linda-preview`           | 具有地區設定和`en-CA` `en-CA-Linda`語音的容器映射。           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和`en-GB` `en-GB-George-Apollo`語音的容器映射。   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和`en-GB` `en-GB-HazelRUS`語音的容器映射。        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和`en-GB` `en-GB-Susan-Apollo`語音的容器映射。    |
| `1.3.0-amd64-en-ie-sean-preview`            | 具有地區設定和`en-IE` `en-IE-Sean`語音的容器映射。            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和`en-IN` `en-IN-Heera-Apollo`語音的容器映射。    |
| `1.3.0-amd64-en-in-priyarus-preview`        | 具有地區設定和`en-IN` `en-IN-PriyaRUS`語音的容器映射。        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和`en-IN` `en-IN-Ravi-Apollo`語音的容器映射。     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和`en-US` `en-US-BenjaminRUS`語音的容器映射。     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和`en-US` `en-US-Guy24kRUS`語音的容器映射。       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和`en-US` `en-US-Jessa24kRUS`語音的容器映射。     |
| `1.3.0-amd64-en-us-jessarus-preview`        | 具有地區設定和`en-US` `en-US-JessaRUS`語音的容器映射。        |
| `1.3.0-amd64-en-us-zirarus-preview`         | 具有地區設定和`en-US` `en-US-ZiraRUS`語音的容器映射。         |
| `1.3.0-amd64-es-es-helenarus-preview`       | 具有地區設定和`es-ES` `es-ES-HelenaRUS`語音的容器映射。       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和`es-ES` `es-ES-Laura-Apollo`語音的容器映射。    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和`es-ES` `es-ES-Pablo-Apollo`語音的容器映射。    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和`es-MX` `es-MX-HildaRUS`語音的容器映射。        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和`es-MX` `es-MX-Raul-Apollo`語音的容器映射。     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | 具有地區設定和`fi-FI` `fi-FI-HeidiRUS`語音的容器映射。        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和`fr-CA` `fr-CA-Caroline`語音的容器映射。        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和`fr-CA` `fr-CA-HarmonieRUS`語音的容器映射。     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | 具有地區設定和`fr-CH` `fr-CH-Guillaume`語音的容器映射。       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和`fr-FR` `fr-FR-HortenseRUS`語音的容器映射。     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和`fr-FR` `fr-FR-Julie-Apollo`語音的容器映射。    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和`fr-FR` `fr-FR-Paul-Apollo`語音的容器映射。     |
| `1.3.0-amd64-he-il-asaf-preview`            | 具有地區設定和`he-IL` `he-IL-Asaf`語音的容器映射。            |
| `1.3.0-amd64-hi-in-hemant-preview`          | 具有地區設定和`hi-IN` `hi-IN-Hemant`語音的容器映射。          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 具有地區設定和`hi-IN` `hi-IN-Kalpana-Apollo`語音的容器映射。  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 具有地區設定和`hi-IN` `hi-IN-Kalpana`語音的容器映射。         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 具有地區設定和`hi-IN` `hi-IN-Kalpana`語音的容器映射。         |
| `1.3.0-amd64-hr-hr-matej-preview`           | 具有地區設定和`hr-HR` `hr-HR-Matej`語音的容器映射。           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | 具有地區設定和`hu-HU` `hu-HU-Szabolcs`語音的容器映射。        |
| `1.3.0-amd64-id-id-andika-preview`          | 具有地區設定和`id-ID` `id-ID-Andika`語音的容器映射。          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和`it-IT` `it-IT-Cosimo-Apollo`語音的容器映射。   |
| `1.3.0-amd64-it-it-luciarus-preview`        | 具有地區設定和`it-IT` `it-IT-LuciaRUS`語音的容器映射。        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和`ja-JP` `ja-JP-Ayumi-Apollo`語音的容器映射。    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和`ja-JP` `ja-JP-HarukaRUS`語音的容器映射。       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和`ja-JP` `ja-JP-Ichiro-Apollo`語音的容器映射。   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和`ko-KR` `ko-KR-HeamiRUS`語音的容器映射。        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | 具有地區設定和`ms-MY` `ms-MY-Rizwan`語音的容器映射。          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | 具有地區設定和`nb-NO` `nb-NO-HuldaRUS`語音的容器映射。        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | 具有地區設定和`nl-NL` `nl-NL-HannaRUS`語音的容器映射。        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | 具有地區設定和`pl-PL` `pl-PL-PaulinaRUS`語音的容器映射。      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和`pt-BR` `pt-BR-Daniel-Apollo`語音的容器映射。   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和`pt-BR` `pt-BR-HeloisaRUS`語音的容器映射。      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | 具有地區設定和`pt-PT` `pt-PT-HeliaRUS`語音的容器映射。        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | 具有地區設定和`ro-RO` `ro-RO-Andrei`語音的容器映射。          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | 具有地區設定和`ru-RU` `ru-RU-EkaterinaRUS`語音的容器映射。    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | 具有地區設定和`ru-RU` `ru-RU-Irina-Apollo`語音的容器映射。    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | 具有地區設定和`ru-RU` `ru-RU-Pavel-Apollo`語音的容器映射。    |
| `1.3.0-amd64-sk-sk-filip-preview`           | 具有地區設定和`sk-SK` `sk-SK-Filip`語音的容器映射。           |
| `1.3.0-amd64-sl-si-lado-preview`            | 具有地區設定和`sl-SI` `sl-SI-Lado`語音的容器映射。            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | 具有地區設定和`sv-SE` `sv-SE-HedvigRUS`語音的容器映射。       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | 具有地區設定和`ta-IN` `ta-IN-Valluvar`語音的容器映射。        |
| `1.3.0-amd64-te-in-chitra-preview`          | 具有地區設定和`te-IN` `te-IN-Chitra`語音的容器映射。          |
| `1.3.0-amd64-th-th-pattara-preview`         | 具有地區設定和`th-TH` `th-TH-Pattara`語音的容器映射。         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | 具有地區設定和`tr-TR` `tr-TR-SedaRUS`語音的容器映射。         |
| `1.3.0-amd64-vi-vn-an-preview`              | 具有地區設定和`vi-VN` `vi-VN-An`語音的容器映射。              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和`zh-CN` `zh-CN-HuihuiRUS`語音的容器映射。       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和`zh-CN` `zh-CN-Kangkang-Apollo`語音的容器映射。 |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和`zh-CN` `zh-CN-Yaoyao-Apollo`語音的容器映射。   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | 具有地區設定和`zh-HK` `zh-HK-Danny-Apollo`語音的容器映射。    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | 具有地區設定和`zh-HK` `zh-HK-Tracy-Apollo`語音的容器映射。    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | 具有地區設定和`zh-HK` `zh-HK-TracyRUS`語音的容器映射。        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | 具有地區設定和`zh-TW` `zh-TW-HanHanRUS`語音的容器映射。       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | 具有地區設定和`zh-TW` `zh-TW-Yating-Apollo`語音的容器映射。   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有地區設定和`zh-TW` `zh-TW-Zhiwei-Apollo`語音的容器映射。   |
| `1.2.0-amd64-de-de-heddarus-preview`        | 具有地區設定和`de-DE` `de-DE-Hedda`語音的容器映射。           |
| `1.2.0-amd64-de-de-heddarus-preview`        | 具有地區設定和`de-DE` `de-DE-HeddaRUS`語音的容器映射。        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和`de-DE` `de-DE-Stefan-Apollo`語音的容器映射。   |
| `1.2.0-amd64-en-au-catherine-preview`       | 具有地區設定和`en-AU` `en-AU-Catherine`語音的容器映射。       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和`en-AU` `en-AU-HayleyRUS`語音的容器映射。       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和`en-GB` `en-GB-George-Apollo`語音的容器映射。   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和`en-GB` `en-GB-HazelRUS`語音的容器映射。        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和`en-GB` `en-GB-Susan-Apollo`語音的容器映射。    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和`en-IN` `en-IN-Heera-Apollo`語音的容器映射。    |
| `1.2.0-amd64-en-in-priyarus-preview`        | 具有地區設定和`en-IN` `en-IN-PriyaRUS`語音的容器映射。        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和`en-IN` `en-IN-Ravi-Apollo`語音的容器映射。     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和`en-US` `en-US-BenjaminRUS`語音的容器映射。     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和`en-US` `en-US-Guy24kRUS`語音的容器映射。       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和`en-US` `en-US-Jessa24kRUS`語音的容器映射。     |
| `1.2.0-amd64-en-us-jessarus-preview`        | 具有地區設定和`en-US` `en-US-JessaRUS`語音的容器映射。        |
| `1.2.0-amd64-en-us-zirarus-preview`         | 具有地區設定和`en-US` `en-US-ZiraRUS`語音的容器映射。         |
| `1.2.0-amd64-es-es-helenarus-preview`       | 具有地區設定和`es-ES` `es-ES-HelenaRUS`語音的容器映射。       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和`es-ES` `es-ES-Laura-Apollo`語音的容器映射。    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和`es-ES` `es-ES-Pablo-Apollo`語音的容器映射。    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和`es-MX` `es-MX-HildaRUS`語音的容器映射。        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和`es-MX` `es-MX-Raul-Apollo`語音的容器映射。     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和`fr-CA` `fr-CA-Caroline`語音的容器映射。        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和`fr-CA` `fr-CA-HarmonieRUS`語音的容器映射。     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和`fr-FR` `fr-FR-HortenseRUS`語音的容器映射。     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和`fr-FR` `fr-FR-Julie-Apollo`語音的容器映射。    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和`fr-FR` `fr-FR-Paul-Apollo`語音的容器映射。     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和`it-IT` `it-IT-Cosimo-Apollo`語音的容器映射。   |
| `1.2.0-amd64-it-it-luciarus-preview`        | 具有地區設定和`it-IT` `it-IT-LuciaRUS`語音的容器映射。        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和`ja-JP` `ja-JP-Ayumi-Apollo`語音的容器映射。    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和`ja-JP` `ja-JP-HarukaRUS`語音的容器映射。       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和`ja-JP` `ja-JP-Ichiro-Apollo`語音的容器映射。   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和`ko-KR` `ko-KR-HeamiRUS`語音的容器映射。        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和`pt-BR` `pt-BR-Daniel-Apollo`語音的容器映射。   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和`pt-BR` `pt-BR-HeloisaRUS`語音的容器映射。      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和`zh-CN` `zh-CN-HuihuiRUS`語音的容器映射。       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和`zh-CN` `zh-CN-Kangkang-Apollo`語音的容器映射。 |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和`zh-CN` `zh-CN-Yaoyao-Apollo`語音的容器映射。   |
| `1.1.0-amd64-de-de-hedda-preview`           | 具有地區設定和`de-DE` `de-DE-Hedda`語音的容器映射。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 具有地區設定和`de-DE` `de-DE-Hedda`語音的容器映射。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 具有地區設定和`de-DE` `de-DE-HeddaRUS`語音的容器映射。        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和`de-DE` `de-DE-Stefan-Apollo`語音的容器映射。   |
| `1.1.0-amd64-en-au-catherine-preview`       | 具有地區設定和`en-AU` `en-AU-Catherine`語音的容器映射。       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和`en-AU` `en-AU-HayleyRUS`語音的容器映射。       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和`en-GB` `en-GB-George-Apollo`語音的容器映射。   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和`en-GB` `en-GB-HazelRUS`語音的容器映射。        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和`en-GB` `en-GB-Susan-Apollo`語音的容器映射。    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和`en-IN` `en-IN-Heera-Apollo`語音的容器映射。    |
| `1.1.0-amd64-en-in-priyarus-preview`        | 具有地區設定和`en-IN` `en-IN-PriyaRUS`語音的容器映射。        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和`en-IN` `en-IN-Ravi-Apollo`語音的容器映射。     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和`en-US` `en-US-BenjaminRUS`語音的容器映射。     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和`en-US` `en-US-Guy24kRUS`語音的容器映射。       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和`en-US` `en-US-Jessa24kRUS`語音的容器映射。     |
| `1.1.0-amd64-en-us-jessarus-preview`        | 具有地區設定和`en-US` `en-US-JessaRUS`語音的容器映射。        |
| `1.1.0-amd64-en-us-zirarus-preview`         | 具有地區設定和`en-US` `en-US-ZiraRUS`語音的容器映射。         |
| `1.1.0-amd64-es-es-helenarus-preview`       | 具有地區設定和`es-ES` `es-ES-HelenaRUS`語音的容器映射。       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和`es-ES` `es-ES-Laura-Apollo`語音的容器映射。    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和`es-ES` `es-ES-Pablo-Apollo`語音的容器映射。    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和`es-MX` `es-MX-HildaRUS`語音的容器映射。        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和`es-MX` `es-MX-Raul-Apollo`語音的容器映射。     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和`fr-CA` `fr-CA-Caroline`語音的容器映射。        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和`fr-CA` `fr-CA-HarmonieRUS`語音的容器映射。     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和`fr-FR` `fr-FR-HortenseRUS`語音的容器映射。     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和`fr-FR` `fr-FR-Julie-Apollo`語音的容器映射。    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和`fr-FR` `fr-FR-Paul-Apollo`語音的容器映射。     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和`it-IT` `it-IT-Cosimo-Apollo`語音的容器映射。   |
| `1.1.0-amd64-it-it-luciarus-preview`        | 具有地區設定和`it-IT` `it-IT-LuciaRUS`語音的容器映射。        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和`ja-JP` `ja-JP-Ayumi-Apollo`語音的容器映射。    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和`ja-JP` `ja-JP-HarukaRUS`語音的容器映射。       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和`ja-JP` `ja-JP-Ichiro-Apollo`語音的容器映射。   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和`ko-KR` `ko-KR-HeamiRUS`語音的容器映射。        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和`pt-BR` `pt-BR-Daniel-Apollo`語音的容器映射。   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和`pt-BR` `pt-BR-HeloisaRUS`語音的容器映射。      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和`zh-CN` `zh-CN-HuihuiRUS`語音的容器映射。       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和`zh-CN` `zh-CN-Kangkang-Apollo`語音的容器映射。 |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和`zh-CN` `zh-CN-Yaoyao-Apollo`語音的容器映射。   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和`en-US` `en-US-BenjaminRUS`語音的容器映射。     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和`en-US` `en-US-Guy24kRUS`語音的容器映射。       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和`en-US` `en-US-Jessa24kRUS`語音的容器映射。     |
| `1.0.0-amd64-en-us-jessarus-preview`        | 具有地區設定和`en-US` `en-US-JessaRUS`語音的容器映射。        |
| `1.0.0-amd64-en-us-zirarus-preview`         | 具有地區設定和`en-US` `en-US-ZiraRUS`語音的容器映射。         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和`zh-CN` `zh-CN-HuihuiRUS`語音的容器映射。       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和`zh-CN` `zh-CN-Kangkang-Apollo`語音的容器映射。 |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和`zh-CN` `zh-CN-Yaoyao-Apollo`語音的容器映射。   |

## <a name="key-phrase-extraction"></a>關鍵片語擷取

您[Key Phrase Extraction][ta-kp]可以在`mcr.microsoft.com`容器登錄整合中找到關鍵片語擷取的容器映射。 它位於存放`azure-cognitive-services`庫中，且名`keyphrase`為。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/keyphrase`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>語言偵測

您[Language Detection][ta-la]可以在`mcr.microsoft.com`容器登錄整合中找到語言偵測的容器映射。 它位於存放`azure-cognitive-services`庫中，且名`language`為。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/language`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>情感分析

您[Sentiment Analysis][ta-se]可以在`mcr.microsoft.com`容器登錄整合中找到情感分析的容器映射。 它位於存放`azure-cognitive-services`庫中，且名`sentiment`為。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/sentiment`。

此容器映射具有下列可用的標記：

| 影像標記                    | 備忘錄 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
