---
title: 如何使用 Azure AD 和 Azure 地圖服務 REST Api 保護輸入限制裝置
titleSuffix: Azure Maps
description: 如何設定不支援登入 Azure AD 和呼叫 Azure 地圖服務 REST Api 的無瀏覽器應用程式。
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b9e75a25237a76c7fa6d300e01bb4d42db75fe08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988343"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>使用 Azure AD 和 Azure 地圖服務 REST Api 保護輸入限制裝置

本指南討論如何保護無法安全地儲存秘密或接受瀏覽器輸入的公用應用程式或裝置。 這些類型的應用程式落在 IoT 或物聯網的類別之下。 這些應用程式的一些範例可能包括：智慧型電視裝置或發出應用程式的感應器資料。 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中建立應用程式註冊

> [!NOTE]
> * **先決條件閱讀：** [案例：呼叫 web api 的傳統型應用程式](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-overview)
> * 下列案例會使用裝置程式碼流程，這不會牽涉到網頁瀏覽器來取得權杖。

在 Azure AD 中建立以裝置為基礎的應用程式，以啟用 Azure AD 登入。 此應用程式將被授與 Azure 地圖服務 REST Api 的存取權。

1. 在 [Azure 入口網站] 的 [Azure 服務] 清單中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新增註冊**]。  

    > [!div class="mx-imgBorder"]
    > ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

2. 輸入**名稱**，選擇**此組織目錄中的 [僅限帳戶**] 做為**支援的帳戶類型**。 在 [重新**導向 uri**] 中，指定**公用用戶端/原生（行動 & 桌面）** ，然後將新增 `https://login.microsoftonline.com/common/oauth2/nativeclient` 至值。 如需詳細資訊，請參閱[呼叫 Web api 的 Azure AD 桌面應用程式：應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)。 然後**註冊**應用程式。

    > [!div class="mx-imgBorder"]
    > ![新增名稱和重新導向 uri 的應用程式註冊詳細資料](./media/azure-maps-authentication/devicecode-app-registration.png)

3. 流覽至 [**驗證**]，並啟用 [將**應用程式視為公用用戶端**]。 這會啟用 Azure AD 的裝置程式碼驗證。
    
    > [!div class="mx-imgBorder"]
    > ![啟用應用程式註冊作為公用用戶端](./media/azure-maps-authentication/devicecode-public-client.png)

4.  若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後選取 [ **API 許可權**] [  >  **新增許可權**]。 在 [**我的組織使用的 api**] 底下，搜尋並選取 [ **Azure 地圖服務**]。

    > [!div class="mx-imgBorder"]
    > ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

5. 選取 [**存取 Azure 地圖服務**] 旁的核取方塊，然後選取 [**新增許可權**]。

    > [!div class="mx-imgBorder"]
    > ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

6. 為使用者或群組設定以 Azure 角色為基礎的存取控制。 請參閱[將使用者角色型存取權授與 Azure 地圖服務](#grant-role-based-access-for-users-to-azure-maps)。

7. 新增程式碼以取得應用程式中的權杖流程，如需執行的詳細資訊，請參閱[裝置程式碼流程](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-acquire-token#device-code-flow)。 取得權杖時，請參考範圍： `user_impersonation` 在先前的步驟中選取了。

> [!Tip]
> 使用 Microsoft 驗證程式庫（MSAL）來取得存取權杖。 請參閱在[呼叫 Web api 的傳統型應用程式上的建議：程式碼](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-configuration)設定

8. 使用從 Azure AD 取得的權杖來撰寫 HTTP 要求，並使用有效的 HTTP 用戶端傳送要求。

### <a name="sample-request"></a>範例要求
以下是用來上傳使用中心點和半徑以圓形幾何表示之簡單地理柵欄的範例要求主體。

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 以下的範例要求主體位於 GeoJSON 中：
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>範例回應：

Headers：
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

主體：
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>後續步驟

尋找 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"]
> [檢視使用計量](how-to-view-api-usage.md)
