---
title: 範例：在 C# 中探索影像處理應用程式
titleSuffix: Azure Cognitive Services
description: 探索使用「Azure 認知服務」中「電腦視覺 API」的基本 Windows 應用程式。 在影像中執行 OCR、建立縮圖，以及處理視覺特徵。
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 522cf801e7c1bfdd3ed1f452c123a2db701c0c42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936345"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>範例：使用 C# 探索影像處理應用程式

探索使用「電腦視覺」在影像中執行光學字元辨識 (OCR)、建立智慧型裁剪縮圖以及偵測、分類、標記和描述視覺特徵 (包括臉部) 的基本 Windows 應用程式。 以下範例可讓您提交影像 URL 或儲存在本機的檔案。 您可以將這個開放原始碼範例當作一個範本，來利用「電腦視覺 API」和 Windows Presentation Foundation (WPF，.NET Framework 的組件) 組建自己的 Windows 應用程式。

> [!div class="checklist"]
> * 從 GitHub 取得範例應用程式
> * 請在 Visual Studio 中開啟並建立範例應用程式
> * 執行範例應用程式並與之互動，以執行各種案例
> * 探索範例應用程式隨附的各種案例

## <a name="prerequisites"></a>必要條件

探索範例應用程式之前，請確認您已符合下列必要條件：

* 您必須有 [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) 或更新版本。
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="建立電腦視覺資源"  target="_blank">建立電腦視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]****。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 電腦視覺服務。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="get-the-sample-app"></a>取得範例應用程式

電腦視覺範例應用程式可從 `Microsoft/Cognitive-Vision-Windows` 存放庫的 GitHub 取得。 此存放庫也包含作為 Git 子模組的 `Microsoft/Cognitive-Common-Windows` 存放庫。 您可以使用命令列中的 `git clone --recurse-submodules` 命令或是使用 GitHub Desktop 來遞迴複製此存放庫 (包括子模組)。

例如，若要從命令提示字元來遞迴複製電腦視覺範例應用程式的存放庫，請執行下列命令：

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> 請勿以 ZIP 格式下載此存放庫。 以 ZIP 格式下載存放庫時，Git 不會包含子模組。

### <a name="get-optional-sample-images"></a>取得選用的範例影像

您可以選擇性使用[臉部](../../Face/Overview.md)範例應用程式隨附的範例影像，可從 `Microsoft/Cognitive-Face-Windows` 存放庫的 GitHub 取得。 該範例應用程式包含資料夾 `/Data`，內含多張人物影像。 您也可以利用針對電腦視覺範例應用程式所敘述的方法，遞迴複製此存放庫。

例如，若要從命令提示字元來遞迴複製臉部範例應用程式的存放庫，請執行下列命令：

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>請在 Visual Studio 中開啟並建立範例應用程式

在您執行或探索範例應用程式前，必須先建立範例應用程式，如此一來 Visual Studio 就能解決相依性問題。 若要開啟和建立範例應用程式，請執行下列步驟：

1. 在 Visual Studio 中開啟 Visual Studio 解決方案檔案 `/Sample-WPF/VisionAPI-WPF-Samples.sln`。
1. 確認 Visual Studio 解決方案包含兩個專案：  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   如果無法使用 SampleUserControlLibrary 專案，請確認您已經遞迴複製 `Microsoft/Cognitive-Vision-Windows` 存放庫。
1. 在 Visual Studio 中，請按 Ctrl+Shift+B，或選擇功能區功能表中的 [建置]****，然後選擇 [建置方案]**** 來建置該方案。

## <a name="run-and-interact-with-the-sample-app"></a>執行範例應用程式並與之互動

您可以執行範例應用程式來了解執行各種工作時 (例如產生縮圖或標記影像)，此應用程式如何與您和電腦視覺用戶端程式庫互動。 若要執行範例應用程式並與之互動，請執行下列步驟：

1. 建置完成後，按下 **F5** 或選擇功能區功能表中的 [偵錯]****，然後選擇 [開始偵錯]**** 來執行範例應用程式。
1. 顯示範例應用程式時，請選擇瀏覽窗格中的 [訂用帳戶金鑰管理]****，以顯示訂用帳戶金鑰管理頁面。
   ![訂用帳戶金鑰管理頁面](../Images/Vision_UI_Subscription.PNG)  
1. 在 [訂用帳戶金鑰]**** 中輸入您的訂用帳戶金鑰。
1. 在 [端點]**** 中輸入端點 URL。  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 如果下次執行範例應用程式時，不想再輸入您的訂用帳戶金鑰和端點 URL，請選擇 [儲存設定]****，將訂用帳戶金鑰和端點 URL 儲存到您的電腦上。 如果您想刪除先前儲存的訂用帳戶金鑰和端點 URL，請選擇 [刪除設定]****。

   > [!NOTE]
   > 範例應用程式會使用隔離儲存區以及 `System.IO.IsolatedStorage` 來儲存您的訂用帳戶金鑰和端點 URL。

1. 在瀏覽窗格中的 [選取案例]**** 底下，選取範例應用程式目前隨附的其中一個案例：  

   | 狀況 | 描述 |
   |----------|-------------|
   |分析影像 | 使用[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)作業來分析本機或遠端影像。 您可以選擇分析視覺功能和語言，並查看影像及結果。  |
   |使用領域模型分析影像 | 使用[列出特定領域模型](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) (英文) 作業可列出您可以選取的領域模型，使用[辨識特定領域內容](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (英文) 作業則能以選取的領域模型來分析本機或遠端影像。 您也可以選擇要分析的語言。 |
   |描述影像 | 使用[描述影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) (英文) 作業可建立一般人能理解的本機或遠端影像描述。 您也可以選擇要描述的語言。 |
   |產生標籤 | 使用[標記影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) (英文) 作業可標記本機或遠端影像的視覺功能。 您也可以選擇標籤所用的語言。 |
   |辨識文字 (OCR) | 使用 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) (英文) 作業可辨識及擷取影像中的列印文字。 您可以選擇要使用的語言，或是由電腦視覺自動偵測語言。 |
   |辨識文字 V2 (英文) | 使用[辨識文字](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) (英文) 和[取得辨識文字作業結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) (英文) 作業，能以非同步方式辨識及擷取影像中的列印或手寫文字。 |
   |取得縮圖 | 使用[取得縮圖](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (英文) 作業可產生本機或遠端影像的縮圖。 |

   下列螢幕擷取畫面顯示的是在分析範例影像之後，針對分析影像案例提供的頁面。
   ![分析影像頁面的螢幕擷取畫面](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>探索範例應用程式

電腦視覺範例應用程式的 Visual Studio 解決方案包含兩個專案：

* SampleUserControlLibrary  
  SampleUserControlLibrary 專案提供多個認知服務範例可共用的功能。 此專案包含下列內容：
  * SampleScenarios  
    提供標準化簡報的 UserControl，例如標題列、瀏覽窗格以及內容窗格。 電腦視覺範例應用程式會使用 [MainWindow.xaml] 視窗中的這個控制項來顯示案例頁面，也會用於存取多個案例共用的資訊，例如訂用帳戶金鑰和端點 URL。
  * SubscriptionKeyPage  
    提供標準化版面配置的頁面，用來輸入範例應用程式的訂用帳戶金鑰和端點 URL。 電腦視覺範例應用程式會使用此頁面來管理案例頁面所使用的訂用帳戶金鑰和端點 URL。
  * VideoResultControl  
    提供影片資訊標準化簡報的 UserControl。 電腦視覺範例應用程式不會使用此控制項。
* VisionAPI-WPF-Samples  
  電腦視覺範例應用程式的主要專案，內有電腦視覺的所有精彩功能。 此專案包含下列內容：
  * AnalyzeInDomainPage.xaml  
    使用領域模型案例的分析影像案例頁面。
  * AnalyzeImage.xaml  
    分析影像案例的案例頁面。
  * DescribePage.xaml  
    描述影像案例的案例頁面。
  * ImageScenarioPage.cs  
    ImageScenarioPage 類別，從其中衍生範例應用程式中所有的案例頁面。 此類別會管理功能，例如提供認證和格式化輸出，並且由所有案例頁面共用。
  * MainWindow.xaml  
    範例應用程式的主視窗，會使用 SampleScenarios 控制項來呈現 SubscriptionKeyPage 和案例頁面。
  * OCRPage.xaml  
    辨識文字 (OCR) 案例的案例頁面。
  * RecognizeLanguage.cs  
    RecognizeLanguage 類別，可提供範例應用程式中各種方法所支援的語言相關資訊。
  * TagsPage.xaml  
    產生標籤案例的案例頁面。
  * TextRecognitionPage.xaml  
    辨識文字 V2 (英文) 案例的案例頁面。
  * ThumbnailPage.xaml  
    取得縮圖案例的案例頁面。

### <a name="explore-the-sample-code"></a>探討範例程式碼

範例程式碼的主要部分會以開頭為 `KEY SAMPLE CODE STARTS HERE` 而結尾為 `KEY SAMPLE CODE ENDS HERE` 的註解區塊框出，方便您研究範例應用程式。 範例程式碼的這些主要部分包含的程式碼，與學習使用電腦視覺 API 用戶端程式庫來執行各種工作最為相關。 您可以在 Visual Studio 中搜尋 `KEY SAMPLE CODE STARTS HERE`，即可在電腦視覺範例應用程式中最相關的程式碼部分之間移動。 

例如，以下所示的 `UploadAndAnalyzeImageAsync` 方法已納入 AnalyzePage.xaml 之中，示範了如何透過叫用 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法以用戶端程式庫來分析本機影像。

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>探索用戶端程式庫

此範例應用程式會利用電腦視覺 API 用戶端程式庫，這是適用於 Azure 認知服務中電腦視覺 API 的精簡版 C# 用戶端包裝函式。 [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) (英文) 套件中的 NuGet可提供此用戶端程式庫。 在組建 Visual Studio 應用程式時，會從對應的 NuGet 套件中擷取用戶端程式庫。 您也可以在 `Microsoft/Cognitive-Vision-Windows` 存放庫的 `/ClientLibrary` 資料夾中檢視用戶端程式庫的原始程式碼。

在 `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` 命名空間中，用戶端程式庫功能以 `ComputerVisionClient` 類別為主，而在 `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` 命名空間中可找到與電腦視覺互動時 `ComputerVisionClient` 類別所使用的模型。 在範例應用程式隨附的各種 XAML 案例頁面中，可以找到這些命名空間適用的 `using` 指示詞，如下所示：

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

在您探索電腦視覺範例應用程式隨附的案例時，您會對 `ComputerVisionClient` 類別隨附的各種方法有更深入的認識。

## <a name="explore-the-analyze-image-scenario"></a>探索分析影像案例

此案例由 AnalyzePage.xaml 頁面管理。 您可以選擇分析視覺功能和語言，並查看影像及結果。 案例頁面的做法是根據影像來源使用下列其中一種方法進行：

* UploadAndAnalyzeImageAsync  
  此方法適用於本機影像，其中影像必須編碼成 `Stream`，並藉由呼叫 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法傳送到電腦視覺。
* AnalyzeUrlAsync  
  此方法適用於遠端影像，會藉由呼叫 `ComputerVisionClient.AnalyzeImageAsync` 方法，將影像的 URL 傳送到電腦視覺。

`UploadAndAnalyzeImageAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 由於範例應用程式正在分析本機影像，因此必須將該影像的內容傳送至電腦視覺。 此應用程式會開啟 `imageFilePath` 中指定的本機檔案，並以 `Stream` 的形式讀取，接著會取得視覺功能和案例頁面中選取的語言。 其會呼叫 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法，並傳遞該檔案的 `Stream`、視覺化功能以及語言，然後傳回做為 `ImageAnalysis` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

`AnalyzeUrlAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 其會取得視覺功能和案例頁面中選取的語言， 而且會呼叫 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法，並傳遞影像 URL、視覺化功能以及語言，然後傳回做為 `ImageAnalysis` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>探索使用領域模型案例的分析影像

此案例由 AnalyzeInDomainPage.xaml 頁面管理。 您可以選擇 `celebrities` 或 `landmarks` 等領域模型和語言來執行特定領域的影像分析，並查看影像和結果。 案例頁面會根據影像來源使用下列方法：

* GetAvailableDomainModelsAsync  
  這個方法會從電腦視覺取得可用的領域模型清單，並使用 `ComputerVisionClient.ListModelsAsync` 方法在頁面上填入 `_domainModelComboBox` ComboBox 控制項。
* UploadAndAnalyzeInDomainImageAsync  
  此方法適用於本機影像，其中影像必須編碼成 `Stream`，並藉由呼叫 `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` 方法傳送到電腦視覺。
* AnalyzeInDomainUrlAsync  
  此方法適用於遠端影像，會藉由呼叫 `ComputerVisionClient.AnalyzeImageByDomainAsync` 方法，將影像的 URL 傳送到電腦視覺。

`UploadAndAnalyzeInDomainImageAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 由於範例應用程式正在分析本機影像，因此必須將該影像的內容傳送至電腦視覺。 此應用程式會開啟 `imageFilePath` 中指定的本機檔案，並以 `Stream` 的形式讀取，接著會取得在案例頁面中選取的語言。 其會呼叫 `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` 方法，並傳遞該檔案的 `Stream`、領域模型名稱以及語言，然後傳回做為 `DomainModelResults` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

`AnalyzeInDomainUrlAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 其會取得在案例頁面中選取的語言， 而且會呼叫 `ComputerVisionClient.AnalyzeImageByDomainAsync` 方法，並傳遞影像 URL、視覺化功能以及語言，然後傳回做為 `DomainModelResults` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

## <a name="explore-the-describe-image-scenario"></a>探索描述影像案例

此案例由 DescribePage.xaml 頁面管理。 您可以選擇一種語言來建立一般人可理解的影像描述，並查看影像和結果。 案例頁面會根據影像來源使用下列方法：

* UploadAndDescribeImageAsync  
  此方法適用於本機影像，其中影像必須編碼成 `Stream`，並藉由呼叫 `ComputerVisionClient.DescribeImageInStreamAsync` 方法傳送到電腦視覺。
* DescribeUrlAsync  
  此方法適用於遠端影像，會藉由呼叫 `ComputerVisionClient.DescribeImageAsync` 方法，將影像的 URL 傳送到電腦視覺。

`UploadAndDescribeImageAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 由於範例應用程式正在分析本機影像，因此必須將該影像的內容傳送至電腦視覺。 此應用程式會開啟 `imageFilePath` 中指定的本機檔案，並以 `Stream` 的形式讀取，接著會取得在案例頁面中選取的語言。 其會呼叫 `ComputerVisionClient.DescribeImageInStreamAsync` 方法，並傳遞該檔案的 `Stream`、候選項目數量上限 (本案例中為 3) 以及語言，然後傳回做為 `ImageDescription` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

`DescribeUrlAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 其會取得在案例頁面中選取的語言， 而且會呼叫 `ComputerVisionClient.DescribeImageAsync` 方法，並傳遞影像 URL、候選項目數量上限 (本案例中為 3) 以及語言，然後傳回做為 `ImageDescription` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

## <a name="explore-the-generate-tags-scenario"></a>探索產生標籤案例

此案例由 TagsPage.xaml 頁面管理。 您可以選擇要標記影像視覺功能的語言，並查看影像及結果。 案例頁面會根據影像來源使用下列方法：

* UploadAndGetTagsForImageAsync  
  此方法適用於本機影像，其中影像必須編碼成 `Stream`，並藉由呼叫 `ComputerVisionClient.TagImageInStreamAsync` 方法傳送到電腦視覺。
* GenerateTagsForUrlAsync  
  此方法適用於遠端影像，會藉由呼叫 `ComputerVisionClient.TagImageAsync` 方法，將影像的 URL 傳送到電腦視覺。

`UploadAndGetTagsForImageAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 由於範例應用程式正在分析本機影像，因此必須將該影像的內容傳送至電腦視覺。 此應用程式會開啟 `imageFilePath` 中指定的本機檔案，並以 `Stream` 的形式讀取，接著會取得在案例頁面中選取的語言。 其會呼叫 `ComputerVisionClient.TagImageInStreamAsync` 方法，並傳遞該檔案的 `Stream` 和語言，然後傳回做為 `TagResult` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

`GenerateTagsForUrlAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 其會取得在案例頁面中選取的語言， 而且會呼叫 `ComputerVisionClient.TagImageAsync` 方法，並傳遞影像 URL 和語言，然後傳回做為 `TagResult` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

## <a name="explore-the-recognize-text-ocr-scenario"></a>探索辨識文字 (OCR) 案例

此案例由 OCRPage.xaml 頁面管理。 您可以選擇一種語言來辨識和擷取影像中的列印文字，並查看影像及結果。 案例頁面會根據影像來源使用下列方法：

* UploadAndRecognizeImageAsync  
  此方法適用於本機影像，其中影像必須編碼成 `Stream`，並藉由呼叫 `ComputerVisionClient.RecognizePrintedTextInStreamAsync` 方法傳送到電腦視覺。
* RecognizeUrlAsync  
  此方法適用於遠端影像，會藉由呼叫 `ComputerVisionClient.RecognizePrintedTextAsync` 方法，將影像的 URL 傳送到電腦視覺。

`UploadAndRecognizeImageAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 由於範例應用程式正在分析本機影像，因此必須將該影像的內容傳送至電腦視覺。 此應用程式會開啟 `imageFilePath` 中指定的本機檔案，並以 `Stream` 的形式讀取，接著會取得在案例頁面中選取的語言。 其會呼叫 `ComputerVisionClient.RecognizePrintedTextInStreamAsync` 方法，並說明偵測不到方向及傳遞該檔案的 `Stream` 和語言，然後傳回做為 `OcrResult` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

`RecognizeUrlAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 其會取得在案例頁面中選取的語言， 而且會呼叫 `ComputerVisionClient.RecognizePrintedTextAsync` 方法，並說明偵測不到方向及傳遞影像 URL 和語言，然後傳回做為 `OcrResult` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

## <a name="explore-the-recognize-text-v2-english-scenario"></a>探索辨識文字 V2 (英文) 案例

此案例由 TextRecognitionPage.xaml 頁面管理。 您可以選擇辨識模式和語言，以非同步方式辨識和擷取影像的列印或手寫文字，並查看影像和結果。 案例頁面會根據影像來源使用下列方法：

* UploadAndRecognizeImageAsync  
  此方法適用於本機影像，其中影像必須編碼成 `Stream`，並藉由呼叫 `RecognizeAsync` 方法及傳遞 `ComputerVisionClient.RecognizeTextInStreamAsync` 方法的參數化委派，傳送到電腦視覺。
* RecognizeUrlAsync  
  此方法適用於遠端影像，其中藉由呼叫 `RecognizeAsync` 方法及傳遞 `ComputerVisionClient.RecognizeTextAsync` 方法的參數化委派，將影像的 URL 傳送到電腦視覺。
* RecognizeAsync 此方法會處理 `UploadAndRecognizeImageAsync` 和 `RecognizeUrlAsync` 方法的非同步呼叫，並且藉由呼叫 `ComputerVisionClient.GetTextOperationResultAsync` 方法的方式輪詢結果。

和電腦視覺範例應用程式內的其他案例不同，此案例屬於非同步，會呼叫一項方法來啟動流程，但是也會呼叫其他方法來檢查狀態並傳回流程結果。 此案例中的邏輯流程和其他案例的流程稍有不同。

此 `UploadAndRecognizeImageAsync` 方法會開啟 `imageFilePath` 中指定的本機檔案，並以 `Stream` 的形式讀取，然後呼叫 `RecognizeAsync` 方法，並傳遞：

* `ComputerVisionClient.RecognizeTextInStreamAsync` 方法的參數化非同步委派所適用的 Lambda 運算式，以及檔案的 `Stream` 和在 `GetHeadersAsyncFunc` 中做為參數的辨識模式。
* 委派的 Lambda 運算式，用來取得 `GetOperationUrlFunc` 中的 `Operation-Location` 回應標頭值。

`RecognizeUrlAsync` 方法會呼叫 `RecognizeAsync` 方法，並傳遞：

* `ComputerVisionClient.RecognizeTextAsync` 方法的參數化非同步委派所適用的 Lambda 運算式，以及遠端影像的 URL 和在 `GetHeadersAsyncFunc` 中做為參數的辨識模式。
* 委派的 Lambda 運算式，用來取得 `GetOperationUrlFunc` 中的 `Operation-Location` 回應標頭值。

當 `RecognizeAsync` 方法完成時，`UploadAndRecognizeImageAsync` 和 `RecognizeUrlAsync` 方法都會傳回做為 `TextOperationResult` 執行個體的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

`RecognizeAsync` 方法會呼叫在 `GetHeadersAsyncFunc` 中傳遞的 `ComputerVisionClient.RecognizeTextInStreamAsync` 或 `ComputerVisionClient.RecognizeTextAsync` 方法適用的參數化委派，並等候回應。 此方法接著會呼叫在 `GetOperationUrlFunc` 中傳遞的委派，以便取得該回應中的 `Operation-Location` 回應標頭值。 這個值是 URL，用來從電腦視覺擷取 `GetHeadersAsyncFunc` 中傳遞的方法結果。

`RecognizeAsync` 方法接著會呼叫 `ComputerVisionClient.GetTextOperationResultAsync` 方法，並傳遞擷取自 `Operation-Location` 回應標頭的 URL，以取得 `GetHeadersAsyncFunc` 中傳遞的方法狀態和結果。 如果此狀態未說明該方法是否已完成 (無論成功或失敗)，`RecognizeAsync` 方法會再呼叫 `ComputerVisionClient.GetTextOperationResultAsync` 3 次，各次呼叫之間間隔 3 秒。 `RecognizeAsync` 方法會將結果傳回到呼叫它的方法。

## <a name="explore-the-get-thumbnail-scenario"></a>探索取得縮圖案例

此案例由 ThumbnailPage.xaml 頁面管理。 您可以表示是否要使用智慧裁剪，並指定所需的高度和寬度來產生影像縮圖，並查看影像和結果。 案例頁面會根據影像來源使用下列方法：

* UploadAndThumbnailImageAsync  
  此方法適用於本機影像，其中影像必須編碼成 `Stream`，並藉由呼叫 `ComputerVisionClient.GenerateThumbnailInStreamAsync` 方法傳送到電腦視覺。
* ThumbnailUrlAsync  
  此方法適用於遠端影像，會藉由呼叫 `ComputerVisionClient.GenerateThumbnailAsync` 方法，將影像的 URL 傳送到電腦視覺。

`UploadAndThumbnailImageAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 由於範例應用程式正在分析本機影像，因此必須將該影像的內容傳送至電腦視覺。 其會開啟 `imageFilePath` 中指定的本機檔案，並以 `Stream` 的形式讀取， 而且會呼叫 `ComputerVisionClient.GenerateThumbnailInStreamAsync` 方法，並傳遞寬度、高度、檔案的 `Stream`，以及是否要使用智慧裁剪，然後傳回做為 `Stream` 的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

`RecognizeUrlAsync` 方法會使用指定的訂用帳戶金鑰和端點 URL 建立新的 `ComputerVisionClient` 執行個體。 而且會呼叫 `ComputerVisionClient.GenerateThumbnailAsync` 方法，並傳遞寬度、高度、檔案的 URL，以及是否要使用智慧裁剪，然後傳回做為 `Stream` 的結果。 這些繼承自 `ImageScenarioPage` 類別的方法會呈現案例頁面中傳回的結果。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除複製 `Microsoft/Cognitive-Vision-Windows` 存放庫時所儲存的資料夾。 如果您選擇使用範例影像，也請刪除複製 `Microsoft/Cognitive-Face-Windows` 存放庫時所儲存的資料夾。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用臉部辨識服務](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
