---
title: 快速入門：適用於 Java 的電腦視覺用戶端程式庫
description: 在本快速入門中，開始使用適用於 Java 的電腦視覺用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 10/13/2019
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 4a82b5550c9105a81bdee9b0f0a5846f93938ab0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95095449"
---
<a name="HOLTop"></a>

[參考文件](/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[成品 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [範例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="建立電腦視覺資源"  target="_blank">建立電腦視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 電腦視覺服務。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
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

本快速入門會使用 Gradle 相依性管理員。 您可以在 [Maven 中央存放庫](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)中找到用戶端程式庫和其他相依性管理員的資訊。

找出 build.gradle.kts，並使用您慣用的 IDE 或文字編輯器加以開啟。 然後，在其中複製下列組建組態。 此組態會將專案定義為進入點為 **ComputerVisionQuickstarts** 類別的 Java 應用程式。 這會匯入電腦視覺程式庫。

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>建立 Java 檔案

在您的工作目錄中執行下列命令，以建立專案來源資料夾：

```console
mkdir -p src/main/java
```

流覽至新的資料夾，並建立名為 ComputerVisionQuickstarts.java 的檔案。 在您慣用的編輯器或 IDE 中開啟該檔案，並新增下列 `import` 陳述式：

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上找到該檔案，其中包含本快速入門中的程式碼範例。

在應用程式的 **ComputerVisionQuickstarts** 類別中，為資源的金鑰和端點建立變數。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 [產品名稱] 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

在應用程式的 **main** 方法中，針對本快速入門中使用的方法新增呼叫。 稍後您會定義這些項目。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>物件模型

下列類別和介面會處理電腦視覺 Java SDK 的一些主要功能。

|名稱|描述|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | 所有電腦視覺功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| 此類別來自用戶端物件，並且會直接處理所有影像作業，例如影像分析、文字偵測和縮圖產生。|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| 此列舉會定義可在標準分析作業中完成的不同影像分析類型。 視您的需求而定，您可以指定一組 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Java 的電腦視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [分析影像](#analyze-an-image)
* [讀取印刷和手寫文字](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>驗證用戶端


在新方法中，使用端點和金鑰將 [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) 物件具現化。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]


## <a name="analyze-an-image"></a>分析影像

下列程式碼會定義 `AnalyzeLocalImage` 方法，以使用用戶端物件來分析本機影像並輸出結果。 該方法會傳回文字描述、分類、標記清單、偵測到的臉部、成人內容旗標、主要色彩和影像類型。

> [!TIP]
> 您也可以使用其 URL 來分析遠端影像。 請參閱 [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) 方法，例如 **AnalyzeImage**。 或如需遠端影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上的範例程式碼。

### <a name="set-up-test-image"></a>設定測試影像

首先，在專案的 **src/main/** 資料夾中建立 **resources/** 資料夾，並新增您想要分析的影像。 然後，將下列方法定義新增至您的 **ComputerVisionQuickstarts** 類別。 請變更 `pathToLocalImage` 的值，以符合您的影像檔案。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>指定視覺特徵

接下來，指定要在分析中擷取的視覺功能。 如需完整清單，請參閱 [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)列舉。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>分析
此方法會針對每個影像分析範圍，將詳細結果輸出到主控台。 我們建議您將此方法呼叫含括在 Try/Catch 區塊中。 **analyzeImageInStream** 方法會傳回包含所有已擷取資訊的 **ImageAnalysis** 物件。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

下列各節將詳細說明如何剖析這項資訊。

### <a name="get-image-description"></a>取得影像說明

下列程式碼會取得為影像產生的標題清單。 如需詳細資訊，請參閱[描述影像](../../concept-describing-images.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>取得影像類別

下列程式碼會取得已偵測到的影像類別。 如需詳細資訊，請參閱[將影像分類](../../concept-categorizing-images.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>取得影像標籤

下列程式碼會取得影像中已偵測到的一組標籤。 如需詳細資訊，請參閱[內容標籤](../../concept-tagging-images.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>偵測臉部

下列程式碼會傳回影像中偵測到的臉部及其矩形座標，然後選取臉部特性。 如需詳細資訊，請參閱[臉部偵測](../../concept-detecting-faces.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>偵測成人、猥褻或暴力內容

下列程式碼會列印影像中偵測到的成人內容。 如需詳細資訊，請參閱[成人、猥褻、暴力內容](../../concept-detecting-adult-content.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>取得影像色彩配置

下列程式碼會列印影像中偵測到的色彩特性，例如主色和輔色。 如需詳細資訊，請參閱[色彩配置](../../concept-detecting-color-schemes.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>取得特定領域內容

電腦視覺可以使用特製化模型來對影像執行進一步的分析。 如需詳細資訊，請參閱[特定領域內容](../../concept-detecting-domain-content.md)。 

下列程式碼會剖析影像中偵測到的名人相關資料。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

下列程式碼會剖析影像中偵測到的地標相關資料。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>取得影像類型

下列程式碼可列印影像類型相關資訊&mdash;不論是美工圖案或線條繪圖。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>讀取印刷和手寫文字

電腦視覺可以讀取影像中的可見文字，並將它轉換成字元資料流。 此區段會定義一個方法 `ReadFromFile`，該方法採用本機檔案路徑並將影像的文字列印到主控台。

> [!TIP]
> 您也可以讀取由 URL 參考之遠端影像中的文字。 請參閱 [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) 方法，例如 **read**。 或如需遠端影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上的範例程式碼。

### <a name="set-up-test-image"></a>設定測試影像

在專案的 **src/main/** 資料夾中建立 **resources/** 資料夾，然後新增您想要從中讀取文字的影像。 您可以下載要在這裡使用的[範例映像](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg)。

然後，將下列方法定義新增至您的 **ComputerVisionQuickstarts** 類別。 請變更 `localFilePath` 的值，以符合您的影像檔案。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>呼叫讀取 API

然後，新增下列程式碼，以針對指定的映像呼叫 **readInStreamWithServiceResponseAsync** 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]


下列程式碼區塊會從讀取呼叫的回應中擷取作業識別碼。 其使用此識別碼搭配 Helper 方法，將文字讀取結果列印到主控台。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

關閉 try/catch 區塊和方法定義。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>取得讀取結果

然後，新增 Helper 方法的定義。 這個方法會使用前一個步驟中的作業識別碼來查詢讀取作業，並在有 OCR 結果可用時加以取得。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

此方法的其餘部分會剖析 OCR 結果，並將其列印到主控台。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

最後，新增上面使用的其他 Helper 方法，此方法會從初始回應中擷取作業識別碼。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

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

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用電腦視覺 Java 程式庫來執行基本工作。 接下來，請瀏覽參考文件來深入了解此程式庫。

> [!div class="nextstepaction"]
>[電腦視覺參考 (Java)](/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [什麼是電腦視覺？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上找到。