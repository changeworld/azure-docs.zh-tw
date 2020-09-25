---
title: 應用程式設定-LUIS
description: 適用于 Azure 認知服務語言理解應用程式的應用程式設定會儲存在應用程式和入口網站中。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 0578e3c3c952a475c6beb01ffcf354e19eda6e26
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319158"
---
# <a name="app-and-version-settings"></a>應用程式和版本設定

這些設定會儲存在 [匯出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) 的應用程式中，並以 REST API 或 LUIS 入口網站更新。

變更您的應用程式版本設定會將您的應用程式定型狀態重設為未定型。

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


文字參考與範例包括：

* [標點符號](#punctuation-normalization)
* [調](#diacritics-normalization)

## <a name="diacritics-normalization"></a>字元正規化

下列語句顯示變音符號標準化如何影響語句：

|使用字元設定為 false 的字元|使用設為 true 的字元|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>變音符號的語言支援

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>巴西葡萄牙文 `pt-br` 音符號

|設定為 false 的字元符號|設為 true 的字元|
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

#### <a name="dutch-nl-nl-diacritics"></a>荷蘭文變 `nl-nl` 音符號

|設定為 false 的字元符號|設為 true 的字元|
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

#### <a name="french-fr--diacritics"></a>法文變 `fr-` 音符號

這包括法文和加拿大子文化特性。

|設定為 false 的字元符號|設為 true 的字元|
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

#### <a name="german-de-de-diacritics"></a>德文變 `de-de` 音符號

|設定為 false 的字元符號|設為 true 的字元|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>義大利文變 `it-it` 音符號

|設定為 false 的字元符號|設為 true 的字元|
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

#### <a name="spanish-es--diacritics"></a>西班牙文變 `es-` 音符號

這包括西班牙文和加拿大墨西哥。

|設定為 false 的字元符號|設為 true 的字元|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|

## <a name="punctuation-normalization"></a>標點符號正規化

下列語句顯示標點符號如何影響語句：

|將標點符號設為 False|將標點符號設為 True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>已移除標點符號

下列標點符號會被移除，並 `NormalizePunctuation` 設定為 true。

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

* 瞭解變音符號和標點符號的 [概念](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) 。
