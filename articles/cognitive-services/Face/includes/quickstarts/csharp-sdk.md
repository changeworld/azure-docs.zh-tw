---
title: 臉部 .NET 用戶端程式庫快速入門
description: 使用適用於 .NET 的臉部用戶端程式庫來偵測臉部、尋找相似 (依影像進行臉部搜尋)、識別臉部 (臉部辨識搜尋) 及遷移臉部資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 972609b0a1b2249c9da9ab6da9309c0950e76734
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697983"
---
開始使用適用於 .NET 的臉部用戶端程式庫進行臉部辨識。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 臉部服務可讓您存取先進的演算法，以偵測和辨識影像中的人臉。

使用適用於 .NET 的臉部用戶端程式庫來：

* [偵測影像中的臉部](#detect-faces-in-an-image)
* [尋找類似臉部](#find-similar-faces)
* [建立人員群組](#create-a-person-group)
* [識別臉部](#identify-a-face)

[參考文件](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [範例](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>必要條件


* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或目前版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio，建立新的 .NET Core 應用程式。 

### <a name="install-the-client-library"></a>安裝用戶端程式庫 

建立新專案後，以滑鼠右鍵按一下 [方案總管] 中的專案解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的套件管理員中，選取 [瀏覽]、核取 [包含發行前版本]，然後搜尋 `Microsoft.Azure.CognitiveServices.Vision.Face`。 選取版本 `2.6.0-preview.1`，然後 **安裝**。 

#### <a name="cli"></a>[CLI](#tab/cli)

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `face-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*program.cs*。 

```console
dotnet new console -n face-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫 

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 臉部用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) 上找到該檔案，其中包含本快速入門中的程式碼範例。


從專案目錄中中開啟 program.cs 檔案，並新增下列 `using` 指示詞：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

在應用程式的 **Program** 類別中，為資源的金鑰和端點建立變數。


> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [**必要條件**] 區段中建立的臉部資源已成功部署，請按一下 **[下一步]** 下的 [**移至資源**] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

在應用程式的 **Main** 方法中，針對本快速入門中使用的方法新增呼叫。 您稍後會實作這些呼叫。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>物件模型

下列類別和介面會處理臉部 .NET 用戶端程式庫的一些主要功能：

|名稱|描述|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | 此類別代表可使用臉部服務的授權，需要有此授權才能執行所有臉部功能。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。 |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|此類別會處理可使用人臉來執行的基本偵測和辨識工作。 |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|此類別代表從影像中單一臉部所偵測到的所有資料。 您可以用此資料來取出該臉部的詳細資訊。|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|此類別會管理儲存於雲端的 **FaceList** 建構，而這些建構會儲存一組混合的臉部。 |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| 此類別會管理儲存於雲端的 **Person** 建構，而這些建構會儲存一組屬於單一人員的臉部。|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| 此類別會管理儲存於雲端的 **PersonGroup** 建構，而這些建構會儲存一組混合的 **Person** 物件。 |

## <a name="code-examples"></a>程式碼範例

下列程式碼片段說明如何使用適用於 .NET 的臉部用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [偵測影像中的臉部](#detect-faces-in-an-image)
* [尋找類似臉部](#find-similar-faces)
* [建立人員群組](#create-a-person-group)
* [識別臉部](#identify-a-face)

## <a name="authenticate-the-client"></a>驗證用戶端

在新方法中，使用端點和金鑰來具現化用戶端。 使用您的金鑰建立 **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** 物件，並使用該物件與您的端點建立 **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 物件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>宣告 helper 欄位

您稍後將新增的幾個臉部作業需要下列欄位。 在 **Program** 類別的根目錄中，定義下列 URL 字串。 此 URL 會指向範例影像的資料夾。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

在 **Main** 方法中，定義要指向不同辨識模型類型的字串。 稍後，您將能夠指定要用於臉部偵測的辨識模型。 如需這些選項的詳細資訊，請參閱[指定辨識模型](../../Face-API-How-to-Topics/specify-recognition-model.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>偵測影像中的人臉

### <a name="get-detected-face-objects"></a>取得偵測到的臉部物件

建立新方法來偵測臉部。 `DetectFaceExtract` 方法會處理指定 URL 上的三個影像，並在程式記憶體中建立 **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** 物件的清單。 **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 值的清單會指定要擷取的特徵。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> 您也可以偵測本機影像中的臉部。 請參閱 [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations?view=azure-dotnet) 方法，例如 **DetectWithStreamAsync**。

### <a name="display-detected-face-data"></a>顯示偵測到的臉部資料

`DetectFaceExtract` 方法的其餘部分會剖析每個偵測到的臉部，並列印屬性資料。 每個屬性必須在原始的臉部偵測 API 呼叫中個別指定 (在 **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 清單中)。 下列程式碼會處理每個屬性，但您可能只需要使用一個或幾個屬性。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>尋找類似臉部

下列程式碼會取得一個偵測到的臉部 (來源)，並搜尋一組其他臉部 (目標) 來尋找相符臉部 (依影像進行臉部搜尋)。 若找到相符的臉部，便會將相符臉部的識別碼列印到主控台。

### <a name="detect-faces-for-comparison"></a>偵測臉部以進行比較

首先，定義第二個臉部偵測方法。 您必須先偵測影像中的臉部，才能進行比較，而此偵測方法已針對比較作業進行最佳化。 該方法不會擷取詳細的臉部特性 (如上節中所述)，而是會使用不同的辨識模型。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>尋找相符臉部

下列方法會在一組目標影像和單一來源影像中偵測臉部。 然後，該方法會比較這些影像，並尋找與來源影像類似的所有目標影像。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>列印相符臉部

下列程式碼會將相符臉部的詳細資料列印到主控台：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>識別臉部

識別作業會取用個人 (或多人) 的影像，並尋找影像中每個臉部的身分識別 (臉部辨識搜尋)。 其會比較所偵測到的每個臉部與 **PersonGroup**，該資料庫具有已知臉部特徵的不同 **Person** 物件。 若要執行識別作業，您必須先建立並訓練 **PersonGroup**

### <a name="create-a-person-group"></a>建立人員群組

下列程式碼會建立有六個不同 **Person** 物件的 **PersonGroup**。 其會將每個 **Person** 與一組影像範例產生關聯，然後進行訓練以透過其臉部特徵辨識每個人。 **Person** 和 **PersonGroup** 物件會用在驗證、識別和群組作業中。

在類別根目錄上宣告字串變數，以代表您將建立的 **PersonGroup** 識別碼。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

在新方法中，新增下列程式碼。 此方法會執行識別作業。 第一個程式碼區塊會將人員的名稱與範例影像產生關聯。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

請注意，此程式碼會定義 `sourceImageFileName`變數。 此變數會對應至來源影像&mdash;該影像包含要識別的人員。

接下來，新增下列程式碼，在字典中為每個人建立 **Person** 物件，並從適當的影像加入臉部資料。 每個 **Person** 物件都會透過其唯一的識別碼字串，與相同的 **PersonGroup** 產生關聯。 請記得將變數 `client`、`url` 和 `RECOGNITION_MODEL1` 傳遞給此方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> 您也可以從本機影像建立 **PersonGroup**。 請參閱 [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson?view=azure-dotnet) 方法，例如 **AddFaceFromStreamAsync**。

### <a name="train-the-persongroup"></a>訓練 PersonGroup

當您從影像中擷取臉部資料，並將其分類至不同 **Person** 物件後，您必須訓練 **PersonGroup**，以識別與其每一個 **Person** 物件相關聯的視覺特徵。 下列程式碼會呼叫非同步 **訓練** 方法並輪詢結果，以將狀態列印到主控台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> 臉部 API 會在一組本質為靜態的預建模型上執行 (模型的效能不會在服務執行時衰退或改善)。 如果 Microsoft 更新模型的後端，而未遷移到全新的模型版本，則模型產生的結果可能會變更。 若要利用較新版本的模型，您可以使用相同的註冊映像來重新訓練 **PersonGroup**，進而將較新的模型指定為參數。

此 **Person** 群組及其相關聯的 **Person** 物件現在已準備好用於驗證、識別或群組作業。

### <a name="identify-faces"></a>識別人臉

下列程式碼會採用來源影像，並建立在影像中偵測到的所有臉部清單。 這些是依據 **PersonGroup** 所識別的臉部。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

下一個程式碼片段會呼叫 **IdentifyAsync** 作業，並將結果列印至主控台。 在此，該服務會嘗試將來源影像中的每個臉部與指定 **PersonGroup** 中的 **Person** 進行比對。 這會關閉您的識別方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>執行應用程式

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

按一下 IDE 視窗頂端的 [偵錯] 按鈕，以執行應用程式。

#### <a name="cli"></a>[CLI](#tab/cli)

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

如果您在本快速入門中建立了 **PersonGroup**，但想要將其刪除，請在程式中執行下列程式碼：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

以下列程式碼定義刪除方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用適用於 .NET 的臉部用戶端程式庫來執行基本臉部辨識工作。 接下來，請瀏覽參考文件來深入了解此程式庫。

> [!div class="nextstepaction"]
> [臉部 API 參考 (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [什麼是臉部辨識服務？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) 上找到。