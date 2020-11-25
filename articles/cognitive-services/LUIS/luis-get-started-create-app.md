---
title: 快速入門：建立應用程式 - LUIS
description: 本快速入門說明如何建立 LUIS 應用程式，該應用程式會使用預先建置網域 `HomeAutomation` 來開啟或關閉燈光和應用程式。 這個預先建置網域可為您提供意圖、實體和範例語句。 完成之後，您會擁有一個在雲端中執行的 LUIS 端點。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/13/2020
ms.openlocfilehash: 2048079a9b1eb5234d03c6fe57073228bd892d53
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019155"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>快速入門：使用預先建置的家庭自動化應用程式

在本快速入門中，建立 LUIS 應用程式，該應用程式會使用預先建置網域 `HomeAutomation` 來開啟或關閉燈光和應用程式。 這個預先建置網域可為您提供意圖、實體和範例語句。 完成之後，您會擁有一個在雲端中執行的 LUIS 端點。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>建立新的應用程式
您可以在 [我的應用程式] 上建立和管理應用程式。

### <a name="create-an-application"></a>建立應用程式

若要建立應用程式，請按一下 [+ 新增應用程式]。 

在顯示的視窗中，輸入下列資訊：

|名稱  |描述  |
|---------|---------|
|AName     | 您的應用程式名稱。 例如 "home automation"。        |
|文化特性     | 您的應用程式了解及讀出的語言。   |
|描述 | 應用程式的描述。
|預測資源 | 將接收查詢的預測資源。 |

選取 [完成]  。

>[!NOTE]
>建立應用程式之後便無法變更文化特性 (Culture)。

## <a name="add-prebuilt-domain"></a>新增預建網域

1. 從左側導覽中，選取 [預建網域]。
1. 搜尋 **HomeAutomation**。
1. 在 HomeAutomation 卡片上選取 [新增網域]。

    > [!div class="mx-imgBorder"]
    > ![選取「預先建置網域」，然後搜尋 'HomeAutomation'。 在 HomeAutomation 卡片上選取「新增網域」。](media/luis-quickstart-new-app/home-automation.png)

    當成功新增網域時，預先建置網域方塊會顯示 [移除網域] 按鈕。

## <a name="intents-and-entities"></a>意圖和實體

1. 在左側導覽功能表中選取 [意圖]，以查看 HomeAutomation 網域意圖。 其範例語句包括 `HomeAutomation.QueryState` 和 `HomeAutomation.SetDevice`。

    > [!NOTE]
    > 「無」是所有 LUIS 應用程式都會提供的意圖。 您可以使用它來處理未對應至應用程式所提供功能的語句。

1. 選取 **HomeAutomation.TurnOff** 意圖。 意圖包含以實體標示的範例語句清單。

    > [!div class="mx-imgBorder"]
    > [![HomeAutomation.TurnOff 意圖的螢幕擷取畫面](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 意圖的螢幕擷取畫面")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. 如果您想要檢視應用程式的實體，請選取 [實體]。 如果您按一下其中一個實體 (例如 **HomeAutomation.DeviceName)** ，您會看到其相關值的清單。 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="影像替代文字" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>測試應用程式
一旦您定型您的應用程式，就可以進行測試。

1. 從右上方導覽中選取 [測試]。

1. 將測試語句 (例如 `Turn off the lights`) 輸入到 [互動測試] 窗格中，然後按下 Enter 鍵。 例如，*關燈*。

    在此範例中，`Turn off the lights` 會正確識別為 **HomeAutomation.TurnOff** 的最高得分意圖。

    ![螢幕擷取畫面，具有醒目提示語句的測試面板](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. 選取 [檢查] 以檢視預測的詳細資訊。

    > [!div class="mx-imgBorder"]
    > ![具有檢查資訊的測試面板的螢幕擷取畫面](media/luis-quickstart-new-app/test.png)

1. 關閉 [測試] 窗格。

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL

若要在聊天機器人或其他應用程式中收到 LUIS 預測，您必須將應用程式發佈到預測端點。

1. 選取視窗右上角的 [發佈]。

1. 選取 [生產位置]，然後選取 [完成]。

    > [!div class="mx-imgBorder"]
    > ![LUIS 發佈到端點的螢幕擷取畫面](media/howto-publish/publish-app-popup.png)

1. 選取通知中的 [存取您的端點 URL] 連結，以移至 [Azure 資源] 頁面。 端點 URL 會列為 [範例查詢]。

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>查詢 V3 API 預測端點

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. 在瀏覽器網址列中，請確定查詢字串的 URL 中有下列名稱和值列。 如果查詢字串中沒有這些項目，請將其加入：

    |名稱/值組|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. 在瀏覽器網址列中，移至 URL 結尾，並針對 [查詢] 值輸入 `turn off the living room light`，然後按 Enter。

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    深入了解 [V3 預測端點](luis-migration-api-v3.md)。


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

您可以從程式碼呼叫端點：

> [!div class="nextstepaction"]
> [使用程式碼呼叫 LUIS 端點](./luis-get-started-get-intent-from-rest.md)