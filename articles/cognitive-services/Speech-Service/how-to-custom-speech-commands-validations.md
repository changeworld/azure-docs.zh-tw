---
title: 如何：將驗證新增至自訂命令參數（預覽）
titleSuffix: Azure Cognitive Services
description: 在本文中，我們會說明如何將驗證新增至自訂命令中的參數。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "76156449"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>如何：將驗證新增至自訂命令參數（預覽）

在本文中，您將瞭解如何將驗證新增至參數，並提示更正。

## <a name="prerequisites"></a>先決條件

您必須已完成下列文章中的步驟：

- [快速入門：建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-new.md)
- [快速入門：使用參數來建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>建立 SetTemperature 命令

為了示範驗證，讓我們建立新的命令，以允許使用者設定溫度。

1. 在[語音 Studio](https://speech.microsoft.com/)中開啟您先前建立的自訂命令應用程式
1. 建立新的命令**SetTemperature**
1. 新增目標溫度的參數
1. 新增溫度參數的驗證
   > [!div class="mx-imgBorder"]
   > ![新增範圍驗證](media/custom-speech-commands/validations-add-temperature.png)

   | 設定           | 建議的值                                          | 描述                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | 名稱              | 溫度                                              | 命令參數的描述性名稱                                                    |
   | 必要          | true                                                     | 指出是否需要此參數值的核取方塊，才能完成命令 |
   | 回應範本 | 「您想要什麼溫度？」                     | 當不知道此參數的值時，所要求的提示                              |
   | 類型              | 數字                                                   | 參數的類型，例如數位、字串或日期時間                                      |
   | 驗證        | 最小值：60，最大值：80                             | 若為數字參數，則為參數允許的值範圍                             |
   | 回應範本 | 「-抱歉，我只能設定60到80度」      | 如果驗證失敗，提示要求更新的值                                       |

1. 新增一些範例句子

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 新增完成規則以確認結果

   | 設定    | 建議的值                                           | 描述                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | 規則名稱  | 確認訊息                                      | 描述規則用途的名稱          |
   | 條件 | 必要參數-溫度                          | 判斷規則何時可執行檔條件    |
   | 動作    | SpeechResponse-"-Ok，設定為 {溫度} 度" | 規則條件為 true 時要採取的動作 |

> [!TIP]
> 這個範例會使用語音回應來確認結果。 如需使用用戶端動作完成命令的範例，請參閱：[如何：使用語音 SDK 在用戶端上完成命令（預覽）](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>試試看

選取 [測試] 面板，然後嘗試一些互動。

- 輸入：將溫度設定為72度
- 輸出：「確定，設定為72度」

- 輸入：將溫度設定為45度
- 輸出：「抱歉，我只能設定60到80度」
- 輸入：請改為72度
- 輸出：「確定，設定為72度」

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：在自訂命令中新增確認（預覽）](./how-to-custom-speech-commands-confirmations.md)
