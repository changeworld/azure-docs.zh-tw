---
title: 應用程式設定-LUIS
description: 適用于 Azure 的應用程式設定認知服務語言理解應用程式會儲存在應用程式和入口網站中。
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 7b545e0959a43520b7d643ef8c0658a1e1a3b295
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590975"
---
# <a name="app-and-version-settings"></a>應用程式和版本設定

這些設定會儲存在[匯出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)的應用程式中，並使用 REST API 或 LUIS 入口網站進行更新。

變更您的應用程式版本設定會將您的應用程式訓練狀態重設為未訓練。

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


文字參考和範例包括：

* [標點](#punctuation-normalization)
* [音調](#diacritics-normalization)

## <a name="diacritics-normalization"></a>變音符號正規化

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

#### <a name="french-fr--diacritics"></a>法文 `fr-` 字元

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

#### <a name="italian-it-it-diacritics"></a>義大利文 `it-it` 字元

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

#### <a name="spanish-es--diacritics"></a>西班牙文 `es-` 音符號

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

下列語句顯示標點符號如何影響語句：

|設定為 False 的標點符號|設定為 True 的標點符號|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>已移除標點符號

已移除下列標點符號，並將 `NormalizePunctuation` 設定為 true。

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

* 瞭解變音符號和標點符號的[概念](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)。
