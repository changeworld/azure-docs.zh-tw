---
title: 如何：將參數設定為外部實體實體
titleSuffix: Azure Cognitive Services
description: 在本文中，我們將說明如何設定字串參數，以參考透過 web 端點公開的目錄。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284178"
---
# <a name="add-configurations-to-commands-parameters"></a>將設定新增至命令參數

在本文中，您將深入瞭解 advanced 參數設定，包括：

 - 參數值如何屬於定義于自訂命令應用程式的外部集合
 - 如何在參數值上新增驗證子句

## <a name="prerequisites"></a>Prerequisites

您必須已完成下列文章中的步驟：

> [!div class="checklist"]
> * [How To：使用簡單的命令來建立應用程式](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：將參數加入至命令](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>將參數設定為外部目錄實體

在本節中，您會設定字串類型參數，以參考裝載于 web 端點的外部目錄。 這可讓您獨立更新外部類別目錄，而不需要對自訂命令應用程式進行編輯。 如果類別目錄專案的數量很大，這種方法就很有用。

從**TurnOnOff**命令重複使用**SubjectDevice**參數。 此參數目前的設定會 **接受來自內部目錄的預先定義輸入**。 這指的是參數設定中定義的靜態裝置清單。 我們想要將此內容移至可獨立更新的外部資料源。

若要這樣做，請先加入新的 web 端點。 移至左窗格中的 [ **web 端點** ] 區段，並使用下列設定來新增新的 Web 端點。

| 設定 | 建議的值 |
|----|----|
| 名稱 | `getDevices` |
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

### <a name="try-it-out"></a>試做

選取 [ **定型** ] 並等待定型完成。 定型完成後，請選取 [ **測試** ] 並嘗試幾個互動。

* 輸入：開啟
* 輸出：您要控制的裝置為何？
* 輸入：燈
* 輸出：確定，開啟燈

> [!NOTE]
> 請注意，您現在可以控制所有裝載于 web 端點上的裝置。 您仍然需要訓練應用程式來測試新的變更，並重新發佈應用程式。

## <a name="add-validation-to-parameters"></a>將驗證新增至參數

**驗證** 是適用于特定參數類型的結構，可讓您設定參數值的條件約束，並在不屬於條件約束的值時提示您修正。 如需擴充驗證結構之參數類型的完整清單，請移至 [參考](./custom-commands-references.md)

使用 **>settemperature** 命令來測試驗證。 使用下列步驟來新增 **溫度** 參數的驗證。

1. 選取左窗格中的 [ **>settemperature** ] 命令。
1. 在中間窗格中選取 [  **溫度** ]。
1. 選取右窗格中的 [ **加入驗證** ]。
1. 在 **新的驗證** 視窗中，依照下列方式設定驗證，然後選取 [ **建立**]。


    | 參數設定 | 建議的值 | 說明 |
    | ---- | ---- | ---- |
    | 最小值 | `60` | 若為數字參數，此參數可以採用的最小值 |
    | 最大值 | `80` | 若為數字參數，此參數可以採用的最大值 |
    | 失敗回應 |  簡易編輯器 > 第一個變化 > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 如果驗證失敗，則提示要求新值 |

    > [!div class="mx-imgBorder"]
    > ![新增範圍驗證](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>試做

1. 選取右窗格頂端的 [ **定型** ] 圖示。

1. 定型完成後，請選取 [ **測試** ] 並嘗試幾個互動：

    - 輸入：將溫度設定為72度
    - 輸出：確定，將溫度設定為72度
    - 輸入：將溫度設定為45度
    - 輸出：抱歉，我只能設定介於60到80度之間的溫度
    - 輸入：改為將其設為72度
    - 輸出：確定，將溫度設定為72度

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [如何：加入互動規則](./how-to-custom-commands-add-interaction-rules.md)
