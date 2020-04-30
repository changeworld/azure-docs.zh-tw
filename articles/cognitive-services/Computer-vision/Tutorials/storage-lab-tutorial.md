---
title: 教學課程：產生 Azure 映像的中繼資料
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將了解如何將 Azure 電腦視覺服務整合到 Web 應用程式中，以產生影像的中繼資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 43172cb08bb1e31c8cff891628ca6ef85cb8c864
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404419"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>教學課程：使用電腦視覺在 Azure 儲存體中產生影像中繼資料

在本教學課程中，您將了解如何將 Azure 電腦視覺服務整合到 Web 應用程式中，為上傳的影像產生中繼資料。 這適用於[數位資產管理 (DAM)](../Home.md#computer-vision-for-digital-asset-management) 案例，例如公司想要快速為其所有影像產生描述性標題或可搜尋的關鍵字。

您可以在 GitHub 上的 [Azure 儲存體和認知服務實驗室](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md)中找到完整的應用程式指南；本教學課程主要將說明實驗室的練習 5。 您可以遵循每個步驟來建立完整的應用程式，但如果您只想了解如何將電腦視覺整合到現有 Web 應用程式，請閱讀這裡。

本教學課程說明如何：

> [!div class="checklist"]
> * 在 Azure 中建立電腦視覺資源
> * 對 Azure 儲存體影像執行影像分析
> * 將中繼資料連結至 Azure 儲存體影像
> * 使用 Azure 儲存體總管查看影像中繼資料

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 

## <a name="prerequisites"></a>Prerequisites

- 已安裝「ASP.NET 與網頁程式開發」與「Azure 開發」工作負載的 [Visual Studio 2017 Community 版本](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)或更新版本。
- 已為影像儲存體設定 Blob 容器的 Azure 儲存體帳戶 (如需此步驟的相關說明，請遵循 [Azure 儲存體實驗室的練習 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1))。
- Azure 儲存體總管工具 (如需此步驟的相關說明，請遵循 [Azure 儲存體實驗室的練習 2](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2))。
- 可存取 Azure 儲存體的 ASP.NET Web 應用程式 (若要快速建立這類應用程式，請遵循 [Azure 儲存體實驗室的練習 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3))。

## <a name="create-a-computer-vision-resource"></a>建立電腦視覺資源

您必須為您的 Azure 帳戶建立電腦視覺資源；這項資源會管理您對 Azure 電腦視覺服務的存取。 

1. 請遵循[建立 Azure 認知服務資源](../../cognitive-services-apis-create-account.md)中的指示來建立電腦視覺資源。

1. 接著，移至您資源群組的功能表，然後按一下您剛建立的電腦視覺 API 訂用帳戶。 將 [端點]  下方的 URL 複製到您可輕鬆快速加以擷取之處。 然後，按一下 [顯示存取金鑰]  。

    ![Azure 入口網站頁面：已框出端點 URL 和存取金鑰連結](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. 在下一個視窗中，將 [金鑰 1]  的值複製到剪貼簿。

    ![[管理金鑰] 對話方塊：已框出 [複製] 按鈕](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>新增電腦視覺認證

接著，您會將必要的認證新增至您的應用程式，使其可存取電腦視覺資源

在 Visual Studio 中開啟您的 ASP.NET Web 應用程式，並瀏覽至位於專案根目錄的 **Web.config** 檔案。 在檔案的 `<appSettings>` 區段中新增下列陳述式，並將 `VISION_KEY` 取代為您在上一個步驟中複製的金鑰，同時將 `VISION_ENDPOINT` 取代為您在之前的步驟中儲存的 URL。

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

然後，在方案總管中以滑鼠右鍵按一下專案，並使用**管理 NuGet 套件**命令來安裝套件 **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**。 此套件包含呼叫電腦視覺 API 所需的類型。

## <a name="add-metadata-generation-code"></a>新增中繼資料產生程式碼

接著，您會新增實際運用電腦視覺服務來建立影像中繼資料的程式碼。 這些步驟將套用至實驗室中的 ASP.NET 應用程式，但您可以將其調整並套用至自己的應用程式。 重要的是，此時您已有 ASP.NET Web 應用程式可將影像上傳至 Azure 儲存體容器、從中讀取影像，並將其顯示在檢視中。 如果您對此步驟沒有把握，建議您遵循 [Azure 儲存體實驗室的練習 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3)。 

1. 在專案的 **Controllers** 資料夾中開啟 *HomeController.cs* 檔案，並在檔案頂端新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. 然後，移至 **Upload** 方法；此方法會將影像轉換並上傳至 Blob 儲存體。 在以 `// Generate a thumbnail` 開頭的區塊後面緊接著新增下列程式碼 (或是在影像 Blob 建立程序的結尾處新增)。 此程式碼會取用包含影像的 Blob (`photo`)，並使用電腦視覺產生該影像的描述。 電腦視覺 API 也會產生套用至影像的關鍵字清單。 產生的描述和關鍵字會儲存在 Blob 的中繼資料中，以便稍後擷取。

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. 接下來，移至相同檔案中的 **Index** 方法。 此方法會列舉目標 Blob 容器中已儲存的影像 Blob (作為 **IListBlobItem** 執行個體)，並將其傳至應用程式檢視。 請將此方法中的 `foreach` 區塊取代為下列程式碼。 此程式碼會呼叫 **CloudBlockBlob.FetchAttributes**，以取得每個 Blob 連結的中繼資料。 它會從中繼資料擷取電腦產生的描述 (`caption`)，並將其新增至 **BlobInfo** 物件，繼而傳至檢視。
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>測試應用程式

在 Visual Studio 中儲存您的變更，然後按 **Ctrl+F5** 在瀏覽器中啟動應用程式。 使用應用程式，從實驗室資源中的 "photos" 資料夾或是從您自己的資料夾上傳一些影像。 當您將游標停留在檢視中的一個影像上方時，應該會出現工具提示視窗，並顯示電腦為影像產生的標題。

![電腦產生的標題](../Images/thumbnail-with-tooltip.png)

若要檢視所有連結的中繼資料，請使用 Azure 儲存體總管檢視您為影像使用的儲存體容器。 以滑鼠右鍵按一下容器中的任何 Blob，然後選取 [屬性]  。 在對話方塊中，您會看到索引鍵/值組清單。 電腦產生的影像描述會儲存在「標題」項目中，而搜尋關鍵字會儲存於 "Tag0"、"Tag1"，依此類推。 完成作業後，請按一下 [取消]  以關閉對話方塊。

![影像屬性對話方塊視窗：已列出中繼資料標記](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續使用 Web 應用程式，請參閱[後續步驟](#next-steps)一節。 如果您不打算繼續使用此應用程式，則應刪除應用程式專屬的所有資源。 若要刪除資源，您可以刪除包含 Azure 儲存體訂用帳戶和電腦視覺資源的資源群組。 這將會移除儲存體帳戶、上傳至該帳戶的 Blob，以及與 ASP.NET Web 應用程式連線所需的 App Service 資源。 

若要刪除資源群組，請在入口網站中開啟 [資源群組]  索引標籤，並瀏覽至您用於此專案的資源群組，然後按一下檢視頂端的 [刪除資源群組]  。 系統會要求您輸入資源群組的名稱以確認您要加以刪除，因為資源群組一經刪除即無法復原。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在現有 Web 應用程式中設定 Azure 的電腦視覺服務，以在 Blob 影像上傳時自動為其產生標題和關鍵字。 接著，請參閱 Azure 儲存體實驗室的練習 6，以了解如何為 Web 應用程式新增搜尋功能。 此功能會利用電腦視覺服務所產生的搜尋關鍵字。

> [!div class="nextstepaction"]
> [將搜尋新增至應用程式](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
