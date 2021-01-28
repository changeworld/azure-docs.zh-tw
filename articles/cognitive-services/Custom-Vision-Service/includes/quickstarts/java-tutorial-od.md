---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 5b8d0e677fc623a5fd1e8ba755db62931a8f3495
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948588"
---
開始使用適用於 Java 的自訂視覺用戶端程式庫來建置物件偵測模型。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 請使用此範例作為自行建置影像辨識應用程式的範本。

> [!NOTE]
> 如果您想要在「不用」撰寫程式碼的情況下，建立和訓練物件偵測模型，請改為參閱[以瀏覽器為基礎的指引](../../get-started-build-detector.md)。

使用適用於 Java 的自訂視覺用戶端程式庫可執行下列作業：

* 建立新的自訂視覺專案
* 將標記新增至專案
* 上傳和標記影像
* 為專案定型
* 發佈目前的反覆項目
* 測試預測端點

[參考文件](/java/api/overview/azure/cognitiveservices/client/customvision) | 程式庫原始程式碼 [(訓練)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(預測)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| 成品 (Maven) [(訓練)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(預測)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar) | 
[範例](/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="建立自訂視覺資源"  target="_blank">建立自訂視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以建立定型和預測資源及取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至自訂視覺。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-gradle-project"></a>建立新的 Gradle 專案

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

從您的工作目錄執行 `gradle init` 命令。 此命令會建立 Gradle 的基本組建檔案，包括 *build.gradle.kts*，此檔案將在執行階段用來建立及設定您的應用程式。

```console
gradle init --type basic
```

出現選擇 **DSL** 的提示時，請選取 [Kotlin]。

### <a name="install-the-client-library"></a>安裝用戶端程式庫

找出 build.gradle.kts，並使用您慣用的 IDE 或文字編輯器加以開啟。 然後，在其中複製下列組建組態。 此組態會將專案定義為進入點為 **CustomVisionQuickstart** 類別的 Java 應用程式。 這會匯入自訂視覺程式庫。

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>建立 Java 檔案


在您的工作目錄中執行下列命令，以建立專案來源資料夾：

```console
mkdir -p src/main/java
```

瀏覽至新的資料夾，並建立名為 *CustomVisionQuickstart.java* 的檔案。 在您慣用的編輯器或 IDE 中開啟該檔案，並新增下列 `import` 陳述式：

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java) 上找到該檔案，其中包含本快速入門中的程式碼範例。


在應用程式的 **CustomVisionQuickstart** 類別中，為資源的金鑰和端點建立變數。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的自訂視覺資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 您必須同時取得訓練和預測金鑰，以及訓練資源的端點。
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

在應用程式的 **main** 方法中，針對本快速入門中使用的方法新增呼叫。 稍後您會定義這些項目。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>物件模型

下列類別和介面會處理自訂視覺 Java 用戶端程式庫的一些主要功能。

|名稱|描述|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | 此類別會處理模型的建立、定型和發佈。 |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| 此類別會處理您的模型查詢，以進行物件偵測預測。|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| 此類別會定義單一影像上的單一物件預測。 其中包含物件識別碼和名稱的屬性、物件的周框方塊位置，以及信賴分數。|

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Java 的自訂視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [建立新的自訂視覺專案](#create-a-new-custom-vision-project)
* [將標記新增至專案](#add-tags-to-the-project)
* [上傳和標記影像](#upload-and-tag-images)
* [為專案定型](#train-the-project)
* [發佈目前的反覆項目](#publish-the-current-iteration)
* [測試預測端點](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>驗證用戶端

在您的 **main** 方法中，使用您的端點和金鑰來具現化定型和預測用戶端。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>建立新的自訂視覺專案

下一個方法會建立物件偵測專案。 所建立的專案會顯示在您稍早瀏覽過的[自訂視覺網站](https://customvision.ai/)上。 當您建立專案時，請參閱 [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) 方法多載來指定其他選項 (如[建立偵測器](../../get-started-build-detector.md) Web 入口網站指南中所述)。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>將標記新增到您的專案

此方法會定義您將用來定型模型的標記。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>上傳和標記影像

首先，下載此專案的範例影像。 將[範例影像資料夾](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)的內容儲存到您的本機裝置。

> [!NOTE]
> Trove 是 Microsoft Garage 專案，可讓您收集和購買影像集以供訓練使用。 收集影像之後，您可以下載影像，然後以一般方式將其匯入至自訂視覺專案。 如需深入了解，請造訪 [Trove 頁面](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)。

為物件偵測專案中的影像加上標記時，您必須使用標準化座標來識別每個加上標記的物件所屬的區域。 下列程式碼會為每個範例影像及其已加上標記的區域建立關聯。

> [!NOTE]
> 如果您沒有按住並拖曳公用程式可標示區域的座標，您可以使用 [Customvision.ai](https://www.customvision.ai/) 上的 Web UI。 此範例已提供座標。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

下一個程式碼區塊會將影像新增至專案。 您需要變更 `GetImage` 呼叫的引數以指向您先前下載的 **分支** 和 **交叉** 資料夾。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

先前的程式碼片段利用兩個協助程式函式，將影像擷取為資源串流，然後上傳到服務 (您最多可以在單一批次中上傳 64 個影像)。 定義這些方法。 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>為專案定型

此方法會建立專案中的第一個定型反覆運算。 其會查詢服務，直到定型完成為止。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>發佈目前的反覆項目

此方法會讓模型的目前反覆運算可供查詢。 您可以使用模型名稱作為參考，以傳送預測要求。 您必須針對 `predictionResourceId` 輸入自己的值。 您可以在 Azure 入口網站中資源的 [概觀] 索引標籤上找到預測資源識別碼，該識別碼列為 [訂用帳戶識別碼]。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>測試預測端點

此方法會載入測試影像、查詢模型端點，並將預測資料輸出至主控台。

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>執行應用程式

您可以使用下列命令來建置應用程式：

```console
gradle build
```

使用 `gradle run` 命令執行應用程式：

```console
gradle run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已完成在程式碼中執行物件偵測程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。 下列指南會處理影像分類，但其原則類似於物件偵測。

> [!div class="nextstepaction"]
> [測試和重新定型模型](../../test-your-model.md)

* 什麼是自訂視覺服務？
* 此範例的原始程式碼位於 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
