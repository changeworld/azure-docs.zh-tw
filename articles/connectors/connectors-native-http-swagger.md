---
title: 從 Azure 邏輯應用連接到 REST 終結點
description: 通過使用 Azure 邏輯應用監視自動化任務、流程和工作流中的 REST 終結點
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787364"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用調用 REST 終結點

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和內置 HTTP + Swagger 連接器，可以通過構建邏輯應用自動執行通過[Swagger 檔](https://swagger.io)定期調用任何 REST 終結點的工作流。 HTTP + Swagger 觸發器和操作的工作方式與[HTTP 觸發器和操作](connectors-native-http.md)相同，但通過公開 Swagger 檔描述的 API 結構和輸出，在邏輯應用設計器中提供更好的體驗。 要實現輪詢觸發器，請遵循創建自訂 API 中描述的輪詢模式[，從邏輯應用調用其他 API、服務和系統](../logic-apps/logic-apps-create-api-app.md#polling-triggers)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 描述目標 REST 終結點的斯瓦格（非 OpenAPI）檔的 URL

  通常，REST 終結點必須滿足此條件，連接器才能工作：

  * 斯瓦格檔必須託管在可公開訪問的 HTTPS URL 上。

  * Swagger 檔必須啟用[跨源資源分享 （CORS）。](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

  要引用未託管或不符合安全和跨源要求的 Swagger 檔，可以將[Swagger 檔上載到 Azure 存儲帳戶中的 blob 容器](#host-swagger)，並在該存儲帳戶上啟用 CORS，以便可以引用該檔。

  本主題中的示例使用[認知服務人臉 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)，這需要[認知服務帳戶和訪問金鑰](../cognitive-services/cognitive-services-apis-create-account.md)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您是邏輯應用的新增功能，請查看什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)

* 要從何處調用目標終結點的邏輯應用。 要從 HTTP + Swagger 觸發器開始，[請創建一個空白邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要使用 HTTP + Swagger 操作，請使用所需的任何觸發器啟動邏輯應用。 本示例使用 HTTP + 斯瓦格觸發器作為第一步。

## <a name="add-an-http--swagger-trigger"></a>添加 HTTP + 搖曳觸發器

此內置觸發器向 Swagger 檔的 URL 發送 HTTP 要求，該檔描述 REST API 並返回包含該檔內容的回應。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在邏輯應用設計器中打開空白邏輯應用。

1. 在設計器上，在搜索框中，輸入"搖號"作為篩選器。 從**觸發器**清單中選擇**HTTP + 搖曳觸發器**。

   ![選擇 HTTP + 搖曳觸發器](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 在 **"斯瓦格終端 URL"** 框中，輸入斯瓦格檔的 URL，然後選擇 **"下一步**"。

   此示例使用位於美國西部區域的 Swagger URL 用於[認知服務面部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入斯瓦格終結點的 URL](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 當設計器顯示斯瓦格檔描述的操作時，選擇要使用的操作。

   ![斯瓦格檔中的操作](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 提供要包含在終結點調用中的觸發器參數的值，這些參數因所選操作而異。 設置定期事件，以便觸發器調用終結點的頻率。

   本示例將觸發器重命名為"HTTP + 搖擺觸發器：面 - 檢測"，以便步驟具有更具描述性的名稱。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 要添加其他可用參數，請打開 **"添加新參數**清單"，然後選擇所需的參數。

   有關可用於 HTTP + 斯瓦格的身份驗證類型的詳細資訊，請參閱[將身份驗證添加到出站調用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 完成後，請記住保存邏輯應用。 在設計工具的工具列上，選取 [儲存]****。

## <a name="add-an-http--swagger-action"></a>添加 HTTP + 搖曳操作

此內置操作對 Swagger 檔的 URL 發出 HTTP 要求，該檔描述 REST API 並返回包含該檔內容的回應。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在要添加 HTTP + Swagger 操作的步驟下，選擇 **"新建步驟**"。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在設計器上，在搜索框中，輸入"搖號"作為篩選器。 從 **"操作"** 清單中，選擇**HTTP + 搖曳操作**。

    ![選取 HTTP + Swagger 動作](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 在 **"斯瓦格終端 URL"** 框中，輸入斯瓦格檔的 URL，然後選擇 **"下一步**"。

   此示例使用位於美國西部區域的 Swagger URL 用於[認知服務面部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入斯瓦格終結點的 URL](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 當設計器顯示斯瓦格檔描述的操作時，選擇要使用的操作。

   ![斯瓦格檔中的操作](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 提供要包含在終結點調用中的指令引數的值，這些參數因所選操作而異。

   此示例沒有參數，但將操作重命名為"HTTP + 搖擺操作：面 - 識別"，以便步驟具有更具描述性的名稱。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 要添加其他可用參數，請打開 **"添加新參數**清單"，然後選擇所需的參數。

   有關可用於 HTTP + 斯瓦格的身份驗證類型的詳細資訊，請參閱[將身份驗證添加到出站調用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成後，請記住保存邏輯應用。 在設計工具的工具列上，選取 [儲存]****。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Azure 存儲中的主機搖曳

通過將該檔上載到 Azure 存儲帳戶中的 Blob 容器並在該存儲帳戶上啟用 CORS，可以引用未託管或不符合安全和跨源要求的 Swagger 檔。 要在 Azure 存儲中創建、設置和存儲斯瓦格檔，請按照以下步驟操作：

1. [創建 Azure 存儲帳戶](../storage/common/storage-create-storage-account.md)。

1. 現在為 blob 啟用 CORS。 在存儲帳戶的功能表上，選擇**CORS**。 在**Blob 服務**選項卡上，指定這些值，然後選擇 **"保存**"。

   | 屬性 | 值 |
   |----------|-------|
   | **允許的原始來源** | `*` |
   | **允許的方法** | `GET`, `HEAD`, `PUT` |
   | **允許的標頭** | `*` |
   | **公開的標頭** | `*` |
   | **最大年齡**（以秒為單位） | `200` |
   |||

   儘管此示例使用[Azure 門戶](https://portal.azure.com)，但可以使用[Azure 存儲資源管理器](https://storageexplorer.com/)等工具，或者使用此示例[PowerShell 腳本](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)自動設定此設置。

1. [創建 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 在容器的 **"概述"** 窗格中，選擇 **"變更存取層級**"。 從**公共存取層級**清單中，選擇**Blob（僅限 Blob 的匿名讀取存取），** 然後選擇 **"確定**"。

1. 通過[Azure 門戶](https://portal.azure.com)或[Azure 存儲資源管理器](https://storageexplorer.com/)[將 Swagger 檔上載到 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)。

1. 要引用 Blob 容器中的檔，請使用遵循此格式的 HTTPS 連結，該格式區分大小寫：

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>連接器參考

以下是有關 HTTP + Swagger 觸發器或操作的輸出的詳細資訊。 HTTP + Swagger 調用返回此資訊：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| headers | 物件 (object) | 請求中的標頭 |
| body | 物件 (object) | JSON 物件 | 具有請求中正文內容的物件 |
| 狀態碼 | int | 請求中的狀態碼 |
|||

| 狀態碼 | 描述 |
|-------------|-------------|
| 200 | [確定] |
| 202 | 已接受 |
| 400 | 不正確的要求 |
| 401 | 未經授權 |
| 403 | 禁止 |
| 404 | 找不到 |
| 500 | 內部伺服器錯誤。 發生未知錯誤。 |
|||

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
