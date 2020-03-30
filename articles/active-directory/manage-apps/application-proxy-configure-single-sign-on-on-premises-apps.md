---
title: 使用 Azure AD 應用代理的本地應用的 SAML 單一登入
description: 瞭解如何為通過 SAML 身份驗證保護的本地應用程式提供單一登入。 使用應用程式代理提供對本地應用的遠端存取。
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
ms.openlocfilehash: ccf34b52e06e369fe4dd459ff9dfa2880596fb35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481342"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>具有應用程式代理的本地應用程式的 SAML 單登錄

您可以將單一登入 （SSO） 提供給通過 SAML 身份驗證保護的本地應用程式，並通過應用程式代理對這些應用程式提供遠端存取。 使用 SAML 單一登入，Azure 活動目錄 （Azure AD） 使用使用者的 Azure AD 帳戶對應用程式進行身份驗證。 Azure AD 與應用程式透過連線通訊協定，進行登入資訊通訊。 您還可以根據在 SAML 聲明中定義的規則將使用者映射到特定的應用程式角色。 通過啟用除 SAML SSO 之外的應用程式代理，您的使用者將具有對應用程式的外部存取權限和無縫的 SSO 體驗。

應用程式必須能夠使用**Azure 活動目錄**頒發的 SAML 權杖。 此配置不適用於使用本地標識提供程式的應用程式。 對於這些方案，我們建議查看[資源以將應用程式遷移到 Azure AD](migration-resources.md)。

具有應用程式代理的 SAML SSO 也適用于 SAML 權杖加密功能。 有關詳細資訊，請參閱配置[Azure AD SAML 權杖加密](howto-saml-token-encryption.md)。

下面的協定圖描述了服務提供者啟動（SP 啟動）流和標識提供程式啟動（IdP 啟動）流的單一登入順序。 應用程式代理通過緩存 SAML 請求和回應來與 SAML SSO 配合使用。

  ![SAML SP 流](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP 流](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>創建應用程式並設置 SAML SSO

1. 在 Azure 門戶中，選擇**Azure 活動目錄>企業應用程式**，然後選擇 **"新應用程式**"。

2. 輸入新應用程式的顯示名稱，選擇 **"集成未在庫中找到的任何其他應用程式**"，然後選擇"**創建**"。

3. 在應用的 **"概述"** 頁上，選擇 **"單一登入**"。

4. 選擇**SAML**作為單一登入方法。

5. 首先設置 SAML SSO，以便在公司網路上工作。 在"**使用 SAML 設置單一登入"頁中**，轉到**基本 SAML 配置**標題並選擇其**編輯**圖示（鉛筆）。 按照[輸入基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration)中的步驟為應用程式佈建基於 SAML 的身份驗證。

6. 至少向應用程式添加一個使用者，並確保測試帳戶有權訪問該應用程式。 連接到公司網路時，請使用測試帳戶查看應用程式是否單一登入。 

   > [!NOTE]
   > 設置應用程式代理後，您將回來更新 SAML**回復 URL**。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>使用應用程式代理髮布本地應用程式

在為本地應用程式提供 SSO 之前，需要啟用應用程式代理並安裝連接器。 請參閱教程[在 Azure AD 中通過應用程式代理添加用於遠端存取的本地應用程式](application-proxy-add-on-premises-application.md)，瞭解如何準備本地環境、安裝和註冊連接器以及測試連接器。 然後按照以下步驟使用應用程式代理髮布新應用程式。 有關下面未提及的其他設置，請參閱本教程中的"[將本地應用添加到 Azure AD"](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)部分。

1. 當應用程式在 Azure 門戶中仍然打開時，請選擇**應用程式代理**。 為應用程式提供**內部 URL。** 如果您使用的是自訂域，則還需要上傳應用程式的 TLS/SSL 憑證。 
   > [!NOTE]
   > 最佳做法是盡可能使用自訂域來優化使用者體驗。 瞭解有關在[Azure AD 應用程式代理 中使用自訂域的更多詳細資訊](application-proxy-configure-custom-domain.md)。

2. 選擇**Azure 活動目錄**作為應用程式的**預身份驗證**方法。

3. 複製應用程式**的外部 URL。** 您需要此 URL 才能完成 SAML 配置。

4. 使用測試帳戶，請嘗試使用**外部 URL**打開應用程式，以驗證應用程式代理設置是否正確。 如果有問題，請參閱[排除應用程式代理問題和錯誤訊息](application-proxy-troubleshoot.md)。

## <a name="update-the-saml-configuration"></a>更新 SAML 配置

1. 在 Azure 門戶中應用程式仍然打開時，選擇**單一登入**。 

2. 在"**使用 SAML 設置單一登入"頁中**，轉到**基本 SAML 配置**標題並選擇其**編輯**圖示（鉛筆）。 請確保在應用程式代理中配置**的外部 URL**填充在**識別碼**、回復**URL**和**登出 URL**欄位中。 應用程式代理正常工作需要這些 URL。 

3. 編輯較早配置的**回復 URL，** 以便應用程式代理可以覆蓋其域。 例如，如果您的外部**URL**和`https://contosotravel-f128.msappproxy.net`原始回復**URL**是`https://contosotravel.com/acs`，則需要將原始**回復 URL**更新到`https://contosotravel-f128.msappproxy.net/acs`。 

    ![輸入基本的 SAML 配置資料](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 選擇更新的 **"回復 URL"** 旁邊的核取方塊，將其標記為預設值。

   * 如果已列出所需的**回復 URL，** 請將其**回復 URL**標記為預設值，並刪除以前配置的回復**URL**。

   * 對於 SP 啟動的流，請確保後端應用程式指定用於接收身份驗證權杖的正確**回復 URL**或斷言消費者服務 URL。

    > [!NOTE]
    > 如果後端應用程式希望**回復 URL**是內部 URL，則需要使用[自訂域](application-proxy-configure-custom-domain.md)來匹配內部和外部 URLS，或者在使用者設備上安裝"我的應用"安全登錄擴展。 此擴展將自動重定向到相應的應用程式代理服務。 要安裝擴展，請參閱[我的應用程式安全登錄擴展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。
    
## <a name="test-your-app"></a>測試應用程式

當您完成所有這些步驟時，您的應用程式應該啟動並執行。 要測試應用程式：

1. 打開瀏覽器並導航到發佈應用時創建的 **"外部 URL"。** 
1. 使用您指派給應用程式的測試帳戶來登入。 您應該能夠載入應用程式並將 SSO 引入應用程式。

## <a name="next-steps"></a>後續步驟

- [Azure AD 應用程式 Proxy 如何提供單一登入？](application-proxy-single-sign-on.md)
- [疑難排解應用程式 Proxy](application-proxy-troubleshoot.md)
