---
title: 快速入門：建立應用程式 - LUIS
titleSuffix: Azure Cognitive Services
description: 本快速入門說明如何建立 LUIS 應用程式，該應用程式會使用預先建置網域 `HomeAutomation` 來開啟或關閉燈光和應用程式。 這個預先建置網域可為您提供意圖、實體和範例語句。 完成之後，您會擁有一個在雲端中執行的 LUIS 端點。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 302321a36a6ce7526ad5e3144f87b88edbfaaec7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393785"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>快速入門：使用預先建置的家庭自動化應用程式

在本快速入門中，建立 LUIS 應用程式，該應用程式會使用預先建置網域 `HomeAutomation` 來開啟或關閉燈光和應用程式。 這個預先建置網域可為您提供意圖、實體和範例語句。 完成之後，您會擁有一個在雲端中執行的 LUIS 端點。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>建立新的應用程式
您可以在 [我的應用程式] 上建立和管理應用程式。

1. 在 LUIS 入口網站的 [我的應用程式] 清單中，選取 [+ 建立]。

    ![在 LUIS 入口網站的 [我的應用程式] 清單中，選取 [+ 建立]。](./media/create-app-in-portal.png)

1. 在對話方塊中，將您的應用程式命名為 `Home Automation`，然後選取 [完成]。 LUIS 會建立應用程式。 說明是選擇性的，而且不會用於撰寫或預測。 建立 LUIS 應用程式時，預測資源也是選擇性的。 當您將應用程式發佈至生產環境時，您應該指派預測資源，讓您的應用程式可以處理許多要求。

    ![在對話方塊中，將您的應用程式命名為 `Home Automation`。](./media/create-new-app-details.png)

    >[!NOTE]
    >建立應用程式之後便無法變更文化特性 (Culture)。

## <a name="add-prebuilt-domain"></a>新增預建網域

選取 [預建網域]，然後搜尋 **HomeAutomation**。 在 HomeAutomation 卡片上選取 [新增網域]。

![選取 [預建網域]，然後搜尋 'HomeAutomation'。 在 HomeAutomation 卡片上選取 [新增網域]。](media/luis-quickstart-new-app/home-automation.png)

當成功新增網域時，預先建置網域方塊會顯示 [移除網域] 按鈕。

## <a name="intents-and-entities"></a>意圖和實體

選取**意圖**，以檢閱 HomeAutomation 網域意圖。 預建網域意圖具有範例語句。

![HomeAutomation 意圖清單的螢幕擷取畫面](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation 意圖清單的螢幕擷取畫面")

> [!NOTE]
> 「無」是所有 LUIS 應用程式都會提供的意圖。 您可以使用它來處理未對應至應用程式所提供功能的語句。

選取 **HomeAutomation.TurnOff** 意圖。 您可以看到意圖包含以實體標示的語句清單。

[![HomeAutomation.TurnOff 意圖的螢幕擷取畫面](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 意圖的螢幕擷取畫面")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>測試應用程式
一旦您定型您的應用程式，就可以進行測試。 選取 [測試]。 將測試語句 (例如 `Turn off the lights`) 輸入到 [互動測試] 窗格中，然後按下 Enter 鍵。

```
Turn off the lights
```

檢查最高得分意圖是否對應至您對於每個測試語句所預期的意圖。

在此範例中，`Turn off the lights` 會正確識別為 **HomeAutomation.TurnOff** 的最高得分意圖。

![螢幕擷取畫面，具有醒目提示語句的測試面板](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

選取 [檢查] 以檢閱預測的詳細資訊。

![具有檢查資訊的測試面板螢幕擷取畫面](media/luis-quickstart-new-app/test.png)

再次選取 [測試] 以摺疊測試窗格。

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>查詢 V3 API 預測端點

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

1. 在瀏覽器網址列中，請確定查詢字串的 URL 中有下列名稱和值列。 如果查詢字串中沒有這些項目，請將其加入：

    |名稱/值組|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. 在瀏覽器網址列中，移至 URL 結尾，並針對 [查詢]`turn off the living room light`_值輸入_，然後按 Enter。

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
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
                            "score": 0.907323956,
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
> [使用程式碼呼叫 LUIS 端點](luis-get-started-cs-get-intent.md)
