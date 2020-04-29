---
title: 如何使用參考 Swagger 檔-Azure 數位 Twins |Microsoft Docs
description: 了解如何使用 Azure Digital Twins Swagger 參考文件。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023288"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure Digital Twins Swagger 參考文件

每個已佈建的 Azure Digital Twins 執行個體都包含本身自動產生的 Swagger 參考文件。

[Swagger](https://swagger.io/) \(英文\) (或 [OpenAPI](https://www.openapis.org/) \(英文\)) 會將複雜的 API 資訊結合至可互動且與語言無關的參考資源。 若要對 API 執行作業，Swagger 會針對要使用的 JSON 承載、HTTP 方法和特定端點提供重要的參考資料。

## <a name="swagger-summary"></a>Swagger 摘要

Swagger 會提供可互動的 API 摘要，包括：

* API 和物件模型資訊。
* REST API 端點，會指定必要的要求承載、標頭、參數、內容路徑和 HTTP 方法。
* API 功能測試。
* 用於驗證和確認 HTTP 回應的範例回應資訊。
* 錯誤碼資訊。

Swagger 是一個很便利的工具，可協助您開發和測試對 Azure Digital Twins 管理 API 所發出的呼叫。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>參考資料

自動產生的 Swagger 參考資料提供重要概念的快速概觀、可用的管理 API 端點，以及每個物件模型的說明，以協助執行開發和測試工作。

透過簡單摘要來說明 API。

[![Swagger 摘要和 API 總覽資訊](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

此外，也會列出管理 API 物件模型。

[![在 Swagger UI 底部列出的 swagger 模型](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

您可以選取每個列出的物件模型，以取得更詳細的主要屬性摘要。

[![擴充以讀取模型內容的 Swagger 模型](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

產生的 Swagger 物件模型可方便讀取所有可用的 Azure 數位 Twins[物件和 api](./concepts-objectmodel-spatialgraph.md)。 開發人員可在於 Azure Digital Twins 上建置解決方案時使用此資源。

## <a name="endpoint-summary"></a>端點摘要

Swagger 也提供管理 API 中所有端點的整體概觀。

每個列出的端點也會包含必要的要求資訊，例如：

* 必要參數。
* 必要參數資料類型。
* 要存取資源的 HTTP 方法。

[![Swagger UI 中顯示的 swagger 端點](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

選取每個資源以顯示其其他內容，以取得更詳細的總覽。

## <a name="use-swagger-to-test-endpoints"></a>使用 Swagger 來測試端點

Swagger 所提供的強大功能之一是直接透過文件 UI 來測試 API 端點。

選取特定端點之後，將會顯示 [立即**試用**] 按鈕。

[![Swagger 立即試用按鈕](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

展開該區段後，即會顯示每個必要和選擇性參數的輸入欄位。 輸入正確的值，然後選取 [執行]****。

[![Swagger 試用結果範例](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

當您執行測試之後，就可以驗證回應資料。

## <a name="swagger-response-data"></a>Swagger 回應資料

每個列出的端點也會包含回應主體資料，可用來驗證您的開發和測試。 這些範例包括成功的 HTTP 要求的狀態碼和 JSON。

[![Swagger JSON 回應範例](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

範例也會包含錯誤碼，可用來協助偵錯或改善失敗的測試。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 授權

> [!NOTE]
> * 建立 Azure 數位 Twins 資源的使用者主體將具有「空間管理員」角色指派，而且能夠為其他使用者建立其他角色指派。 這些使用者及其角色可以獲得授權，以呼叫 Api。

1. 請依照[快速入門](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app)中的步驟來建立和設定 Azure Active Directory 應用程式。 或者，您可以重複使用現有的應用程式註冊。

1. 將下列重新**導向 URI**新增至您的 Azure Active Directory 應用程式註冊：

    [![在 AAD 中註冊 Swagger 重新導向 url](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | 更換為 | 範例 |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | 您的管理 REST API 在入口網站中找到的檔 URL  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. 選取 [**隱含授** > 與**存取權杖**] 核取方塊，以允許使用 OAuth 2.0 隱含授與流程。 選取 [**設定**]，然後按一下 [**儲存**]。

1. 複製 Azure Active Directory 應用程式的**用戶端識別碼**。

完成 Azure Active Directory 註冊之後：

1. 選取 swagger 頁面上的 [**授權**] 按鈕。

    [![選取 [Swagger 授權] 按鈕](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. 將 [應用程式識別碼] 貼到 [ **client_id** ] 欄位中。

    [![Swagger client_id 欄位](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. 然後，系統會將您重新導向至下列成功模式。

    [![Swagger 重新導向模式](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

若要深入瞭解以互動方式測試受 OAuth 2.0 保護的要求，請參閱[官方檔](https://swagger.io/docs/specification/authentication/oauth2/)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure Digital Twins 物件模型和空間智慧圖表，請參閱[了解 Azure Digital Twins 物件模型](./concepts-objectmodel-spatialgraph.md)。

- 若要了解如何使用管理 API 進行驗證，請參閱[使用 API 進行驗證](./security-authenticating-apis.md)。
