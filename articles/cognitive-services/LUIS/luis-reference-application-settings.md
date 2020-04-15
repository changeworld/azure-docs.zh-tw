---
title: 應用程式設定 - LUIS
description: Azure 認知服務語言理解應用的應用程式設置存儲在應用和門戶中。
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382193"
---
# <a name="application-settings"></a>應用程式設定

這些應用程式設定存儲在[匯出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)的[應用程式中,並](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)更新為 REST API。 更改應用版本設置會將應用培訓狀態重置為未定型。

學習音調符號與標點符號[的概念](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)。

|設定|預設值|注意|
|--|--|--|
|要要顯示化標點|True|刪除標點符號。|
|NormalizeDiacritics|True|刪除音調符號。|

## <a name="diacritics-normalization"></a>音調符號規範化

在`settings`參數中打開音調符號對 LUIS JSON 應用檔的話語規範化。

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

以下陳述顯示音調符號規範化如何影響話語:

|將音調符號設定為 false|與音調符號設定為 true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>對音調符號的語言支援

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>巴西葡萄牙文`pt-br`音調符號

|音調符號設定為 false|音調符號設定為 true|
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

#### <a name="dutch-nl-nl-diacritics"></a>荷蘭`nl-nl`音調符號

|音調符號設定為 false|音調符號設定為 true|
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

#### <a name="french-fr--diacritics"></a>法語`fr-`音調符號

這包括法國和加拿大的亞文化。

|音調符號設定為 false|音調符號設定為 true|
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

#### <a name="german-de-de-diacritics"></a>德國`de-de`音調符號

|音調符號設定為 false|音調符號設定為 true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>義大利`it-it`音調符號

|音調符號設定為 false|音調符號設定為 true|
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

#### <a name="spanish-es--diacritics"></a>西班牙`es-`音調符號

這包括西班牙和加拿大墨西哥人。

|音調符號設定為 false|音調符號設定為 true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>標點規範化

在參數中打開標點符號的說出規範化。 `settings`

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

以下陳述顯示標點符號如何影響話語:

|標點設置為"False"|標點設定為 True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>已移除標點符號

以下標點符號被刪除,設置為`NormalizePunctuation`true。

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

## <a name="next-steps"></a>後續步驟

* 學習音調符號與標點符號[的概念](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)。
