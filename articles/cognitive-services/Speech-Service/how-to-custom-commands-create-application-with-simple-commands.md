---
title: 如何：使用簡單命令建立應用程式-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何使用簡單的命令來建立及測試託管的自訂命令應用程式。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 8e0927ccd8c94f589adf6eb11004b728f697b6e1
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362402"
---
# <a name="create-application-with-simple-commands"></a>使用簡單的命令建立應用程式

在本文中，您將學會如何：
 - 建立空的應用程式
 - 更新 LUIS 資源
 - 將一些基本命令新增至您的自訂命令應用程式

## <a name="create-empty-application"></a>建立空的應用程式
建立空白的自訂命令應用程式。 如需詳細資訊，請參閱[快速入門](quickstart-custom-commands-application.md)。 這次，您不需要匯入專案，而是建立空白專案。

1. 在 [**名稱**] 方塊中，輸入 [專案名稱] 做為 `Smart-Room-Lite` （或您選擇的其他內容）。
1. 在 [**語言**] 清單中，選取 [**英文（美國）**]。
1. 選取或建立您選擇的 LUIS 資源。

   > [!div class="mx-imgBorder"]
   > ![建立專案](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>更新 LUIS 資源（選擇性）

您可以更新您在 [**新增專案**] 視窗中選取的撰寫資源，並設定預測資源。 當您的自訂命令應用程式發行時，會使用預測資源來進行辨識。 在開發和測試階段中，您不需要預測資源。

## <a name="add-turnon-command"></a>新增 Homeautomation.turnon 命令

在您剛建立的空白「**智慧室 Lite** 」自訂命令應用程式中，新增一個簡單的命令來處理語句、 `turn on the tv` ，並以訊息回應 `Ok, turning the tv on` 。

1. 選取左窗格頂端的 [**新增命令**] 來建立新的命令。 [**新增命令**] 視窗隨即開啟。
1. 提供 [**名稱**] 欄位的值做為**homeautomation.turnon**。
1. 選取 [建立]****。

中間窗格會列出命令的不同屬性。 您可以設定命令的下列屬性。 如需命令所有設定屬性的說明，請移至 [[參考](./custom-commands-references.md)]。

| 組態            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **範例句子** | 使用者可以說觸發此命令的範例語句                                                                 |
| **參數**       | 完成命令所需的資訊                                                                                |
| **完成規則** | 要用來完成命令的動作。 例如，回應使用者或與另一個 web 服務通訊。 |
| **互動規則**   | 處理更具體或複雜情況的其他規則                                                              |


> [!div class="mx-imgBorder"]
> ![建立命令](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>新增範例句子

讓我們從**範例句子**一節開始，並提供使用者可以說出的範例。

1. 在中間窗格中選取 [**範例句子**] 區段。
1. 在最右邊的窗格中，新增範例：

    ```
    turn on the tv
    ```

1.  選取窗格頂端的 [**儲存**]。

我們目前沒有參數，因此可以移至**完成規則**一節。

### <a name="add-a-completion-rule"></a>新增完成規則

接下來，命令必須要有完成規則。 此規則會告訴使用者正在採取履行動作。 若要閱讀有關規則和完成規則的詳細資訊，請移至[參考](./custom-commands-references.md)。

1. 選取 [**完成**預設完成規則] 並加以編輯，如下所示：

    
    | 設定    | 建議的值                          | 描述                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **名稱**       | ConfirmationResponse                  | 描述規則用途的名稱          |
    | **條件** | 無                                     | 判斷規則何時可執行檔條件    |
    | **動作**    | 傳送語音回應 > 簡單編輯器 > 第一次變化 >`Ok, turning the tv on` | 規則條件為 true 時要採取的動作 |
    



   > [!div class="mx-imgBorder"]
   > ![建立語音回應](media/custom-commands/create-speech-response-action.png)

1. 選取 [**儲存**] 以儲存動作。
1. 回到 [**完成規則**] 區段中，選取 [**儲存**] 以儲存所有變更。 


 > [!NOTE]
    > 不需要使用命令隨附的預設完成規則。 如有需要，您可以刪除現有的預設完成規則，並新增您自己的規則。

### <a name="try-it-out"></a>試試看

使用測試聊天面板測試行為
1. 選取右窗格頂端的 [**定型**] 圖示。
1. 訓練完成後，選取 [**測試**]。 透過語音/文字試用下列語句：
    - 輸入：開啟電視
    - 輸出： [確定]，開啟電視


> [!div class="mx-imgBorder"]
> ![使用網路聊天進行測試](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> 在 [測試] 面板中，您可以選取 [**開啟詳細資料**]，以取得如何處理此語音/文字輸入的資訊。  

## <a name="add-settemperature-command"></a>新增 SetTemperature 命令

現在，再新增一個命令**SetTemperature** ，它會接受單一語句， `set the temperature to 40 degrees` 並以訊息回應 `Ok, setting temperature to 40 degrees` 。

遵循**homeautomation.turnon**命令所說明的步驟，使用範例句子「**將溫度設定為40度**」來建立新命令。

然後，**編輯現有完成的完成規則**，如下所示：

| 設定    | 建議的值                          |
| ---------- | ---------------------------------------- |
| Name  | ConfirmationResponse                  |
| 條件 | 無                                     |
| 動作    | 傳送語音回應 > 簡單編輯器 > 第一次變化 >`Ok, setting temperature to 40 degrees` |

選取 [**儲存**] 以儲存對命令所做的所有變更。

## <a name="add-setalarm-command"></a>新增 SetAlarm 命令
使用範例句子建立新的命令**SetAlarm** ，「**將鬧鐘設定為明天 9**點」。 然後，**編輯現有完成的完成規則**，如下所示：

| 設定    | 建議的值                          |
| ---------- | ---------------------------------------- |
| 規則名稱  | ConfirmationResponse                  |
| 條件 | 無                                     |
| 動作    | 傳送語音回應 > 簡單編輯器 > 第一次變化 >`Ok, setting an alarm for 9 am tomorrow` |

選取 [**儲存**] 以儲存對命令所做的所有變更。

## <a name="try-it-out"></a>試試看

使用測試聊天面板測試行為
1. 選取 [訓練]。 定型成功之後，請選取 [**測試**] 並試用：
    - 輸入：將溫度設定為40度
    - 預期的回應： [確定]，將溫度設定為40度
    - 輸入：開啟電視
    - 預期的回應： [確定]，開啟電視
    - 輸入：將鬧鐘設定為明天9點
    - 預期的回應： Ok，將鬧鐘設定為明天9點

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：將參數新增至命令](./how-to-custom-commands-add-parameters-to-commands.md)
