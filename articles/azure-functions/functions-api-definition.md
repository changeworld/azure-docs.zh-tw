---
title: Azure Functions 中的 OpenAPI 中繼資料
description: Azure Functions 中的 OpenAPI 支援概觀
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385818"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions 中的 OpenAPI 2.0 中繼資料支援 (預覽)
Azure Functions 中的 OpenAPI 2.0 (先前稱為 Swagger) 中繼資料支援是預覽功能，可讓您用來撰寫函式應用程式內的 OpenAPI 2.0 定義。 接著您可以使用函式應用程式裝載該檔案。

> [!IMPORTANT]
> 目前只有 1.x 執行階段中有提供 OpenAPI 預覽功能。 如需有關如何建立 1.x 函數應用程式的資訊，請[參閱這裡](./functions-versions.md#creating-1x-apps)。

[OpenAPI 中繼資料](https://swagger.io/)可讓裝載 REST API 的函式供其他各種軟體使用。 此軟體包含諸如 PowerApps 和 [Azure App Service 的 API Apps 功能](../app-service/overview.md)等 Microsoft 供應項目、諸如 [Postman](https://www.getpostman.com/docs/importing_swagger) 等第三方開發人員工具，以及[許多其他套件](https://swagger.io/tools/)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>建議您從[入門教學課程](./functions-openapi-definition.md)開始，然後再回到此文件學習更多特定的功能。

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>啟用 OpenAPI 定義支援
您可以在函式應用程式設定的**平台功能**中的 **API 定義**頁面上，設定所有 OpenAPI 設定。

> [!NOTE]
> 搶鮮版 (Beta) 執行階段目前不支援函式 API 定義功能。

若要允許產生託管的 OpenAPI 定義和快速入門定義，請將 **API 定義來源** 設定為**函式 (預覽)**。 **外部 URL** 可讓您的函式使用裝載在其他位置的 OpenAPI 定義。

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a> 從您的函式中繼資料產生 Swagger 基本架構
範本可協助您開始撰寫第一個 OpenAPI 定義。 定義範本功能會針對每個 HTTP 觸發程序函式，使用 function.json 中所有的中繼資料來建立鬆散的 OpenAPI 定義。 您會需要從 [OpenAPI 規格](https://swagger.io/specification/)填入 API 的更多相關資訊，例如要求和回應範本。

如需逐步指示，請參閱[快速入門教學課程](./functions-openapi-definition.md)。

### <a name="available-templates"></a><a name="templates"></a>可用範本

|名稱| 描述 |
|:-----|:-----|
|已產生的定義|具有可從函式現有中繼資料推斷之最大數量資訊的 OpenAPI 定義。|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>產生的定義中所包含之中繼資料

下表顯示 Azure 入口網站設定和 function.json 中相對應的資料，如同對應到所產生的 Swagger 基本架構。

|Swagger.json|入口網站 UI|Function.json|
|:----|:-----|:-----|
|[主控件](https://swagger.io/specification/#fixed-fields-15)|**函數應用程式設定**  > **App Service 設定**  > **總覽**  > **URL**|*不存在*
|[路徑](https://swagger.io/specification/#paths-object-29)|**整合**  > **選取的 HTTP 方法**|繫結：路由
|[Path Item](https://swagger.io/specification/#path-item-object-32)|**整合**  > **路由範本**|繫結：方法
|[安全性](https://swagger.io/specification/#security-scheme-object-112)|**[索引鍵]**|*不存在*|
|operationID*|**路由 + 允許的動詞**|路由 + 允許的動詞|

\*只有與 PowerApps 和 Flow 整合才需要作業識別碼。
> [!NOTE]
> x-ms-summary 擴充提供 Logic Apps、Power 和 AppsFlow 中的顯示名稱。
>
> 若要進一步了解，請參閱[為 PowerApps 自訂 Swagger 定義](/connectors/custom-connectors/openapi-extensions)。

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>使用 CI/CD 設定 API 定義

 從原始檔控制啟用讓原始檔控制修改 API 定義之前，您必須先啟用裝載在入口網站中的 API 定義。 遵循下列指示：

1. 瀏覽至函式應用程式設定中的 **API 定義 (預覽)**。
   1. 將 **API 定義來源**設定為 **Function**。
   1. 依序按一下 [產生 API 定義範本]****、[儲存]**** 可建立範本定義以供稍後修改。
   1. 請注意您的 API 定義 URL 和金鑰。
1. [設定持續整合/持續部署 (CI/CD)](./functions-continuous-deployment.md#requirements-for-continuous-deployment)。
2. 在原始檔控制中的 \site\wwwroot\.azurefunctions\swagger\swagger.json 修改 swagger.json。

現在，您對存放庫中的 swagger.json 所進行之變更，會由您在步驟 1.c 中記下的 API 定義 URL 及金鑰之函式應用程式進行裝載。

## <a name="next-steps"></a>後續步驟
* [入門教學](./functions-openapi-definition.md)課程。 請嘗試我們的逐步解說，以查看 OpenAPI 定義實際運作。
* [Azure Functions GitHub 存放庫](https://github.com/Azure/Azure-Functions/)。 查看 Functions 存放庫以提供我們 API 定義支援預覽的意見反應。 針對您想在更新看到的任何功能提出 GitHub 問題。
* [Azure Functions 開發人員參考](functions-reference.md)。 深入了解如何撰寫函式程式碼及定義觸發程序和繫結。
