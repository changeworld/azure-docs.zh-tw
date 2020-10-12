---
title: 如何使用使用者登入來保護單一頁面應用程式
titleSuffix: Azure Maps
description: 如何設定單一頁面應用程式，以支援 Azure 地圖服務 Web SDK Azure AD 單一登入。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 0ba2e23e8121a76ec281b5e411819ee7d450cbe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319685"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>使用使用者登入來保護單一頁面應用程式

下列指南適用于裝載于內容伺服器上的應用程式，或具有基本 web 伺服器相依性的應用程式。 應用程式只會提供保護的資源給 Azure AD 使用者。 此案例的目標是要讓 web 應用程式能夠驗證 Azure AD，並代表使用者呼叫 Azure 地圖服務 REST Api。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中建立應用程式註冊

在 Azure AD 中建立 web 應用程式以供使用者登入。 Web 應用程式會將使用者存取權委派給 Azure 地圖服務 REST Api。

1. 在 Azure 入口網站的 Azure 服務清單中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

2. 輸入 **名稱**、選擇 **支援帳戶類型**、提供重新導向 URI，以代表 Azure AD 將發出權杖的 url，以及裝載地圖控制項的 url。 如需詳細範例，請參閱 [Azure 地圖服務 Azure AD 範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)。 然後，選取 [註冊]。  

3. 若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後，在 [**應用程式註冊**] 下，選取 [ **API 許可權**]  >  **新增許可權**。 在 [ **我的組織使用的 api**] 下，搜尋並選取 [ **Azure 地圖服務**]。

    > [!div class="mx-imgBorder"]
    > ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

4. 選取 [ **存取 Azure 地圖服務**旁的核取方塊，然後選取 [ **新增許可權**]。

    > [!div class="mx-imgBorder"]
    > ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

5. 啟用 `oauth2AllowImplicitFlow`。 若要啟用此功能，請在應用程式註冊的 [ **資訊清單** ] 區段中，將設定 `oauth2AllowImplicitFlow` 為 `true` 。

6. 從應用程式註冊複製 Azure AD 的應用程式識別碼和 Azure AD 租使用者識別碼，以在 Web SDK 中使用。 從 Azure 地圖帳戶將 Azure AD 應用程式註冊詳細資料和新增 `x-ms-client-id` 至 WEB SDK。

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

7. 為使用者或群組設定 Azure 角色型存取控制。 [若要啟用 RBAC，請參閱下列各節](#grant-role-based-access-for-users-to-azure-maps)。
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>後續步驟

進一步瞭解單一頁面應用程式案例：
> [!div class="nextstepaction"]
> [單一頁面應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

尋找您 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何整合 Azure AD 與 Azure 地圖服務的範例：
> [!div class="nextstepaction"]
> [Azure 地圖服務範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
