---
title: 在 Azure AD Azure API for FHIR 中註冊服務應用程式
description: 瞭解如何在 Azure Active Directory 中註冊服務用戶端應用程式，以用來驗證和取得權杖。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871078"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊服務用戶端應用程式

在本文中，您將瞭解如何在 Azure Active Directory 中註冊服務用戶端應用程式。 用戶端應用程式註冊是應用程式的 Azure Active Directory 標記法，可用於驗證和取得權杖。 服務用戶端可供應用程式用來取得存取權杖，而不需要使用者進行互動式驗證。 取得存取權杖時，它會有特定的應用程式許可權，並使用應用程式密碼（密碼）。

請遵循下列步驟來建立新的服務用戶端。

## <a name="app-registrations-in-azure-portal"></a>Azure 入口網站中的應用程式註冊

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]****。

2. 在 [ **Azure Active Directory** ] 分頁中，按一下 [**應用程式註冊**：

    ![Azure 入口網站。 新的應用程式註冊。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 按一下 [新增註冊]。

## <a name="service-client-application-details"></a>服務用戶端應用程式詳細資料

* 服務用戶端需要顯示名稱，而且您也可以提供回復 URL，但通常不會使用它。

    ![Azure 入口網站。 新增服務用戶端應用程式註冊。](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API 權限

您將需要授與服務用戶端應用程式角色。 

1. 開啟 [ **api 許可權**]，然後選取您的[FHIR API 資源應用程式註冊](register-resource-azure-ad-client-app.md)。 如果您使用 Azure API for FHIR，您會在 [**我的組織使用的 api**] 底下搜尋 Azure 醫療保健 api，以新增 Azure 醫療保健 api 的許可權。

    ![Azure 入口網站。 服務用戶端 API 許可權](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. 從資源應用程式上定義的應用程式角色中選取您的

    ![Azure 入口網站。 服務用戶端應用程式許可權](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. 將同意授與應用程式。 如果您沒有必要的許可權，請洽詢您的 Azure Active Directory 系統管理員：

    ![Azure 入口網站。 服務用戶端系統管理員同意](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>應用程式祕密

服務用戶端需要秘密（密碼），您在取得權杖時將會用到它。

1. 按一下 [**憑證 &amp; 密碼**]

2. 按一下 [新增用戶端密碼]

    ![Azure 入口網站。 服務用戶端密碼](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 提供秘密的持續時間。

4. 一旦產生之後，它只會在入口網站中顯示一次。 請記下它，然後安全地儲存。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Active Directory 中註冊服務用戶端應用程式。 接下來，在 Azure 中部署 FHIR API。
 
>[!div class="nextstepaction"]
>[部署開放原始碼 FHIR 伺服器](fhir-oss-powershell-quickstart.md)