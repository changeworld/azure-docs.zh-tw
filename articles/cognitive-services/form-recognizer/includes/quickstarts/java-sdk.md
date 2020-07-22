---
title: 快速入門：適用於 Java 的表單辨識器用戶端程式庫
description: 在本快速入門中，開始使用適用於 Java 的表單辨識器用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: 479891513eb48e4ced4c1dff2feb3215b3c8ea57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544271"
---
[參考文件](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [套件 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [範例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可以使用[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)中 **Train** 資料夾底下的檔案。
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。

## <a name="setting-up"></a>設定

### <a name="create-a-form-recognizer-azure-resource"></a>建立表單辨識器 Azure 資源

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>建立環境變數

[!INCLUDE [environment-variables](../environment-variables.md)]

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

建立範例應用程式的資料夾。 從工作目錄執行下列命令：

```console
mkdir -p src/main/java
```

巡覽至新的資料夾，並建立名為 formrecognizer-quickstart.java 的檔案。 在您慣用的編輯器或 IDE 中開啟該檔案，並新增下列 `import` 陳述式：

```java
import Azure.AI.FormRecognizer;
import Azure.AI.FormRecognizer.Models;

import java.util.concurrent.atomic.AtomicReference;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
```

在應用程式的 `main` 方法中，為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須關閉編輯器、IDE 或殼層，再重新開啟，才能存取該變數。 您會在稍後定義方法。


```java
public static void Main(string[] args)
{
    String key = System.getenv("FORM_RECOGNIZER_KEY");
    String endpoint = System.getenv("FORM_RECOGNIZER_ENDPOINT");
}
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

本快速入門會使用 Gradle 相依性管理員。 您可以在 [Maven 中央存放庫](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)中找到用戶端程式庫和其他相依性管理員的資訊。

在專案的 build.gradle.kts 檔案中，請務必將用戶端程式庫納入為 `implementation` 陳述式。 

```kotlin
dependencies {
    implementation group: 'com.azure', name: 'azure-ai-formrecognizer', version: '1.0.0-beta.3'
}
```

<!-- 
    Object model tbd
-->

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 Java 的表單辨識器用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [辨識表單內容](#recognize-form-content)
* [辨識收據](#recognize-receipts)
* [訓練自訂模型](#train-a-custom-model)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [管理您的自訂模型](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>驗證用戶端

在您的 `Main` 方法中新增下列程式碼。 在此，您將使用在上方定義的訂用帳戶變數來驗證兩個用戶端物件。 您會使用 **AzureKeyCredential** 物件，這樣當您有需要時，不必建立新的用戶端物件就能更新 API 金鑰。

```java
FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential("{key}"))
    .endpoint("{endpoint}")
    .buildClient();
    
FormTrainingClient trainingClient = recognizerClient.getFormTrainingClient();
```

### <a name="call-client-specific-methods"></a>呼叫用戶端專用方法

下一個程式碼區塊會使用用戶端物件，針對表單辨識器 SDK 中每個主要工作來呼叫方法。 您稍後將會定義這些方法。

您也需要為訓練和測試資料新增 URL 的參考。 
* 若要為您的自訂模型訓練資料擷取 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，複製 [URL] 區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* 若要取得要測試的表單 URL，您可以使用上述步驟來取得 Blob 儲存體中個別文件的 SAS URL， 或使用位於他處的文件 URL。
* 也請使用上述方法取得收據影像的 URL。

> [!NOTE]
> 本指南中的程式碼片段會使用 URL 所存取的遠端表單。 如果您想要改為處理本機表單文件，請參閱[參考文件](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview)中的相關方法。

```java
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl) ;
```


## <a name="recognize-form-content"></a>辨識表單內容

您可以使用表單辨識器來辨識文件中的資料表、行和字組，而不需要訓練模型。

若要辨識位於指定 URI 的檔案內容，請使用 **beginRecognizeContentFromUrl** 方法。

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<OperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

傳回的值會是 **FormPage** 物件的集合：提交的文件每頁各一個。 下列程式碼會逐一查看這些物件，並列印擷取到的索引鍵/值組和資料表資料。

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %d and height: %d, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

## <a name="recognize-receipts"></a>辨識收據

本節示範如何使用預先訓練的收據模型，辨識並擷取美國收據中的常見欄位。

若要從 URI 辨識收據，請使用 **beginRecognizeReceiptsFromUrl** 方法。 傳回的值會是 **RecognizedReceipt** 物件的集合：提交的文件每頁各一個。

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    SyncPoller<OperationResult, List<RecognizedReceipt>> syncPoller =
        formRecognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedReceipt> receiptPageResults = syncPoller.getFinalResult();
```

下一個程式碼區塊會逐一查看收據，並將其詳細資料列印至主控台。

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedReceipt recognizedReceipt = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedReceipt.getRecognizedForm().getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (merchantNameField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantNameField.getFieldValue().asString(),
                    merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (merchantAddressField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddressField.getFieldValue().asString(),
                    merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (transactionDateField.getFieldValue().getType() == FieldValueType.DATE) {
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDateField.getFieldValue().asDate(),
                    transactionDateField.getConfidence());
            }
        }
```
下一個程式碼區塊會逐一查看在收據上偵測到的個別項目，並將其詳細資料列印至主控台。

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (receiptItemsField.getFieldValue().getType() == FieldValueType.LIST) {
                List<FormField> receiptItems = receiptItemsField.getFieldValue().asList();
                receiptItems.forEach(receiptItem -> {
                    if (receiptItem.getFieldValue().getType() == FieldValueType.MAP) {
                        receiptItem.getFieldValue().asMap().forEach((key, formField) -> {
                            if (key.equals("Name")) {
                                if (formField.getFieldValue().getType() == FieldValueType.STRING) {
                                    System.out.printf("Name: %s, confidence: %.2fs%n",
                                        formField.getFieldValue().asString(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("Quantity")) {
                                if (formField.getFieldValue().getType() == FieldValueType.INTEGER) {
                                    System.out.printf("Quantity: %d, confidence: %.2f%n",
                                        formField.getFieldValue().asInteger(), formField.getConfidence());
                                }
                            }
                            if (key.equals("Price")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("TotalPrice")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Total Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                        });
                    }
                });
            }
        }
    }
}
```

## <a name="train-a-custom-model"></a>定型自訂模型

本節會示範如何使用自己的資料來訓練模型。 經過訓練的模型能夠輸出結構化資料，且其中包含原始表單文件內的索引鍵/值關係。 定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器範例標記工具](../../quickstarts/label-tool.md)來訓練模型。

### <a name="train-a-model-without-labels"></a>訓練沒有標籤的模型

訓練自訂模型，以辨識在自訂表單中找到的所有欄位和值，而不需要手動標記訓練文件。

下列方法會在指定文件集上訓練模型，並將模型的狀態列印至主控台。 

```java
private static String TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    String trainingSetSource = "{unlabeled_training_set_SAS_URL}";
    SyncPoller<OperationResult, CustomFormModel> trainingPoller =
        formTrainingClient.beginTraining(trainingSetSource, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getCreatedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```
傳回的 **CustomFormModel** 物件包含模型可辨識的表單類型資訊，及其可從每個表單類型中擷取的欄位。 下列程式碼區塊會將此資訊列印至主控台。

```java 
    System.out.println("Recognized Fields:");
    // looping through the sub-models, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        customFormSubModel.getFieldMap().forEach((field, customFormModelField) ->
            System.out.printf("Field: %s Field Label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

最後，此方法會傳回模型的唯一識別碼。

```java
    return customFormModel.getModelId();
}
```

### <a name="train-a-model-with-labels"></a>訓練具備標籤的模型

您也可以手動標記訓練文件來訓練自訂模型。 在某些情況下，使用標籤來訓練會讓效能更佳。 若要使用標籤來訓練，您的 Blob 儲存體容器中除了訓練文件以外，還必須要有特殊的標籤資訊檔案 ( *\<filename\>.pdf.labels.json*)。 [表單辨識器範例標籤工具](../../quickstarts/label-tool.md)提供可協助您建立這些標籤檔案的 UI。 只要有了這些檔案，即可呼叫 **beginTraining** 方法，且將 *useTrainingLabels* 參數設為 `true`。

```java
private static String TrainModelWithLabels(
    FormRecognizerClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<OperationResult, CustomFormModel> trainingPoller = client.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getRequestedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```

傳回的 **CustomFormModel** 會指出模型可擷取的欄位，及其在每個欄位中的預估正確性。 下列程式碼區塊會將此資訊列印至主控台。

```java
    // looping through the sub-models, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        System.out.printf("Sub-model accuracy: %.2f%n", customFormSubModel.getAccuracy());
        customFormSubModel.getFieldMap().forEach((label, customFormModelField) ->
            System.out.printf("Field: %s Field Name: %s Field Accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自訂模型分析表單

本節示範如何使用以自身表單訓練的模型，從您的自訂表單類型中擷取索引鍵/值資訊和其他內容。

> [!IMPORTANT]
> 若要這樣做，您必須先訓練模型，才能將其識別碼傳遞至下列方法。 請參閱[訓練模型](#train-a-model-without-labels)一節。

您會使用 **beginRecognizeCustomFormsFromUrl** 方法。 傳回的值會是 **RecognizedForm** 物件的集合：提交的文件每頁各一個。

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    String modelId = modelId;
    SyncPoller<OperationResult, List<RecognizedForm>> recognizeFormPoller =
        client.beginRecognizeCustomFormsFromUrl(pdfFormUrl, modelId);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

下列程式碼會將分析結果列印至主控台。 列印內容包括辨識到的每個欄位和對應的值，以及信賴分數。

```java
    recognizedForms.forEach(form -> {
        System.out.println("----------- Recognized Form -----------");
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) -> {
            System.out.printf("Field %s has value %s with confidence score of %.2f.%n", label,
                formField.getFieldValue(),
                formField.getConfidence());
        });
        System.out.print("-----------------------------------");
    });
}
```

## <a name="manage-your-custom-models"></a>管理您的自訂模型

本節示範如何管理您的帳戶中儲存的自訂模型。 下列程式碼範例會在單一方法中執行所有模型管理工作。 請先複製下面的方法簽章：

```java
private static void ManageModels(
    FormRecognizerClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>檢查 FormRecognizer 資源帳戶中的模型數

下列程式碼區塊會檢查您已在表單辨識器帳戶中儲存的模型數，並比對帳戶限制。

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = client.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出目前儲存於資源帳戶中的模型

下列程式碼區塊會列出您帳戶中目前的模型，並將其詳細資料列印至主控台。

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = client.getModelInfos();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = client.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Created on: %s%n", customModel.getRequestedOn());
        System.out.printf("Updated on: %s%n", customModel.getCompletedOn());
        customModel.getSubmodels().forEach(customFormSubModel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubModel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubModel.getAccuracy());
            if (customFormSubModel.getFieldMap() != null) {
                customFormSubModel.getFieldMap().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }

        });
    });
```

### <a name="delete-a-model-from-the-resource-account"></a>從資源帳戶中刪除模型

您也可以參考模型的識別碼，從帳戶中將其刪除。

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s operation completed with status: %s%n", modelId.get(),
        client.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>執行應用程式

您可以使用下列命令來建置應用程式：

```console
gradle build
```

使用 `run` 目標執行應用程式：

```console
gradle run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑難排解

表單辨識器用戶端會引發 `ErrorResponseException` 例外狀況。 例如，如果您嘗試提供無效的檔案來源 URL，則會引發 `ErrorResponseException` 並出現指出失敗原因的錯誤。 下列程式碼片段會攔截例外狀況以適當地處理錯誤，並顯示錯誤的額外資訊。

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>啟用用戶端記錄
適用於 Java 的 Azure SDK 會提供一致的記錄案例，來協助針對應用程式錯誤進行疑難排解，並加快解決速度。 產生的記錄會在到達終端狀態之前，先擷取應用程式流程來協助尋找根本問題。 如需啟用記錄的相關指引，請檢視[記錄 Wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) (英文)。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用了表單辨識器的 Java 用戶端程式庫，以不同方式訓練模型和分析表單。 接下來，您將了解建立更好的訓練資料集有何秘訣，然後產生更精確的模型。

> [!div class="nextstepaction"]
> [建置訓練資料集](../../build-training-data-set.md)

* [什麼是表單辨識器？](../../overview.md)
* 您可在 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples) 取得本指南中的範例程式碼 (及其他資源)。