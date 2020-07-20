---
title: 在 Azure AD Azure API for FHIR 中註冊機密用戶端應用程式
description: 在 Azure Active Directory 中註冊機密用戶端應用程式，以代表使用者進行驗證，並要求存取資源應用程式。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6642adf5f51be1f4f54c538624829fe9004d3907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871118"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊機密用戶端應用程式

在本教學課程中，您將瞭解如何在 Azure Active Directory 中註冊機密用戶端應用程式。 

用戶端應用程式註冊是應用程式的 Azure Active Directory 標記法，可以用來代表使用者進行驗證，並要求存取[資源應用程式](register-resource-azure-ad-client-app.md)。 機密用戶端應用程式是一種應用程式，可以信任它來保存秘密，並在要求存取權杖時呈現該秘密。 機密應用程式的範例包括伺服器端應用程式。

若要在入口網站中註冊新的機密應用程式，請遵循下列步驟。

## <a name="app-registrations-in-azure-portal"></a>Azure 入口網站中的應用程式註冊

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]****。

2. 在 [ **Azure Active Directory** ] 分頁中，按一下 [**應用程式註冊**：

    ![Azure 入口網站。 新的應用程式註冊。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 按一下 [**新增註冊**]。

## <a name="register-a-new-application"></a>註冊新的應用程式

1. 提供應用程式的顯示名稱。

2. 提供 [回復 URL]。 稍後可以變更這些詳細資料，但如果您知道應用程式的回復 URL，請立即輸入。

    ![新的機密用戶端應用程式註冊。](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API 權限

下一步新增 API 許可權：

1. 開啟**API 許可權**：

    ![機密用戶端。 API 許可權](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. 按一下 [**新增許可權**]

3. 選取適當的資源 API：

    針對 Azure API for FHIR （受控服務），按一下 [**我的組織使用的 api** ]，並搜尋「Azure 醫療保健 api」。 針對 Azure 的開放原始碼 FHIR 伺服器，請選取您的[FHIR API 資源應用程式註冊](register-resource-azure-ad-client-app.md)：

    ![機密用戶端。 我的 Api](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. 選取機密應用程式應該能夠代表使用者要求的範圍（許可權）：

    ![機密用戶端。 委派的權限](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>應用程式祕密

1. 建立應用程式密碼（用戶端密碼）：

    ![機密用戶端。 應用程式密碼](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. 提供密碼的描述和持續時間。

3. 一旦產生，就只會在入口網站中顯示一次。 記下它並安全地儲存。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Active Directory 中註冊機密用戶端應用程式。 您現在已準備好部署[Azure API for FHIR](fhir-paas-powershell-quickstart.md)。

部署 Azure API for FHIR 之後，您可以查看其他可用的設定。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)