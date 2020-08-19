---
title: 臉部 Python 用戶端程式庫快速入門
description: 本快速入門將協助您開始使用適用於 Python 的臉部用戶端程式庫來進行偵測、尋找類似項目、識別和驗證等等。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 8ec32c7bd0d977ea18e4ff66dc616c300851a633
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505854"
---
開始使用適用於 Python 的臉部用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 臉部服務可讓您存取先進的演算法，以偵測和辨識影像中的人臉。

使用適用於 Python 的臉部用戶端程式庫來：

* 偵測影像中的人臉
* 尋找類似臉部
* 建立並訓練人員群組
* 識別臉部
* 驗證臉部
* 取得用於移轉資料的快照集

[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [套件 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [範例](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>必要條件

* [Python 3.x](https://www.python.org/)
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]****。
    * 您需要所建立之資源的金鑰和端點，以將應用程式連線至臉部 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 取得金鑰和端點後，請為名稱分別是 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT` 的金鑰及端點[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

## <a name="setting-up"></a>設定
 
### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 指令碼&mdash;例如，quickstart-file.py**。 在您慣用的編輯器或 IDE 中開啟該檔案，並匯入下列程式庫。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

然後，為資源的 Azure 端點和金鑰建立變數。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

### <a name="install-the-client-library"></a>安裝用戶端程式庫

您可以使用下列命令來安裝用戶端程式庫：

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>物件模型

下列類別和介面會處理臉部 Python 用戶端程式庫的一些主要功能。

|名稱|描述|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | 此類別代表可使用臉部服務的授權，需要有此授權才能執行所有臉部功能。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。 |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|此類別會處理可使用人臉來執行的基本偵測和辨識工作。 |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|此類別代表從影像中單一臉部所偵測到的所有資料。 您可以用此資料來取出該臉部的詳細資訊。|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|此類別會管理儲存於雲端的 **FaceList** 建構，而這些建構會儲存一組混合的臉部。 |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| 此類別會管理儲存於雲端的 **Person** 建構，而這些建構會儲存一組屬於單一人員的臉部。|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| 此類別會管理儲存於雲端的 **PersonGroup** 建構，而這些建構會儲存一組混合的 **Person** 物件。 |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|此類別會管理快照集功能；您可以用此類別來暫時儲存所有的雲端式臉部資料，並將該資料遷移至新的 Azure 訂用帳戶。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的臉部用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [偵測影像中的臉部](#detect-faces-in-an-image)
* [尋找類似臉部](#find-similar-faces)
* [建立並訓練人員群組](#create-and-train-a-person-group)
* [識別臉部](#identify-a-face)
* [驗證臉部](#verify-faces)
* [取得用於移轉資料的快照集](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 本快速入門假設您已針對名為 `FACE_SUBSCRIPTION_KEY` 的臉部金鑰[建立環境變數](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

使用端點和金鑰來具現化用戶端。 使用金鑰建立 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 物件，並使用該物件與您的端點建立 [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) 物件。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>偵測影像中的人臉

下列程式碼會偵測遠端影像中的臉部。 其會將偵測到的臉部識別碼列印到主控台，同時並儲存在程式記憶體中。 然後，其會偵測多人影像中的臉部，同時並將其識別碼列印到主控台。 藉由變更 [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) 方法中的參數，您可以使用每個 [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) 物件傳回不同的資訊。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

如需更多偵測案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) 上的程式碼範例。

### <a name="display-and-frame-faces"></a>顯示和框出臉部

下列程式碼會將指定的影像輸出到顯示器，並使用 DetectedFace.faceRectangle 屬性在臉部周圍繪製矩形。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![臉部周圍繪有紅色矩形的年輕女性](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>尋找類似臉部

下列程式碼會取得一個偵測到的臉部，並搜尋一組其他臉部來尋找相符臉部。 若找到相符的臉部，便會將相符臉部的矩形座標列印到主控台。 

### <a name="find-matches"></a>尋找相符臉部

首先，請執行上一節 ([偵測影像中的臉部](#detect-faces-in-an-image)) 中的程式碼，以儲存單一臉部的參考。 然後執行下列程式碼，來取得群組影像中數個臉部的參考。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

然後新增下列程式碼區塊，以尋找群組中第一個臉部的實例。 若要了解如何修改此行為，請參閱 [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) 方法。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>列印相符臉部

使用下列程式碼將相符臉部的詳細資料列印到主控台。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>建立並訓練人員群組

下列程式碼會建立有三個不同 **Person** 物件的 **PersonGroup**。 其會將每個 **Person** 與一組影像範例產生關聯，然後進行訓練以辨識每個人。 

### <a name="create-persongroup"></a>建立 PersonGroup

若要逐步執行此案例，您必須將下列影像儲存至專案的根目錄： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

此影像群組包含三組對應至三個不同人員的臉部影像。 此程式碼會定義三個 **Person** 物件，並將其與開頭為 `woman`、`man` 和 `child` 的影像檔產生關聯。

在設定好影像後，請在指令碼頂端為您要建立的 **PersonGroup** 物件定義標籤。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

然後將下列程式碼新增到指令碼底部。 此程式碼會建立一個 **PersongGroup** 和三個 **Person** 物件。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>將臉部指派給人員

下列程式碼會排序影像 (依影像的前置詞)、偵測臉部，然後將臉部指派給每個 **Person** 物件。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>訓練 PersonGroup

指派臉部之後，您必須訓練 **PersonGroup**，使其能夠識別與其每個 **Person** 物件相關聯的視覺功能。 下列程式碼會呼叫非同步**訓練**方法並輪詢結果，以將狀態列印到主控台。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>識別臉部

下列程式碼會取得有多個臉部的影像，並想要找到影像中每個人員的身分識別。 其會比較所偵測到的每個臉部與 **PersonGroup**，該資料庫具有已知臉部特徵的不同 **Person** 物件。

> [!IMPORTANT]
> 若要執行這個範例，您必須先執行[建立並訓練人員群組](#create-and-train-a-person-group)中的程式碼。

### <a name="get-a-test-image"></a>取得測試影像

下列程式碼會在專案的根目錄中尋找 test-image-person-group.jpg__ 影像，並偵測該影像中的臉部。 若要尋找此影像，您可以利用下列在管理 **PersonGroup** 時所使用的影像： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>識別人臉

**識別**方法會取得所偵測到臉部的陣列，並將這些臉部與 **PersonGroup** 進行比較。 如果所偵測到的臉部與某個 **Person** 相符，便會儲存結果。 此程式碼會將詳細的比對結果列印到主控台。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>驗證臉部

驗證作業會取得臉部識別碼，以及另一個臉部識別碼或 **Person** 物件，然後判斷這些項目是否屬於同一人。

下列程式碼會偵測兩個來源影像中的臉部，然後根據從目標影像中偵測到的臉部來對來源影像中的臉部進行驗證。

### <a name="get-test-images"></a>取得測試影像

下列程式碼區塊會宣告變數，以指向驗證作業的來源和目標影像。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>偵測臉部以進行驗證

下列程式碼會偵測來源和目標影像中的臉部，並將其儲存至變數。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>取得驗證結果

下列程式碼會將每個來源影像與目標影像進行比較，並列印訊息來指出這些影像是否屬於同一人。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>取得用於移轉資料的快照集

快照集功能可讓您將已儲存的臉部資料 (例如已完成訓練的 **PersonGroup**) 移至不同的 Azure 認知服務臉部訂用帳戶。 舉例來說，如果您已使用免費訂用帳戶建立 **PersonGroup** 物件，而且現在想要將該物件遷移至付費訂用帳戶，就可以使用此功能。 如需快照集功能的廣泛概觀，請參閱[遷移臉部資料](../../Face-API-How-to-Topics/how-to-migrate-face-data.md)。

在此範例中，您會遷移在[建立並訓練人員群組](#create-and-train-a-person-group)中所建立的 **PersonGroup**。 您可以先完成該區段，也可以使用您自己的臉部資料建構。

### <a name="set-up-target-subscription"></a>設定目標訂用帳戶

首先，您必須擁有第二個臉部資源 Azure 訂用帳戶；若要這麼做，請遵循[設定](#setting-up)一節中的步驟。 

然後，在指令碼頂端附近建立下列變數。 您還需要為 Azure 帳戶的訂用帳戶識別碼建立新的環境變數，以及為新的 (目標) 帳戶建立金鑰、端點和訂用帳戶識別碼。 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>驗證目標用戶端

之後在指令碼中將目前的用戶端物件儲存為來源用戶端，然後向目標訂用帳戶驗證新的用戶端物件。 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>使用快照集

其餘快照集作業會在非同步函式中進行。 

1. 第一個步驟是**取得**快照集，這會將原始訂用帳戶的臉部資料儲存至暫存的雲端位置。 此方法會傳回識別碼供您查詢作業的狀態。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. 接下來，請查詢識別碼直到作業完成。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    此程式碼會利用 `wait_for_operation` 函式，請另外定義此函式：

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. 返回非同步函式。 使用**套用**作業將臉部資料寫入至目標訂用帳戶。 此方法也會傳回識別碼。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. 同樣地，使用 `wait_for_operation` 函式來查詢識別碼直到作業完成。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

完成這些步驟後，您就可以從新的 (目標) 訂用帳戶存取臉部資料建構。

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `python` 命令執行應用程式。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

如果您在本快速入門中建立了 **PersonGroup**，但想要將其刪除，請在指令碼中執行下列程式碼：

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

如果您已使用本快速入門中的快照集功能來遷移資料，則還必須刪除已儲存到目標訂用帳戶的 **PersonGroup**。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用適用於 Python 的臉部程式庫來執行基本工作。 接下來，請瀏覽參考文件來深入了解此程式庫。

> [!div class="nextstepaction"]
> [臉部 API 參考 (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [什麼是臉部辨識服務？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) 上找到。
