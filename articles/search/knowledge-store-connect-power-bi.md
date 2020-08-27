---
title: 使用 Power BI 連線至知識存放區
titleSuffix: Azure Cognitive Search
description: 使用 Power BI 連接 Azure 認知搜尋知識存放區，以進行分析和探索。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 5be97414a2e4270870d976a9922415b227c66e27
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924345"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>使用 Power BI 連線到知識存放區

在本文中，了解如何使用 Power BI Desktop 應用程式中的 Power Query 來連線及探索知識存放區。 您可以透過範本更快地開始使用，或從頭開始建立自訂儀表板。 以下簡短影片將示範如何使用 Azure 認知搜尋搭配 Power BI 來豐富資料的體驗。


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ 遵循[在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)或[使用 REST 建立 Azure 認知搜尋知識存放區](knowledge-store-create-rest.md)中的步驟，建立本逐步解說中所使用的知識存放區範例。 您也需要用來建立知識存放區的 Azure 儲存體帳戶名稱，以及其來自 Azure 入口網站的存取金鑰。

+ [安裝 Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>範例 Power BI 範本 - 僅限 Azure 入口網站

[使用 Azure 入口網站建立知識存放區](knowledge-store-create-portal.md)時，您可以在[匯入資料](https://github.com/Azure-Samples/cognitive-search-templates)精靈的第二個頁面選擇下載 **Power BI 範本**。 此範本提供了數個適用於文字型內容的視覺效果，例如 WordCloud 和 Network Navigator。 

按一下 [新增認知技能]**** 頁面上的 [取得 Power BI 範本]****，可從其公用 GitHub 位置擷取並下載範本。 此精靈會修改範本以容納您的資料圖形，如同精靈中指定的知識存放區投影所擷取的。 因此，您每次執行精靈時所下載的範本都會有所不同，並採用不同的資料輸入和技能選取項目。

![範例 Azure 認知搜尋 Power BI 範本](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "範例 Power BI 範本")

> [!NOTE]
> 雖然範本會在精靈進行時下載，但您必須等到知識存放區實際建立於 Azure 資料表儲存體後，才能使用範本。

## <a name="connect-with-power-bi"></a>使用 Power BI 進行連接

1. 啟動 Power BI Desktop 並按一下 [取得資料]****。

1. 在 [取得資料]**** 中，依序選取 [Azure]**** 和 [Azure 資料表儲存體]****。

1. 按一下 [ **連接**]。

1. 針對 [帳戶名稱或 URL]****，輸入您的 Azure 儲存體帳戶名稱 (系統會為您建立完整的 URL)。

1. 如果出現提示，請輸入儲存體帳戶金鑰。

1. 選取先前的逐步解說所建立的旅館評論資料所屬的資料表。 

   + 入口網站逐步解說的資料表名稱為 *hotelReviewsSsDocument*、*hotelReviewsSsEntities*、*hotelReviewsSsKeyPhrases* 和 *hotelReviewsSsPages*。 
   
   + REST 逐步解說的資料表名稱為 *hotelReviewsDocument*、*hotelReviewsPages*、*hotelReviewsKeyPhrases* 和 *hotelReviewsSentiment*。

1. 按一下 [載入]。

1. 在頂端的功能區中，按一下 [編輯查詢]**** 以開啟 [Power Query 編輯器]****。

   ![開啟 Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "開啟 Power Query")

1. 選取 hotelReviewsSsDocument**，然後移除 PartitionKey**、RowKey** 和 Timestamp** 資料行。 
   ![編輯資料表](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "編輯資料表")

1. 按一下資料表右上方具有反向箭號的圖示，以展開 [內容]**。 當資料行清單出現時，選取所有資料行，然後取消選取以 'metadata ' 開頭的資料行。 按一下 [確定]**** 以顯示選取的資料行。

   ![編輯資料表](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "展開內容")

1. 按一下資料行左上方的 ABC-123 圖示，變更下列資料行的資料類型。

   + 針對 content.latitude** 和 Content.longitude**，選取 [十進位數字]****。
   + 針對 Content.reviews_date** 和 Content.reviews_dateAdded**，選取 [日期/時間]****。

   ![變更資料類型](media/knowledge-store-connect-power-bi/powerbi-change-type.png "變更資料類型")

1. 選取 hotelReviewsSsPages**，然後重複步驟 9 和 10 以刪除資料行並展開 [內容]**。
1. 將 Content.SentimentScore** 的資料類型變更為 [十進位數字]****。
1. 選取 hotelReviewsSsKeyPhrases**，然後重複步驟 9 和 10 以刪除資料行並展開 [內容]**。 此資料表無須修改資料類型。

1. 按一下命令列上的 [關閉並套用]****。

1. 按一下左側導覽窗格中的 [模型] 圖格，然後驗證 Power BI 是否顯示全部三個資料表之間的關聯性。

   ![驗證關聯性](media/knowledge-store-connect-power-bi/powerbi-relationships.png "驗證關聯性")

1. 按兩下每個關聯性，並確定 [交叉篩選方向]**** 設定為 [兩者]****。  這可讓您的視覺效果在套用篩選條件後重新整理。

1. 按一下左側瀏覽窗格中的 [報表] 磚，以透過視覺效果瀏覽資料。 對於文字欄位，資料表和卡片是有用的視覺效果。 您可以選擇三個不同資料表中的欄位，以填入資料表或卡片。 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>清理

如果您是在自己的訂用帳戶中進行，建議您在專案結束時判斷自己是否仍需要先前所建立的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。

## <a name="next-steps"></a>後續步驟

若要了解如何使用儲存體總管來探索此知識存放區，請參閱下列文章。

> [!div class="nextstepaction"]
> [使用儲存體總管檢視](knowledge-store-view-storage-explorer.md)