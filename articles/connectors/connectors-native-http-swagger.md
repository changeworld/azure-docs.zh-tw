---
title: 從 Azure Logic Apps 連接到 REST 端點
description: 使用 Azure Logic Apps 監視自動化工作、進程和工作流程中的 REST 端點
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74787364"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 呼叫 REST 端點

使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和內建的 HTTP + Swagger 連接器，您可以藉由建立邏輯應用程式，將定期透過[Swagger](https://swagger.io)檔案呼叫任何 REST 端點的工作流程自動化。 HTTP + Swagger 觸發程式和動作的運作方式與[HTTP 觸發程式和動作](connectors-native-http.md)相同，但會公開 Swagger 檔案所描述的 API 結構和輸出，以在邏輯應用程式設計工具中提供更好的體驗。 若要執行輪詢觸發程式，請依照建立自訂 Api 中所述的輪詢模式，[從邏輯應用程式呼叫其他 api、服務和系統](../logic-apps/logic-apps-create-api-app.md#polling-triggers)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於描述目標 REST 端點的 Swagger （非 OpenAPI）檔案的 URL

  一般而言，REST 端點必須符合此條件，連接器才能正常執行：

  * Swagger 檔案必須裝載在可公開存取的 HTTPS URL 上。

  * Swagger 檔案必須啟用[跨原始來源資源分享（CORS）](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 。

  若要參考未裝載或不符合安全性和跨原始來源需求的 Swagger 檔案，您可以將 Swagger 檔案上[傳到 Azure 儲存體帳戶中的 blob 容器](#host-swagger)，並在該儲存體帳戶上啟用 CORS，讓您可以參考該檔案。

  本主題中的範例會使用[認知服務臉部 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)，這需要[認知服務帳戶和存取金鑰](../cognitive-services/cognitive-services-apis-create-account.md)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

* 您想要從中呼叫目標端點的邏輯應用程式。 若要開始使用 HTTP + Swagger 觸發程式，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP + Swagger 動作，請使用您想要的任何觸發程式來啟動邏輯應用程式。 這個範例會使用 HTTP + Swagger 觸發程式做為第一個步驟。

## <a name="add-an-http--swagger-trigger"></a>新增 HTTP + Swagger 觸發程式

此內建觸發程式會將 HTTP 要求傳送至 Swagger 檔案的 URL，該檔案會描述 REST API 並傳回包含該檔案內容的回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在設計工具的 [搜尋] 方塊中，輸入 "swagger" 作為篩選準則。 從**觸發**程式清單中，選取 [ **HTTP + Swagger** ] 觸發程式。

   ![選取 HTTP + Swagger 觸發程式](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 在 [ **SWAGGER 端點 Url** ] 方塊中，輸入 swagger 檔案的 URL，然後選取 **[下一步]**。

   這個範例會使用位於美國西部區域的 Swagger URL 來進行[認知服務臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入 Swagger 端點的 URL](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 當設計工具顯示 Swagger 檔案所描述的作業時，請選取您要使用的操作。

   ![Swagger 檔案中的作業](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 根據您想要包含在端點呼叫中的所選作業，提供觸發程式參數的值。 針對您想要讓觸發程式呼叫端點的頻率，設定週期。

   這個範例會將觸發程式重新命名為「HTTP + Swagger 觸發程式：臉部-偵測」，讓步驟有更具描述性的名稱。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 若要加入其他可用的參數，請開啟 [**加入新的參數**] 清單，然後選取您想要的參數。

   如需可用於 HTTP + Swagger 之驗證類型的詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

## <a name="add-an-http--swagger-action"></a>新增 HTTP + Swagger 動作

這個內建動作會對 Swagger 檔案的 URL 提出 HTTP 要求，該檔案會描述 REST API 並傳回包含該檔案內容的回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在您要新增 HTTP + Swagger 動作的步驟底下，選取 [**新增步驟**]。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選取顯示的加號（**+**），然後選取 [**新增動作**]。

1. 在設計工具的 [搜尋] 方塊中，輸入 "swagger" 作為篩選準則。 從 [**動作**] 清單中，選取 [ **HTTP + Swagger** ] 動作。

    ![選取 HTTP + Swagger 動作](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 在 [ **SWAGGER 端點 Url** ] 方塊中，輸入 swagger 檔案的 URL，然後選取 **[下一步]**。

   這個範例會使用位於美國西部區域的 Swagger URL 來進行[認知服務臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入 Swagger 端點的 URL](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 當設計工具顯示 Swagger 檔案所描述的作業時，請選取您要使用的操作。

   ![Swagger 檔案中的作業](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 針對您想要包含在端點呼叫中的動作參數，提供其值（根據選取的作業而有所不同）。

   這個範例沒有參數，但會將動作重新命名為「HTTP + Swagger 動作：臉部-識別」，讓步驟有更具描述性的名稱。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 若要加入其他可用的參數，請開啟 [**加入新的參數**] 清單，然後選取您想要的參數。

   如需可用於 HTTP + Swagger 之驗證類型的詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>在 Azure 儲存體中裝載 Swagger

將該檔案上傳至 Azure 儲存體帳戶中的 blob 容器，並在該儲存體帳戶上啟用 CORS，即可參考未裝載或不符合安全性和跨原始來源需求的 Swagger 檔案。 若要在 Azure 儲存體中建立、設定和儲存 Swagger 檔案，請遵循下列步驟：

1. [建立 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)。

1. 現在啟用 blob 的 CORS。 在儲存體帳戶的功能表上，選取 [ **CORS**]。 在 [ **Blob 服務**] 索引標籤上，指定這些值，然後選取 [**儲存**]。

   | 屬性 | 值 |
   |----------|-------|
   | **允許的原始來源** | `*` |
   | **允許的方法** | `GET`, `HEAD`, `PUT` |
   | **允許的標頭** | `*` |
   | **公開的標頭** | `*` |
   | **最長使用期限**（以秒為單位） | `200` |
   |||

   雖然此範例使用[Azure 入口網站](https://portal.azure.com)，但您可以使用[Azure 儲存體總管](https://storageexplorer.com/)之類的工具，或使用此範例[PowerShell 腳本](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)自動設定此設定。

1. [建立 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 在容器的 **[總覽**] 窗格中，選取 [**變更存取層級**]。 從 [**公用存取層級**] 清單中，選取 [ **blob （僅限 blob 的匿名讀取存取）**]，然後選取 **[確定]**。

1. 將[Swagger 檔案上傳至 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)，方法是透過[Azure 入口網站](https://portal.azure.com)或[Azure 儲存體總管](https://storageexplorer.com/)。

1. 若要參考 blob 容器中的檔案，請使用遵循此格式的 HTTPS 連結，這會區分大小寫：

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>連接器參考

以下是來自 HTTP + Swagger 觸發程式或動作之輸出的詳細資訊。 HTTP + Swagger 呼叫會傳回下列資訊：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| headers | 物件 | 要求的標頭 |
| body | 物件 | JSON 物件 | 具有來自要求之本文內容的物件 |
| 狀態碼 | int | 來自要求的狀態碼 |
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
