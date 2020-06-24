---
title: 如何：將簡單命令新增至自訂命令應用程式-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何將參數新增至自訂命令
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 1d74bf089f3e5bc1fd04232b58ce95c649a170e1
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307985"
---
# <a name="add-parameters-to-commands"></a>將參數新增至命令

在本文中，您將瞭解如何將參數新增至自訂命令。 參數是用來完成工作的命令所需的資訊。 在複雜的案例中，參數也可以用來定義觸發自訂動作的條件。

## <a name="prerequisites"></a>必要條件

> [!div class="checklist"]
> * [如何：使用簡單的命令建立應用程式](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>設定 Homeautomation.turnon 命令的參數

編輯現有的**homeautomation.turnon**命令，以開啟和關閉多個裝置。

1. 因為此命令現在會處理 on 和 off 案例，請將命令重新命名為**TurnOnOff**。
   1. 在左窗格中選取 [ **homeautomation.turnon** ] 命令，然後選取窗格頂端 [**新增命令**] 旁的省略號（...）按鈕。
   
   1. 選取 [**重新命名**]。 在 [**重新命名] 命令**視窗中，將 [**名稱**] 變更為**TurnOnOff**。

1. 接下來，您會將新的參數新增到這個命令，以代表使用者是否要開啟或關閉裝置。
   1. 選取中間窗格頂端的 [**新增**]。 從下拉式選單中，選取 [**參數**]。
   1. 在右窗格的 [**參數**] 區段中，將 [**名稱**] 方塊中的值新增為**OnOff**。
   1. 選取 [**必要**]。 在 [**加入必要參數的回應**] 視窗中，選取 [**簡單編輯器**]。 在**第一種變化**中，新增
        ```
        On or Off?
        ```
   1. 選取 [更新]。

       > [!div class="mx-imgBorder"]
       > ![建立必要的參數回應](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 現在我們要設定參數屬性。 如需命令所有設定屬性的說明，請移至 [[參考](./custom-commands-references.md)]。 設定參數的其餘屬性，如下所示：
      

       | 設定      | 建議的值     | 描述                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名稱               | `OnOff`           | 參數的描述性名稱                                                                           |
       | 為全域          | unchecked       | 核取方塊，指出此參數的值是否會全域套用至應用程式中的所有命令|
       | 必要           | checked         | 指出是否需要此參數值的核取方塊，才能完成命令 |
       | 必要參數的回應      |簡單編輯器 >`On or Off?`      | 當不知道此參數的值時，所要求的提示 |
       | 類型               | String          | 參數的類型，例如數位、字串、日期時間或地理位置   |
       | 設定      | 接受來自內部目錄的預先定義輸入值 | 若為字串，這會將輸入限制為一組可能的值 |
       | 預先定義的輸入值     | `on`, `off`           | 一組可能的值及其別名         |
       
        > [!div class="mx-imgBorder"]
        > ![建立參數](media/custom-commands/create-on-off-parameter.png)

   1. 選取 [**儲存**] 以儲存參數的所有設定。
 
 ### <a name="add-subjectdevice-parameter"></a>新增 SubjectDevice 參數 

   1. 接下來，再次選取 [**新增**] 以新增第二個參數，以代表可以使用此命令控制的裝置名稱。 使用下列設定。
   

       | 設定            | 建議的值       |
       | ------------------ | --------------------- |
       | 名稱               | `SubjectDevice`         |
       | 為全域          | unchecked             |
       | 必要           | checked               |
       | 必要參數的回應     | 簡單編輯器 >`Which device do you want to control?`    | 
       | 類型               | String                |          |
       | 設定      | 接受來自內部目錄的預先定義輸入值 | 
       | 預先定義的輸入值 | `tv`, `fan`               |
       | 別名（ `tv` ）      | `television`, `telly`     |

   1. 選取 [儲存]。

### <a name="modify-example-sentences"></a>修改範例句子

對於具有參數的命令，新增涵蓋所有可能組合的範例句子會很有説明。 例如：

* 完成參數資訊-`turn {OnOff} the {SubjectDevice}`
* 部分參數資訊-`turn it {OnOff}`
* 無參數資訊-`turn something`

具有不同資訊程度的範例句子可讓自訂命令應用程式以部分資訊解析一次解析度和多回合解析。

記住這一點之後，請編輯範例句子來使用參數，如下所示：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

選取 [儲存]。

> [!TIP]
> 在範例句子編輯器中，使用大括弧來參考您的參數。 - `turn {OnOff} the {SubjectDevice}`使用 tab 鍵自動完成，由先前建立的參數所支援。

### <a name="modify-completion-rules-to-include-parameters"></a>修改完成規則以包含參數

修改現有的完成規則**ConfirmationResponse**。

1. 在 [**條件**] 區段中，選取 [**新增條件**]。
1. 在 [**新增條件**] 視窗的 [**類型**] 清單中，選取 [**必要參數**]。 在下面的檢查清單中，檢查**OnOff**和**SubjectDevice**。
1. 選取 [建立]。
1. 在 [**動作**] 區段中，將滑鼠游標停留在動作上，然後選取 [編輯] 按鈕，以編輯現有的**傳送語音回應**動作。 這次，請使用新建立的**OnOff**和**SubjectDevice**參數

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 選取 [儲存]。

### <a name="try-it-out"></a>試試看
1. 選取右窗格頂端的 [**定型**] 圖示。

1. 訓練完成時，選取 [**測試**]。 [**測試您的應用程式**] 視窗隨即出現。
 嘗試幾個互動。

    - 輸入：關閉電視
    - 輸出：正常，關閉電視
    - 輸入：關閉電視
    - 輸出：正常，關閉電視
    - 輸入：關閉
    - 輸出：您要控制哪一個裝置？
    - 輸入：電視
    - 輸出：正常，關閉電視

## <a name="configure-parameters-for-settemperature-command"></a>設定 SetTemperature 命令的參數

修改**SetTemperature**命令，讓它設定使用者所導向的溫度。

使用下列設定來新增參數**溫度**

| 設定      | 建議的值     |
| ------------------ | ----------------|
| 名稱               | `Temperature`           |
| 必要           | checked         |
| 必要參數的回應      | 簡單編輯器 >`What temperature would you like?`
| 類型               | Number          |


將範例語句編輯為下列值。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

根據下列設定編輯現有的完成規則。

| 設定      | 建議的值     |
| ------------------ | ----------------|
| 條件         | 必要參數 > 溫度           |
| 動作           | 傳送語音回應 >`Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>試試看

使用一些互動來**定型**和**測試**變更。

- 輸入：設定溫度
- 輸出：您要的溫度為何？
- 輸入：50度
- 輸出：正常，將溫度設定為50度

## <a name="configure-parameters-to-the-setalarm-command"></a>設定 SetAlarm 命令的參數

使用下列設定新增名為**DateTime**的參數。

   | 設定                           | 建議的值                     | 
   | --------------------------------- | ----------------------------------------|
   | 名稱                              | `DateTime`                               |
   | 必要                          | checked                                 |
   | 必要參數的回應   | 簡單編輯器 >`For what time?`            | 
   | 類型                              | Datetime                                |
   | 日期預設值                     | 如果日期遺失，請立即使用            |
   | 時間預設值                     | 如果遺失時間，使用日開始     |


> [!NOTE]
> 在本文中，我們主要使用了 string、number 和 DateTime 參數類型。 如需所有支援的參數類型及其屬性的清單，請移至 [[參考](./custom-commands-references.md)]。


將範例語句編輯為下列值。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

根據下列設定編輯現有的完成規則。

   | 設定    | 建議的值                               |
   | ---------- | ------------------------------------------------------- |
   | 動作    | 傳送語音回應-`Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>試試看

**定型**和**測試**變更。
- 輸入：為明天的中午設定鬧鐘
- 輸出：正常，已為 2020-05-02 12:00:00 設定鬧鐘
- 輸入：設定鬧鐘
- 輸出：何謂時間？
- 輸入：下午5
- 輸出：正常，已為 2020-05-01 17:00:00 設定鬧鐘


## <a name="try-out-all-the-commands"></a>試用所有命令

使用與不同命令相關的語句，同時測試三個命令。 請注意，您可以在不同的命令之間切換。

- 輸入：設定鬧鐘
- 輸出：針對什麼時間？
- 輸入：開啟電視
- 輸出： [確定]，開啟電視
- 輸入：設定鬧鐘
- 輸出：針對什麼時間？
- 輸入：下午5
- 輸出：正常，已為 2020-05-01 17:00:00 設定鬧鐘

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：將設定新增至命令參數](./how-to-custom-commands-add-parameter-configuration.md)
