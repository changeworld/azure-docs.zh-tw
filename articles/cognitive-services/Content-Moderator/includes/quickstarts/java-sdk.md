---
title: Content Moderator Java 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，了解如何開始使用適用於 Java 的 Azure Content Moderator 用戶端程式庫。 在應用程式中建置內容篩選軟體，以遵循法規或維護使用者應有的環境。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: b45873da5e34c77d3eae706975619df948d84874
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698107"
---
開始使用適用於 Java 的 Azure Content Moderator 用戶端程式庫。 請遵循下列步驟來安裝 Maven 套件，並試用基本工作的程式碼範例。 

Content Moderator 是一種 AI 服務，可讓您處理可能具冒犯意味、有風險或不當的資料。 使用 AI 驅動的內容審核服務來掃描文字、影像和影片，並自動套用內容旗標。 在應用程式中建置內容篩選軟體，以遵循法規或維護使用者應有的環境。

使用適用於 Java 的 Content Moderator 用戶端程式庫可以：

* 仲裁文字
* 仲裁影像

[參考文件](/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) |[成品 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [範例](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="建立 Content Moderator 資源"  target="_blank">建立 Content Moderator 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Content Moderator。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
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

## <a name="install-the-client-library"></a>安裝用戶端程式庫

找出 *build.gradle.kts*，並使用您慣用的 IDE 或文字編輯器加以開啟。 然後，在其中複製下列組建組態。 此組態會將專案定義為進入點為 **ContentModeratorQuickstart** 類別的 Java 應用程式。 其會匯入 Content Moderator 用戶端程式庫以及 GSON SDK，以進行 JSON 序列化。

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>建立 Java 檔案


在您的工作目錄中執行下列命令，以建立專案來源資料夾：

```console
mkdir -p src/main/java
```

瀏覽至新的資料夾，並建立名為 *ContentModeratorQuickstart.java* 的檔案。 在您慣用的編輯器或 IDE 中開啟該檔案，並新增下列 `import` 陳述式：

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) 上找到該檔案，其中包含本快速入門中的程式碼範例。

In the application's **ContentModeratorQuickstart** class, create variables for your resource's key and endpoint.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 Content Moderator 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

在應用程式的 **main** 方法中，針對本快速入門中使用的方法新增呼叫。 稍後您會定義這些項目。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>物件模型

下列類別會處理 Content Moderator Java 用戶端程式庫的一些主要功能。

|Name|說明|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|所有 Content Moderator 功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|此類別提供對成人內容、個人資訊或人臉的影像進行分析的功能。|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|此類別提供對語言、粗話、錯誤和個人資訊的文字進行分析的功能。|
|[審查](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|此類別提供審核 API 的功能，包括建立作業、自訂工作流程和人工審核的方法。|


## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Java 的 Content Moderator 用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [仲裁文字](#moderate-text)
* [仲裁影像](#moderate-images)


## <a name="authenticate-the-client"></a>驗證用戶端

在應用程式的 `main` 方法中，使用您的訂用帳戶端點值和訂用帳戶金鑰來建立 [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) 物件。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>仲裁文字

### <a name="set-up-sample-text"></a>設定範例文字

在 **ContentModeratorQuickstart** 類別的頂端，定義對本地文字檔的參考。 將 .txt 檔案新增至您的專案目錄，並輸入想要分析的文字。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>分析文字

建立新的方法來讀取 .txt 檔案，並在每一行上呼叫 **screenText** 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>列印文字仲裁結果

新增下列程式碼，將仲裁結果列印到專案目錄中的 .json 檔案。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

關閉 `try` 和 `catch` 陳述式以完成此方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>仲裁影像

### <a name="set-up-sample-image"></a>設定範例影像

在新的方法中，使用指向影像的指定 URL 字串建立 **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel?view=azure-java-stable)** 物件。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>定義協助程式類別

然後，在您的 *ContentModeratorQuickstart.java* 檔案中，將下列類別定義新增至 **ContentModeratorQuickstart** 類別內。 影像仲裁程序將會使用此內部類別。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>分析內容
這行程式碼會檢查指定 URL 中的影像是否有成人或猥褻內容。 如需這些字詞的相關資訊，請參閱「影像仲裁概念」指南。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>檢查文字
這行程式碼會檢查影像中是否有可見文字。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>檢查臉部
這行程式碼會檢查影像中是否有人臉。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

最後，將傳回的資訊儲存在 `EvaluationData` 清單中。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>列印結果

在 `while` 迴圈後面新增下列程式碼，以將結果列印至主控台和輸出檔案 *src/main/resources/ModerationOutput.json*。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

關閉 `try` 陳述式，並新增用來完成此方法的 `catch` 陳述式。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>執行應用程式

您可以使用下列命令來建置應用程式：

```console
gradle build
```

使用 `gradle run` 命令執行應用程式：

```console
gradle run
```

然後，瀏覽至 *src/main/resources/ModerationOutput.json* 檔案，並檢視內容仲裁的結果。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Content Moderator Java 程式庫來執行仲裁工作。 接下來請閱讀概念性指南，以深入了解影像或其他媒體的仲裁。

> [!div class="nextstepaction"]
> [影像仲裁概念](../../image-moderation-api.md)

* [什麼是 Azure Content Moderator？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) 上找到。