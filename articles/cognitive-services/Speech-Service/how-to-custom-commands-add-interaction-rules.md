---
title: 如何：在自訂命令中新增確認
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何在自訂命令中執行命令的確認。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308034"
---
# <a name="add-interaction-rules"></a>新增互動規則

在本文中，您將瞭解**互動規則**。 這些是額外的規則，可處理更具體或複雜的情況。 雖然您可以免費撰寫自己的自訂互動規則，但在本文中，您會針對下列目標案例使用互動規則：

* 確認命令
* 將一個步驟更正新增至命令

若要深入瞭解互動規則，請移至[參考](./custom-commands-references.md)一節。

## <a name="prerequisites"></a>必要條件

您必須已完成下列文章中的步驟：
> [!div class="checklist"]
> * [如何：使用簡單的命令建立應用程式](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：將參數新增至命令](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>將確認新增至命令

若要新增確認，請使用**SetTemperature**命令。 若要達到確認，請使用下列步驟建立互動規則。

1. 從左窗格中選取 [ **SetTemperature**命令]。
2. 選取中間窗格中的 [**新增**]，然後選取 [**互動規則**] [  >  **確認命令**]，以新增互動規則。

    這會新增3個互動規則，此規則會要求使用者確認警示的日期和時間，並預期會有確認（是/否）以進行下一回合。

    1. 依據下列設定，修改 [**確認命令**互動] 規則
        1. 將**名稱**重新命名為 **`Confirm Temperature`** 。
        1. 將新條件新增為**必要參數 > 溫度**
        1. 新增新動作**類型 > 傳送語音回應 > `Are you sure you want to set the temperature as {Temperature} degrees?` **
        1. 將 [預期] 區段中的預設值保留為 [**需要使用者確認**]。
      
         > [!div class="mx-imgBorder"]
         > ![建立必要的參數回應](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 修改**確認成功**的互動規則，以處理成功的確認（使用者已說是）。
      
          1. 將**名稱**修改為 **`Confirmation temperature succeeded`** 。
          1. 保留已存在的**確認為成功**狀況。
          1. 將新條件新增為**類型 > 必要參數 > 溫度**
          1. 將 [**後續執行狀態**] 的預設值保留為 [**執行完成規則**]。

    1. 修改 [**拒絕確認**] （使用者已說為 [否]），以在確認遭到拒絕時處理案例。

          1. 將**名稱**修改為 **`Confirmation temperature denied`** 。
          1. 保留已存在的 [**確認已拒絕**] 條件。
          1. 將新條件新增為**類型 > 必要參數 > 溫度**
          1. 新增新動作**類型 > 傳送語音回應 > `No problem. What temperature then?` **
          1. 將 [**執行後狀態**] 的預設值保留為 [**等候使用者輸入**]。

> [!IMPORTANT]
> 在本文中，您已使用內建的確認功能。 或者，您也可以手動逐一新增互動規則，以達到相同的效果。
   

### <a name="try-out-the-changes"></a>試用變更

選取 [**定型**]，等待訓練完成，然後選取 [**測試**]。

- 輸入：將溫度設定為80度
- 輸出：確定80？
- 輸入：否
- 輸出：沒有問題。 接下來的溫度為何？
- 輸入：83度
- 輸出：確定83？
- 輸入：是
- 輸出：正常，將溫度設定為83度


## <a name="implementing-corrections-in-a-command"></a>在命令中執行更正

在本節中，您會設定一個步驟更正，這會在完成執行動作之後使用。 您也會看到一個範例，說明如何在命令尚未完成的情況下，預設啟用更正。 若要在命令未完成時加入更正，請新增參數**AlarmTone**。

從左窗格中選取 [ **SetAlarm**命令]，然後加入新的參數**AlarmTone**。
        
- **檔案名** > `AlarmTone`
- **輸入**> 字串
- **預設值** > `Chimes`
- **Configuration** > 接受來自內部目錄的預先定義輸入值
- **預先定義的輸入值**  >  `Chimes` 、 `Jingle` 和 `Echo` 。 每個都是個別預先定義的輸入。


接下來，將 DateTime 參數的回應更新為 `Ready to set alarm with tone as {AlarmTone}. For what time?` 。 然後修改完成規則，如下所示。

1. 選取現有的完成規則**ConfirmationResponse**。
1. 在右窗格中，將滑鼠停留在現有的動作上，然後選取 [**編輯**] 按鈕。
1. 將語音回應更新為`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>試用變更

選取 [**定型**]，等待訓練完成，然後選取 [**測試**]。
試用下列語句：

- 輸入：設定鬧鐘
- 輸出：準備好將警報器設定為 Chimes。 有什麼時間？
- 輸入：將具有音調的鬧鐘設定為明天9點的叮噹
- 輸出：正常，已為 2020-05-30 09:00:00 設定警示。 鬧鐘音調為叮噹。

> [!IMPORTANT]
> 請注意，不需要在進行中的命令中進行任何明確設定，亦即，當命令尚未完成時，如何變更鬧鐘音調。 **根據預設，所有命令參數都會啟用更正，而不論是否要滿足此命令，都可以使用 [回合編號]。**

### <a name="correction-when-command-is-completed"></a>完成命令時的更正

自訂命令平臺也會提供單一步驟修正的功能，即使命令已經完成也一樣。 但這項功能預設不會啟用，而且必須明確設定。 使用下列步驟來設定單步驟更正。

1. 在**SetAlarm**命令中，新增類型為**update previous 命令**的互動規則，以更新先前設定的鬧鐘。 重新命名互動規則的預設**名稱**，以**更新先前**的警示。
1. 保留預設條件 [**先前的命令需要更新**]。
1.  將新條件新增為**類型 > 必要參數 > DateTime**。
1. 新增新動作做為**類型 > 傳送語音回應 > 簡單編輯器 > `Updating previous alarm time to {DateTime}.` **
1. 將 [post 執行狀態] 的預設值保留為 [**已完成] 命令**。

### <a name="try-out-the-changes"></a>試用變更

選取 [**定型**]，等待訓練完成，然後選取 [**測試**]。

- 輸入：設定鬧鐘
- 輸出：準備好將警報器設定為 Chimes。 什麼時候？
- 輸入：將具有音調的鬧鐘設定為明天9點的叮噹
- 輸出：正常，已為 2020-05-21 09:00:00 設定警示。 鬧鐘音調為叮噹。
- 輸入：否，上午8點
- 輸出：將先前的鬧鐘時間更新為 2020-05-29 08:00。

> [!NOTE]
> 在實際的應用程式中，在此更正規則的 [動作] 區段中，您也必須將活動傳送回用戶端或呼叫 HTTP 端點，以更新您系統中的警示時間。 此動作應該 singularly 負責僅更新警示時間，而不是命令的任何其他屬性（在此情況下為鬧鐘）。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：新增語音回應的語言產生範本](./how-to-custom-commands-add-language-generation-templates.md)