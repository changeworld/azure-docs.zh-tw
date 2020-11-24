---
title: 認知服務容器映像標籤
titleSuffix: Azure Cognitive Services
description: 所有認知服務容器映射標記的完整清單。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: 09a83c28d07540b8ecd813e7ab2f10ceee891d7a
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95792978"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure 認知服務容器映射標記和版本資訊

Azure 認知服務提供許多容器映射。 容器的登錄和對應的存放庫會在容器映射之間有所不同。 每個容器映射名稱都會提供多個標記。 容器映射標記是一種管理容器映射的機制。 本文旨在作為列出所有認知服務容器映射及其可用標記的完整參考。

> [!TIP]
> 使用時 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) ，請密切注意容器登錄、儲存機制、容器映射名稱和對應標記的大小寫，因為它們會區分 **大小** 寫。

## <a name="anomaly-detector"></a>Anomaly Detector

您可以在 container registry 整合上找到 [異常][ad-containers] 偵測器容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/decision` 庫中，並命名為 `anomaly-detector` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[舊版](#tab/previous)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>讀取 OCR (光學字元辨識) 

您可以在 container registry 整合中找到 [電腦視覺][cv-containers] Read OCR 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services` 庫中，並命名為 `read` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/vision/read` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

的版本資訊 `3.2-preview.1` ：

* 新的3.2 容器

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `latest`                      |       |
| `3.2-preview.1` |  |

# <a name="previous-versions"></a>[舊版](#tab/previous)

的版本資訊 `v2.0.013250001-amd64-preview` ：

* 進一步減少容器的記憶體使用量。
* 需要外部快取以進行多 pod 設定。 例如，設定快取的 Redis。
* 修正 Redis 快取設定且設為0時的遺漏結果 `ResultExpirationPeriod` 。
* 移除26MB 的要求主體大小限制。 容器現在可以接受 >26MB 檔。
* 將時間戳記和組建版本新增至主控台記錄。

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

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `2.0.013250001-amd64-preview` |       |
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>表單辨識器

您可以在 container registry 整合上找到 [表單辨識器][fr-containers] 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/custom-form` 庫中，並命名為 `labeltool` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[舊版](#tab/previous)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>語言理解 (LUIS)

[LUIS][lu-containers]容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/language` 庫中，並命名為 `luis` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/language/luis` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[先前版本](#tab/previous)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>自訂語音轉換文字

您可以在 container registry 整合上找到 [自訂的語音轉換文字][sp-cstt] 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `custom-speech-to-text` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` 。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list)的完整清單。


# <a name="latest-version"></a>[最新版本](#tab/current)

的版本注意事項 `2.7.0-amd64` ：

**功能**
* 預設會將標點符號設定為 enabled。

請注意，由於包含的片語清單，此容器映射的大小已增加。

| 影像標記                    | 備忘稿 | Digest                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |
| `2.7.0-amd64`                 |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |


# <a name="previous-version"></a>[先前版本](#tab/previous)
的版本注意事項 `2.6.0-amd64` ：

**功能**
* Phraselist v2 的支援 
* 下列地區設定支援片語清單：
    * en-us
    * en-ca
    * en-gb
    * en-us
    * zh-tw
    * zh-cn
* 自訂基底模型下載支援。 
    * 搭配 `BaseModelLocale=<locale>` 命令使用 `docker run`
* 完整遷移至 .NET 3。1

**修正**
* 修正信賴分數在 Diarization 模式中一律為1的問題
* 已遷移至 >microsoft.azure.cognitiveservices.language.textanalytics 3.0 api

請注意，由於包含的片語清單，此容器映射的大小已增加。

的版本注意事項 `2.5.0-amd64` ：

**功能**
* 支援自訂模型上的自訂發音
* 支援 Azure 和 Azure 美國政府雲端

**修正**
* 在 Diarization 模式上將執行修正為非根使用者問題

| 影像標記                    | 備忘稿               |
|-------------------------------|:--------------------|
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   第1版 GA 版本    |

---

## <a name="custom-text-to-speech"></a>自訂文字轉換語音

[自訂文字轉換語音][sp-ctts]容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `custom-text-to-speech` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` 。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)的完整清單。


# <a name="latest-version"></a>[最新版本](#tab/current)

的版本注意事項 `1.9.0-amd64` ：

一般每月發行

| 影像標記                    | 備忘稿 | Digest                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |
| `1.9.0-amd64`                 |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |


# <a name="previous-version"></a>[先前版本](#tab/previous)
的版本注意事項 `1.8.0-amd64` ：

**功能**
* 完整遷移至 .NET 3。1

的版本注意事項 `1.7.0-amd64` ：

**功能**
* 部分遷移至 .NET 3。1

| 影像標記                    | 備忘稿               |
|-------------------------------|:--------------------|
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   第1版 GA 版本    |

---

## <a name="speech-to-text"></a>語音轉文字

「 [語音轉換文字][sp-stt] 」容器映射可在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `speech-to-text` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` 。 您可以 [在 MCR 上](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)找到完整的標記清單。

由於語音轉換文字的2.5.0， *美國政府弗吉尼亞州* 區域支援映射。 使用此區域時，請使用 *美國政府的佛吉尼亞* 帳單端點和 API 金鑰。

# <a name="latest-version"></a>[最新版本](#tab/current)

的版本注意事項 `2.7.0-amd64-<locale>` ：

**功能**
* 支援下列新的地區設定：
    * ar-bh、ar-iq、ar-jo、ar-lb、ar-om、ar-sy
    * bg-bg
    * el-gr
    * en-us、en-us、en-us、en-sg、en-za
    * es-ar、es、es、cl、es-co、es-cr、es、es、es、es、es、es、es、es、es-.py、es-sv、es、es-uy、es-ve。
    * et-ee
    * ga-ie
    * hr-hr
    * hu-hu
    * lt-lt
    * lv-lv
    * mt-mt
    * ro-ro
    * sk-sk
    * sl-sl
* 預設會啟用標點符號。

請注意，由於包含的片語清單，此容器映射的大小已增加。 

| 影像標記                    | 備忘稿                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | 具有地區設定的容器映射 `en-US` 。                                                             |
| `2.7.0-amd64-<locale>`        | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `2.7.0-amd64-en-us` 。 |

此容器具有下列可用的地區設定。

| V 2.7.0 的地區設定           | 備忘稿                                    | Digest                                                                  |
|-----------------------------|:-----------------------------------------|:------------------------------------------------------------------------|
| `ar-ae`                     | 具有地區設定的容器映射 `ar-AE` 。 | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | 具有地區設定的容器映射 `ar-BH` 。 | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | 具有地區設定的容器映射 `ar-EG` 。 | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | 具有地區設定的容器映射 `ar-IQ` 。 | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | 具有地區設定的容器映射 `ar-JO` 。 | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | 具有地區設定的容器映射 `ar-KW` 。 | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | 具有地區設定的容器映射 `ar-LB` 。 | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | 具有地區設定的容器映射 `ar-OM` 。 | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | 具有地區設定的容器映射 `ar-QA` 。 | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | 具有地區設定的容器映射 `ar-SA` 。 | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | 具有地區設定的容器映射 `ar-SY` 。 | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | 具有地區設定的容器映射 `bg-BG` 。 | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | 具有地區設定的容器映射 `ca-ES` 。 | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | 具有地區設定的容器映射 `cs-CZ` 。 | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | 具有地區設定的容器映射 `da-DK` 。 | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | 具有地區設定的容器映射 `de-DE` 。 | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | 具有地區設定的容器映射 `el-GR` 。 | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | 具有地區設定的容器映射 `en-AU` 。 | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | 具有地區設定的容器映射 `en-CA` 。 | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | 具有地區設定的容器映射 `en-GB` 。 | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | 具有地區設定的容器映射 `en-HK` 。 | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | 具有地區設定的容器映射 `en-IE` 。 | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | 具有地區設定的容器映射 `en-IN` 。 | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | 具有地區設定的容器映射 `en-NZ` 。 | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | 具有地區設定的容器映射 `en-PH` 。 | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | 具有地區設定的容器映射 `en-SG` 。 | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | 具有地區設定的容器映射 `en-US` 。 | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | 具有地區設定的容器映射 `en-ZA` 。 | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | 具有地區設定的容器映射 `es-AR` 。 | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | 具有地區設定的容器映射 `es-BO` 。 | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | 具有地區設定的容器映射 `es-CL` 。 | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | 具有地區設定的容器映射 `es-CO` 。 | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | 具有地區設定的容器映射 `es-CR` 。 | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | 具有地區設定的容器映射 `es-CU` 。 | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | 具有地區設定的容器映射 `es-DO` 。 | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | 具有地區設定的容器映射 `es-EC` 。 | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | 具有地區設定的容器映射 `es-ES` 。 | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | 具有地區設定的容器映射 `es-GT` 。 | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | 具有地區設定的容器映射 `es-HN` 。 | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | 具有地區設定的容器映射 `es-MX` 。 | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | 具有地區設定的容器映射 `es-NI` 。 | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | 具有地區設定的容器映射 `es-PA` 。 | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | 具有地區設定的容器映射 `es-PE` 。 | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | 具有地區設定的容器映射 `es-PR` 。 | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | 具有地區設定的容器映射 `es-PY` 。 | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | 具有地區設定的容器映射 `es-SV` 。 | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | 具有地區設定的容器映射 `es-US` 。 | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | 具有地區設定的容器映射 `es-UY` 。 | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | 具有地區設定的容器映射 `es-VE` 。 | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | 具有地區設定的容器映射 `et-EE` 。 | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | 具有地區設定的容器映射 `fi-FI` 。 | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | 具有地區設定的容器映射 `fr-CA` 。 | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | 具有地區設定的容器映射 `fr-FR` 。 | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | 具有地區設定的容器映射 `ga-IE` 。 | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | 具有地區設定的容器映射 `gu-IN` 。 | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | 具有地區設定的容器映射 `hi-IN` 。 | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | 具有地區設定的容器映射 `hr-HR` 。 | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | 具有地區設定的容器映射 `hu-HU` 。 | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | 具有地區設定的容器映射 `it-IT` 。 | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | 具有地區設定的容器映射 `ja-JP` 。 | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | 具有地區設定的容器映射 `ko-KR` 。 | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | 具有地區設定的容器映射 `lt-LT` 。 | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | 具有地區設定的容器映射 `lv-LV` 。 | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | 具有地區設定的容器映射 `mr-IN` 。 | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | 具有地區設定的容器映射 `mt-MT` 。 | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | 具有地區設定的容器映射 `nb-NO` 。 | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | 具有地區設定的容器映射 `nl-NL` 。 | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | 具有地區設定的容器映射 `pl-PL` 。 | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | 具有地區設定的容器映射 `pt-BR` 。 | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | 具有地區設定的容器映射 `pt-PT` 。 | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | 具有地區設定的容器映射 `ro-RO` 。 | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | 具有地區設定的容器映射 `ru-RU` 。 | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | 具有地區設定的容器映射 `sk-SK` 。 | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | 具有地區設定的容器映射 `sl-SI` 。 | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | 具有地區設定的容器映射 `sv-SE` 。 | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | 具有地區設定的容器映射 `ta-IN` 。 | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | 具有地區設定的容器映射 `te-IN` 。 | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | 具有地區設定的容器映射 `th-TH` 。 | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | 具有地區設定的容器映射 `tr-TR` 。 | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | 具有地區設定的容器映射 `zh-CN` 。 | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | 具有地區設定的容器映射 `zh-HK` 。 | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | 具有地區設定的容器映射 `zh-TW` 。 | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |


# <a name="previous-version"></a>[先前版本](#tab/previous)

的版本注意事項 `2.6.0-amd64-<locale>` ：

**功能**
* 升級至最新的模型，並完整遷移至 .NET 3。1
* Phraselist v2 的支援
* 下列地區設定支援片語清單：
    * en-us
    * en-ca
    * en-gb
    * en-us
    * zh-tw
    * zh-cn
* 新地區設定的支援 `cs-CZ` 
    * 目前不支援大小寫和標點符號。

**修正**
* 修正信賴分數在 Diarization 模式中一律為1的問題
* 已遷移使用 >microsoft.azure.cognitiveservices.language.textanalytics 3.0 API

請注意，由於包含的片語清單，此容器映射的大小已增加。 

的版本注意事項 `2.5.0-amd64-<locale>` ：

**功能**
* 支援 Azure 美國政府雲端

**修正**
* 修正在 Diarization 模式中做為非根使用者執行的問題

| 影像標記                  | 備忘稿                                    |
|-----------------------------|:-----------------------------------------|
| `2.6.0-amd64-<locale>`      | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `2.6.0-amd64-en-us` 。 |
| `2.5.0-amd64-<locale>`      | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `2.5.0-amd64-en-us` 。 |


此容器具有下列可用的地區設定。

| V 2.6.0 的地區設定           | 備忘稿                                    |
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

| V 2.5.0 的地區設定           | 備忘稿                                    |
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

的版本注意事項 `1.9.0-amd64-<locale-and-voice>` ：

* 一般每月發行

| 影像標記                                  | 備忘稿                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。                                            | 
| `1.9.0-amd64-<locale-and-voice>`            | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.9.0-amd64-en-us-ariarus` 。  |


| V 1.9.0 的地區設定                          | 備忘稿                                                                      | Digest                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | 具有地區設定和語音的容器映射 `ar-EG` `ar-EG-Hoda` 。            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | 具有地區設定和語音的容器映射 `ar-SA` `ar-SA-Naayf` 。           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | 具有地區設定和語音的容器映射 `bg-BG` `bg-BG-Ivan` 。            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | 具有地區設定和語音的容器映射 `ca-ES` `ca-ES-HerenaRUS` 。       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | 具有地區設定和語音的容器映射 `cs-CZ` `cs-CZ-Jakub` 。           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | 具有地區設定和語音的容器映射 `da-DK` `da-DK-HelleRUS` 。        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | 具有地區設定和語音的容器映射 `de-AT` `de-AT-Michael` 。         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | 具有地區設定和語音的容器映射 `de-CH` `de-CH-Karsten` 。         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Hedda` 。           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | 具有地區設定和語音的容器映射 `de-DE` `de-DE-Stefan-Apollo` 。   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | 具有地區設定和語音的容器映射 `el-GR` `el-GR-Stefanos` 。        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | 具有地區設定和語音的容器映射 `en-AU` `en-AU-Catherine` 。       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | 具有地區設定和語音的容器映射 `en-AU` `en-AU-HayleyRUS` 。       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | 具有地區設定和語音的容器映射 `en-CA` `en-CA-HeatherRUS` 。      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | 具有地區設定和語音的容器映射 `en-CA` `en-CA-Linda` 。           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | 具有地區設定和語音的容器映射 `en-GB` `en-GB-George-Apollo` 。   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | 具有地區設定和語音的容器映射 `en-GB` `en-GB-HazelRUS` 。        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | 具有地區設定和語音的容器映射 `en-GB` `en-GB-Susan-Apollo` 。    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | 具有地區設定和語音的容器映射 `en-IE` `en-IE-Sean` 。            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Heera-Apollo` 。    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | 具有地區設定和語音的容器映射 `en-IN` `en-IN-PriyaRUS` 。        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | 具有地區設定和語音的容器映射 `en-IN` `en-IN-Ravi-Apollo` 。     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | 具有地區設定和語音的容器映射 `en-US` `en-US-Aria24kRUS` 。      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaRUS` 。         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | 具有地區設定和語音的容器映射 `en-US` `en-US-BenjaminRUS` 。     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | 具有地區設定和語音的容器映射 `en-US` `en-US-Guy24kRUS` 。       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | 具有地區設定和語音的容器映射 `en-US` `en-US-ZiraRUS` 。         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | 具有地區設定和語音的容器映射 `es-ES` `es-ES-HelenaRUS` 。       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Laura-Apollo` 。    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | 具有地區設定和語音的容器映射 `es-ES` `es-ES-Pablo-Apollo` 。    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | 具有地區設定和語音的容器映射 `es-MX` `es-MX-HildaRUS` 。        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | 具有地區設定和語音的容器映射 `es-MX` `es-MX-Raul-Apollo` 。     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | 具有地區設定和語音的容器映射 `fi-FI` `fi-FI-HeidiRUS` 。        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-Caroline` 。        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-HarmonieRUS` 。     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | 具有地區設定和語音的容器映射 `fr-CH` `fr-CH-Guillaume` 。       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-HortenseRUS` 。     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Julie-Apollo` 。    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-Paul-Apollo` 。     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | 具有地區設定和語音的容器映射 `he-IL` `he-IL-Asaf` 。            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Hemant` 。          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana-Apollo` 。  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | 具有地區設定和語音的容器映射 `hi-IN` `hi-IN-Kalpana` 。         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | 具有地區設定和語音的容器映射 `hr-HR` `hr-HR-Matej` 。           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | 具有地區設定和語音的容器映射 `hu-HU` `hu-HU-Szabolcs` 。        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | 具有地區設定和語音的容器映射 `id-ID` `id-ID-Andika` 。          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | 具有地區設定和語音的容器映射 `it-IT` `it-IT-Cosimo-Apollo` 。   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | 具有地區設定和語音的容器映射 `it-IT` `it-IT-LuciaRUS` 。        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ayumi-Apollo` 。    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-HarukaRUS` 。       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-Ichiro-Apollo` 。   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-HeamiRUS` 。        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | 具有地區設定和語音的容器映射 `ms-MY` `ms-MY-Rizwan` 。          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | 具有地區設定和語音的容器映射 `nb-NO` `nb-NO-HuldaRUS` 。        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | 具有地區設定和語音的容器映射 `nl-NL` `nl-NL-HannaRUS` 。        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | 具有地區設定和語音的容器映射 `pl-PL` `pl-PL-PaulinaRUS` 。      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-Daniel-Apollo` 。   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-HeloisaRUS` 。      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | 具有地區設定和語音的容器映射 `pt-PT` `pt-PT-HeliaRUS` 。        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | 具有地區設定和語音的容器映射 `ro-RO` `ro-RO-Andrei` 。          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-EkaterinaRUS` 。    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Irina-Apollo` 。    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | 具有地區設定和語音的容器映射 `ru-RU` `ru-RU-Pavel-Apollo` 。    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | 具有地區設定和語音的容器映射 `sk-SK` `sk-SK-Filip` 。           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | 具有地區設定和語音的容器映射 `sl-SI` `sl-SI-Lado` 。            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | 具有地區設定和語音的容器映射 `sv-SE` `sv-SE-HedvigRUS` 。       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | 具有地區設定和語音的容器映射 `ta-IN` `ta-IN-Valluvar` 。        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | 具有地區設定和語音的容器映射 `te-IN` `te-IN-Chitra` 。          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | 具有地區設定和語音的容器映射 `th-TH` `th-TH-Pattara` 。         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | 具有地區設定和語音的容器映射 `tr-TR` `tr-TR-SedaRUS` 。         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | 具有地區設定和語音的容器映射 `vi-VN` `vi-VN-An` 。              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-HuihuiRUS` 。       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Kangkang-Apollo` 。 | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-Yaoyao-Apollo` 。   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Danny-Apollo` 。    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-Tracy-Apollo` 。    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | 具有地區設定和語音的容器映射 `zh-HK` `zh-HK-TracyRUS` 。        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-HanHanRUS` 。       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Yating-Apollo` 。   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | 具有地區設定和語音的容器映射 `zh-TW` `zh-TW-Zhiwei-Apollo` 。   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |


# <a name="previous-version"></a>[先前版本](#tab/previous)

的版本注意事項 `1.8.0-amd64-<locale-and-voice>` ：

**功能**

* 完整遷移至 .NET 3。1

的版本注意事項 `1.7.0-amd64-<locale-and-voice>` ：

**功能**

* 升級至 .NET 3.1 的元件

| 影像標記                                  | 備忘稿                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.8.0-amd64-<locale-and-voice>`            | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.8.0-amd64-en-us-ariarus` 。  |
| `1.7.0-amd64-<locale-and-voice>`            | 第1版 GA 版本。 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.7.0-amd64-en-us-ariarus` 。  |


| V 1.8.0 的地區設定                          | 備忘稿                                                                      |
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

| V 1.7.0 的地區設定                          | 備忘稿                                                                      |
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


# <a name="latest-version"></a>[最新版本](#tab/current)

的版本資訊 `v1.3.0` ：
* 神經文字轉換語音容器現在已正式推出。 

| 影像標記                                  | 備忘稿                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `1.3.0-amd64-<locale-and-voice>`    | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.3.0-amd64-en-us-arianeural` 。 |


| v 1.3.0 地區設定和語音           | 備忘稿                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | 具有地區設定和語音的容器映射 `de-DE` `de-DE-KatjaNeural` 。     |
| `en-au-natashaneural`               | 具有地區設定和語音的容器映射 `en-AU` `en-AU-NatashaNeural` 。   |
| `en-ca-claraneural`                 | 具有地區設定和語音的容器映射 `en-CA` `en-CA-ClaraNeural` 。     |
| `en-gb-libbyneural`                 | 具有地區設定和語音的容器映射 `en-GB` `en-GB-LibbyNeural` 。     |
| `en-gb-mianeural`                   | 具有地區設定和語音的容器映射 `en-GB` `en-GB-MiaNeural` 。       |
| `en-us-arianeural`                  | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `en-us-guyneural`                   | 具有地區設定和語音的容器映射 `en-US` `en-US-GuyNeural` 。       |
| `es-es-elviraneural`                | 具有地區設定和語音的容器映射 `es-ES` `es-ES-ElviraNeural` 。    |
| `es-mx-dalianeural`                 | 具有地區設定和語音的容器映射 `es-MX` `es-MX-DaliaNeural` 。     |
| `fr-ca-sylvieneural`                | 具有地區設定和語音的容器映射 `fr-CA` `fr-CA-SylvieNeural` 。    |
| `fr-fr-deniseneural`                | 具有地區設定和語音的容器映射 `fr-FR` `fr-FR-DeniseNeural` 。    |
| `it-it-elsaneural`                  | 具有地區設定和語音的容器映射 `it-IT` `it-IT-ElsaNeural` 。      |
| `ja-jp-nanamineural`                | 具有地區設定和語音的容器映射 `ja-JP` `ja-JP-NanamiNeural` 。    |
| `ko-kr-sunhineural`                 | 具有地區設定和語音的容器映射 `ko-KR` `ko-KR-SunHiNeural` 。     |
| `pt-br-franciscaneural`             | 具有地區設定和語音的容器映射 `pt-BR` `pt-BR-FranciscaNeural` 。 |
| `zh-cn-xiaoxiaoneural`              | 具有地區設定和語音的容器映射 `zh-CN` `zh-CN-XiaoxiaoNeural` 。  |

# <a name="previous-version"></a>[先前版本](#tab/previous)

| 影像標記                                  | 備忘稿                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有地區設定和語音的容器映射 `en-US` `en-US-AriaNeural` 。      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | 取代 `<locale>` 為下列其中一個可用的地區設定，如下所示。 例如 `1.2.0-amd64-en-us-arianeural-preview` 。 |


| v 1.2.0 Preview 地區設定和語音           | 備忘稿                                                                      |
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

---

## <a name="speech-language-detection"></a>語音語言偵測

您可以在 container registry 聯合中找到 [語音語言偵測][sp-lid] 容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/speechservices/` 庫中，並命名為 `language-detection` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list)的完整清單。

| 影像標記                                  | 備忘稿                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>關鍵片語擷取

容器映射可以在 `mcr.microsoft.com` container registry 整合上找到。 它位於存放 `azure-cognitive-services/textanalytics/` 庫中，並命名為 `keyphrase` 。 完整的容器映射名稱是、 `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` 。

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)的完整清單。

# <a name="latest-version"></a>[最新版本](#tab/current)


| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[舊版](#tab/previous)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>文字語言偵測

您可以在 container registry 整合上找到 [語言偵測][ta-la] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/textanalytics/` 庫中，並命名為 `language` 。 完整的容器映射名稱為、 `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)的完整清單。

# <a name="latest-versions"></a>[最新版本](#tab/current)

| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[舊版](#tab/previous)


| 影像標記                    | 備忘稿 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>情感分析

您可以在 container registry 整合上找到 [情感分析][ta-se] 的容器映射 `mcr.microsoft.com` 。 它位於存放 `azure-cognitive-services/textanalytics/` 庫中，並命名為 `sentiment` 。 完整的容器映射名稱為、 `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

此容器映射有下列可用的標記。 您也可以在 [MCR 上找到標記](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)的完整清單。

| 影像標記 | 備忘稿                                         |
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
