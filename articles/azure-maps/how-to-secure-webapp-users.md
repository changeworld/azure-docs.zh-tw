---
title: 如何使用互動式單一登入來保護 web 應用程式
titleSuffix: Azure Maps
description: 如何設定 web 應用程式，其支援使用 OpenID Connect 通訊協定搭配 Azure 地圖服務 Web SDK 的 Azure AD 單一登入。
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f591e52ba0c54dcf3242d11f7e81e8e73a570c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988336"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>使用使用者登入保護 web 應用程式

下列指南適用于裝載在 web 伺服器上的應用程式、維護多個商務案例，並部署至 web 伺服器。 應用程式必須提供受保護的資源，只保護 Azure AD 的使用者。 此案例的目標是要讓 web 應用程式向 Azure AD 進行驗證，並代表使用者呼叫 Azure 地圖服務 REST Api。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中建立應用程式註冊

您必須在 Azure AD 中建立 web 應用程式，使用者才能登入。 然後，此 web 應用程式會將使用者存取權委派給 Azure 地圖服務 REST Api。

1. 在 [Azure 入口網站] 的 [Azure 服務] 清單中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新增註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

2. 輸入 [**名稱**]、[選擇**支援帳戶類型**]、[提供重新導向 URI]，其中會代表 Azure AD 將會發出權杖的 url，而是裝載地圖控制項的 url。 如需詳細資訊，請參閱 Azure AD[案例：登入使用者的 Web 應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)。 完成 Azure AD 案例中提供的步驟。  

3. 應用程式註冊完成後，請確認應用程式登入適用于使用者。 登入運作之後，應用程式可以被授與 Azure 地圖服務 REST Api 的委派存取權。
    
4.  若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後選取 [ **API 許可權**] [  >  **新增許可權**]。 在 [**我的組織使用的 api**] 底下，搜尋並選取 [ **Azure 地圖服務**]。

    > [!div class="mx-imgBorder"]
    > ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

5. 選取 [**存取 Azure 地圖服務**] 旁的核取方塊，然後選取 [**新增許可權**]。

    > [!div class="mx-imgBorder"]
    > ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

6. 啟用 web 應用程式以使用應用程式密碼設定應用程式註冊以呼叫 Azure 地圖服務 REST Api，如需詳細步驟，請參閱[呼叫 Web api 的 web 應用程式：應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-app-registration)。 需要密碼才能代表使用者向 Azure AD 進行驗證。 應用程式註冊憑證或密碼應儲存在安全存放區中，web 應用程式才能抓取以向 Azure AD 進行驗證。 
   
   * 如果應用程式已設定 Azure AD 應用程式註冊和密碼，則可以略過此步驟。

> [!Tip]
> 如果應用程式裝載于 Azure 環境中，我們建議使用[適用于 azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別和 Azure Key Vault 實例，藉由取得[存取權杖](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token)來存取 Azure Key Vault 的秘密或憑證，以存取秘密。 若要連線至 Azure Key Vault 以取得秘密，請參閱[透過受控識別](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)連線的教學課程。
   
7. 為 Azure 地圖服務 Web SDK 執行安全權杖端點，以存取權杖。 
   
   * 如需範例權杖控制器，請參閱[Azure 地圖服務 Azure AD 範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs)。 
   * 如需非 AspNetCore 的執行或其他方式，請參閱從 Azure AD 檔[取得應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token)。
   * 受保護的權杖端點會負責傳回已驗證和已授權使用者的存取權杖，以呼叫 Azure 地圖服務 REST Api。

8. 為使用者或群組設定以 Azure 角色為基礎的存取控制。 請參閱將[角色型存取授與使用者](#grant-role-based-access-for-users-to-azure-maps)。

9. 使用 Azure 地圖服務 Web SDK 設定 web 應用程式頁面，以存取安全權杖端點。 

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

深入瞭解 web 應用程式案例：
> [!div class="nextstepaction"]
> [案例：登入使用者的 Web 應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)

尋找 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何將 Azure AD 與 Azure 地圖服務整合的範例：
> [!div class="nextstepaction"]
> [Azure 地圖服務 Azure AD Web 應用程式範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)
