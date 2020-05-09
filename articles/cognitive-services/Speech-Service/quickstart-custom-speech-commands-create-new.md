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
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872516"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>快速入門：建立自訂命令應用程式（預覽）

在本快速入門中，您將瞭解如何建立和測試自訂命令應用程式。
建立的應用程式將會處理像是「開啟電視」的語句，並以簡單的訊息回復「正常，開啟電視」。

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立 Azure 語音資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > 目前，自訂命令僅支援 westus、westus2 和 neur 區域中的語音訂用帳戶。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>前往適用于自訂命令的語音 Studio

1. 開啟網頁瀏覽器，並流覽至[語音 Studio](https://speech.microsoft.com/)
1. 輸入您的認證來登入此入口網站。

   - 預設的視圖是您的語音訂用帳戶清單。
     > [!NOTE]
     > 如果您沒有看到 [選取訂用帳戶] 頁面，您可以從頂端列的 [設定] 功能表選擇 [語音資源] 來流覽。

1. 選取您的語音訂用帳戶，然後選取 [**移至 Studio**]。
1. 選取 [**自訂命令**]。

     - 預設的 view 是您所選訂用帳戶下的自訂命令應用程式清單。

## <a name="create-a-custom-commands-project"></a>建立自訂命令專案

1. 選取 [**新增專案**] 以建立新的專案。

   > [!div class="mx-imgBorder"]
   > ![建立專案](media/custom-speech-commands/create-new-project.png)

1. 輸入專案名稱。
1. 從下拉式選單中選取 [語言]。
1. 從下拉式選單中選取 [撰寫資源]。 如果沒有有效的撰寫資源，請按一下 [**建立新的 LUIS 撰寫資源**] 建立一個。

   > [!div class="mx-imgBorder"]
   > ![建立資源](media/custom-speech-commands/create-new-resource.png)

   - 輸入 [資源名稱]、[資源群組]。
   - 從下拉式選單選擇 [位置] 和 [定價層] 的 [值]。

      > [!NOTE]
      > 您可以在 [資源群組] 欄位中輸入所需的資源組名，以建立資源群組。 當選取 [**建立**] 時，將會建立資源群組。

1. 接下來，選取 [**建立**] 以建立您的專案。
1. 建立之後，請選取您的專案。

    - 您的視圖現在應該會概述新建立的自訂命令應用程式。

## <a name="update-luis-resources-optional"></a>更新 LUIS 資源（選擇性）

您可以更新在 [新增專案] 視窗中設定的撰寫資源，並設定預測資源。 當您的自訂命令應用程式發佈之後，就會使用預測資源來進行辨識。 您不需要開發和測試階段的預測資源。

1. 從左窗格中選取 [**設定**]，然後流覽至中間窗格中的 [ **LUIS 資源**] 區段。
1. 選取預測資源，或選取 [**建立新資源**] 來建立一個。
1. 選取 [儲存]  。
    
    > [!div class="mx-imgBorder"]
    > ![設定 LUIS 資源](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> 由於撰寫資源僅支援每月1000個預測端點要求，因此您必須在發佈自訂命令應用程式之前，mandatorily 必須先設定 LUIS 預測資源。


## <a name="create-a-new-command"></a>建立新的命令

讓我們建立一個簡單的命令，它會採用單一語句`turn on the tv`，並以訊息`Ok, turning on the tv`回應。

1. 選取最左邊窗格頂端的圖示`+ New command` ，建立新的命令。 新的快顯視窗會出現標題為 [**新增命令**]。
1. 將 [**名稱**] 欄位的值`TurnOn`提供為。
1. 選取 [建立]  。

中間窗格會登記命令的不同屬性：


| 組態            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 範例句子 | 使用者可以說觸發此命令的範例語句                                                                 |
| 參數       | 完成命令所需的資訊                                                                                |
| 完成規則 | 要用來完成命令的動作。 例如，回應使用者或與另一個 web 服務通訊。 |
| 互動規則   | 處理更具體或複雜情況的其他規則                                                              |


> [!div class="mx-imgBorder"]
> ![建立命令](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>新增範例句子

讓我們從範例句子一節開始，並提供使用者可以說出的範例。

1. 從中間窗格選取 [**範例句子**] 區段，然後在最右邊的窗格中，新增範例：

    ```
    turn on the tv
    ```

1. 選取`Save`此窗格頂端的 [圖示]。

我們目前沒有參數，因此我們可以繼續進行 [**完成規則**] 區段。

### <a name="add-a-completion-rule"></a>新增完成規則

現在，使用下列設定來新增完成規則。 此規則表示正在採取履行動作的使用者。


| 設定    | 建議的值                          | 描述                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 規則名稱  | ConfirmationResponse                  | 描述規則用途的名稱          |
| 條件 | 無                                     | 判斷規則何時可執行檔條件    |
| 動作    | SpeechResponse "-Ok，開啟電視" | 規則條件為 true 時要採取的動作 |

1. 選取中間窗格頂端的`+Add`圖示，以建立新的完成規則。
1. 在 [**名稱**] 區段中提供值。
1. 新增動作
   1. 選取 [**動作**] 區段中的 [ **+ 新增] 動作**，以建立新的動作。
   1. 在 [**新增動作**] 快顯視窗中， `Send speech response`從 [**類型**] 的下拉式選項中選取。
   1. 針對`Simple editor` [**回應**] 欄位選擇。
       - 在**第一個 [變化**] 欄位中，將 [回應] 的值提供為`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![建立語音回應](media/custom-speech-commands/create-speech-response-action.png)

1. 按一下 [**儲存**] 以儲存規則。
1. 回到 [**完成規則**] 區段中，選取 [**儲存**] 以儲存所有變更。 

> [!div class="mx-imgBorder"]
> ![建立完成規則](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>試試看

使用測試聊天面板測試行為
1. 選取`Train`右窗格頂端的 [圖示]。
1. 一旦完成訓練，請選取`Test`。 您的**應用程式**視窗會出現新的測試。
    - 輸入：開啟電視
    - 預期的回應： [確定]，開啟電視


> [!div class="mx-imgBorder"]
> ![使用網路聊天進行測試](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：使用參數來建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
