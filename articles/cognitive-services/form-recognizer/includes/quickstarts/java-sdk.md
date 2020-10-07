---
title: 快速入門：適用於 Java 的表單辨識器用戶端程式庫
description: 使用適用於 Java 的表單辨識器用戶端程式庫建立表單處理應用程式，從您的自訂文件中擷取索引鍵/值組和資料表資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 4632c5cb12b6d3c2a1b8d4baebf37e9237704591
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318880"
---
> [!IMPORTANT]
> * 表單辨識器 SDK 目前鎖定的目標是表單辨識器服務 2.0 版。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 請參閱下列參考文件。 

[參考文件](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [套件 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [範例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可以使用[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)中 **Train** 資料夾底下的檔案。
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="建立表單辨識器資源"  target="_blank">建立表單辨識器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至表單辨識器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

瀏覽至新的 **src/main/java** 資料夾，並建立名為 Management.java的檔案。 在您慣用的編輯器或 IDE 中開啟該檔案，並新增下列 `import` 陳述式：

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.training.*;
import com.azure.ai.formrecognizer.models.*;
import com.azure.ai.formrecognizer.training.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

新增類別和 `main` 方法，以及為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須關閉編輯器、IDE 或殼層，再重新開啟，才能存取該變數。 您會在稍後定義方法。


```java
public class FormRecognizer {
    public static void main(String[] args)
    {
        String key = "<replace-with-your-form-recognizer-key>";
        String endpoint = "<replace-with-your-form-recognizer-endpoint>";
    }
}
```

## <a name="object-model"></a>物件模型 

透過表單辨識器，您可以建立兩種不同的用戶端類型。 第一個是 `FormRecognizerClient`，可用來查詢服務，以辨識出表單欄位和內容。 第二個是 `FormTrainingClient`，可用來建立和管理自訂模型，以供您改善辨識效果。 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` 會提供用於下列目的的作業：

- 使用已定型的自訂模型辨識表單欄位和內容，以辨識自訂表單。  這些值會在 `RecognizedForm` 物件的集合中傳回。 請參閱[分析自訂表單](#analyze-forms-with-a-custom-model)範例。
- 辨識表單內容，包括資料表、線條和字組，而不需要將模型定型。  表單內容會在 `FormPage` 物件的集合中傳回。 請參閱[辨識表單內容](#recognize-form-content)範例。
- 使用表單辨識器服務上已預先定型的收據模型，辨識美國收據的常見欄位。  這些欄位和中繼資料會在 `RecognizedForm` 物件的集合中傳回。 請參閱[辨識收據](#recognize-receipts)範例。

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` 會提供用於下列目的的作業：

- 將自訂模型定型，以辨識在自訂表單中找到的所有欄位和值。  傳回的 `CustomFormModel` 會指出模型將辨識的表單類型，以及其會針對每個表單類型來擷取的欄位。
- 將自訂模型定型，藉由為自訂表單加上標籤來辨識您指定的特定欄位和值。  傳回的 `CustomFormModel` 會指出模型將會擷取的欄位，以及每個欄位的預估正確性。
- 管理在您的帳戶中建立的模型。
- 將自訂模型從一個表單辨識器資源複製到另一個。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器標籤工具](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)) 將模型定型。

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

> [!IMPORTANT]
> 從 Azure 入口網站取得您的金鑰和端點。 如果您在 [必要條件] 區段中建立的表單辨識器資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 例如，[Azure金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

```java
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
    
    FormTrainingClient trainingClient = new FormTrainingClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
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
    String trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    String formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    String receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    String modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl);
```


## <a name="recognize-form-content"></a>辨識表單內容

您可以使用表單辨識器來辨識文件中的資料表、行和字組，而不需要訓練模型。

若要辨識位於指定 URI 的檔案內容，請使用 **beginRecognizeContentFromUrl** 方法。

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

傳回的值會是 **FormPage** 物件的集合：提交的文件每頁各一個。 下列程式碼會逐一查看這些物件，並列印擷取到的索引鍵/值組和資料表資料。

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
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

### <a name="output"></a>輸出

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="recognize-receipts"></a>辨識收據

本節示範如何使用預先訓練的收據模型，辨識並擷取美國收據中的常見欄位。

若要從 URI 辨識收據，請使用 **beginRecognizeReceiptsFromUrl** 方法。 傳回的值會是 **RecognizedReceipt** 物件的集合：提交的文件每頁各一個。

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, String receiptUri)
{
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> syncPoller =
    recognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedForm> receiptPageResults = syncPoller.getFinalResult();
```

下一個程式碼區塊會逐一查看收據，並將其詳細資料列印至主控台。

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedForm recognizedForm = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedForm.getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (FieldValueType.STRING == merchantNameField.getValue().getValueType()) {
                String merchantName = merchantNameField.getValue().asString();
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantName, merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (FieldValueType.STRING == merchantAddressField.getValue().getValueType()) {
                String merchantAddress = merchantAddressField.getValue().asString();
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddress, merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (FieldValueType.DATE == transactionDateField.getValue().getValueType()) {
                LocalDate transactionDate = transactionDateField.getValue().asDate();
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDate, transactionDateField.getConfidence());
            }
        }
```
下一個程式碼區塊會逐一查看在收據上偵測到的個別項目，並將其詳細資料列印至主控台。

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (FieldValueType.LIST == receiptItemsField.getValue().getValueType()) {
                List<FormField> receiptItems = receiptItemsField.getValue().asList();
                receiptItems.stream()
                    .filter(receiptItem -> FieldValueType.MAP == receiptItem.getValue().getValueType())
                    .map(formField -> formField.getValue().asMap())
                    .forEach(formFieldMap -> formFieldMap.forEach((key, formField) -> {
                        if ("Name".equals(key)) {
                            if (FieldValueType.STRING == formField.getValue().getValueType()) {
                                String name = formField.getValue().asString();
                                System.out.printf("Name: %s, confidence: %.2fs%n",
                                    name, formField.getConfidence());
                            }
                        }
                        if ("Quantity".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float quantity = formField.getValue().asFloat();
                                System.out.printf("Quantity: %f, confidence: %.2f%n",
                                    quantity, formField.getConfidence());
                            }
                        }
                        if ("Price".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float price = formField.getValue().asFloat();
                                System.out.printf("Price: %f, confidence: %.2f%n",
                                    price, formField.getConfidence());
                            }
                        }
                        if ("TotalPrice".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float totalPrice = formField.getValue().asFloat();
                                System.out.printf("Total Price: %f, confidence: %.2f%n",
                                    totalPrice, formField.getConfidence());
                            }
                        }
                }));
            }
        }
    }
}
```

### <a name="output"></a>輸出 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
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
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller =
        trainingClient.beginTraining(trainingDataUrl, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```
傳回的 **CustomFormModel** 物件包含模型可辨識的表單類型資訊，及其可從每個表單類型中擷取的欄位。 下列程式碼區塊會將此資訊列印至主控台。

```java 
    System.out.println("Recognized Fields:");
    // looping through the subModels, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        System.out.printf("The subModel has form type %s%n", customFormSubmodel.getFormType());
        customFormSubmodel.getFields().forEach((field, customFormModelField) ->
            System.out.printf("The model found field '%s' with label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

最後，此方法會傳回模型的唯一識別碼。

```java
    return customFormModel.getModelId();
}
```

### <a name="output"></a>輸出

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>訓練具備標籤的模型

您也可以手動標記訓練文件來訓練自訂模型。 在某些情況下，使用標籤來訓練會讓效能更佳。 若要使用標籤來訓練，您的 Blob 儲存體容器中除了訓練文件以外，還必須要有特殊的標籤資訊檔案 ( *\<filename\>.pdf.labels.json*)。 [表單辨識器範例標籤工具](../../quickstarts/label-tool.md)提供可協助您建立這些標籤檔案的 UI。 只要有了這些檔案，即可呼叫 **beginTraining** 方法，且將 *useTrainingLabels* 參數設為 `true`。

```java
private static String TrainModelWithLabels(
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller = trainingClient.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```

傳回的 **CustomFormModel** 會指出模型可擷取的欄位，及其在每個欄位中的預估正確性。 下列程式碼區塊會將此資訊列印至主控台。

```java
    // looping through the subModels, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        System.out.printf("The subModel with form type %s has accuracy: %.2f%n",
            customFormSubmodel.getFormType(), customFormSubmodel.getAccuracy());
        customFormSubmodel.getFields().forEach((label, customFormModelField) ->
            System.out.printf("The model found field '%s' to have name: %s with an accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

### <a name="output"></a>輸出

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
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
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> recognizeFormPoller =
    formClient.beginRecognizeCustomFormsFromUrl(modelId, pdfFormUrl);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

下列程式碼會將分析結果列印至主控台。 列印內容包括辨識到的每個欄位和對應的值，以及信賴分數。

```java
    for (int i = 0; i < recognizedForms.size(); i++) {
        final RecognizedForm form = recognizedForms.get(i);
        System.out.printf("----------- Recognized custom form info for page %d -----------%n", i);
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) ->
            // label data is populated if you are using a model trained with unlabeled data,
            // since the service needs to make predictions for labels if not explicitly given to it.
            System.out.printf("Field '%s' has label '%s' with a confidence "
                + "score of %.2f.%n", label, formField.getLabelData().getText(), formField.getConfidence()));
    }
}
```

### <a name="output"></a>輸出

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>管理自訂模型

本節示範如何管理您的帳戶中儲存的自訂模型。 下列程式碼範例會在單一方法中執行所有模型管理工作。 請先複製下面的方法簽章：

```java
private static void ManageModels(
    FormTrainingClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>檢查 FormRecognizer 資源帳戶中的模型數

下列程式碼區塊會檢查您已在表單辨識器帳戶中儲存的模型數，並比對帳戶限制。

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = trainingClient.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

#### <a name="output"></a>輸出 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出目前儲存於資源帳戶中的模型

下列程式碼區塊會列出您帳戶中目前的模型，並將其詳細資料列印至主控台。

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = trainingClient.listCustomModels();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = trainingClient.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n", customModel.getTrainingCompletedOn());
        customModel.getSubmodels().forEach(customFormSubmodel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubmodel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubmodel.getAccuracy());
            if (customFormSubmodel.getFields() != null) {
                customFormSubmodel.getFields().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }
        });
    });
```

#### <a name="output"></a>輸出 

為了方便閱讀，此回應已截斷。

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>從資源帳戶中刪除模型

您也可以參考模型的識別碼，從帳戶中將其刪除。

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s, operation completed with status: %s%n", modelId.get(),
    trainingClient.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>執行應用程式

巡覽回到您的主要專案目錄。 然後，使用下列命令建置應用程式：

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
