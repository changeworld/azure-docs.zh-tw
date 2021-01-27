---
title: Translator Dictionary 範例方法
titleSuffix: Azure Cognitive Services
description: Translator Dictionary 範例方法提供的範例會示範如何在內容中使用字典中的詞彙。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: e7f0e106c1ca154dcd54990395430b3e0f6c536f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895504"
---
# <a name="translator-30-dictionary-examples"></a>Translator 3.0：字典範例

提供範例以說明字典中的字詞在內容中的使用方式。 此作業會與[字典查閱](./v3-0-dictionary-lookup.md)搭配使用。

## <a name="request-url"></a>要求 URL

將 `POST` 要求傳送至：

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>要求參數

在查詢字串上傳遞的要求參數為：

| 查詢參數 | 描述 |
| --------- | ----------- |
| api-version <img width=200/> | **必要參數**。<br/>用戶端要求的 API 版本。 值必須為 `3.0`。 |
| 從 | **必要參數**。<br/>指定輸入文字的語言。 來源語言必須是 `dictionary` 範圍內包含的[支援語言](./v3-0-languages.md)之一。 |
| to | **必要參數**。<br/>指定輸出文字的語言。 目標語言必須是 `dictionary` 範圍內包含的[支援語言](./v3-0-languages.md)之一。  | 

要求標頭包括：

| 標題  | 描述 |
| ------ | ----------- |
| 驗證標頭 <img width=200/>  | 必要的要求標頭。<br/>請參閱<a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">可用的驗證選項</a>。 |
| Content-Type | 必要的要求標頭。<br/>指定承載的內容類型。 可能的值為：`application/json`。 |
| Content-Length   | 必要的要求標頭。<br/>要求本文的長度。 |
| X-ClientTraceId   | **選擇項**。<br/>用於識別唯一要求的 GUID，由用戶端產生。 若您使用名為 `ClientTraceId` 的查詢參數在查詢字串中包含追蹤識別碼，您就可以省略此標頭。 |

## <a name="request-body"></a>要求本文

要求的本文是 JSON 陣列。 每個陣列元素都是具有下列屬性的 JSON 物件：

  * `Text`：一個字串，指定要查閱的字詞。 此屬性應為先前[字典查閱](./v3-0-dictionary-lookup.md)要求的反向翻譯中的 `normalizedText` 欄位值。 它也可以是 `normalizedSource` 欄位的值。

  * `Translation`：一個字串，指定[字典查閱](./v3-0-dictionary-lookup.md)作業先前傳回的翻譯文字。 此屬性應為[字典查閱](./v3-0-dictionary-lookup.md)回應的 `translations` 清單中包含的 `normalizedTarget` 欄位值。 服務會傳回特定的來源-目標字組配對的範例。

範例如下：

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

適用下列限制：

* 陣列最多可以有 10 個項目。
* 陣列項目的文字值不能超過 100 個字元，包括空格。

## <a name="response-body"></a>回應本文

成功的回應是輸入陣列的每個字串各有一個結果的 JSON 陣列。 結果物件包含下列屬性：

  * `normalizedSource`：一個字串，指定來源字詞的標準化形式。 一般而言，此屬性應等同於要求本文中位於相符清單索引上的 `Text` 欄位值。
    
  * `normalizedTarget`：一個字串，指定目標字詞的標準化形式。 一般而言，此屬性應等同於要求本文中位於相符清單索引上的 `Translation` 欄位值。
  
  * `examples`：(來源字詞、目標字詞) 配對的範例清單。 清單的每個項目是具有下列屬性的物件：

    * `sourcePrefix`：要在 `sourceTerm` 的值之前串連以形成完整範例的字串。 請勿加入空格字元，因為此字元會在必要時自動加入。 此值可以是空字串。

    * `sourceTerm`：與查閱的實際字詞等同的字串。 此字串可透過 `sourcePrefix` 和 `sourceSuffix` 來新增，以形成完整範例。 其值會分隔以便標示於使用者介面中，例如藉由粗體來標示。

    * `sourceSuffix`：要在 `sourceTerm` 的值之後串連以形成完整範例的字串。 請勿加入空格字元，因為此字元會在必要時自動加入。 此值可以是空字串。

    * `targetPrefix`：一個類似於 `sourcePrefix` 但用於目標的字串。

    * `targetTerm`：一個類似於 `sourceTerm` 但用於目標的字串。

    * `targetSuffix`：一個類似於 `sourceSuffix` 但用於目標的字串。

    > [!NOTE]
    > 如果字典中沒有範例，回應會是 200 (良好)，但 `examples` 清單卻是空白清單。

## <a name="examples"></a>範例

此範例說明如何查閱為英文字詞 `fly` 及其西班牙文翻譯 `volar` 所做配對的範例。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

回應本文 (為了清楚緣故已縮減) 如下：

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```