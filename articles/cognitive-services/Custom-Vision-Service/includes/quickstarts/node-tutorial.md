---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-js
ms.openlocfilehash: 90927109a78d387ed3a535128e98ae7910c222dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321047"
---
本指南提供指示和範例程式碼，可協助您開始使用適用於 Node.js 的自訂視覺用戶端程式庫來建置影像分類模型。 您將建立專案、新增標籤、將專案定型，並使用專案的預測端點 URL 以程式設計方式加以測試。 請使用此範例作為自行建置影像辨識應用程式的範本。

> [!NOTE]
> 如果您想要在「不用」撰寫程式碼的情況下建立和定型分類模型，請參閱[以瀏覽器為基礎的指引](../../getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>Prerequisites

- 已安裝 [Node.js 8](https://www.nodejs.org/en/download/) 或更新版本。
- 已安裝 [npm](https://www.npmjs.com/)。
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>安裝自訂視覺用戶端程式庫

若要使用適用於 Node.js 的自訂視覺來撰寫影像分析應用程式，您將需要自訂視覺 NPM 套件。 若要加以安裝，請在 PowerShell 中執行下列命令：

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [node-get-images](../../includes/node-get-images.md)]

## <a name="add-the-code"></a>新增程式碼

在您偏好的專案目錄中建立名為 sample.js  的新檔案。

## <a name="create-the-custom-vision-project"></a>建立自訂視覺專案

在指令碼中新增下列程式碼，以建立新的自訂視覺服務專案。 在適當的定義中插入您的訂用帳戶金鑰，並將 sampleDataRoot 路徑值設定為您的映像資料夾路徑。 請確定 endPoint 值符合您在 [Customvision.ai](https://www.customvision.ai/) 建立的訓練和預測端點。 請注意，建立物件偵測和影像分類專案之間的差異，就是在 **createProject** 呼叫中指定的領域。

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");
const msRest = require("@azure/ms-rest-js");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const credentials = new msRest.ApiKeyCredentials({ inHeader: { "Training-key": trainingKey } });
const trainer = new TrainingApi.TrainingAPIClient(credentials, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project");
```

## <a name="create-tags-in-the-project"></a>在專案中建立標記

若要在專案中建立分類標記，請在 sample.js  結尾新增以下程式碼：

```javascript
    const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
    const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

## <a name="upload-and-tag-images"></a>上傳和標記影像

若要將範例影像新增到專案，在標記建立之後插入下列程式碼。 此程式碼會上傳每個影像及其對應標記。 您最多可以在單一批次中上傳 64 個影像。

> [!NOTE]
> 您必須根據認知服務 Node.js SDK 範例專案稍早的下載位置，將 *sampleDataRoot* 變更為影像的路徑。

```javascript
    console.log("Adding images...");
    let fileUploadPromises = [];
    
    const hemlockDir = `${sampleDataRoot}/Hemlock`;
    const hemlockFiles = fs.readdirSync(hemlockDir);
    hemlockFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
    });
    
    const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
    const japaneseCherryFiles = fs.readdirSync(cherryDir);
    japaneseCherryFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
    });
    
    await Promise.all(fileUploadPromises);
```

## <a name="train-and-publish-the-classifier"></a>定型和發佈分類器

此程式碼會在預測模型中建立第一個反覆項目，然後將該反覆項目發佈至預測端點。 提供給已發佈反覆項目的名稱可用來傳送預測要求。 反覆項目要等到發佈後才可在預測端點中使用。

```javascript
    console.log("Training...");
    let trainingIteration = await trainer.trainProject(sampleProject.id);
    
    // Wait for training to complete
    console.log("Training started...");
    while (trainingIteration.status == "Training") {
        console.log("Training status: " + trainingIteration.status);
        await setTimeoutPromise(1000, null);
        trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
    }
    console.log("Training status: " + trainingIteration.status);
    
    // Publish the iteration to the end point
    await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

## <a name="use-the-prediction-endpoint"></a>使用預測端點

若要將影像傳送到預測端點並擷取預測，在檔案結尾處新增以下程式碼：

```javascript
    const predictor_credentials = new msRest.ApiKeyCredentials({ inHeader: { "Prediction-key": predictionKey } });
    const predictor = new PredictionApi.PredictionAPIClient(predictor_credentials, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>執行應用程式

執行 sample.js  。

```shell
node sample.js
```

應用程式的輸出應該會類似下列文字：

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

接著，您可以確認測試影像 (位於 **<base_image_url>/Images/Test/** ) 的標記是否適當。 您也可以返回[自訂視覺網站](https://customvision.ai)，然後查看新建立專案的目前狀態。

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已了解如何在程式碼中完成物件偵測程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](../../test-your-model.md)

* [什麼是自訂視覺服務？](../../overview.md)
* [SDK 參考文件 (定型)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest)
* [SDK 參考文件 (預測)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest)