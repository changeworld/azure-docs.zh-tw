---
title: 應用程式設定 - LUIS
titleSuffix: Azure Cognitive Services
description: Azure 認知服務語言理解應用的應用程式設定存儲在應用和門戶中。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270812"
---
# <a name="application-settings"></a>應用程式設定

這些應用程式設定存儲在[匯出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)的[應用程式中，並](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)更新為 REST API。 更改應用版本設置會將應用培訓狀態重置為未定型。

|設定|預設值|注意|
|--|--|--|
|正常化標點|True|刪除標點符號。|
|NormalizeDiacritics|True|刪除變音符號。|

## <a name="diacritics-normalization"></a>變音符號正常化

在`settings`參數中打開變音符號對 LUIS JSON 應用檔的話語正常化。

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

以下陳述顯示了變音符號正常化如何影響話語：

|將變音符號設置為 false|與變音符號設置為 true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>對變音符號的語言支援

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>巴西葡萄牙文`pt-br`變音符號

|變音符號設置為 false|變音符號設置為 true|
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

#### <a name="dutch-nl-nl-diacritics"></a>荷蘭`nl-nl`變音符號

|變音符號設置為 false|變音符號設置為 true|
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

#### <a name="french-fr--diacritics"></a>法語`fr-`變音符號

這包括法國和加拿大的亞文化。

|變音符號設置為 false|變音符號設置為 true|
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

#### <a name="german-de-de-diacritics"></a>德國`de-de`變音符號

|變音符號設置為 false|變音符號設置為 true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>義大利`it-it`變音符號

|變音符號設置為 false|變音符號設置為 true|
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

#### <a name="spanish-es--diacritics"></a>西班牙`es-`變音符號

這包括西班牙和加拿大墨西哥人。

|變音符號設置為 false|變音符號設置為 true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>標點正常化

在參數中打開標點符號的說出正常化。 `settings`

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

以下陳述顯示了標點符號如何影響話語：

|標點設置為"False"|標點設置為 True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>已刪除標點符號

以下標點符號被刪除，設置為`NormalizePunctuation`true。

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
