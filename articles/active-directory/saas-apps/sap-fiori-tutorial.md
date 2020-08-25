---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 SAP Fiori 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP Fiori 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 4d2f6766fa32beb9cebaa8f77c04f6865e15e14d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543388"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SAP Fiori 整合

在此教學課程中，您將了解如何整合 SAP Fiori 與 Azure Active Directory (Azure AD)。 在整合 SAP Fiori 與 Azure AD 時，您可以︰

* 您可以在 Azure AD 中控制可存取 SAP Fiori 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SAP Fiori。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SAP Fiori 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* SAP Fiori 支援由 **SP** 起始的 SSO

> [!NOTE]
> 針對 SAP Fiori 起始的 iFrame 驗證，建議您在 SAML AuthnRequest 中使用 **IsPassive** 參數來進行無訊息驗證。 如需 **IsPassive** 參數的詳細資訊，請參閱 [Azure AD SAML 單一登入](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol) \(部分機器翻譯\) 資訊

## <a name="adding-sap-fiori-from-the-gallery"></a>從資源庫新增 SAP Fiori

若要進行將 SAP Fiori 整合到 Azure AD 中的設定，您必須從資源庫將 SAP Fiori 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 [SAP Fiori]  。
1. 從結果面板選取 [SAP Fiori]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>設定及測試 SAP Fiori 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 SAP Fiori 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SAP Fiori 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP Fiori 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 SAP Fiori SSO](#configure-sap-fiori-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 SAP Fiori 測試使用者](#create-sap-fiori-test-user)** - 使 SAP Fiori 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 開啟新的 Web 瀏覽器視窗，以系統管理員身分登入您的 SAP Fiori 公司網站。

1. 確定 **http** 和 **https** 服務為作用中，且相關連接埠已指派給交易代碼 **SMICM**。

1. 登入 SAP 系統 **T01** 的 SAP 商務用戶端，此為需要單一登入之處。 接著，啟用 HTTP 安全性工作階段管理。

    1. 移至交易代碼 **SICF_SESSIONS**。 此處會顯示所有的相關設定檔參數及其目前的值。 其內容如下範例所示：

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > 請根據您的組織需求調整這些參數。 上述參數僅供範例說明之用。

    1. 如有需要，請在 SAP 系統的執行個體 (預設) 設定檔中調整參數，並重新啟動 SAP 系統。

    1. 按兩下相關用戶端，以啟用 HTTP 安全性工作階段。

        ![SAP 中的 [相關設定檔參數] 頁面目前的值](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. 啟用下列 SICF 服務：

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. 在 SAP 系統 [**T01/122**] 的商務用戶端中，移至交易代碼 **SAML2**。 設定 UI 會在新的瀏覽器視窗中開啟。 在此範例中，我們使用 SAP 系統 122 的商務用戶端。

    ![SAP Fiori 商務用戶端登入頁面](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. 輸入您的使用者名稱和密碼，然後選取 [登入]  。

    ![SAP 中的 [ABAP 系統 T01/122] 頁面的 SAML 2.0 組態](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. 在 [提供者名稱]  方塊中，將 **T01122** 取代為 **http:\//T01122**，然後選取 [儲存]  。

    > [!NOTE]
    > 根據預設，提供者名稱會採用 \<sid>\<client> 的格式。 Azure AD 預期該名稱應採用 \<protocol>://\<name> 的格式。 建議您保留 https\://\<sid>\<client> 格式的提供者名稱，以便在 Azure AD 中設定多個 SAP Fiori ABAP 引擎。

    ![SAP 中的 [ABAP 系統 T01/122] 頁面的 SAML 2.0 組態中已更新的提供者名稱](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. 選取 [本機提供者] 索引標籤   > [中繼資料]  。

1. 在 [SAML 2.0 中繼資料]  對話方塊方塊中，下載產生的中繼資料 XML 檔案，並將其儲存在您的電腦上。

    ![SAP SAML 2.0 中繼資料對話方塊中的下載中繼資料連結](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SAP Fiori]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 如果您有**服務提供者中繼資料檔案**，請在 [基本 SAML 設定]  區段上執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]  。

    ![上傳中繼資料檔案](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    ![選擇中繼資料檔案](common/browse-upload-metadata.png)

    c. 成功上傳中繼資料檔案後，就會在 [基本 SAML 組態]  窗格中自動填入 [識別碼]  和 [回覆 URL]  值。 在 [登入 URL]  方塊中，輸入具有下列模式的 URL：`https:\//\<your company instance of SAP Fiori\>`。

    > [!NOTE]
    > 幾個客戶回報了有關於設定的 [回覆 URL]  值不正確的錯誤。 如果您看到此錯誤，可以使用下列 PowerShell 指令碼為您的執行個體設定正確的回覆 URL：
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > 您可以在執行指令碼之前自行設定 `ServicePrincipal` 物件識別碼，也可以在這裡傳遞。

1. SAP Fiori 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 若要管理這些屬性值，請在 [以 SAML 設定單一登入]  窗格中選取 [編輯]  。

    ![[使用者屬性] 窗格](common/edit-attribute.png)

1. 在 [使用者屬性和宣告]  窗格中，依照上圖的說明設定 SAML 權杖屬性。 然後完成下列步驟：

    1. 選取 [編輯]  以開啟 [管理使用者宣告]  窗格。

    1. 在 [轉換]  清單中，選取 [ExtractMailPrefix()]  。

    1. 從 [參數 1]  清單中，選取 [user.userprincipalname]  。

    1. 選取 [儲存]  。

       ![[管理使用者宣告] 窗格](./media/sapfiori-tutorial/nameidattribute.png)

       ![在 [管理使用者宣告] 窗格中的 [轉換] 區段](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 SAP Fiori]  區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAP Fiori 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SAP Fiori]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-sap-fiori-sso"></a>設定 SAP Fiori SSO

1. 登入 SAP 系統並移至交易代碼 **SAML2**。 此時會開啟含有 SAML 組態頁面的新瀏覽器視窗。

1. 若要設定信任的識別提供者 (Azure AD) 的端點，請選取 [信任的提供者]  索引標籤。

    ![SAP 中的 [信任的提供者] 索引標籤](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. 選取 [新增]  ，然後從操作功能表中選取 [上傳中繼資料檔案]  。

    ![SAP 中的新增和上傳中繼資料檔案選項](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. 上傳您在 Azure 入口網站中下載的中繼資料檔案。 選取 [下一步]  。

    ![選取要在 SAP 中上傳的中繼資料檔案](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. 在下一個頁面的 [別名]  方塊中，輸入別名名稱。 例如 **aadsts**。 選取 [下一步]  。

    ![SAP 中的 [別名] 方塊](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. 確定 [演算法]  方塊中的值為 **SHA-256**。 選取 [下一步]  。

    ![確認 SAP 中的摘要演算法值](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. 在 [單一登入端點]  下方選取 [HTTP POST]  ，然後選取 [下一步]  。

    ![SAP 中的單一登入端點選項](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. 在 [單一登出端點]  下方選取 [HTTP 重新導向]  ，然後選取 [下一步]  。

    ![SAP 中的單一登出端點選項](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. 在 [成品端點]  下方選取 [下一步]  ，以繼續操作。

    ![SAP 中的成品端點選項](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. 在 [驗證需求]  下方，選取 [完成]  。

    ![SAP 中的驗證需求選項和完成選項](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. 選取 [信任的提供者]   > [身分識別同盟]  (位於頁面底部)。 選取 [編輯]  。

    ![SAP 中的 [信任的提供者] 和 [身分識別同盟] 索引標籤](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. 選取 [新增]  。

    ![[身分識別同盟] 索引標籤上的新增選項](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. 在 [支援的 NameID 格式]  對話方塊中，選取 [未指定]  。 選取 [確定]  。

    ![SAP 中的 [支援的 NameID 格式] 對話方塊和選項](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    [使用者識別碼來源]  和 [使用者識別碼對應模式]  的值會決定 SAP 使用者與 Azure AD 宣告之間的連結。  

    **案例 1**：SAP 使用者與 Azure AD 使用者的對應

    1. 在 SAP 中的 [NameID 格式「未指定」的詳細資料]  下方，記下詳細資料：

        ![SAP 中的 [NameID 格式「未指定」的詳細資料] 對話方塊](./media/sapfiori-tutorial/nameiddetails.png)

    1. 在 Azure 入口網站中的 [使用者屬性和宣告]  下方，記下 Azure AD 中的必要宣告。

        ![Azure 入口網站中的 [使用者屬性和宣告] 對話方塊](./media/sapfiori-tutorial/claimsaad1.png)

    **案例 2**：根據 SU01 中已設定的電子郵件地址選取 SAP 使用者識別碼。 在此情況下，應針對每個需要 SSO 的使用者在 SU01 中設定電子郵件識別碼。

    1.  在 SAP 中的 [NameID 格式「未指定」的詳細資料]  下方，記下詳細資料：

        ![SAP 中的 [NameID 格式「未指定」的詳細資料] 對話方塊](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. 在 Azure 入口網站中的 [使用者屬性和宣告]  下方，記下 Azure AD 中的必要宣告。

       ![Azure 入口網站中的 [使用者屬性和宣告] 對話方塊](./media/sapfiori-tutorial/claimsaad2.png)

1. 選取 [儲存]  ，然後選取 [啟用]  以啟用識別提供者。

    ![SAP 中的儲存和啟用選項](./media/sapfiori-tutorial/configuration1.png)

1. 出現提示時，選取 [確定]  。

    ![SAP 中的 [SAML 2.0 組態] 對話方塊中的 [確定] 選項](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>建立 SAP Fiori 測試使用者

在本節中，您會在 SAP Fiori 中建立名為 Britta Simon 的使用者。 請與您內部的 SAP 專家小組合作，或與組織的 SAP 夥伴合作，在 SAP Fiori 平台中新增使用者。

## <a name="test-sso"></a>測試 SSO

1. 在 SAP Fiori 中啟用識別提供者 Azure AD 之後，請嘗試存取下列其中一個 URL，以測試單一登入 (系統應該不會提示您輸入使用者名稱和密碼)：

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > 請將 *sapurl* 取代為實際的 SAP 主機名稱。

1. 測試 URL 應該會將您導向至 SAP 中的下列測試應用程式頁面。 如果此頁面開啟，表示 Azure AD 單一登入已成功設定。

    ![SAP 中的標準測試應用程式頁面](./media/sapfiori-tutorial/testingsso.png)

1. 如果系統提示您輸入使用者名稱和密碼，請啟用追蹤以利診斷問題。 請使用下列 URL 進行追蹤：https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 SAP Fiori](https://aad.portal.azure.com/)
