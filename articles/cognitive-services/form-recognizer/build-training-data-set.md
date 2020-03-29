---
title: 如何為自訂模型構建訓練資料集 - 表單識別器
titleSuffix: Azure Cognitive Services
description: 瞭解如何確保您的培訓資料集經過優化，以訓練表單識別器模型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380621"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>為自訂模型構建訓練資料集

使用表單識別器自訂模型時，可以提供您自己的訓練資料，以便模型可以培訓到特定于行業的表單。 可以訓練模型具有五個填寫的表單或一個空表單（必須在檔案名中包含單詞"空"）以及兩個填寫的表單。 即使您有足夠的填寫表單來訓練，向訓練資料集添加一個空表單也可以提高模型的準確性。

如果要使用手動標記的訓練資料，則應從至少五種相同類型的表單開始。 您仍然可以在同一資料集中使用未標記的表單和空表單。

## <a name="training-data-tips"></a>培訓資料提示

使用針對培訓優化的資料集非常重要。 使用以下提示確保您從[訓練自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)操作中獲得最佳結果：

* 如果可能，請使用基於文本的 PDF 文檔而不是基於圖像的文檔。 掃描的 PDF 以圖像方式處理。
* 對於填寫的表單，請使用已填寫其所有欄位的示例。
* 使用在每個欄位中具有不同值的表單。
* 如果表單圖像品質較低，請使用較大的資料集（例如 10-15 個圖像）。
* 訓練資料集的總大小可達 500 頁。

## <a name="general-input-requirements"></a>一般輸入要求

確保您的培訓資料集也遵循所有表單識別器內容的輸入要求。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>上傳您的培訓資料

將用於培訓的表單文件組放在一起後，需要將其上載到 Azure Blob 存儲容器。 如果您不知道如何使用容器創建 Azure 存儲帳戶，請遵循 Azure 門戶的[Azure 存儲快速入門](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

### <a name="organize-your-data-in-subfolders-optional"></a>在子資料夾中組織資料（可選）

預設情況下，[訓練自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)API 將僅使用位於存儲容器根目錄的表單文檔。 但是，如果在 API 呼叫中指定資料，則可以使用子資料夾中的資料進行訓練。 通常，[訓練自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)調用的正文具有以下形式，其中容器的共用`<SAS URL>`訪問簽名 URL 的位置：

```json
{
  "source":"<SAS URL>"
}
```

如果將以下內容添加到請求正文中，API 將使用位於子資料夾中的文檔進行訓練。 該`"prefix"`欄位是可選的，並將訓練資料集限制為路徑以給定字串開頭的檔。 因此，例如，`"Test"`值將導致 API 僅查看以單詞"Test"開頭的檔或資料夾。

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

現在，您已經瞭解如何構建訓練資料集，請按照快速入門來訓練自訂表格單識別器模型，並開始在表單上使用它。

* [快速入門：使用 cURL 訓練模型並提取表單資料](./quickstarts/curl-train-extract.md)
* [快速入門：使用 PYTHON 的 REST API 訓練模型並提取表單資料](./quickstarts/python-train-extract.md)
* [使用 REST API 和 Python 以標籤進行定型](./quickstarts/python-labeled-data.md)