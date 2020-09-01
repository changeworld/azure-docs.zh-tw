---
title: Microsoft 身分識別平台與 SAML 持有人判斷提示流程 | Azure
description: 了解如何使用 SAML 持有人判斷提示流程從 Microsoft Graph 擷取資料，而無須提示使用者提供認證。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 6e7e4dd6383b1f264ff2da7893d9f86a3708217d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227911"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft 身分識別平台和 OAuth 2.0 SAML 持有人判斷提示流程
OAuth 2.0 SAML 持有人判斷提示流程可供在用戶端需要使用現有的信任關係時，使用 SAML 判斷提示來要求 OAuth 存取權杖。 套用至 SAML 判斷提示的簽章會提供授權應用程式其驗證。 SAML 判斷提示是一種由識別提供者發行，並由服務提供者取用的 XML 安全性權杖。 服務提供者會針對與安全性相關的用途，依賴其內容來識別判斷提示的主旨。

SAML 判斷提示會張貼到 OAuth 權杖端點。  端點會處理判斷提示，並根據先前應用程式的核准來發行存取權杖。 用戶端不需要擁有或儲存重新整理權杖，也不需要將用戶端祕密傳遞至權杖端點。

SAML 持有人判斷提示流程在從 Microsoft Graph API (僅支援委派權限) 擷取資料，而無須提示使用者提供認證時相當實用。 在此案例中，背景處理序所慣用的用戶端認證授與無法運作。

針對執行以瀏覽器為基礎互動式登入以取得 SAML 判斷提示，然後想要將存取新增至受 OAuth 保護 API (例如 Microsoft Graph) 的應用程式，您可發出 OAuth 要求來取得 API 的存取權杖。 當瀏覽器重新導向至 Azure AD 以對使用者進行驗證時，瀏覽器會挑選 SAML 登入的工作階段，讓使用者無須輸入其認證。

只有驗證身分識別提供者的使用者（例如 Active Directory 同盟服務 (ADFS) 同盟至 Azure Active Directory 時，才支援 OAuth SAML 持有人判斷提示流程。  從 ADFS 取得的 SAML 判斷提示可在 OAuth 流程中用來驗證使用者。

![OAuth 流程](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>使用 SAML 持有人判斷提示呼叫 Graph
現在讓我們了解如何實際地以程式設計方式來擷取 SAML 判斷提示。 這種方法已使用 ADFS 進行測試。 但是，這適用於任何支援以程式設計方式傳回 SAML 判斷提示的識別提供者。 基本程序為：取得 SAML 判斷提示、取得存取權杖，然後存取 Microsoft Graph。

### <a name="prerequisites"></a>必要條件

建立授權伺服器/環境 (Microsoft 365) 與識別提供者之間的信任關係，或與 SAML 2.0 持有人判斷提示簽發者 (ADFS) 之間的信任關係。 若要針對單一登入設定 ADFS 以及將其設為識別提供者，您可參閱[本文](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365)。

在[入口網站](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中註冊應用程式：
1. 登入[入口網站的應用程式註冊刀鋒視窗](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (請注意，由於我們正在使用 Graph API 的 v2.0 端點，因此需要在此入口網站中註冊應用程式。 否則，我們可以使用 Azure Active Directory 中的註冊)。 
1. 選取 [新增註冊]。
1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊： 
    1. **名稱** - 輸入會顯示給應用程式使用者的有意義應用程式名稱。
    1. **支援的帳戶類型** - 選取要讓應用程式支援的帳戶。
    1. **重新導向 URI (選擇性)** - 選取正在建置的應用程式類型 (Web 或公用用戶端 (行動和傳統型))，然後輸入應用程式的重新導向 URI (或回覆 URL)。
    1. 完成時，選取 [註冊]。
1. 請記下應用程式 (用戶端) 識別碼。
1. 在左側窗格中，選取 [憑證及祕密]。 在 [用戶端祕密] 區段中按一下 [新增用戶端祕密]。 複製新的用戶端祕密，因為當離開刀鋒視窗後，將無法再次取得。
1. 在左側窗格中，選取 [API 權限]，然後選取 [新增權限]。 選取 [Microsoft Graph]，然後選取 [委派權限]，然後選取 [Task.read]，因為我們計劃使用 Outlook Graph API。 

安裝 [Postman](https://www.getpostman.com/)，這是一種測試範例要求所需要的工具。  您可在稍後將要求轉換成程式碼。

### <a name="get-the-saml-assertion-from-adfs"></a>從 ADFS 取得 SAML 判斷提示
使用 SOAP 信封向 ADFS 端點發出 POST 要求，以擷取 SAML 判斷提示：

![取得 SAML 判斷提示](./media/v2-saml-bearer-assertion/2.png)

標頭值：

![標頭值](./media/v2-saml-bearer-assertion/3.png)

ADFS 要求本文：

![ADFS 要求本文](./media/v2-saml-bearer-assertion/4.png)

在成功張貼要求後，您應該會收到來自 ADFS 的 SAML 判斷提示。 您只需要 **SAML Assertion** 標籤資料，並將其轉換成 base64 編碼以用於後續要求。

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>使用 SAML 判斷提示來取得 OAuth2 權杖 
在此步驟中，將會使用 ADFS 判斷提示回應來擷取 OAuth2 權杖。

1. 透過以下方式來搭配標頭值建立 POST 要求：

    ![POST 要求](./media/v2-saml-bearer-assertion/5.png)
1. 在要求的本文中，取代 **client_id**、**client_secret** 和 **assertion** (上一個步驟中所取得 SAML 判斷提示的 base64 編碼)：

    ![Request body](./media/v2-saml-bearer-assertion/6.png)
1. 成功要求後，將會收到來自 Azure Active Directory 的存取權杖。

### <a name="get-the-data-with-the-oauth-token"></a>使用 OAuth 權杖取得資料

在收到存取權杖後，請呼叫 Graph API (在本範例中為 Outlook 工作)。 

1. 使用從上一個步驟擷取到的存取權杖來建立 GET 要求：

    ![GET 要求](./media/v2-saml-bearer-assertion/7.png)

1. 成功要求之後，您將會收到 JSON 回應。

## <a name="next-steps"></a>後續步驟

深入了解不同的[驗證流程和應用程式範例](authentication-flows-app-scenarios.md)。
