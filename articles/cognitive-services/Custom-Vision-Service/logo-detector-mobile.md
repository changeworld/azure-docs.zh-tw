---
title: 教學課程：使用自訂標誌偵測器辨識 Azure 服務 - 自訂視覺
titleSuffix: Azure Cognitive Services
description: 在此教學課程中，您將在標誌偵測案例中逐步執行使用自訂視覺的範例應用程式。 了解如何將自訂視覺與其他元件搭配使用以提供端對端應用程式。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b85b2d9b81e84ec6c6e09fef16c66a919aa30cd7
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616751"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>教學課程：辨識相機圖片中的 Azure 服務標誌

在此教學課程中，您將探索在較大的案例中使用自訂視覺的範例應用程式。 AI Visual Provision 應用程式是適用於行動平台的 Xamarin.Forms 應用程式，它可分析 Azure 服務標誌的相機相片，然後將實際的服務部署至使用者的 Azure 帳戶。 在此您將了解此應用程式如何搭配使用自訂視覺與其他元件來提供有用的端對端應用程式。 您可以自行執行整個應用程式案例，也可以僅完成設定的自訂視覺部分，並大致了解應用程式如何加以運用。

此教學課程將為您示範如何：

> [!div class="checklist"]
> - 建立可辨識 Azure 服務標誌的自訂物件偵測器。
> - 將應用程式連線到 Azure 電腦視覺和自訂視覺。
> - 建立 Azure 服務主體帳戶以從應用程式部署 Azure 服務。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。 

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2017 或更新版本](https://www.visualstudio.com/downloads/)
- 適用於 Visual Studio 的 Xamarin 工作負載 (請參閱[安裝 Xamarin](/xamarin/cross-platform/get-started/installation/windows))
- 適用於 Visual Studio 的 iOS 或 Android 模擬器
- [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (選擇性)

## <a name="get-the-source-code"></a>取得原始程式碼

如果您想要使用隨附的 Web 應用程式，請從 GitHub 上的 [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) 存放庫複製或下載應用程式的原始程式碼。 在 Visual Studio 中開啟 *Source/VisualProvision.sln* 檔案。 稍後您將編輯某些專案檔，以便執行應用程式。

## <a name="create-an-object-detector"></a>建立物件偵測器

登入[自訂視覺網站](https://customvision.ai/)，並建立新的專案。 指定物件偵測專案，並使用「標誌」網域；這可讓服務使用最適用的演算法進行標誌偵測。 

![自訂視覺網站在 Chrome 瀏覽器中顯示的新增專案視窗](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>上傳和標記影像

接著，請上傳 Azure 服務標誌的影像並手動加以標記，以訓練標誌偵測演算法。 AIVisualProvision 存放庫中包含一組可供使用的訓練影像。 在網站上，選取 [訓練影像] 索引標籤上的 [新增影像] 按鈕。然後，移至存放庫的 **Documents/Images/Training_DataSet** 資料夾。 您將手動標記每個影像中的標誌，因此如果您只要測試此專案，您可以僅上傳一部分的影像。 對於您預計要使用的每個標記，至少須分別上傳 15 個執行個體。

上傳訓練影像之後，請選取顯示畫面上的第一個影像。 隨即會出現標記視窗。 為每個影像中的各個標誌繪製方塊並指派標記。 

![自訂視覺網站上的標誌標記](media/azure-logo-tutorial/tag-logos.png)

應用程式會設定為使用特定的標記字串。 您可在 *Source\VisualProvision\Services\Recognition\RecognitionService.cs* 檔案中找到相關定義：

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

標記一個影像之後，請移至右側以標記下一個影像。 完成後請關閉標記視窗。

## <a name="train-the-object-detector"></a>訓練物件偵測器

在左窗格中，將 [標記] 參數設為 [已標記]，以顯示您的影像。 然後，選取頁面頂端的綠色按鈕，以訓練模型。 此演算法會訓練辨識新影像中的相同標記。 此外也會以您現有的一些影像測試模型，以產生精確度分數。

![自訂視覺網站的 [訓練影像] 索引標籤。此螢幕擷取畫面標示出訓練按鈕的部分](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>取得預測 URL

模型已定型後，即可將其整合到應用程式中。 您必須取得端點 URL (應用程式將查詢的模型位址) 和預測金鑰 (為應用程式授與預測要求的存取權)。 在 [效能] 索引標籤上，選取頁面頂端的 [預測 URL] 按鈕。

![自訂視覺網站的預測 API 視窗，顯示 URL 位址和 API 金鑰](media/azure-logo-tutorial/cusvis-endpoint.png)

將端點 URL 和 **Prediction-Key** 值複製到 *Source\VisualProvision\AppSettings.cs* 檔案中的適當欄位：

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>查看自訂視覺的使用方式

開啟 *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* 檔案，查看應用程式使用自訂視覺金鑰和端點 URL 的方式。 **PredictImageContentsAsync** 方法會使用影像檔案的位元組資料流和取消權杖 (用於非同步工作管理)、呼叫自訂視覺預測 API，然後傳回預測的結果。 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

此結果會採用 **PredictionResult** 執行個體的形式，其中會包含 **預測** 執行個體的清單。 **預測** 中包含偵測到的標記和及其週框方塊在影像中的位置。

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

若要深入了解應用程式處理這項資料的方式，請從 **GetResourcesAsync** 方法著手。 此方法定義於 *Source/VisualProvision/Services/Recognition/RecognitionService.cs* 檔案中。  

## <a name="add-computer-vision"></a>新增電腦視覺

本教學課程的自訂視覺部分已完成。 如果您想要執行應用程式，則必須再整合電腦視覺服務。 應用程式會使用電腦視覺文字辨識功能來補強標誌偵測程序。 Azure 標誌可從其外觀 *或* 旁邊列印的文字來辨識。 不同於自訂視覺模型，電腦視覺已經過預先訓練，會對影像或影片執行特定作業。

訂閱電腦視覺服務，即可取得金鑰和端點 URL。 如需此步驟的說明，請參閱[如何取得訂用帳戶金鑰](../cognitive-services-apis-create-account.md?tabs=singleservice%2Cwindows)。

![Azure 入口網站中已選取快速入門功能表的電腦視覺服務。 其中標示出金鑰的連結和 API 端點 URL](media/azure-logo-tutorial/comvis-keys.png)

然後，開啟 *Source\VisualProvision\AppSettings.cs* 檔案，並在 `ComputerVisionEndpoint` 和 `ComputerVisionKey` 變數中填入正確的值。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>建立服務主體

應用程式必須透過 Azure 服務主體帳戶將服務部署至您的 Azure 訂用帳戶。 服務主體可讓您使用 Azure 角色型存取控制將特定權限委派給應用程式。 若要深入了解，請參閱[服務主體指南](/azure-stack/operator/azure-stack-create-service-principals)。

您可以使用 Azure Cloud Shell 或 Azure CLI 來建立服務主體，如下所示。 首先，請登入並選取您要使用的訂用帳戶。

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

然後，建立您的服務主體。 (這個程序需要一些時間才能完成。)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

順利完成後，您應該會看到下列 JSON 輸出，其中包含所需的認證。

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

請記下 `clientId` 和 `tenantId` 值。 將其新增至 *Source\VisualProvision\AppSettings.cs* 檔案中的適當欄位。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>執行應用程式

此時，您已為應用程式提供下列項目的存取權：

- 定型的自訂視覺模型
- 電腦視覺服務
- 服務主體帳戶

請依照下列步驟執行應用程式：

1. 在 Visual Studio 方案總管中，選取 **VisualProvision.Android** 專案或 **VisualProvision.iOS** 專案。 從主要工具列的下拉式功能表中選擇對應的模擬器或連線的行動裝置。 然後，執行應用程式。

    > [!NOTE]
    > 您必須以 MacOS 裝置執行 iOS 模擬器。

1. 在第一個畫面中，輸入您的服務主體用戶端識別碼、租用戶識別碼和密碼。 選取 [登入] 按鈕。

    > [!NOTE]
    > 在某些模擬器上執行此步驟時，[登入] 按鈕可能並未啟動。 若是如此，請停止應用程式、開啟 *Source/VisualProvision/Pages/LoginPage.xaml* 檔案、找出標示為 **LOGIN BUTTON** 的 `Button` 元素，並移除以下這一行，然後重新執行應用程式。
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![顯示服務主體認證欄位的應用程式畫面](media/azure-logo-tutorial/app-credentials.png)

1. 在下一個畫面上，從下拉式功能表中選取您的 Azure 訂用帳戶。 (此功能表應包含您的服務主體可存取的所有訂用帳戶。)選取 [繼續] 按鈕。 此時應用程式可能會提示您授與裝置相機和相片儲存體的存取權。 授與存取權限。

    ![顯示目標 Azure 訂用帳戶下拉式欄位的應用程式畫面](media/azure-logo-tutorial/app-az-subscription.png)


1. 裝置上的相機將會啟動。 對您已訓練的其中一個 Azure 服務標誌拍一張相片。 部署視窗應該會提示您選取新服務的區域和資源群組 (就像從 Azure 入口網站加以部署時一樣)。 

    ![智慧型手機相機畫面，主要顯示兩張 Azure 標誌剪紙圖片](media/azure-logo-tutorial/app-camera-capture.png)

    ![顯示部署區域和資源群組欄位的應用程式畫面](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>清除資源

如果您依照此案例的所有步驟操作，並使用應用程式將 Azure 服務部署到您的帳戶，請移至 [Azure 入口網站](https://ms.portal.azure.com/)。 在該處取消您不想要使用的服務。

如果您預計要使用自訂視覺自行建立物件偵測專案，您可以刪除在本教學課程中建立的標誌偵測專案。 自訂視覺的免費訂用帳戶只能建立兩個專案。 若要刪除標誌偵測專案，請在[自訂視覺網站](https://customvision.ai)上開啟 [專案]，然後選取 [我的新專案] 下方的垃圾桶圖示。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定並瀏覽功能完整的 Xamarin.Forms 應用程式，了解它如何利用自訂視覺服務來偵測行動裝置相機影像中的標誌。 接下來，請了解建置自訂視覺模型的最佳做法，以便後續為您自己的應用程式建立強大而精確的模型。

> [!div class="nextstepaction"]
> [如何改善分類器](getting-started-improving-your-classifier.md)