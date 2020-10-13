---
title: 在 Azure AD Azure API for FHIR 中註冊機密用戶端應用程式
description: 在 Azure Active Directory 中註冊機密用戶端應用程式，以代表使用者進行驗證，並要求存取資源應用程式。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826224"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊機密用戶端應用程式

在本教學課程中，您將瞭解如何在 Azure Active Directory 中註冊機密用戶端應用程式。 

用戶端應用程式註冊是應用程式的 Azure Active Directory 標記法，可用來代表使用者進行驗證，並要求存取 [資源應用程式](register-resource-azure-ad-client-app.md)。 機密用戶端應用程式是一種應用程式，可以信任來保存秘密，並在要求存取權杖時呈現該秘密。 機密應用程式的範例是伺服器端應用程式。

若要在入口網站中註冊新的機密應用程式，請遵循下列步驟。

## <a name="register-a-new-application"></a>註冊新的應用程式

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至 **Azure Active Directory**。

1. 選取 **應用程式註冊**。

    ![Azure 入口網站。 新的應用程式註冊。](media/how-to-aad/portal-aad-new-app-registration.png)

1. 選取 [新增註冊]。

1. 提供應用程式的顯示名稱。

1. 提供回復 URL。 您可以稍後變更這些詳細資料，但如果您知道應用程式的回復 URL，請立即輸入。

    ![新的機密用戶端應用程式註冊。](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. 選取 [註冊]。

## <a name="api-permissions"></a>API 權限

您已註冊應用程式，接下來您必須選取此應用程式應該要能夠代表使用者要求的 API 許可權：

1. 選取 [API 權限]****。

    ![機密用戶端。 API 權限](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. 選取 [新增權限]。

    如果您使用 Azure API for FHIR，您會在**我的組織使用的 api**下搜尋**azure 醫療保健 api** ，以新增 azure 醫療保健 api 的許可權。 如果您已經 [部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)，就可以找到這個。

    如果您參考不同的資源應用程式，請選取您先前在**api**下建立的[FHIR API 資源應用程式註冊](register-resource-azure-ad-client-app.md)。


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="機密用戶端。我的組織 Api" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. 選取 [範圍] () 機密應用程式應該能夠代表使用者要求的許可權：

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="機密用戶端。我的組織 Api":::

## <a name="application-secret"></a>應用程式祕密

1. 選取 [憑證和秘密]。
1. 選取 [新增用戶端密碼]。 

    ![機密用戶端。 應用程式秘密](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. 提供秘密 (1 年、2年或永不) 的描述和持續時間。

3. 一旦產生，就只會在入口網站中顯示一次。 請記下它並安全地儲存。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Active Directory 中註冊機密用戶端應用程式。 接下來，您可以使用 Postman 存取 FHIR 伺服器
 
>[!div class="nextstepaction"]
>[使用 Postman 存取 Azure API for FHIR](access-fhir-postman-tutorial.md)
