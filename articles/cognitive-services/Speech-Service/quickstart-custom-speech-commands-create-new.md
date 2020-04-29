---
title: 快速入門：建立自訂命令（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會建立並測試託管的自訂命令應用程式。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "76155582"
---
# <a name="quickstart-create-a-custom-command-preview"></a>快速入門：建立自訂命令（預覽）

在本文中，您將瞭解如何建立和測試託管的自訂命令應用程式。
應用程式會辨識「開啟電視」之類的語句，並以簡單的訊息回應「正常，開啟電視」。

## <a name="prerequisites"></a>先決條件

- 語音訂用帳戶。

如果您沒有語音訂用帳戶，您可以流覽至[語音 Studio](https://speech.microsoft.com/)並選取 [**建立語音資源**] 來建立一個。

  > [!div class="mx-imgBorder"]
  > [![建立專案](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > 在預覽期間，僅支援 westus2 區域。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>前往適用于自訂命令的語音 Studio

1. 開啟網頁瀏覽器，並流覽至[語音 Studio](https://speech.microsoft.com/)
1. 輸入您的認證以登入入口網站

   - 預設的視圖是您的語音訂用帳戶清單
     > [!NOTE]
     > 如果您沒有看到 [選取訂用帳戶] 頁面，您可以從頂端列的 [設定] 功能表選擇 [語音資源] 來流覽。

1. 選取您的語音訂用帳戶，然後選取 [**移至 Studio** ]
1. 選取**自訂命令（預覽）**

預設的視圖是您所建立之自訂命令應用程式的清單。

## <a name="create-a-custom-commands-project"></a>建立自訂命令專案

1. 選取 [**新增專案**] 以建立新的專案

   > [!div class="mx-imgBorder"]
   > ![建立專案](media/custom-speech-commands/create-new-project.png)

1. 輸入 [專案名稱] 和 [語言]。
1. 選取 [撰寫中] 資源。 如果沒有有效的撰寫資源，請選取 [**建立新資源**] 加以建立。

   > [!div class="mx-imgBorder"]
   > ![建立資源](media/custom-speech-commands/create-new-resource.png)

   1. 輸入 [資源名稱]、[群組]、[位置] 和 [定價層]。

         > [!NOTE]
         > 您可以在 [資源群組] 欄位中輸入所需的資源組名，以建立資源群組。 當選取 [**建立**] 時，將會建立資源群組。

1. 按一下 [**建立**] 以建立您的專案。
1. 建立之後，請選取您的專案。

您的 view 現在應該是您自訂命令應用程式的總覽。

## <a name="update-luis-resources-optional"></a>更新 LUIS 資源（選擇性）

您可以在 [新增專案] 視窗中更新撰寫資源集，並在執行時間設定用來辨識輸入的預測資源。

> [!NOTE]
> 您必須先設定預測資源，應用程式才會要求預測超過撰寫資源所提供的1000要求。

> [!div class="mx-imgBorder"]
> ![設定 LUIS 資源](media/custom-speech-commands/set-luis-resources.png)

1. 從左窗格中選取 [**設定**]，然後從中間窗格**LUIS [資源**]，以流覽至 [LUIS 資源] 窗格。
1. 選取預測資源，或選取 [**建立新資源**] 來建立一個
1. 選取 [儲存]  。

## <a name="create-a-new-command"></a>建立新的命令

現在您可以建立命令。 讓我們使用一個範例，它會採用單一語句， `turn on the tv`並以訊息`Ok, turning on the TV`回應。

1. 選取 [命令] 旁的`+`圖示，並為其命名，以建立新的命令`TurnOn`
1. 選取 [儲存]  。

> [!div class="mx-imgBorder"]
> ![建立命令](media/custom-speech-commands/create-add-command.png)

命令是一組：

| 群組            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 範例句子 | 使用者可以說觸發此命令的範例語句                                                                 |
| 參數       | 完成命令所需的資訊                                                                                |
| 完成規則 | 要用來完成命令的動作。 例如，回應使用者或與另一個 web 服務通訊 |
| Advanced 規則   | 處理更具體或複雜情況的其他規則                                                              |

### <a name="add-a-sample-sentence"></a>新增範例句子

讓我們從範例句子開始，並提供使用者可以說出的範例：

```
turn on the tv
```

我們目前沒有參數，因此我們可以繼續完成規則。

### <a name="add-a-completion-rule"></a>新增完成規則

現在新增完成規則以回應使用者，指出正在採取動作。

1. 選取 [完成規則] 旁的圖示`+` ，以建立新的完成規則
1. 輸入規則名稱
1. 新增動作
   1. 選取 [動作] 旁的`+`圖示以建立新的語音回應動作，然後選取`SpeechResponse`
   1. 輸入回應

   > [!NOTE]
   > 一般文字的開頭必須是破折號。 如需詳細資訊，請移至[這裡](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![建立語音回應](media/custom-speech-commands/create-speech-response-action.png)

1. 按一下 [**儲存**] 以儲存規則

> [!div class="mx-imgBorder"]
> ![建立完成規則](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 設定    | 建議的值                          | 描述                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 規則名稱  | "ConfirmationResponse"                   | 描述規則用途的名稱          |
| 條件 | 無                                     | 判斷規則何時可執行檔條件    |
| 動作    | SpeechResponse "-Ok，開啟電視" | 規則條件為 true 時要採取的動作 |

## <a name="try-it-out"></a>試試看

使用 [測試聊天] 面板來測試行為。

> [!div class="mx-imgBorder"]
> ![使用網路聊天進行測試](media/custom-speech-commands/create-basic-test-chat.png)

- 您輸入：「開啟電視」
- 預期的回應：「確定，開啟電視」

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：使用參數來建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
