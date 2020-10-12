---
title: 整合 Azure 儲存體以取得通知和模型備份
titleSuffix: Azure Cognitive Services
description: 瞭解如何整合 Azure 儲存體，以在您定型或匯出自訂視覺模型時接收推播通知。 您也可以儲存匯出模型的備份。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: f4d9cc4c02ab062c73e9dbd977d9ea9e6ccdb60d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532625"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>整合 Azure 儲存體以取得通知和備份

您可以將自訂視覺專案與 Azure Blob 儲存體佇列整合，以取得專案訓練/匯出活動的推播通知，以及已發行模型的備份副本。 這項功能有助於避免長時間執行作業時，系統持續輪詢服務來取得結果。 相反地，您可以將儲存體佇列通知整合到您的工作流程中。

本指南說明如何透過捲曲使用這些 REST Api。 您也可以使用 HTTP 要求服務（例如 Postman）發出要求。

> [!NOTE]
> 推播通知取決於**CreateProject** API 中的選擇性_notificationQueueUri_參數，而模型備份則需要您也必須使用選擇性的_exportModelContainerUri_參數。 本指南將針對完整的功能集使用這兩者。

## <a name="prerequisites"></a>必要條件

- Azure 中的自訂視覺資源。 如果您沒有帳戶，請移至 Azure 入口網站並 [建立新的自訂視覺資源](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。 這項功能目前不支援 (的認知服務資源，) 一個金鑰。
- 具有 blob 容器的 Azure 儲存體帳戶。 如果您需要此步驟的協助，請遵循 [Azure 儲存體實驗室的練習 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) 。

## <a name="set-up-azure-storage-integration"></a>設定 Azure 儲存體整合

移至 Azure 入口網站的自訂視覺訓練資源、選取 [身分 **識別** ] 頁面，然後啟用系統指派的受控識別。

接下來，移至 Azure 入口網站中的儲存體資源。 移至 [ **存取控制] (IAM) ** ] 頁面，並為每個整合功能新增角色指派：
* 如果您打算使用模型備份功能，請選取您的自訂視覺定型資源，並指派 **儲存體 Blob 資料參與者** 角色。 
* 然後，選取您的自訂視覺定型資源，並在您打算使用通知佇列功能時，指派 **儲存體佇列資料參與者** 。

> [!div class="mx-imgBorder"]
> ![儲存體帳戶新增角色指派頁面](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>取得整合 Url

接下來，您將取得可讓自訂視覺資源存取這些端點的 Url。

針對通知佇列整合 URL，請移至儲存體帳戶的 [ **佇列** ] 頁面、新增佇列，並將其 URL 儲存至暫存位置。

> [!div class="mx-imgBorder"]
> ![Azure 儲存體佇列頁面](./media/storage-integration/queue-url.png) 

針對模型備份整合 URL，請移至儲存體帳戶的 [ **容器** ] 頁面，然後建立新的容器。 然後選取它，並移至 [ **屬性** ] 頁面。 將 URL 複製到暫存位置。
 
> [!div class="mx-imgBorder"]
> ![Azure 儲存體容器屬性頁面](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>整合自訂視覺專案

現在您已經有整合 Url，可以建立整合 Azure 儲存體功能的新自訂視覺專案。 您也可以更新現有的專案以新增功能。

### <a name="create-new-project"></a>建立新專案

當您呼叫 [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API 時，請新增選擇性參數 _exportModelContainerUri_ 和 _notificationQueueUri_。 指派您在上一節中取得的 URL 值。 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

如果您收到 `200/OK` 回應，表示 url 已設定成功。 您也應該會在 JSON 回應中看到您的 URL 值：

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
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>更新現有的專案

若要使用 Azure 儲存體功能整合來更新現有的專案，請使用您想要更新之專案的識別碼來呼叫 [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API。 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

將要求主體 (`body`) 設定為下列 JSON 格式，並填入適當的 _ExportModelContainerUri_ 和 _notificationQueueUri_值：

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

如果您收到 `200/OK` 回應，表示 url 已設定成功。

## <a name="verify-the-connection"></a>驗證連線 

您在上一節中的 API 呼叫應該已在您的 Azure 儲存體帳戶中觸發新的資訊。 

在您指定的容器中， **CustomVision-TestPermission** 資料夾內應該會有一個測試 blob。 此 blob 只會暫時存在。

在您的通知佇列中，您應該會看到如下格式的測試通知：
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>取得事件通知

當您準備好時，請在您的專案上呼叫 [>trainproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API，以進行一般定型作業。

在您的儲存體通知佇列中，您將會在定型完成時收到通知：

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`欄位可以是 `"TrainingCompleted"` 或 `"TrainingFailed"` 。 此 `"iterationId"` 欄位是定型模型的識別碼。

## <a name="get-model-export-backups"></a>取得模型匯出備份

當您準備好時，請呼叫 [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API，將定型的模型匯出到指定的平臺。

在您指定的儲存體容器中，會顯示匯出模型的備份副本。 Blob 名稱的格式會是：

```
{projectId} - {iterationId}.{platformType}
```

此外，當匯出完成時，您會在佇列中收到通知。 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`欄位可以是 `"ExportCompleted"` 或 `"ExportFailed"` 。 此 `"modelUri"` 欄位會包含儲存在容器中的備份模型的 URL （假設您在一開始就已整合佇列通知）。 如果您沒有這麼做， `"modelUri"` 欄位將會顯示自訂視覺模型 blob 的 SAS URL。

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何在自訂視覺資源之間複製和移動專案。 接下來，探索 API 參考檔，以瞭解您可以使用自訂視覺的其他功能。
* [REST API 參考文件](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
