---
title: 如何建立自訂模型-表單辨識器的訓練資料集
titleSuffix: Azure Cognitive Services
description: 瞭解如何確保訓練資料集已針對定型表單辨識器模型進行優化。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75380621"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>建立自訂模型的訓練資料集

當您使用表單辨識器自訂模型時，您會提供自己的定型資料，讓模型可以訓練至您的產業特定表單。 您可以使用五個填滿表單或空白表單來定型模型（您必須在檔案名中包含 "empty" 這個字，加上兩個填滿的表單）。 即使您有足夠的填寫表單來進行定型，將空白表單加入訓練資料集可以改善模型的精確度。

如果您想要使用手動加上標籤的定型資料，您應該從至少五個相同類型的表單開始。 您仍然可以在相同的資料集中使用未標記的表單和空白表單。

## <a name="training-data-tips"></a>定型資料提示

請務必使用已針對定型優化的資料集。 使用下列秘訣，確保您可以從[訓練自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)作業取得最佳結果：

* 可能的話，請使用以文字為基礎的 PDF 檔，而不是以影像為基礎的檔。 掃描的 Pdf 會當做影像來處理。
* 針對填寫的表單，使用已填入其所有欄位的範例。
* 使用在每個欄位中具有不同值的表單。
* 如果您的表單影像品質較低，請使用較大的資料集（例如，10-15 影像）。
* 訓練資料集的總大小最多可達500頁。

## <a name="general-input-requirements"></a>一般輸入需求

請確定您的訓練資料集也會遵循所有表單辨識器內容的輸入需求。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>上傳您的定型資料

當您將用於定型的一組表單檔放在一起時，您需要將它上傳至 Azure blob 儲存體容器。 如果您不知道如何建立具有容器的 Azure 儲存體帳戶，請遵循 Azure 入口網站的[Azure 儲存體快速入門](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

### <a name="organize-your-data-in-subfolders-optional"></a>在子資料夾中組織資料（選擇性）

根據預設，[訓練自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)API 只會使用位於儲存體容器根目錄的表單檔。 不過，如果您在 API 呼叫中指定，您可以使用子資料夾中的資料進行定型。 一般來說，[訓練自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)呼叫的主體具有下列格式，其中`<SAS URL>`是您容器的共用存取簽章 URL：

```json
{
  "source":"<SAS URL>"
}
```

如果您將下列內容新增至要求本文，API 將會使用位於子資料夾中的檔進行定型。 `"prefix"`欄位是選擇性的，而且會將訓練資料集限制為路徑開頭為指定字串的檔案。 例如，的值`"Test"`會導致 API 只查看以 "Test" 一字開頭的檔案或資料夾。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何建立訓練資料集，請遵循快速入門來訓練自訂表單辨識器模型，並開始在您的表單上使用它。

* [快速入門：使用捲曲來定型模型並將表單資料解壓縮](./quickstarts/curl-train-extract.md)
* [快速入門：使用 REST API 搭配 Python 來定型模型並將表單資料解壓縮](./quickstarts/python-train-extract.md)
* [使用 REST API 和 Python 以標籤進行定型](./quickstarts/python-labeled-data.md)