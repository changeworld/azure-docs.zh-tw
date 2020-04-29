---
title: 快速入門：使用參數來建立自訂命令（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會將參數新增至自訂命令應用程式。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80348539"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>快速入門：使用參數來建立自訂命令（預覽）

在[前一篇文章](./quickstart-custom-speech-commands-create-new.md)中，我們建立了新的自訂命令專案，以回應不含參數的命令。

在本文中，我們將使用參數擴充此應用程式，讓它可以處理開啟和關閉多個裝置。

## <a name="create-parameters"></a>Create Parameters

1. 開啟[我們先前建立](./quickstart-custom-speech-commands-create-new.md)的專案
1. 因為此命令現在會處理 on 和 off，請將命令重新命名為 "TurnOnOff"
   - 將滑鼠停留在命令的名稱上，然後選取 [編輯] 圖示以變更名稱
1. 建立新的參數來代表使用者是否要開啟或關閉裝置
   - 選取 [ `+`參數] 區段旁的圖示

   > [!div class="mx-imgBorder"]
   > ![建立參數](media/custom-speech-commands/create-on-off-parameter.png)

   | 設定            | 建議的值     | 描述                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | 名稱               | OnOff               | 參數的描述性名稱                                                                     |
   | 為全域          | unchecked           | 核取方塊，指出此參數的值是否會全域套用至專案中的所有命令 |
   | 必要           | checked             | 指出是否需要此參數值的核取方塊，才能完成命令          |
   | 回應範本  | 「-開啟或關閉？」      | 當不知道此參數的值時，所要求的提示                                       |
   | 類型               | 字串              | 參數的類型，例如數位、字串或日期時間                                               |
   | 設定      | 字串清單         | 若為字串，字串清單會將輸入限制為一組可能值                                      |
   | 字串清單值 | on、off             | 若為字串清單參數，可能值的集合和其同義字                                |

   - 接下來，再次`+`選取圖示來新增第二個參數，以代表裝置的名稱。 針對此範例，電視和風扇

   | 設定            | 建議的值       | 描述                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | 名稱               | SubjectDevice         | 參數的描述性名稱                                                                     |
   | 為全域          | unchecked             | 核取方塊，指出此參數的值是否會全域套用至專案中的所有命令 |
   | 必要           | checked               | 指出是否需要此參數值的核取方塊，才能完成命令          |
   | 回應範本  | 「-哪個裝置？」     | 當不知道此參數的值時，所要求的提示                                       |
   | 類型               | 字串                | 參數的類型，例如數位、字串或日期時間                                               |
   | 設定      | 字串清單           | 若為字串，字串清單會將輸入限制為一組可能值                                      |
   | 字串清單值 | 電視、風扇               | 若為字串清單參數，可能值的集合和其同義字                                |
   | 同義字（電視）      | 電視，telly     | 字串清單參數的每個可能值的選擇性同義字                                      |

## <a name="add-sample-sentences"></a>新增範例句子

使用參數時，加入涵蓋所有可能組合的範例句子會很有説明。 例如：

1. 完整參數資訊-`"turn {OnOff} the {SubjectDevice}"`
1. 部分參數資訊-`"turn it {OnOff}"`
1. 無參數資訊-`"turn something"`

具有不同資訊數量的範例句子可讓自訂命令應用程式以部分資訊解析一次解析度和多回合解析。

記住這一點之後，請編輯範例句子以使用下列建議的參數。

> [!TIP]
> 在範例句子編輯器中，使用大括弧來參考您的參數。 - `turn {OnOff} the {SubjectDevice}`使用 tab 鍵自動完成來參考先前建立的參數。

> [!div class="mx-imgBorder"]
> ![含有參數的範例句子](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>將參數新增至完成規則

修改您在[前一個快速入門](./quickstart-custom-speech-commands-create-new.md)中建立的完成規則：

1. 新增條件，然後選取 [必要參數]。 同時`OnOff`選取和`SubjectDevice`
1. 編輯語音回應動作以使用`OnOff`和： `SubjectDevice`

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>試試看

開啟 [測試聊天] 面板，然後嘗試一些互動。

- 輸入：關閉電視
- 輸出：正常，關閉電視

- 輸入：關閉電視
- 輸出：正常，關閉電視

- 輸入：關閉
- 輸出：哪一個裝置？
- 輸入：電視
- 輸出：正常，關閉電視

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [快速入門：使用自訂命令搭配自訂語音（預覽）](./quickstart-custom-speech-commands-select-custom-voice.md)
