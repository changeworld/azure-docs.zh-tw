---
title: How to：開發自訂命令應用程式-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何開發和自訂命令應用程式。 這些語音命令應用程式最適合用於工作完成或命令和控制案例。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: b3e9c1e8ad23ea0ebf540eddbd6d4a03b8a72fe2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835069"
---
# <a name="develop-custom-commands-applications"></a>開發自訂命令應用程式

在此操作說明文章中，您將瞭解如何開發和設定自訂命令應用程式。 自訂命令功能可協助您建立豐富的語音命令應用程式，這些應用程式已針對語音優先互動體驗進行優化。 這項功能最適合用於工作完成或命令和控制案例。 它特別適用于物聯網 (IoT) 裝置以及環境和無周邊裝置。

在本文中，您將建立可以開啟和關閉電視的應用程式、設定溫度，以及設定鬧鐘。 建立這些基本命令之後，您將會瞭解下列自訂命令的選項：

* 將參數新增至命令
* 將設定新增至命令參數
* 建立互動規則
* 建立語音回應的語言產生範本
* 使用自訂語音工具

## <a name="create-an-application-by-using-simple-commands"></a>使用簡單的命令來建立應用程式

首先，建立一個空白的自訂命令應用程式。 如需詳細資訊，請參閱 [快速入門](quickstart-custom-commands-application.md)。 在此應用程式中，您會建立空白專案，而不是匯入專案。

1. 在 [ **名稱** ] 方塊中，輸入專案名稱 *-Lite* (或您選擇的另一個名稱) 。
1. 在 [ **語言** ] 清單中，選取 [ **英文 (美國)**。
1. 選取或建立 LUIS 資源。

   > [!div class="mx-imgBorder"]
   > ![顯示 [新增專案] 視窗的螢幕擷取畫面。](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a> (選用) 更新 LUIS 資源

您可以更新在 [ **新增專案** ] 視窗中選取的撰寫資源。 您也可以設定預測資源。 

當您的自訂命令應用程式發行時，會使用預測資源來進行辨識。 在開發和測試階段，您不需要預測資源。

### <a name="add-a-turnon-command"></a>新增 Homeautomation.turnon 命令

在您所建立的空白智慧室 Lite 自訂命令應用程式中，新增命令。 此命令會處理語句 `Turn on the tv` 。 它會以訊息回應 `Ok, turning the tv on` 。

1. 選取左窗格頂端的 [ **新增命令** ]，以建立新的命令。 **新的命令** 視窗隨即開啟。
1. 針對 [ **名稱** ] 欄位提供值 `TurnOn` 。
1. 選取 [建立]。

中間窗格會列出命令的屬性。 

下表說明命令的設定屬性。 如需詳細資訊，請參閱 [自訂命令概念和定義](./custom-commands-references.md)。

| 組態            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 句子範例 | 範例語句使用者可以說觸發這個命令。                                                                 |
| 參數       | 完成命令所需的資訊。                                                                                |
| 完成規則 | 要執行以完成命令的動作。 範例：回應使用者或與 web 服務進行通訊。 |
| 互動規則   | 處理更具體或複雜情況的其他規則。                                                              |


> [!div class="mx-imgBorder"]
> ![螢幕擷取畫面，顯示要在哪裡建立命令。](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>新增範例句子

在 **範例句子** 區段中，您會提供使用者可以說出的範例。

1. 在中間窗格中，選取 [ **範例句子**]。
1. 在右側窗格中，新增範例：

    ```
    Turn on the tv
    ```

1.  在頁面上方選取 [儲存]。

您還沒有參數，所以您可以移至 **完成規則** 區段。

#### <a name="add-a-completion-rule"></a>新增完成規則

接下來，命令需要完成規則。 此規則會告訴使用者正在執行履行動作。 

如需規則和完成規則的詳細資訊，請參閱 [自訂命令概念和定義](./custom-commands-references.md)。

1. 選取 [ **完成**] 預設完成規則。 然後進行編輯，如下所示：

    
    | 設定    | 建議的值                          | 描述                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **名稱**       | `ConfirmationResponse`                  | 描述規則用途的名稱          |
    | **條件** | 無                                     | 判斷規則何時可以執行的條件    |
    | **動作**    | **傳送語音回應**  > **簡單編輯器**  > **第一個變化** > `Ok, turning the tv on` | 當規則條件為真時所要採取的動作 |

   > [!div class="mx-imgBorder"]
   > ![顯示要在哪裡建立語音回應的螢幕擷取畫面。](media/custom-commands/create-speech-response-action.png)

1. 選取 [ **儲存** ] 以儲存動作。
1. 回到 [ **完成規則** ] 區段中，選取 [ **儲存** ] 以儲存所有變更。 

    > [!NOTE]
    > 您不需要使用命令隨附的預設完成規則。 您可以刪除預設的完成規則，並新增您自己的規則。

### <a name="add-a-settemperature-command"></a>新增 >settemperature 命令

現在再新增另一個命令 `SetTemperature` 。 此命令會採用單一語句， `Set the temperature to 40 degrees` 並以訊息回應 `Ok, setting temperature to 40 degrees` 。

若要建立新的命令，請遵循命令所使用的步驟 `TurnOn` ，但使用範例句子 `Set the temperature to 40 degrees` 。

然後 **編輯現有的完成規則** ，如下所示：

| 設定    | 建議的值                          |
| ---------- | ---------------------------------------- |
| **名稱**  | `ConfirmationResponse`                  |
| **條件** | 無                                     |
| **動作**    | **傳送語音回應**  > **簡單編輯器**  > **第一個變化** > `Ok, setting temperature to 40 degrees` |

選取 [ **儲存** ] 以儲存命令的所有變更。

### <a name="add-a-setalarm-command"></a>新增 SetAlarm 命令

建立新的 `SetAlarm` 命令。 使用範例句子 `Set an alarm for 9 am tomorrow` 。 然後 **編輯現有的完成規則** ，如下所示：

| 設定    | 建議的值                          |
| ---------- | ---------------------------------------- |
| **名稱**  | `ConfirmationResponse`                  |
| **條件** | 無                                     |
| **動作**    | **傳送語音回應**  > **簡單編輯器**  > **第一個變化** > `Ok, setting an alarm for 9 am tomorrow` |

選取 [ **儲存** ] 以儲存命令的所有變更。

### <a name="try-it-out"></a>試做

使用 [測試] 窗格來測試應用程式的行為： 

1. 選取窗格右上角的 [ **定型** ] 圖示。 
1. 定型完成時，選取 [ **測試**]。 

使用語音或文字來嘗試下列語句範例：

- 輸入： *將溫度設定為40度*
- 預期的回應：確定，將溫度設定為40度
- 輸入： *開啟電視*
- 預期的回應：確定，開啟電視
- 您輸入：*將鬧鐘設定為明天 9* 點
- 預期的回應：確定，在明天設定9的鬧鐘

> [!div class="mx-imgBorder"]
> ![螢幕擷取畫面，顯示網路聊天介面中的測試。](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> 在 [測試] 窗格中，您可以選取 [ **轉換詳細資料** ]，以取得如何處理此語音輸入或文字輸入的相關資訊。

## <a name="add-parameters-to-commands"></a>將參數新增至命令

在本節中，您會瞭解如何將參數新增至命令。 命令需要參數才能完成工作。 在複雜的案例中，您可以使用參數來定義觸發自訂動作的條件。

### <a name="configure-parameters-for-a-turnon-command"></a>設定 Homeautomation.turnon 命令的參數

從編輯現有的 `TurnOn` 命令開始，以開啟和關閉多個裝置。

1. 現在命令會處理 on 和 off 案例，請將命令重新命名為 *TurnOnOff*。
   1. 在左側窗格中，選取 [ **homeautomation.turnon** ] 命令。 然後在窗格頂端的 [ **新命令** ] 旁，選取省略號 (**...**) 按鈕。
   
   1. 選取 [ **重新命名**]。 在 [ **重新命名] 命令** 視窗中，將名稱變更為 *TurnOnOff*。

1. 將新的參數新增至命令。 參數代表使用者要開啟或關閉裝置。
   1. 在中間窗格的頂端，選取 [  **新增**]。 從下拉式功能表中選取 [ **參數**]。
   1. 在右側窗格的 [ **參數** ] 區段中，于 [ **名稱** ] 方塊中加入 `OnOff` 。
   1. 選取 [ **必要**]。 在 [ **加入必要參數的回應** ] 視窗中，選取 [ **簡單編輯器**]。 在 **第一個變化** 欄位中，加入 *或關閉？*。
   1. 選取 [更新]。

       > [!div class="mx-imgBorder"]
       > ![顯示要在哪裡建立必要參數回應的螢幕擷取畫面。](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 使用下表來設定參數的屬性。 如需命令的所有設定屬性的相關資訊，請參閱 [自訂命令概念和定義](./custom-commands-references.md)。
      

       | 設定      | 建議的值     | 描述                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **名稱**               | `OnOff`           | 參數的描述性名稱                                                                           |
       | **為全域**          | 未選取       | 核取方塊，指出此參數的值是否全域套用至應用程式中的所有命令。|
       | **必要**           | 已選取         | 核取方塊，指出命令完成之前是否需要此參數的值。 |
       | **必要參數的回應**      |**簡單編輯器** > `On or Off?`      | 當已知此參數的值時，提示您要求此參數的值。 |
       | **型別**               | **String**          | 參數類型，例如數位、字串、日期時間或地理位置。   |
       | **設定**      | **接受來自內部目錄的預先定義輸入值** | 若為字串，此設定會將輸入限制為一組可能的值。 |
       | **預先定義的輸入值**     | `on`, `off`           | 可能值和其別名的集合。         |
       
        
   1. 若要加入預先定義的輸入值，請選取 [ **新增預先定義的輸入**]。 在 [ **新專案**  ] 視窗中，輸入 *名稱* ，如上表所示。 在此情況下，您不會使用別名，因此您可以將此欄位保留空白。
   
      > [!div class="mx-imgBorder"]
      > ![顯示如何建立參數的螢幕擷取畫面。](media/custom-commands/create-on-off-parameter.png)

   1. 選取 [ **儲存** ] 以儲存參數的所有設定。
 
#### <a name="add-a-subjectdevice-parameter"></a>新增 SubjectDevice 參數

1. 若要新增第二個參數來代表可使用此命令控制的裝置名稱，請選取 [ **新增**]。 使用下列設定。


    | 設定            | 建議的值       |
    | ------------------ | --------------------- |
    | **名稱**               | `SubjectDevice`         |
    | **為全域**          | 未選取             |
    | **必要**           | 已選取               |
    | **必要參數的回應**     | **簡單編輯器** > `Which device do you want to control?`    | 
    | **型別**               | **String**                |          |
    | **設定**      | **接受來自內部目錄的預先定義輸入值** | 
    | **預先定義的輸入值** | `tv`, `fan`               |
    | **別名** (`tv`)       | `television`, `telly`     |

1. 選取 [儲存]。

#### <a name="modify-example-sentences"></a>修改範例句子

針對使用參數的命令，新增涵蓋所有可能組合的範例句子會很有説明。 例如：

* 完整的參數資訊： `turn {OnOff} the {SubjectDevice}`
* 部分參數資訊： `turn it {OnOff}`
* 沒有參數資訊： `turn something`

使用不同資訊程度的範例句子，可讓自訂命令應用程式使用部分資訊來解決單次解析和多回合解析度。

記住這項資訊後，請編輯範例句子以使用這些建議的參數：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

選取 [儲存]。

> [!TIP]
> 在範例-句子編輯器中，使用大括弧來參考您的參數。 例如： `turn {OnOff} the {SubjectDevice}` 。
> 使用 tab 鍵自動完成（由先前建立的參數所支援）。

#### <a name="modify-completion-rules-to-include-parameters"></a>修改完成規則以包含參數

修改現有的完成規則 `ConfirmationResponse` 。

1. 在 [ **條件** ] 區段中，選取 [ **新增條件**]。
1. 在 [ **新增條件** ] 視窗的 [ **類型** ] 清單中，選取 [ **必要參數**]。 在接下來的清單中，選取 [ **OnOff** ] 和 [ **SubjectDevice**]。
1. 將 **IsGlobal** 保留為未選取狀態。
1. 選取 [建立]。
1. 在 [ **動作** ] 區段中，將滑鼠停留在其上方，然後選取 [編輯] 按鈕，以編輯 [ **傳送語音回應** ] 動作。 這次，請使用新建立的 `OnOff` 和 `SubjectDevice` 參數：

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 選取 [儲存]。

選取右側窗格頂端的 [ **定型** ] 圖示，以嘗試變更。 

定型完成時，選取 [ **測試**]。 [ **測試應用程式** ] 視窗隨即出現。 請嘗試下列互動：

- 輸入：關閉 *電視*
- 輸出：確定，關閉電視
- 輸入：關閉 *電視*
- 輸出：確定，關閉電視
- 輸入： *關閉*
- 輸出：您要控制的裝置為何？
- 輸入： *電視*
- 輸出：確定，關閉電視

### <a name="configure-parameters-for-a-settemperature-command"></a>設定 >settemperature 命令的參數

修改 `SetTemperature` 命令，讓它能夠在使用者指示時設定溫度。

加入 `Temperature` 參數。 請使用下列組態︰

| 設定      | 建議的值     |
| ------------------ | ----------------|
| **名稱**               | `Temperature`           |
| **必要**           | 已選取         |
| **必要參數的回應**      | **簡單編輯器** > `What temperature would you like?`
| **型別**               | `Number`          |


編輯範例語句以使用下列值。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

編輯現有的完成規則。 使用下列設定。

| 設定      | 建議的值     |
| ------------------ | ----------------|
| **條件**         | **必要參數**  > **溫度**           |
| **動作**           | **傳送語音回應** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>設定 SetAlarm 命令的參數

加入名為的參數 `DateTime` 。 使用下列設定。

   | 設定                           | 建議的值                     | 
   | --------------------------------- | ----------------------------------------|
   | **名稱**                              | `DateTime`                               |
   | **必要**                          | 已選取                                 |
   | **必要參數的回應**   | **簡單編輯器** > `For what time?`            | 
   | **型別**                              | **DateTime**                                |
   | **日期預設值**                     | 如果日期遺失，請立即使用。            |
   | **時間預設值**                     | 如果時間遺失，請使用一天的開始時間。     |


> [!NOTE]
> 本文大多使用字串、數位和日期時間參數類型。 如需所有支援的參數類型和其屬性的清單，請參閱 [自訂命令概念和定義](./custom-commands-references.md)。


編輯範例語句。 請使用下面的值。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

編輯現有的完成規則。 使用下列設定。

   | 設定    | 建議的值                               |
   | ---------- | ------------------------------------------------------- |
   | **動作**    | **傳送語音回應** > `Ok, alarm set for {DateTime}`  |

使用與不同命令相關的語句，同時測試三個命令。  (您可以在不同的命令之間切換。 ) 

- 輸入：*設定* 警示
- 輸出：時間為何？
- 輸入： *開啟電視*
- 輸出：確定，開啟電視
- 輸入：*設定* 警示
- 輸出：時間為何？
- 輸入：*下午 5* 點
- 輸出：確定，已為 2020-05-01 17:00:00 設定鬧鐘

## <a name="add-configurations-to-command-parameters"></a>將設定新增至命令參數

在本節中，您會深入瞭解 advanced 參數設定，包括：

 - 參數值如何屬於在自訂命令應用程式之外定義的集合。
 - 如何在參數值上加入驗證子句。

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>將參數設定為外部目錄實體

自訂命令功能可讓您設定字串類型參數，以參考裝載于 web 端點的外部目錄。 因此，您可以獨立更新外部類別目錄，而不需要編輯自訂命令應用程式。 這種方法在目錄專案很多的情況下很有用。

`SubjectDevice`從命令重複使用參數 `TurnOnOff` 。 此參數目前的設定會 **接受來自內部目錄的預先定義輸入**。 這項設定是指參數設定中的靜態裝置清單。 將此內容移至可獨立更新的外部資料源。

若要移動內容，請先加入新的 web 端點。 在左側窗格中，移至 [ **Web 端點** ] 區段。 在該處，加入新的 web 端點。 使用下列設定。

| 設定 | 建議的值 |
|----|----|
| **名稱** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **方法** | **GET** |


如果 URL 的建議值不適用於您，請設定並裝載 web 端點，此端點會傳回 JSON 檔案，該檔案包含可控制的裝置清單。 Web 端點應傳回格式如下的 JSON 檔：
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

接下來，移至 [ **SubjectDevice** 參數設定] 頁面。 設定下列屬性。

| 設定 | 建議的值 |
| ----| ---- |
| **設定** | **接受來自外部目錄的預先定義輸入** |                               
| **目錄端點** | `getDevices` |
| **方法** | **GET** |

接著，選取 [儲存]。

> [!IMPORTANT]
> 除非您已在左側窗格的 [ **web 端點** ] 區段中設定 web 端點，否則您不會看到設定參數以接受來自外部目錄之輸入的選項。

選取 [ **定型**] 來試用。 定型完成後，請選取 [ **測試** ] 並嘗試幾個互動。

* 輸入： *開啟*
* 輸出：您要控制的裝置為何？
* 輸入： *燈*
* 輸出：確定，開啟燈

> [!NOTE]
> 您現在可以控制 web 端點上裝載的所有裝置。 但是您仍然需要訓練應用程式來測試新的變更，然後重新發佈應用程式。

### <a name="add-validation-to-parameters"></a>將驗證新增至參數

*驗證* 是適用于特定參數類型的結構，可讓您設定參數值的條件約束。 如果值不在限制範圍內，則會提示您進行更正。 如需擴充驗證結構的參數類型清單，請參閱 [自訂命令概念和定義](./custom-commands-references.md)。

使用命令來測試驗證 `SetTemperature` 。 使用下列步驟來新增參數的驗證 `Temperature` 。

1. 在左側窗格中，選取 [ **>settemperature** ] 命令。
1. 在中間窗格中，選取 [ **溫度**]。
1. 在右側窗格中，選取 [ **加入驗證**]。
1. 在 **新的驗證** 視窗中，如下表所示設定驗證。 然後選取 [建立]。


    | 參數設定 | 建議的值 | 描述 |
    | ---- | ---- | ---- |
    | **最小值** | `60` | 若為數字參數，此參數可以採用的最小值 |
    | **最大值** | `80` | 若為數字參數，此參數可以採用的最大值 |
    | **失敗回應** |  **簡單編輯器**  > **第一個變化** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 如果驗證失敗，會提示要求新值 |

    > [!div class="mx-imgBorder"]
    > ![顯示如何新增範圍驗證的螢幕擷取畫面。](media/custom-commands/add-validations-temperature.png)

選取右側窗格頂端的 [ **定型** ] 圖示，即可試用。 定型完成後，選取 [ **測試**]。 嘗試一些互動：

- 輸入： *將溫度設定為72度*
- 輸出：確定，將溫度設定為72度
- 輸入： *將溫度設定為45度*
- 輸出：抱歉，我只能設定介於60到80度之間的溫度
- 輸入： *改為將其設為72度*
- 輸出：確定，將溫度設定為72度

## <a name="add-interaction-rules"></a>新增互動規則

互動規則是處理特定或複雜情況的 *額外* 規則。 雖然您可以自由撰寫自己的互動規則，但是在此範例中，您會在下列案例中使用互動規則：

* 確認命令
* 將單步驟更正新增至命令

如需互動規則的詳細資訊，請參閱 [自訂命令概念和定義](./custom-commands-references.md)。

### <a name="add-confirmations-to-a-command"></a>將確認新增至命令

若要新增確認，請使用 `SetTemperature` 命令。 若要完成確認，請使用下列步驟來建立互動規則：

1. 在左側窗格中，選取 [ **>settemperature** ] 命令。
1. 在中間窗格中，選取 [ **新增**] 來新增互動規則。 然後選取 [**互動規則**  >  **確認] 命令**。

    此動作會新增三個互動規則。 這些規則會要求使用者確認警示的日期和時間。 它們預期會有確認 (是，或在下一個回合中沒有) 。

    1. 使用下列設定修改 **Confirm 命令** 互動規則：
        1. 變更名稱以 **確認溫度**。
        1. 新增條件：**必要參數**  >  **溫度**。
        1. 新增動作：**輸入**  >  **傳送語音回應**  >  **您確定要將溫度設定為 {溫度} 度嗎？**
        1. 在 [ **預期** ] 區段中，保留 [ **使用者需要確認**] 的預設值。
      
         > [!div class="mx-imgBorder"]
         > ![顯示如何建立必要參數回應的螢幕擷取畫面。](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 修改 **確認** 成功的互動規則，以處理成功確認 (使用者說的是) 。
      
          1. 將名稱變更為 **確認溫度成功**。
          1. 讓現有的 **確認** 條件保持成功。
          1. 新增條件：**輸入**  >  **必要參數**  >  **溫度**。
          1. 將預設的 **後續執行狀態** 值保留為 **執行完成規則**。

    1. 修改確認遭到拒絕時， (使用者未) 的情況下，修改 **確認拒絕** 的互動規則來處理案例。

          1. 將名稱變更為 **確認溫度拒絕**。
          1. 保留現有 **確認已拒絕** 的情況。
          1. 新增條件：**輸入**  >  **必要參數**  >  **溫度**。
          1. 新增動作：**輸入**  >  **傳送語音回應**  >  **無問題。接下來的溫度為何？**
          1. 變更預設的 **後續執行狀態** 值以 **等候使用者的輸入**。

> [!IMPORTANT]
> 在本文中，您會使用內建的確認功能。 您也可以手動逐一新增互動規則。
   
選取 [ **定型**] 來嘗試變更。 定型完成時，選取 [ **測試**]。

- **輸入**： *將溫度設定為80度*
- **輸出**：您確定要將溫度設定為80度嗎？
- **輸入**： *否*
- **輸出**：沒問題。 接下來的溫度為何？
- **輸入**： *72 度*
- **輸出**：您確定要將溫度設定為72度嗎？
- **輸入**： *是*
- **輸出**：確定，將溫度設定為72度

### <a name="implement-corrections-in-a-command"></a>在命令中執行更正

在本節中，您將設定一個步驟修正。 執行履行動作之後，會使用這項更正。 您也會看到一個範例，說明如何在命令尚未完成時，根據預設啟用更正。 若要在命令未完成時新增更正，請新增參數 `AlarmTone` 。

在左窗格中，選取 [ **SetAlarm** ] 命令。 然後加入新的參數 **AlarmTone**。
        
- **名字** > `AlarmTone`
- **類型**  > **字串**
- **預設值**  > **Chimes**
- 設定  > **接受來自內部目錄的預先定義輸入值**
- **預先定義的輸入值**  > **Chimes**、**叮噹** 和 **Echo** (這些值是個別預先定義的輸入。 ) 


接下來，將 **DateTime** 參數的回應更新為 **Ready，以將警報器設定為 {AlarmTone}。什麼時候？**。 然後修改完成規則，如下所示：

1. 選取現有的完成規則 **ConfirmationResponse**。
1. 在右側窗格中，將滑鼠停留在現有的動作上方，然後選取 [ **編輯**]。
1. 將語音回應更新為 `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` 。

> [!IMPORTANT]
> 警示語氣可能會變更，而不會在進行中的命令進行任何明確的設定。 例如，它可能會在命令尚未完成時變更。 所有命令參數的更正 *預設* 會啟用，而不論是否要滿足此命令，所有命令參數都是如此。

#### <a name="implement-a-correction-when-a-command-is-finished"></a>在命令完成時執行更正

即使命令已完成，自訂命令平臺仍允許進行單步驟更正。 預設不會啟用此功能。 必須明確設定它。 

使用下列步驟來設定單一步驟的修正：

1. 在 **SetAlarm** 命令中，新增 type **update previous 命令** 的互動規則，以更新先前設定的警示。 將互動規則重新命名為 **更新先前** 的警示。
1. 保留預設條件： **需要更新上一個命令**。
1. 新增條件：**輸入**  >  **必要的參數**  >  **日期時間**。
1. 新增動作：**輸入**  >  **傳送語音回應**  >  **簡單編輯器** 將先前的警示  >  **時間更新為 {DateTime}**。
1. 將預設的 **後續執行狀態** 值保留為 **命令完成**。

選取 [ **定型**] 來嘗試變更。 等候定型完成，然後選取 [ **測試**]。

- **輸入**： *設定警示。*
- **輸出**：準備好使用 Chimes 來設定警報器。 這是什麼時候？
- **輸入**： *將鬧鐘設定為叮噹在明天上午9點的語氣。*
- **輸出**： [確定]，為 2020-05-21 09:00:00 設定鬧鐘。 警示語氣是叮噹。
- **輸入**： *否，上午8點。*
- **輸出**：將先前的警示時間更新為 2020-05-29 08:00。

> [!NOTE]
> 在實際的應用程式中，在此更正規則的 [ **動作** ] 區段中，您也必須將活動傳回給用戶端，或呼叫 HTTP 端點來更新您系統中的警示時間。 此動作應該單獨負責更新警示時間。 它不應該負責命令的任何其他屬性。 在此情況下，該屬性將會是鬧鐘語氣。

## <a name="add-language-generation-templates-for-speech-responses"></a>新增適用于語音回應的語言產生範本

語言產生 (LG) 範本可讓您自訂傳送給用戶端的回應。 它們會在回應中引進變異數。 您可以使用下列方法來達成語言產生：

* 語言產生範本。
* 自我調整運算式。

自訂命令範本是以 Bot Framework 的 [LG 範本](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)為基礎。 由於自訂命令功能會在必要時建立新的 LG 範本 () 參數或動作中的語音回應，因此您不需要指定 LG 範本的名稱。 

因此，您不需要定義您的範本，如下所示：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

相反地，您可以定義沒有名稱的範本主體，如下所示：

> [!div class="mx-imgBorder"]
> ![顯示範本編輯器範例的螢幕擷取畫面。](./media/custom-commands/template-editor-example.png)


這種變更會在傳送給用戶端的語音回應中引進變化。 若為語句，則會從提供的選項中隨機挑選對應的語音回應。

利用 LG 範本，您也可以使用適應性運算式來定義命令的複雜語音回應。 如需詳細資訊，請參閱 [LG 範本格式](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。 

根據預設，自訂命令功能支援所有功能，但有下列微小差異：

* 在 LG 範本中，實體會表示為 `${entityName}` 。 自訂命令功能不會使用實體。 但是，您可以使用參數做為具有 `${parameterName}` 標記法或 `{parameterName}` 標記法的變數。
* 自訂命令功能不支援範本撰寫和展開，因為您永遠不會直接編輯 *lg* 檔案。 您只會編輯自動建立之範本的回應。
* 自訂命令功能不支援 LG 插入的自訂函數。 支援預先定義的函式。
* 自訂命令功能不支援選項，例如 `strict` 、 `replaceNull` 和 `lineBreakStyle` 。

### <a name="add-template-responses-to-a-turnonoff-command"></a>將範本回應新增至 TurnOnOff 命令

修改 `TurnOnOff` 命令以新增參數。 使用下列設定。

| 設定            | 建議的值       | 
| ------------------ | --------------------- | 
| **名稱**               | `SubjectContext`         | 
| **為全域**          | 未選取             | 
| **必要**           | 未選取               | 
| **型別**               | **String**                |
| **預設值**      | `all` |
| **設定**      | **接受來自內部目錄的預先定義輸入值** | 
| **預先定義的輸入值** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>修改完成規則

編輯現有完成規則 **ConfirmationResponse** 的 [**動作**] 區段。 在 [ **編輯動作** ] 視窗中，切換至 [ **範本編輯器**]。 然後以下列範例取代文字。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

使用下列輸入和輸出來定型及測試您的應用程式。 請注意回應的變化。 變化是由範本值的多個替代專案所建立，也可以使用自我調整運算式來建立。

* 輸入： *開啟電視*
* 輸出：確定，開啟電視
* 輸入： *開啟電視*
* 輸出：已完成，已開啟電視
* 輸入：關閉 *燈光*
* 輸出：確定，關閉所有燈光
* 輸入： *關閉房間燈*
* 輸出：確定，關閉房間燈

## <a name="use-a-custom-voice"></a>使用自訂語音

自訂自訂命令回應的另一種方式是選取輸出語音。 使用下列步驟將預設語音切換為自訂語音：

1. 在自訂命令應用程式的左側窗格中，選取 [ **設定**]。
1. 在中間窗格中，選取 [ **自訂語音**]。
1. 在資料表中，選取自訂語音或公共語音。
1. 選取 [儲存]。

> [!div class="mx-imgBorder"]
> ![顯示範例句子和參數的螢幕擷取畫面。](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> 針對公用語音，類神經類型只適用于特定區域。 如需詳細資訊，請參閱 [語音服務支援的區域](./regions.md#standard-and-neural-voices)。
>
> 您可以在 [ **自訂語音** 專案] 頁面上建立自訂語音。 如需詳細資訊，請參閱 [開始使用自訂語音](./how-to-custom-voice.md)。

現在應用程式將會以選取的語音回應，而不是預設的語音。

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用語音 SDK 將 [自訂命令應用程式](how-to-custom-commands-setup-speech-sdk.md) 與用戶端應用程式整合。
* 使用 Azure DevOps 為您的自訂命令應用程式[設定連續部署](how-to-custom-commands-deploy-cicd.md)。 
                      
