---
title: 從 Azure Logic Apps 連線到 REST 端點
description: 使用 Azure Logic Apps，在自動執行的工作、處理序和工作流程中監視 REST 端點
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: cf32938b534272a13af5891d6a31e64b8136a528
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281458"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 呼叫 REST 端點

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和內建的 HTTP + Swagger 連接器，您就可以藉由建置邏輯應用程式，透過 [Swagger 檔案](https://swagger.io) \(英文\)，將定期呼叫任何 REST 端點的工作流程自動化。 HTTP + Swagger 觸發程序和動作的運作方式與 [HTTP 觸發程序和動作](connectors-native-http.md)相同，但能夠藉由公開 Swagger 檔案所描述的 API 結構和輸出，在邏輯應用程式設計工具中提供更好的體驗。 若要實作輪詢觸發程序，請遵循[建立自訂 API 來呼叫邏輯應用程式的其他 API、服務和系統](../logic-apps/logic-apps-create-api-app.md#polling-triggers)中所述的輪詢模式。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 適用於描述目標 REST 端點之 Swagger (非 OpenAPI) 檔案的 URL

  一般而言，REST 端點必須符合此準則，連接器才能運作：

  * Swagger 檔案必須裝載於可公開存取的 HTTPS URL 上。

  * Swagger 檔案必須已啟用[跨原始來源資源共用 (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) \(部分機器翻譯\)。

  若要參考未裝載或不符合安全性和跨原始來源需求的 Swagger 檔案，您可以[將 Swagger 檔案上傳到 Azure 儲存體帳戶中的 Blob 容器](#host-swagger)，並在該儲存體帳戶上啟用 CORS，如此就能參考檔案。

  此主題中的範例會使用[認知服務臉部 API](../cognitive-services/face/overview.md)，其需要[認知服務帳戶和存取金鑰](../cognitive-services/cognitive-services-apis-create-account.md)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

* 您想要從中呼叫目標端點的邏輯應用程式。 若要開始使用 HTTP + Swagger 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP + Swagger 動作，使用您所需的任何觸發程序來啟動邏輯應用程式。 這個範例會使用 HTTP + Swagger 觸發程序作為第一個步驟。

## <a name="add-an-http--swagger-trigger"></a>新增 HTTP + Swagger 觸發程序

此內建觸發程序會將 HTTP 要求傳送至描述 REST API 的 Swagger 檔案的 URL，並傳回包含該檔案內容的回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟您的空白邏輯應用程式。

1. 在設計工具的搜尋方塊中，輸入 "swagger" 作為篩選條件。 從 [觸發程序] 清單中，選取 [HTTP + Swagger] 觸發程序。

   ![選取 HTTP + Swagger 觸發程序](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 在 [SWAGGER 端點 URL] 方塊中，輸入 Swagger 檔案的 URL，然後選取 [下一步]。

   這個範例會針對[認知服務臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) \(英文\)，使用位於美國西部區域的 Swagger URL：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入 Swagger 端點的 URL](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 當設計工具顯示 Swagger 檔案所描述的作業時，選取您要使用的作業。

   ![Swagger 檔案中的作業](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 提供觸發程序參數的值，其會根據您想要包含在端點呼叫中的所選作業而不同。 設定您想要觸發程序呼叫端點的頻率週期。

   這個範例會將觸發程序重新命名為 "HTTP + Swagger trigger:Face - Detect"，因而可讓步驟的名稱更具描述性。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 若要新增其他可用參數，開啟 [新增參數] 清單，然後選取您所需的參數。

   如需適用於 HTTP + Swagger 的驗證類型詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

## <a name="add-an-http--swagger-action"></a>新增 HTTP + Swagger 動作

此內建動作會將對描述 REST API 之 Swagger 檔案的 URL 提出 HTTP 要求，並傳回包含該檔案內容的回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在您要新增 HTTP + Swagger 動作的步驟底下，選取 [新增步驟]。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇所顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在設計工具的搜尋方塊中，輸入 "swagger" 作為篩選條件。 從 [動作] 清單中，選取 [HTTP + Swagger] 動作。

    ![選取 HTTP + Swagger 動作](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 在 [SWAGGER 端點 URL] 方塊中，輸入 Swagger 檔案的 URL，然後選取 [下一步]。

   這個範例會針對[認知服務臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) \(英文\)，使用位於美國西部區域的 Swagger URL：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入 Swagger 端點的 URL](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 當設計工具顯示 Swagger 檔案所描述的作業時，選取您要使用的作業。

   ![Swagger 檔案中的作業](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 提供動作參數的值，其會根據您想要包含在端點呼叫中的所選作業而不同。

   這個範例沒有參數，但會將動作重新命名為 "HTTP + Swagger action:Face - Identify"，因而可讓步驟的名稱更具描述性。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 若要新增其他可用參數，開啟 [新增參數] 清單，然後選取您所需的參數。

   如需適用於 HTTP + Swagger 的驗證類型詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>在 Azure 儲存體中裝載 Swagger

您可以藉由將 Swagger 檔案上傳到 Azure 儲存體帳戶中的 Blob 容器，並在該儲存體帳戶上啟用 CORS，來參考未裝載或不符合安全性和跨原始來源需求的 Swagger 檔案。 若要在 Azure 儲存體中建立、設定和儲存 Swagger 檔案，請遵循下列步驟：

1. [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)。

1. 現在針對 Blob 啟用 CORS。 在您的儲存體帳戶功能表上，選取 [CORS]。 在 [Blob 服務] 索引標籤上，指定這些值，然後選取 [儲存]。

   | 屬性 | 值 |
   |----------|-------|
   | **允許的原始來源** | `*` |
   | **允許的方法** | `GET`、`HEAD`、`PUT` |
   | **允許的標頭** | `*` |
   | **公開的標頭** | `*` |
   | **存留期上限** (秒) | `200` |
   |||

   儘管此範例會使用 [Azure 入口網站](https://portal.azure.com)，但您還是可以使用 [Azure 儲存體總管](https://storageexplorer.com/)之類的工具，或使用此範例 [PowerShell 指令碼](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)來自動設定此設定。

1. [建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 在容器的 [概觀] 窗格中，選取 [變更存取層級]。 從 [公用存取層級] 清單中，選取 [Blob (僅限 blob 的匿名讀取權限)]，然後選取 [確定]。

1. 透過 [Azure 入口網站](https://portal.azure.com)或 [Azure 儲存體總管](https://storageexplorer.com/)，[將 Swagger 檔案上傳至 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)。

1. 若要參考 Blob 容器中的檔案，請從 Azure 儲存體總管中取得遵循此格式的 HTTPS URL (區分大小寫)：

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>連接器參考

以下是來自 HTTP + Swagger 觸發程序或動作之輸出的詳細資訊。 HTTP + Swagger 呼叫會傳回下列資訊：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| headers | 物件 (object) | 要求的標頭 |
| body | 物件 (object) | JSON 物件 | 具有來自要求之本文內容的物件 |
| 狀態碼 | int | 要求的狀態碼 |
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

