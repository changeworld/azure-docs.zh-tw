---
title: 人類加上標籤的轉譯指導方針-語音服務
titleSuffix: Azure Cognitive Services
description: 若要改善語音辨識精確度（例如刪除單字或不正確地替代字詞時），您可以使用人類加上標籤的轉譯以及您的音訊資料。 人類加上標籤的轉譯是音訊檔案逐字的單字轉譯。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "74806057"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>如何建立人為標記的轉譯

如果您想要改善辨識精確度，特別是當單字被刪除或不正確地替代時所造成的問題，您會想要使用人類加上標籤的轉譯以及您的音訊資料。 什麼是人為標記的轉譯？ 這很簡單，它們都是一字單字、逐字的音訊檔案轉譯。

需要大量的轉譯資料樣本，以改善辨識，建議您提供10到1000小時的轉譯資料。 在此頁面上，我們將探討專為協助您建立高品質轉譯所設計的指導方針。 本指南是依地區設定來細分，其中包含 US 英文、普通話中文和德文的章節。

## <a name="us-english-en-us"></a>美式英文 (en-US)

個人標記的英文音訊轉譯必須以純文字提供，而且只能使用 ASCII 字元。 避免使用拉丁-1 或 Unicode 標點符號字元。 從文字處理應用程式複製文字或從網頁抓取資料時，通常會不小心加入這些字元。 如果這些字元存在，請務必使用適當的 ASCII 替代來更新它們。

以下是一些範例：

| 要避免的字元 | Substitution | 備忘錄 |
| ------------------- | ------------ | ----- |
| "Hello world" | "Hello world" | 開頭和結尾的引號已取代成適當的 ASCII 字元。 |
| John 的日 | John 的日 | 這個撇號已用適當的 ASCII 字元來取代。 |
| it was good—no, it was great! | it was good--no, it was great! | Em 破折號會以兩個連字號取代。 |

### <a name="text-normalization-for-us-english"></a>美式英文的文字正規化

文字正規化是將單字轉換成在定型模型時所使用的一致格式。 某些正規化規則會自動套用至文字，不過，建議您在準備人為標記的轉譯資料時，使用這些指導方針：

- 以單字寫出縮寫。
- 以單字寫出非標準數值字串（例如，會計條款）。
- 非字母字元或混合的英數位元應轉譯成發音。
- 不應編輯單字的縮寫（例如「雷達圖」、「鐳射」、「RAM」或「NATO」）。
- 寫出縮寫為個別字母，並以空格分隔每個字母。

以下是您應該在轉譯上執行的一些正規化範例：

| 原始文字               | 正規化後的文字              |
| --------------------------- | ------------------------------------- |
| Bruce 橫幅            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| 我的血類型是 O +         | My blood type is O positive           |
| 水是 H20                | 水是 H 2 O                        |
| Play OU812 by Van Halen     | 播放 O U 8 1 2 by Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

下列正規化規則會自動套用至轉譯：

- 使用小寫字母。
- 移除單字內的撇號以外的所有標點符號。
- 將數位展開成單字/讀出形式，例如金額。

以下是一些針對轉譯自動執行的正規化範例：

| 原始文字                          | 正規化後的文字          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| It 成本\$3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>普通話中文（zh-CN）

人類加上標籤的轉譯 for 普通話中文音訊必須是以位元組順序標記編碼的 UTF-8。 請避免使用半形標點符號字元。 當您在文字處理程式中準備資料，或是從網頁抓取資料時，可能會不小心包含這些字元。 如果這些字元存在，請務必使用適當的全形替代來更新它們。

以下是一些範例：

| 要避免的字元 | Substitution   | 備忘錄 |
| ------------------- | -------------- | ----- |
| 你好 | 你好 | 開頭和結尾的引號已用適當的字元來取代。 |
| 需要什么帮助? | 需要什么帮助？| 問號已用適當的字元來取代。 |

### <a name="text-normalization-for-mandarin-chinese"></a>普通話中文的文字正規化

文字正規化是將單字轉換成在定型模型時所使用的一致格式。 某些正規化規則會自動套用至文字，不過，建議您在準備人為標記的轉譯資料時，使用這些指導方針：

- 以單字寫出縮寫。
- 將數值字串以口語形式寫出。

以下是您應該在轉譯上執行的一些正規化範例：

| 原始文字 | 正規化後的文字 |
| ------------- | ------------------------ |
| 我今年21 | 我今年二十一 |
| 3號樓504 | 三号 楼 五 零 四 |

下列正規化規則會自動套用至轉譯：

- 移除所有標點符號
- 將數位展開為說話形式
- 將全形字母轉換成半形字母
- 對於所有的英文字組使用大寫字母

以下是一些針對轉譯自動執行的正規化範例：

| 原始文字 | 正規化後的文字 |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ¥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00 的航班 | 下午 五点 的 航班 |
| 我今年21歲 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>德文（de）和其他語言

針對德文音訊（和其他非英文或普通話中文語言）以人為標記的轉譯，必須是以位元組順序標記編碼的 UTF-8。 每個音訊檔案都應該提供一個人為標記的文字記錄。

### <a name="text-normalization-for-german"></a>德文的文字正規化

文字正規化是將單字轉換成在定型模型時所使用的一致格式。 某些正規化規則會自動套用至文字，不過，建議您在準備人為標記的轉譯資料時，使用這些指導方針：

- 將小數點寫入 "，" 而非 "."。
- 將時間分隔符號寫成 "：" 而非 "."（例如： 12:00 Uhr）。
- "ca." 之類的縮寫 不會被取代。 建議您使用完整的說話形式。
- 已移除四個主要的數學運算子 (+、-、\* 和 /)。 我們建議以撰寫的表單來取代它們： "plus"、"減號"、"「不正確」和「geteilt」。
- 比較運算子會被移除（=、< 和 >）。 建議並取代成 "gleich"、"kleiner als" 和 "grösser als"。
- 以書面形式寫入分數（例如3/4）（例如： "drei viertel"，而不是3/4）。
- 以其書寫形式 "Euro" 取代 "€" 符號。

以下是您應該在轉譯上執行的一些正規化範例：

| 原始文字    | 使用者正規化之後的文字 | 系統正規化後的文字       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

下列正規化規則會自動套用至轉譯：

- 所有文字使用小寫字母。
- 移除所有標點符號，包括各種類型的引號（"test"、' test '、"test" 和«測試»都是正常的）。
- 捨棄此集合中包含任何特殊字元的資料列： [¦]、[©] [®] [²μ×ÿØ]。
- 將數位展開為說話形式，包括美元或歐元金額。
- 只接受 a、o 和您的母音。 其他則會以 "th" 取代或捨棄。

以下是一些針對轉譯自動執行的正規化範例：

| 原始文字    | 正規化後的文字 |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>後續步驟

- [準備和測試您的資料](how-to-custom-speech-test-data.md)
- [檢查您的資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
- [部署模型](how-to-custom-speech-deploy-model.md)
