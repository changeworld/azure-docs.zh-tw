---
title: 透過媒體服務 v3 分析影片
titleSuffix: Azure Media Services
description: 了解如何使用 Azure 媒體服務分析影片。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 02/02/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: abd4a3a3a3e8494ea325e65a78eea7fb56b78f94
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988357"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>教學課程：透過媒體服務 v3 分析影片

> [!NOTE]
> 雖然本教學課程使用 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 範例，但是 [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支援 [SDK](media-services-apis-overview.md#sdks) 的一般步驟都相同。

本教學課程會示範如何使用 Azure 媒體服務分析影片。 在許多情況下，您會想從記錄影片或音訊內容中取得深入的見解。 例如，為達到更高的客戶滿意度，組織可以執行語音轉文字處理，將客戶支援記錄轉換為可搜尋的目錄，且其中可包含索引及儀表板。 然後，他們可以取得其業務的深入解析。 這些深入解析包括常見的抱怨清單、這類抱怨的來源和其他實用資訊等。

本教學課程說明如何：

> [!div class="checklist"]
> * 下載本主題中說明的範例應用程式。
> * 檢查用來分析指定影片的程式碼。
> * 執行應用程式。
> * 檢查輸出。
> * 清除資源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>合規性、隱私權和安全性
 
重要提醒是，在使用影片索引子時，您必須遵守所有適用的法律，且您不得以違反他人權利或可能會對他人有害的方式使用影片索引子或任何其他 Azure 服務。 將任何影片 (包括任何生物特徵辨識資料) 上傳至影片索引子服務以進行處理和儲存之前，您必須擁有所有適當的權限，包括向影片中的個人徵得所有必要的同意。 若要了解影片索引子中的合規性、隱私權和安全性，請參閱 Microsoft [認知服務條款](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)。 如需 Microsoft 的隱私權義務和您的資料處理方式，請參閱 Microsoft 的 [隱私權聲明](https://privacy.microsoft.com/PrivacyStatement)、[線上服務條款](https://www.microsoft.com/licensing/product-licensing/products) ("OST") 和 [資料處理增補](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")。 其他隱私權資訊 (包括資料保留、刪除/銷毀) 可在 OST 中和[這裡](../video-indexer/faq.md)取得。 一旦使用影片索引子，即表示您同意受到認知服務條款、OST、DPA 和隱私權聲明的規範。

## <a name="prerequisites"></a>Prerequisites

- 如果您未安裝 Visual Studio，請取得 [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)。
- [建立媒體服務帳戶](create-account-cli-how-to.md)。<br/>請務必記住您用於資源群組名稱和「媒體服務」帳戶名稱的值。
- 請依照[使用 Azure CLI 存取 Azure 媒體服務 API](access-api-cli-how-to.md) 中的步驟，並儲存認證。 您必須使用這些認證來存取 API。

## <a name="download-and-configure-the-sample"></a>下載並設定範例

使用以下命令將包含 .NET 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

此範例位於 [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) 資料夾中。

在您下載的專案中開啟 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json)。 將值取代為您從[存取 API](access-api-cli-how-to.md) 中取得的認證。

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>檢查用來分析指定視訊的程式碼

本節將針對 AnalyzeVideos  專案的 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) 檔案，檢查其中定義的函式。

此範例會完成下列動作：

1. 建立用來分析視訊的**轉換**和**作業**。
2. 建立輸入**資產**，並將視訊上傳到其中。 此資產會作為作業的輸入。
3. 建立輸出資產，用以儲存作業的輸出。
4. 提交作業。
5. 檢查作業的狀態。
6. 下載執行作業所產生的檔案。

> [!NOTE]
> 使用視訊或音訊分析程式的預設時，可使用 Azure 入口網站將帳戶設定為擁有 10 個 S3 編碼保留單元。 如需詳細資訊，請參閱[調整媒體處理](media-reserved-units-cli-how-to.md)。

### <a name="start-using-media-services-apis-with-net-sdk"></a>開始搭配使用媒體服務 API 與 .NET SDK

若要開始搭配使用媒體服務 API 與 .NET，您需要建立 **AzureMediaServicesClient** 物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在您於本文一開始複製的程式碼中，**GetCredentialsAsync** 函式會根據本機組態檔中提供的認證建立 ServiceClientCredentials 物件。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>建立輸入資產並將本機檔案上傳到其中 

**CreateInputAsset** 函式會建立新的輸入[資產](https://docs.microsoft.com/rest/api/media/assets)，並將指定的本機影片檔案上傳到其中。 這項資產會用來作為編碼作業的輸入。 在媒體服務 v3 中，作業的輸入可以是資產，或是您透過 HTTPS URL 讓媒體服務帳戶可存取的內容。 若要了解如何從 HTTPS URL 進行編碼，請參閱[這篇](job-input-from-http-how-to.md)文章。  

在媒體服務 v3 中，您會使用 Azure 儲存體 API 來上傳檔案。 下列 .NET 程式碼片段將說明執行方式。

下列函式會完成下列動作：

* 建立資產。
* 取得可寫入的 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)，以存取[儲存體中的資產容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)。
* 使用 SAS URL，將檔案上傳至儲存體中的容器。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>建立要儲存作業結果的輸出資產

輸出[資產](https://docs.microsoft.com/rest/api/media/assets)會儲存您的作業結果。 專案會定義 **DownloadResults** 函式，以將結果從此輸出資產下載至「輸出」資料夾，因此您可以看到結果。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>建立分析影片的轉換和作業

在媒體服務中編碼或處理內容時，將編碼設定設為配方 (recipe) 是很常見的模式。 然後您可以透過提交**作業**，將該配方套用到影片。 藉由為每部新影片提交新的作業，您可以將該配方套用到媒體櫃中的所有影片。 配方在媒體服務中稱為**轉換**。 如需詳細資訊，請參閱[轉換和作業](transform-concept.md)。 本教學課程中所述的範例會定義用來分析指定影片的配方。

#### <a name="transform"></a>轉換

建立新的[轉換](https://docs.microsoft.com/rest/api/media/transforms)執行個體時，您需要指定想要其產生的輸出是什麼。 **TransformOutput** 是必要參數。 每個 **TransformOutput** 都會包含 **Preset (預設)** 。 **Preset** 會描述影片和/或音訊處理作業的逐步指示，以產生所需的 **TransformOutput**。 此範例中會使用 **VideoAnalyzerPreset** 預設，以及將語言 ("en-US") 傳遞至其建構函式 (`new VideoAnalyzerPreset("en-US")`)。 此預設可讓您從影片擷取多個音訊和影片見解。 如果您需要從影片擷取多個音訊見解，可以使用 **AudioAnalyzerPreset** 預設。

建立**轉換**時，請先使用 **Get** 方法檢查是否已有轉換存在，如下列程式碼所示。 在媒體服務 v3 中，如果實體不存在，對實體執行的 **Get** 方法會傳回 **null** (檢查名稱時不區分大小寫)。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>工作 (Job)

如同前面所述，[轉換](https://docs.microsoft.com/rest/api/media/transforms)物件是配方，而[作業](https://docs.microsoft.com/rest/api/media/jobs)則是實際要求媒體服務，將**轉換**套用至指定的輸入影片或音訊內容。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用下列項目指定影片的位置：HTTPS URL、SAS URL 或媒體服務帳戶的資產。

在此範例中，工作輸入是本機影片。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>請等待作業完成

此作業需要一些時間才能完成。 作業完成時，您會想要收到通知。 取得[作業](https://docs.microsoft.com/rest/api/media/jobs)完成通知的選項有很多種。 最簡單的選項 (如此處所示) 是使用輪詢。

對生產應用程式而言，輪詢不是建議的最佳做法，因為可能會發生延遲。 如果過度使用帳戶，輪詢可能會進行節流處理。 開發人員應改為使用事件方格。

事件方格專為高可用性、一致效能及動態調整而設計。 透過事件方格，您的應用程式幾乎可以從所有 Azure 服務和自訂來源接聽及回應事件。 以 HTTP 為基礎的簡單回應式事件處理，可協助您透過智慧型事件篩選和路由來建置有效率的解決方案。 如需詳細資訊，請參閱[將事件路由至自訂 Web 端點](job-state-events-cli-how-to.md)。

**作業**通常會經歷下列狀態：**已排程**、**已排入佇列**、**正在處理**、**已完成** (最後一個狀態)。 如果作業發生錯誤，您會收到**錯誤**狀態。 正在取消作業時會顯示**正在取消**的狀態，而完成時則會顯示**已取消**狀態。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>作業錯誤碼

請參閱[錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode) \(英文\)。

### <a name="download-the-result-of-the-job"></a>下載作業結果

下列函式會將結果從輸出[資產](https://docs.microsoft.com/rest/api/media/assets)下載至「輸出」資料夾，讓您可以查看作業的結果。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>清除媒體服務帳戶中的資源

一般而言，您應清除所有項目，而只保留您預計要重複使用的物件 (您通常會重複使用轉換並且保存 StreamingLocators)。 如果您想要在實驗之後有乾淨的帳戶，請刪除您不打算重複使用的資源。 例如，下列程式碼會刪除作業：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>執行範例應用程式

按 Ctrl+F5 執行 *AnalyzeVideos* 應用程式。

當我們執行程式時，此作業會為影片中找到的每個臉部製作縮圖。 並且也會產生 insights.json 檔案。

## <a name="examine-the-output"></a>檢查輸出

分析影片的輸出檔稱為 insights.json。 此檔案包含影片的見解。 您可以在[媒體智慧](intelligence-concept.md)一文中，為 json 檔案中發現的元素找到說明。

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包含您為教學課程建立的媒體服務和儲存體帳戶)，請將稍早建立的資源群組刪除。

執行下列 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>多執行緒

Azure 媒體服務 v3 SDK 不是安全執行緒。 當使用多執行緒應用程式時，您應該為每個執行緒產生新的 AzureMediaServicesClient 物件。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：上傳、編碼和串流檔案](stream-files-tutorial-with-api.md)
