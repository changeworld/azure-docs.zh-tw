---
title: How to：開發自訂命令應用程式-語音服務
titleSuffix: Azure Cognitive Services
description: 在此操作說明中，您將瞭解如何開發和自訂命令應用程式。 自訂命令可讓您輕鬆地建立針對語音優先互動體驗優化的豐富語音命令應用程式，且最適合用於工作完成或命令和控制案例，特別是符合物聯網 (IoT) 裝置、環境和無周邊裝置的適當比對。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632636"
---
# <a name="develop-custom-commands-applications"></a>開發自訂命令應用程式

在此操作說明中，您將瞭解如何開發和設定自訂命令應用程式。 自訂命令可讓您輕鬆地建立針對語音優先互動體驗優化的豐富語音命令應用程式，且最適合用於工作完成或命令和控制案例，特別是符合物聯網 (IoT) 裝置、環境和無周邊裝置的適當比對。

在本文中，您將建立可以開啟和關閉電視的應用程式、設定溫度，以及設定鬧鐘。 建立這些基本命令之後，會涵蓋下列自訂命令的選項：

* 將參數新增至命令
* 將設定新增至命令參數
* 建立互動規則
* 建立語音回應的語言產生範本
* 使用自訂語音 

## <a name="create-application-with-simple-commands"></a>使用簡單的命令建立應用程式

首先，從建立空白的自訂命令應用程式開始。 如需詳細資訊，請參閱 [快速入門](quickstart-custom-commands-application.md)。 這次，您會建立空白專案，而不是匯入專案。

1. 在 [ **名稱** ] 方塊中，輸入 [專案名稱] 做為 `Smart-Room-Lite` (，或輸入您選擇的其他內容) 。
1. 在 [ **語言** ] 清單中，選取 [ **英文 (美國)**。
1. 選取或建立您所選擇的 LUIS 資源。

   > [!div class="mx-imgBorder"]
   > ![建立專案](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a> (選用) 更新 LUIS 資源

您可以更新您在 [ **新增專案** ] 視窗中選取的撰寫資源，並設定預測資源。 當您的自訂命令應用程式發行時，會使用預測資源來進行辨識。 在開發和測試階段，您不需要預測資源。

### <a name="add-turnon-command"></a>Add Homeautomation.turnon 命令

在您剛才建立的空的 **智慧型空間 Lite** 自訂命令應用程式中，新增一個簡單的命令來處理語句、 `turn on the tv` ，並以訊息回應 `Ok, turning the tv on` 。

1. 選取左窗格頂端的 [ **新增命令** ]，以建立新的命令。 **新的命令** 視窗隨即開啟。
1. 將 [ **名稱** ] 欄位的值提供為 **homeautomation.turnon**。
1. 選取 [建立]。

中間窗格會列出命令的不同屬性。 您可以設定命令的下列屬性。 如需命令的所有設定屬性的說明，請移至 [ [參考](./custom-commands-references.md)]。

| 組態            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **句子範例** | 範例語句使用者可以說觸發此命令                                                                 |
| **參數**       | 完成命令所需的資訊                                                                                |
| **完成規則** | 要執行以完成命令的動作。 例如，回應使用者或與另一個 web 服務進行通訊。 |
| **互動規則**   | 處理更具體或複雜情況的其他規則                                                              |


> [!div class="mx-imgBorder"]
> ![建立命令](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>新增範例句子

讓我們從 **範例句子** 區段開始，並提供使用者可以說出的範例。

1. 在中間窗格中選取 **範例句子** 區段。
1. 在最右邊的窗格中，新增範例：

    ```
    turn on the tv
    ```

1.  選取窗格頂端的 [ **儲存** ]。

我們現在沒有參數，所以我們可以移至 **完成規則** 區段。

#### <a name="add-a-completion-rule"></a>新增完成規則

接下來，命令必須有完成規則。 此規則會告訴使用者正在執行履行動作。 若要閱讀有關規則和完成規則的詳細資訊，請移至 [參考](./custom-commands-references.md)。

1. 選取 [ **完成** 預設完成規則]，然後進行編輯，如下所示：

    
    | 設定    | 建議的值                          | 描述                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **名稱**       | ConfirmationResponse                  | 描述規則用途的名稱          |
    | **條件** | 無                                     | 判斷規則何時可以執行的條件    |
    | **動作**    | 將語音回應 > 簡單的編輯器 > 首次變化 > `Ok, turning the tv on` | 當規則條件為真時所要採取的動作 |

   > [!div class="mx-imgBorder"]
   > ![建立語音回應](media/custom-commands/create-speech-response-action.png)

1. 選取 [ **儲存** ] 以儲存動作。
1. 回到 [ **完成規則** ] 區段中，選取 [ **儲存** ] 以儲存所有變更。 

    > [!NOTE]
    > 不需要使用命令隨附的預設完成規則。 如有需要，您可以刪除現有的預設完成規則，並新增您自己的規則。

### <a name="add-settemperature-command"></a>Add >settemperature 命令

現在，再新增一個將採用單一語句的命令 **>settemperature** ， `set the temperature to 40 degrees` 然後以訊息回應 `Ok, setting temperature to 40 degrees` 。

依照 **homeautomation.turnon** 命令所說明的步驟，使用範例句子「**將溫度設為40度**」來建立新的命令。

然後， **編輯現有的完成規則** ，如下所示：

| 設定    | 建議的值                          |
| ---------- | ---------------------------------------- |
| Name  | ConfirmationResponse                  |
| 條件 | 無                                     |
| 動作    | 將語音回應 > 簡單的編輯器 > 首次變化 > `Ok, setting temperature to 40 degrees` |

選取 [ **儲存** ] 以儲存命令的所有變更。

### <a name="add-setalarm-command"></a>Add SetAlarm 命令

使用範例句子建立新的命令 **SetAlarm** ，「**為明天的9點設定鬧鐘**」。 然後， **編輯現有的完成規則** ，如下所示：

| 設定    | 建議的值                          |
| ---------- | ---------------------------------------- |
| 規則名稱  | ConfirmationResponse                  |
| 條件 | 無                                     |
| 動作    | 將語音回應 > 簡單的編輯器 > 首次變化 >`Ok, setting an alarm for 9 am tomorrow` |

選取 [ **儲存** ] 以儲存命令的所有變更。

### <a name="try-it-out"></a>試做

使用測試聊天面板來測試行為。 選取右窗格頂端的 [ **定型** ] 圖示。 定型完成後，選取 [ **測試**]。 透過語音或文字試用下列語句範例：

- 輸入：將溫度設定為40度
- 預期的回應：確定，將溫度設定為40度
- 輸入：開啟電視
- 預期的回應：確定，開啟電視
- 您輸入：將鬧鐘設定為明天9點
- 預期的回應：確定，在明天設定9的鬧鐘

> [!div class="mx-imgBorder"]
> ![使用網路聊天進行測試](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> 在 [測試] 面板中，您可以選取 [ **轉換詳細** 資訊]，以瞭解如何處理此語音/文字輸入。

## <a name="add-parameters-to-commands"></a>將參數新增至命令

在本節中，您會瞭解如何將參數新增至命令。 參數是命令完成工作所需的資訊。 在複雜的情況下，參數也可以用來定義觸發自訂動作的條件。

### <a name="configure-parameters-for-turnon-command"></a>設定 Homeautomation.turnon 命令的參數

從編輯現有的 **homeautomation.turnon** 命令開始，以開啟和關閉多個裝置。

1. 現在命令現在會處理 on 和 off 案例，請將命令重新命名為 **TurnOnOff**。
   1. 在左窗格中，選取 [ **homeautomation.turnon** ] 命令，然後選取窗格頂端的 [ **新命令** ] 旁的省略號 ( ... ) 按鈕。
   
   1. 選取 [ **重新命名**]。 在 [ **重新命名命令** ] 視窗中，將 [ **名稱** ] 變更為 **TurnOnOff**。

1. 接下來，您要在這個命令中加入新的參數，以代表使用者要開啟或關閉裝置。
   1. 選取中間窗格頂端的 [  **新增** ]。 從下拉式清單中選取 [ **參數**]。
   1. 在右窗格的 [ **參數** ] 區段中，將 [ **名稱** ] 方塊中的值新增為 **OnOff**。
   1. 選取 [ **必要**]。 在 [ **加入必要參數的回應** ] 視窗中，選取 [ **簡單編輯器**]。 在 **第一種變化** 中，新增
        ```
        On or Off?
        ```
   1. 選取 [更新]。

       > [!div class="mx-imgBorder"]
       > ![建立必要的參數回應](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 現在我們要設定參數屬性。 如需命令的所有設定屬性的說明，請移至 [ [參考](./custom-commands-references.md)]。 設定參數的屬性，如下所示：
      

       | 組態      | 建議的值     | 描述                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name               | `OnOff`           | 參數的描述性名稱                                                                           |
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
 
#### <a name="add-subjectdevice-parameter"></a>新增 SubjectDevice 參數 

1. 接下來，選取 [ **新增** ] 以新增第二個參數，以代表可使用此命令控制的裝置名稱。 使用下列設定。


    | 設定            | 建議的值       |
    | ------------------ | --------------------- |
    | Name               | `SubjectDevice`         |
    | 為全域          | unchecked             |
    | 必要           | checked               |
    | 必要參數的回應     | 簡單編輯器 > `Which device do you want to control?`    | 
    | 類型               | String                |          |
    | 組態      | 接受來自內部目錄的預先定義輸入值 | 
    | 預先定義的輸入值 | `tv`, `fan`               |
    | 別名 (`tv`)       | `television`, `telly`     |

1. 選取 [儲存]。

#### <a name="modify-example-sentences"></a>修改範例句子

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

選取 [儲存]。

> [!TIP]
> 在範例句子編輯器中，請使用大括弧來參考您的參數。 - `turn {OnOff} the {SubjectDevice}` 使用 tab 鍵自動完成（由先前建立的參數所支援）。

#### <a name="modify-completion-rules-to-include-parameters"></a>修改完成規則以包含參數

修改現有的完成規則 **ConfirmationResponse**。

1. 在 [ **條件** ] 區段中，選取 [ **新增條件**]。
1. 在 [ **新增條件** ] 視窗的 [ **類型** ] 清單中，選取 [ **必要參數**]。 在下列檢查清單中，檢查 **OnOff** 和 **SubjectDevice**。
1. 將 **IsGlobal** 保留為未核取。
1. 選取 [建立]。
1. 在 [ **動作** ] 區段中，將滑鼠停留在動作上，然後選取 [編輯] 按鈕，以編輯現有的 **傳送語音回應** 動作。 這次，請使用新建立的 **OnOff** 和 **SubjectDevice** 參數

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 選取 [儲存]。

選取右窗格頂端的 [ **定型** ] 圖示，以嘗試變更。 定型完成時，選取 [ **測試**]。 **應用程式** 視窗的測試隨即出現。 請嘗試下列互動。

- 輸入：關閉電視
- 輸出：確定，關閉電視
- 輸入：關閉電視
- 輸出：確定，關閉電視
- 輸入：關閉
- 輸出：您要控制的裝置為何？
- 輸入：電視
- 輸出：確定，關閉電視

### <a name="configure-parameters-for-settemperature-command"></a>設定 >settemperature 命令的參數

修改 **>settemperature** 命令，讓它能夠設定使用者所導向的溫度。

使用下列設定來新增參數 **溫度**

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

### <a name="configure-parameters-to-the-setalarm-command"></a>設定 SetAlarm 命令的參數

使用下列設定來新增名為 **DateTime** 的參數。

   | 設定                           | 建議的值                     | 
   | --------------------------------- | ----------------------------------------|
   | 名稱                              | `DateTime`                               |
   | 必要                          | checked                                 |
   | 必要參數的回應   | 簡單編輯器 > `For what time?`            | 
   | 類型                              | DateTime                                |
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

使用與不同命令相關的語句，同時測試三個命令。 請注意，您可以在不同的命令之間切換。

- 輸入：設定警示
- 輸出：時間為何？
- 輸入：開啟電視
- 輸出：確定，開啟電視
- 輸入：設定警示
- 輸出：時間為何？
- 輸入：下午5點
- 輸出：確定，已為 2020-05-01 17:00:00 設定鬧鐘

## <a name="add-configurations-to-commands-parameters"></a>將設定新增至命令參數

在本節中，您會深入瞭解 advanced 參數設定，包括：

 - 參數值如何屬於定義于自訂命令應用程式的外部集合
 - 如何在參數值上新增驗證子句

### <a name="configure-parameter-as-external-catalog-entity"></a>將參數設定為外部目錄實體

自訂命令可讓您設定字串類型參數，以參考裝載于 web 端點的外部目錄。 這可讓您獨立更新外部類別目錄，而不需要對自訂命令應用程式進行編輯。 如果類別目錄專案的數量很大，這種方法就很有用。

從 **TurnOnOff** 命令重複使用 **SubjectDevice** 參數。 此參數目前的設定會 **接受來自內部目錄的預先定義輸入**。 這指的是參數設定中定義的靜態裝置清單。 我們想要將此內容移至可獨立更新的外部資料源。

若要這樣做，請先加入新的 web 端點。 移至左窗格中的 [ **web 端點** ] 區段，並使用下列設定來新增新的 Web 端點。

| 設定 | 建議的值 |
|----|----|
| Name | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| 方法 | GET |


如果 URL 的建議值不適用於您，您需要設定和裝載簡單的 web 端點，以傳回 json，其中包含可控制的裝置清單。 Web 端點應傳回 json 格式，如下所示：
    
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

接下來，移至 [ **SubjectDevice** 參數設定] 頁面，並將屬性變更為下列內容。

| 設定 | 建議的值 |
| ----| ---- |
| 組態 | 接受來自外部目錄的預先定義輸入 |                               
| 目錄端點 | getDevices |
| 方法 | GET |

然後選取 [儲存]。

> [!IMPORTANT]
> 除非您在左窗格的 [ **web 端點** ] 區段中設定 web 端點，否則您不會看到設定參數以接受來自外部目錄之輸入的選項。

選取 [ **定型** 並等候定型完成] 來試用。 定型完成後，請選取 [ **測試** ] 並嘗試幾個互動。

* 輸入：開啟
* 輸出：您要控制的裝置為何？
* 輸入：燈
* 輸出：確定，開啟燈

> [!NOTE]
> 請注意，您現在可以控制所有裝載于 web 端點上的裝置。 您仍然需要訓練應用程式來測試新的變更，並重新發佈應用程式。

### <a name="add-validation-to-parameters"></a>將驗證新增至參數

**驗證** 是適用于特定參數類型的結構，可讓您設定參數值的條件約束，並在不屬於條件約束的值時提示您修正。 如需擴充驗證結構之參數類型的完整清單，請移至 [參考](./custom-commands-references.md)

使用 **>settemperature** 命令來測試驗證。 使用下列步驟來新增 **溫度** 參數的驗證。

1. 選取左窗格中的 [ **>settemperature** ] 命令。
1. 在中間窗格中選取 [  **溫度** ]。
1. 選取右窗格中的 [ **加入驗證** ]。
1. 在 **新的驗證** 視窗中，依照下列方式設定驗證，然後選取 [ **建立**]。


    | 參數設定 | 建議的值 | 描述 |
    | ---- | ---- | ---- |
    | 最小值 | `60` | 若為數字參數，此參數可以採用的最小值 |
    | 最大值 | `80` | 若為數字參數，此參數可以採用的最大值 |
    | 失敗回應 |  簡易編輯器 > 第一個變化 > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 如果驗證失敗，則提示要求新值 |

    > [!div class="mx-imgBorder"]
    > ![新增範圍驗證](media/custom-commands/add-validations-temperature.png)

選取右窗格頂端的 [ **定型** ] 圖示，即可試用。 定型完成後，請選取 [ **測試** ] 並嘗試幾個互動：

- 輸入：將溫度設定為72度
- 輸出：確定，將溫度設定為72度
- 輸入：將溫度設定為45度
- 輸出：抱歉，我只能設定介於60到80度之間的溫度
- 輸入：改為將其設為72度
- 輸出：確定，將溫度設定為72度

## <a name="add-interaction-rules"></a>新增互動規則

互動規則是處理特定或複雜情況的 *額外規則* 。 雖然您可以自行撰寫自訂互動規則，但是在此範例中，您會在下列目標案例中使用互動規則：

* 確認命令
* 將單步驟更正新增至命令

若要深入瞭解互動規則，請移至 [ [參考](./custom-commands-references.md) ] 區段。

### <a name="add-confirmations-to-a-command"></a>將確認新增至命令

若要新增確認，請使用 **>settemperature** 命令。 若要確認，您可以使用下列步驟來建立互動規則。

1. 選取左窗格中的 [ **>settemperature** ] 命令。
1. 在中間窗格中選取 [ **加入** ]，以新增互動規則。 然後選取 [**互動規則**  >  **確認] 命令**。

    此動作會新增三個互動規則，以要求使用者確認警示的日期和時間，並預期下一回合 (是/否) 的確認。

    1. 依據下列設定修改 **Confirm 命令** 互動規則：
        1. 重新命名 **名稱** 以 **確認溫度**。
        1. 新增條件作為必要的 **參數**  >  **溫度**。
        1. 將新的動作新增為 **類型**  >  **傳送語音回應**  >  **您確定要將溫度設定為 {溫度} 度嗎？**
        1. 保留 [**預期**] 區段中的 [**使用者必須確認**] 的預設值。
      
         > [!div class="mx-imgBorder"]
         > ![建立必要的參數回應](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 修改 **確認** 成功的互動規則，以處理成功確認 (使用者說的是) 。
      
          1. 修改 **名稱** 以 **確認溫度成功**。
          1. 保留已存在的 **確認成功** 條件。
          1. 新增條件為 **類型**  >  **所需的參數**  >  **溫度**。
          1. 將 **執行後狀態** 的預設值保留為 **執行完成規則**。

    1. 修改確認被拒時所發生的 **確認拒絕** 互動規則， (使用者未說) 。

          1. 修改 **名稱** 以 **確認溫度被拒**。
          1. 保留已存在的 **確認已拒絕** 的情況。
          1. 新增條件為 **類型**  >  **所需的參數**  >  **溫度**。
          1. 將新的動作新增為 **類型**  >  **傳送語音回應**  >  **無問題。接下來的溫度為何？**
          1. 將 **執行後狀態** 的預設值保留為 **等待使用者輸入**。

> [!IMPORTANT]
> 在本文中，您已使用內建的確認功能。 您也可以手動逐一新增互動規則。
   
選取 [ **定型**]、等候定型完成，然後選取 [ **測試**]，以嘗試變更。

- **輸入**：將溫度設定為80度
- **輸出**：您確定要將溫度設定為80度嗎？
- **輸入**：否
- **輸出**：沒問題。 接下來的溫度為何？
- **輸入**：72度
- **輸出**：您確定要將溫度設定為72度嗎？
- **輸入**：是
- **輸出**：確定，將溫度設定為83度

### <a name="implement-corrections-in-a-command"></a>在命令中執行更正

在本節中，您會設定一個步驟更正，以在執行履行動作之後使用。 您也會看到一個範例，說明如何在命令尚未完成時，根據預設啟用修正。 若要在命令未完成時新增更正，請新增參數 **AlarmTone**。

從左窗格中選取 [ **SetAlarm** ] 命令，然後加入新的參數 **AlarmTone**。
        
- **名稱**  > **AlarmTone**
- **類型**  > **字串**
- **預設值**  > **Chimes**
- 設定  > **接受來自內部目錄的預先定義輸入值**
- **預先定義的輸入值**  > **Chimes**、**叮噹** 和 **Echo** 作為個別預先定義的輸入


接下來，將 **DateTime** 參數的回應更新為 **Ready，以將警報器設定為 {AlarmTone}。什麼時候？**。 然後修改完成規則，如下所示：

1. 選取現有的完成規則 **ConfirmationResponse**。
1. 在右窗格中，將滑鼠停留在現有的動作上方，然後選取 [ **編輯**]。
1. 將語音回應更新為 **[確定]，為 {DateTime} 設定鬧鐘。鬧鐘語氣為 {AlarmTone}。**

> [!IMPORTANT]
> 在進行中的命令（例如，當命令尚未完成）時，不需要任何明確設定即可變更警示語氣。 *依預設會針對所有的命令參數啟用更正，不論是否已完成命令，都不會有任何回合號碼。*

#### <a name="correction-when-command-is-completed"></a>命令完成時的更正

即使命令已完成，自訂命令平臺也會提供一步更正的功能。 預設不會啟用此功能。 必須明確設定它。 使用下列步驟來設定單一步驟的修正。

1. 在 **SetAlarm** 命令中，新增 type **update previous 命令** 的互動規則，以更新先前設定的警示。 重新命名互動規則的預設 **名稱** ，以 **更新先前** 的警示。
1. 保留預設條件 [ **上一步] 命令必須更新** 為 [是]。
1. 將新條件加入為 **類型**  >  **必要的參數**  >  **日期時間**。
1. 將新的動作新增為 **類型**  >  **傳送語音回應**  >  **簡單編輯器** 將先前的警示  >  **時間更新為 {DateTime}。**
1. 將 **執行後狀態** 的預設值保留為 **命令完成**。

選取 [ **定型**]、等候定型完成，然後選取 [ **測試**]，以嘗試變更。

- **輸入**：設定警示。
- **輸出**：準備好使用 Chimes 來設定警報器。 這是什麼時候？
- **輸入**：將鬧鐘設定為叮噹在明天上午9點的語氣。
- **輸出**： [確定]，為 2020-05-21 09:00:00 設定鬧鐘。 警示語氣是叮噹。
- **輸入**：否，上午8點。
- **輸出**：將先前的警示時間更新為 2020-05-29 08:00。

> [!NOTE]
> 在實際的應用程式中，在此更正規則的 [ **動作** ] 區段中，您也必須將活動傳回給用戶端，或呼叫 HTTP 端點來更新您系統中的警示時間。 此動作應該單獨負責更新警示時間，而不是命令的任何其他屬性。 在此情況下，這會是警示語氣。

## <a name="add-language-generation-templates-for-speech-responses"></a>新增語音回應的語言產生範本

語言產生範本可讓您自訂傳送給用戶端的回應，並在回應中引進變異數。 您可以透過下列方式來達成語言產生自訂：

* 使用語言產生範本
* 使用自我調整運算式

自訂命令範本是根據 BotFramework 的 [LG 範本](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。 由於自訂命令會在必要時建立新的 LG 範本 (也就是說，針對參數或動作中的語音回應) 您不需要指定 LG 範本的名稱。 因此，請不要將您的範本定義為：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

您只需要定義沒有名稱的範本主體，如下所示。

> [!div class="mx-imgBorder"]
> ![範本編輯器範例](./media/custom-commands/template-editor-example.png)


這種變更會對傳送至用戶端的語音回應帶來變化。 因此，在相同的語句中，對應的語音回應會從提供的選項中隨機挑選。

利用 LG 範本也可讓您使用適應性運算式來定義命令的複雜語音回應。 如需詳細資料，您可以參考 [LG 範本格式](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) 。 自訂命令預設支援所有功能，但有下列差異：

* 在 LG 範本中，實體會以 $ {entityName} 表示。 在自訂命令中，我們不會使用實體，但參數可以用來做為具有其中一個標記法 $ {parameterName} 或 {parameterName} 的變數
* 自訂命令中不支援範本撰寫和展開。 這是因為您永遠不會 `.lg` 直接編輯檔案，只會回應自動建立的範本。
* 自訂命令中不支援 LG 插入的自訂函數。 仍支援預先定義的函數。
* 自訂命令中不支援 (strict，replaceNull & lineBreakStyle) 的選項。

### <a name="add-template-responses-to-turnonoff-command"></a>將範本回應新增至 TurnOnOff 命令

修改 **TurnOnOff** 命令以新增具有下列設定的新參數：

| 設定            | 建議的值       | 
| ------------------ | --------------------- | 
| Name               | `SubjectContext`         | 
| 為全域          | unchecked             | 
| 必要           | unchecked               | 
| 類型               | String                |
| 預設值      | `all` |
| 組態      | 接受來自內部目錄的預先定義輸入值 | 
| 預先定義的輸入值 | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>修改完成規則

編輯現有完成規則 **ConfirmationResponse** 的 [**動作**] 區段。 在 [ **編輯動作** ] 快顯視窗中，切換至 [ **範本編輯器** ]，並將文字取代為下列範例。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

依照下列方式 **定型** 和 **測試** 您的應用程式。 請注意，回應的變化是因為使用了多個範本值的替代專案，而且也使用了引數運算式。

* 輸入：開啟電視
* 輸出：確定，開啟電視
* 輸入：開啟電視
* 輸出：已完成，已開啟電視
* 輸入：關閉燈光
* 輸出：確定，關閉所有燈光
* 輸入：關閉房間燈
* 輸出：確定，關閉房間燈

## <a name="use-custom-voice"></a>使用自訂語音

自訂自訂命令回應的另一種方式是選取自訂輸出語音。 使用下列步驟將預設語音切換為自訂語音。

1. 在您的自訂命令應用程式中，從左窗格中選取 [ **設定** ]。
1. 從中間窗格中選取 [ **自訂語音** ]。
1. 從資料表中選取所需的自訂或公用語音。
1. 選取 [儲存]。

> [!div class="mx-imgBorder"]
> ![具有參數的範例句子](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - 針對 **公用語音**， **類神經類型** 只適用于特定區域。 若要檢查可用性，請參閱 [依區域/端點的標準和類神經語音](./regions.md#standard-and-neural-voices)。
> - 針對 **自訂語音**，您可以從 [自訂語音專案] 頁面建立它們。 請參閱 [自訂語音的開始](./how-to-custom-voice.md)。

現在應用程式將會以選取的語音回應，而不是預設的語音。

## <a name="next-steps"></a>下一步

* 瞭解如何使用語音 SDK 將 [自訂命令應用程式](how-to-custom-commands-setup-speech-sdk.md) 與用戶端應用程式整合。
* 使用 Azure DevOps 為您的自訂命令應用程式[設定持續部署](how-to-custom-commands-deploy-cicd.md)。 
                      