---
title: 教學課程：從 Power Apps 查詢
titleSuffix: Azure Cognitive Search
description: 此逐步指引會說明如何建置可連線至 Azure 認知搜尋索引、傳送查詢和呈現結果的 Power App。
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 04434753949f0e00eaf99610199cc4997ef53caf
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751050"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>教學課程：從 Power Apps 查詢認知搜尋索引

利用 Power Apps 的快速應用程式開發環境在 Azure 認知搜尋中為可搜尋的內容建立自訂應用程式。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 連線至 Azue 認知搜尋
> * 設定查詢要求
> * 在畫布應用程式中將結果視覺化

如果您沒有 Azure 訂用帳戶，請在開始前開啟[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* [Power Apps 帳戶](http://make.powerapps.com)

* [飯店 - 範例索引](search-get-started-portal.md)

* [查詢 API 金鑰](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 - 建立自訂連接器

Power Apps 中的連接器是資料來源連線。 在此步驟中，您將建立自訂連接器，以連線至雲端中的搜尋索引。

1. [登入](http://make.powerapps.com) Power Apps。

1. 在左側展開 [資料] > [自訂連接器]。
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="自訂連接器功能表" border="true":::

1. 選取 [+ 新增自訂連接器]，然後選取 [從空白建立]。

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="[從空白建立] 功能表" border="true":::

1. 為自訂連接器命名 (例如，*AzureSearchQuery*)，然後按一下 [繼續]。

1. 在 [一般] 頁面中輸入資訊：

   * 圖示背景色彩 (例如，#007ee5)
   * 說明 (例如，「Azure 認知搜尋的連接器」)
   * 在 [主機] 中，您必須輸入搜尋服務 URL (例如 `<yourservicename>.search.windows.net`)
   * 在 [基底 URL] 中，直接輸入 "/" 即可

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="[一般資訊] 對話方塊" border="true":::

1. 在 [安全性] 頁面中，將 [API 金鑰] 設定為 [驗證類型]，並將參數標籤和參數名稱皆設定為 [api-key]。 在 [參數位置] 中選取 [標頭]，如下所示。

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="驗證類型選項" border="true":::

1. 在 [定義] 頁面中選取 [+ 新增動作] 以建立會查詢索引的動作。 輸入「查詢」這個值來作為作業識別碼的摘要和名稱。 輸入說明，例如「查詢搜尋索引」。

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="新增動作選項" border="true":::

1. 向下捲動。 在 [要求] 中選取 [+ 從範例匯入] 按鈕，以設定搜尋服務的查詢要求：

   * 選取動詞 `GET`

   * 在 [URL] 中輸入搜尋索引的範例查詢 (`search=*` 會傳回所有文件，`$select=` 則可讓您選擇欄位)。 需要 API 版本。 完整指定的 URL 可能如下所示：`https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * 在 [標頭] 中輸入 `Content-Type`。 

     **Power Apps** 會使用此語法從查詢中擷取參數。 請注意，我們已明確定義 [搜尋] 欄位。 

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="從範例匯入" border="true":::

1. 按一下 [匯入] 以自動填入要求。 按一下每個參數旁的 **...** 符號，以完成參數中繼資料的設定。 按一下 [上一步] 可在每個參數更新之後返回 [要求] 頁面。

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="[從範例匯入] 對話方塊" border="true":::

1. 針對 [搜尋]：將 `*` 設定為 [預設值]、將 [必要] 設定為 [False]，並將 [可見性] 設定為 [無]。 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="搜尋參數中繼資料" border="true":::

1. 針對 [選取]：將 `HotelName,Description,Address/City` 設定為 [預設值]、將 [必要] 設定為 [False]，並將 [可見性] 設定為 [無]。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="選取參數中繼資料" border="true":::

1. 針對 [api-version]：將 `2020-06-30` 設定為 [預設值]、將 [必要] 設定為 [True]，並將 [可見性] 設定為 [內部]。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="版本參數中繼資料" border="true":::

1. 針對 [Content-Type]：設定為 `application/json`。

1. 在進行這些變更後，切換至 [Swagger 編輯器] 檢視。 在 [參數] 區段中，您應該會看到下列設定：

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. 返回 **3.要求**步驟，並向下卷動到 [回應] 區段。 按一下 [新增預設回應]。 這很重要，因為這可協助 Power Apps 了解回應的結構描述。 

1. 貼上範例回應。 若要擷取範例回應，有個簡單的方式是透過 Azure 入口網站中的「搜尋總管」。 在「搜尋總管」中，您應該輸入與要求相同的查詢，但新增 **$top=2** 以將結果限制為只有兩個文件：`search=*&$select=HotelName,Description,Address/City&$top=2`。 

   Power Apps 只需要幾個結果即可偵測結構描述。

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > 您可以輸入的 JSON 回應有字元限制，因此，您可以先簡化 JSON 再貼上。 回應的結構描述和格式比值本身更加重要。 例如，[說明] 欄位可以簡化為只有第一個句子。

1. 按一下右上方的 [建立連接器]。

## <a name="2---test-the-connection"></a>2 - 測試連線

第一次建立連接器時，您必須從 [自訂連接器] 清單將其重新開啟才能進行測試。 之後，如果您進行其他更新，則可以從精靈內進行測試。

您需要有[查詢 API 金鑰](search-security-api-keys.md#find-existing-keys)才能進行此工作。 每次建立連線時 (無論是測試回合還是包含在應用程式中)，連接器都需要使用查詢 API 金鑰來連線到 Azure 認知搜尋。

1. 在最左邊按一下 [自訂連接器]。

1. 依名稱搜尋連接器 (在本教學課程中為 "AzureSearchQuery")。

1. 選取連接器、展開 [動作] 清單，然後選取 [檢視屬性]。

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="檢視屬性" border="true":::

1. 選取右上方的 [編輯]。

1. 選取 [4.測試] 以開啟 [測試] 頁面。

1. 在 [測試作業] 中，按一下 [+ 新增連線]。

1. 輸入查詢 API 金鑰。 這種 Azure 認知搜尋查詢已可用於對索引進行唯讀存取。 您可以在 Azure 入口網站中[尋找金鑰](search-security-api-keys.md#find-existing-keys)。 

1. 在 [作業] 中，按一下 [測試作業] 按鈕。 如果成功，您應該會看到 200 狀態，並在回應主體中看到說明搜尋結果的 JSON。

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON 回應" border="true":::

## <a name="3---visualize-results"></a>3 - 將結果視覺化

在此步驟中，請建立具有搜尋方塊、搜尋按鈕和結果顯示區域的 Power App。 Power App 會連線到最近建立的自訂連接器，以從 Azure 搜尋服務取得資料。

1. 在左側展開 [應用程式] > [+ 新增應用程式] > [畫布]。

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="建立畫布應用程式" border="true":::

1. 選取應用程式的類型。 在本教學課程中，請建立具有 [手機配置] 的 [空白應用程式]。 隨即會出現 **Power Apps Studio**。

1. 進入 Power Apps Studio 後，選取 [資料來源] 索引標籤，然後按一下您剛才建立的新連接器。 在我們的案例中，此連接器的名稱為 *AzureSearchQuery*。 按一下 [新增連線]。

   輸入查詢 API 金鑰。

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="連線連接器" border="true":::

    現在 *AzureSearchQuery* 是可從應用程式使用的資料來源。

1. 在 [插入] 索引標籤上，於畫布中新增一些控制項。

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="插入控制項" border="true":::

1. 插入下列元素：

   * 具有值「Query:」的文字標籤
   * 文字輸入元素 (將其稱為 *txtQuery*，預設值："*")
   * 具有文字「搜尋」的按鈕 
   * 所呼叫的垂直資源庫 (將其稱為 *galleryResults*)

    畫布應該會看起來像這樣：

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="控制項配置" border="true":::

1. 若要讓 [搜尋] 按鈕發出查詢，請將下列動作貼入 **OnSelect**：

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   下列螢幕擷取畫面顯示了 **OnSelect** 動作的資料編輯列。

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="按鈕 OnSelect" border="true":::

   此動作會讓按鈕使用 [txtQuery] 文字方塊中的文字作為查詢字詞，來以搜尋查詢的結果更新名為 *azResult* 的新集合。

   > [!NOTE]
   > 如果您收到公式語法錯誤「函式 'ClearCollect' 有一些不正確函式」，請嘗試下列方法：
   > 
   > * 首先，請確定連接器參考是否正確。 清除連接器名稱，然後開始輸入連接器的名稱。 Intellisense 應該會建議正確的連接器和動詞。
   > 
   > * 若錯誤持續發生，請刪除連接器再重新建立。 如果連接器有多個執行個體，應用程式可能會使用了錯誤的執行個體。
   > 

1. 將「垂直資源庫」控制項連結至您完成上一個步驟時所建立的 *azResult* 集合。 

   選取資源庫控制項，然後在 [屬性] 窗格中執行下列動作。

   * 將 [資料來源] 設定為 [azResult]。
   * 根據索引資料的類型選取適合您的 [配置]。 在本案例中，我們使用的是 [標題、副標題和主體] 配置。
   * [編輯欄位]，然後選取您想要視覺化的欄位。

    由於我們在定義連接器時提供了範例結果，因此應用程式會知道索引中可用的欄位。
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="[資源庫] 欄位" border="true":::   
 
1. 按 **F5** 以預覽應用程式。  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="最終應用程式" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。

## <a name="next-steps"></a>後續步驟

Power Apps 可讓您快速開發自訂應用程式。 既然您已了解如何連線至搜尋索引，接下來請深入了解如何在自訂 Power App 中建立豐富的視覺化體驗。

> [!div class="nextstepaction"]
> [Power Apps 學習目錄](https://docs.microsoft.com/powerapps/learning-catalog/get-started)

