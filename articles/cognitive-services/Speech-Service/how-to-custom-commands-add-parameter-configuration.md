---
title: 如何：將參數設定為外部實體實體
titleSuffix: Azure Cognitive Services
description: 在本文中，我們會說明如何設定字串參數，以參考在 web 端點上公開的目錄。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284178"
---
# <a name="add-configurations-to-commands-parameters"></a>將設定新增至命令參數

在本文中，您將深入瞭解 advanced 參數設定，包括：

 - 參數值如何屬於自訂命令應用程式外部定義的集合
 - 如何在參數值上新增驗證子句

## <a name="prerequisites"></a>先決條件

您必須已完成下列文章中的步驟：

> [!div class="checklist"]
> * [如何：使用簡單的命令建立應用程式](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：將參數新增至命令](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>將參數設定為外部目錄實體

在本節中，您會設定字串類型參數，以參考裝載于 web 端點上的外部目錄。 這可讓您獨立更新外部目錄，而不需要對自訂命令應用程式進行編輯。 當類別目錄專案的數目很大時，這個方法就很有用。

從**TurnOnOff**命令重複使用**SubjectDevice**參數。 此參數的目前設定是**接受來自內部目錄的預先定義輸入**。 這是指參數設定中所定義的靜態裝置清單。 我們想要將此內容移出至可獨立更新的外部資料源。

若要這麼做，請從加入新的 web 端點開始。 移至左窗格中的 [ **Web 端點**] 區段，並使用下列設定新增 Web 端點。

| 設定 | 建議的值 |
|----|----|
| Name | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| 方法 | GET |


如果 URL 的建議值無法供您使用，您必須設定並裝載簡單的 web 端點，以傳回由可控制的裝置清單所組成的 json。 Web 端點應傳回 json 格式，如下所示：
    
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


接著，移至 [ **SubjectDevice**參數設定] 頁面，並將屬性變更為下列內容。

| 設定 | 建議的值 |
| ----| ---- |
| 設定 | 接受來自外部目錄的預先定義輸入 |                               
| 目錄端點 | getDevices |
| 方法 | GET |

然後選取 [儲存]。

> [!IMPORTANT]
> 除非您已在左窗格的 [ **web 端點**] 區段中設定 web 端點，否則您不會看到設定參數以接受來自外部類別目錄之輸入的選項。

### <a name="try-it-out"></a>試做

選取 [**定型**]，並等待定型完成。 定型完成之後，請選取 [**測試**]，然後嘗試一些互動。

* 輸入：開啟
* 輸出：您要控制哪一個裝置？
* 輸入：燈
* 輸出： [確定]，開啟燈

> [!NOTE]
> 請注意，您可以立即控制裝載于 web 端點上的所有裝置。 您仍然需要訓練應用程式來測試新的變更，然後重新發佈應用程式。

## <a name="add-validation-to-parameters"></a>將驗證新增至參數

**驗證**是適用于特定參數類型的結構，可讓您設定參數值的條件約束，並在值不落在條件約束中時提示更正。 如需擴充驗證結構之參數類型的完整清單，請移至[參考](./custom-commands-references.md)

使用**SetTemperature**命令測試驗證。 使用下列步驟來新增**溫度**參數的驗證。

1. 在左窗格中選取 [ **SetTemperature**命令]。
1. 選取中間窗格中的 [**溫度**]。
1. 選取右窗格中的 [**新增驗證**]。
1. 在 [**新增驗證**] 視窗中，如下所示設定驗證，然後選取 [**建立**]。


    | 參數設定 | 建議的值 | 描述 |
    | ---- | ---- | ---- |
    | 最小值 | `60` | 對於 Number 參數，這個參數可以採用的最小值 |
    | 最大值 | `80` | 對於 Number 參數，這個參數可以採用的最大值 |
    | 失敗回應 |  簡單編輯器 > 第一種變化 >`Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 如果驗證失敗，提示要求輸入新值 |

    > [!div class="mx-imgBorder"]
    > ![新增範圍驗證](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>試做

1. 選取右窗格頂端的 [**定型**] 圖示。

1. 訓練完成後，請選取 [**測試**]，然後嘗試幾個互動：

    - 輸入：將溫度設定為72度
    - 輸出：正常，將溫度設定為72度
    - 輸入：將溫度設定為45度
    - 輸出：抱歉，我只能設定60到80度之間的溫度
    - 輸入：請改為72度
    - 輸出：正常，將溫度設定為72度

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：新增互動規則](./how-to-custom-commands-add-interaction-rules.md)
