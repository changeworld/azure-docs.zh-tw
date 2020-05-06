---
title: 快速入門：適用於 Python 的電腦視覺用戶端程式庫
description: 透過本快速入門開始使用適用於 Python 的電腦視覺用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5f552dfc320459d23e538e65e5f34c085f5b5ad9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80134450"
---
<a name="HOLTop"></a>

[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [套件 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [範例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-computer-vision-azure-resource"></a>建立電腦視覺 Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立電腦視覺的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services/#decision)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源

從試用版訂用帳戶或資源取得金鑰後，請為名稱分別是 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT` 的金鑰及端點 URL [建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。
 
### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 指令碼&mdash;例如，quickstart-file.py  。 在您慣用的編輯器或 IDE 中開啟該檔案，並匯入下列程式庫。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

然後，為資源的 Azure 端點和金鑰建立變數。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

### <a name="install-the-client-library"></a>安裝用戶端程式庫

您可以使用下列命令來安裝用戶端程式庫：

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>物件模型

下列類別和介面會處理電腦視覺 Python SDK 的一些主要功能。

|名稱|描述|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| 此類別會直接處理所有影像作業，例如影像分析、文字偵測和縮圖產生。|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | 所有電腦視覺功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。 它會實作 **ComputerVisionClientOperationsMixin**。|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| 此列舉會定義可在標準分析作業中完成的不同影像分析類型。 視您的需求而定，您可以指定一組 **VisualFeatureTypes** 值。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的電腦視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [分析影像](#analyze-an-image)
* [讀取印刷和手寫文字](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 本快速入門假設您已針對名為 `COMPUTER_VISION_SUBSCRIPTION_KEY` 的電腦視覺金鑰[建立環境變數](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

使用端點和金鑰來具現化用戶端。 使用金鑰建立 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 物件，並使用該物件與您的端點建立 [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) 物件。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>分析影像

將參考儲存至您要分析的影像 URL。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>取得影像說明

下列程式碼會取得為影像產生的標題清單。 如需詳細資訊，請參閱[描述影像](../../concept-describing-images.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>取得影像類別

下列程式碼會取得已偵測到的影像類別。 如需詳細資訊，請參閱[將影像分類](../../concept-categorizing-images.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>取得影像標籤

下列程式碼會取得影像中已偵測到的一組標籤。 如需詳細資訊，請參閱[內容標籤](../../concept-tagging-images.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>偵測物件

下列程式碼會偵測影像中的一般物件，並將其輸出到主控台。 如需詳細資訊，請參閱[物件偵測](../../concept-object-detection.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>偵測品牌

下列程式碼會偵測影像中的公司品牌和標誌，並將其輸出到主控台。 如需詳細資訊，請參閱[品牌偵測](../../concept-brand-detection.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>偵測臉部

下列程式碼會傳回影像中偵測到的臉部及其矩形座標，然後選取臉部特性。 如需詳細資訊，請參閱[臉部偵測](../../concept-detecting-faces.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>偵測成人、猥褻或暴力內容

下列程式碼會列印影像中偵測到的成人內容。 如需詳細資訊，請參閱[成人、猥褻、暴力內容](../../concept-detecting-adult-content.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>取得影像色彩配置

下列程式碼會列印影像中偵測到的色彩特性，例如主色和輔色。 如需詳細資訊，請參閱[色彩配置](../../concept-detecting-color-schemes.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>取得特定領域內容

電腦視覺可以使用特製化模型來對影像執行進一步的分析。 如需詳細資訊，請參閱[特定領域內容](../../concept-detecting-domain-content.md)。 

下列程式碼會剖析影像中偵測到的名人相關資料。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

下列程式碼會剖析影像中偵測到的地標相關資料。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>取得影像類型

下列程式碼可列印影像類型相關資訊&mdash;不論是美工圖案或線條繪圖。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>讀取印刷和手寫文字

電腦視覺可以讀取影像中的可見文字，並將它轉換成字元資料流。 您可以分兩個部分執行此作業。

### <a name="call-the-read-api"></a>呼叫讀取 API

首先，使用下列程式碼來呼叫指定影像的 **batch_read_file** 方法。 這會傳回作業識別碼並啟動非同步程序來讀取影像的內容。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>取得讀取結果

接下來，取得從 **batch_read_file** 呼叫傳回的作業識別碼，並使用它來查詢服務中的作業結果。 下列程式碼會以一秒的間隔檢查作業，直到傳回結果為止。 然後，它會將已解壓縮的文字資料輸出到主控台。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `python` 命令執行應用程式。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用適用於 Python 的電腦視覺程式庫來執行基本工作。 接下來，請瀏覽參考文件來深入了解此程式庫。


> [!div class="nextstepaction"]
>[電腦視覺 API 參考 (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [什麼是電腦視覺？](../../Home.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 上找到。
