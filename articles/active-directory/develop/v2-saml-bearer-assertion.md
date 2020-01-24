---
title: Microsoft 身分識別平臺 & SAML 持有人判斷提示流程 |Azure
description: 瞭解如何從 Microsoft Graph 提取資料，而不需要使用 SAML 持有人判斷提示流程來提示使用者提供認證。
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700204"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft 身分識別平臺和 OAuth 2.0 SAML 持有人宣告流程
OAuth 2.0 SAML 持有人宣告流程可讓您在用戶端需要使用現有的信任關係時，使用 SAML 判斷提示來要求 OAuth 存取權杖。 套用至 SAML 判斷提示的簽章會提供授權應用程式的驗證。 SAML 判斷提示是由身分識別提供者所發行並由服務提供者所使用的 XML 安全性權杖。 服務提供者依賴其內容來識別判斷提示的主旨，以進行安全性相關的目的。

SAML 判斷提示會張貼到 OAuth 權杖端點。  端點會處理判斷提示，並根據應用程式先前的核准來發出存取權杖。 用戶端不一定要有或儲存重新整理權杖，也不需要將用戶端秘密傳遞至權杖端點。

從 Microsoft Graph Api （僅支援委派的許可權）提取資料時，SAML 持有人判斷提示流程很有用，而不會提示使用者提供認證。 在此案例中，用戶端認證授與（這是背景進程慣用的）無法運作。

對於執行互動式瀏覽器登入的應用程式，若要取得 SAML 判斷提示，然後想要將存取權新增至受 OAuth 保護的 API （例如 Microsoft Graph），您可以提出 OAuth 要求來取得 API 的存取權杖。 當瀏覽器重新導向至 Azure AD 以驗證使用者時，瀏覽器會從 SAML 登入中挑選會話，而使用者不需要輸入其認證。

使用身分識別提供者（例如 Active Directory 同盟服務（ADFS）同盟至 Azure Active Directory 的使用者進行驗證時，也支援 OAuth SAML 持有人判斷提示流程。  從 ADFS 取得的 SAML 判斷提示可在 OAuth 流程中用來驗證使用者。

![OAuth 流程](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>使用 SAML 持有人判斷提示呼叫圖形
現在讓我們瞭解如何實際以程式設計方式提取 SAML 判斷提示。 此方法已使用 ADFS 進行測試。 不過，這適用于任何支援以程式設計方式傳回 SAML 判斷提示的身分識別提供者。 基本程式如下：取得 SAML 判斷提示、取得存取權杖，以及存取 Microsoft Graph。

### <a name="prerequisites"></a>必要條件

建立授權伺服器/環境（Microsoft 365）與身分識別提供者之間的信任關係，或 SAML 2.0 持有人判斷提示（ADFS）的簽發者。 若要設定 ADFS 以進行單一登入並做為身分識別提供者，您可以參考[這篇文章](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)。

在[入口網站](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中註冊應用程式：
1. 登入[入口網站的 [應用程式註冊](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)] 分頁（請注意，我們會使用圖形 API 的 v2.0 端點，因此需要在此入口網站中註冊應用程式。 否則，我們可以使用 Azure active directory 中的註冊）。 
1. 選取 [新增註冊]。
1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊： 
    1. **名稱** - 輸入會顯示給應用程式使用者的有意義應用程式名稱。
    1. **支援的帳戶類型** - 選取要讓應用程式支援的帳戶。
    1. 重新**導向 URI （選用）** -選取您要建立的應用程式類型、Web 或公用用戶端（行動 & 桌面），然後輸入應用程式的 [重新導向 URI] （或 [回復 URL]）。
    1. 完成時，選取 [註冊]。
1. 記下應用程式（用戶端）識別碼。
1. 在左窗格中，選取 [**憑證 & 密碼**]。 按一下 [**用戶端**密碼] 區段中的 [**新增用戶端密碼**]。 複製新的用戶端密碼，當您離開分頁時，將無法取得。
1. 在左窗格中，依序選取 [ **API 許可權**] 和 [**新增許可權**]。 依序選取 [ **Microsoft Graph**] 和 [**委派的許可權**]，然後選取 [工作] **。請閱讀**，因為我們想要使用 Outlook 圖形 API。 

安裝[Postman](https://www.getpostman.com/)，這是測試範例要求所需的工具。  稍後，您可以將要求轉換成程式碼。

### <a name="get-the-saml-assertion-from-adfs"></a>從 ADFS 取得 SAML 判斷提示
使用 SOAP 信封建立對 ADFS 端點的 POST 要求，以提取 SAML 判斷提示：

![取得 SAML 判斷提示](./media/v2-saml-bearer-assertion/2.png)

標頭值：

![標頭值](./media/v2-saml-bearer-assertion/3.png)

ADFS 要求主體：

![ADFS 要求主體](./media/v2-saml-bearer-assertion/4.png)

成功公佈此要求之後，您應該會收到來自 ADFS 的 SAML 判斷提示。 只需要**SAML： Assertion**標記資料，將它轉換成 base64 編碼，以便在進一步的要求中使用。

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>使用 SAML 判斷提示取得 OAuth2 token 
在此步驟中，使用 ADFS 判斷提示回應來提取 OAuth2 token。

1. 建立 POST 要求，如下所示加上標頭值：

    ![POST 要求](./media/v2-saml-bearer-assertion/5.png)
1. 在要求的本文中，取代**client_id**、 **client_secret**和判斷提示 **（以**base64 編碼的 SAML 判斷提示取得上一個步驟）：

    ![Request body](./media/v2-saml-bearer-assertion/6.png)
1. 成功要求之後，您會收到來自 Azure active directory 的存取權杖。

### <a name="get-the-data-with-the-oauth-token"></a>使用 Oauth 權杖取得資料

收到存取權杖之後，請呼叫 Graph Api （在此範例中為 Outlook tasks）。 

1. 使用在上一個步驟中提取的存取權杖來建立 GET 要求：

    ![GET 要求](./media/v2-saml-bearer-assertion/7.png)

1. 成功要求之後，您將會收到 JSON 回應。

## <a name="next-steps"></a>後續步驟

瞭解不同的[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。