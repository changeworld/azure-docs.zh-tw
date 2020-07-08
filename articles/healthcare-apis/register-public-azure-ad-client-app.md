---
title: 在 Azure AD Azure API for FHIR 中註冊公用用戶端應用程式
description: 本文說明如何在 Azure Active Directory 中註冊公用用戶端應用程式，以準備在 Azure 中部署 FHIR API。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5aa9e5a33dbe66e3ebd787decfa3a520454fc6f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870998"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊公用用戶端應用程式

在本文中，您將瞭解如何在 Azure Active Directory 中註冊公用應用程式。  

用戶端應用程式註冊是應用程式的 Azure Active Directory 標記法，可以用來進行驗證，並代表使用者要求 API 許可權。 公用用戶端是一種應用程式，例如行動應用程式和單一頁面 JavaScript 應用程式，無法將秘密保密。 此程式類似于[註冊機密用戶端](register-confidential-azure-ad-client-app.md)，但由於公用用戶端不受信任而無法保存應用程式密碼，因此不需要新增一個。

## <a name="app-registrations-in-azure-portal"></a>Azure 入口網站中的應用程式註冊

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]****。

2. 在 [ **Azure Active Directory** ] 分頁中，按一下 [**應用程式註冊**：

    ![Azure 入口網站。 新的應用程式註冊。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 按一下 [**新增註冊**]。

## <a name="application-registration-overview"></a>應用程式註冊總覽

1. 提供應用程式的顯示名稱。

2. 提供 [回復 URL]。 [回復 URL] 是指將驗證碼傳回用戶端應用程式的位置。 您可以新增更多回復 Url，稍後再編輯現有的 Url。

    ![Azure 入口網站。 新的公用應用程式註冊。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API 權限

類似于[機密用戶端應用程式](register-confidential-azure-ad-client-app.md)，您必須選取此應用程式應該要能夠代表使用者要求的 API 許可權：

1. 開啟**API 許可權**。

    如果您使用 Azure API for FHIR，您會藉由搜尋 [**我的組織使用的 api** ] 底下的 [Azure 醫療保健 api]，將許可權新增至 Azure 醫療保健 api （下圖）。
    
    如果您參考不同的資源應用程式，請選取您先前在 [**我的 api**] 底下建立的[FHIR API 資源應用程式註冊](register-resource-azure-ad-client-app.md)：

    ![Azure 入口網站。 新的公用 API 許可權-Azure API for FHIR 預設值](media/public-client-app/api-permissions.png)


2. 選取您想要讓應用程式能夠要求的許可權： ![ Azure 入口網站。 應用程式權限](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>驗證 FHIR 伺服器授權單位
如果您在本文中註冊的應用程式和 FHIR 伺服器位於相同的 Azure AD 租使用者中，您就可以繼續進行後續步驟。

如果您在 FHIR 伺服器的不同 Azure AD 租使用者中設定用戶端應用程式，就必須更新**授權**單位。 在 Azure API for FHIR 中，您會在 [設定] 下設定授權單位--> 驗證]。 將您的授權單位設定為 **https://login.microsoftonline.com/\<TENANT-ID>** 。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Active Directory 中註冊公用用戶端應用程式。 接下來，使用 Postman 測試 FHIR 伺服器的存取權。
 
>[!div class="nextstepaction"]
>[使用 Postman 存取 Azure API for FHIR](access-fhir-postman-tutorial.md)
