---
title: 人記轉錄指南. 語音服務
titleSuffix: Azure Cognitive Services
description: 為了提高語音辨識的準確性（例如刪除或不正確替換單詞時），您可以使用人工標記的轉錄和音訊資料。 人標記的轉錄是音訊檔的逐字逐字的逐字轉錄。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806057"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>如何創建人標記轉錄

如果您希望提高識別準確性，尤其是在刪除或不正確替換單詞時引起的問題，則需要使用人工標記的轉錄以及音訊資料。 什麼是人標記的轉錄？ 這很簡單，它們是音訊檔的逐字逐字的逐字轉錄。

需要大量轉錄資料樣本來提高識別能力，我們建議提供 10 到 1，000 小時的轉錄資料。 在本頁中，我們將查看旨在説明您創建高品質轉錄的指南。 本指南按地區設定進行細分，包括美國英語、普通話和德語部分。

## <a name="us-english-en-us"></a>美式英文 (en-US)

英語音訊的人工標記轉錄必須作為純文字提供，只能使用 ASCII 字元。 避免使用拉丁文 1 或 Unicode 標點符號字元。 在從文字處理應用程式複製文本或從網頁刮擦資料時，通常會無意中添加這些字元。 如果存在這些字元，請確保使用相應的 ASCII 替換更新它們。

以下是一些範例：

| 要避免的字元 | Substitution | 注意 |
| ------------------- | ------------ | ----- |
| "你好世界" | "Hello world" | 首號和收盤價用相應的 ASCII 字元替換。 |
| 約翰節 | 約翰節 | 撇號已被替換為適當的 ASCII 字元。 |
| it was good—no, it was great! | it was good--no, it was great! | em 破折號被兩個連字號替換。 |

### <a name="text-normalization-for-us-english"></a>美國英語的文本正常化

文本正常化是將單詞轉換為訓練模型時使用的一致格式。 某些正常化規則會自動應用於文本，但是，我們建議您在準備人工標記的轉錄資料時使用這些準則：

- 用文字寫出縮寫。
- 用單詞（如會計術語）寫出非標準數位字串。
- 非字母字元或混合字母數位字元應轉錄為發音字元。
- 發音為單詞的縮寫不應編輯（如"雷達"、"鐳射"、"RAM"或"北約"）。
- 寫出發音為單獨字母的縮寫，每個字母由空格分隔。

下面是一些應在轉錄上執行的正常化示例：

| 原始文字               | 正常化後的文本              |
| --------------------------- | ------------------------------------- |
| 布魯斯·班納博士            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| 我的血型是O+         | My blood type is O positive           |
| 水是H20                | 水是 H 2 O                        |
| 玩 OU812 由范·哈倫     | 玩 O U 8 1 2 由 范·哈倫           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

以下正常化規則將自動應用於轉錄：

- 使用小寫字母。
- 刪除單詞中撇號以外的所有標點符號。
- 將數位擴展為單詞/口語形式，如美元金額。

下面是在轉錄上自動執行的一些正常化示例：

| 原始文字                          | 正常化後的文本          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| 成本\$3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>普通話（zh-CN）

中文音訊的人工標記轉錄必須使用位元組順序標記編碼為 UTF-8。 請避免使用半形標點符號字元。 當您在文字處理程式中準備資料或從網頁刮擦資料時，可能會無意中包含這些字元。 如果存在這些字元，請確保使用適當的全寬替換更新它們。

以下是一些範例：

| 要避免的字元 | Substitution   | 注意 |
| ------------------- | -------------- | ----- |
| "=" | "=" | 首號和收盤價用適當的字元替換。 |
| 需要什么帮助? | 需要什么帮助？| 問號已被用適當的字元替換。 |

### <a name="text-normalization-for-mandarin-chinese"></a>普通話的文本正常化

文本正常化是將單詞轉換為訓練模型時使用的一致格式。 某些正常化規則會自動應用於文本，但是，我們建議您在準備人工標記的轉錄資料時使用這些準則：

- 用文字寫出縮寫。
- 將數值字串以口語形式寫出。

下面是一些應在轉錄上執行的正常化示例：

| 原始文字 | 正常化後的文本 |
| ------------- | ------------------------ |
| # 21 | 我今年二十一 |
| 3 * 504 | 三号 楼 五 零 四 |

以下正常化規則將自動應用於轉錄：

- 刪除所有標點符號
- 將數位擴展到口語形式
- 將全寬字母轉換為半寬字母
- 對於所有的英文字組使用大寫字母

下面是在轉錄上自動執行的一些正常化示例：

| 原始文字 | 正常化後的文本 |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ≤ 3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年 = 8 + 8 * | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 5：00 | | 下午 五点 的 航班 |
| [ 21 # | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>德語（去德語）和其他語言

德語音訊（和其他非英語或普通話）的人工標記轉錄必須使用位元組順序標記編碼的 UTF-8。 應為每個音訊檔提供一個人工標記的腳本。

### <a name="text-normalization-for-german"></a>德語的文本正常化

文本正常化是將單詞轉換為訓練模型時使用的一致格式。 某些正常化規則會自動應用於文本，但是，我們建議您在準備人工標記的轉錄資料時使用這些準則：

- 將小數點寫成""，而不是""。
- 將時間分隔符號寫成"："而不是"。（例如：12：00 Uhr）。
- "ca." 之類的縮寫 不會被取代。 我們建議您使用完整的口語形式。
- 已移除四個主要的數學運算子 (+、-、\* 和 /)。 我們建議用書面形式替換它們："加號"、"減"、"減"、"瑪律"和"geteilt"。
- 將刪除比較運算子（*、<和>）。 建議並取代成 "gleich"、"kleiner als" 和 "grösser als"。
- 以書面形式寫分數，如 3/4（例如："drei viertel"而不是 3/4）。
- 將"+"符號替換為其書面形式"歐元"。

下面是一些應在轉錄上執行的正常化示例：

| 原始文字    | 使用者正常化後的文本 | 系統正常化後的文本       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

以下正常化規則將自動應用於轉錄：

- 對所有文本使用小寫字母。
- 刪除所有標點符號，包括各種類型的引號（"測試"、"測試"、"測試"和 [測試] 都正常）。
- 丟棄具有此集中任何特殊字元的行：[ ] [ ] © [ ® ] 2 = { { } } } } } } } } } } } } }
- 將數位擴展為口頭形式，包括美元或歐元金額。
- 只接受一個，o，和你。 其他將被"th"替換或被丟棄。

下面是在轉錄上自動執行的一些正常化示例：

| 原始文字    | 正常化後的文本 |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>後續步驟

- [準備和測試資料](how-to-custom-speech-test-data.md)
- [檢查資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
- [部署模型](how-to-custom-speech-deploy-model.md)
