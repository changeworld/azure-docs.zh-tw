---
title: 語言支援 - Bing 圖像式搜尋 API
titleSuffix: Azure Cognitive Services
description: Bing 圖像式搜尋 API 支援的自然語言、國家/地區和區域清單。 Bing 圖像式搜尋 API 支援三十多個國家/地區，其中許多國家/地區有一種以上的語言。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883556"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Bing 圖像式搜尋 API 支援的語言和區域

Bing 圖像式搜尋 API 支援三十多個國家/地區，其中許多國家/地區有一種以上的語言。 每個要求中應該都包含使用者選擇的國家/地區以及語言。 了解使用者的市場，有助於 Bing 傳回適合的結果。 如果未指定國家/地區和語言，Bing 也會盡力判斷使用者的國家/地區和語言。 由於結果中可能含有連往 Bing 的連結，若 Bing 能知道使用者的國家/地區和語言，在使用者按下 Bing 連結時，就能為使用者提供慣用的當地語系化 Bing 使用者體驗。

若要指定國家/地區和語言，請將 `mkt` (market) 查詢參數設定為下面「市場」**** 表格中的代碼。 市場可指定國家/地區和語言。 如果使用者較希望以不同語言查看顯示文字，請將 `setLang` 查詢參數設為合適的語言代碼。

或者，您可以使用 `cc` 查詢參數指定國家/地區。 如果您指定國家/地區，您也必須使用 `Accept-Language` HTTP 標頭指定一或多個語言代碼。 支援的語言視國家/地區而異；「市場」表格中提供針對每個國家/地區支援的語言。



> [!NOTE]
> 適用以下市場限制：
>
> - 影像辨識的註釋僅提供英文版本。
> - 食譜、購物以及內含入解析的頁面，僅在 EN-US 市場提供。


## <a name="countriesregions"></a>國家/地區

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

|國家/地區|Language|市場代碼|
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
|美國|英文|zh-TW|
|美國|西班牙文|es-US|
