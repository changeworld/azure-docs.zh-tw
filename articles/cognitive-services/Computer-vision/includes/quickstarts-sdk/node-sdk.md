---
title: 快速入門：適用於 Node.js 的電腦視覺用戶端程式庫
description: 透過本快速入門開始使用適用於 Node.js 的電腦視覺用戶端程式庫
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 556e56991d59eb65f789fde4d705706a03a63be9
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "85073396"
---
<a name="HOLTop"></a>

[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [套件 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [範例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Node.js](https://nodejs.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="建立電腦視覺資源"  target="_blank">建立電腦視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要所建立之資源的金鑰和端點，以將應用程式連線至電腦視覺服務。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 為金鑰和端點 URL [建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，名稱分別為 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir myapp && cd myapp
```

執行命令 `npm init`，以使用 `package.json` 檔案建立節點應用程式。

```console
npm init
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 `ms-rest-azure` 和 `@azure/cognitiveservices-computervision` NPM 套件：

```console
npm install @azure/cognitiveservices-computervision
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。

### <a name="prepare-the-nodejs-script"></a>准辦 Node.js 指令碼

建立新檔案 index.js，並在文字編輯器中開啟它。 新增下列 import 陳述式。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

然後，定義函式 `computerVision`，然後使用主要函式和回呼函式宣告非同步序列。 您會將快速入門程式碼新增至主要函式，然後在指令碼底部呼叫 `computerVision`。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>物件模型

下列類別和介面會處理電腦視覺 Node.js SDK 的一些主要功能。

|名稱|描述|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | 所有電腦視覺功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並使用它來進行大部分的影像作業。|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| 此列舉會定義可在標準分析作業中完成的不同影像分析類型。 視您的需求而定，您可以指定一組 **VisualFeatureTypes** 值。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Node.js 的電腦視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [分析影像](#analyze-an-image)
* [讀取印刷和手寫文字](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>驗證用戶端

為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

使用端點和金鑰來具現化用戶端。 使用您的金鑰和端點建立 [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) 物件，然後使用該物件建立 [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) 物件。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>分析影像

本節中的程式碼會分析遠端影像，以擷取各種視覺功能。 您可以在用戶端物件的 **analyzeImage** 方法中執行這些作業，也可以使用個別的方法來呼叫它們。 如需詳細資訊，請參閱[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)。

> [!NOTE]
> 您也可以分析本機影像。 如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 上的範例程式碼。

### <a name="get-image-description"></a>取得影像說明

下列程式碼會取得為影像產生的標題清單。 如需詳細資訊，請參閱[描述影像](../../concept-describing-images.md)。

首先，定義要分析的影像 URL：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

然後，新增下列程式碼以取得影像描述，並將其輸出到主控台。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>取得影像類別

下列程式碼會取得已偵測到的影像類別。 如需詳細資訊，請參閱[將影像分類](../../concept-categorizing-images.md)。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

定義協助程式函式 `formatCategories`：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>取得影像標籤

下列程式碼會取得影像中已偵測到的一組標籤。 如需詳細資訊，請參閱[內容標籤](../../concept-tagging-images.md)。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

定義協助程式函式 `formatTags`：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>偵測物件

下列程式碼會偵測影像中的一般物件，並將其輸出到主控台。 如需詳細資訊，請參閱[物件偵測](../../concept-object-detection.md)。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

定義協助程式函式 `formatRectObjects`：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>偵測品牌

下列程式碼會偵測影像中的公司品牌和標誌，並將其輸出到主控台。 如需詳細資訊，請參閱[品牌偵測](../../concept-brand-detection.md)。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>偵測臉部

下列程式碼會傳回影像中偵測到的臉部及其矩形座標，然後選取臉部特性。 如需詳細資訊，請參閱[臉部偵測](../../concept-detecting-faces.md)。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

定義協助程式函式 `formatRectFaces`：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>偵測成人、猥褻或暴力內容

下列程式碼會列印影像中偵測到的成人內容。 如需詳細資訊，請參閱[成人、猥褻、暴力內容](../../concept-detecting-adult-content.md)。

定義要使用的影像 URL：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

然後新增下列程式碼來偵測成人內容，並將結果輸出到主控台。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>取得影像色彩配置

下列程式碼會列印影像中偵測到的色彩特性，例如主色和輔色。 如需詳細資訊，請參閱[色彩配置](../../concept-detecting-color-schemes.md)。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

定義協助程式函式 `printColorScheme`，以將色彩配置的詳細資料輸出到主控台。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>取得特定領域內容

電腦視覺可以使用特製化模型來對影像執行進一步的分析。 如需詳細資訊，請參閱[特定領域內容](../../concept-detecting-domain-content.md)。

首先，定義要分析的影像 URL：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

下列程式碼會剖析影像中偵測到的地標相關資料。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

定義協助程式函式 `formatRectDomain`，以剖析所偵測地標的位置資料。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>取得影像類型

下列程式碼可列印影像類型相關資訊&mdash;不論是美工圖案或線條繪圖。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

定義協助程式函式 `describeType`：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>讀取印刷和手寫文字

電腦視覺可以讀取影像中的可見文字，並將它轉換成字元資料流。

> [!NOTE]
> 您也可以從本機影像讀取文字。 如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 上的範例程式碼。

### <a name="set-up-test-images"></a>設定測試影像

儲存您要從中擷取文字之影像的 URL 參考。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>呼叫辨識 API

新增下面的程式碼，以針對指定的影像呼叫 `recognizeText` 函式。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

定義 `recognizeText` 函式。 這會在用戶端物件上呼叫 **recognizeText** 方法，以傳回作業識別碼並啟動非同步程序來讀取影像的內容。 然後，它會以一秒的間隔使用作業識別碼來檢查作業，直到傳回結果為止。 它會接著傳回擷取的結果。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

然後，定義協助程式函式 `printRecText`，此函式會將辨識作業的結果輸出到主控台。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node` 命令執行應用程式。

```console
node index.js
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[電腦視覺 API 參考 (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [什麼是電腦視覺？](../../Home.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 上找到。
