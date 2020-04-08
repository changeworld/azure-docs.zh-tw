---
title: 使用 Azure AD 應用程式代理的本地應用程式的 SAML 單一登入
description: 瞭解如何為透過 SAML 身份驗證保護的本地應用程式提供單一登錄。 使用應用程式代理提供對本地應用的遠程訪問。
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
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803292"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>具有應用程式代理的本地應用程式的 SAML 單登入

您可以將單一登入 (SSO) 提供給透過 SAML 身份驗證保護的本地應用程式,並透過應用程式代理對這些應用程式提供遠端存取。 使用 SAML 單一登入,Azure 活動目錄 (Azure AD) 使用使用者的 Azure AD 帳戶對應用程式進行身份驗證。 Azure AD 與應用程式透過連線通訊協定，進行登入資訊通訊。 您還可以根據在 SAML 聲明中定義的規則將使用者映射到特定的應用程式角色。 通過啟用除 SAML SSO 之外的應用程式代理,您的使用者將具有對應用程式的外部存取許可權和無縫的 SSO 體驗。

應用程式必須能夠使用**Azure 活動目錄**頒發的 SAML 權杖。 此配置不適用於使用本地標識提供程式的應用程式。 對於這些方案,我們建議查看[資源以將應用程式遷移到 Azure AD](migration-resources.md)。

具有應用程式代理的 SAML SSO 也適用於 SAML 令牌加密功能。 有關詳細資訊,請參閱設定[Azure AD SAML 權杖加密](howto-saml-token-encryption.md)。

下面的協定圖描述了服務提供者啟動(SP 啟動)流和標識提供程式啟動(IdP 啟動)流的單一登錄順序。 應用程式代理透過快取 SAML 請求和回應來與 SAML SSO 配合使用。

  ![SAML SP 串流](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP 串流](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>建立應用程式並設定 SAML SSO

1. 在 Azure 門戶中,選擇**Azure 活動目錄>企業應用程式**,然後選擇 **「新應用程式**」。

2. 輸入新應用程式的顯示名稱,選擇 **「集成未在庫中找到的任何其他應用程式**」,然後選擇「**創建**」。

3. 在應用的 **「概述」** 頁上,選擇 **「單一登錄**」 。

4. 選擇**SAML**作為單一登錄方法。

5. 首先設置 SAML SSO,以便在公司網路上工作。 在「**使用 SAML 設定單一登入」頁中**,轉到**基本 SAML 設定**標題並選擇其**編輯**圖示(鉛筆)。 按照[輸入基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration)中的步驟為應用程式配置基於 SAML 的身份驗證。

6. 至少向應用程式添加一個使用者,並確保測試帳戶有權訪問該應用程式。 連接到公司網路時,請使用測試帳戶查看應用程式是否單一登錄。 

   > [!NOTE]
   > 設定應用程式代理後,您將回來更新 SAML**回覆 URL**。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>使用應用程式代理發佈本地端應用程式

在為本地應用程式提供 SSO 之前,需要啟用應用程式代理並安裝連接器。 請參閱教程[在 Azure AD 中透過應用程式代理添加用於遠端存取的本地應用程式](application-proxy-add-on-premises-application.md),瞭解如何準備本地環境、安裝和註冊連接器以及測試連接器。 然後按照以下步驟使用應用程式代理發佈新應用程式。 有關下面未提及的其他設置,請參閱本教程中的"[將本地應用添加到 Azure AD"](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)部分。

1. 當應用程式在 Azure 門戶中仍然開啟時,請選擇**應用程式代理**。 為應用程式提供**內部 URL。** 如果您使用的是自定義域,則還需要上傳應用程式的 TLS/SSL 證書。 
   > [!NOTE]
   > 最佳做法是盡可能使用自定義域來優化用戶體驗。 瞭解有關在[Azure AD 應用程式代理 中使用自訂網域的詳細資訊](application-proxy-configure-custom-domain.md)。

2. 選擇**Azure 活動目錄**作為應用程式的**預身份驗證**方法。

3. 複製應用程式**的外部 URL。** 您需要此 URL 才能完成 SAML 設定。

4. 使用測試帳戶,請嘗試使用**外部 URL**打開應用程式,以驗證應用程式代理設定是否正確。 如果有問題,請參閱[排除應用程式代理問題和錯誤訊息](application-proxy-troubleshoot.md)。

## <a name="update-the-saml-configuration"></a>更新 SAML 設定

1. 在 Azure 門戶中應用程式仍然開啟時,選擇**單一登入**。 

2. 在「**使用 SAML 設定單一登入」頁中**,轉到**基本 SAML 設定**標題並選擇其**編輯**圖示(鉛筆)。 請確保在應用程式代理中配置**的外部 URL**填充在**識別碼**、回覆**URL**和**登出網址**欄位中。 應用程式代理正常工作需要這些 URL。 

3. 編輯較早設定的**回覆 URL,** 以便其域可透過應用程式代理在網路上覆蓋。 例如,如果您的外部**網址**與`https://contosotravel-f128.msappproxy.net`原始回覆`https://contosotravel.com/acs`**網址**是 ,則需要將原始`https://contosotravel-f128.msappproxy.net/acs`**回覆 URL**更新到 。

    ![輸入基本的 SAML 設定資料](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 選擇更新的 **「回復 URL」** 旁邊的複選框,將其標記為預設值。

   * 將所需的 **「答覆 URL」** 標記為預設後,還可以刪除以前配置的使用內部 URL 的**回覆 URL。**

   * 對於 SP 啟動的流,請確保後端應用程式指定用於接收身份驗證令牌的正確**回覆 URL**或斷言消費者服務 URL。

    > [!NOTE]
    > 如果後端應用程式希望**回覆 URL**是內部 URL,則需要使用[自訂網域](application-proxy-configure-custom-domain.md)來匹配內部和外部 URLS,或者在使用者裝置上安裝「我的應用」安全登錄擴展。 此擴展將自動重定向到相應的應用程式代理服務。 要安裝延伸,請參閱[我的應用程式安全登入延伸](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。
    
## <a name="test-your-app"></a>測試應用程式

當您完成所有這些步驟時，您的應用程式應該啟動並執行。 要測試應用程式:

1. 打開瀏覽器並導航到發佈應用時創建的 **「外部 URL」。。** 
1. 使用您指派給應用程式的測試帳戶來登入。 您應該能夠載入應用程式並將 SSO 引入應用程式。

## <a name="next-steps"></a>後續步驟

- [Azure AD 應用程式 Proxy 如何提供單一登入？](application-proxy-single-sign-on.md)
- [疑難排解應用程式 Proxy](application-proxy-troubleshoot.md)
