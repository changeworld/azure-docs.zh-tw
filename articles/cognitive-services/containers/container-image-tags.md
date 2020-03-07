---
title: 認知服務容器映像標籤
titleSuffix: Azure Cognitive Services
description: 所有認知服務容器映射標記的完整清單。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/18/2019
ms.author: dapine
ms.openlocfilehash: 0d8c7a36582c30975f3a408a2ea6e95d39e560ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390765"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure 認知服務容器映射標籤

Azure 認知服務提供許多容器映射。 容器登錄和對應的存放庫會因容器映射而有所不同。 每個容器映射名稱都會提供多個標記。 容器映射標記是將容器映射進行版本控制的機制。 本文旨在用來列出所有認知服務容器映射及其可用標記的完整參考。

> [!TIP]
> 使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)時，請密切注意容器登錄、存放庫、容器映射名稱和對應標記的大小寫，因為它們會區分**大小**寫。

## <a name="anomaly-detector"></a>異常偵測器

您可以在 `containerpreview.azurecr.io` 容器登錄上找到[異常][ad-containers]偵測器容器映射。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-anomaly-detector`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008500001-amd64-preview` | |

## <a name="computer-vision"></a>電腦視覺

[電腦視覺][cv-containers]容器映射可在 `containerpreview.azurecr.io` 容器登錄中找到。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-read`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-read`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.009920003-amd64-preview` | |
| `1.1.009910003-amd64-preview` | |

## <a name="face"></a>臉部

您可以在 `containerpreview.azurecr.io` 容器登錄上找到[臉部][fa-containers]容器映射。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-face`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-face`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |
| `1.1.006490002-amd64-preview` | |
| `1.0.005940002-amd64-preview` | |
| `1.0.005550001-amd64-preview` | |

## <a name="form-recognizer"></a>表單辨識器

您可以在 `containerpreview.azurecr.io` 容器登錄上找到[表單辨識器][fr-containers]容器映射。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-form-recognizer`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008640001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |

## <a name="language-understanding-luis"></a>語言理解 (LUIS)

您可以在 `mcr.microsoft.com` 容器登錄整合中找到[LUIS][lu-containers]容器映射。 它位於 `azure-cognitive-services` 存放庫中，且名為 `luis`。 完整的容器映射名稱為，`mcr.microsoft.com/azure-cognitive-services/luis`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.010330004-amd64-preview` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.008010002-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.007020001-amd64-preview` | |

## <a name="custom-speech-to-text"></a>自訂語音轉換文字

您可以在 `containerpreview.azurecr.io` 容器登錄中找到[自訂的語音轉換文字][sp-cstt]容器映射。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-custom-speech-to-text`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `2.0.0-amd64-preview` | |

## <a name="custom-text-to-speech"></a>自訂文字轉換語音

您可以在 `containerpreview.azurecr.io` 容器登錄中找到[自訂文字轉換語音][sp-ctts]容器映射。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-custom-text-to-speech`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.3.0-amd64-preview` | |

## <a name="speech-to-text"></a>語音轉文字

[語音轉換文字][sp-stt]容器映射可在 `containerpreview.azurecr.io` 容器登錄中找到。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-speech-to-text`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | 具有 `en-US` 地區設定的容器映射。 |
| `2.0.0-amd64-ar-eg-preview` | 具有 `ar-EG` 地區設定的容器映射。 |
| `2.0.0-amd64-ca-es-preview` | 具有 `ca-ES` 地區設定的容器映射。 |
| `2.0.0-amd64-da-dk-preview` | 具有 `da-DK` 地區設定的容器映射。 |
| `2.0.0-amd64-de-de-preview` | 具有 `de-DE` 地區設定的容器映射。 |
| `2.0.0-amd64-en-au-preview` | 具有 `en-AU` 地區設定的容器映射。 |
| `2.0.0-amd64-en-ca-preview` | 具有 `en-CA` 地區設定的容器映射。 |
| `2.0.0-amd64-en-gb-preview` | 具有 `en-GB` 地區設定的容器映射。 |
| `2.0.0-amd64-en-in-preview` | 具有 `en-IN` 地區設定的容器映射。 |
| `2.0.0-amd64-en-nz-preview` | 具有 `en-NZ` 地區設定的容器映射。 |
| `2.0.0-amd64-en-us-preview` | 具有 `en-US` 地區設定的容器映射。 |
| `2.0.0-amd64-es-es-preview` | 具有 `es-ES` 地區設定的容器映射。 |
| `2.0.0-amd64-es-mx-preview` | 具有 `es-MX` 地區設定的容器映射。 |
| `2.0.0-amd64-fi-fi-preview` | 具有 `fi-FI` 地區設定的容器映射。 |
| `2.0.0-amd64-fr-ca-preview` | 具有 `fr-CA` 地區設定的容器映射。 |
| `2.0.0-amd64-fr-fr-preview` | 具有 `fr-FR` 地區設定的容器映射。 |
| `2.0.0-amd64-hi-in-preview` | 具有 `hi-IN` 地區設定的容器映射。 |
| `2.0.0-amd64-it-it-preview` | 具有 `it-IT` 地區設定的容器映射。 |
| `2.0.0-amd64-ja-jp-preview` | 具有 `ja-JP` 地區設定的容器映射。 |
| `2.0.0-amd64-ko-kr-preview` | 具有 `ko-KR` 地區設定的容器映射。 |
| `2.0.0-amd64-nb-no-preview` | 具有 `nb-NO` 地區設定的容器映射。 |
| `2.0.0-amd64-nl-nl-preview` | 具有 `nl-NL` 地區設定的容器映射。 |
| `2.0.0-amd64-pl-pl-preview` | 具有 `pl-PL` 地區設定的容器映射。 |
| `2.0.0-amd64-pt-br-preview` | 具有 `pt-BR` 地區設定的容器映射。 |
| `2.0.0-amd64-pt-pt-preview` | 具有 `pt-PT` 地區設定的容器映射。 |
| `2.0.0-amd64-ru-ru-preview` | 具有 `ru-RU` 地區設定的容器映射。 |
| `2.0.0-amd64-sv-se-preview` | 具有 `sv-SE` 地區設定的容器映射。 |
| `2.0.0-amd64-th-th-preview` | 具有 `th-TH` 地區設定的容器映射。 |
| `2.0.0-amd64-tr-tr-preview` | 具有 `tr-TR` 地區設定的容器映射。 |
| `2.0.0-amd64-zh-cn-preview` | 具有 `zh-CN` 地區設定的容器映射。 |
| `2.0.0-amd64-zh-hk-preview` | 具有 `zh-HK` 地區設定的容器映射。 |
| `2.0.0-amd64-zh-tw-preview` | 具有 `zh-TW` 地區設定的容器映射。 |
| `1.2.0-amd64-de-de-preview` | 具有 `de-DE` 地區設定的容器映射。 |
| `1.2.0-amd64-en-au-preview` | 具有 `en-AU` 地區設定的容器映射。 |
| `1.2.0-amd64-en-ca-preview` | 具有 `en-CA` 地區設定的容器映射。 |
| `1.2.0-amd64-en-gb-preview` | 具有 `en-GB` 地區設定的容器映射。 |
| `1.2.0-amd64-en-in-preview` | 具有 `en-IN` 地區設定的容器映射。 |
| `1.2.0-amd64-en-us-preview` | 具有 `en-US` 地區設定的容器映射。 |
| `1.2.0-amd64-es-es-preview` | 具有 `es-ES` 地區設定的容器映射。 |
| `1.2.0-amd64-es-mx-preview` | 具有 `es-MX` 地區設定的容器映射。 |
| `1.2.0-amd64-fr-ca-preview` | 具有 `fr-CA` 地區設定的容器映射。 |
| `1.2.0-amd64-fr-fr-preview` | 具有 `fr-FR` 地區設定的容器映射。 |
| `1.2.0-amd64-it-it-preview` | 具有 `it-IT` 地區設定的容器映射。 |
| `1.2.0-amd64-ja-jp-preview` | 具有 `ja-JP` 地區設定的容器映射。 |
| `1.2.0-amd64-pt-br-preview` | 具有 `pt-BR` 地區設定的容器映射。 |
| `1.2.0-amd64-zh-cn-preview` | 具有 `zh-CN` 地區設定的容器映射。 |
| `1.1.3-amd64-de-de-preview` | 具有 `de-DE` 地區設定的容器映射。 |
| `1.1.3-amd64-en-au-preview` | 具有 `en-AU` 地區設定的容器映射。 |
| `1.1.3-amd64-en-ca-preview` | 具有 `en-CA` 地區設定的容器映射。 |
| `1.1.3-amd64-en-gb-preview` | 具有 `en-GB` 地區設定的容器映射。 |
| `1.1.3-amd64-en-in-preview` | 具有 `en-IN` 地區設定的容器映射。 |
| `1.1.3-amd64-en-us-preview` | 具有 `en-US` 地區設定的容器映射。 |
| `1.1.3-amd64-es-es-preview` | 具有 `es-ES` 地區設定的容器映射。 |
| `1.1.3-amd64-es-mx-preview` | 具有 `es-MX` 地區設定的容器映射。 |
| `1.1.3-amd64-fr-ca-preview` | 具有 `fr-CA` 地區設定的容器映射。 |
| `1.1.3-amd64-fr-fr-preview` | 具有 `fr-FR` 地區設定的容器映射。 |
| `1.1.3-amd64-it-it-preview` | 具有 `it-IT` 地區設定的容器映射。 |
| `1.1.3-amd64-ja-jp-preview` | 具有 `ja-JP` 地區設定的容器映射。 |
| `1.1.3-amd64-pt-br-preview` | 具有 `pt-BR` 地區設定的容器映射。 |
| `1.1.3-amd64-zh-cn-preview` | 具有 `zh-CN` 地區設定的容器映射。 |
| `1.1.2-amd64-de-de-preview` | 具有 `de-DE` 地區設定的容器映射。 |
| `1.1.2-amd64-en-au-preview` | 具有 `en-AU` 地區設定的容器映射。 |
| `1.1.2-amd64-en-ca-preview` | 具有 `en-CA` 地區設定的容器映射。 |
| `1.1.2-amd64-en-gb-preview` | 具有 `en-GB` 地區設定的容器映射。 |
| `1.1.2-amd64-en-in-preview` | 具有 `en-IN` 地區設定的容器映射。 |
| `1.1.2-amd64-en-us-preview` | 具有 `en-US` 地區設定的容器映射。 |
| `1.1.2-amd64-es-es-preview` | 具有 `es-ES` 地區設定的容器映射。 |
| `1.1.2-amd64-es-mx-preview` | 具有 `es-MX` 地區設定的容器映射。 |
| `1.1.2-amd64-fr-ca-preview` | 具有 `fr-CA` 地區設定的容器映射。 |
| `1.1.2-amd64-fr-fr-preview` | 具有 `fr-FR` 地區設定的容器映射。 |
| `1.1.2-amd64-it-it-preview` | 具有 `it-IT` 地區設定的容器映射。 |
| `1.1.2-amd64-ja-jp-preview` | 具有 `ja-JP` 地區設定的容器映射。 |
| `1.1.2-amd64-pt-br-preview` | 具有 `pt-BR` 地區設定的容器映射。 |
| `1.1.2-amd64-zh-cn-preview` | 具有 `zh-CN` 地區設定的容器映射。 |
| `1.1.1-amd64-de-de-preview` | 具有 `de-DE` 地區設定的容器映射。 |
| `1.1.1-amd64-en-au-preview` | 具有 `en-AU` 地區設定的容器映射。 |
| `1.1.1-amd64-en-ca-preview` | 具有 `en-CA` 地區設定的容器映射。 |
| `1.1.1-amd64-en-gb-preview` | 具有 `en-GB` 地區設定的容器映射。 |
| `1.1.1-amd64-en-in-preview` | 具有 `en-IN` 地區設定的容器映射。 |
| `1.1.1-amd64-en-us-preview` | 具有 `en-US` 地區設定的容器映射。 |
| `1.1.1-amd64-es-es-preview` | 具有 `es-ES` 地區設定的容器映射。 |
| `1.1.1-amd64-es-mx-preview` | 具有 `es-MX` 地區設定的容器映射。 |
| `1.1.1-amd64-fr-ca-preview` | 具有 `fr-CA` 地區設定的容器映射。 |
| `1.1.1-amd64-fr-fr-preview` | 具有 `fr-FR` 地區設定的容器映射。 |
| `1.1.1-amd64-it-it-preview` | 具有 `it-IT` 地區設定的容器映射。 |
| `1.1.1-amd64-ja-jp-preview` | 具有 `ja-JP` 地區設定的容器映射。 |
| `1.1.1-amd64-pt-br-preview` | 具有 `pt-BR` 地區設定的容器映射。 |
| `1.1.1-amd64-zh-cn-preview` | 具有 `zh-CN` 地區設定的容器映射。 |
| `1.1.0-amd64-de-de-preview` | 具有 `de-DE` 地區設定的容器映射。 |
| `1.1.0-amd64-en-au-preview` | 具有 `en-AU` 地區設定的容器映射。 |
| `1.1.0-amd64-en-ca-preview` | 具有 `en-CA` 地區設定的容器映射。 |
| `1.1.0-amd64-en-gb-preview` | 具有 `en-GB` 地區設定的容器映射。 |
| `1.1.0-amd64-en-in-preview` | 具有 `en-IN` 地區設定的容器映射。 |
| `1.1.0-amd64-en-us-preview` | 具有 `en-US` 地區設定的容器映射。 |
| `1.1.0-amd64-es-es-preview` | 具有 `es-ES` 地區設定的容器映射。 |
| `1.1.0-amd64-es-mx-preview` | 具有 `es-MX` 地區設定的容器映射。 |
| `1.1.0-amd64-fr-ca-preview` | 具有 `fr-CA` 地區設定的容器映射。 |
| `1.1.0-amd64-fr-fr-preview` | 具有 `fr-FR` 地區設定的容器映射。 |
| `1.1.0-amd64-it-it-preview` | 具有 `it-IT` 地區設定的容器映射。 |
| `1.1.0-amd64-ja-jp-preview` | 具有 `ja-JP` 地區設定的容器映射。 |
| `1.1.0-amd64-pt-br-preview` | 具有 `pt-BR` 地區設定的容器映射。 |
| `1.1.0-amd64-zh-cn-preview` | 具有 `zh-CN` 地區設定的容器映射。 |
| `1.0.0-amd64-de-de-preview` | 具有 `de-DE` 地區設定的容器映射。 |
| `1.0.0-amd64-en-au-preview` | 具有 `en-AU` 地區設定的容器映射。 |
| `1.0.0-amd64-en-ca-preview` | 具有 `en-CA` 地區設定的容器映射。 |
| `1.0.0-amd64-en-gb-preview` | 具有 `en-GB` 地區設定的容器映射。 |
| `1.0.0-amd64-en-in-preview` | 具有 `en-IN` 地區設定的容器映射。 |
| `1.0.0-amd64-en-us-preview` | 具有 `en-US` 地區設定的容器映射。 |
| `1.0.0-amd64-es-es-preview` | 具有 `es-ES` 地區設定的容器映射。 |
| `1.0.0-amd64-es-mx-preview` | 具有 `es-MX` 地區設定的容器映射。 |
| `1.0.0-amd64-fr-ca-preview` | 具有 `fr-CA` 地區設定的容器映射。 |
| `1.0.0-amd64-fr-fr-preview` | 具有 `fr-FR` 地區設定的容器映射。 |
| `1.0.0-amd64-it-it-preview` | 具有 `it-IT` 地區設定的容器映射。 |
| `1.0.0-amd64-ja-jp-preview` | 具有 `ja-JP` 地區設定的容器映射。 |
| `1.0.0-amd64-pt-br-preview` | 具有 `pt-BR` 地區設定的容器映射。 |
| `1.0.0-amd64-zh-cn-preview` | 具有 `zh-CN` 地區設定的容器映射。 |

## <a name="text-to-speech"></a>文字轉換語音

[文字轉換語音][sp-tts]容器映射可在 `containerpreview.azurecr.io` 容器登錄中找到。 它位於 `microsoft` 存放庫中，且名為 `cognitive-services-text-to-speech`。 完整的容器映射名稱為，`containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | 具有 `en-US` 地區設定和 `en-US-JessaRUS` 語音的容器映射。 |
| `1.3.0-amd64-ar-eg-hoda-preview` | 具有 `ar-EG` 地區設定和 `ar-EG-Hoda` 語音的容器映射。 |
| `1.3.0-amd64-ar-sa-naayf-preview` | 具有 `ar-SA` 地區設定和 `ar-SA-Naayf` 語音的容器映射。 |
| `1.3.0-amd64-bg-bg-ivan-preview` | 具有 `bg-BG` 地區設定和 `bg-BG-Ivan` 語音的容器映射。 |
| `1.3.0-amd64-ca-es-herenarus-preview` | 具有 `ca-ES` 地區設定和 `ca-ES-HerenaRUS` 語音的容器映射。 |
| `1.3.0-amd64-cs-cz-jakub-preview` | 具有 `cs-CZ` 地區設定和 `cs-CZ-Jakub` 語音的容器映射。 |
| `1.3.0-amd64-da-dk-hellerus-preview` | 具有 `da-DK` 地區設定和 `da-DK-HelleRUS` 語音的容器映射。 |
| `1.3.0-amd64-de-at-michael-preview` | 具有 `de-AT` 地區設定和 `de-AT-Michael` 語音的容器映射。 |
| `1.3.0-amd64-de-ch-karsten-preview` | 具有 `de-CH` 地區設定和 `de-CH-Karsten` 語音的容器映射。 |
| `1.3.0-amd64-de-de-hedda-preview` | 具有 `de-DE` 地區設定和 `de-DE-Hedda` 語音的容器映射。 |
| `1.3.0-amd64-de-de-heddarus-preview` | 具有 `de-DE` 地區設定和 `de-DE-Hedda` 語音的容器映射。 |
| `1.3.0-amd64-de-de-heddarus-preview` | 具有 `de-DE` 地區設定和 `de-DE-HeddaRUS` 語音的容器映射。 |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | 具有 `de-DE` 地區設定和 `de-DE-Stefan-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-el-gr-stefanos-preview` | 具有 `el-GR` 地區設定和 `el-GR-Stefanos` 語音的容器映射。 |
| `1.3.0-amd64-en-au-catherine-preview` | 具有 `en-AU` 地區設定和 `en-AU-Catherine` 語音的容器映射。 |
| `1.3.0-amd64-en-au-hayleyrus-preview` | 具有 `en-AU` 地區設定和 `en-AU-HayleyRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-ca-heatherrus-preview` | 具有 `en-CA` 地區設定和 `en-CA-HeatherRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-ca-linda-preview` | 具有 `en-CA` 地區設定和 `en-CA-Linda` 語音的容器映射。 |
| `1.3.0-amd64-en-gb-george-apollo-preview` | 具有 `en-GB` 地區設定和 `en-GB-George-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-en-gb-hazelrus-preview` | 具有 `en-GB` 地區設定和 `en-GB-HazelRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | 具有 `en-GB` 地區設定和 `en-GB-Susan-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-en-ie-sean-preview` | 具有 `en-IE` 地區設定和 `en-IE-Sean` 語音的容器映射。 |
| `1.3.0-amd64-en-in-heera-apollo-preview` | 具有 `en-IN` 地區設定和 `en-IN-Heera-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-en-in-priyarus-preview` | 具有 `en-IN` 地區設定和 `en-IN-PriyaRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | 具有 `en-IN` 地區設定和 `en-IN-Ravi-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-en-us-benjaminrus-preview` | 具有 `en-US` 地區設定和 `en-US-BenjaminRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-us-guy24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Guy24kRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-us-jessa24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Jessa24kRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-us-jessarus-preview` | 具有 `en-US` 地區設定和 `en-US-JessaRUS` 語音的容器映射。 |
| `1.3.0-amd64-en-us-zirarus-preview` | 具有 `en-US` 地區設定和 `en-US-ZiraRUS` 語音的容器映射。 |
| `1.3.0-amd64-es-es-helenarus-preview` | 具有 `es-ES` 地區設定和 `es-ES-HelenaRUS` 語音的容器映射。 |
| `1.3.0-amd64-es-es-laura-apollo-preview` | 具有 `es-ES` 地區設定和 `es-ES-Laura-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | 具有 `es-ES` 地區設定和 `es-ES-Pablo-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-es-mx-hildarus-preview` | 具有 `es-MX` 地區設定和 `es-MX-HildaRUS` 語音的容器映射。 |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | 具有 `es-MX` 地區設定和 `es-MX-Raul-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-fi-fi-heidirus-preview` | 具有 `fi-FI` 地區設定和 `fi-FI-HeidiRUS` 語音的容器映射。 |
| `1.3.0-amd64-fr-ca-caroline-preview` | 具有 `fr-CA` 地區設定和 `fr-CA-Caroline` 語音的容器映射。 |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | 具有 `fr-CA` 地區設定和 `fr-CA-HarmonieRUS` 語音的容器映射。 |
| `1.3.0-amd64-fr-ch-guillaume-preview` | 具有 `fr-CH` 地區設定和 `fr-CH-Guillaume` 語音的容器映射。 |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-HortenseRUS` 語音的容器映射。 |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-Julie-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-Paul-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-he-il-asaf-preview` | 具有 `he-IL` 地區設定和 `he-IL-Asaf` 語音的容器映射。 |
| `1.3.0-amd64-hi-in-hemant-preview` | 具有 `hi-IN` 地區設定和 `hi-IN-Hemant` 語音的容器映射。 |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | 具有 `hi-IN` 地區設定和 `hi-IN-Kalpana-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | 具有 `hi-IN` 地區設定和 `hi-IN-Kalpana` 語音的容器映射。 |
| `1.3.0-amd64-hi-in-kalpana-preview` | 具有 `hi-IN` 地區設定和 `hi-IN-Kalpana` 語音的容器映射。 |
| `1.3.0-amd64-hr-hr-matej-preview` | 具有 `hr-HR` 地區設定和 `hr-HR-Matej` 語音的容器映射。 |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | 具有 `hu-HU` 地區設定和 `hu-HU-Szabolcs` 語音的容器映射。 |
| `1.3.0-amd64-id-id-andika-preview` | 具有 `id-ID` 地區設定和 `id-ID-Andika` 語音的容器映射。 |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | 具有 `it-IT` 地區設定和 `it-IT-Cosimo-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-it-it-luciarus-preview` | 具有 `it-IT` 地區設定和 `it-IT-LuciaRUS` 語音的容器映射。 |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-Ayumi-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-ja-jp-harukarus-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-HarukaRUS` 語音的容器映射。 |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-Ichiro-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-ko-kr-heamirus-preview` | 具有 `ko-KR` 地區設定和 `ko-KR-HeamiRUS` 語音的容器映射。 |
| `1.3.0-amd64-ms-my-rizwan-preview` | 具有 `ms-MY` 地區設定和 `ms-MY-Rizwan` 語音的容器映射。 |
| `1.3.0-amd64-nb-no-huldarus-preview` | 具有 `nb-NO` 地區設定和 `nb-NO-HuldaRUS` 語音的容器映射。 |
| `1.3.0-amd64-nl-nl-hannarus-preview` | 具有 `nl-NL` 地區設定和 `nl-NL-HannaRUS` 語音的容器映射。 |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | 具有 `pl-PL` 地區設定和 `pl-PL-PaulinaRUS` 語音的容器映射。 |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | 具有 `pt-BR` 地區設定和 `pt-BR-Daniel-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-pt-br-heloisarus-preview` | 具有 `pt-BR` 地區設定和 `pt-BR-HeloisaRUS` 語音的容器映射。 |
| `1.3.0-amd64-pt-pt-heliarus-preview` | 具有 `pt-PT` 地區設定和 `pt-PT-HeliaRUS` 語音的容器映射。 |
| `1.3.0-amd64-ro-ro-andrei-preview` | 具有 `ro-RO` 地區設定和 `ro-RO-Andrei` 語音的容器映射。 |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | 具有 `ru-RU` 地區設定和 `ru-RU-EkaterinaRUS` 語音的容器映射。 |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | 具有 `ru-RU` 地區設定和 `ru-RU-Irina-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | 具有 `ru-RU` 地區設定和 `ru-RU-Pavel-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-sk-sk-filip-preview` | 具有 `sk-SK` 地區設定和 `sk-SK-Filip` 語音的容器映射。 |
| `1.3.0-amd64-sl-si-lado-preview` | 具有 `sl-SI` 地區設定和 `sl-SI-Lado` 語音的容器映射。 |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | 具有 `sv-SE` 地區設定和 `sv-SE-HedvigRUS` 語音的容器映射。 |
| `1.3.0-amd64-ta-in-valluvar-preview` | 具有 `ta-IN` 地區設定和 `ta-IN-Valluvar` 語音的容器映射。 |
| `1.3.0-amd64-te-in-chitra-preview` | 具有 `te-IN` 地區設定和 `te-IN-Chitra` 語音的容器映射。 |
| `1.3.0-amd64-th-th-pattara-preview` | 具有 `th-TH` 地區設定和 `th-TH-Pattara` 語音的容器映射。 |
| `1.3.0-amd64-tr-tr-sedarus-preview` | 具有 `tr-TR` 地區設定和 `tr-TR-SedaRUS` 語音的容器映射。 |
| `1.3.0-amd64-vi-vn-an-preview` | 具有 `vi-VN` 地區設定和 `vi-VN-An` 語音的容器映射。 |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-HuihuiRUS` 語音的容器映射。 |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Kangkang-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Yaoyao-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | 具有 `zh-HK` 地區設定和 `zh-HK-Danny-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | 具有 `zh-HK` 地區設定和 `zh-HK-Tracy-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | 具有 `zh-HK` 地區設定和 `zh-HK-TracyRUS` 語音的容器映射。 |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | 具有 `zh-TW` 地區設定和 `zh-TW-HanHanRUS` 語音的容器映射。 |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | 具有 `zh-TW` 地區設定和 `zh-TW-Yating-Apollo` 語音的容器映射。 |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | 具有 `zh-TW` 地區設定和 `zh-TW-Zhiwei-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-de-de-heddarus-preview` | 具有 `de-DE` 地區設定和 `de-DE-Hedda` 語音的容器映射。 |
| `1.2.0-amd64-de-de-heddarus-preview` | 具有 `de-DE` 地區設定和 `de-DE-HeddaRUS` 語音的容器映射。 |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | 具有 `de-DE` 地區設定和 `de-DE-Stefan-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-en-au-catherine-preview` | 具有 `en-AU` 地區設定和 `en-AU-Catherine` 語音的容器映射。 |
| `1.2.0-amd64-en-au-hayleyrus-preview` | 具有 `en-AU` 地區設定和 `en-AU-HayleyRUS` 語音的容器映射。 |
| `1.2.0-amd64-en-gb-george-apollo-preview` | 具有 `en-GB` 地區設定和 `en-GB-George-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-en-gb-hazelrus-preview` | 具有 `en-GB` 地區設定和 `en-GB-HazelRUS` 語音的容器映射。 |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | 具有 `en-GB` 地區設定和 `en-GB-Susan-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-en-in-heera-apollo-preview` | 具有 `en-IN` 地區設定和 `en-IN-Heera-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-en-in-priyarus-preview` | 具有 `en-IN` 地區設定和 `en-IN-PriyaRUS` 語音的容器映射。 |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | 具有 `en-IN` 地區設定和 `en-IN-Ravi-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-en-us-benjaminrus-preview` | 具有 `en-US` 地區設定和 `en-US-BenjaminRUS` 語音的容器映射。 |
| `1.2.0-amd64-en-us-guy24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Guy24kRUS` 語音的容器映射。 |
| `1.2.0-amd64-en-us-jessa24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Jessa24kRUS` 語音的容器映射。 |
| `1.2.0-amd64-en-us-jessarus-preview` | 具有 `en-US` 地區設定和 `en-US-JessaRUS` 語音的容器映射。 |
| `1.2.0-amd64-en-us-zirarus-preview` | 具有 `en-US` 地區設定和 `en-US-ZiraRUS` 語音的容器映射。 |
| `1.2.0-amd64-es-es-helenarus-preview` | 具有 `es-ES` 地區設定和 `es-ES-HelenaRUS` 語音的容器映射。 |
| `1.2.0-amd64-es-es-laura-apollo-preview` | 具有 `es-ES` 地區設定和 `es-ES-Laura-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | 具有 `es-ES` 地區設定和 `es-ES-Pablo-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-es-mx-hildarus-preview` | 具有 `es-MX` 地區設定和 `es-MX-HildaRUS` 語音的容器映射。 |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | 具有 `es-MX` 地區設定和 `es-MX-Raul-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-fr-ca-caroline-preview` | 具有 `fr-CA` 地區設定和 `fr-CA-Caroline` 語音的容器映射。 |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | 具有 `fr-CA` 地區設定和 `fr-CA-HarmonieRUS` 語音的容器映射。 |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-HortenseRUS` 語音的容器映射。 |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-Julie-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-Paul-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | 具有 `it-IT` 地區設定和 `it-IT-Cosimo-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-it-it-luciarus-preview` | 具有 `it-IT` 地區設定和 `it-IT-LuciaRUS` 語音的容器映射。 |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-Ayumi-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-ja-jp-harukarus-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-HarukaRUS` 語音的容器映射。 |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-Ichiro-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-ko-kr-heamirus-preview` | 具有 `ko-KR` 地區設定和 `ko-KR-HeamiRUS` 語音的容器映射。 |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | 具有 `pt-BR` 地區設定和 `pt-BR-Daniel-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-pt-br-heloisarus-preview` | 具有 `pt-BR` 地區設定和 `pt-BR-HeloisaRUS` 語音的容器映射。 |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-HuihuiRUS` 語音的容器映射。 |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Kangkang-Apollo` 語音的容器映射。 |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Yaoyao-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-de-de-hedda-preview` | 具有 `de-DE` 地區設定和 `de-DE-Hedda` 語音的容器映射。 |
| `1.1.0-amd64-de-de-heddarus-preview` | 具有 `de-DE` 地區設定和 `de-DE-Hedda` 語音的容器映射。 |
| `1.1.0-amd64-de-de-heddarus-preview` | 具有 `de-DE` 地區設定和 `de-DE-HeddaRUS` 語音的容器映射。 |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | 具有 `de-DE` 地區設定和 `de-DE-Stefan-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-en-au-catherine-preview` | 具有 `en-AU` 地區設定和 `en-AU-Catherine` 語音的容器映射。 |
| `1.1.0-amd64-en-au-hayleyrus-preview` | 具有 `en-AU` 地區設定和 `en-AU-HayleyRUS` 語音的容器映射。 |
| `1.1.0-amd64-en-gb-george-apollo-preview` | 具有 `en-GB` 地區設定和 `en-GB-George-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-en-gb-hazelrus-preview` | 具有 `en-GB` 地區設定和 `en-GB-HazelRUS` 語音的容器映射。 |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | 具有 `en-GB` 地區設定和 `en-GB-Susan-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-en-in-heera-apollo-preview` | 具有 `en-IN` 地區設定和 `en-IN-Heera-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-en-in-priyarus-preview` | 具有 `en-IN` 地區設定和 `en-IN-PriyaRUS` 語音的容器映射。 |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | 具有 `en-IN` 地區設定和 `en-IN-Ravi-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-en-us-benjaminrus-preview` | 具有 `en-US` 地區設定和 `en-US-BenjaminRUS` 語音的容器映射。 |
| `1.1.0-amd64-en-us-guy24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Guy24kRUS` 語音的容器映射。 |
| `1.1.0-amd64-en-us-jessa24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Jessa24kRUS` 語音的容器映射。 |
| `1.1.0-amd64-en-us-jessarus-preview` | 具有 `en-US` 地區設定和 `en-US-JessaRUS` 語音的容器映射。 |
| `1.1.0-amd64-en-us-zirarus-preview` | 具有 `en-US` 地區設定和 `en-US-ZiraRUS` 語音的容器映射。 |
| `1.1.0-amd64-es-es-helenarus-preview` | 具有 `es-ES` 地區設定和 `es-ES-HelenaRUS` 語音的容器映射。 |
| `1.1.0-amd64-es-es-laura-apollo-preview` | 具有 `es-ES` 地區設定和 `es-ES-Laura-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | 具有 `es-ES` 地區設定和 `es-ES-Pablo-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-es-mx-hildarus-preview` | 具有 `es-MX` 地區設定和 `es-MX-HildaRUS` 語音的容器映射。 |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | 具有 `es-MX` 地區設定和 `es-MX-Raul-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-fr-ca-caroline-preview` | 具有 `fr-CA` 地區設定和 `fr-CA-Caroline` 語音的容器映射。 |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | 具有 `fr-CA` 地區設定和 `fr-CA-HarmonieRUS` 語音的容器映射。 |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-HortenseRUS` 語音的容器映射。 |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-Julie-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | 具有 `fr-FR` 地區設定和 `fr-FR-Paul-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | 具有 `it-IT` 地區設定和 `it-IT-Cosimo-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-it-it-luciarus-preview` | 具有 `it-IT` 地區設定和 `it-IT-LuciaRUS` 語音的容器映射。 |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-Ayumi-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-ja-jp-harukarus-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-HarukaRUS` 語音的容器映射。 |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | 具有 `ja-JP` 地區設定和 `ja-JP-Ichiro-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-ko-kr-heamirus-preview` | 具有 `ko-KR` 地區設定和 `ko-KR-HeamiRUS` 語音的容器映射。 |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | 具有 `pt-BR` 地區設定和 `pt-BR-Daniel-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-pt-br-heloisarus-preview` | 具有 `pt-BR` 地區設定和 `pt-BR-HeloisaRUS` 語音的容器映射。 |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-HuihuiRUS` 語音的容器映射。 |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Kangkang-Apollo` 語音的容器映射。 |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Yaoyao-Apollo` 語音的容器映射。 |
| `1.0.0-amd64-en-us-benjaminrus-preview` | 具有 `en-US` 地區設定和 `en-US-BenjaminRUS` 語音的容器映射。 |
| `1.0.0-amd64-en-us-guy24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Guy24kRUS` 語音的容器映射。 |
| `1.0.0-amd64-en-us-jessa24krus-preview` | 具有 `en-US` 地區設定和 `en-US-Jessa24kRUS` 語音的容器映射。 |
| `1.0.0-amd64-en-us-jessarus-preview` | 具有 `en-US` 地區設定和 `en-US-JessaRUS` 語音的容器映射。 |
| `1.0.0-amd64-en-us-zirarus-preview` | 具有 `en-US` 地區設定和 `en-US-ZiraRUS` 語音的容器映射。 |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-HuihuiRUS` 語音的容器映射。 |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Kangkang-Apollo` 語音的容器映射。 |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | 具有 `zh-CN` 地區設定和 `zh-CN-Yaoyao-Apollo` 語音的容器映射。 |

## <a name="key-phrase-extraction"></a>關鍵片語擷取

[關鍵片語擷取][ta-kp]容器映射可在 `mcr.microsoft.com` container registry 聯合中找到。 它位於 `azure-cognitive-services` 存放庫中，且名為 `keyphrase`。 完整的容器映射名稱為，`mcr.microsoft.com/azure-cognitive-services/keyphrase`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="language-detection"></a>語言偵測

[語言偵測][ta-la]容器映射可在 `mcr.microsoft.com` container registry 聯合中找到。 它位於 `azure-cognitive-services` 存放庫中，且名為 `language`。 完整的容器映射名稱為，`mcr.microsoft.com/azure-cognitive-services/language`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="sentiment-analysis"></a>情感分析

[情感分析][ta-se]容器映射可在 `mcr.microsoft.com` container registry 聯合中找到。 它位於 `azure-cognitive-services` 存放庫中，且名為 `sentiment`。 完整的容器映射名稱為，`mcr.microsoft.com/azure-cognitive-services/sentiment`。

此容器映射具有下列可用的標記：

| 影像標記 | 注意 |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

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
