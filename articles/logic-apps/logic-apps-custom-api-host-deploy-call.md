---
title: 呼叫自訂 Web API 和 REST API
description: 從 Azure Logic Apps 呼叫您自己的 Web API 和 REST API
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659782"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>在 Azure Logic Apps 中從工作流程部署和呼叫自訂 API

在[建立您自己的 API](./logic-apps-create-api-app.md) 供邏輯應用程式工作流程使用之後，您必須先部署這些 API，才能呼叫。 您可以將 API 部署為 [web 應用程式](../app-service/overview.md)，但請考慮將您的 API 部署為 [API 應用程式](../app-service/app-service-web-tutorial-rest-api.md)，如此一來，當您在雲端中及內部部署建置、裝載並自訂 API 時，可讓您的作業更容易。 您不需要在 API 中變更任何程式碼 - 只需將您的程式碼部署至 API 應用程式。 您可以將 API 裝載在 [Azure App Service](../app-service/overview.md) 上，這是一個平台即服務 (PaaS) 供應項目，提供擴充性高且簡便的 API 裝載服務。

雖然您可以從邏輯應用程式呼叫任何 API，但為了獲得最佳體驗，請新增 [Swagger 中繼資料](https://swagger.io/specification/)來描述 API 的作業和參數。 此 Swagger 文件有助於 API 更輕鬆整合、更有效搭配邏輯應用程式。

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>將您的 API 部署為 web 應用程式或 API 應用程式

請先將您的 API 部署為 web 應用程式或 API 應用程式到 Azure App Service 後，才能從邏輯應用程式呼叫自訂 API。 若要讓 Logic Apps 設計工具讀取您的 Swagger 文件，請為您的 Web 應用程式或 API 應用程式，設定 API 定義屬性並啟用[跨原始資源共用 (CORS)](../app-service/overview.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取您的 Web 應用程式或 API 應用程式。

2. 在開啟的應用程式功能表之 [API] 下方，選擇 [API 定義]。 將 [API 定義位置] 設定為 swagger.json 檔案的 URL。

   通常，URL 會以這種格式出現：`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![自訂 API 的 Swagger 文件連結](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. 在 [API] 下，選擇 [CORS]。 將 [允許的來源] 的 CORS 原則設定為 **'*'** (全部允許)。

   此設定允許來自 Logic App Designer 的要求。

   ![允許來自 Logic App Designer 對您自訂 API 的要求](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

如需詳細資訊，請參閱[在 Azure App Service 中裝載具有 CORS 支援的 RESTful API](../app-service/app-service-web-tutorial-rest-api.md)。

## <a name="call-your-custom-api-from-logic-app-workflows"></a>從邏輯應用程式工作流程呼叫自訂 API

在您設定 API 定義屬性和 CORS 之後，您自訂 API 的觸發程序和動作應該就可供您用來包含在邏輯應用程式工作流程中。 

*  若要檢視具有 OpenAPI URL 的網站，您可以在 Logic Apps Designer 中瀏覽訂用帳戶網站。

*  若要藉由指向 Swagger 文件來檢視可用的動作和輸入，請使用 [HTTP + Swagger 動作](../connectors/connectors-native-http-swagger.md)。

*  若要呼叫任何 API，包括沒有或未公開 Swagger 文件的 API，您一律可以使用 [HTTP 動作](../connectors/connectors-native-http.md)建立要求。

## <a name="next-steps"></a>後續步驟

* [自訂連接器概觀](../logic-apps/custom-connector-overview.md)
