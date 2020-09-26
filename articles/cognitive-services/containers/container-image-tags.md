---
title: 認知服務容器映像標籤
titleSuffix: Azure Cognitive Services
description: 所有認知服務容器映射標記的完整清單。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 2a24433389e738bf5d0ecb7ecac6bf369c8ba183
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369479"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure 認知服務容器映射標記

Azure 認知服務提供許多容器映射。 容器的登錄和對應的存放庫會在容器映射之間有所不同。 每個容器映射名稱都會提供多個標記。 容器映射標記是一種管理容器映射的機制。 本文旨在作為列出所有認知服務容器映射及其可用標記的完整參考。

> [!TIP]
> 使用時 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) ，請密切注意容器登錄、儲存機制、容器映射名稱和對應標記的大小寫，因為它們會區分 **大小**寫。

## <a name="anomaly-detector"></a>異常偵測器

您可以在 container registry 整合上找到 [異常][ad-containers] 偵測器容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services` 庫中，並命名為 `anomaly-detector` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` 。

此容器映射有下列可用的標記：

| 影像標記                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>電腦視覺

您可以在容器登錄中找到 [電腦視覺][cv-containers] Read OCR 容器映射 `containerpreview.azurecr.io` 。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-read` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-read` 。

此容器映射有下列可用的標記：

| 影像標記                    | 注意 |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | •進一步減少容器的記憶體使用量。 |
|                                          | •需要外部快取以進行多 pod 設定。 例如，設定快取的 Redis。 |
|                                          | •修正設定 Redis 快取和 ResultExpirationPeriod = 0 時的結果遺失問題。  |
|                                          | •移除26MB 的要求主體大小限制。 容器現在可以接受 >26MB 檔。  |
|                                          | •將時間戳記和組建版本新增至主控台記錄。  |
| `1.1.013050001-amd64-preview`            | * 新增了 ReadEngineConfig： ResultExpirationPeriod 容器初始化設定，以指定系統應該清除辨識結果的時間。 |
|                                          | 設定是以小時為單位，預設值為48hr。   |
|                                          |   此設定可以減少儲存結果的記憶體使用量，尤其是在使用容器記憶體中儲存體時。  |
|                                          |    * 範例1。 ReadEngineConfig： ResultExpirationPeriod = 1，系統會在進程之後清除辨識結果1小時。   |
|                                          |    * 範例2。 ReadEngineConfig： ResultExpirationPeriod = 0，系統會在結果抓取之後清除辨識結果。  |
|                                          | 修正將不正確影像格式傳遞到系統時的500內部伺服器錯誤。 它現在會傳回400錯誤：   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>臉部

[臉部][fa-containers]容器映射可在 container registry 上找到 `containerpreview.azurecr.io` 。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-face` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-face` 。

此容器映射有下列可用的標記：

| 影像標記                    | 注意 |
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

您可以在容器登錄上找到 [表單辨識器][fr-containers] 容器映射 `containerpreview.azurecr.io` 。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-form-recognizer` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` 。

此容器映射有下列可用的標記：

| 影像標記                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>語言理解 (LUIS)

[LUIS][lu-containers]容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services` 庫中，並命名為 `luis` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/luis` 。

此容器映射有下列可用的標記：

| 影像標記                    | 注意 |
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

您可以在 container registry 上找到 [自訂的語音轉換文字][sp-cstt] 容器映射 `containerpreview.azurecr.io` 。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-custom-speech-to-text` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` 。

此容器映射有下列可用的標記：

| 影像標記            | 注意 |
|-----------------------|:------|
| `latest`              |       |
| `2.5.0-amd64`         |       |
| `2.4.0-amd64-preview` |       |
| `2.3.1-amd64-preview` |       | 
| `2.3.0-amd64-preview` |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>自訂文字轉換語音

您可以在容器登錄上找到 [自訂文字轉換語音][sp-ctts] 的容器映射 `containerpreview.azurecr.io` 。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-custom-text-to-speech` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` 。

此容器映射有下列可用的標記：

| 影像標記            | 注意 |
|-----------------------|:------|
| `latest`              |       |
| `1.7.0-amd64`         |       |
| `1.6.0-amd64-preview` |       |
| `1.6.0-amd64-preview` |       |
| `1.5.0-amd64-preview` |       |
| `1.4.0-amd64-preview` |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>語音轉文字

[語音轉換文字][sp-stt]容器映射可在 `containerpreview.azurecr.io` container registry 上找到。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-speech-to-text` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` 。
*美國政府的弗吉尼亞州*支援語音轉換文字 v 2.5.0 映射。 請使用 *美國政府的佛吉尼亞* 帳單端點和 api 金鑰來試用。

此容器映射有下列可用的標記：

| 影像標記                  | 注意                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | 具有地區設定的容器映射 `en-US` 。 |
| `2.5.0-amd64-ar-ae`         | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.5.0-amd64-ar-eg`         | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.5.0-amd64-ar-kw`         | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.5.0-amd64-ar-qa`         | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.5.0-amd64-ar-sa`         | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.5.0-amd64-ca-es`         | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.5.0-amd64-da-dk`         | 具有地區設定的容器映射 `da-DK` 。 |
| `2.5.0-amd64-de-de`         | 具有地區設定的容器映射 `de-DE` 。 |
| `2.5.0-amd64-en-au`         | 具有地區設定的容器映射 `en-AU` 。 |
| `2.5.0-amd64-en-ca`         | 具有地區設定的容器映射 `en-CA` 。 |
| `2.5.0-amd64-en-gb`         | 具有地區設定的容器映射 `en-GB` 。 |
| `2.5.0-amd64-en-in`         | 具有地區設定的容器映射 `en-IN` 。 |
| `2.5.0-amd64-en-nz`         | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.5.0-amd64-en-us`         | 具有地區設定的容器映射 `en-US` 。 |
| `2.5.0-amd64-es-es`         | 具有地區設定的容器映射 `es-ES` 。 |
| `2.5.0-amd64-es-mx`         | 具有地區設定的容器映射 `es-MX` 。 |
| `2.5.0-amd64-fi-fi`         | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.5.0-amd64-fr-ca`         | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.5.0-amd64-fr-fr`         | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.5.0-amd64-gu-in`         | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.5.0-amd64-hi-in`         | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.5.0-amd64-it-it`         | 具有地區設定的容器映射 `it-IT` 。 |
| `2.5.0-amd64-ja-jp`         | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.5.0-amd64-ko-kr`         | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.5.0-amd64-mr-in`         | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.5.0-amd64-nb-no`         | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.5.0-amd64-nl-nl`         | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.5.0-amd64-pl-pl`         | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.5.0-amd64-pt-br`         | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.5.0-amd64-pt-pt`         | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.5.0-amd64-ru-ru`         | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.5.0-amd64-sv-se`         | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.5.0-amd64-ta-in`         | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.5.0-amd64-te-in`         | 具有地區設定的容器映射 `te-IN` 。 |
| `2.5.0-amd64-th-th`         | 具有地區設定的容器映射 `th-TH` 。 |
| `2.5.0-amd64-tr-tr`         | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.5.0-amd64-zh-cn`         | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.5.0-amd64-zh-hk`         | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.5.0-amd64-zh-tw`         | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.4.0-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.4.0-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.4.0-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.4.0-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.4.0-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.4.0-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.4.0-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.4.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.4.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.4.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.4.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.4.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.4.0-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.4.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.4.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.4.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.4.0-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.4.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.4.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.4.0-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.4.0-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.4.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.4.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.4.0-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.4.0-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.4.0-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.4.0-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.4.0-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.4.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.4.0-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.4.0-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.4.0-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.4.0-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.4.0-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.4.0-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.4.0-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.4.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.4.0-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.4.0-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.3.1-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.3.1-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.3.1-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.3.1-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.3.1-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.3.1-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.3.1-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.3.1-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.3.1-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.3.1-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.3.1-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.3.1-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.3.1-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.3.1-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.3.1-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.3.1-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.3.1-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.3.1-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.3.1-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.3.1-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.3.1-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.3.1-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.3.1-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.3.1-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.3.1-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.3.1-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.3.1-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.3.1-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.3.1-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.3.1-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.3.1-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.3.1-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.3.1-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.3.1-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.3.1-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.3.1-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.3.1-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.3.1-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.3.1-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.3.0-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.3.0-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.3.0-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.3.0-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.3.0-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.3.0-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.3.0-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.3.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.3.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.3.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.3.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.3.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.3.0-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.3.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.3.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.3.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.3.0-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.3.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.3.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.3.0-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.3.0-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.3.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.3.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.3.0-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.3.0-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.3.0-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.3.0-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.3.0-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.3.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.3.0-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.3.0-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.3.0-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.3.0-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.3.0-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.3.0-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.3.0-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.3.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.3.0-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.3.0-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.2.0-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.2.0-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.2.0-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.2.0-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.2.0-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.2.0-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.2.0-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.2.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.2.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.2.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.2.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.2.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.2.0-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.2.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.2.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.2.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.2.0-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.2.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.2.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.2.0-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.2.0-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.2.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.2.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.2.0-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.2.0-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.2.0-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.2.0-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.2.0-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.2.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.2.0-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.2.0-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.2.0-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.2.0-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.2.0-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.2.0-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.2.0-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.2.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.2.0-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.2.0-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.1.1-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.1.1-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.1.1-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.1.1-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.1.1-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.1.1-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.1.1-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.1.1-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.1.1-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.1.1-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.1.1-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.1.1-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.1.1-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.1.1-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.1.1-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.1.1-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.1.1-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.1.1-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.1.1-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.1.1-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.1.1-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.1.1-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.1.1-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.1.1-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.1.1-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.1.1-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.1.1-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.1.1-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.1.1-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.1.1-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.1.1-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.1.1-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.1.1-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.1.1-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.1.1-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.1.1-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.1.1-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.1.1-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.1.1-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.1.1-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.1.0-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.1.0-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.1.0-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.1.0-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.1.0-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.1.0-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.1.0-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.1.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.1.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.1.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.1.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.1.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.1.0-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.1.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.1.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.1.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.1.0-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.1.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.1.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.1.0-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.1.0-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.1.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.1.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.1.0-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.1.0-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.1.0-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.1.0-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.1.0-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.1.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.1.0-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.1.0-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.1.0-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.1.0-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.1.0-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.1.0-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.1.0-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.1.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.1.0-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.1.0-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.0.3-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.0.2-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.0.2-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.0.2-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.0.2-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.0.2-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.0.2-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.0.2-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.0.2-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.0.2-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.0.2-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.0.2-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.0.2-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.0.2-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.0.2-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.0.2-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.0.2-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.0.2-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.0.2-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.0.2-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.0.2-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.0.2-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.0.2-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.0.2-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.0.2-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.0.2-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.0.2-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.0.2-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.0.2-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.0.2-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.0.2-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.0.2-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.0.2-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.0.2-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.0.2-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.0.2-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.0.2-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.0.2-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.0.2-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.0.2-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.0.1-amd64-ar-ae-preview` | 具有地區設定的容器映射 `ar-AE` 。 |
| `2.0.1-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.0.1-amd64-ar-kw-preview` | 具有地區設定的容器映射 `ar-KW` 。 |
| `2.0.1-amd64-ar-qa-preview` | 具有地區設定的容器映射 `ar-QA` 。 |
| `2.0.1-amd64-ar-sa-preview` | 具有地區設定的容器映射 `ar-SA` 。 |
| `2.0.1-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.0.1-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.0.1-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.0.1-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.0.1-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.0.1-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.0.1-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.0.1-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.0.1-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.0.1-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.0.1-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.0.1-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.0.1-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.0.1-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.0.1-amd64-gu-in-preview` | 具有地區設定的容器映射 `gu-IN` 。 |
| `2.0.1-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.0.1-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.0.1-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.0.1-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.0.1-amd64-mr-in-preview` | 具有地區設定的容器映射 `mr-IN` 。 |
| `2.0.1-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.0.1-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.0.1-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.0.1-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.0.1-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.0.1-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.0.1-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.0.1-amd64-ta-in-preview` | 具有地區設定的容器映射 `ta-IN` 。 |
| `2.0.1-amd64-te-in-preview` | 具有地區設定的容器映射 `te-IN` 。 |
| `2.0.1-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.0.1-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.0.1-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.0.1-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.0.1-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `2.0.0-amd64-ar-eg-preview` | 具有地區設定的容器映射 `ar-EG` 。 |
| `2.0.0-amd64-ca-es-preview` | 具有地區設定的容器映射 `ca-ES` 。 |
| `2.0.0-amd64-da-dk-preview` | 具有地區設定的容器映射 `da-DK` 。 |
| `2.0.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `2.0.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `2.0.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `2.0.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `2.0.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `2.0.0-amd64-en-nz-preview` | 具有地區設定的容器映射 `en-NZ` 。 |
| `2.0.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `2.0.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `2.0.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `2.0.0-amd64-fi-fi-preview` | 具有地區設定的容器映射 `fi-FI` 。 |
| `2.0.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `2.0.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `2.0.0-amd64-hi-in-preview` | 具有地區設定的容器映射 `hi-IN` 。 |
| `2.0.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `2.0.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `2.0.0-amd64-ko-kr-preview` | 具有地區設定的容器映射 `ko-KR` 。 |
| `2.0.0-amd64-nb-no-preview` | 具有地區設定的容器映射 `nb-NO` 。 |
| `2.0.0-amd64-nl-nl-preview` | 具有地區設定的容器映射 `nl-NL` 。 |
| `2.0.0-amd64-pl-pl-preview` | 具有地區設定的容器映射 `pl-PL` 。 |
| `2.0.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `2.0.0-amd64-pt-pt-preview` | 具有地區設定的容器映射 `pt-PT` 。 |
| `2.0.0-amd64-ru-ru-preview` | 具有地區設定的容器映射 `ru-RU` 。 |
| `2.0.0-amd64-sv-se-preview` | 具有地區設定的容器映射 `sv-SE` 。 |
| `2.0.0-amd64-th-th-preview` | 具有地區設定的容器映射 `th-TH` 。 |
| `2.0.0-amd64-tr-tr-preview` | 具有地區設定的容器映射 `tr-TR` 。 |
| `2.0.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `2.0.0-amd64-zh-hk-preview` | 具有地區設定的容器映射 `zh-HK` 。 |
| `2.0.0-amd64-zh-tw-preview` | 具有地區設定的容器映射 `zh-TW` 。 |
| `1.2.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `1.2.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `1.2.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `1.2.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `1.2.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `1.2.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `1.2.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `1.2.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `1.2.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `1.2.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `1.2.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `1.2.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `1.2.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `1.2.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `1.1.3-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `1.1.3-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `1.1.3-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `1.1.3-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `1.1.3-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `1.1.3-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `1.1.3-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `1.1.3-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `1.1.3-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `1.1.3-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `1.1.3-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `1.1.3-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `1.1.3-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `1.1.3-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `1.1.2-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `1.1.2-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `1.1.2-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `1.1.2-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `1.1.2-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `1.1.2-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `1.1.2-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `1.1.2-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `1.1.2-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `1.1.2-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `1.1.2-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `1.1.2-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `1.1.2-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `1.1.2-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `1.1.1-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `1.1.1-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `1.1.1-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `1.1.1-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `1.1.1-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `1.1.1-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `1.1.1-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `1.1.1-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `1.1.1-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `1.1.1-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `1.1.1-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `1.1.1-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `1.1.1-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `1.1.1-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `1.1.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `1.1.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `1.1.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `1.1.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `1.1.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `1.1.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `1.1.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `1.1.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `1.1.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `1.1.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `1.1.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `1.1.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `1.1.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `1.1.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |
| `1.0.0-amd64-de-de-preview` | 具有地區設定的容器映射 `de-DE` 。 |
| `1.0.0-amd64-en-au-preview` | 具有地區設定的容器映射 `en-AU` 。 |
| `1.0.0-amd64-en-ca-preview` | 具有地區設定的容器映射 `en-CA` 。 |
| `1.0.0-amd64-en-gb-preview` | 具有地區設定的容器映射 `en-GB` 。 |
| `1.0.0-amd64-en-in-preview` | 具有地區設定的容器映射 `en-IN` 。 |
| `1.0.0-amd64-en-us-preview` | 具有地區設定的容器映射 `en-US` 。 |
| `1.0.0-amd64-es-es-preview` | 具有地區設定的容器映射 `es-ES` 。 |
| `1.0.0-amd64-es-mx-preview` | 具有地區設定的容器映射 `es-MX` 。 |
| `1.0.0-amd64-fr-ca-preview` | 具有地區設定的容器映射 `fr-CA` 。 |
| `1.0.0-amd64-fr-fr-preview` | 具有地區設定的容器映射 `fr-FR` 。 |
| `1.0.0-amd64-it-it-preview` | 具有地區設定的容器映射 `it-IT` 。 |
| `1.0.0-amd64-ja-jp-preview` | 具有地區設定的容器映射 `ja-JP` 。 |
| `1.0.0-amd64-pt-br-preview` | 具有地區設定的容器映射 `pt-BR` 。 |
| `1.0.0-amd64-zh-cn-preview` | 具有地區設定的容器映射 `zh-CN` 。 |

## <a name="text-to-speech"></a>文字轉換語音

您可以在容器登錄上找到 [文字轉換語音][sp-tts] 的容器映射 `containerpreview.azurecr.io` 。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-text-to-speech` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` 。

此容器映射有下列可用的標記：

| 影像標記                                  | 注意                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。         |
| `1.7.0-amd64-ar-eg-hoda`                    | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            |
| `1.7.0-amd64-ar-sa-naayf`                   | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           |
| `1.7.0-amd64-bg-bg-ivan`                    | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            |
| `1.7.0-amd64-ca-es-herenarus`               | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       |
| `1.7.0-amd64-cs-cz-jakub`                   | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           |
| `1.7.0-amd64-da-dk-hellerus`                | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        |
| `1.7.0-amd64-de-at-michael`                 | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         |
| `1.7.0-amd64-de-ch-karsten`                 | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         |
| `1.7.0-amd64-de-de-hedda`                   | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.7.0-amd64-de-de-heddarus`                | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.7.0-amd64-de-de-stefan-apollo`           | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `1.7.0-amd64-el-gr-stefanos`                | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        |
| `1.7.0-amd64-en-au-catherine`               | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `1.7.0-amd64-en-au-hayleyrus`               | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `1.7.0-amd64-en-ca-heatherrus`              | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      |
| `1.7.0-amd64-en-ca-linda`                   | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           |
| `1.7.0-amd64-en-gb-george-apollo`           | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `1.7.0-amd64-en-gb-hazelrus`                | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `1.7.0-amd64-en-gb-susan-apollo`            | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `1.7.0-amd64-en-ie-sean`                    | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            |
| `1.7.0-amd64-en-in-heera-apollo`            | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `1.7.0-amd64-en-in-priyarus`                | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `1.7.0-amd64-en-in-ravi-apollo`             | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `1.7.0-amd64-en-us-benjaminrus`             | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.7.0-amd64-en-us-guy24krus`               | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.7.0-amd64-en-us-aria24krus`              | 具有地區設定和語音的容器映射 `en-US` `en-US-Aria24kRUS` 。      |
| `1.7.0-amd64-en-us-ariarus`                 | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。         |
| `1.7.0-amd64-en-us-zirarus`                 | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.7.0-amd64-es-es-helenarus`               | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `1.7.0-amd64-es-es-laura-apollo`            | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `1.7.0-amd64-es-es-pablo-apollo`            | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `1.7.0-amd64-es-mx-hildarus`                | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `1.7.0-amd64-es-mx-raul-apollo`             | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `1.7.0-amd64-fi-fi-heidirus`                | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        |
| `1.7.0-amd64-fr-ca-caroline`                | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `1.7.0-amd64-fr-ca-harmonierus`             | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `1.7.0-amd64-fr-ch-guillaume`               | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       |
| `1.7.0-amd64-fr-fr-hortenserus`             | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `1.7.0-amd64-fr-fr-julie-apollo`            | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `1.7.0-amd64-fr-fr-paul-apollo`             | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `1.7.0-amd64-he-il-asaf`                    | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            |
| `1.7.0-amd64-hi-in-hemant`                  | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          |
| `1.7.0-amd64-hi-in-kalpana-apollo`          | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana-Apollo` 。  |
| `1.7.0-amd64-hi-in-kalpana`                 | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `1.7.0-amd64-hr-hr-matej`                   | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           |
| `1.7.0-amd64-hu-hu-szabolcs`                | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        |
| `1.7.0-amd64-id-id-andika`                  | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          |
| `1.7.0-amd64-it-it-cosimo-apollo`           | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `1.7.0-amd64-it-it-luciarus`                | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `1.7.0-amd64-ja-jp-ayumi-apollo`            | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `1.7.0-amd64-ja-jp-harukarus`               | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `1.7.0-amd64-ja-jp-ichiro-apollo`           | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `1.7.0-amd64-ko-kr-heamirus`                | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `1.7.0-amd64-ms-my-rizwan`                  | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          |
| `1.7.0-amd64-nb-no-huldarus`                | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        |
| `1.7.0-amd64-nl-nl-hannarus`                | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        |
| `1.7.0-amd64-pl-pl-paulinarus`              | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      |
| `1.7.0-amd64-pt-br-daniel-apollo`           | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `1.7.0-amd64-pt-br-heloisarus`              | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `1.7.0-amd64-pt-pt-heliarus`                | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        |
| `1.7.0-amd64-ro-ro-andrei`                  | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          |
| `1.7.0-amd64-ru-ru-ekaterinarus`            | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    |
| `1.7.0-amd64-ru-ru-irina-apollo`            | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    |
| `1.7.0-amd64-ru-ru-pavel-apollo`            | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    |
| `1.7.0-amd64-sk-sk-filip`                   | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           |
| `1.7.0-amd64-sl-si-lado`                    | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            |
| `1.7.0-amd64-sv-se-hedvigrus`               | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       |
| `1.7.0-amd64-ta-in-valluvar`                | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        |
| `1.7.0-amd64-te-in-chitra`                  | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          |
| `1.7.0-amd64-th-th-pattara`                 | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         |
| `1.7.0-amd64-tr-tr-sedarus`                 | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         |
| `1.7.0-amd64-vi-vn-an`                      | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              |
| `1.7.0-amd64-zh-cn-huihuirus`               | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.7.0-amd64-zh-cn-kangkang-apollo`         | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.7.0-amd64-zh-cn-yaoyao-apollo`           | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `1.7.0-amd64-zh-hk-danny-apollo`            | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    |
| `1.7.0-amd64-zh-hk-tracy-apollo`            | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    |
| `1.7.0-amd64-zh-hk-tracyrus`                | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        |
| `1.7.0-amd64-zh-tw-hanhanrus`               | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       |
| `1.7.0-amd64-zh-tw-yating-apollo`           | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   |
| `1.7.0-amd64-zh-tw-zhiwei-apollo`           | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   |
| `1.6.0-amd64-ar-eg-hoda-preview`            | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            |
| `1.6.0-amd64-ar-sa-naayf-preview`           | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           |
| `1.6.0-amd64-bg-bg-ivan-preview`            | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            |
| `1.6.0-amd64-ca-es-herenarus-preview`       | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       |
| `1.6.0-amd64-cs-cz-jakub-preview`           | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           |
| `1.6.0-amd64-da-dk-hellerus-preview`        | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        |
| `1.6.0-amd64-de-at-michael-preview`         | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         |
| `1.6.0-amd64-de-ch-karsten-preview`         | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         |
| `1.6.0-amd64-de-de-hedda-preview`           | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.6.0-amd64-de-de-heddarus-preview`        | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.6.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `1.6.0-amd64-el-gr-stefanos-preview`        | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        |
| `1.6.0-amd64-en-au-catherine-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `1.6.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `1.6.0-amd64-en-ca-heatherrus-preview`      | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      |
| `1.6.0-amd64-en-ca-linda-preview`           | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           |
| `1.6.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `1.6.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `1.6.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `1.6.0-amd64-en-ie-sean-preview`            | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            |
| `1.6.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `1.6.0-amd64-en-in-priyarus-preview`        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `1.6.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `1.6.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.6.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.6.0-amd64-en-us-aria24krus-preview`      | 具有地區設定和語音的容器映射 `en-US` `en-US-Aria24kRUS` 。      |
| `1.6.0-amd64-en-us-ariarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。         |
| `1.6.0-amd64-en-us-zirarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.6.0-amd64-es-es-helenarus-preview`       | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `1.6.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `1.6.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `1.6.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `1.6.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `1.6.0-amd64-fi-fi-heidirus-preview`        | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        |
| `1.6.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `1.6.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `1.6.0-amd64-fr-ch-guillaume-preview`       | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       |
| `1.6.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `1.6.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `1.6.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `1.6.0-amd64-he-il-asaf-preview`            | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            |
| `1.6.0-amd64-hi-in-hemant-preview`          | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          |
| `1.6.0-amd64-hi-in-kalpana-apollo-preview`  | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana-Apollo` 。  |
| `1.6.0-amd64-hi-in-kalpana-preview`         | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `1.6.0-amd64-hr-hr-matej-preview`           | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           |
| `1.6.0-amd64-hu-hu-szabolcs-preview`        | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        |
| `1.6.0-amd64-id-id-andika-preview`          | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          |
| `1.6.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `1.6.0-amd64-it-it-luciarus-preview`        | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `1.6.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `1.6.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `1.6.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `1.6.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `1.6.0-amd64-ms-my-rizwan-preview`          | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          |
| `1.6.0-amd64-nb-no-huldarus-preview`        | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        |
| `1.6.0-amd64-nl-nl-hannarus-preview`        | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        |
| `1.6.0-amd64-pl-pl-paulinarus-preview`      | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      |
| `1.6.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `1.6.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `1.6.0-amd64-pt-pt-heliarus-preview`        | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        |
| `1.6.0-amd64-ro-ro-andrei-preview`          | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          |
| `1.6.0-amd64-ru-ru-ekaterinarus-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    |
| `1.6.0-amd64-ru-ru-irina-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    |
| `1.6.0-amd64-ru-ru-pavel-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    |
| `1.6.0-amd64-sk-sk-filip-preview`           | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           |
| `1.6.0-amd64-sl-si-lado-preview`            | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            |
| `1.6.0-amd64-sv-se-hedvigrus-preview`       | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       |
| `1.6.0-amd64-ta-in-valluvar-preview`        | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        |
| `1.6.0-amd64-te-in-chitra-preview`          | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          |
| `1.6.0-amd64-th-th-pattara-preview`         | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         |
| `1.6.0-amd64-tr-tr-sedarus-preview`         | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         |
| `1.6.0-amd64-vi-vn-an-preview`              | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              |
| `1.6.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.6.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.6.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `1.6.0-amd64-zh-hk-danny-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    |
| `1.6.0-amd64-zh-hk-tracy-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    |
| `1.6.0-amd64-zh-hk-tracyrus-preview`        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        |
| `1.6.0-amd64-zh-tw-hanhanrus-preview`       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       |
| `1.6.0-amd64-zh-tw-yating-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   |
| `1.6.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   |
| `1.5.0-amd64-ar-eg-hoda-preview`            | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            |
| `1.5.0-amd64-ar-sa-naayf-preview`           | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           |
| `1.5.0-amd64-bg-bg-ivan-preview`            | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            |
| `1.5.0-amd64-ca-es-herenarus-preview`       | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       |
| `1.5.0-amd64-cs-cz-jakub-preview`           | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           |
| `1.5.0-amd64-da-dk-hellerus-preview`        | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        |
| `1.5.0-amd64-de-at-michael-preview`         | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         |
| `1.5.0-amd64-de-ch-karsten-preview`         | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         |
| `1.5.0-amd64-de-de-hedda-preview`           | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.5.0-amd64-de-de-heddarus-preview`        | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.5.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `1.5.0-amd64-el-gr-stefanos-preview`        | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        |
| `1.5.0-amd64-en-au-catherine-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `1.5.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `1.5.0-amd64-en-ca-heatherrus-preview`      | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      |
| `1.5.0-amd64-en-ca-linda-preview`           | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           |
| `1.5.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `1.5.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `1.5.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `1.5.0-amd64-en-ie-sean-preview`            | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            |
| `1.5.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `1.5.0-amd64-en-in-priyarus-preview`        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `1.5.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `1.5.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.5.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.5.0-amd64-en-us-aria24krus-preview`      | 具有地區設定和語音的容器映射 `en-US` `en-US-Aria24kRUS` 。     |
| `1.5.0-amd64-en-us-ariarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。        |
| `1.5.0-amd64-en-us-zirarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.5.0-amd64-es-es-helenarus-preview`       | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `1.5.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `1.5.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `1.5.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `1.5.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `1.5.0-amd64-fi-fi-heidirus-preview`        | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        |
| `1.5.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `1.5.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `1.5.0-amd64-fr-ch-guillaume-preview`       | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       |
| `1.5.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `1.5.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `1.5.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `1.5.0-amd64-he-il-asaf-preview`            | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            |
| `1.5.0-amd64-hi-in-hemant-preview`          | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          |
| `1.5.0-amd64-hi-in-kalpana-apollo-preview`  | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana-Apollo` 。  |
| `1.5.0-amd64-hi-in-kalpana-preview`         | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `1.5.0-amd64-hr-hr-matej-preview`           | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           |
| `1.5.0-amd64-hu-hu-szabolcs-preview`        | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        |
| `1.5.0-amd64-id-id-andika-preview`          | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          |
| `1.5.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `1.5.0-amd64-it-it-luciarus-preview`        | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `1.5.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `1.5.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `1.5.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `1.5.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `1.5.0-amd64-ms-my-rizwan-preview`          | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          |
| `1.5.0-amd64-nb-no-huldarus-preview`        | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        |
| `1.5.0-amd64-nl-nl-hannarus-preview`        | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        |
| `1.5.0-amd64-pl-pl-paulinarus-preview`      | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      |
| `1.5.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `1.5.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `1.5.0-amd64-pt-pt-heliarus-preview`        | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        |
| `1.5.0-amd64-ro-ro-andrei-preview`          | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          |
| `1.5.0-amd64-ru-ru-ekaterinarus-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    |
| `1.5.0-amd64-ru-ru-irina-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    |
| `1.5.0-amd64-ru-ru-pavel-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    |
| `1.5.0-amd64-sk-sk-filip-preview`           | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           |
| `1.5.0-amd64-sl-si-lado-preview`            | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            |
| `1.5.0-amd64-sv-se-hedvigrus-preview`       | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       |
| `1.5.0-amd64-ta-in-valluvar-preview`        | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        |
| `1.5.0-amd64-te-in-chitra-preview`          | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          |
| `1.5.0-amd64-th-th-pattara-preview`         | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         |
| `1.5.0-amd64-tr-tr-sedarus-preview`         | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         |
| `1.5.0-amd64-vi-vn-an-preview`              | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              |
| `1.5.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.5.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.5.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `1.5.0-amd64-zh-hk-danny-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    |
| `1.5.0-amd64-zh-hk-tracy-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    |
| `1.5.0-amd64-zh-hk-tracyrus-preview`        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        |
| `1.5.0-amd64-zh-tw-hanhanrus-preview`       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       |
| `1.5.0-amd64-zh-tw-yating-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   |
| `1.5.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   |
| `1.4.0-amd64-ar-eg-hoda-preview`            | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            |
| `1.4.0-amd64-ar-sa-naayf-preview`           | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           |
| `1.4.0-amd64-bg-bg-ivan-preview`            | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            |
| `1.4.0-amd64-ca-es-herenarus-preview`       | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       |
| `1.4.0-amd64-cs-cz-jakub-preview`           | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           |
| `1.4.0-amd64-da-dk-hellerus-preview`        | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        |
| `1.4.0-amd64-de-at-michael-preview`         | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         |
| `1.4.0-amd64-de-ch-karsten-preview`         | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         |
| `1.4.0-amd64-de-de-hedda-preview`           | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.4.0-amd64-de-de-heddarus-preview`        | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.4.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `1.4.0-amd64-el-gr-stefanos-preview`        | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        |
| `1.4.0-amd64-en-au-catherine-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `1.4.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `1.4.0-amd64-en-ca-heatherrus-preview`      | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      |
| `1.4.0-amd64-en-ca-linda-preview`           | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           |
| `1.4.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `1.4.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `1.4.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `1.4.0-amd64-en-ie-sean-preview`            | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            |
| `1.4.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `1.4.0-amd64-en-in-priyarus-preview`        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `1.4.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `1.4.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.4.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.4.0-amd64-en-us-aria24krus-preview`      | 具有地區設定和語音的容器映射 `en-US` `en-US-Aria24kRUS` 。     |
| `1.4.0-amd64-en-us-ariarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。        |
| `1.4.0-amd64-en-us-zirarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.4.0-amd64-es-es-helenarus-preview`       | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `1.4.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `1.4.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `1.4.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `1.4.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `1.4.0-amd64-fi-fi-heidirus-preview`        | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        |
| `1.4.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `1.4.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `1.4.0-amd64-fr-ch-guillaume-preview`       | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       |
| `1.4.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `1.4.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `1.4.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `1.4.0-amd64-he-il-asaf-preview`            | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            |
| `1.4.0-amd64-hi-in-hemant-preview`          | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          |
| `1.4.0-amd64-hi-in-kalpana-apollo-preview`  | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana-Apollo` 。  |
| `1.4.0-amd64-hi-in-kalpana-preview`         | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `1.4.0-amd64-hr-hr-matej-preview`           | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           |
| `1.4.0-amd64-hu-hu-szabolcs-preview`        | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        |
| `1.4.0-amd64-id-id-andika-preview`          | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          |
| `1.4.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `1.4.0-amd64-it-it-luciarus-preview`        | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `1.4.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `1.4.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `1.4.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `1.4.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `1.4.0-amd64-ms-my-rizwan-preview`          | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          |
| `1.4.0-amd64-nb-no-huldarus-preview`        | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        |
| `1.4.0-amd64-nl-nl-hannarus-preview`        | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        |
| `1.4.0-amd64-pl-pl-paulinarus-preview`      | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      |
| `1.4.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `1.4.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `1.4.0-amd64-pt-pt-heliarus-preview`        | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        |
| `1.4.0-amd64-ro-ro-andrei-preview`          | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          |
| `1.4.0-amd64-ru-ru-ekaterinarus-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    |
| `1.4.0-amd64-ru-ru-irina-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    |
| `1.4.0-amd64-ru-ru-pavel-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    |
| `1.4.0-amd64-sk-sk-filip-preview`           | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           |
| `1.4.0-amd64-sl-si-lado-preview`            | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            |
| `1.4.0-amd64-sv-se-hedvigrus-preview`       | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       |
| `1.4.0-amd64-ta-in-valluvar-preview`        | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        |
| `1.4.0-amd64-te-in-chitra-preview`          | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          |
| `1.4.0-amd64-th-th-pattara-preview`         | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         |
| `1.4.0-amd64-tr-tr-sedarus-preview`         | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         |
| `1.4.0-amd64-vi-vn-an-preview`              | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              |
| `1.4.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.4.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.4.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `1.4.0-amd64-zh-hk-danny-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    |
| `1.4.0-amd64-zh-hk-tracy-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    |
| `1.4.0-amd64-zh-hk-tracyrus-preview`        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        |
| `1.4.0-amd64-zh-tw-hanhanrus-preview`       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       |
| `1.4.0-amd64-zh-tw-yating-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   |
| `1.4.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   |
| `1.3.0-amd64-ar-eg-hoda-preview`            | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        |
| `1.3.0-amd64-de-at-michael-preview`         | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         |
| `1.3.0-amd64-de-ch-karsten-preview`         | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         |
| `1.3.0-amd64-de-de-hedda-preview`           | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 具有地區設定和語音的容器映射 `de-DE` `de-DE-HeddaRUS` 。        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        |
| `1.3.0-amd64-en-au-catherine-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      |
| `1.3.0-amd64-en-ca-linda-preview`           | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `1.3.0-amd64-en-ie-sean-preview`            | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `1.3.0-amd64-en-in-priyarus-preview`        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-Jessa24kRUS` 。     |
| `1.3.0-amd64-en-us-jessarus-preview`        | 具有地區設定和語音的容器映射 `en-US` `en-US-JessaRUS` 。        |
| `1.3.0-amd64-en-us-zirarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.3.0-amd64-es-es-helenarus-preview`       | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `1.3.0-amd64-he-il-asaf-preview`            | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            |
| `1.3.0-amd64-hi-in-hemant-preview`          | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `1.3.0-amd64-hr-hr-matej-preview`           | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        |
| `1.3.0-amd64-id-id-andika-preview`          | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `1.3.0-amd64-it-it-luciarus-preview`        | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    |
| `1.3.0-amd64-sk-sk-filip-preview`           | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           |
| `1.3.0-amd64-sl-si-lado-preview`            | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        |
| `1.3.0-amd64-te-in-chitra-preview`          | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          |
| `1.3.0-amd64-th-th-pattara-preview`         | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         |
| `1.3.0-amd64-vi-vn-an-preview`              | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   |
| `1.2.0-amd64-de-de-hedda-preview`           | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.2.0-amd64-de-de-heddarus-preview`        | 具有地區設定和語音的容器映射 `de-DE` `de-DE-HeddaRUS` 。        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `1.2.0-amd64-en-au-catherine-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `1.2.0-amd64-en-in-priyarus-preview`        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-Jessa24kRUS` 。     |
| `1.2.0-amd64-en-us-jessarus-preview`        | 具有地區設定和語音的容器映射 `en-US` `en-US-JessaRUS` 。        |
| `1.2.0-amd64-en-us-zirarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.2.0-amd64-es-es-helenarus-preview`       | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `1.2.0-amd64-it-it-luciarus-preview`        | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `1.1.0-amd64-de-de-hedda-preview`           | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 具有地區設定和語音的容器映射 `de-DE` `de-DE-HeddaRUS` 。        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `1.1.0-amd64-en-au-catherine-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `1.1.0-amd64-en-in-priyarus-preview`        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-Jessa24kRUS` 。     |
| `1.1.0-amd64-en-us-jessarus-preview`        | 具有地區設定和語音的容器映射 `en-US` `en-US-JessaRUS` 。        |
| `1.1.0-amd64-en-us-zirarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.1.0-amd64-es-es-helenarus-preview`       | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `1.1.0-amd64-it-it-luciarus-preview`        | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | 具有地區設定和語音的容器映射 `en-US` `en-US-Jessa24kRUS` 。     |
| `1.0.0-amd64-en-us-jessarus-preview`        | 具有地區設定和語音的容器映射 `en-US` `en-US-JessaRUS` 。        |
| `1.0.0-amd64-en-us-zirarus-preview`         | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |

## <a name="neural-text-to-speech"></a>神經文字轉換語音

您可以在 container registry 中找到 [類神經文字轉換語音] [sp ntts] 容器映射 `containerpreview.azurecr.io` 。 它位於存放 `microsoft` 庫中，並命名為 `cognitive-services-neural-text-to-speech` 。 完整的容器映射名稱是、 `containerpreview.azurecr.io/microsoft/cognitive-services-neural-text-to-speech` 。

此容器映射有下列可用的標記：

| 影像標記                                  | 注意                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `1.2.0-amd64-de-de-katjaneural-preview`     | 具有地區設定和語音的容器映射 `de-DE` `de-DE-KatjaNeural` 。     |
| `1.2.0-amd64-en-au-natashaneural-preview`   | 具有地區設定和語音的容器映射 `en-AU` `en-AU-NatashaNeural` 。   |
| `1.2.0-amd64-en-ca-claraneural-preview`     | 具有地區設定和語音的容器映射 `en-CA` `en-CA-ClaraNeural` 。     |
| `1.2.0-amd64-en-gb-libbyneural-preview`     | 具有地區設定和語音的容器映射 `en-GB` `en-GB-LibbyNeural` 。     |
| `1.2.0-amd64-en-gb-mianeural-preview`       | 具有地區設定和語音的容器映射 `en-GB` `en-GB-MiaNeural` 。       |
| `1.2.0-amd64-en-us-arianeural-preview`      | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `1.2.0-amd64-en-us-guyneural-preview`       | 具有地區設定和語音的容器映射 `en-US` `en-US-GuyNeural` 。       |
| `1.2.0-amd64-es-es-elviraneural-preview`    | 具有地區設定和語音的容器映射 `es-ES` `es-ES-ElviraNeural` 。    |
| `1.2.0-amd64-es-mx-dalianeural-preview`     | 具有地區設定和語音的容器映射 `es-MX` `es-MX-DaliaNeural` 。     |
| `1.2.0-amd64-fr-ca-sylvieneural-preview`    | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-SylvieNeural` 。    |
| `1.2.0-amd64-fr-fr-deniseneural-preview`    | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-DeniseNeural` 。    |
| `1.2.0-amd64-it-it-elsaneural-preview`      | 具有地區設定和語音的容器映射 `it-IT` `it-IT-ElsaNeural` 。      |
| `1.2.0-amd64-ja-jp-nanamineural-preview`    | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-NanamiNeural` 。    |
| `1.2.0-amd64-ko-kr-sunhineural-preview`     | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-SunHiNeural` 。     |
| `1.2.0-amd64-pt-br-franciscaneural-preview` | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-FranciscaNeural` 。 |
| `1.2.0-amd64-zh-cn-xiaoxiaoneural-preview`  | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-XiaoxiaoNeural` 。  |

## <a name="key-phrase-extraction"></a>關鍵片語擷取

您可以在 container registry 整合上找到 [關鍵片語擷取][ta-kp] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services` 庫中，並命名為 `keyphrase` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/keyphrase` 。

此容器映射有下列可用的標記：

| 影像標記                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>語言偵測

您可以在 container registry 整合上找到 [語言偵測][ta-la] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services` 庫中，並命名為 `language` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/language` 。

此容器映射有下列可用的標記：

| 影像標記                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>情感分析

您可以在 container registry 整合上找到 [情感分析][ta-se] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services` 庫中，並命名為 `sentiment` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/sentiment` 。

此容器映射有下列可用的標記：

| 影像標記 | 注意                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 情感分析 v3 (英文)                |
| `3.0-es`   | 情感分析 v3 (西班牙文)                |
| `3.0-fr`   | 情感分析 v3 (法文)                 |
| `3.0-it`   | 情感分析 v3 (義大利文)                |
| `3.0-de`   | 情感分析 v3 (德文)                 |
| `3.0-zh`   | 情感分析 v3 (簡體中文)   |
| `3.0-zht`  | 情感分析 v3 (繁體中文)  |
| `3.0-ja`   | 情感分析 v3 (日文)               |
| `3.0-pt`   | 情感分析 v3 (葡萄牙文)             |
| `3.0-nl`   | 情感分析 v3 (荷蘭)                  |
| `1.1.009301-amd64-preview`    | 情感分析 v2      |
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
