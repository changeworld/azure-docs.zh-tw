---
title: 教學課程：角色的內容相關資料 - LUIS
description: 根據內容尋找相關的資料。 例如，從一個建築物和辦公室移到另一個建築物和辦公室的實體移動原點和目的地位置會相關。
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475816"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>教學課程：從語句擷取內容相關的資料

在本教學課程中，根據內容尋找相關的資料片段。 例如，從某個城市前往另一個城市的出發地和目的地位置。 可能會需要這兩個資料片段，且這兩者彼此相關。

角色可以與任何預先建置或自訂實體型別一起使用，並在範例表達和模式中使用。

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 建立新的應用程式
> * 新增意圖
> * 使用角色取得來源和目的地資訊
> * 定型
> * 發佈
> * 從端點取得意圖和實體角色

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>相關資料

此應用程式會判斷要將員工從出發地城市移至目的地城市的何處。 它會使用 GeographyV2 預先建置實體來識別縣/市名稱，並使用角色來決定表達中的位置類型 (來源和目的地)。

在擷取實體資料時應使用角色：

* 在表達的內容中彼此相關。
* 使用特定文字來表示每個角色。 這些字的範例包括：from/to、leaving/headed to、away from/toward。
* 這兩種角色經常在相同的表達中，允許 LUIS 從這種頻繁的內容使用方式中學習。
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。

## <a name="create-a-new-app"></a>建立新的應用程式

1. 登入 [LUIS **預覽版**入口網站](https://preview.luis.ai)。

1. 選取 [+新增對話應用程式]  ，輸入名稱 `HumanResources`，並保留預設文化特性 - **英文**。 將描述和預測資源保留空白。 選取 [完成]  。

## <a name="create-an-intent-to-move-employees-between-cities"></a>建議意圖來在城市之間移動員工

意圖可用來根據使用者的意圖 (從自然語言文字判斷) 對使用者語句進行分類。

若要將語句分類，意圖需要應歸類為此意圖的使用者語句範例。

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 選取 [+ 建立]  。

1. 在快顯對話方塊方塊中輸入 `MoveEmployeeToCity`，然後選取 [完成]  。

    > [!div class="mx-imgBorder"]
    > ![建立新意圖對話方塊的螢幕擷取畫面](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. 將您預期使用者會要求的數個範例語句新增至此意圖。

    |範例語句|
    |--|
    |move John W. Smith leaving Seattle headed to Orlando|
    |transfer Jill Jones from Seattle to Cairo|
    |Place John Jackson away from Tampa, coming to Atlanta |
    |move Debra Doughtery to Tulsa from Chicago|
    |mv Jill Jones leaving Cairo headed to Tampa|
    |Shift Alice Anderson to Oakland from Redmond|
    |Carl Chamerlin from San Francisco to Redmond|
    |Transfer Steve Standish from San Diego toward Bellevue |
    |lift Tanner Thompson from Kansas city and shift to Chicago|

    > [!div class="mx-imgBorder"]
    > ![MoveEmployee 意圖中有新語句的 LUIS 螢幕擷取畫面](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>新增預先建置的實體 geographyV2

預先建置實體 **geographyV2** 擷取位置資訊，包括縣/市名稱。 由於表達有兩個縣/市名稱，在內容中彼此相關，因此使用角色來擷取該內容。

1. 從左側瀏覽列中，選取 [實體]  。

1. 選取 [+新增預先建置的實體]  ，然後在搜尋列中輸入 `geo` 以篩選預先建置的實體。

    > [!div class="mx-imgBorder"]
    > ![將 geographyV2 預先建置的實體新增至應用程式](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. 選取此核取方塊，然後選取 [完成]  。

## <a name="add-roles-to-prebuilt-entity"></a>將角色新增至預先建置的實體

1. 在 [實體]  清單中，選取 [geographyV2]  開啟新的實體。
1. 若要新增角色，請選取 **+** 並新增下列兩個角色： `Origin` 和 `Destination`。

    > [!div class="mx-imgBorder"]
    > ![將角色新增至預先建置的實體](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>標記範例語句中的實體角色

1. 從左側導覽中選取 [意圖]  ，然後選取 [MoveEmployeeToCity]  意圖。 請注意，縣/市名稱會加上預先建置的實體 **geographyV2**。
1. 在內容工具列中，選取 [實體選擇區]  的_鉛筆圖示_。

    > [!div class="mx-imgBorder"]
    > ![從內容工具列中選取實體選擇區](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. 選取預先建立的實體 (**geographyV2**)，然後選取 [實體偵測器]  。
1. 在 [實體偵測器]  中，選取一個角色，即 [目的地]  。 這會變更滑鼠指標。 使用滑鼠指標來標記所有語句中作為目的地位置的文字。

    > [!div class="mx-imgBorder"]
    > ![在實體選擇區中選取角色](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. 返回 [實體偵測器]  ，將角色變更為 [原點]  。 使用滑鼠指標來標記所有語句中作為原點位置的文字。

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>訓練應用程式，因此可以測試意圖的變更

若要將應用程式定型，請選取 [定型]  。 在定型後，會將變更 (例如新的實體和加上標籤的語句) 套用至使用中模型。

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>發佈應用程式以便從 HTTP 端點加以存取

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. 移至網址列中的 URL 尾端，並以 `Please move Carl Chamerlin from Tampa to Portland` 取代 _YOUR_QUERY_HERE_。

此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回 `MoveEmployee` 意圖及所擷取的實體。

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相關資訊

* [實體概念](luis-concept-entity-types.md)
* [角色概念](luis-concept-roles.md)
* [預先建置實體清單](luis-reference-prebuilt-entities.md)
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)
* [角色](luis-concept-roles.md)

## <a name="next-steps"></a>後續步驟

本教學課程建立了新意圖，並針對出發地和目的地位置的內容相關學習資料新增了範例語句。 應用程式一旦經過訓練並發佈，用戶端應用程式即可使用該資訊來建立包含相關資訊的移動票證。

> [!div class="nextstepaction"]
> [了解如何新增複合實體](luis-tutorial-composite-entity.md)
