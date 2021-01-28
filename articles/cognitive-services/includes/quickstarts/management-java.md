---
title: 快速入門：適用於 Java 的 Azure 管理用戶端程式庫
description: 在本快速入門中，開始使用適用於 Java 的 Azure 管理用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 555a6410418232ff164a441dbc90eb10236b9f36
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947459"
---
[參考文件](/java/api/com.microsoft.azure.management.cognitiveservices) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18) | [套件 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>Java 必要條件

* 有效的 Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>建立新的 Java 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

從您的工作目錄執行 `gradle init` 命令。 此命令會建立 Gradle 的基本組建檔案，包括 *build.gradle.kts*，此檔案將在執行階段用來建立及設定您的應用程式。

```console
gradle init --type basic
```

出現選擇 **DSL** 的提示時，請選取 [Kotlin]。

從工作目錄執行下列命令：

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

本快速入門會使用 Gradle 相依性管理員。 您可以在 [Maven 中央存放庫](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)中找到用戶端程式庫和其他相依性管理員的資訊。

在專案的 build.gradle.kts 檔案中，將用戶端程式庫納入為 `implementation` 陳述式，以及必要的外掛程式和設定。

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>匯入程式庫

瀏覽至新的 **src/main/java** 資料夾，並建立名為 Management.java 的檔案。 在您慣用的編輯器或 IDE 中開啟該檔案，並新增下列 `import` 陳述式：

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>驗證用戶端

在 Management.java 中新增類別，然後在新增下列欄位及其值。 使用您所建立的服務主體和其他 Azure 帳戶資訊來填入其值。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

然後，在您的 **main** 方法中，使用這些值來建構 **CognitiveServicesManager** 物件。 您所有的 Azure 管理作業都需要此物件。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>呼叫管理方法

將下列程式碼新增至您的 **Main** 方法，以列出可用的資源、建立範例資源、列出您擁有的資源，然後刪除範例資源。 您會在稍後的步驟中定義這些方法。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>建立認知服務資源 (Java)

若要建立並訂閱新的認知服務資源，請使用 **create** 方法。 這個方法會將新的可計費資源新增至您傳入的資源群組。 建立新的資源時，必須知道您要使用的服務「種類」，以及其定價層 (或 SKU) 和 Azure 位置。 下列方法會將這些項目全部當作引數，並建立資源。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>選擇服務和定價層

建立新的資源時，必須知道您要使用的服務「種類」，以及想要的[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/) (或 SKU)。 建立資源時，您將使用此資訊和其他資訊作為參數。 您可以呼叫下列方法，以尋找可用的認知服務「種類」清單：

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>檢視資源

若要檢視您 Azure 帳戶下的所有資源 (跨所有資源群組)，請使用下列方法：

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>刪除資源

下列方法會從給定的資源群組中刪除指定的資源。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>另請參閱

* [Azure 管理 SDK 參考文件](/java/api/com.microsoft.azure.management.cognitiveservices)
* [Azure 認知服務是什麼？](../../what-are-cognitive-services.md)
* [驗證 Azure 認知服務要求](../../authentication.md)
* [使用 Azure 入口網站建立新的資源](../../cognitive-services-apis-create-account.md)
