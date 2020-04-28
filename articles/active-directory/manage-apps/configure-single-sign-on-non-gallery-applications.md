---
title: SAML 單一登入-非資源庫應用程式-Microsoft 身分識別平臺 |Microsoft Docs
description: 在 Microsoft 身分識別平臺（Azure AD）中設定非資源庫應用程式的單一登入（SSO）
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244208"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>為非資源庫應用程式設定 SAML 型單一登入

當您[將資源庫應用程式](add-gallery-app.md)或[非資源庫 web 應用](add-non-gallery-app.md)程式新增至 Azure AD 企業應用程式時，您可以使用的其中一個單一登入選項是[SAML 型單一登入](what-is-single-sign-on.md#saml-sso)。 盡可能針對使用其中一個 SAML 通訊協定進行驗證的應用程式選擇 [SAML]。 使用 SAML 單一登入時，Azure AD 會使用使用者的 Azure AD 帳戶向應用程式驗證。 Azure AD 與應用程式透過連線通訊協定，進行登入資訊通訊。 您可以根據您在 SAML 宣告中定義的規則，將使用者對應至特定的應用程式角色。 本文說明如何為非資源庫應用程式設定 SAML 型單一登入。 

> [!NOTE]
> 要新增資源庫應用程式？ 在 [SaaS 應用程式教學課程清單](../saas-apps/tutorial-list.md)中尋找逐步設定指示

若要為不在資源庫中的應用程式設定 SAML 單一登入，而不需撰寫程式碼，您需要有訂用帳戶和 Azure AD Premium 授權，而且應用程式必須支援 SAML 2.0。 如需有關 Azure AD 版本的詳細資訊，請參閱 [Azure AD 定價](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="before-you-begin"></a>開始之前

如果應用程式尚未新增至您的 Azure AD 租使用者，請參閱新增不在資源庫中的[應用程式](add-non-gallery-app.md)。

## <a name="step-1-edit-the-basic-saml-configuration"></a>步驟 1： 編輯基本 SAML 組態

1. 以雲端應用程式系統管理員或 Azure AD 租用戶的應用程式系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 流覽至**Azure Active Directory** > **企業應用程式**]，並從清單中選取應用程式。 
   
   - 若要搜尋應用程式，請在 [應用程式類型]**** 功能表中選取 [所有應用程式]****，然後選取 [套用]****。 在搜尋方塊中輸入應用程式的名稱，然後從結果中選取應用程式。

3. 在 [管理]**** 區段中，選取 [單一登入]****。 

4. 選取 [ **SAML**]。 [設定使用 SAML 的單一登入 - 預覽]**** 頁面隨即出現。

   ![步驟1編輯基本 SAML 設定](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. 若要編輯基本 SAML 組態選項，請選取 [基本 SAML 組態]**** 區段右上角的 [編輯]**** 圖示 (鉛筆)。

1. 輸入下列設定。 您應該會從應用程式廠商取得這些值。 您可手動輸入值或上傳中繼資料檔案，以擷取欄位的值。

    | 基本 SAML 組態設定 | SP 起始 | idP 起始 | 說明 |
    |:--|:--|:--|:--|
    | **識別碼 (實體識別碼)** | 某些應用程式需要 | 某些應用程式需要 | 唯一識別應用程式。 Azure AD 會將識別碼傳送給應用程式作為 SAML 權杖的 Audience 參數。 應用程式應該會對其進行驗證。 在應用程式中提供的任何 SAML 中繼資料中，此值也會顯示為實體識別碼。 輸入使用下列模式的 URL： ' HTTPs://<subdomain>. Contoso.com '*您可以在應用程式所傳送的**AuthnRequest** （SAML 要求）中找到此值做為**Issuer**元素。* |
    | **回復 URL** | 必要 | 必要 | 指定應用程式預期要接收 SAML 權杖的位置。 此回覆 URL 也稱為判斷提示取用者服務 (ACS) URL。 您可以使用 [其他回復 URL] 欄位來指定多個回復 Url。 例如，您可能需要多個子域的其他回復 Url。 或者，基於測試目的，您可以一次指定多個回復 Url （本機主機和公用 Url）。 |
    | **登入 URL** | 必要 | 請勿指定 | 當使用者開啟此 URL 時，服務提供者會重新導向至 Azure AD 以進行驗證，並將使用者登入。 Azure AD 會使用此 URL 從 Office 365 和 Azure AD「存取面板」中啟動應用程式。 當空白時，Azure AD 會在使用者從 Office 365、Azure AD 存取面板或 Azure AD SSO URL 啟動應用程式時，執行 IdP 起始的登入。|
    | **轉送狀態** | 選用 | 選用 | 對應用程式指定在驗證完成後應將使用者重新導向到的位置。 此值通常是對應用程式而言有效的 URL。 不過，有些應用程式會以不同的方式使用此欄位。 如需詳細資訊，請詢問應用程式廠商。
    | **登出 URL** | 選用 | 選用 | 用於將 SAML 登出回應傳回應用程式。

如需詳細資訊，請參閱[單一登入 SAML 通訊協定](../develop/single-sign-on-saml-protocol.md)。

## <a name="step-2-configure-user-attributes-and-claims"></a>步驟 2： 設定使用者屬性和宣告 

當使用者向應用程式驗證時，Azure AD 會向應用程式發出 SAML 權杖，其中包含可唯一識別它們的使用者相關資訊（或宣告）。 根據預設，這項資訊包括使用者的使用者名稱、電子郵件地址、名字和姓氏。 例如，如果應用程式需要特定的宣告值或**名稱**格式，而不是 username，則您可能需要自訂這些宣告。 資源庫應用程式的需求會在[應用程式特定的教學課程](../saas-apps/tutorial-list.md)中說明，或者您可以詢問應用程式廠商。 設定使用者屬性和宣告的一般步驟如下所述。

1. 在 [使用者屬性和宣告]**** 區段中，選取右上角的 [編輯]**** 圖示 (鉛筆)。

   ![步驟2設定使用者屬性和宣告](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. 確認 [名稱識別碼值]****。 預設值為 *user.principalname*。 使用者識別碼可唯一識別應用程式內的每個使用者。 例如，如果電子郵件地址同時是使用者名稱和唯一識別碼，請將此值設定為 *user.mail*。

3. 若要修改 [名稱識別碼值]****，請選取 [名稱識別碼值]**** 欄位的 [編輯]**** 圖示 (鉛筆)。 視需要，對識別碼格式和來源進行適當的變更。 如需詳細資訊，請參閱[編輯 NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid)。 在完成時儲存變更。 
 
4. 若要設定群組宣告，請選取 [宣告中傳回的群組]**** 欄位的 [編輯]**** 圖示。 如需詳細資訊，請參閱[設定群組宣告](../hybrid/how-to-connect-fed-group-claims.md)。

5. 若要新增宣告，請選取頁面頂端的 [新增宣告]****。 輸入 [名稱]**** 並選取適當的來源。 如果您選取 [屬性]**** 來源，則必須選擇您要使用的 [來源屬性]****。 如果您選取 [翻譯]**** 來源，則必須選擇您要使用的 [轉換]**** 和 [參數 1]****。 如需詳細資訊，請參閱[新增應用程式特定宣告](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)。 在完成時儲存變更。 

6. 選取 [儲存]  。 新的宣告會出現在資料表中。

   > [!NOTE]
   > 若想了解還有哪些方式可自訂從 Azure AD 到應用程式的 SAML 權杖，請參閱下列資源。
   >- 若要透過 Azure 入口網站建立自訂角色，請參閱[設定角色宣告](../develop/active-directory-enterprise-app-role-management.md)。
   >- 若要透過 PowerShell 自訂宣告，請參閱[自訂宣告 - PowerShell](../develop/active-directory-claims-mapping.md)。
   >- 若要修改應用程式資訊清單以設定應用程式的選擇性宣告，請參閱[設定選擇性宣告](../develop/active-directory-optional-claims.md)。
   >- 若要為重新整理權杖、存取權杖、工作階段權杖及識別碼權杖設定權杖存留期原則，請參閱[設定權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)。 或者，若要透過 Azure AD 條件式存取來限制驗證工作階段，請參閱[驗證工作階段管理功能](https://go.microsoft.com/fwlink/?linkid=2083106)。

## <a name="step-3-manage-the-saml-signing-certificate"></a>步驟 3： 管理 SAML 簽署憑證

Azure AD 會使用憑證來簽署它傳送至應用程式的 SAML 權杖。 您需要此憑證才能設定 Azure AD 和應用程式之間的信任。 如需憑證格式的詳細資料，請參閱應用程式的 SAML 文件。 如需詳細資訊，請參閱在 SAML 權杖中[管理同盟單一登入](manage-certificates-for-federated-single-sign-on.md)和[先進憑證簽署選項](certificate-signing-options.md)的憑證。

從 Azure AD 中，您可以直接從主要 [**以 SAML 設定單一登入**] 頁面下載使用 Base64 或原始格式的有效憑證。 或者，您可以藉由下載應用程式中繼資料 XML 檔案或使用應用程式同盟中繼資料 URL 來取得使用中的憑證。 若要查看、建立或下載您的憑證（作用中或非作用中），請遵循下列步驟。

1. 移至 [SAML 簽署憑證]**** 區段。 

   ![步驟3管理 SAML 簽署憑證](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. 請確認憑證具有：

   - *所需到期日。* 您可以將到期日設定為最多三年的未來。
   - *所需憑證的狀態為 [作用中]。* 如果狀態為 [**非**使用中]，請將狀態變更為 [**作用中]。** 若要變更狀態，請在所需的憑證資料列上按一下滑鼠右鍵，然後選取 [**讓憑證成為**使用中]。
   - *正確的簽署選項和演算法。*
   - *正確的通知電子郵件地址。* 當使用中的憑證接近到期日時，Azure AD 會將通知傳送至此欄位中設定的電子郵件地址。

2. 若要下載憑證，請選取 [Base64 格式]、[原始格式] 或 [同盟中繼資料 XML] 的其中一個選項。 Azure AD 也提供**應用程式同盟中繼資料 URL**，您可以在其中以 `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 格式存取應用程式特定的中繼資料。

3. 若要管理、建立或匯入憑證，請選取 [ **SAML 簽署憑證**] 區段右上角的 [**編輯**] 圖示（鉛筆）。

   ![SAML 簽署憑證](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   採取下列任何一項動作：

   - 若要建立新的憑證，請依序選取 [新增憑證]****、[到期日]**** 和 [儲存]****。 若要啟用憑證，請選取操作功能表 (**...**)，然後選取 [使憑證為使用中]****。
   - 若要上傳具有私密金鑰與 pfx 認證的憑證，請選取 [匯入憑證]****，然後瀏覽至憑證。 輸入 **PFX 密碼**，然後選取 [新增]****。  
   - 若要設定進階憑證簽署選項，請使用下列選項。 如需這些選項的描述，請參閱[進階憑證簽署選項](certificate-signing-options.md)一文。
      - 在 [簽署選項]**** 下拉式清單中，選擇 [簽署 SAML回應]****、[簽署 SAML 判斷提示]**** 或 [簽署 SAML 回應和判斷提示]****。
      - 在 [簽署演算法]**** 下拉式清單中，選擇 [SHA-1]**** 或 [SHA-256]****。
   - 若要在使用中的憑證即將到期時通知其他人員，請在 [通知電子郵件地址]**** 欄位中輸入電子郵件地址。

4. 如果您進行變更，請選取 [ **SAML 簽署憑證**] 區段頂端的 [**儲存**]。 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>步驟 4： 將應用程式設定為使用 Azure AD

[設定 \<applicationName>]**** 區段會列出必須在應用程式中設定而使其以 Azure AD 作為 SAML 識別提供者的值。 必要的值會根據應用程式而有所不同。 如需詳細資訊，請參閱應用程式的 SAML 文件。 若要尋找檔，請移至 [**設定\<應用程式名稱]>** ] 標題，然後選取 [**流覽逐步指示**]。 檔會出現在 [**設定**登入] 頁面中。 該頁面會引導您填寫 [**設定\<應用程式名稱]>** 標題中的 [**登入 Url**]、[ **Azure AD 識別碼**] 和 [**登出 URL** ] 值。

1. 向下捲動至 [設定 \<applicationName>]**** 區段。 
   
   ![步驟4設定應用程式](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. 視需要複製此區段中每個資料列的值，並依照應用程式特定的指示，將值新增至應用程式。 針對資源庫應用程式，您可以藉由選取 [檢視逐步指示]**** 來檢視文件。 
   - [登入 URL]**** 和 [登出 URL]**** 會解析為相同端點，此端點即是您 Azure AD 執行個體的 SAML 要求處理端點。 
   - [Azure AD 識別碼]**** 是核發給應用程式的 SAML 權杖中包含的 [簽發者]**** 值。
2. 當您將所有值貼入適當的欄位後，請選取 [儲存]****。

## <a name="step-5-validate-single-sign-on"></a>步驟 5。 驗證單一登入

將應用程式設定為使用 Azure AD 做為 SAML 型識別提供者之後，您就可以測試設定，以查看單一登入是否適用于您的帳戶。 

2. 捲動到 [驗證單一登入方式]**<applicationName>** 區段。

   ![步驟5驗證單一登入](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. 選取 [驗證]  。 測試選項隨即出現。

4. 選取 [以目前使用者身分登入]****。 

如果登入成功，您就可以將使用者和群組指派給 SAML 應用程式。
如果出現錯誤訊息，請完成下列步驟：

1. 請將訊息內容複製並貼到 [錯誤的描述為何?]**** 方塊中。

    ![取得解決方案指引](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. 選取 [取得解決方案指引]****。 隨即會顯示根本原因和解決方法指引。  在此範例中，未將使用者指派給應用程式。

3. 閱讀解決方案指引，然後修正此問題 (如果可能的話)。

4. 再次執行測試，直到順利完成。

如需詳細資訊，請參閱[在 Azure Active Directory 中，對應用程式進行以 SAML 為基礎的單一登入](../azuread-dev/howto-v1-debug-saml-sso-issues.md)。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [設定自動使用者帳戶布建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
