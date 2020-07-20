---
title: Content Moderator Java 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，了解如何開始使用適用於 Java 的 Azure 認知服務 Content Moderator 用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: ffbb0e4bd2eec3299b4d5b2b38477edf611db1a2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187443"
---
開始使用適用於 Java 的 Content Moderator 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 Content Moderator 是一項認知服務，可檢查文字、影像和視訊內容是否含有可能具冒犯意味、有風險或不當的資料。 找到這類資料時，服務會對內容套用適當的標籤 (旗標)。 加上旗標的內容隨後可由您的應用程式處理，以遵循法規或維護使用者應有的環境。

使用適用於 Java 的 Content Moderator 用戶端程式庫可以：

* 仲裁成人或猥褻內容、文字或人臉的影像。

[參考文件](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [成品 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [範例](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。

## <a name="setting-up"></a>設定

### <a name="create-a-content-moderator-azure-resource"></a>建立 Content Moderator Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立 Content Moderator 的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services/#decision)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源。

從試用版訂用帳戶或資源取得金鑰後，請為名為 `AZURE_CONTENTMODERATOR_KEY` 的金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-gradle-project"></a>建立新的 Gradle 專案

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```
執行 `gradle init`。 此命令會建立 Gradle 的基本組建檔案，包括 *build.gradle.kts*，此檔案將在執行階段用來建立及設定您的應用程式。 從您的工作目錄執行下列命令：

```console
gradle init --type basic
```

出現選擇建置指令碼 DSL 的提示時，請選取 [Kotlin]  。

找出 *build.gradle.kts*，並使用您慣用的 IDE 或文字編輯器加以開啟。 然後，在其中複製下列組建組態。 此組態會將專案定義為進入點為 **ContentModeratorQuickstart** 類別的 Java 應用程式。 其會匯入 Content Moderator 用戶端程式庫以及 Gson SDK，以進行 JSON 序列化。

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

在您的工作目錄中執行下列命令，以建立專案來源資料夾。

```console
mkdir -p src/main/java
```

然後，在新的資料夾中建立名為 *ContentModeratorQuickstart.java* 的檔案。 在您慣用的編輯器或 IDE 中開啟檔案，並在頂端匯入下列程式庫：

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>物件模型

下列類別會處理 Content Moderator Java 用戶端程式庫的一些主要功能。

|名稱|描述|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|所有 Content Moderator 功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|此類別提供對成人內容、個人資訊或人臉的影像進行分析的功能。|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|此類別提供對語言、粗話、錯誤和個人資訊的文字進行分析的功能。|
|[審查](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|此類別提供審核 API 的功能，包括建立作業、自訂工作流程和人工審核的方法。|


## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Java 的 Content Moderator 用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [仲裁影像](#moderate-images)

## <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 此步驟假設您已針對名為 `AZURE_CONTENTMODERATOR_KEY` 的 Content Moderator 金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

在應用程式的 `main` 方法中，使用您的訂用帳戶端點值和訂用帳戶金鑰環境變數來建立 [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) 物件。 

> [!NOTE]
> 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>仲裁影像

### <a name="get-images"></a>取得影像

在專案的 **src/main/** 資料夾中建立 **resources** 資料夾，並瀏覽至該資料夾。 然後，建立新的文字檔 *ImageFiles.txt*。 在此檔案中，您會新增要分析之影像的 URL &mdash; 每行一個 URL。 您可以使用下列範例影像：

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>定義協助程式類別

然後，在您的 *ContentModeratorQuickstart.java* 檔案中，將下列類別定義新增至 **ContentModeratorQuickstart** 類別內。 影像仲裁程序後續將會使用此內部類別。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>逐一查看影像

接著，將下列程式碼新增至 `main` 方法底部。 或者，您可以將其新增至從 `main` 呼叫的個別方法。 此程式碼會逐步執行 _ImageFiles.txt_ 檔案的每一行。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>檢查成人/猥褻內容
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
>[影像仲裁概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [什麼是 Azure Content Moderator？](../../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) 上找到。