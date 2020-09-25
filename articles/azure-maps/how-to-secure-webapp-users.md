---
title: 如何使用互動式單一登入保護 web 應用程式
titleSuffix: Azure Maps
description: 如何設定 web 應用程式，以使用 OpenID Connect 通訊協定來支援搭配 Azure 地圖服務 Web SDK 的 Azure AD 單一登入。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 50194341d1d34da4b02558461f532ae64b941b16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319617"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>使用使用者登入來保護 web 應用程式

下列指南適用于裝載在 web 伺服器上的應用程式、維護多個商務案例，以及部署至 web 伺服器。 應用程式必須將受保護的資源僅提供給 Azure AD 使用者的保護。 此案例的目標是要讓 web 應用程式能夠驗證 Azure AD，並代表使用者呼叫 Azure 地圖服務 REST Api。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中建立應用程式註冊

您必須在 Azure AD 中建立 web 應用程式，使用者才能登入。 此 web 應用程式接著會將使用者存取權委派給 Azure 地圖服務 REST Api。

1. 在 Azure 入口網站的 Azure 服務清單中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

2. 輸入 **名稱**、選擇 **支援帳戶類型**、提供重新導向 URI，以代表 Azure AD 將發出權杖的 url，以及裝載地圖控制項的 url。 如需詳細資訊，請參閱 Azure AD [案例：登入使用者的 Web 應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)。 從 Azure AD 案例中完成提供的步驟。  

3. 應用程式註冊完成後，請確認應用程式登入適用于使用者。 登入運作之後，應用程式就可以被授與 Azure 地圖服務 REST Api 的委派存取權。
    
4.  若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後選取 [ **API 許可權**]  >  **新增許可權**。 在 [ **我的組織使用的 api**] 下，搜尋並選取 [ **Azure 地圖服務**]。

    > [!div class="mx-imgBorder"]
    > ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

5. 選取 [ **存取 Azure 地圖服務**旁的核取方塊，然後選取 [ **新增許可權**]。

    > [!div class="mx-imgBorder"]
    > ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

6. 讓 web 應用程式使用應用程式密碼設定應用程式註冊，以呼叫 Azure 地圖服務 REST Api，如需詳細步驟，請參閱 [呼叫 Web api 的 web 應用程式：應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-app-registration)。 需要秘密，才能代表使用者對 Azure AD 進行驗證。 應用程式註冊憑證或秘密應儲存在安全存放區中，以供 web 應用程式抓取以向 Azure AD 進行驗證。 
   
   * 如果應用程式已設定 Azure AD 應用程式註冊和密碼，則可以略過此步驟。

> [!Tip]
> 如果應用程式裝載于 Azure 環境中，我們建議使用適用 [于 azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 識別，並透過取得 [存取權杖](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) 來存取 Azure Key Vault 秘密或憑證，以 Azure Key Vault 實例來存取秘密。 若要連線到 Azure Key Vault 以取得秘密，請參閱 [透過受控識別](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)連線的教學課程。
   
7. 針對 Azure 地圖服務 Web SDK，執行安全的權杖端點以存取權杖。 
   
   * 如需範例權杖控制器，請參閱 [Azure 地圖服務 Azure AD 範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs)。 
   * 若為非 AspNetCore 的執行或其他，請參閱 Azure AD 檔中 [的取得應用程式權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token) 。
   * 受保護的權杖端點負責傳回已驗證和授權使用者的存取權杖，以呼叫 Azure 地圖服務 REST Api。

8. 為使用者或群組設定 Azure 角色型存取控制。 請參閱 [為使用者授與以角色為基礎的存取權](#grant-role-based-access-for-users-to-azure-maps)。

9. 使用 Azure 地圖服務 Web SDK 來設定 web 應用程式頁面，以存取安全權杖端點。 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>後續步驟

進一步瞭解 web 應用程式案例：
> [!div class="nextstepaction"]
> [案例：登入使用者的 Web 應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)

尋找您 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何整合 Azure AD 與 Azure 地圖服務的範例：
> [!div class="nextstepaction"]
> [Azure 地圖服務 Azure AD Web 應用程式範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)
