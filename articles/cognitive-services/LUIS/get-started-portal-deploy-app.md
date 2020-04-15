---
title: 快速入門：使用 LUIS 入口網站部署應用程式
description: 此快速入門說明如何藉由建立預測端點資源、指派資源、訓練及發佈應用程式來部署應用程式。
ms.topic: quickstart
ms.date: 04/06/2020
ms.openlocfilehash: aaf86766c2357c5382b78cd4a35fd4b159e5c0f3
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756299"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>快速入門：在 LUIS 入口網站中部署應用程式

LUIS 應用程式準備將表達預測傳回用戶端應用程式 (例如聊天機器人) 後，您必須將應用程式部署至預測端點。

在本快速入門中，您將了解如何部署應用程式。 您將建立預測的端點資源、將資源指派給應用程式、定型應用程式，並發佈應用程式。

## <a name="prerequisites"></a>Prerequisites

* 取得 [Azure 訂用帳戶](https://azure.microsoft.com/free)。
* 完成[先前的入口網站快速入門](get-started-portal-build-app.md)，或[下載並匯入應用程式](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)。
* 如果您的應用程式版本早於 Azure 資源驗證，請[遷移至 Azure 資源](luis-migration-authoring.md)。 當電子郵件驗證生效時，某些入口網站頁面的外觀會有所不同。

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>將資源金鑰指派給 LUIS 入口網站中的 LUIS 應用程式

每當您為 LUIS 建立新製作或查詢預測資源時，都需要將資源指派給 LUIS 應用程式。 指派之後，除非您建立新的資源，否則不需要再次執行此步驟。 您可以建立新的資源，以擴充您的應用程式區域，或支援更多預測查詢數目。

1. 登入[預覽 LUIS 入口網站](https://preview.luis.ai)，從應用程式清單中選擇 **myEnglishApp** 應用程式。

1. 選取右上方功能表中的 [管理]  ，然後選取 [Azure 資源]  。

1. 若要新增 LUIS，請選取 [新增預測資源]  。

    ![若要新增 LUIS 預測資源，請選取 [新增預測資源]。](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. 選取您的租用戶、訂用帳戶和資源群組。 選取 [指派資源]  。

   ![將資源指派給應用程式](./media/get-started-portal-deploy-app/assign-resource.png)

1. 完成相同的步驟，將製作金鑰新增至您的應用程式。

1. 在新預測資源的資料表中尋找新的資料列並複製端點 URL。 以正確方式建構，可對 LUIS API 端點提出 `HTTP GET` 要求進行預測。

> [!TIP]
> 如果您想要使用主動式學習來改善 LUIS 應用程式，請選取 [變更查詢參數]  ，然後選取 [儲存記錄]  。 此動作會藉由新增 `log=true` 查詢字串參數來變更範例 URL。 對執行階段端點進行預測查詢時，請複製並使用已變更的範例查詢 URL。

## <a name="train-the-app"></a>進行應用程式定型

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>將應用程式發佈至預測端點

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>預測端點要求

在預覽入口網站中，URL 結尾的 `query=` 是使用者的語句附加至 GET 要求之處。 在 `query=` 後面，輸入於先前快速入門結尾使用的相同使用者表達：

```Is there a form named hrf-234098```

確定查詢字串包含下列配對：

* `show-all-intents=true`
* `verbose=true`

瀏覽器會顯示回應：

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
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

若要在 [測試] 窗格中看到和此相同層級的資訊，您必須發佈應用程式。 發佈應用程式之後，請選取[測試] 窗格中的 [與已發行比較]  。 在發佈的測試窗格中，使用 [顯示 JSON 檢視]  ，即可查看和上一個步驟相同的 JSON。 如此一來，您即可比較目前作業所在應用程式的變更與已發佈至端點的應用程式。

[![比較目前正在編輯與已發佈的應用程式版本](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從頂端導覽功能表中，選取 [我的應用程式]  。 從清單中選取應用程式的核取方塊，再從清單上方的內容工具列中選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [找出常見的意圖和實體](luis-tutorial-prebuilt-intents-entities.md)
