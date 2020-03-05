---
title: 應用程式設定-LUIS
titleSuffix: Azure Cognitive Services
description: 適用于 Azure 的應用程式設定認知服務語言理解應用程式會儲存在應用程式和入口網站中。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270812"
---
# <a name="application-settings"></a>應用程式設定

這些應用程式設定會儲存在[匯出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)的應用程式中，並使用 REST api 來[更新](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)。 變更您的應用程式版本設定會將您的應用程式訓練狀態重設為未訓練。

|設定|預設值|注意|
|--|--|--|
|NormalizePunctuation|True|移除標點符號。|
|NormalizeDiacritics|True|移除變音符號。|

## <a name="diacritics-normalization"></a>變音符號正規化

在 `settings` 參數中，為您的 LUIS JSON 應用程式檔開啟變音符號的語句正規化。

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

下列語句顯示如何正規化的變音符號正規化會影響語句：

|以變音符號設定為 false|將變音符號設定為 true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>變音符號的語言支援

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>巴西葡萄牙文 `pt-br` 變音符號

|變音符號設定為 false|變音符號設定為 true|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>荷蘭文 `nl-nl` 變音符號

|變音符號設定為 false|變音符號設定為 true|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>法文 `fr-` 變音符號

這包括法文和加拿大子文化特性。

|變音符號設定為 false|變音符號設定為 true|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>德文 `de-de` 變音符號

|變音符號設定為 false|變音符號設定為 true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>義大利文 `it-it` 變音符號

|變音符號設定為 false|變音符號設定為 true|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>西班牙文 `es-` 變音符號

這包括西班牙文和加拿大墨西哥。

|變音符號設定為 false|變音符號設定為 true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>標點符號正規化

在 `settings` 參數中，為您的 LUIS JSON 應用程式檔開啟標點符號的語句正規化。

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

下列語句顯示標點符號如何影響語句：

|設定為 False 的標點符號|設定為 True 的標點符號|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>已移除標點符號

`NormalizePunctuation` 設定為 true 時，會移除下列標點符號。

|標點符號|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
