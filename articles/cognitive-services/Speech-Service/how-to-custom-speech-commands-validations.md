---
title: 如何：將驗證添加到自訂命令參數（預覽）
titleSuffix: Azure Cognitive Services
description: 在本文中，我們將解釋如何向自訂命令中的參數添加驗證。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156449"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>如何：將驗證添加到自訂命令參數（預覽）

在本文中，您將學習如何向參數添加驗證並提示更正。

## <a name="prerequisites"></a>Prerequisites

您必須已完成以下文章中的步驟：

- [快速入門：創建自訂命令（預覽）](./quickstart-custom-speech-commands-create-new.md)
- [快速入門：使用參數創建自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>創建設置溫度命令

為了演示驗證，讓我們創建一個新命令，允許使用者設置溫度。

1. 在[語音工作室](https://speech.microsoft.com/)中打開以前創建的自訂命令應用程式
1. 創建新的命令**集溫度**
1. 添加目標溫度的參數
1. 添加溫度參數的驗證
   > [!div class="mx-imgBorder"]
   > ![添加範圍驗證](media/custom-speech-commands/validations-add-temperature.png)

   | 設定           | 建議的值                                          | 描述                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | 名稱              | 溫度                                              | 命令參數的描述性名稱                                                    |
   | 必要          | true                                                     | 選中核取方塊，指示在完成命令之前是否需要此參數的值 |
   | 回應範本 | "-你想什麼溫度？"                     | 提示在不知道此參數時請求該參數的值                              |
   | 類型              | Number                                                   | 參數的類型，如數位、字串或日期時間                                      |
   | 驗證        | 最小值： 60， 最大值： 80                             | 對於 Number 參數，參數的允許值範圍                             |
   | 回應範本 | "-對不起，我只能設置在60到80度之間"      | 如果驗證失敗，提示請求更新的值                                       |

1. 添加一些示例句子

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 添加完成規則以確認結果

   | 設定    | 建議的值                                           | 描述                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | 規則名稱  | 確認訊息                                      | 描述規則目的的名稱          |
   | 條件 | 所需參數 - 溫度                          | 確定規則何時可以運行的條件    |
   | 動作    | 語音回應 - "-確定，設置為 [溫度] 度" | 規則條件為 true 時要執行的操作 |

> [!TIP]
> 此示例使用語音回應來確認結果。 有關使用用戶端操作完成命令的示例，請參閱[：如何：使用語音 SDK 在用戶端上執行命令（預覽）](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>試做

選擇"測試"面板並嘗試一些交互。

- 輸入：將溫度設置為 72 度
- 輸出："好的，設置為 72 度"

- 輸入：將溫度設置為 45 度
- 輸出："對不起，我只能設置在60到80度之間"
- 輸入：使其為 72 度
- 輸出："好的，設置為 72 度"

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：向自訂命令添加確認（預覽）](./how-to-custom-speech-commands-confirmations.md)
