---
title: 如何使用非互動式登入保護單一頁面應用程式
titleSuffix: Azure Maps
description: 如何使用非互動式 Azure AD 角色型存取控制和 Azure 地圖服務 Web SDK 來設定單一頁面應用程式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: e49954065f8a4ec03eb54d7333a3fff34bafb143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319651"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>如何使用非互動式登入保護單一頁面應用程式

下列指南適用于使用 Azure Active Directory (Azure AD) 在使用者無法登入 Azure 地圖服務時提供存取權杖，以 Azure AD 應用程式的應用程式。 此流程需要裝載 web 服務，而該服務必須受到保護，只能由單一頁面 web 應用程式存取。 有多個可以完成驗證 Azure AD 的執行。 本指南會利用產品（Azure Function）來取得存取權杖。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure 地圖服務可以支援來自使用者登入/互動式流程的存取權杖。 互動式流程可讓存取權撤銷和密碼管理的範圍更受限制。

## <a name="create-azure-function"></a>建立 Azure 函式

建立安全的 web 服務應用程式，以負責 Azure AD 的驗證。 

1. 在 Azure 入口網站中建立函數。 如需詳細資訊，請參閱 [建立 Azure Function](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function)。

2. 在 Azure 函式上設定 CORS 原則，以供單一頁面 web 應用程式存取。 這會將瀏覽器用戶端安全地保護至 web 應用程式的允許來源。 請參閱 [新增 CORS 功能](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#add-cors-functionality)。

3. 在 Azure 函[式上新增系統指派](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)的身分識別，以允許建立服務主體以驗證 Azure AD。  

4. 將系統指派之身分識別的角色型存取權授與 Azure 地圖服務帳戶。 如需詳細資料，請參閱 [授與角色型存取權](#grant-role-based-access) 。

5. 撰寫 Azure 函式的程式碼，以使用系統指派的身分識別，搭配其中一個支援的機制或 REST 通訊協定來取得 Azure 地圖服務存取權杖。 請參閱 [取得 Azure 資源的權杖](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

    範例 REST 通訊協定範例：

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    範例回應：

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. 設定 Azure 函數 HttpTrigger 的安全性

   * [建立函數存取金鑰](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)
   * 在生產環境中保護 Azure function 的[HTTP 端點](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)。
   
7. Azure 地圖服務 Web SDK 設定 web 應用程式。 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>授與以角色為基礎的存取權

您可以藉由將系統指派的身分識別指派給一或多個 Azure 角色定義，來授與 *azure 角色型存取控制 (AZURE RBAC) * 存取權。 若要查看可用於 Azure 地圖服務的 Azure 角色定義，請移至 [ **存取控制] (IAM) **。 選取 [ **角色**]，然後搜尋以 *Azure 地圖服務*開頭的角色。

1. 移至您的 **Azure 地圖服務帳戶**。 選取 [**存取控制] (IAM) **  >  **角色指派**。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure RBAC 授與存取權](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [ **角色指派** ] 索引標籤的 [ **角色**] 底下，選取內建的 Azure 地圖服務角色定義，例如 **Azure 地圖服務資料讀取器** 或 **Azure 地圖服務資料參與者**。 在 [ **指派存取權**] 底下，選取 [ **函數應用程式**]。 依名稱選取主體。 然後選取 [儲存]。

   * 請參閱 [新增或移除角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)的詳細資料。

> [!WARNING]
> Azure 地圖服務內建角色定義可提供非常大的授權存取權給許多 Azure 地圖服務 REST Api。 若要限制 Api 存取的最小值，請參閱 [建立自訂角色定義，並將系統指派的身分識別指派](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) 給自訂角色定義。 這將會啟用應用程式存取 Azure 地圖服務所需的最低許可權。

## <a name="next-steps"></a>後續步驟

進一步瞭解單一頁面應用程式案例：
> [!div class="nextstepaction"]
> [單一頁面應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

尋找您 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何整合 Azure AD 與 Azure 地圖服務的其他範例：
> [!div class="nextstepaction"]
> [Azure 地圖服務範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
