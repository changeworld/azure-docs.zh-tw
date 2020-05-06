---
title: 快速入門：適用於 Go 的電腦視覺用戶端程式庫
titleSuffix: Azure Cognitive Services
description: 透過本快速入門開始使用適用於 Go 的電腦視覺用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: d8f40ab57ee2569b2cb5bf62f391919476b8ab17
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80134452"
---
<a name="HOLTop"></a>

[參考文件](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [套件](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Go](https://golang.org/dl/)

## <a name="setting-up"></a>設定

### <a name="create-a-computer-vision-azure-resource"></a>建立電腦視覺 Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立電腦視覺的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services/#decision)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源。

從試用版訂用帳戶或資源取得金鑰後，請為名稱分別是 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT` 的金鑰及端點 URL [建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-go-project-directory"></a>建立 Go 專案目錄

在主控台視窗 (cmd、PowerShell、Bash) 中，為您的 Go 專案建立新的工作區 `my-app`，並瀏覽至該工作區。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

您的工作區將包含三個資料夾：

* **src** - 此目錄將包含原始程式碼和套件。 任何使用 `go get` 命令安裝的套件都將位於此目錄中。
* **pkg** - 此目錄將包含已編譯的 Go 套件物件。 這些檔案都具有 `.a` 副檔名。
* **bin** - 此目錄將包含您執行 `go install` 時所建立的二進位可執行檔。

> [!TIP]
> 若要深入了解 Go 工作區的結構，請參閱 [Go 語言文件](https://golang.org/doc/code.html#Workspaces)。 本指南包含用來設定 `$GOPATH` 和 `$GOROOT` 的資訊。

### <a name="install-the-client-library-for-go"></a>安裝適用於 Go 的用戶端程式庫

然後，安裝適用於 Go 的用戶端程式庫：

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

或者，如果您使用 dep，請在存放庫中執行：

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>建立 Go 應用程式

接著，在 **src** 目錄中建立名為 `sample-app.go` 的檔案：

```bash
cd src
touch sample-app.go
```

在您慣用的 IDE 或文字編輯器中開啟 `sample-app.go`。 然後，新增套件名稱，並匯入下列程式庫：

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

此外，請在指令碼的根目錄宣告內容。 您將需要此物件來執行大部分的電腦視覺函式呼叫：

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

接著，您將開始新增程式碼以執行不同的電腦視覺作業。

## <a name="object-model"></a>物件模型

下列類別和介面會處理電腦視覺 Go SDK 的一些主要功能。

|名稱|描述|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | 所有電腦視覺功能都需要此類別，例如影像分析和文字閱讀。 您可以使用訂用帳戶資訊來具現化此類別，並使用它來進行大部分的影像作業。|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| 此類型包含 **AnalyzeImage** 函式呼叫的結果。 每個類別特有的函式都會有類似的類型。|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| 此類型包含批次讀取作業的結果。 |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| 此類型會定義可在標準分析作業中完成的不同影像分析類型。 視您的需求而定，您可以指定一組 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Go 的電腦視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [分析影像](#analyze-an-image)
* [讀取印刷和手寫文字](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 此步驟假設您已針對名為 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT` 的電腦視覺金鑰和端點[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

建立 `main` 函式，並在其中新增下列程式碼，以使用您的端點和金鑰具現化用戶端。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>分析影像

下列程式碼會使用用戶端物件來分析遠端影像，並將結果列印至主控台。 您可以取得文字描述、分類、標籤清單、偵測到的物件、偵測到的品牌、偵測到的臉部、成人內容旗標、主要色彩和影像類型。

### <a name="set-up-test-image"></a>設定測試影像

首先，儲存您要分析之影像的 URL 參考。 將此參考放在您的 `main` 函式中。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> 您也可以分析本機影像。 如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上的範例程式碼。

### <a name="specify-visual-features"></a>指定視覺特徵

下列函式呼叫會從範例影像中擷取不同的視覺功能。 您將在接下來的幾節中定義這些函式。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>取得影像說明

下列函式會取得為影像產生的標題清單。 如需影像描述的詳細資訊，請參閱[描述影像](../../concept-describing-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>取得影像類別

下列函式會取得已偵測到的影像類別。 如需詳細資訊，請參閱[將影像分類](../../concept-categorizing-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>取得影像標籤

下列函式會取得影像中已偵測到的一組標籤。 如需詳細資訊，請參閱[內容標籤](../../concept-tagging-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>偵測物件

下列函式會偵測影像中的一般物件，並將其輸出到主控台。 如需詳細資訊，請參閱[物件偵測](../../concept-object-detection.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>偵測品牌

下列程式碼會偵測影像中的公司品牌和標誌，並將其輸出到主控台。 如需詳細資訊，請參閱[品牌偵測](../../concept-brand-detection.md)。

首先，在您的 `main` 函式內宣告新影像的參考。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

下列程式碼會定義品牌偵測函式。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>偵測臉部

下列函式會傳回影像中偵測到的臉部及其矩形座標，以及特定臉部特性。 如需詳細資訊，請參閱[臉部偵測](../../concept-detecting-faces.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>偵測成人、猥褻或暴力內容

下列函式會列印影像中偵測到的成人內容。 如需詳細資訊，請參閱[成人、猥褻、暴力內容](../../concept-detecting-adult-content.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>取得影像色彩配置

下列函式會列印影像中偵測到的色彩特性，例如主色和輔色。 如需詳細資訊，請參閱[色彩配置](../../concept-detecting-color-schemes.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>取得特定領域內容

電腦視覺可以使用特製化模型對影像執行進一步的分析。 如需詳細資訊，請參閱[特定領域內容](../../concept-detecting-domain-content.md)。 

下列程式碼會剖析影像中偵測到的名人相關資料。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

下列程式碼會剖析影像中偵測到的地標相關資料。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>取得影像類型

下列函式可列印影像類型的相關資訊&mdash;不論是美工圖案還是線條繪圖。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>讀取印刷和手寫文字

電腦視覺可以讀取影像中的可見文字，並將它轉換成字元資料流。 本節中的程式碼會定義 `RecognizeTextReadAPIRemoteImage` 函式，其使用用戶端物件來偵測及擷取影像中的印刷或手寫文字。

在您的 `main` 函式中新增範例影像參考和函式呼叫。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> 您也可以從本機影像擷取文字。 如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上的範例程式碼。

### <a name="call-the-read-api"></a>呼叫讀取 API

定義用來讀取文字的新函式 `RecognizeTextReadAPIRemoteImage`。 新增下列程式碼，以針對指定的影像呼叫 **BatchReadFile**方法。 此方法會傳回作業識別碼，並啟動非同步程序來讀取影像的內容。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>取得讀取結果

接下來，取得從 **BatchReadFile** 呼叫傳回的作業識別碼，並將其用於 **GetReadOperationResult** 方法，以查詢服務中的作業結果。 下列程式碼會以一秒的間隔檢查作業，直到傳回結果為止。 然後，它會將已解壓縮的文字資料輸出到主控台。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>顯示讀取結果

新增下列程式碼，以剖析並顯示已擷取的文字資料，並完成函式定義。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>執行應用程式

使用 `go run` 命令從您的應用程式目錄執行應用程式。

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [電腦視覺 API 參考 (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [什麼是電腦視覺？](../../Home.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上找到。
