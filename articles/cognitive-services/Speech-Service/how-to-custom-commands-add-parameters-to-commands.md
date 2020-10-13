---
title: 如何：將簡單的命令新增至自訂命令應用程式-語音服務
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
ms.openlocfilehash: 0ed237debc2395ed307658b2d57a541574f9478a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284144"
---
# <a name="add-parameters-to-commands"></a>將參數新增至命令

在本文中，您將瞭解如何將參數新增至自訂命令。 參數是命令完成工作所需的資訊。 在複雜的情況下，參數也可以用來定義觸發自訂動作的條件。

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * [How To：使用簡單的命令來建立應用程式](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>設定 Homeautomation.turnon 命令的參數

編輯現有的 **homeautomation.turnon** 命令，以開啟和關閉多個裝置。

1. 由於命令現在會處理 on 和 off 案例，因此請將命令重新命名為 **TurnOnOff**。
   1. 在左窗格中，選取 [ **homeautomation.turnon** ] 命令，然後選取窗格頂端的 [ **新命令** ] 旁的省略號 ( ... ) 按鈕。
   
   1. 選取 [ **重新命名**]。 在 [ **重新命名命令** ] 視窗中，將 [ **名稱** ] 變更為 **TurnOnOff**。

1. 接下來，您要在這個命令中加入新的參數，以代表使用者要開啟或關閉裝置。
   1. 選取中間窗格頂端的 [  **新增** ]。 從下拉式清單中選取 [ **參數**]。
   1. 在右窗格的 [ **參數** ] 區段中，將 [ **名稱** ] 方塊中的值新增為 **OnOff**。
   1. 選取 [ **必要**]。 在 [ **加入必要參數的回應** ] 視窗中，選取 [ **簡單編輯器**]。 在 **第一種變化**中，新增
        ```
        On or Off?
        ```
   1. 選取 [更新]。

       > [!div class="mx-imgBorder"]
       > ![建立必要的參數回應](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 現在我們要設定參數屬性。 如需命令的所有設定屬性的說明，請移至 [ [參考](./custom-commands-references.md)]。 設定參數的屬性，如下所示：
      

       | 組態      | 建議的值     | 說明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名稱               | `OnOff`           | 參數的描述性名稱                                                                           |
       | 為全域          | unchecked       | 核取方塊，指出此參數的值是否全域套用至應用程式中的所有命令|
       | 必要           | checked         | 核取方塊，指出完成命令之前是否需要此參數的值 |
       | 必要參數的回應      |簡單編輯器 > `On or Off?`      | 當已知此參數的值時，提示要求其值 |
       | 類型               | String          | 參數的類型，例如數位、字串、日期時間或地理位置   |
       | 組態      | 接受來自內部目錄的預先定義輸入值 | 若為字串，這會將輸入限制為一組可能的值。 |
       | 預先定義的輸入值     | `on`, `off`           | 可能值和其別名的集合         |
       
        
   1. 若要加入預先定義的輸入值，請選取 [ **加入預先定義的輸入** ]，然後在 [ **新專案**  ] 視窗中，輸入上表中所提供的 **名稱** 。 在此情況下，我們不會使用別名，因此您可以將它保留空白。
   
      > [!div class="mx-imgBorder"]
      > ![建立參數](media/custom-commands/create-on-off-parameter.png)

   1. 選取 [ **儲存** ] 以儲存參數的所有設定。
 
 ### <a name="add-subjectdevice-parameter"></a>新增 SubjectDevice 參數 

   1. 接下來，選取 [ **新增** ] 以新增第二個參數，以代表可使用此命令控制的裝置名稱。 使用下列設定。
   

       | 設定            | 建議的值       |
       | ------------------ | --------------------- |
       | 名稱               | `SubjectDevice`         |
       | 為全域          | unchecked             |
       | 必要           | checked               |
       | 必要參數的回應     | 簡單編輯器 > `Which device do you want to control?`    | 
       | 類型               | String                |          |
       | 組態      | 接受來自內部目錄的預先定義輸入值 | 
       | 預先定義的輸入值 | `tv`, `fan`               |
       | 別名 (`tv`)       | `television`, `telly`     |

   1. 選取 [儲存]。

### <a name="modify-example-sentences"></a>修改範例句子

針對具有參數的命令，新增涵蓋所有可能組合的範例句子會很有説明。 例如：

* 完整參數資訊- `turn {OnOff} the {SubjectDevice}`
* 部分參數資訊- `turn it {OnOff}`
* 沒有參數資訊- `turn something`

具有不同資訊程度的範例句子，可讓自訂命令應用程式以部分資訊解決一次性解析度和多回合解析度。

記住這一點之後，請編輯範例句子以使用以下建議的參數：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

選取 [儲存]****。

> [!TIP]
> 在範例句子編輯器中，請使用大括弧來參考您的參數。 - `turn {OnOff} the {SubjectDevice}` 使用 tab 鍵自動完成（由先前建立的參數所支援）。

### <a name="modify-completion-rules-to-include-parameters"></a>修改完成規則以包含參數

修改現有的完成規則 **ConfirmationResponse**。

1. 在 [ **條件** ] 區段中，選取 [ **新增條件**]。
1. 在 [ **新增條件** ] 視窗的 [ **類型** ] 清單中，選取 [ **必要參數**]。 在下列檢查清單中，檢查 **OnOff** 和 **SubjectDevice**。
1. 將 **IsGlobal** 保留為未核取。
1. 選取 [建立]****。
1. 在 [ **動作** ] 區段中，將滑鼠停留在動作上，然後選取 [編輯] 按鈕，以編輯現有的 **傳送語音回應** 動作。 這次，請使用新建立的 **OnOff** 和 **SubjectDevice** 參數

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 選取 [儲存]****。

### <a name="try-it-out"></a>試做
1. 選取右窗格頂端的 [ **定型** ] 圖示。

1. 定型完成時，選取 [ **測試**]。 **應用程式**視窗的測試隨即出現。
 請嘗試幾個互動。

    - 輸入：關閉電視
    - 輸出：確定，關閉電視
    - 輸入：關閉電視
    - 輸出：確定，關閉電視
    - 輸入：關閉
    - 輸出：您要控制的裝置為何？
    - 輸入：電視
    - 輸出：確定，關閉電視

## <a name="configure-parameters-for-settemperature-command"></a>設定 >settemperature 命令的參數

修改 **>settemperature** 命令，讓它能夠設定使用者所導向的溫度。

使用下列設定來新增參數**溫度**

| 組態      | 建議的值     |
| ------------------ | ----------------|
| 名稱               | `Temperature`           |
| 必要           | checked         |
| 必要參數的回應      | 簡單編輯器 > `What temperature would you like?`
| 類型               | 數字          |


將範例語句編輯為下列值。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

依據下列設定編輯現有的完成規則。

| 組態      | 建議的值     |
| ------------------ | ----------------|
| 條件         | 必要參數 > 溫度           |
| 動作           | 傳送語音回應 > `Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>試做

使用一些互動來**定型**及**測試**變更。

- 輸入：設定溫度
- 輸出：您希望的溫度為何？
- 輸入：50度
- 輸出：確定，將溫度設定為50度

## <a name="configure-parameters-to-the-setalarm-command"></a>設定 SetAlarm 命令的參數

使用下列設定來新增名為 **DateTime** 的參數。

   | 設定                           | 建議的值                     | 
   | --------------------------------- | ----------------------------------------|
   | 名稱                              | `DateTime`                               |
   | 必要                          | checked                                 |
   | 必要參數的回應   | 簡單編輯器 > `For what time?`            | 
   | 類型                              | Datetime                                |
   | 日期預設值                     | 如果日期遺失，請立即使用            |
   | 時間預設值                     | 如果缺少時間，請使用日開頭     |


> [!NOTE]
> 在本文中，我們主要是使用字串、數位和日期時間參數類型。 如需所有支援的參數類型和其屬性的清單，請移至 [ [參考](./custom-commands-references.md)]。


將範例語句編輯為下列值。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

依據下列設定編輯現有的完成規則。

   | 設定    | 建議的值                               |
   | ---------- | ------------------------------------------------------- |
   | 動作    | 傳送語音回應- `Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>試做

**定型** 和 **測試** 變更。
- 輸入：為明天的中午設定鬧鐘
- 輸出：確定，已為 2020-05-02 12:00:00 設定鬧鐘
- 輸入：設定警示
- 輸出：時間為何？
- 輸入：下午5點
- 輸出：確定，已為 2020-05-01 17:00:00 設定鬧鐘


## <a name="try-out-all-the-commands"></a>試用所有命令

使用與不同命令相關的語句，同時測試三個命令。 請注意，您可以在不同的命令之間切換。

- 輸入：設定警示
- 輸出：時間為何？
- 輸入：開啟電視
- 輸出：確定，開啟電視
- 輸入：設定警示
- 輸出：時間為何？
- 輸入：下午5點
- 輸出：確定，已為 2020-05-01 17:00:00 設定鬧鐘

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [如何：將設定新增至命令參數](./how-to-custom-commands-add-parameter-configuration.md)
