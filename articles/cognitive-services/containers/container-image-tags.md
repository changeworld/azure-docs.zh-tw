---
title: 認知服務容器映像標籤
titleSuffix: Azure Cognitive Services
description: 所有認知服務容器映射標記的完整清單。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 0efb278c217ede94f67b47250633622501a24414
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412539"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure 認知服務容器映射標記和版本資訊

Azure 認知服務提供許多容器映射。 容器的登錄和對應的存放庫會在容器映射之間有所不同。 每個容器映射名稱都會提供多個標記。 容器映射標記是一種管理容器映射的機制。 本文旨在作為列出所有認知服務容器映射及其可用標記的完整參考。

> [!TIP]
> 使用時 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) ，請密切注意容器登錄、儲存機制、容器映射名稱和對應標記的大小寫，因為它們會區分 **大小** 寫。

## <a name="anomaly-detector"></a>Anomaly Detector

您可以在 container registry 整合上找到 [異常][ad-containers] 偵測器容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/decision` 庫中，並命名為 `anomaly-detector` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[舊版](#tab/previous)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>讀取 OCR (光學字元辨識) 

您可以在 container registry 整合中找到 [電腦視覺][cv-containers] Read OCR 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services` 庫中，並命名為 `read` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/vision/read` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

的版本資訊 `v2.0.013250001-amd64-preview` ：

* 進一步減少容器的記憶體使用量。
* 需要外部快取以進行多 pod 設定。 例如，設定快取的 Redis。
* 修正 Redis 快取設定且設為0時的遺漏結果 `ResultExpirationPeriod` 。
* 移除26MB 的要求主體大小限制。 容器現在可以接受 >26MB 檔。
* 將時間戳記和組建版本新增至主控台記錄。

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[舊版](#tab/previous)

的版本資訊 `1.1.013050001-amd64-preview`

* 已新增 `ReadEngineConfig:ResultExpirationPeriod` 容器初始化設定，以指定系統何時應清除辨識結果。 
    * 設定是以小時為單位，預設值為48小時。
    * 此設定可以減少儲存結果的記憶體使用量，尤其是在使用容器記憶體中儲存體時。
    * 範例 1. ReadEngineConfig： ResultExpirationPeriod = 1，系統會在進程之後清除辨識結果1小時。
    * 如果此設定設為0，則系統會在抓取結果之後清除辨識結果。

* 修正將不正確影像格式傳遞給系統時的500內部伺服器錯誤。 它現在會傳回400錯誤：

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>表單辨識器

您可以在 container registry 整合上找到 [表單辨識器][fr-containers] 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/custom-form` 庫中，並命名為 `labeltool` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[舊版](#tab/previous)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>語言理解 (LUIS)

[LUIS][lu-containers]容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/language` 庫中，並命名為 `luis` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/language/luis` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[先前版本](#tab/previous)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>自訂語音轉換文字

您可以在 container registry 整合上找到 [自訂的語音轉換文字][sp-cstt] 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `custom-speech-to-text` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` 。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list)的完整清單。


# <a name="latest-version"></a>[最新版本](#tab/current)

的版本注意事項 `2.6.0-amd64` ：

**功能**
* Phraselist v2 的支援 
* 下列地區設定支援片語清單：
    * en-us
    * en-ca
    * en-gb
    * en-us
    * en-nz
    * zh-tw
    * zh-cn
* 自訂基底模型下載支援。 
    * 搭配 `BaseModelLocale=<locale>` 命令使用 `docker run`
* 完整遷移至 .NET 3。1

**修正**
* 修正信賴分數在 Diarization 模式中一律為1的問題
* 已遷移至 >microsoft.azure.cognitiveservices.language.textanalytics 3.0 api

請注意，由於包含的片語清單，此容器映射的大小已增加。

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[先前版本](#tab/previous)

的版本注意事項 `2.5.0-amd64` ：

**功能**
* 支援自訂模型上的自訂發音
* 支援 Azure 和 Azure 美國政府雲端

**修正**
* 在 Diarization 模式上將執行修正為非根使用者問題

| 影像標記                    | 備註               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   第1版 GA 版本    |

---

## <a name="custom-text-to-speech"></a>自訂文字轉換語音

[自訂文字轉換語音][sp-ctts]容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `custom-text-to-speech` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` 。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)的完整清單。


# <a name="latest-version"></a>[最新版本](#tab/current)

的版本注意事項 `1.8.0-amd64` ：

**功能**
* 完整遷移至 .NET 3。1

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[先前版本](#tab/previous)

的版本注意事項 `1.7.0-amd64` ：

**功能**
* 部分遷移至 .NET 3。1

| 影像標記                    | 備註               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   第1版 GA 版本    |

---

## <a name="speech-to-text"></a>語音轉文字

「 [語音轉換文字][sp-stt] 」容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `speech-to-text` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` 。 您可以 [在 MCR 上](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)找到完整的標記清單。

由於語音轉換文字的2.5.0， *美國政府弗吉尼亞州* 區域支援映射。 使用此區域時，請使用 *美國政府的佛吉尼亞* 帳單端點和 API 金鑰。

# <a name="latest-version"></a>[最新版本](#tab/current)

的版本注意事項 `2.6.0-amd64-<locale>` ：

**功能**
* 升級至最新的模型，並完整遷移至 .NET 3。1
* Phraselist v2 的支援
* 下列地區設定支援片語清單：
    * en-us
    * en-ca
    * en-gb
    * en-us
    * en-nz
    * zh-tw
    * zh-cn
* 新地區設定的支援 `cs-CZ` 
    * 目前不支援大小寫和標點符號。

**修正**
* 修正信賴分數在 Diarization 模式中一律為1的問題
* 已遷移使用 >microsoft.azure.cognitiveservices.language.textanalytics 3.0 API

請注意，由於包含的片語清單，此容器映射的大小已增加。 

| 影像標記                    | 備註                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | 具有地區設定的容器映射 `en-US` 。                                                             |
| `2.6.0-amd64-<locale>`        | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `2.6.0-amd64-en-us` 。 |

此容器具有下列可用的地區設定。

| V 2.6.0 的地區設定           | 備註                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | 具有地區設定的容器映射 `ar-AE` 。 |
| `ar-eg`                     | 具有地區設定的容器映射 `ar-EG` 。 |
| `ar-kw`                     | 具有地區設定的容器映射 `ar-KW` 。 |
| `ar-qa`                     | 具有地區設定的容器映射 `ar-QA` 。 |
| `ar-sa`                     | 具有地區設定的容器映射 `ar-SA` 。 |
| `ca-es`                     | 具有地區設定的容器映射 `ca-ES` 。 |
| `cs-cz`                     | 具有地區設定的容器映射 `cs-CZ` 。 |
| `da-dk`                     | 具有地區設定的容器映射 `da-DK` 。 |
| `de-de`                     | 具有地區設定的容器映射 `de-DE` 。 |
| `en-au`                     | 具有地區設定的容器映射 `en-AU` 。 |
| `en-ca`                     | 具有地區設定的容器映射 `en-CA` 。 |
| `en-gb`                     | 具有地區設定的容器映射 `en-GB` 。 |
| `en-in`                     | 具有地區設定的容器映射 `en-IN` 。 |
| `en-nz`                     | 具有地區設定的容器映射 `en-NZ` 。 |
| `en-us`                     | 具有地區設定的容器映射 `en-US` 。 |
| `es-es`                     | 具有地區設定的容器映射 `es-ES` 。 |
| `es-mx`                     | 具有地區設定的容器映射 `es-MX` 。 |
| `fi-fi`                     | 具有地區設定的容器映射 `fi-FI` 。 |
| `fr-ca`                     | 具有地區設定的容器映射 `fr-CA` 。 |
| `fr-fr`                     | 具有地區設定的容器映射 `fr-FR` 。 |
| `gu-in`                     | 具有地區設定的容器映射 `gu-IN` 。 |
| `hi-in`                     | 具有地區設定的容器映射 `hi-IN` 。 |
| `it-it`                     | 具有地區設定的容器映射 `it-IT` 。 |
| `ja-jp`                     | 具有地區設定的容器映射 `ja-JP` 。 |
| `ko-kr`                     | 具有地區設定的容器映射 `ko-KR` 。 |
| `mr-in`                     | 具有地區設定的容器映射 `mr-IN` 。 |
| `nb-no`                     | 具有地區設定的容器映射 `nb-NO` 。 |
| `nl-nl`                     | 具有地區設定的容器映射 `nl-NL` 。 |
| `pl-pl`                     | 具有地區設定的容器映射 `pl-PL` 。 |
| `pt-br`                     | 具有地區設定的容器映射 `pt-BR` 。 |
| `pt-pt`                     | 具有地區設定的容器映射 `pt-PT` 。 |
| `ru-ru`                     | 具有地區設定的容器映射 `ru-RU` 。 |
| `sv-se`                     | 具有地區設定的容器映射 `sv-SE` 。 |
| `ta-in`                     | 具有地區設定的容器映射 `ta-IN` 。 |
| `te-in`                     | 具有地區設定的容器映射 `te-IN` 。 |
| `th-th`                     | 具有地區設定的容器映射 `th-TH` 。 |
| `tr-tr`                     | 具有地區設定的容器映射 `tr-TR` 。 |
| `zh-cn`                     | 具有地區設定的容器映射 `zh-CN` 。 |
| `zh-hk`                     | 具有地區設定的容器映射 `zh-HK` 。 |
| `zh-tw`                     | 具有地區設定的容器映射 `zh-TW` 。 |


# <a name="previous-version"></a>[先前版本](#tab/previous)

的版本注意事項 `2.5.0-amd64-<locale>` ：

**功能**
* 支援 Azure 美國政府雲端

**修正**
* 修正在 Diarization 模式中做為非根使用者執行的問題

| 影像標記                  | 備註                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `2.5.0-amd64-en-us` 。  |

此容器具有下列可用的地區設定。

| V 2.5.0 的地區設定           | 備註                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | 具有地區設定的容器映射 `ar-AE` 。 |
| `ar-eg`                     | 具有地區設定的容器映射 `ar-EG` 。 |
| `ar-kw`                     | 具有地區設定的容器映射 `ar-KW` 。 |
| `ar-qa`                     | 具有地區設定的容器映射 `ar-QA` 。 |
| `ar-sa`                     | 具有地區設定的容器映射 `ar-SA` 。 |
| `ca-es`                     | 具有地區設定的容器映射 `ca-ES` 。 |
| `da-dk`                     | 具有地區設定的容器映射 `da-DK` 。 |
| `de-de`                     | 具有地區設定的容器映射 `de-DE` 。 |
| `en-au`                     | 具有地區設定的容器映射 `en-AU` 。 |
| `en-ca`                     | 具有地區設定的容器映射 `en-CA` 。 |
| `en-gb`                     | 具有地區設定的容器映射 `en-GB` 。 |
| `en-in`                     | 具有地區設定的容器映射 `en-IN` 。 |
| `en-nz`                     | 具有地區設定的容器映射 `en-NZ` 。 |
| `en-us`                     | 具有地區設定的容器映射 `en-US` 。 |
| `es-es`                     | 具有地區設定的容器映射 `es-ES` 。 |
| `es-mx`                     | 具有地區設定的容器映射 `es-MX` 。 |
| `fi-fi`                     | 具有地區設定的容器映射 `fi-FI` 。 |
| `fr-ca`                     | 具有地區設定的容器映射 `fr-CA` 。 |
| `fr-fr`                     | 具有地區設定的容器映射 `fr-FR` 。 |
| `gu-in`                     | 具有地區設定的容器映射 `gu-IN` 。 |
| `hi-in`                     | 具有地區設定的容器映射 `hi-IN` 。 |
| `it-it`                     | 具有地區設定的容器映射 `it-IT` 。 |
| `ja-jp`                     | 具有地區設定的容器映射 `ja-JP` 。 |
| `ko-kr`                     | 具有地區設定的容器映射 `ko-KR` 。 |
| `mr-in`                     | 具有地區設定的容器映射 `mr-IN` 。 |
| `nb-no`                     | 具有地區設定的容器映射 `nb-NO` 。 |
| `nl-nl`                     | 具有地區設定的容器映射 `nl-NL` 。 |
| `pl-pl`                     | 具有地區設定的容器映射 `pl-PL` 。 |
| `pt-br`                     | 具有地區設定的容器映射 `pt-BR` 。 |
| `pt-pt`                     | 具有地區設定的容器映射 `pt-PT` 。 |
| `ru-ru`                     | 具有地區設定的容器映射 `ru-RU` 。 |
| `sv-se`                     | 具有地區設定的容器映射 `sv-SE` 。 |
| `ta-in`                     | 具有地區設定的容器映射 `ta-IN` 。 |
| `te-in`                     | 具有地區設定的容器映射 `te-IN` 。 |
| `th-th`                     | 具有地區設定的容器映射 `th-TH` 。 |
| `tr-tr`                     | 具有地區設定的容器映射 `tr-TR` 。 |
| `zh-cn`                     | 具有地區設定的容器映射 `zh-CN` 。 |
| `zh-hk`                     | 具有地區設定的容器映射 `zh-HK` 。 |
| `zh-tw`                     | 具有地區設定的容器映射 `zh-TW` 。 |

---

## <a name="text-to-speech"></a>文字轉換語音

[文字轉換語音][sp-tts]容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `text-to-speech` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list)的完整清單。


# <a name="latest-version"></a>[最新版本](#tab/current)

的版本注意事項 `1.8.0-amd64-<locale-and-voice>` ：

**功能**
* 完整遷移至 .NET 3。1

| 影像標記                                  | 備註                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.8.0-amd64-en-us-ariarus` 。  |


| V 1.8.0 的地區設定                          | 備註                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            |
| `ar-sa-naayf`                               | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           |
| `bg-bg-ivan`                                | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            |
| `ca-es-herenarus`                           | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       |
| `cs-cz-jakub`                               | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           |
| `da-dk-hellerus`                            | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        |
| `de-at-michael`                             | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         |
| `de-ch-karsten`                             | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         |
| `de-de-hedda`                               | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `de-de-heddarus`                            | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `de-de-stefan-apollo`                       | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `el-gr-stefanos`                            | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        |
| `en-au-catherine`                           | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `en-au-hayleyrus`                           | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `en-ca-heatherrus`                          | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      |
| `en-ca-linda`                               | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           |
| `en-gb-george-apollo`                       | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `en-gb-hazelrus`                            | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `en-gb-susan-apollo`                        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `en-ie-sean`                                | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            |
| `en-in-heera-apollo`                        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `en-in-priyarus`                            | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `en-in-ravi-apollo`                         | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `en-us-benjaminrus`                         | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `en-us-guy24krus`                           | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `en-us-aria24krus`                          | 具有地區設定和語音的容器映射 `en-US` `en-US-Aria24kRUS` 。      |
| `en-us-ariarus`                             | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。         |
| `en-us-zirarus`                             | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `es-es-helenarus`                           | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `es-es-laura-apollo`                        | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `es-es-pablo-apollo`                        | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `es-mx-hildarus`                            | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `es-mx-raul-apollo`                         | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `fi-fi-heidirus`                            | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        |
| `fr-ca-caroline`                            | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `fr-ca-harmonierus`                         | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `fr-ch-guillaume`                           | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       |
| `fr-fr-hortenserus`                         | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `fr-fr-julie-apollo`                        | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `fr-fr-paul-apollo`                         | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `he-il-asaf`                                | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            |
| `hi-in-hemant`                              | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          |
| `hi-in-kalpana-apollo`                      | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana-Apollo` 。  |
| `hi-in-kalpana`                             | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `hr-hr-matej`                               | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           |
| `hu-hu-szabolcs`                            | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        |
| `id-id-andika`                              | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          |
| `it-it-cosimo-apollo`                       | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `it-it-luciarus`                            | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `ja-jp-ayumi-apollo`                        | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `ja-jp-harukarus`                           | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `ja-jp-ichiro-apollo`                       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `ko-kr-heamirus`                            | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `ms-my-rizwan`                              | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          |
| `nb-no-huldarus`                            | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        |
| `nl-nl-hannarus`                            | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        |
| `pl-pl-paulinarus`                          | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      |
| `pt-br-daniel-apollo`                       | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `pt-br-heloisarus`                          | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `pt-pt-heliarus`                            | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        |
| `ro-ro-andrei`                              | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          |
| `ru-ru-ekaterinarus`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    |
| `ru-ru-irina-apollo`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    |
| `ru-ru-pavel-apollo`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    |
| `sk-sk-filip`                               | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           |
| `sl-si-lado`                                | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            |
| `sv-se-hedvigrus`                           | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       |
| `ta-in-valluvar`                            | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        |
| `te-in-chitra`                              | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          |
| `th-th-pattara`                             | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         |
| `tr-tr-sedarus`                             | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         |
| `vi-vn-an`                                  | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              |
| `zh-cn-huihuirus`                           | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `zh-cn-kangkang-apollo`                     | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `zh-cn-yaoyao-apollo`                       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `zh-hk-danny-apollo`                        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    |
| `zh-hk-tracy-apollo`                        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    |
| `zh-hk-tracyrus`                            | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        |
| `zh-tw-hanhanrus`                           | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       |
| `zh-tw-yating-apollo`                       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   |
| `zh-tw-zhiwei-apollo`                       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   |


# <a name="previous-version"></a>[先前版本](#tab/previous)

的版本注意事項 `1.7.0-amd64-<locale-and-voice>` ：

**功能**
* 升級至 .NET 3.1 的元件

| 影像標記                                  | 備註                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | 第1版 GA 版本。 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.7.0-amd64-en-us-ariarus` 。  |


| V 1.7.0 的地區設定                          | 備註                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            |
| `ar-sa-naayf`                               | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           |
| `bg-bg-ivan`                                | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            |
| `ca-es-herenarus`                           | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       |
| `cs-cz-jakub`                               | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           |
| `da-dk-hellerus`                            | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        |
| `de-at-michael`                             | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         |
| `de-ch-karsten`                             | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         |
| `de-de-hedda`                               | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `de-de-heddarus`                            | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           |
| `de-de-stefan-apollo`                       | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   |
| `el-gr-stefanos`                            | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        |
| `en-au-catherine`                           | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       |
| `en-au-hayleyrus`                           | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       |
| `en-ca-heatherrus`                          | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      |
| `en-ca-linda`                               | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           |
| `en-gb-george-apollo`                       | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   |
| `en-gb-hazelrus`                            | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        |
| `en-gb-susan-apollo`                        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    |
| `en-ie-sean`                                | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            |
| `en-in-heera-apollo`                        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    |
| `en-in-priyarus`                            | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        |
| `en-in-ravi-apollo`                         | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     |
| `en-us-benjaminrus`                         | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     |
| `en-us-guy24krus`                           | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       |
| `en-us-aria24krus`                          | 具有地區設定和語音的容器映射 `en-US` `en-US-Aria24kRUS` 。      |
| `en-us-ariarus`                             | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。         |
| `en-us-zirarus`                             | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         |
| `es-es-helenarus`                           | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       |
| `es-es-laura-apollo`                        | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    |
| `es-es-pablo-apollo`                        | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    |
| `es-mx-hildarus`                            | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        |
| `es-mx-raul-apollo`                         | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     |
| `fi-fi-heidirus`                            | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        |
| `fr-ca-caroline`                            | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        |
| `fr-ca-harmonierus`                         | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     |
| `fr-ch-guillaume`                           | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       |
| `fr-fr-hortenserus`                         | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     |
| `fr-fr-julie-apollo`                        | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    |
| `fr-fr-paul-apollo`                         | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     |
| `he-il-asaf`                                | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            |
| `hi-in-hemant`                              | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          |
| `hi-in-kalpana-apollo`                      | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana-Apollo` 。  |
| `hi-in-kalpana`                             | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         |
| `hr-hr-matej`                               | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           |
| `hu-hu-szabolcs`                            | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        |
| `id-id-andika`                              | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          |
| `it-it-cosimo-apollo`                       | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   |
| `it-it-luciarus`                            | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        |
| `ja-jp-ayumi-apollo`                        | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    |
| `ja-jp-harukarus`                           | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       |
| `ja-jp-ichiro-apollo`                       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   |
| `ko-kr-heamirus`                            | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        |
| `ms-my-rizwan`                              | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          |
| `nb-no-huldarus`                            | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        |
| `nl-nl-hannarus`                            | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        |
| `pl-pl-paulinarus`                          | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      |
| `pt-br-daniel-apollo`                       | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   |
| `pt-br-heloisarus`                          | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      |
| `pt-pt-heliarus`                            | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        |
| `ro-ro-andrei`                              | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          |
| `ru-ru-ekaterinarus`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    |
| `ru-ru-irina-apollo`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    |
| `ru-ru-pavel-apollo`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    |
| `sk-sk-filip`                               | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           |
| `sl-si-lado`                                | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            |
| `sv-se-hedvigrus`                           | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       |
| `ta-in-valluvar`                            | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        |
| `te-in-chitra`                              | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          |
| `th-th-pattara`                             | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         |
| `tr-tr-sedarus`                             | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         |
| `vi-vn-an`                                  | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              |
| `zh-cn-huihuirus`                           | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       |
| `zh-cn-kangkang-apollo`                     | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 |
| `zh-cn-yaoyao-apollo`                       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   |
| `zh-hk-danny-apollo`                        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    |
| `zh-hk-tracy-apollo`                        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    |
| `zh-hk-tracyrus`                            | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        |
| `zh-tw-hanhanrus`                           | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       |
| `zh-tw-yating-apollo`                       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   |
| `zh-tw-zhiwei-apollo`                       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   |

---

## <a name="neural-text-to-speech"></a>神經文字轉換語音

您可以在 container registry 聯合中找到類 [神經文字轉換語音][sp-ntts] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `neural-text-to-speech` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list)的完整清單。

| 影像標記                                  | 備註                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.2.0-amd64-en-us-arianeural-preview` 。 |


| v 1.2.0 Preview 地區設定和語音           | 備註                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `de-de-katjaneural-preview`                 | 具有地區設定和語音的容器映射 `de-DE` `de-DE-KatjaNeural` 。     |
| `en-au-natashaneural-preview`               | 具有地區設定和語音的容器映射 `en-AU` `en-AU-NatashaNeural` 。   |
| `en-ca-claraneural-preview`                 | 具有地區設定和語音的容器映射 `en-CA` `en-CA-ClaraNeural` 。     |
| `en-gb-libbyneural-preview`                 | 具有地區設定和語音的容器映射 `en-GB` `en-GB-LibbyNeural` 。     |
| `en-gb-mianeural-preview`                   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-MiaNeural` 。       |
| `en-us-arianeural-preview`                  | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `en-us-guyneural-preview`                   | 具有地區設定和語音的容器映射 `en-US` `en-US-GuyNeural` 。       |
| `es-es-elviraneural-preview`                | 具有地區設定和語音的容器映射 `es-ES` `es-ES-ElviraNeural` 。    |
| `es-mx-dalianeural-preview`                 | 具有地區設定和語音的容器映射 `es-MX` `es-MX-DaliaNeural` 。     |
| `fr-ca-sylvieneural-preview`                | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-SylvieNeural` 。    |
| `fr-fr-deniseneural-preview`                | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-DeniseNeural` 。    |
| `it-it-elsaneural-preview`                  | 具有地區設定和語音的容器映射 `it-IT` `it-IT-ElsaNeural` 。      |
| `ja-jp-nanamineural-preview`                | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-NanamiNeural` 。    |
| `ko-kr-sunhineural-preview`                 | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-SunHiNeural` 。     |
| `pt-br-franciscaneural-preview`             | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-FranciscaNeural` 。 |
| `zh-cn-xiaoxiaoneural-preview`              | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-XiaoxiaoNeural` 。  |

## <a name="speech-language-detection"></a>語音語言偵測

您可以在 container registry 聯合中找到 [語音語言偵測][sp-lid] 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `language-detection` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list)的完整清單。

| 影像標記                                  | 備註                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>關鍵片語擷取

容器映射可以在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/textanalytics/` 庫中，並命名為 `keyphrase` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)


| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[舊版](#tab/previous)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>文字語言偵測

您可以在 container registry 整合上找到 [語言偵測][ta-la] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/textanalytics/` 庫中，並命名為 `language` 。 完整的容器映射名稱為、 `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)的完整清單。

# <a name="latest-versions"></a>[最新版本](#tab/current)

| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[舊版](#tab/previous)


| 影像標記                    | 備註 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>情感分析

您可以在 container registry 整合上找到 [情感分析][ta-se] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/textanalytics/` 庫中，並命名為 `sentiment` 。 完整的容器映射名稱為、 `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)的完整清單。

| 影像標記 | 備註                                         |
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
| `2.1`    | 情感分析 v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
