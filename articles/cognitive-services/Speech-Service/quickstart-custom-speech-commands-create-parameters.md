---
title: 快速入門：使用參數創建自訂命令（預覽） - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將向自訂命令應用程式添加參數。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348539"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>快速入門：使用參數創建自訂命令（預覽）

在[上一篇文章中](./quickstart-custom-speech-commands-create-new.md)，我們創建了一個新的自訂命令專案來回應沒有參數的命令。

在本文中，我們將使用參數擴展此應用程式，以便它可以處理打開和關閉多個設備。

## <a name="create-parameters"></a>Create Parameters

1. 打開[我們以前創建](./quickstart-custom-speech-commands-create-new.md)的專案
1. 由於命令現在將打開和關閉，請重命名命令為"打開"
   - 將滑鼠懸停在命令的名稱上，然後選擇編輯圖示以更改名稱
1. 創建新參數以表示使用者是打開還是關閉設備
   - 選擇`+`"參數"部分旁邊的圖示

   > [!div class="mx-imgBorder"]
   > ![創建參數](media/custom-speech-commands/create-on-off-parameter.png)

   | 設定            | 建議的值     | 描述                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | 名稱               | 開關               | 參數的描述性名稱                                                                     |
   | 是全域的          | unchecked           | 指示此參數的值是否全域應用於專案中的所有命令核取方塊 |
   | 必要           | checked             | 選中核取方塊，指示在完成命令之前是否需要此參數的值          |
   | 回應範本  | "-開還是關？"      | 提示在不知道此參數時請求該參數的值                                       |
   | 類型               | String              | 參數的類型，如數位、字串或日期時間                                               |
   | 組態      | 字串清單         | 對於字串，字串清單將輸入限制為一組可能的值                                      |
   | 字串清單值 | on、off             | 對於字串清單參數，可能的值集及其同義字                                |

   - 接下來，`+`再次選擇圖示以添加第二個參數以表示設備的名稱。 對於此示例，電視和風扇

   | 設定            | 建議的值       | 描述                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | 名稱               | 主題設備         | 參數的描述性名稱                                                                     |
   | 是全域的          | unchecked             | 指示此參數的值是否全域應用於專案中的所有命令核取方塊 |
   | 必要           | checked               | 選中核取方塊，指示在完成命令之前是否需要此參數的值          |
   | 回應範本  | "-哪種設備？     | 提示在不知道此參數時請求該參數的值                                       |
   | 類型               | String                | 參數的類型，如數位、字串或日期時間                                               |
   | 組態      | 字串清單           | 對於字串，字串清單將輸入限制為一組可能的值                                      |
   | 字串清單值 | 電視， 風扇               | 對於字串清單參數，可能的值集及其同義字                                |
   | 同義字（電視）      | 電視， 電視     | 字串清單參數的每個可能值的可選同義字                                      |

## <a name="add-sample-sentences"></a>添加示例句子

使用參數，添加涵蓋所有可能組合的示例句子非常有用。 例如：

1. 完整參數資訊 -`"turn {OnOff} the {SubjectDevice}"`
1. 部分參數資訊 -`"turn it {OnOff}"`
1. 無參數資訊 -`"turn something"`

具有不同資訊量的示例句子允許自訂命令應用程式使用部分資訊同時解析單次解析度和多轉解析度。

考慮到這一點，編輯示例句子以按照下面建議使用參數。

> [!TIP]
> 在"示例句子"編輯器中使用大括弧來引用您的參數。 - `turn {OnOff} the {SubjectDevice}`使用選項卡完成引用以前創建的參數。

> [!div class="mx-imgBorder"]
> ![具有參數的句子示例](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>將參數添加到"完成"規則

修改您在[上一個快速入門](./quickstart-custom-speech-commands-create-new.md)中創建的完成規則：

1. 添加新條件並選擇"必需"參數。 同時選擇`OnOff`和`SubjectDevice`
1. 編輯"語音回應"操作以`OnOff`使用`SubjectDevice`和 ：

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>試做

打開"測試聊天"面板並嘗試一些交互。

- 輸入：關閉電視
- 輸出：好的，關閉電視

- 輸入：關閉電視
- 輸出：好的，關閉電視

- 輸入：將其關閉
- 輸出：哪種設備？
- 輸入：電視
- 輸出：好的，關閉電視

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [快速入門：使用自訂命令與自訂語音（預覽）](./quickstart-custom-speech-commands-select-custom-voice.md)
