---
title: 教學課程：使用 .NET SDK 來偵測和顯示影像中的臉部資料
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您會建立 Windows 應用程式，以使用臉部辨識服務來偵測並框出影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 633404b59581a86dc3c115f132b06d8c8165d13a
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84986504"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>教學課程：建立 Windows Presentation Framework (WPF) 應用程式來顯示影像中的臉部資料

在本教學課程中，您將了解如何使用 Azure 臉部辨識服務，透過 .NET 用戶端 SDK 來偵測影像中的人臉，然後在 UI 中呈現該資料。 您會建立 WPF 應用程式，以偵測人臉、在每張臉孔的周圍繪出邊框，然後在狀態列中顯示該張臉孔的描述。 

本教學課程說明如何：

> [!div class="checklist"]
> - 建立 WPF 應用程式
> - 安裝臉部用戶端程式庫
> - 使用用戶端程式庫來偵測影像中的臉部
> - 在所偵測到的每個臉部周圍繪出邊框
> - 在狀態列上顯示所醒目提示臉部的描述

![顯示已偵測到臉部並使用矩形框出的螢幕擷取畫面](../Images/getting-started-cs-detected.png)

GitHub 上的[認知臉部 CSharp 範例](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)存放庫會有完整的程式碼範例。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。 


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]****。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 為金鑰和服務端點字串[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，兩者分別名為 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`。
- 任何版本的 [Visual Studio](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

請遵循下列步驟以建立新的 WPF 應用程式專案。

1. 在 Visual Studio 中，開啟 [新增專案] 對話方塊。 依序展開 [已安裝]**** 和 [Visual C#]****，然後選取 [WPF 應用程式 (.NET Framework)]****。
1. 將應用程式命名為 **FaceTutorial**，然後按一下 [確定]****。
1. 取得必要的 NuGet 套件。 以滑鼠右鍵按一下 [方案總管] 中的專案，並選取 [管理 NuGet 套件]****，然後尋找並安裝下列套件：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.5.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="add-the-initial-code"></a>新增初始程式碼

在本節中，您會新增應用程式的基本架構，但不會新增其臉部特有功能。

### <a name="create-the-ui"></a>建立 UI

開啟 MainWindow.xaml**，然後使用下列程式碼取代其中的內容&mdash;此程式碼會建立 UI 視窗。 `FacePhoto_MouseMove` 和 `BrowseButton_Click` 方法是會在稍後定義的事件處理常式。

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>建立主類別

開啟 MainWindow.xaml.cs**，然後新增用戶端程式庫命名空間，以及其他必要的命名空間。 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

接下來，在 **MainWindow** 類別中插入下列程式碼。 此程式碼會使用訂用帳戶金鑰和端點建立 **FaceClient** 執行個體。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

接下來，新增 **MainWindow** 建構函式。 它會檢查您的端點 URL 字串，然後將它與用戶端物件產生關聯。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

最後，在類別中新增 **BrowseButton_Click** 和 **FacePhoto_MouseMove** 方法。 這些方法會對應至 MainWindow.xaml** 中所宣告的事件處理常式。 **BrowseButton_Click** 方法會建立 **OpenFileDialog**，讓使用者可以選取 .jpg 影像。 然後，便會將影像顯示在主視窗中。 您會在稍後的步驟中插入 **BrowseButton_Click** 和 **FacePhoto_MouseMove** 的剩餘程式碼。 也請注意 `faceList` 參考&mdash;這是 **DetectedFace** 物件的清單。 此參考是應用程式會儲存和呼叫實際臉部資料的所在。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>試用應用程式

按下功能表上的 [啟動]**** 來測試應用程式。 當應用程式視窗開啟時，按一下左下角的 [瀏覽]****。 [檔案開啟]**** 對話方塊應該會隨即出現。 請從檔案系統中選取影像，並確認它有顯示在視窗中。 然後，關閉應用程式，並進入下一個步驟。

![顯示未修改臉部影像的螢幕擷取畫面](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>上傳影像和偵測臉部

應用程式會藉由呼叫 **FaceClient.Face.DetectWithStreamAsync** 方法來偵測臉部，此方法會包裝[偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API 來上傳本機影像。

在 **MainWindow** 類別的 **FacePhoto_MouseMove** 方法下方插入下列方法。 此方法會定義所要擷取的臉部屬性清單，並將所提交的影像檔案讀取到 **Stream**。 然後，它會將這兩個物件都傳遞至 **DetectWithStreamAsync** 方法呼叫。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>在臉部周圍繪製矩形

接下來，您會新增程式碼以在影像中所偵測到的每個臉部周圍繪製矩形。 在 **MainWindow** 類別中，於 `FacePhoto.Source = bitmapSource` 這一行後面，將下列程式碼插入 **BrowseButton_Click** 方法的結尾。 此程式碼會將所偵測到臉部的清單從呼叫填到 **UploadAndDetectFaces** 中。 然後，它會在每個臉部的周圍繪製矩形，並在主視窗中顯示修改後的影像。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>描述臉部

將下列方法新增至 **MainWindow** 類別的 **UploadAndDetectFaces** 方法下方。 此方法會將所擷取的臉部屬性轉換成字串來描述臉部。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>顯示臉部描述

將下列程式碼新增至 **FacePhoto_MouseMove** 方法。 當滑鼠指標停留在所偵測到的臉部矩形上時，此事件處理常式就會在 `faceDescriptionStatusBar` 中顯示臉部描述字串。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>執行應用程式

執行此應用程式，然後瀏覽包含臉部的影像。 請等候幾秒，讓臉部服務回應。 您應該會看到影像中的每個臉部上有一個紅色矩形。 如果您將滑鼠指標移至臉部矩形上，該臉部的描述應該就會出現在狀態列上。

![顯示已偵測到臉部並使用矩形框出的螢幕擷取畫面](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解臉部服務 .NET SDK 的基本使用程序，並建立了應用程式來偵測並框出影像中的臉部。 接下來，請深入了解臉部偵測的詳細資料。

> [!div class="nextstepaction"]
> [如何偵測影像中的人臉](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
