---
title: 使用 Azure AD App Proxy 進行內部部署應用程式的 SAML 單一登入
description: 瞭解如何為使用 SAML 驗證保護的內部部署應用程式提供單一登入。 使用應用程式 Proxy 提供內部部署應用程式的遠端存取。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3d2117e913f292e92f37f31d2e123587c70a189
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80803292"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>使用應用程式 Proxy 進行內部部署應用程式的 SAML 單一登入

您可以為使用 SAML 驗證保護的內部部署應用程式提供單一登入（SSO），並透過應用程式 Proxy 提供這些應用程式的遠端存取。 使用 SAML 單一登入，Azure Active Directory （Azure AD）會使用使用者的 Azure AD 帳戶向應用程式進行驗證。 Azure AD 與應用程式透過連線通訊協定，進行登入資訊通訊。 您也可以根據您在 SAML 宣告中定義的規則，將使用者對應至特定的應用程式角色。 除了 SAML SSO 以外，啟用應用程式 Proxy，您的使用者將可存取應用程式和順暢的 SSO 體驗。

應用程式必須能夠取用**Azure Active Directory**所簽發的 SAML 權杖。 此設定不適用於使用內部部署身分識別提供者的應用程式。 針對這些案例，我們建議您檢查[資源，將應用程式遷移至 Azure AD](migration-resources.md)。

SAML SSO 與應用程式 Proxy 也適用于 SAML 權杖加密功能。 如需詳細資訊，請參閱[設定 SAML 權杖加密 Azure AD](howto-saml-token-encryption.md)。

下列通訊協定圖表描述服務提供者起始（SP 起始）流程和識別提供者起始（IdP 起始）流程的單一登入順序。 應用程式 Proxy 會藉由快取 SAML 要求和內部部署應用程式的回應，來與 SAML SSO 搭配運作。

  ![SAML SP 流程](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP 流程](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>建立應用程式並設定 SAML SSO

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory > 企業應用程式**]，然後選取 [**新增應用程式**]。

2. 輸入新應用程式的顯示名稱，選取 **[整合您在資源庫中找不到的任何其他應用程式**]，然後選取 [**建立**]。

3. 在應用程式的 **[總覽**] 頁面上，選取 [**單一登入**]。

4. 選取 [ **SAML** ] 做為單一登入方法。

5. 第一次設定 SAML SSO 在公司網路上運作。 在 [以**SAML 設定單一登入**] 頁面中，移至 [**基本 SAML**設定] 標題，然後選取其 [**編輯**] 圖示（鉛筆）。 依照[輸入基本 SAML](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration)設定中的步驟，為應用程式設定 SAML 型驗證。

6. 將至少一個使用者新增至應用程式，並確定測試帳戶具有應用程式的存取權。 連線到公司網路時，請使用測試帳戶來查看您是否有應用程式的單一登入。 

   > [!NOTE]
   > 設定應用程式 Proxy 之後，您將會返回並更新 SAML**回復 URL**。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>使用應用程式 Proxy 發佈內部部署應用程式

您必須先啟用應用程式 Proxy 並安裝連接器，才可提供內部部署應用程式的 SSO。 請參閱在[Azure AD 中新增內部部署應用程式以透過應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)教學課程，以瞭解如何準備您的內部部署環境、安裝和註冊連接器，以及測試連接器。 然後依照下列步驟，使用應用程式 Proxy 發佈新的應用程式。 如需以下未提及的其他設定，請參閱教學課程中的[將內部部署應用程式新增至 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)一節。

1. 應用程式仍在 Azure 入口網站中開啟，請選取 [**應用程式 Proxy**]。 提供應用程式的**內部 URL** 。 如果您使用的是自訂網域，您也必須上傳應用程式的 TLS/SSL 憑證。 
   > [!NOTE]
   > 最佳做法是盡可能使用自訂網域，以獲得優化的使用者體驗。 深入瞭解如何[在 Azure AD 應用程式 Proxy 中使用自訂網域](application-proxy-configure-custom-domain.md)。

2. 選取 [ **Azure Active Directory** ] 做為應用程式的 [**預先驗證**] 方法。

3. 複製應用程式的**外部 URL** 。 您將需要此 URL 才能完成 SAML 設定。

4. 使用測試帳戶，嘗試使用**外部 URL**開啟應用程式，以驗證是否已正確設定應用程式 Proxy。 如果有問題，請參閱針對[應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)。

## <a name="update-the-saml-configuration"></a>更新 SAML 設定

1. 當應用程式仍在 Azure 入口網站中開啟時，請選取 [**單一登入**]。 

2. 在 [以**SAML 設定單一登入**] 頁面中，移至 [**基本 SAML**設定] 標題，然後選取其 [**編輯**] 圖示（鉛筆）。 請確定您在 [應用程式 Proxy] 中設定的**外部 URL**已填入 [**識別碼**]、[**回復 URL**] 和 [**登出 url** ] 欄位中。 應用程式 Proxy 必須要有這些 Url，才能正確運作。 

3. 編輯稍早設定的**回復 URL** ，使其網域可在網際網路上透過應用程式 Proxy 連線。 例如，如果您的**外部 URL**是`https://contosotravel-f128.msappproxy.net` `https://contosotravel.com/acs`，而原始的**回復 url**為，則您必須將原始的**回復 url**更新`https://contosotravel-f128.msappproxy.net/acs`為。

    ![輸入基本 SAML 設定資料](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 選取更新的**回復 URL**旁的核取方塊，將它標示為預設值。

   * Aftering 將所需的**回復 url**標示為預設值，您也可以刪除先前設定的「**回復 url** 」，以使用內部 URL。

   * 若為 SP 起始的流程，請確定後端應用程式指定了正確的**回復 URL**或判斷提示取用者服務 URL，以接收驗證權杖。

    > [!NOTE]
    > 如果後端應用程式預期**回復 url**為內部 URL，您必須使用[自訂網域](application-proxy-configure-custom-domain.md)來擁有相符的內部和外部 url，或在使用者的裝置上安裝我的應用程式安全登入延伸模組。 此延伸模組會自動重新導向至適當的應用程式 Proxy 服務。 若要安裝延伸模組，請參閱[我的應用程式安全登入延伸](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)模組。
    
## <a name="test-your-app"></a>測試應用程式

當您完成所有這些步驟時，您的應用程式應該啟動並執行。 若要測試應用程式：

1. 開啟瀏覽器，並流覽至您在發佈應用程式時所建立的**外部 URL** 。 
1. 使用您指派給應用程式的測試帳戶來登入。 您應該能夠載入應用程式，並將 SSO 放入應用程式中。

## <a name="next-steps"></a>後續步驟

- [Azure AD 應用程式 Proxy 如何提供單一登入？](application-proxy-single-sign-on.md)
- [疑難排解應用程式 Proxy](application-proxy-troubleshoot.md)
