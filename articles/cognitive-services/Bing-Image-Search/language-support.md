---
title: 語言支援 - Bing 影像搜尋 API
titleSuffix: Azure Cognitive Services
description: 了解 Bing 影像搜尋 API 支援哪些國家/地區和語言。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 14dacc4351af444c75517df4d4e02ecca3bbf40a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350070"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Bing 影像搜尋 API 支援的語言和區域

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

Bing 影像搜尋 API 支援三十多個國家/地區，許多國家/地區具有一個以上的語言。 使用查詢指定國家/地區，主要是為了根據該國家/地區的興趣，縮小搜尋結果範圍。 此外，結果可能包含 Bing 的連結，而且這些連結可能會根據指定的國家/地區或語言，將 Bing 的使用者體驗當地語系化。

若要指定國家/地區和語言，請將 `mkt` (market) 查詢參數設定為下面「市場」表格中的代碼。 市場可指定國家/地區和語言。 如果使用者較希望以不同語言查看顯示文字，請將 `setLang` 查詢參數設為合適的語言代碼。

或者，您可以使用 `cc` 查詢參數指定國家/地區。 如果您指定國家/地區，您也必須使用 `Accept-Language` HTTP 標頭指定一或多個語言代碼。 支援的語言視國家/地區而異；「市場」表格中提供針對每個國家/地區支援的語言。

> [!NOTE]
> 發燒影像 API 目前僅支援下列市場：
> - en-US (英文，美國)
> - en-CA (英文，加拿大)
> - en-AU (英文，澳洲)
> - zh-CN (中文，中國)

## <a name="countriesregions"></a>國家/地區

|國家/區域|程式碼|
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
|印尼|ID|
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

|國家/區域|Language|市場代碼|
|-------|--------|-----------|
|阿根廷|西班牙文|es-AR|
|澳洲|英文|en-AU|
|奧地利|德文|de-AT|
|比利時|荷蘭文|nl-BE|
|比利時|法文|fr-BE|
|巴西|葡萄牙文|pt-BR|
|加拿大|英文|en-CA|
|加拿大|法文|fr-CA|
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
|英國|英文|en-GB|
|美國|英文|en-US|
|美國|西班牙文|es-US|

## <a name="next-steps"></a>後續步驟
如需更多 Bing 新聞搜尋端點的相關資訊，請參閱[新聞影像搜尋 API v7 參考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)。