---
title: 語言支援 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: Bing 新聞搜尋 API 支援的自然語言、國家/地區和區域清單。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "68882680"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Bing Web 搜尋 API 支援的語言和區域

Bing Web 搜尋 API 支援三十多個國家/地區，許多國家/地區具有一個以上的語言。 使用查詢指定國家/地區，有助於根據該國家/地區的興趣，縮小搜尋結果範圍。 結果可能包含 Bing 的連結，而且這些連結可能會根據指定的國家/地區或語言，將 Bing 的使用者體驗當地語系化。

您可以使用 `cc` 查詢參數指定國家/地區。 指定國家或地區時，您必須使用[ `Accept-Language`標頭](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)指定一或多個語言代碼。 使用[市場資料表](#markets)取得每個市場所支援的語言清單。

或者，您也可以使用 `mkt` 查詢參數和**市場**資料表的代碼來指定市場。 指定市場同時會指定國家/地區和慣用的語言。 您可以使用 `setLang` 查詢參數明確設定語言。

## <a name="countriesregions"></a>國家/區域

|國家/地區|程式碼|
|-------|----|
|阿根廷|AR|
|澳大利亞|AU|
|奧地利|AT|
|比利時|BE|
|巴西|BR|
|Canada|CA|
|智利|CL|
|丹麥|DK|
|芬蘭|FI|
|法國|FR|
|德國|DE|
|香港特別行政區|HK|
|印度|IN|
|印尼|識別碼|
|義大利|IT|
|日本|JP|
|南韓|KR|
|馬來西亞|MY|
|墨西哥|MX|
|荷蘭|NL|
|紐西蘭|NZ|
|挪威|否|
|中國|CN|
|波蘭|PL|
|葡萄牙|PT|
|菲律賓|PH|
|俄羅斯|RU|
|沙烏地阿拉伯|SA|
|南非|ZA|
|西班牙|ES|
|瑞典|SE|
|瑞士|CH|
|台灣|TW|
|土耳其|TR|
|United Kingdom|GB|
|美國|US|

## <a name="markets"></a>市場

|國家/地區|Language|市場代碼|
|-------|--------|-----------|
|阿根廷|西班牙文|es-AR|
|澳大利亞|英文|en-AU|
|奧地利|德文|de-AT|
|比利時|荷蘭文|nl-BE|
|比利時|法文|fr-BE|
|巴西|葡萄牙文|pt-BR|
|Canada|英文|en-CA|
|Canada|法文|fr-CA|
|智利|西班牙文|es-CL|
|丹麥|丹麥文|da-DK|
|芬蘭|芬蘭文|fi-FI|
|法國|法文|fr-FR|
|德國|德文|de-DE|
|香港特別行政區|繁體中文|zh-HK|
|印度|英文|en-IN|
|印尼|英文|en-ID|
|義大利|義大利文|it-IT|
|日本|日文|ja-JP|
|南韓|韓文|ko-KR|
|馬來西亞|英文|en-MY|
|墨西哥|西班牙文|es-MX|
|荷蘭|荷蘭文|nl-NL|
|紐西蘭|英文|en-NZ|
|挪威|挪威文|no-NO|
|中國|中文|zh-CN|
|波蘭|波蘭文|pl-PL|
|葡萄牙|葡萄牙文|pt-PT|
|菲律賓|英文|en-PH|
|俄羅斯|俄文|ru-RU|
|沙烏地阿拉伯|阿拉伯文|ar-SA|
|南非|英文|en-ZA|
|西班牙|西班牙文|es-ES|
|瑞典|瑞典文|sv-SE|
|瑞士|法文|fr-CH|
|瑞士|德文|de-CH|
|台灣|繁體中文|zh-TW|
|土耳其|土耳其文|tr-TR|
|United Kingdom|英文|en-GB|
|美國|英文|zh-TW|
|美國|西班牙文|es-US|

## <a name="next-steps"></a>後續步驟

* [Bing 影像搜尋 API 參考](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
