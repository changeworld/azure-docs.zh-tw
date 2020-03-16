---
title: 教學課程：教學課程：Azure Active Directory 單一登入 (SSO) 與 SAP NetWeaver 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP NetWeaver 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 700f2ca4d46b3483531fa0784cb78699befb20ca
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897741"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SAP NetWeaver 整合

在本教學課程中，您會了解如何整合 SAP NetWeaver 與 Azure Active Directory (Azure AD)。 在整合 SAP NetWeaver 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 SAP NetWeaver 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SAP NetWeaver。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 SAP NetWeaver 單一登入 (SSO) 的訂用帳戶。
* 至少需要 SAP NetWeaver V7.20

## <a name="scenario-description"></a>案例描述

SAP NetWeaver 支援 **SAML** (**SP 起始的 SSO**) 和 **OAuth**。 在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 

> [!NOTE]
> 依據您的組織需求，以 SAML 或 OAuth 設定應用程式。 

## <a name="adding-sap-netweaver-from-the-gallery"></a>從資源庫新增 SAP NetWeaver

若要設定 SAP NetWeaver 與 Azure AD 整合，您需要從資源庫將 SAP NetWeaver 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **SAP NetWeaver**。
1. 從結果面板選取 [SAP NetWeaver]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>設定及測試 SAP NetWeaver 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 SAP NetWeaver 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SAP NetWeaver 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP NetWeaver 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[使用 SAML 設定 SAP NetWeaver](#configure-sap-netweaver-using-saml)** ，以在應用程式端設定 SSO 設定。
    1. **[建立 SAP NetWeaver 測試使用者](#create-sap-netweaver-test-user)** ，使 SAP NetWeaver 中 B.Simon 的對應身分連結到該使用者在 Azure AD 中的代表身分。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。
1. **[設定適用於 OAuth 的 SAP NetWeaver](#configure-sap-netweaver-for-oauth)** ，以在應用程式端設定 SSO 設定。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 SAP NetWeaver 設定 Azure AD 單一登入，請執行下列步驟：

1. 開啟新的 Web 瀏覽器視窗，以系統管理員身分登入您的 SAP NetWeaver 公司網站

1. 確定 **http** 和 **https** 服務為作用中，並已在 **SMICM** T-Code 中指派適當的連接埠。

1. 登入 SAP 系統 (T01) 的商務用戶端 (需要 SSO)，並啟用 HTTP 安全性工作階段管理。

    a. 移至交易代碼 **SICF_SESSIONS**。 它會顯示所有相關的設定檔參數以及目前的值。 如下所示：
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
    > 依照貴組織的需求來調整上述參數。這裡所提供的上述參數僅作為指示。

    b. 如果需要調整參數，在 SAP 系統的執行個體/預設設定檔中，重新啟動 SAP 系統。

    c. 在相關用戶端上按兩下，以啟用 HTTP 安全性工作階段。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. 啟用以下 SICF 服務：
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. 在 SAP 系統 [T01/122] 的商務用戶端中，移至交易代碼 **SAML2**。 它會在瀏覽器中開啟使用者介面。 在此範例中，我們假設 122 為 SAP 商務用戶端。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. 提供您的使用者名稱和密碼以在使用者介面中輸入，然後按一下 [編輯]  。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. 將 [提供者名稱]  從 T01122 取代為 `http://T01122`，然後按一下 [儲存]  。

    > [!NOTE]
    > 根據預設，提供者名稱格式為 `<sid><client>`，但 Azure AD 預期該名稱的格式為 `<protocol>://<name>`，因此建議將提供者名稱保持為 `https://<sid><client>`，以允許在 Azure AD 中設定多個 SAP NetWeaver ABAP 引擎。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **產生服務提供者中繼資料**：在 SAML 2.0 使用者介面上設定好 [本機提供者]  和 [信任的提供者]  設定後，下一個步驟是產生服務提供者的中繼資料檔案 (其中包含 SAP 中的所有設定、驗證內容和其他組態)。 產生這個檔案後，我們就需要在 Azure AD 中上傳此檔案。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. 移至 [本機提供者]  索引標籤。

    b. 按一下 [中繼資料]  。

    c. 在電腦上儲存產生的 [中繼資料 XML 檔案]  ，然後在 [基本 SAML 設定]  區段中上傳該檔案，以在 Azure 入口網站中自動填入 [識別碼]  和 [回覆 URL]  值。

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SAP NetWeaver]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]  ，上傳您稍早取得的 [服務提供者中繼資料檔案]  。

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    c. 當中繼資料檔案成功上傳後，會自動在 [基本 SAML 設定]  區段文字方塊中填入 [識別碼]  及 [回覆 URL]  值，如下所示：

    d. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > 我們注意到有少數客戶回報錯誤，指出我們為其執行個體設定的回覆 URL 不正確。 如果您收到任何這類錯誤，則可以使用下列 PowerShell 指令碼來加以解決，以便為您的執行個體設定正確的回覆 URL：
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal 物件識別碼可由您先自行設定，也可以先在此跳過。

1. SAP NetWeaver 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯]  圖示以開啟 [使用者屬性] 對話方塊。

    ![image](common/edit-attribute.png)

1. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    a. 按一下 [**編輯**] 圖示以開啟 [**管理使用者宣告**] 對話方塊。

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. 從 [轉換]  清單中，選取 [ExtractMailPrefix()]  。

    c. 從 [參數 1]  清單中，選取 [user.userprincipalname]  。

    d. 按一下 [檔案]  。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  來下載憑證，並將其儲存在電腦上。

   ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 SAP NetWeaver]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 SAP NetWeaver 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SAP NetWeaver]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-sap-netweaver-using-saml"></a>使用 SAML 設定 SAP NetWeaver

1. 登入 SAP 系統並移至交易程式碼 SAML2。 它會開啟包含 SAML 組態畫面的新瀏覽器視窗。

2. 若要設定信任的識別提供者 (Azure AD) 的端點，請移至 [信任的提供者]  索引標籤。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. 按下 [新增]  ，然後從操作功能表中選取 [上傳中繼資料檔案]  。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. 上傳您從 Azure 入口網站下載的中繼資料檔案。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. 在下一個畫面中輸入別名名稱。 例如 aadsts，然後按 [下一步]  繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. 確定 [摘要演算法]  應該是 **SHA-256**，而不需要任何變更，並且按 [下一步]  。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. 在 [單一登入端點]  上，使用 [HTTP POST]  並且按 [下一步]  繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. 在 [單一登入端點]  上，選取 [HTTPRedirect]  並且按 [下一步]  繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. 在 [成品端點]  ，按 [下一步]  繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. 在 [驗證需求]  上，按一下 [完成]  。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. 移至 [信任的提供者]  索引標籤 > [識別身分同盟]  (位於畫面底部)。 按一下 **[編輯]** 。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. 按一下 [識別身分同盟]  索引標籤 (視窗底部) 之下的 [新增]  。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. 在快顯視窗中，從 [支援的 NameID 格式]  中選取 [未指定]  ，然後按一下 [確定]。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. 請注意，[使用者識別碼來源]  和 [使用者識別碼對應模式]  值會決定 SAP 使用者與 Azure AD 宣告之間的連結。  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>案例：SAP 使用者與 Azure AD 使用者的對應。

    a. SAP 的 NameID 詳細資料螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. 提及 Azure AD 必要宣告的螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>案例：根據在 SU01 中設定的電子郵件地址選取 SAP 使用者識別碼。 在此情況下，應針對每位需要 SSO 的使用者在 su01 中設定電子郵件識別碼。

    a.  SAP 的 NameID 詳細資料螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. 提及 Azure AD 必要宣告的螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/claimsaad2.png)

15. 按一下 [儲存]  ，然後按一下 [啟用]  以啟用識別提供者。

    ![設定單一登入](./media/sapnetweaver-tutorial/configuration1.png)

16. 出現提示時，按一下 [確定]  。

    ![設定單一登入](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>建立 SAP NetWeaver 測試使用者

    在本節中，您要在 SAP NetWeaver 中建立名為 B.simon 的使用者。 請與您內部的 SAP 專家小組合作，或與貴組織的 SAP 夥伴合作，在 SAP NetWeaver 平台中新增使用者。

## <a name="test-sso"></a>測試 SSO

1. 啟用識別提供者 Azure AD 後，請嘗試存取下列 URL 來檢查 SSO (不會出現輸入使用者名稱和密碼的提示)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (或) 使用下列 URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > 以實際的 SAP 主機名稱取代 sapurl。

2. 上述 URL 應會帶您前往以下所述的畫面。 如果您能夠連到以下頁面，就表示 Azure AD SSO 成功完成設定。

    ![設定單一登入](./media/sapnetweaver-tutorial/testingsso.png)

3. 如果出現使用者名稱和密碼提示，請使用下面的 URL 來啟用追蹤，以診斷問題。

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>設定適用於 OAuth 的 SAP NetWeaver

1. 您可在下列位置取得 SAP 記載的程序：[NetWeaver 閘道服務啟用及 OAuth 2.0 範圍建立](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. 移至 SPRO，並尋找 [啟用和維護服務]  。

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth01.png)

3. 在此範例中，我們要使用 OAuth 將 OData 服務：`DAAG_MNGGRP` 連線到 Azure AD SSO。 使用技術服務名稱搜尋 `DAAG_MNGGRP` 服務，如果尚未啟動則加以啟動 (在 ICF 節點索引標籤底下查看 `green` 狀態)。 確定系統別名 (已連線的後端系統，也就是實際執行服務的位置) 是否正確。

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth02.png)

    * 然後按一下頂端按鈕列上的 [OAuth]  按鈕，並指派 `scope` (保留所提供的預設名稱)。

4. 我們範例中的範圍是 `DAAG_MNGGRP_001`，這是從自動新增數字的服務名稱所產生。 `/IWFND/R_OAUTH_SCOPES` 報表可以用來變更範圍的名稱，或以手動方式建立。

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > `soft state status is not supported` 訊息 – 可以忽略，並不會有問題。 如需詳細資訊，請參閱[這裡](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>建立 OAuth 2.0 用戶端的服務使用者

1. OAuth2 會使用 `service ID` 來代表使用者取得存取權杖。 OAuth 設計的重要限制：`OAuth 2.0 Client ID` 必須等同 OAuth 2.0 用戶端要求存取權杖時用於登入的 `username`。 因此，在我們的範例中，我們將註冊名為 CLIENT1 的 OAuth 2.0 用戶端，然後必須有相同名稱 (CLIENT1) 的使用者存在於 SAP 系統中，而且該使用者將設定為由參考應用程式使用。 

2. 註冊 OAuth 用戶端時，我們會使用 `SAML Bearer Grant type`。

    >[!NOTE]
    >如需詳細資訊，請從[這裡](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)參閱 SAML 持有人權杖授與類型的 OAuth 2.0 用戶端註冊

3. tcod：SU01 / 將使用者 CLIENT1 建立為 `System type` 並指派密碼，然後將其儲存，因為需將認證提供給 API 程式設計人員，他們會將密碼和使用者名稱寫入呼叫程式碼。 不應指派設定檔或角色。

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>使用建立精靈來註冊新的 OAuth 2.0 用戶端識別碼

1. 若要註冊新的 **OAuth 2.0 用戶端**，請啟動 **SOAUTH2** 交易。 交易將會顯示已註冊的 OAuth 2.0 用戶端概觀。 選擇 [建立]  ，為新的 OAuth 用戶端 (在此範例中稱為 CLIENT1) 啟動精靈。

2. 前往 T-Code：**SOAUTH2** 並提供描述，然後按 [下一步]  。

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth04.png)

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth05.png)

3. 從下拉式清單中選取已新增的 **SAML2 IdP – Azure AD**，然後儲存。

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth06.png)

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth07.png)

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth08.png)

4. 在範圍指派底下按一下 [新增]  ，以新增先前建立的範圍：`DAAG_MNGGRP_001`

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth09.png)

    ![設定單一登入](./media/sapnetweaver-tutorial/oauth10.png)

5. 按一下 [完成]  。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [試用搭配 Azure AD 的 SAP NetWeaver](https://aad.portal.azure.com/)