---
title: 在 Azure AD 中為應用程式設定 SAML 型單一登入（SSO）
description: 在 Azure AD 中為應用程式設定 SAML 型單一登入（SSO）
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 82b10525fe4d8b9db88300b61ae2da6630030dfd
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461182"
---
# <a name="configure-saml-based-single-sign-on"></a>設定 SAML 型單一登入

在應用程式管理的[快速入門系列](view-applications-portal.md)中，您已瞭解如何使用 Azure AD 做為應用程式的身分識別提供者（IdP）。 本文會詳細說明單一登入的 SAML 型選項。 


## <a name="before-you-begin"></a>開始之前

視所使用的應用程式而定，使用 Azure AD 做為身分識別提供者（IdP），以及設定單一登入（SSO）的方式都很簡單或很複雜。 有些應用程式只能透過幾個動作來設定。 其他則需要深入設定。 若要快速增加，請逐步執行應用程式管理的[快速入門系列](view-applications-portal.md)。 如果您要新增的應用程式很簡單，那麼您可能不需要閱讀這篇文章。 如果您要新增的應用程式需要 SAML 型 SSO 的自訂設定，則本文適用于您。

在[快速入門系列](view-applications-portal.md)中，有關于設定單一登入的文章。 在其中，您將瞭解如何存取應用程式的 [SAML 設定] 頁面。 [SAML 設定] 頁面包含五個區段。 本文會詳細討論這些章節。

> [!IMPORTANT] 
> 在某些情況下，[**企業應用**程式] 中的應用程式導覽中不會出現 [**單一登入**] 選項。 
>
> 如果應用程式是使用**應用程式註冊**註冊，則單一登入功能會設定為預設使用 OIDC OAuth。 在此情況下，[**企業應用程式**] 下的流覽中不會顯示 [**單一登入**] 選項。 當您使用**應用程式註冊**新增自訂應用程式時，您可以在資訊清單檔中設定選項。 若要深入瞭解資訊清單檔案，請參閱[Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)。 若要深入瞭解 SSO 標準，請參閱[使用 Microsoft 身分識別平臺的驗證和授權](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)。 
>
> 當應用程式裝載于另一個租使用者時，或您的帳戶沒有必要許可權（全域管理員、雲端應用程式系統管理員、應用程式系統管理員或服務主體的擁有者）時，流覽中將遺失**單一登入**的其他案例。 許可權也可能會導致您可以開啟**單一登入**但無法儲存的案例。 若要深入瞭解 Azure AD 系統管理角色，請參閱（ https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 。


## <a name="basic-saml-configuration"></a>基本 SAML 設定

您應該從應用程式廠商處取得這些值。 您可手動輸入值或上傳中繼資料檔案，以擷取欄位的值。

> [!TIP]
> 許多應用程式已預先設定為可與 Azure AD 搭配使用。 這些應用程式會列在應用程式資源庫中，您可以在將應用程式新增至 Azure AD 租使用者時加以流覽。 [快速入門系列](view-applications-portal.md)會引導您完成整個過程。 針對資源庫中的應用程式，您將會找到詳細的逐步解說設定指示。 若要存取這些步驟，您可以在應用程式的 [SAML 設定] 頁面上按一下連結（如快速入門系列中所述），或者您可以流覽[SaaS 應用程式設定教學](../saas-apps/tutorial-list.md)課程中的所有應用程式設定教學課程清單。

| 基本 SAML 組態設定 | SP 起始 | idP 起始 | 描述 |
|:--|:--|:--|:--|
| **識別碼 (實體識別碼)** | 某些應用程式需要 | 某些應用程式需要 | 可唯一識別應用程式。 Azure AD 會將識別碼傳送給應用程式作為 SAML 權杖的 Audience 參數。 應用程式應該會對其進行驗證。 在應用程式中提供的任何 SAML 中繼資料中，此值也會顯示為實體識別碼。 輸入使用下列模式的 URL：'https://<subdomain>.contoso.com' *您可在應用程式傳送的  (SAML 要求) 中找到作為 **Issuer** 元素的這個值*。 |
| **回覆 URL** | 必要 | 必要 | 指定應用程式預期要接收 SAML 權杖的位置。 此回覆 URL 也稱為判斷提示取用者服務 (ACS) URL。 您可以使用其他回覆 URL 欄位來指定多個回覆 URL。 例如，您可能需要多個子網域的其他回覆 URL。 或者，基於測試目的，您可以一次指定多個回覆 URL (本機主機和公用 URL)。 |
| **登入 URL** | 必要 | 請勿指定 | 當使用者開啟此 URL 時，服務提供者會重新導向至 Azure AD 以進行驗證，並將使用者登入。 Azure AD 會使用此 URL 從 Office 365 和 Azure AD「存取面板」中啟動應用程式。 若為空白，Azure AD 會在使用者從 Office 365 啟動應用程式、Azure AD 存取面板，或 Azure AD SSO URL 時，執行 IdP 起始的登入。|
| **轉送狀態** | 選用 | 選用 | 對應用程式指定在驗證完成後應將使用者重新導向到的位置。 此值通常是對應用程式而言有效的 URL。 不過，有些應用程式會以不同的方式使用此欄位。 如需詳細資訊，請詢問應用程式廠商。
| **登出 URL** | 選用 | 選用 | 用於將 SAML 登出回應傳回應用程式。


## <a name="user-attributes-and-claims"></a>使用者屬性和宣告 

當使用者對應用程式進行驗證時，Azure AD 會將 SAML 權杖簽發給應用程式，此權杖具有可唯一識別該應用程式的使用者相關資訊 (或宣告)。 根據預設，此資訊包含使用者的使用者名稱、電子郵件地址、名字和姓氏。 例如，如果應用程式需要特定的宣告值或**名稱**格式，而不是使用者名稱，則您可能需要自訂這些宣告。 

> [!IMPORTANT]
> 許多應用程式已預先設定，而且在應用程式庫中，您不需要擔心設定使用者和群組宣告。 [快速入門系列](view-applications-portal.md)會逐步引導您新增和設定應用程式。


**唯一的使用者識別碼（名稱 ID）** 識別碼值是必要的宣告，而且很重要。 預設值為 [ *user. userprincipalname*]。 使用者識別碼可唯一識別應用程式內的每個使用者。 例如，如果電子郵件地址同時是使用者名稱和唯一識別碼，請將此值設定為 *user.mail*。

若要深入瞭解自訂 SAML 宣告，請參閱[如何：針對企業應用程式自訂 saml 權杖中發出的宣告](../develop/active-directory-saml-claims-customization.md)。

您可以新增宣告，如需詳細資訊，請參閱新增[應用程式特定宣告](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)或新增群組宣告，請參閱[設定群組宣告](../hybrid/how-to-connect-fed-group-claims.md)。


> [!NOTE]
> 若想了解還有哪些方式可自訂從 Azure AD 到應用程式的 SAML 權杖，請參閱下列資源。
>- 若要透過 Azure 入口網站建立自訂角色，請參閱[設定角色宣告](../develop/active-directory-enterprise-app-role-management.md)。
>- 若要透過 PowerShell 自訂宣告，請參閱[自訂宣告 - PowerShell](../develop/active-directory-claims-mapping.md)。
>- 若要修改應用程式資訊清單以設定應用程式的選擇性宣告，請參閱[設定選擇性宣告](../develop/active-directory-optional-claims.md)。
>- 若要為重新整理權杖、存取權杖、工作階段權杖及識別碼權杖設定權杖存留期原則，請參閱[設定權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)。 或者，若要透過 Azure AD 條件式存取來限制驗證工作階段，請參閱[驗證工作階段管理功能](https://go.microsoft.com/fwlink/?linkid=2083106)。

## <a name="saml-signing-certificate"></a>SAML 簽署憑證

Azure AD 會使用憑證來簽署它傳送至應用程式的 SAML 權杖。 您需要此憑證才能設定 Azure AD 和應用程式之間的信任。 如需憑證格式的詳細資料，請參閱應用程式的 SAML 文件。 如需詳細資訊，請參閱[管理同盟單一登入的憑證](manage-certificates-for-federated-single-sign-on.md)和 [SAML 權杖中的進階憑證簽署選項](certificate-signing-options.md)。

> [!IMPORTANT]
> 許多應用程式已預先設定，而且在應用程式資源庫中，您不需要深入瞭解憑證。 [快速入門系列](view-applications-portal.md)會逐步引導您新增和設定應用程式。

從 Azure AD 中，您可以直接從主要 [設定使用 SAML 的單一登入] 頁面，以 Base64 或原始格式下載作用中的憑證。 此外，您也可以下載應用程式中繼資料 XML 檔案，或使用應用程式同盟中繼資料 URL，以取得使用中的憑證。 若要查看、建立或下載您的憑證 (作用中或非作用中)，請遵循下列步驟。

檢查以驗證憑證的一些常見事項包括： 
   - *正確的到期日。* 您最多可將到期日設定為未來三年的日期。
   - *正確憑證的作用中狀態。* 如果狀態處於 [非作用中]，請將狀態變更為 [作用中]。 若要變更狀態，請在憑證的資料列上按一下滑鼠右鍵，然後選取 [**讓憑證成為**使用中]。
   - *正確的簽署選項和演算法。*
   - *正確的通知電子郵件地址。* 作用中的憑證接近到期日時，Azure AD 會傳送通知到此欄位設定的電子郵件地址。

有時候您可能需要下載憑證。 不過，請小心儲存它！ 若要下載憑證，請選取 [Base64 格式]、[原始格式] 或 [同盟中繼資料 XML] 的其中一個選項。 Azure AD 也提供**應用程式同盟中繼資料 URL**，您可以在其中以 `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 格式存取應用程式特定的中繼資料。

若要進行憑證變更，請選取 [編輯] 按鈕。 您可以在 [ **SAML 簽署憑證**] 頁面上執行幾件事：
   - 建立新憑證：選取 [**新增憑證**]，選取 [**到期日**]，然後選取 [**儲存**]。 若要啟用憑證，請選取操作功能表 ( **...** )，然後選取 [使憑證為使用中]。
   - 上傳具有私密金鑰與 pfx 認證的憑證：選取 [匯**入**憑證]，然後流覽至憑證。 輸入 **PFX 密碼**，然後選取 [新增]。  
   - 設定高級憑證簽署。 如需這些選項的詳細資訊，請參閱[先進憑證簽署選項](certificate-signing-options.md)。
   - 當使用中的憑證接近到期日時，通知其他人員：在 [**通知電子郵件地址**] 欄位中輸入電子郵件地址。

## <a name="set-up-the-application-to-use-azure-ad"></a>將應用程式設定為使用 Azure AD

[**設定 \<applicationName> ** ] 區段會列出需要在應用程式中設定的值，因此它會使用 AZURE AD 做為 SAML 識別提供者。 您可以在 [應用程式] 網站上設定頁面上的值。 例如，如果您要設定 GitHub，則會移至 github.com 網站並設定值。 如果應用程式已預先設定，而且在 Azure AD 資源庫中，您會找到一個連結，可供您**查看逐步指示**。 否則，您將需要尋找所設定之應用程式的檔。 

[**登入 url** ] 和 [**登出 url** ] 值都會解析為相同的端點，也就是 AZURE AD 租使用者的 SAML 要求處理端點。 

[Azure AD 識別碼] 是核發給應用程式的 SAML 權杖中包含的 [簽發者] 值。

## <a name="test-single-sign-on"></a>測試單一登入

一旦將您的應用程式設定為使用 Azure AD 作為 SAML 型識別提供者後，您就可以測試設定，以查看單一登入是否適用於您的帳戶。 

選取 [**測試**]，然後選擇使用目前登入的使用者或其他人進行測試。 

如果登入成功，您就可以將使用者和群組指派給 SAML 應用程式。 恭喜！

如果出現錯誤訊息，請完成下列步驟：

1. 請將訊息內容複製並貼到 [錯誤的描述為何?] 方塊中。

    ![取得解決方案指引](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. 選取 [取得解決方案指引]。 隨即會顯示根本原因和解決方法指引。  在此範例中，未將使用者指派給應用程式。

3. 閱讀解決方案指引，然後嘗試修正此問題。

4. 再次執行測試，直到順利完成。

如需詳細資訊，請參閱[針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯](../azuread-dev/howto-v1-debug-saml-sso-issues.md)。


## <a name="next-steps"></a>後續步驟

- [應用程式管理的快速入門系列](view-applications-portal.md)
- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [設定使用者帳戶自動佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [單一登入 SAML 通訊協定](../develop/single-sign-on-saml-protocol.md)
