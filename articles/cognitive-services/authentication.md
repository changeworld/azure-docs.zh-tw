---
title: 驗證
titleSuffix: Azure Cognitive Services
description: 有三種方式可以驗證 Azure 認知服務資源的要求，分別是訂用帳戶金鑰、持有人權杖或多服務訂用帳戶。 在本文中，您將了解每個方法，以及如何提出要求。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74423936"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>驗證 Azure 認知服務要求

Azure 認知服務的每個要求必須包含驗證標頭。 此標頭會與訂用帳戶金鑰或存取權杖一起傳遞，用來驗證您的服務或服務群組訂閱。 在本文中，您將了解驗證要求的三種方式及各自的需求。

* [使用單一服務訂用帳戶金鑰進行驗證](#authenticate-with-a-single-service-subscription-key)
* [使用多服務訂用帳戶金鑰進行驗證](#authenticate-with-a-multi-service-subscription-key)
* [使用權杖進行驗證](#authenticate-with-an-authentication-token)
* [使用 Azure 活動目錄 （AAD） 進行身份驗證](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Prerequisites

提出要求之前，您需要 Azure 帳戶和 Azure 認知服務訂用帳戶。 如果您已經有帳戶，請繼續進行並跳至下一節。 如果您沒有帳戶，我們有一個指南，讓您在幾分鐘內進行設置：[為 Azure 創建認知服務帳戶](cognitive-services-apis-create-account.md)。

創建帳戶或啟動[免費試用版](https://azure.microsoft.com/try/cognitive-services/my-apis)後，可以從[Azure 門戶](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)獲取訂閱金鑰。

## <a name="authentication-headers"></a>驗證標頭

讓我們快速檢閱適用於 Azure 認知服務的驗證標頭。

| 頁首 | 描述 |
|--------|-------------|
| Ocp-Apim-Subscription-Key | 使用此標頭以特定服務的訂用帳戶金鑰或多服務訂用帳戶金鑰進行驗證。 |
| Ocp-Apim-Subscription-Region | 只有在搭配[翻譯工具文字 API](./Translator/reference/v3-0-reference.md) 使用多服務訂用帳戶金鑰時才需要此標頭。 使用此標頭指定訂用帳戶區域。 |
| 授權 | 如果您使用驗證權杖，請使用此標頭。 下列各節會詳細說明執行權杖交換的步驟。 提供的值遵循下列格式：`Bearer <TOKEN>`。 |

## <a name="authenticate-with-a-single-service-subscription-key"></a>使用單一服務訂用帳戶金鑰進行驗證

第一個選項是使用特定服務 (例如翻譯工具文字) 的訂用帳戶金鑰來驗證要求。 金鑰適用於 Azure 入口網站中您建立的每個資源。 若要使用訂用帳戶金鑰來驗證要求，它必須傳遞以作為 `Ocp-Apim-Subscription-Key` 標頭。

這些範例要求示範如何使用 `Ocp-Apim-Subscription-Key` 標頭。 請記住，當使用此範例時，您必須包含有效的訂用帳戶金鑰。

這是 Bing Web 搜尋 API 的範例呼叫：
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

這是翻譯工具文字 API 的範例呼叫：
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

以下影片將示範如何使用認知服務金鑰。

## <a name="authenticate-with-a-multi-service-subscription-key"></a>使用多服務訂用帳戶金鑰進行驗證

>[!WARNING]
> 此時，這些服務**不支援**多服務金鑰：QnA 製造商、語音服務、自訂視覺和異常檢測器。

此選項也會使用訂用帳戶金鑰來驗證要求。 主要差異在於，訂用帳戶金鑰未繫結至特定服務，而是單一金鑰可用來驗證多個認知服務的要求。 如需區域可用性、支援功能和定價的詳細資訊，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/)。

在每個要求中，訂用帳戶金鑰提供作為 `Ocp-Apim-Subscription-Key` 標頭。

[![認知服務的多服務訂閱金鑰演示](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>支援區域

當使用多服務訂用帳戶金鑰對 `api.cognitive.microsoft.com` 提出要求時，您必須在 URL 中包含區域。 例如：`westus.api.cognitive.microsoft.com`。

當搭配翻譯工具文字 API 使用多服務訂用帳戶金鑰時，您必須指定訂用帳戶區域與 `Ocp-Apim-Subscription-Region` 標頭。

在以下區域中支援多服務驗證：

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>範例要求

這是 Bing Web 搜尋 API 的範例呼叫：

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

這是翻譯工具文字 API 的範例呼叫：

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>使用驗證權杖進行驗證

某些 Azure 認知服務接受驗證權杖，而在某些情況下更是需要驗證權杖。 目前，以下服務支援驗證權杖：

* 文字翻譯 API
* 語音服務：語音到文本 REST API
* 語音服務：文本到語音 REST API

>[!NOTE]
> QnA Maker 也會使用授權標頭，但需要端點金鑰。 有關詳細資訊，請參閱[QnA 製造商：從知識庫獲取答案](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md)。

>[!WARNING]
> 支援驗證權杖的服務可能會隨著時間變更，請在使用此驗證方法之前，檢查服務的 API 參考。

單一服務和多服務訂用帳戶金鑰都可以交換驗證權杖。 驗證權杖的有效時間為 10 分鐘。

驗證權杖會包含在要求中作為 `Authorization` 標頭。 提供的權杖值前面必須加上 `Bearer`，例如：`Bearer YOUR_AUTH_TOKEN`。

### <a name="sample-requests"></a>範例要求

使用此 URL 來交換驗證權杖的訂用帳戶金鑰：`https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`。

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

以下多服務區域支援權杖交換：

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

取得驗證權杖之後，您必須在每個要求中加以傳遞作為 `Authorization` 標頭。 這是翻譯工具文字 API 的範例呼叫：

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>另請參閱

* [什麼是認知服務？](welcome.md)
* [認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [自訂子域](cognitive-services-custom-subdomains.md)
