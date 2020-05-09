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
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853594"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>快速入門：使用參數來建立自訂命令應用程式（預覽）

在[前一篇文章](./quickstart-custom-speech-commands-create-new.md)中，您建立了簡單的自訂命令應用程式，但沒有參數。

在本文中，您將擴充此應用程式以使用參數，讓它可以處理開啟和關閉多個裝置。

## <a name="create-parameters"></a>Create Parameters

1. 開啟[您先前建立](./quickstart-custom-speech-commands-create-new.md)的專案
1. 讓我們編輯現有的命令，以開啟和關閉多個裝置。
1. 因為此命令現在會處理 on 和 off，請將命令重新`TurnOnOff`命名為。
   - 在左窗格中選取`TurnOn`命令，然後按一下窗格頂端的`...` [ `+ New command`開啟] 圖示。
   
   - 選取`Rename` [圖示]。 在 [**重新命名] 命令**快顯視窗中，將`TurOnOff`[**名稱**] 變更為。 接下來，選取 [儲存]****。

1. 接下來，您會建立新的參數來表示使用者是否要開啟或關閉裝置。
   - 選取`+ Add`中間窗格頂端的 [圖示]。 從下拉式選單中，選取 [**參數**]。
   - 在最右邊的窗格中，您可以看到 [**參數**設定] 區段。
   - 新增 [**名稱**] 的值。
   - 勾選 [**必要**] 核取方塊。 在 [**新增必要參數的回應**] 視窗中，選取 [**簡單編輯器**]，並在**第一個變化**中加入
        ```
        On or Off?
        ```
   - 選取 [更新]  。

       > [!div class="mx-imgBorder"]
       > ![建立必要的參數回應](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - 接下來，我們將設定參數的其餘屬性，如下所示，然後`Save`選取將所有設定儲存至參數。
       

       | 組態      | 建議的值     | 描述                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名稱               | OnOff           | 參數的描述性名稱                                                                           |
       | 為全域          | unchecked       | 核取方塊，指出此參數的值是否會全域套用至應用程式中的所有命令|
       | 必要           | checked         | 指出是否需要此參數值的核取方塊，才能完成命令 |
       | 必要參數的回應      |簡單編輯器-> 開啟或關閉？      | 當不知道此參數的值時，所要求的提示 |
       | 類型               | String          | 參數的類型，例如數位、字串、日期時間或地理位置   |
       | 組態      | 接受來自內部目錄的預先定義輸入值 | 若為字串，這會將輸入限制為一組可能的值 |
       | 預先定義的輸入值     | on、off             | 一組可能的值及其別名         |
       
        > [!div class="mx-imgBorder"]
        > ![建立參數](media/custom-speech-commands/create-on-off-parameter.png)

   - 接下來，再次`+ Add`選取圖示以新增第二個參數，以使用下列設定來代表裝置的名稱。
   

       | 設定            | 建議的值       | 描述                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | 名稱               | SubjectDevice         | 參數的描述性名稱                                                                     |
       | 為全域          | unchecked             | 核取方塊，指出此參數的值是否會全域套用至應用程式中的所有命令 |
       | 必要           | checked               | 指出是否需要此參數值的核取方塊，才能完成命令          |
       | 簡單編輯器      | 哪一個裝置？    | 當不知道此參數的值時，所要求的提示                                       |
       | 類型               | String                | 參數的類型，例如數位、字串、日期時間或地理位置                                                |
       | 組態      | 接受來自內部目錄的預先定義輸入值 | 若為字串，字串清單會將輸入限制為一組可能值       |
       | 預先定義的輸入值 | 電視、風扇               | 一組可能的值及其別名                               |
       | 別名（電視）      | 電視，telly     | 預先定義輸入值之每個可能值的選擇性別名                                 |

## <a name="add-example-sentences"></a>新增範例句子

透過具有參數的命令，新增涵蓋所有可能組合的範例句子會很有説明。 例如：

1. 完成參數資訊-`turn {OnOff} the {SubjectDevice}`
1. 部分參數資訊-`turn it {OnOff}`
1. 無參數資訊-`turn something`

具有不同資訊程度的範例句子可讓自訂命令應用程式以部分資訊解析一次解析度和多回合解析。

記住這一點之後，請編輯範例句子以使用這些參數，如下所示。

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 在範例句子編輯器中，使用大括弧來參考您的參數。 - `turn {OnOff} the {SubjectDevice}`使用 tab 鍵自動完成，由先前建立的參數所支援。

## <a name="add-parameters-to-completion-rules"></a>將參數新增至完成規則

修改我們在[先前的快速入門](./quickstart-custom-speech-commands-create-new.md)中建立的完成規則。

1. 在 [**條件**] 區段中，選取 [ **+** 新增條件] 來新增條件
1. 在新的快顯**新條件**中，從`Required parameters` [**類型**] 下拉式選單中選取。 在下面的檢查清單中，檢查`OnOff`和。 `SubjectDevice`
1. 按一下 [**建立**]。
1. 在 [**動作**] 區段中，將滑鼠游標停留在動作上，然後按一下 [編輯] 圖示，以編輯現有的 [傳送語音回應] 動作。 這一次，我們將使用新建立`OnOff`的和`SubjectDevice`參數

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>試試看
1. 選取`Train`右窗格頂端的 [圖示]。

1. 一旦完成訓練，請選取`Test`。
    - 您的**應用程式**視窗會出現新的測試。
    - 嘗試幾個互動。

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
