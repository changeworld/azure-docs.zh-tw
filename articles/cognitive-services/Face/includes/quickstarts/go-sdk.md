---
title: 臉部 Go 用戶端程式庫快速入門
description: 使用適用於 Go 的臉部用戶端程式庫來偵測臉部、尋找相似 (依影像進行臉部搜尋)、識別臉部 (臉部辨識搜尋) 及遷移臉部資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 65f7af56e7f0042b8d4c312d17641a537f5fd908
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816596"
---
開始使用適用於 Go 的臉部用戶端程式庫進行臉部辨識。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 臉部服務可讓您存取先進的演算法，以偵測和辨識影像中的人臉。

使用適用於 Go 的臉部服務用戶端程式庫來：

* [偵測影像中的臉部](#detect-faces-in-an-image)
* [尋找類似臉部](#find-similar-faces)
* [建立並訓練人員群組](#create-and-train-a-person-group)
* [識別臉部](#identify-a-face)

[參考文件](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK 下載](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>必要條件

* 最新版的 [Go](https://golang.org/dl/)
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 取得金鑰和端點後，請為名稱分別是 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT` 的金鑰及端點[建立環境變數](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

## <a name="setting-up"></a>設定

### <a name="create-a-go-project-directory"></a>建立 Go 專案目錄

在主控台視窗 (cmd、PowerShell、Bash) 中，為您的 Go 專案建立新的工作區 `my-app`，並瀏覽至該工作區。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

您的工作區將包含三個資料夾：

* **src** - 此目錄將包含原始程式碼和套件。 任何使用 `go get` 命令安裝的套件都會位於此資料夾中。
* **pkg** - 此目錄將包含已編譯的 Go 套件物件。 這些檔案都具有 `.a` 副檔名。
* **bin** - 此目錄將包含您執行 `go install` 時所建立的二進位可執行檔。

> [!TIP]
> 若要深入了解 Go 工作區的結構，請參閱 [Go 語言文件](https://golang.org/doc/code.html#Workspaces)。 本指南包含用來設定 `$GOPATH` 和 `$GOROOT` 的資訊。

### <a name="install-the-client-library-for-go"></a>安裝適用於 Go 的用戶端程式庫

然後，安裝適用於 Go 的用戶端程式庫：

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

或者，如果您使用 dep，請在存放庫中執行：

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>建立 Go 應用程式

接著，在 **src** 目錄中建立名為 `sample-app.go` 的檔案：

```bash
cd src
touch sample-app.go
```

在您慣用的 IDE 或文字編輯器中開啟 `sample-app.go`。 然後，新增套件名稱，並匯入下列程式庫：

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

接著，您將開始新增程式碼以執行不同的臉部服務作業。

## <a name="object-model"></a>物件模型

下列類別和介面會處理臉部服務 Go 用戶端程式庫的一些主要功能。

|名稱|描述|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | 此類別代表可使用臉部服務的授權，需要有此授權才能執行所有臉部功能。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。 |
|[用戶端](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|此類別會處理可使用人臉來執行的基本偵測和辨識工作。 |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|此類別代表從影像中單一臉部所偵測到的所有資料。 您可以用此資料來取出該臉部的詳細資訊。|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|此類別會管理儲存於雲端的 **FaceList** 建構，而這些建構會儲存一組混合的臉部。 |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| 此類別會管理儲存於雲端的 **Person** 建構，而這些建構會儲存一組屬於單一人員的臉部。|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| 此類別會管理儲存於雲端的 **PersonGroup** 建構，而這些建構會儲存一組混合的 **Person** 物件。 |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|此類別會管理快照集功能。 您可以用此類別來暫時儲存所有的雲端式臉部資料，並將該資料遷移至新的 Azure 訂用帳戶。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼範例會示範如何使用適用於 Go 的臉部服務用戶端程式庫來完成基本工作：

* [驗證用戶端](#authenticate-the-client)
* [偵測影像中的臉部](#detect-faces-in-an-image)
* [尋找類似臉部](#find-similar-faces)
* [建立並訓練人員群組](#create-and-train-a-person-group)
* [識別臉部](#identify-a-face)

## <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE] 
> 本快速入門假設您已分別針對名為 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT` 的臉部金鑰和端點[建立環境變數](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

建立 **main** 函式，並在其中新增下列程式碼，以使用您的端點和金鑰具現化用戶端。 使用金鑰建立 **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** 物件，並使用該物件搭配您的端點來建立 **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** 物件。 此程式碼也會將內容物件具現化，其為建立用戶端物件所需的物件。 此外，也會定義遠端位置，在其中可找到本快速入門中的一些範例影像。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>偵測影像中的人臉

在您的 **main** 方法中新增下列程式碼： 此程式碼會定義遠端範例影像，並指定要從影像中擷取哪些臉部特徵。 此外，也會指定要使用哪個 AI 模型來對偵測到的臉部擷取資料。 如需這些選項的詳細資訊，請參閱[指定辨識模型](../../Face-API-How-to-Topics/specify-recognition-model.md)。 最後， **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** 方法會在影像上執行臉部偵測作業，並將結果儲存在程式記憶體中。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> 您也可以偵測本機影像中的臉部。 請參閱 [用戶端](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client) 方法，例如 **DetectWithStream**。

### <a name="display-detected-face-data"></a>顯示偵測到的臉部資料

下一個程式碼區塊會取得 **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** 物件陣列中的第一個元素，並將其屬性列印到主控台。 如果您使用了有多個臉部的影像，則應該改為逐一查看陣列。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>尋找類似臉部

下列程式碼會取得一個偵測到的臉部 (來源)，並搜尋一組其他臉部 (目標) 來尋找相符臉部 (依影像進行臉部搜尋)。 若找到相符的臉部，便會將相符臉部的識別碼列印到主控台。

### <a name="detect-faces-for-comparison"></a>偵測臉部以進行比較

首先，儲存您在[偵測影像中的臉部](#detect-faces-in-an-image)一節中所偵測到臉部的參考。 此臉部會是來源。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

然後輸入下列程式碼，以偵測不同影像中的一組臉部。 這些臉部會是目標。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>尋找相符臉部

下列程式碼會使用 **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** 方法，尋找符合來源臉部的所有目標臉部。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>列印相符臉部

下列程式碼會將相符臉部的詳細資料列印到主控台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>建立並訓練人員群組

若要逐步執行此案例，您必須將下列影像儲存至專案的根目錄： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

此影像群組包含三組對應至三個不同人員的單一臉部影像。 此程式碼會定義三個 **PersonGroup Person** 物件，並將其與開頭為 `woman`、`man` 和 `child` 的影像檔產生關聯。

### <a name="create-persongroup"></a>建立 PersonGroup

下載影像之後，將下列程式碼新增至 **main** 方法的底部。 此程式碼會驗證 **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** 物件，然後將其用來定義新的 **PersonGroup**。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>建立 PersonGroup Person

下一個程式碼區塊會驗證 **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** ，然後將其用來定義三個新的 **PersonGroup Person** 物件。 這些物件各自代表一組影像中的單一人員。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>將臉部指派給人員

下列程式碼會排序影像 (依影像的前置詞)、偵測臉部，以及根據影像檔案名稱，將臉部指派給各自的 **PersonGroup Person** 物件。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> 您也可以從 URL 所參考的遠端影像，建立 **PersonGroup**。 請參閱 [PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient) 方法，例如 **AddFaceFromURL**。

### <a name="train-persongroup"></a>訓練 PersonGroup

指派臉部之後，您會訓練 **PersonGroup**，使其能夠識別與其每個 **Person** 物件相關聯的視覺功能。 下列程式碼會呼叫非同步 **訓練** 方法並輪詢結果，以將狀態列印到主控台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> 臉部 API 會在一組本質為靜態的預建模型上執行 (模型的效能不會在服務執行時衰退或改善)。 如果 Microsoft 更新模型的後端，而未遷移到全新的模型版本，則模型產生的結果可能會變更。 若要利用較新版本的模型，您可以使用相同的註冊映像來重新訓練 **PersonGroup**，進而將較新的模型指定為參數。

## <a name="identify-a-face"></a>識別臉部

識別作業會取用個人 (或多人) 的影像，並尋找影像中每個臉部的身分識別 (臉部辨識搜尋)。 其會比較所偵測到的每個臉部與 **PersonGroup**，該資料庫具有已知臉部特徵的不同 **Person** 物件。

> [!IMPORTANT]
> 若要執行這個範例，您必須先執行[建立並訓練人員群組](#create-and-train-a-person-group)中的程式碼。

### <a name="get-a-test-image"></a>取得測試影像

下列程式碼會在專案的根目錄中尋找 test-image-person-group.jpg 影像，並將其載入到程式記憶體中。 您可以在與[建立並訓練人員群組](#create-and-train-a-person-group)中所用影像相同的存放庫中找到此影像： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>偵測測試影像中的來源臉部

下一個程式碼區塊會在測試影像上進行一般的臉部偵測，以擷取所有的臉部並將其儲存至陣列。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>識別人臉

**[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** 方法會取得偵測到的臉部陣列，並將其與指定的 **PersonGroup** (在先前章節中定義和訓練) 進行比較。 如果所偵測到的臉部與群組中的某個 **Person** 相符，便會儲存結果。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

此程式碼接著會將詳細的比對結果列印到主控台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>驗證臉部

驗證作業會取得臉部識別碼，以及另一個臉部識別碼或 **Person** 物件，然後判斷這些項目是否屬於同一人。

下列程式碼會偵測兩個來源影像中的臉部，然後根據從目標影像中偵測到的臉部來對來源影像中的臉部進行驗證。

### <a name="get-test-images"></a>取得測試影像

下列程式碼區塊會宣告變數，以指向驗證作業的目標和來源影像。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>偵測臉部以進行驗證

下列程式碼會偵測來源和目標影像中的臉部，並將其儲存至變數。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>取得驗證結果

下列程式碼會將每個來源影像與目標影像進行比較，並列印訊息來指出這些影像是否屬於同一人。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="run-the-application"></a>執行應用程式

使用 `go run <app-name>` 命令，從應用程式目錄執行臉部辨識應用程式。

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

如果您在本快速入門中建立了 **PersonGroup**，但想要將其刪除，請呼叫 **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** 方法。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用適用於 Go 的臉部用戶端程式庫來執行基本臉部辨識工作。 接下來，請瀏覽參考文件來深入了解此程式庫。

> [!div class="nextstepaction"]
> [臉部 API 參考 (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [什麼是臉部辨識服務？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go) 上找到。