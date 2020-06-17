---
title: 在 Azure 入口網站中建立知識存放區 (預覽)
titleSuffix: Azure Cognitive Search
description: 使用「匯入資料」精靈建立用來保存擴充內容的知識存放區。 連線至知識存放區以進行其他應用程式的分析，或將擴充的內容傳送到下游程序。 此功能目前為公開預覽狀態。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/07/2020
ms.openlocfilehash: 06ddb50173299bbb99518921d1589d2f15b0aa88
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488676"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Azure 認知搜尋知識存放區

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱[補充使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

知識存放區是 Azure 認知搜尋的一個功能，可保存內容處理管線的輸出，以進行後續的分析或其他下游處理。 

管線會接受非結構化文字與影像內容、套用由認知服務 (例如 OCR 與自然語言處理) 提供的 AI，並輸出先前不存在的新結構與資訊。 管線所建立的其中一個實體成品是[知識存放區](knowledge-store-concept-intro.md)，您可以透過工具存取加以存取，以分析和探索內容。

在本快速入門中，您將結合 Azure 雲端中的服務和資料以建立知識存放區。 一切都備妥之後，您即可在入口網站中執行**匯入資料**精靈，以將其全部提取。 最終結果是原始文字內容和 AI 產生的內容，而您可以在入口網站 ([儲存體總管](knowledge-store-view-storage-explorer.md)) 中加以檢視。

## <a name="prerequisites"></a>必要條件

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

+ Azure 認知搜尋服務。 在目前的訂用帳戶下，[建立服務](search-create-service-portal.md)或[尋找現有的服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

+ 具有 [Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/)的 Azure 儲存體帳戶。

> [!NOTE]
> 此快速入門也會為 AI 使用 [Azure 認知服務](https://azure.microsoft.com/services/cognitive-services/)。 由於工作負載很小，因此認知服務會在幕後連線以進行免費處理，最多 20 筆交易。 這表示您可以完成此練習，而不必建立其他認知服務資源。

## <a name="set-up-your-data"></a>設定您的資料

在下列步驟中，於 Azure 儲存體中設定 Blob 容器，以儲存異質內容檔案。

1. [下載 HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 這項資料是儲存在 CSV 檔案中的飯店評論資料 (源自於 Kaggle.com)，其中包含 19 個關於單一飯店的客戶意見反應。 

1. [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)，或在您目前的訂用帳戶下方[尋找現有帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 您會將 Azure 儲存體同時用於要匯入的原始內容，以及作為最終結果的知識存放區。

   + 選擇 **StorageV2 (一般用途 V2)** 帳戶類型。

1. 開啟 Blob 服務頁面，並建立名為 *hotel-reviews* 的容器。

1. 按一下 [上傳] 。

    ![上傳資料](media/knowledge-store-create-portal/upload-command-bar.png "上傳飯店評論")

1. 選取您在第一個步驟中下載的 **HotelReviews-Free.csv** 檔案。

    ![建立 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "建立 Azure Blob 容器")

1. 在您結束 Blob 儲存體頁面之前，請使用左側瀏覽窗格上的連結，開啟 [存取金鑰] 頁面。 取得連接字串以從 Blob 儲存體中擷取資料。 連接字串會如下列範例所示：`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

現在您已準備就緒，可移至 [匯入資料] 精靈。

## <a name="run-the-import-data-wizard"></a>執行匯入資料精靈

1. 使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

1. [尋找您的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)，然後在 [概觀] 頁面上，按一下命令列上的 [匯入資料]，以四個步驟建立知識存放區。

   ![匯入資料命令](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>步驟 1:建立資料來源

1. 在 [連線到您的資料] 中，選擇 [Azure Blob 儲存體]，選取您建立的帳戶和容器。 

1. 針對 [名稱] 輸入 `hotel-reviews-ds`。

1. 針對 [剖析模式]，選取 [分隔的文字]，然後選取 [第一行包含標頭] 核取方塊。 請確定 [分隔符號字元] 是逗號 (,)。

1. 在 [連接字串] 中，貼上您從 Azure 儲存體中的 [存取金鑰] 頁面複製的連接字串。

1. 在 [容器] 中，輸入保存資料的 Blob 容器名稱。

    您的頁面應該會看起來如下列螢幕擷取畫面所示。

    ![建立資料來源物件](media/knowledge-store-create-portal/hotel-reviews-ds.png "建立資料來源物件")

1. 繼續進行下一頁。

### <a name="step-2-add-cognitive-skills"></a>步驟 2:新增認知技能

在此精靈步驟中，您將建立具有認知技能擴充的技能集。 來源資料是由數種語言的客戶評論所組成。 與此資料集相關的技能包括關鍵片語擷取、情感偵測和文字翻譯。 在後續步驟中，這些擴充將「投射」到知識存放區中作為 Azure 資料表。

1. 展開 [連結認知服務]。 預設會選取 [免費 (有限的擴充)]。 您可以使用此資源，因為 HotelReviews-Free.csv 中的記錄筆數是 19，而此免費資源一天最多允許 20 筆交易。

1. 展開 [新增擴充]。

1. 針對 [技能集名稱] 輸入 `hotel-reviews-ss`。

1. 針對 [來源資料欄位]，選取 **reviews_text**。

1. 針對 [擴充細微性層級]，選取 [頁面 (5000 個字元區塊)]。

1. 選取這些認知技能：
    + **擷取關鍵片語**
    + **翻譯文字**
    + **偵測情感**

      ![建立技能集](media/knowledge-store-create-portal/hotel-reviews-ss.png "建立技能集")

1. 展開 [將擴充儲存到知識存放區]。

1. 選取下列 [Azure 資料表投影]：
    + **文件**
    + **頁面**
    + **關鍵片語**

1. 輸入您在上一個步驟中儲存的 [儲存體帳戶連接字串]。

    ![設定知識存放區](media/knowledge-store-create-portal/hotel-reviews-ks.png "設定知識存放區")

1. (選擇性) 下載 Power BI 範本。 當您從精靈存取範本時，本機 .pbit 檔案會進行調整以反映您的資料圖形。

1. 繼續進行下一頁。

### <a name="step-3-configure-the-index"></a>步驟 3：設定索引

在此精靈步驟中，您會設定用於選擇性全文檢索搜尋查詢的索引。 此精靈會取樣您的資料來源，以推斷欄位和資料類型。 您只需要選取所需行為的屬性。 例如，[可擷取] 屬性會允許搜尋服務傳回欄位值，而 [可搜尋] 會啟用欄位的全文檢索搜尋。

1. 針對 [索引名稱] 輸入 `hotel-reviews-idx`。

1. 接受屬性的預設選取項目：[可擷取] 和 [可搜尋] 適用於管線所建立的新欄位。

    您的索引應該會看起來如下圖。 因為此清單很長，所以並非所有欄位都會顯示在影像中。

    ![設定索引](media/knowledge-store-create-portal/hotel-reviews-idx.png "設定索引")

1. 繼續進行下一頁。

### <a name="step-4-configure-the-indexer"></a>步驟 4：設定索引子

在此步驟中，您將設定索引子，以便將資料來源、技能集，以及您在先前的精靈步驟中定義的索引整合在一起。

1. 針對 [名稱] 輸入 `hotel-reviews-idxr`。

1. 針對 [排程]，保留預設值 [一次]。

1. 按一下 [提交] 以執行索引子。 資料擷取、編製索引、認知技能的應用全都在此步驟進行。

## <a name="monitor-status"></a>監視狀態

認知技能的索引編製需要比一般文字的索引編製更長的時間來完成。 精靈應該會在 [概觀] 頁面中開啟索引子清單，方便您追蹤進度。 若要自行瀏覽，請移至 [概觀] 頁面，然後按一下 [索引子]。

在 Azure 入口網站中，您也可以監視通知活動記錄中是否有可點選的 **Azure 認知搜尋通知**狀態連結。 執行作業可能需要幾分鐘的時間才能完成。

## <a name="next-steps"></a>後續步驟

現在您已使用認知服務擴充資料，並將結果投射到知識存放區，接下來您可以使用儲存體總管或 Power BI 來探索擴充的資料集。

您可以在儲存體總管中檢視內容，或使用 Power BI 透過視覺效果取得深入解析。

> [!div class="nextstepaction"]
> [使用儲存體總管檢視](knowledge-store-view-storage-explorer.md)
> [使用 Power BI 連接](knowledge-store-connect-power-bi.md)

> [!Tip]
> 如果您想要重複此練習，或嘗試不同的 AI 擴充逐步解說，請刪除 hotel-reviews-idxr 索引子。 刪除索引子，會將認知服務處理的免費每日交易計數器重設回零。
