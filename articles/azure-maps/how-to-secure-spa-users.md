---
title: 如何使用使用者登入保護單一頁面應用程式
titleSuffix: Azure Maps
description: 如何設定單一頁面應用程式，以支援 Azure 地圖服務 Web SDK 的 Azure AD 單一登入。
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 307f1ea8d98aec8594dd4f666d4e18922122b0f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988333"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>使用使用者登入保護單一頁面應用程式

下列指南適用于裝載于內容伺服器上或具有最低 web 伺服器相依性的應用程式。 應用程式只會提供保護的資源給 Azure AD 的使用者。 此案例的目標是要讓 web 應用程式向 Azure AD 進行驗證，並代表使用者呼叫 Azure 地圖服務 REST Api。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中建立應用程式註冊

在 Azure AD 中建立 web 應用程式，供使用者登入。 Web 應用程式會將使用者存取權委派給 Azure 地圖服務 REST Api。

1. 在 [Azure 入口網站] 的 [Azure 服務] 清單中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新增註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

2. 輸入 [**名稱**]、[選擇**支援帳戶類型**]、[提供重新導向 URI]，其中會代表 Azure AD 將會發出權杖的 url，而是裝載地圖控制項的 url。 如需詳細範例，請參閱[Azure 地圖服務 Azure AD 範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)。 然後，選取 [註冊]。  

3. 若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後在 [**應用程式註冊**] 底下，選取 [ **API 許可權**] [  >  **新增許可權**]。 在 [**我的組織使用的 api**] 底下，搜尋並選取 [ **Azure 地圖服務**]。

    > [!div class="mx-imgBorder"]
    > ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

4. 選取 [**存取 Azure 地圖服務**] 旁的核取方塊，然後選取 [**新增許可權**]。

    > [!div class="mx-imgBorder"]
    > ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

5. 啟用 `oauth2AllowImplicitFlow`。 若要啟用它，請在應用程式註冊的 [**資訊清單**] 區段中，將設定 `oauth2AllowImplicitFlow` 為 `true` 。

6. 從應用程式註冊複製 Azure AD 應用程式識別碼和 Azure AD 租使用者識別碼，以用於 Web SDK。 將 Azure AD 應用程式註冊詳細資料和 `x-ms-client-id` Azure 地圖帳戶中的新增至 WEB SDK。

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. 為使用者或群組設定以 Azure 角色為基礎的存取控制。 請參閱[下列各節以啟用 RBAC](#grant-role-based-access-for-users-to-azure-maps)。
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>後續步驟

進一步瞭解單一頁面應用程式案例：
> [!div class="nextstepaction"]
> [單一頁面應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

尋找 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何將 Azure AD 與 Azure 地圖服務整合的範例：
> [!div class="nextstepaction"]
> [Azure 地圖服務範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
