---
title: Azure Cosmos DB 設計模式：社交媒體應用程式
description: 了解具有 Azure Cosmos DB 與其他 Azure 服務之儲存體彈性的社交網路設計模式。
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: d4fbadd03f443d28376a122c7ecb06c475c2247d
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850700"
---
# <a name="going-social-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 跨足社交

在當今大幅互連的社會當中，您的生活或多或少都成為 **社交網路**的一部分。 您會使用社交網路與朋友、同事、家人保持連絡，有時候還可以跟擁有共同興趣的人交流這份愛好。

身為工程師或開發人員，您可能會好奇這些網路如何儲存資料以及與其互連。 或者，您可能已針對特定利基市場，建立或建構新的社交網路。 此時就會出現這個重大問題︰如何儲存所有這些資料？

假設您要建立一個全新且亮眼的社交網路，讓使用者可以張貼含有圖片、影片或甚至音樂等相關媒體的文章。 使用者可以評論貼文，並給予評等的點數。 他們可以看到貼文摘要，並在主要的網站登陸頁面上進行互動。 乍聽之下，這個方法似乎不複雜，但為了簡潔起見，我們先就此打住。 (您可以深入探討受關聯性影響的自訂使用者摘要，但這已超出本文的目標範圍)。

那麼，要如何儲存這些資料，以及儲存在哪裡呢？

您可能有使用 SQL 資料庫的體驗，或具有[關聯式資料模型化](https://en.wikipedia.org/wiki/Relational_model) \(英文\) 的概念。 而您可能會著手繪製如下的內容：

:::image type="content" source="./media/social-media-apps/social-media-apps-sql.png" alt-text="說明相對關聯式模型的圖表" border="false":::

這麼正規且漂亮的資料結構...卻無法調整。

別誤會，我已經使用 SQL 資料庫很久了。 它的確很棒，但它也跟其他的模式、做法和軟體平台一樣，不可能是萬能的。

為什麼 SQL 不是此案例的最佳選擇？ 讓我們看看一篇貼文的結構。 如果我想要在網站或應用程式中顯示這篇貼文，則必須執行查詢並指定...聯結八個資料表 (！)，就只為了顯示單一貼文。 現在，想像螢幕上有一連串動態載入並顯示的文章，您可能就知道我指的是什麼。

您可以使用一個功能夠強大的龐大 SQL 執行個體，以解決數千個含有眾多聯結的查詢來提供您的內容。 但如果有較簡單的解決方案，何須如此大費周章？

## <a name="the-nosql-road"></a>NoSQL 的方法

本文會引導您如何以具成本效益的方式使用 Azure 的 NoSQL 資料庫 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)，將社交平台的資料模型化。 此外，也會告訴您如何使用 [Gremlin API](../cosmos-db/graph-introduction.md) 等其他 Azure Cosmos DB 功能。 如果使用 [NoSQL](https://en.wikipedia.org/wiki/NoSQL) 方法，將資料以 JSON 格式儲存並套用[反正規化](https://en.wikipedia.org/wiki/Denormalization)時，即可將之前複雜的貼文轉換成單一[文件](https://en.wikipedia.org/wiki/Document-oriented_database)：

```json
{
    "id":"ew12-res2-234e-544f",
    "title":"post title",
    "date":"2016-01-01",
    "body":"this is an awesome post stored on NoSQL",
    "createdBy":User,
    "images":["https://myfirstimage.png","https://mysecondimage.png"],
    "videos":[
        {"url":"https://myfirstvideo.mp4", "title":"The first video"},
        {"url":"https://mysecondvideo.mp4", "title":"The second video"}
    ],
    "audios":[
        {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
        {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
    ]
}
```

如此一來，即可利用單一查詢而不需要任何聯結，即可取得該文件。 這個查詢不只簡單、直接也節省成本，而且只需較少的資源，即可達到更佳的結果。

Azure Cosmos DB 可利用自身的自動索引編製作業，確保所有屬性都已編製成索引。 自動索引編製作業甚至還可以進行[自訂](index-policy.md)。 無結構描述的方法可讓我們使用不同的動態結構來儲存文件。 或許日後您會希望貼文具有類別或已與其建立關聯的主題標籤清單？ 我們不需要進行任何額外的工作，Cosmos DB 會處理新的文件與新增的屬性。

對某篇文章的回應可被視為含父屬性的其他貼文。 (此做法可簡化物件的對應)。

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":User2,
    "parent":"ew12-res2-234e-544f"
}

{
    "id":"asd2-fee4-23gc-jh67",
    "title":"Ditto!",
    "date":"2016-01-03",
    "createdBy":User3,
    "parent":"ew12-res2-234e-544f"
}
```

接著，即可將所有社交互動儲存為個別物件上的計數器︰

```json
{
    "id":"dfe3-thf5-232s-dse4",
    "post":"ew12-res2-234e-544f",
    "comments":2,
    "likes":10,
    "points":200
}
```

若要建立摘要，只需建立文件，包含具有特定相關性順序的貼文識別碼清單即可︰

```json
[
    {"relevance":9, "post":"ew12-res2-234e-544f"},
    {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
    {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
]
```

您可以擁有依建立日期排序的「最新」貼文串流。 您也可以擁有過去 24 小時內獲得較多讚的「最熱門」貼文串流。 您甚至可以依據邏輯 (例如關注者與興趣) 為每位使用者實作自訂串流。 而這仍屬於文章清單。 關鍵在於如何建立這些清單，而且讀取效能不會受到影響。 一旦取得其中一個清單，您就可以使用[IN 關鍵字](sql-query-keywords.md#in)發出單一查詢 Cosmos DB，一次取得貼文的頁面。

您可以使用 [Azure App Service](https://azure.microsoft.com/services/app-service/) 的背景處理序 [Webjobs](../app-service/webjobs-create.md) 來建置摘要串流。 建立貼文之後，即可使用 [Azure 儲存體](https://azure.microsoft.com/services/storage/) [佇列](../storage/queues/storage-dotnet-how-to-use-queues.md)來觸發背景處理，以及使用 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 來觸發 WebJobs，其中會根據您自己的自訂邏輯，在串流內實作貼文傳播。

您也可以使用相同的技術，以延後方式來處理貼文的點數和按讚數，建立最終一致的環境。

至於粉絲，則需要有更多的技巧來處理。 Cosmos DB 擁有文件大小限制，在讀取/寫入大型文件時可能會影響應用程式的延展性。 因此，您可能會考慮使用下列結構，以文件形式儲存粉絲：

```json
{
    "id":"234d-sd23-rrf2-552d",
    "followersOf": "dse4-qwe2-ert4-aad2",
    "followers":[
        "ewr5-232d-tyrg-iuo2",
        "qejh-2345-sdf1-ytg5",
        //...
        "uie0-4tyg-3456-rwjh"
    ]
}
```

此結構可能適用於有數千位粉絲的使用者。 但如果某位名人加入行列，這個方法將導致產生大型文件，而可能最終會達到文件大小上限。

為了解決這個問題，您可以使用混合式方法。 您可以在「使用者統計資料」文件中儲存粉絲人數：

```json
{
    "id":"234d-sd23-rrf2-552d",
    "user": "dse4-qwe2-ert4-aad2",
    "followers":55230,
    "totalPosts":452,
    "totalPoints":11342
}
```

您可以使用 Azure Cosmos DB [Gremlin API](../cosmos-db/graph-introduction.md)，將實際的粉絲圖表儲存，並針對每位使用者和[邊緣](http://mathworld.wolfram.com/GraphEdge.html)建立[頂點](http://mathworld.wolfram.com/GraphVertex.html)，以維護「A-追蹤-B」的關聯性。 透過 Gremlin API，您可以取得特定使用者的粉絲，並建立更複雜的查詢來建議具有共通點的人。 如果您在圖表中新增大眾喜愛的「內容類別」，便可開始編排含有智慧型內容探索的體驗、建議您所關注和喜愛的人，或尋找可能與您有許多共通點的人。

您仍可運用使用者統計資料的文件，來建立 UI 或快速分析預覽中的卡片。

## <a name="the-ladder-pattern-and-data-duplication"></a>「階梯」模式與資料複製

您可能已經注意到，參考貼文的 JSON 文件中，有一位使用者出現多次。 您猜的沒錯，出現多次意謂著在使用這種反正規化的情況下，描述使用者的資訊可能會出現在多個位置。

為了能更快速地查詢，您會造成資料重複情況。 這個副作用的問題在於，如果因某個動作而導致某位使用者的資料變更，您就必須找出該使用者進行過的所有活動，並全數更新。 聽起來不太實際，對吧？

您將識別應用程式中針對每項活動顯示的使用者關鍵屬性，來解決此問題。 如果您在應用程式中以視覺方式顯示某篇貼文，且只顯示建立者的名稱和圖片，那麼，為何要在 "createdBy" 屬性中儲存所有使用者資料？ 如果針對每個回應，您都只顯示使用者的圖片，就不需要該使用者資訊的其餘部分。 這時我所謂的「階梯模式」就會開始涉入。

讓我們以使用者資訊當作範例︰

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "address":"742 Evergreen Terrace",
    "birthday":"1983-05-07",
    "email":"john@doe.com",
    "twitterHandle":"\@john",
    "username":"johndoe",
    "password":"some_encrypted_phrase",
    "totalPoints":100,
    "totalPosts":24
}
```

透過查看此資訊，您可以快速偵測出哪些是重要資訊而哪些不是，藉此建立出一道「階梯」：

:::image type="content" source="./media/social-media-apps/social-media-apps-ladder.png" alt-text="階梯模式的圖表" border="false":::

最小的一階稱為「使用者區塊」，其為可識別使用者並為資料重複使用的最基本部分。 透過將重複資料的大小縮減成僅限您將「顯示」的資訊，即可降低需要大量更新的機率。

中間的階梯稱為「使用者」。 其為在 Cosmos DB 中大多數與效能相依之查詢會用到的完整資料，也是最重要與最常存取的資料。 它包含「使用者區塊」所代表的資訊。

最大的一階為「擴充使用者」。 它包含重要的使用者資訊，以及其他不需快速讀取或擁有最終使用情況的相關資料 (例如登入過程)。 這些資料可以儲存在 Cosmos DB 外部、Azure SQL Database 中或 Azure 儲存體資料表中。

為什麼要分割使用者，甚至將此資訊儲存在不同的位置中？ 因為從效能方面來看，文件越大，查詢也就越昂貴。 讓文件保持精簡，只包含適當資訊以針對社交網路進行所有與效能相依的查詢。 將用於最終情況 (例如，完整的設定檔編輯、登入，以及用於使用情況分析和巨量資料計劃的資料採礦) 的其他額外資訊儲存起來。 您實際上並不在乎針對資料採礦進行的資料收集作業速度是否較慢，因為這項作業是在 Azure SQL Database 上執行的。 但您確實在意使用者是否擁有快速且精簡的體驗。 儲存在 Cosmos DB 上的使用者會類似如下程式碼︰

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "username":"johndoe"
    "email":"john@doe.com",
    "twitterHandle":"\@john"
}
```

而貼文應該看起來像這樣：

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":{
        "id":"dse4-qwe2-ert4-aad2",
        "username":"johndoe"
    }
}
```

在會影響到區塊屬性的地方進行編輯時，您可以輕鬆找到受影響的文件。 只要使用指向索引屬性 (例如 `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`) 的查詢，然後更新區塊即可。

## <a name="the-search-box"></a>搜尋方塊

使用者將可幸運地產生許多內容。 而您應該能夠提供功能來搜尋和尋找可能未直接在其內容串流中的內容，這些內容未直接在其內容串流中的原因可能是您未關注建立者，也可能是您只是想要尋找 6 個月前所發的舊貼文。

由於您是使用 Azure Cosmos DB，因此您可以在幾分鐘內使用[Azure 認知搜尋](https://azure.microsoft.com/services/search/)輕鬆地執行搜尋引擎，而不需要輸入任何程式碼，除了搜尋程式和 UI 以外。

為什麼這個程序這麼簡單？

Azure 認知搜尋會執行其呼叫[索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx)、在資料存放庫中攔截的背景處理常式，並自動新增、更新或移除您在索引中的物件。 它們支援 [Azure SQL Database 索引子](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/)、[Azure Blob 索引子](../search/search-howto-indexing-azure-blob-storage.md)，甚至也支援 [Azure Cosmos DB 索引子](../search/search-howto-index-documentdb.md)。 從 Cosmos DB 到 Azure 認知搜尋的資訊轉換相當簡單。 因為這兩項計數皆是以 JSON 格式來儲存資訊，您只需要[建立索引](../search/search-create-index-portal.md)，並從要編製索引的文件來對應屬性即可。 這樣就大功告成了！ 視資料大小而定，所有內容將可在短短幾分鐘內透過雲端基礎結構中的最佳搜尋即服務解決方案來搜尋。

如需有關 Azure 認知搜尋的詳細資訊，您可以造訪[漫遊指南以進行搜尋](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)。

## <a name="the-underlying-knowledge"></a>基礎知識

儲存所有這些每天不斷成長的內容之後，您可能會考慮到：該怎麼運用所有這些來自使用者的資訊串流？

答案很簡單：讓這些內容發揮效益，並從中學習。

但是，您可以學習到什麼呢？ 有幾個簡單的範例，包括 [情感分析](https://en.wikipedia.org/wiki/Sentiment_analysis)、根據使用者喜好設定提供的內容建議，或甚至是確保您社交網路所發佈的內容適合闔家瀏覽的自動化 Content Moderator。

現在，大家一定更感興趣了吧？您一定以為要有數學的博士學位，才能從簡單的資料庫和檔案中擷取這些模式和資訊，但您錯了。

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)（屬於[Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)的一部分）是完全受控的雲端服務，可讓您使用簡單的拖放介面來建立工作流程、以[R](https://en.wikipedia.org/wiki/R_\(programming_language\))撰寫自己的演算法程式碼，或使用一些已建立且可供使用的 api，例如：[文字分析](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2)、內容仲裁或[建議](https://gallery.azure.ai/Solution/Recommendations-Solution)。

為了達成上述任一「機器學習服務」案例，您可以使用 [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) 來內嵌不同來源的資訊。 您也可以使用 [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) 來處理資訊並產生可由 Azure Machine Learning 處理的輸出。

另一個可行的做法是使用 [Azure 認知服務](https://www.microsoft.com/cognitive-services)來分析使用者內容；您不僅可以更加了解他們 (透過以[文字分析 API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api) 分析他們所撰寫的內容)，還可以利用[電腦視覺 API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) 偵測到不想要或成人的內容，並採取適當的動作。 「認知服務」包括許多現成的解決方案，您無須具備任何類型的「機器學習服務」知識也能使用。

## <a name="a-planet-scale-social-experience"></a>全球規模的社交體驗

最後，還有一項重要的主題必須和各位分享，那就是「延展性」****。 在設計架構時，每個元件都應該自行調整。 您最終會需要處理更多資料，或者，會想要有更廣泛的地理涵蓋範圍。 幸好，透過 Cosmos DB，您便能**輕鬆完成**這兩項工作。

Cosmos DB 預設支援動態分割。 它會根據特定**資料分割索引鍵** (定義為您文件中的其中一項屬性) 自動建立資料分割。 定義正確的分割區索引鍵必須在設計階段完成。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的資料分割](partitioning-overview.md)。

針對社交體驗，您的分割策略必須符合您查詢及寫入的方式。 （例如，需要在相同分割區內進行讀取，並藉由將寫入分散到多個磁碟分割來避免「作用點」）。部分選項包括：根據時態索引鍵（日/月/周）、依內容分類、依地理區域或依使用者劃分的資料分割。 這完全視您查詢資料及將它顯示於社交體驗的方式而定。

Cosmos DB 會透明地在所有資料分割上執行您的查詢 (包括[彙總](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (英文))，因此您不需要隨資料增加而新增任何邏輯。

流量最終會隨時間增長，而您的資源消耗 (以 [RU](request-units.md) (要求單位) 為單位) 也會增加。 隨著使用者群的增長，讀取及寫入也會變得更頻繁。 使用者群將會開始建立及讀取更多內容。 因此**調整輸送量**的能力極為重要。 增加 RU 非常容易。 只要在 Azure 入口網站上按幾下，或是[透過 API 發出命令](/rest/api/cosmos-db/replace-an-offer)即可。

:::image type="content" source="./media/social-media-apps/social-media-apps-scaling.png" alt-text="相應增加及定義分割區索引鍵":::

如果情況持續好轉會怎樣？ 假設有來自其他地區、國家或洲的使用者注意到您的平台，並開始使用它。 這還真是個好消息！

但別著急！ 您很快就發現他們無法從您的平台取得最佳的體驗。 因為他們離您的作業區域太遠，使延遲變得非常嚴重。 但您當然也不希望他們因此而放棄使用。 要是有方法能輕鬆**觸達全球使用者**就好了。 當然有！

Cosmos DB 可讓您按幾下就能透明地[將資料複寫至全球](../cosmos-db/tutorial-global-distribution-sql-api.md)，並且自動從您的[用戶端程式碼](../cosmos-db/tutorial-global-distribution-sql-api.md)選取可用的區域。 這個程序也表示您可以擁有[多個容錯移轉區域](high-availability.md)。

當您將資料複寫至全球時，您必須確保您的用戶端能充分利用它。 如果您使用 Web 前端，或是從行動用戶端存取 API，便可以部署 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)，然後將您的 Azure App Service 複製到所有需要的區域，其中使用效能設定來支援延伸的全球涵蓋範圍。 當用戶端存取您的前端或 API 時，系統會將它們路由至最接近的 App Service，以便連接到當地的 Cosmos DB 複本。

:::image type="content" source="./media/social-media-apps/social-media-apps-global-replicate.png" alt-text="為您的社交平台加入全球涵蓋範圍" border="false":::

## <a name="conclusion"></a>結論

本文會探討如何以低成本的服務在 Azure 上完整建立社交網路的替代方案。 其會藉由鼓勵使用多層次儲存體解決方案和稱為「階梯」的資料分散方式來提供結果。

:::image type="content" source="./media/social-media-apps/social-media-apps-azure-solution.png" alt-text="Azure 服務之間社交網路互動的圖表" border="false":::

事實上，這類案例並沒有萬靈丹。 這是結合絕佳服務所建立的協力，可讓我們打造絕佳的體驗： Azure Cosmos DB 提供絕佳的社交應用程式、第一類搜尋解決方案（例如 Azure 認知搜尋）背後的情報、Azure App 服務的彈性，甚至不受語言限制的應用程式，還有強大的背景流程，以及可擴充的 Azure 儲存體和 Azure SQL Database 來儲存大量資料和 Azure 機器的分析能力學習建立可提供意見反應給您的流程，並協助我們將正確的內容傳遞給正確使用者的知識和智慧。

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB 的使用案例，請參閱[常見 Cosmos DB 使用案例](use-cases.md)。
