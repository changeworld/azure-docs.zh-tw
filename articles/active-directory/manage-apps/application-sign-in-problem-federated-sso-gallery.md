---
title: 登入 SAML 型單一登入設定的應用程式時發生問題
description: 登入您使用 Azure Active Directory 的 SAML 型同盟單一登入所設定的應用程式時，特定錯誤的指引
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: 596af29fe72a41b5a86a09e4e6d5072d63b6ac71
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586341"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>登入 SAML 型單一登入設定的應用程式時發生問題
若要針對下列登入問題進行疑難排解，建議您遵循下列步驟來更妥善地進行診斷，並將解決步驟自動化：

- 安裝[我的應用程式安全瀏覽器延伸](./access-panel-deployment-plan.md)以協助 Azure Active Directory (Azure AD) 在使用 Azure 入口網站中的測試體驗時，提供更佳的診斷和解決方法。
- 使用 Azure 入口網站的應用程式組態頁面中的測試體驗，重現錯誤。 深入瞭解如何 [調試 SAML 型單一登入應用程式](./debug-saml-sso-issues.md)

如果您使用 Azure 入口網站中的 [測試體驗](./debug-saml-sso-issues.md) 搭配我的應用程式安全瀏覽器延伸模組，則不需要手動遵循下列步驟來開啟 [SAML 型單一登入設定] 頁面。

若要開啟 SAML 型單一登入組態頁面：
1.  開啟 [**Azure 入口網站**](https://portal.azure.com/) ，然後以 **全域系統管理員** 或 **共同管理員** 身分登入。
1.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟 **Azure Active Directory 擴充** 功能。
1.  在篩選搜尋方塊中輸入 **"Azure Active Directory"** ，然後選取 **Azure Active Directory** 專案。
1.  在 Azure Active Directory 左側導覽功能表中，選取 [企業應用程式]。
1.  選取 [所有應用程式]，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

1.  選取您要設為單一登入的應用程式。
1. 應用程式載入之後，請從應用程式的左側導覽功能表中選取 [ **單一登入** ]。
1. 選取 SAML 型 SSO。

## <a name="application-not-found-in-directory"></a>在目錄中找不到應用程式
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**可能的原因**

`Issuer`從應用程式傳送到 SAML 要求中 Azure AD 的屬性，不符合 Azure AD 中為應用程式設定的識別碼值。

**解決方法**

確定 `Issuer` SAML 要求中的屬性符合 Azure AD 中設定的識別碼值。

在 [SAML SSO 設定] 頁面的 [ **基本 saml** 設定] 區段中，確認 [識別碼] 文字方塊中的值符合錯誤中所顯示的識別碼值的值。

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>回復位址與為應用程式設定的回復位址不符
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**可能的原因**

`AssertionConsumerServiceURL`SAML 要求中的值不符合 Azure AD 中設定的回復 URL 值或模式。 `AssertionConsumerServiceURL`SAML 要求中的值是您在錯誤中看到的 URL。

**解決方法**

請確認 `AssertionConsumerServiceURL` SAML 要求中的值符合 Azure AD 中設定的 [回復 URL] 值。 

確認或更新 [回復 URL] 文字方塊中的值，以符合 `AssertionConsumerServiceURL` SAML 要求中的值。   

當您更新 Azure AD 中的 [回復 URL] 值，且該值符合 SAML 要求中的應用程式所傳送的值之後，您應該能夠登入應用程式。

## <a name="user-not-assigned-a-role"></a>使用者未指派角色
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**可能的原因**

尚未將 Azure AD 中應用程式的存取權授與使用者。

**解決方法**

若要直接將一或多個使用者指派給應用程式，請參閱 [快速入門：將使用者指派給](add-application-portal-assign-users.md)應用程式。

## <a name="not-a-valid-saml-request"></a>不是有效的 SAML 要求
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**可能的原因**

Azure AD 不支援應用程式為單一登入傳送的 SAML 要求。 以下為一些常見問題：
- SAML 要求中遺漏必要的欄位
- SAML 要求編碼方法

**解決方法**

1. 捕捉 SAML 要求。 遵循教學課程，以瞭解如何對 [Azure AD 中的應用程式進行 saml 型單一登入的偵錯工具](./debug-saml-sso-issues.md) ，以瞭解如何捕獲 saml 要求。
1. 請連絡應用程式廠商並提供下列資訊︰
    - SAML 要求
    - [Azure AD 單一登入 SAML 通訊協定需求](../develop/single-sign-on-saml-protocol.md)

應用程式廠商應確認它們支援單一登入的 Azure AD SAML 實行。

## <a name="misconfigured-application"></a>應用程式設定錯誤
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**可能的原因**

`Issuer`從應用程式傳送至 SAML 要求中 Azure AD 的屬性，不符合 Azure AD 中為應用程式設定的識別碼值。

**解決方法**

確定 `Issuer` SAML 要求中的屬性符合 Azure AD 中設定的識別碼值。 

確認 [識別碼] 文字方塊中的值符合錯誤中所顯示之識別碼值的值。

## <a name="certificate-or-key-not-configured"></a>未設定憑證或金鑰
`Error AADSTS50003: No signing key configured.`

**可能的原因**

應用程式物件已損毀，Azure AD 無法辨識為應用程式設定的憑證。

**解決方法**

若要刪除與建立新的憑證，請依照下列步驟執行：
1. 在 [SAML SSO 設定] 畫面上，選取 [ **saml 簽署憑證**] 區段底下的 [**建立新憑證**]。
1. 選取 [到期日]，然後按一下 [ **儲存**]。
1. 核取 [ **讓新憑證成為** 使用中] 以覆寫作用中的憑證。 然後按一下窗格頂端的 [儲存]，並按 [接受] 以啟動變換憑證。
1. 在 [SAML 簽署憑證] 區段下，按一下 [移除] 以移除 **未使用** 的憑證。

## <a name="saml-request-not-present-in-the-request"></a>要求中沒有 SAML 要求
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**可能的原因**

Azure AD 無法識別 HTTP 要求中 URL 參數內的 SAML 要求。 如果應用程式在將 SAML 要求傳送給 Azure AD 時未使用 HTTP 重新導向系結，就會發生這種情況。

**解決方法**

應用程式必須使用 HTTP 重新導向系結，將編碼成位置標頭的 SAML 要求傳送到該位置。 如需有關如何進行此實作的詳細資訊，請參閱 [SAML 通訊協定規格文件](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) \(英文\) 中的＜HTTP 重新導向繫結＞一節。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD 正在將權杖傳送到不正確的端點
**可能的原因**

在單一登入期間，如果登入要求未包含明確的回復 URL (判斷提示取用者服務 URL) 則 Azure AD 會為該應用程式選取任何已設定的回復 Url。 即使應用程式已設定明確的回復 URL，但使用者可能會被重新導向 https://127.0.0.1:444 。 

當應用程式已新增為非資源庫應用程式時，Azure Active Directory 就會建立此回覆 URL 做為預設值。 此行為已變更，Azure Active Directory 預設不會再新增此 URL。 

**解決方法**

刪除為應用程式設定的未使用回復 Url。

在 [SAML SSO 設定] 頁面的 [ **回復 URL (判斷提示取用者服務 URL)** ] 區段中，刪除系統所建立的未使用或預設回復 url。 例如： `https://127.0.0.1:444/applications/default.aspx` 。


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>使用者用來向服務驗證的驗證方法不符合要求的驗證方法
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**可能的原因**

在 `RequestedAuthnContext` SAML 要求中。 這表示應用程式需要所 `AuthnContext` 指定的 `AuthnContextClassRef` 。 不過，使用者在存取應用程式之前已經過驗證，而且 `AuthnContext` 先前的驗證所使用的 (驗證方法) 與要求的驗證方法不同。 例如，發生 myapps 和 WIA 的同盟使用者存取權。 `AuthnContextClassRef`將會是 `urn:federation:authentication:windows` 。 AAD 不會執行全新的驗證要求，它會在此案例中使用 IdP (ADFS 或任何其他 federation service 傳遞給它的驗證內容) 。 因此，如果應用程式要求以外的應用程式，則會有不相符的情況 `urn:federation:authentication:windows` 。 另一種情況是使用多重要素： `'X509, MultiFactor` 。

**解決方法**


`RequestedAuthnContext` 是選擇性的值。 如果可能的話，請詢問應用程式是否可以移除。

另一個選項是確定將會 `RequestedAuthnContext` 接受。 這會藉由要求全新驗證來完成。 如此一來，當 SAML 要求處理完成時，就會進行全新的驗證，並 `AuthnContext` 接受。 若要要求新的驗證，SAML 要求最多會包含此值 `forceAuthn="true"` 。 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>自訂傳送至應用程式的 SAML 宣告時發生問題
若要瞭解如何自訂傳送至應用程式的 SAML 屬性宣告，請參閱 [Azure Active Directory 中的宣告對應](../develop/active-directory-claims-mapping.md)。

## <a name="errors-related-to-misconfigured-apps"></a>設定錯誤的應用程式相關錯誤
確認入口網站中的兩種組態符合您應用程式中的組態。 特別是，比較用戶端/應用程識別碼、回覆 URL、用戶端密碼/金鑰，及應用程式識別碼 URI。

將您正以程式碼要求存取的資源，與在 [必要的資源] 索引標籤中設定的權限比較，以確定您只要求所設定的資源。

## <a name="next-steps"></a>後續步驟
- [應用程式管理快速入門系列](add-application-portal-assign-users.md)
- [如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](./debug-saml-sso-issues.md)
- [Azure AD 單一登入 SAML 通訊協定需求](../develop/single-sign-on-saml-protocol.md)