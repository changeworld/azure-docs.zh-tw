---
title: 複製並移動自訂視覺專案
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用 ExportProject 和 ImportProject Api 來複製和移動自訂視覺專案。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 78ae0fc94e74755b481f80724ca26b34da99122c
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758569"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>複製並移動您的自訂視覺專案

在您建立並定型自訂視覺專案之後，您可能會想要將專案複製到另一個資源。 例如，您可能想要將專案從開發移至生產環境，或將專案備份到不同 Azure 區域中的帳戶，以增加資料安全性。

**[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 和**[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** api 可讓您將專案從一個自訂視覺帳戶複製到其他帳戶，藉此實現此案例。 本指南說明如何透過捲曲使用這些 REST Api。 您也可以使用 HTTP 要求服務（例如 Postman）發出要求。

## <a name="business-scenarios"></a>商務案例

如果您的應用程式或企業相依于使用自訂視覺專案，建議您將模型複製到另一個區域中的另一個自訂視覺帳戶。 如果發生區域性中斷，您可以在其複製的區域中存取您的專案。

##  <a name="prerequisites"></a>必要條件

- 兩個 Azure 自訂視覺資源。 如果您沒有這些專案，請移至 Azure 入口網站並 [建立新的自訂視覺資源](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。
- 您自訂視覺資源的定型金鑰和端點 Url。 您可以在 Azure 入口網站上資源的 [ **總覽** ] 索引標籤中找到這些值。
- 建立的自訂視覺專案。 如需如何進行此動作的指示，請參閱 [建立分類器](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/getting-started-build-a-classifier) 。

## <a name="process-overview"></a>程序概觀

複製專案的套裝程式含下列步驟：

1. 首先，您會取得您想要複製之來源帳戶中的專案識別碼。
1. 然後，您可以使用來源帳戶的專案識別碼和定型金鑰來呼叫 **ExportProject** API。 您將會取得暫存權杖字串。
1. 然後，您可以使用目標帳戶的權杖字串和定型金鑰來呼叫 **ImportProject** API。 專案接著會列在您的目標帳戶下。

## <a name="get-the-project-id"></a>取得專案識別碼

首先，呼叫 **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** 以查看您現有的自訂視覺專案及其識別碼的清單。 使用來源帳戶的定型金鑰和端點。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

您將會 `200\OK` 在主體中取得專案清單及其中繼資料的回應。 `"id"`值是要針對後續步驟複製的字串。

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>匯出專案

使用專案識別碼和來源定型金鑰和端點來呼叫 **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

您將會收到 `200/OK` 有關匯出專案的中繼資料和參考字串的回應 `"token"` 。 複製權杖的值。

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>匯入專案

使用您的目標定型金鑰和端點，以及參考權杖來呼叫 **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** 。 您也可以在新的帳戶中為您的專案提供名稱。

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects/import?token={token}?name={name}"
-H "Training-key: {training key}"
```

您將會收到 `200/OK` 有關新匯入專案之中繼資料的回應。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何在自訂視覺資源之間複製和移動專案。 接下來，探索 API 參考檔，以瞭解您可以使用自訂視覺的其他功能。
* [REST API 參考文件](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
