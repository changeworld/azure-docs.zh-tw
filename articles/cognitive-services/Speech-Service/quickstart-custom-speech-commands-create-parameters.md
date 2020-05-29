---
title: 快速入門：使用參數來建立自訂命令預覽應用程式-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會將參數新增至自訂命令應用程式，讓它可以開啟和關閉多個裝置。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 5461ef9680ab89c8cc9cc2e1166366abb04a6eab
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142277"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>快速入門：使用參數來建立自訂命令預覽應用程式

在[前一篇文章](./quickstart-custom-speech-commands-create-new.md)中，您建立了簡單的自訂命令應用程式，但沒有參數。

在本文中，您將使用參數擴充該應用程式，讓它可以開啟和關閉多個裝置。

## <a name="create-parameters"></a>建立參數

1. 開啟您在[上一篇文章](./quickstart-custom-speech-commands-create-new.md)中建立的專案。

   我們將編輯現有的命令，讓它可以用來開啟和關閉多個裝置。
1. 因為此命令現在會處理 on 和 off，請將它重新命名為**TurnOnOff**。
   1. 在左窗格中選取 [ **homeautomation.turnon** ] 命令，然後選取窗格頂端 [**新增命令**] 旁的省略號（**...**）按鈕。
   
   1. 選取 [**重新命名**]。 在 [**重新命名] 命令**視窗中，將**名稱**變更為**TurOnOff**。 選取 [儲存]。

1. 建立參數來代表使用者是否要開啟或關閉裝置。
   1. 選取中間窗格頂端的 [**新增**]。 在下拉式清單中，選取 [**參數**]。
   1. 在右窗格的 [**參數**] 區段中，于 [**名稱**] 方塊中新增一個值。
   1. 選取 [**必要**]。 在 [**加入必要參數的回應**] 視窗中，選取 [**簡單編輯器**]。 在**第一個 [變化**] 方塊中，輸入下列文字：
        ```
        On or Off?
        ```
   1. 選取 [更新]。

       > [!div class="mx-imgBorder"]
       > ![建立必要的參數回應](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. 設定參數的其餘屬性，如下所示：
       

    | 組態      | 建議的值     | 描述                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **名稱**               | **OnOff**           | 參數的描述性名稱。                                                                  |
    | **為全域**          | 已清除       | 一個核取方塊，指出參數的值是否會全域套用至應用程式中的所有命令。|
    | **必要**           | 已選取         | 指出是否需要參數值的核取方塊。  |
    | **必要參數的回應**      |**簡單編輯器-> 開啟或關閉？**      | 當不知道參數的值時，會出現提示要求。 |
    | **類型**               | **字串**          | 參數類型。 例如，數位、字串、日期時間、地理位置。   |
    | **設定**      | **接受來自內部目錄的預先定義輸入值** | 若為字串，這項設定會將輸入限制為一組可能的值。 |
    | **預先定義的輸入值**     | **開啟**、**關閉**             | 一組可能的值及其別名。         |
       


    > [!div class="mx-imgBorder"]
    > ![建立參數](media/custom-speech-commands/create-on-off-parameter.png)

1. 選取 [儲存] **** 以儲存設定。

 1. 再次選取 [**新增**] 以新增第二個參數。 此參數代表裝置的名稱。 使用下列設定：
   

       | 設定            | 建議的值       | 描述                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **名稱**               | **SubjectDevice**         | 參數的描述性名稱。                                                                     |
       | **為全域**          | 已清除             | 一個核取方塊，指出參數的值是否會全域套用至應用程式中的所有命令。 |
       | **必要**           | 已選取               | 指出是否需要參數值的核取方塊。          |
       | **簡單編輯器**      | **哪一個裝置？**    | 當不知道參數的值時，會出現提示要求。                                       |
       | **類型**               | **字串**                | 參數類型。 例如，數位、字串、日期時間、地理位置。                                                |
       | **設定**      | **接受來自內部目錄的預先定義輸入值** | 若為字串，這項設定會將輸入限制為一組可能的值。       |
       | **預先定義的輸入值** | **電視**、**風扇**               | 一組可能的值及其別名。                               |
       | **別名**（電視）      | **電視**， **telly**     | 每個預先定義輸入值的選擇性別名。                                 |

## <a name="add-example-sentences"></a>新增範例句子

對於具有參數的命令，新增涵蓋所有可能組合的範例句子會很有説明。 例如：

- 完成參數資訊：`turn {OnOff} the {SubjectDevice}`
- 部分參數資訊：`turn it {OnOff}`
- 沒有參數資訊：`turn something`

具有不同資訊數量的範例句子，可讓自訂命令應用程式解析具有部分資訊的一次性解析和多回合解析。

記住這一點之後，請編輯範例句子以使用此處建議的參數：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 在範例句子編輯器中，使用大括弧來參考您的參數： `turn {OnOff} the {SubjectDevice}` 。
>
> 針對先前建立的參數所定義的自動完成，使用 tab 鍵。

## <a name="add-parameters-to-completion-rules"></a>將參數新增至完成規則

修改您在[前一個快速入門](./quickstart-custom-speech-commands-create-new.md)中建立的完成規則。

1. 在 [**條件**] 區段中，選取 [**新增條件**]。
1. 在 [**新增條件**] 視窗的 [**類型**] 清單中，選取 [**必要參數**]。 在清單中，選取 [ **OnOff** ] 和 [ **SubjectDevice**]。
1. 選取 [建立]。
1. 在 [**動作**] 區段中，將滑鼠游標停留在動作上，然後選取 [編輯] 按鈕，以編輯現有的**傳送語音回應**動作。 這次，請使用新的 `OnOff` 和 `SubjectDevice` 參數：

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>試試看
1. 選取右窗格頂端的 [**定型**]。

1. 定型完成時，請選取 [**測試**]。
    
    [**測試您的應用程式**] 視窗隨即出現。

1. 嘗試幾個互動。

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [快速入門：使用自訂命令搭配自訂語音（預覽）](./quickstart-custom-speech-commands-select-custom-voice.md)
