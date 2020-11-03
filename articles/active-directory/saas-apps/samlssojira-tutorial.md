---
title: 教學課程：Azure Active Directory 與 SAML SSO for Jira by resolution GmbH 的整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAML SSO for Jira by resolution GmbH 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: fe241a3fd74e1421f1bd3d39087fe776ee7b61d9
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891494"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>教學課程：Azure Active Directory 與 SAML SSO for Jira by resolution GmbH 整合

在此教學課程中，您將了解如何設定 SAML SSO for Jira by resolution GmbH 與 Azure Active Directory (Azure AD)。
SAML SSO for Jira by resolution GmbH 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制哪些人可以使用 resolution GmbH 的 SAML SSO 外掛程式登入 Jira。
* 您可以透過使用 SAML SSO for Jira by resolution GmbH，讓使用者使用他們的 Azure AD 帳戶自動登入 Jira (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 SAML SSO for Jira by resolution GmbH 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 SAML SSO for Jira by resolution GmbH 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SAML SSO for Jira by resolution GmbH 支援 **SP** 和 **IDP** 起始 SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>新增企業應用程式以用於單一登入

若要在 Azure AD 中設定單一登入，您需要新增新的企業應用程式。 程式庫中已有針對此目的預先設定好的應用程式，也就是 **SAML SSO for Jira by resolution GmbH** 。

**若要從資源庫新增 SAML SSO for Jira by resolution GmbH，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入 **SAML SSO for Jira by resolution GmbH** ，從結果面板中選取 [SAML SSO for Jira by resolution GmbH]，然後按一下 [新增] 按鈕以新增應用程式。 您也可以變更企業應用程式的名稱。

     ![結果清單中的 SAML SSO for Jira by resolution GmbH](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>使用 SAML SSO 外掛程式和 Azure AD 設定並測試單一登入

在此節中，您將會針對 Azure AD 使用者測試並設定單一登入，以方便他們登入 Jira。 我們會針對名為 **Britta Simon** 的測試使用者完成設定與測試。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 SAML SSO for Jira by resolution GmbH 中相關使用者之間的連結關聯性。

若要設定並測試單一登入，您需要完成下列步驟：

1. **[設定 Azure AD 企業應用程式以進行單一登入](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** - 設定 Azure AD 企業應用程式以進行單一登入
2. **[設定您 Jira 執行個體的 SAML SSO 外掛程式](#configure-the-saml-sso-plugin-of-your-jira-instance)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 在 Azure AD 中建立測試使用者。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓測試使用者能夠使用單一登入登入 Azure 端。
1. **[在 Jira 中建立測試使用者](#create-the-test-user-also-in-jira)** - 在 Jira 中為 Azure AD 測試使用者建立互相對應的測試使用者。
1. **[測試單一登入](#test-single-sign-on)** - 驗證設定是否能運作。

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>設定 Azure AD 企業應用程式以進行單一登入

在此節中，您會在 Azure 入口網站中設定單一登入。

若要設定使用 SAML SSO for Jira by resolution GmbH 進行單一登入，請執行下列步驟：

1. 前往 [Azure 入口網站](https://portal.azure.com/)，在剛剛建立的 **SAML SSO for Jira by resolution GmbH** 企業應用程式左側面板中選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 為 [選取單一登入方法] 選取 [SAML] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 然後按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段中，如果想要應用程式使用 **IDP** 起始模式，請執行下列步驟：

    ![顯示 [基本 SAML 設定] 區段的螢幕擷取畫面，其中已反白顯示 [識別碼] 和 [回覆 U R L] 文字方塊，並已選取 [儲存] 按鈕。](common/idp-intiated.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    c. 按一下 [設定其他 URL] 並執行下列步驟 (如果想要設定應用程式使用 **SP** 起始模式)：

    ![SAML SSO for Jira by resolution GmbH 網域和 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > 對於識別碼、回覆 URL 和登入 URL，請使用您 Jira 執行個體的基底 URL 取代 **\<server-base-url>** 。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。 如果您有任何問題，請連絡我們的 [SAML SSO for Jira by resolution GmbH 客戶支援小組](https://www.resolution.de/go/support) \(英文\)。

4. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，下載 **同盟中繼資料 XML** 並儲存到您的電腦。

    ![憑證下載連結](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>設定您 Jira 執行個體的 SAML SSO 外掛程式 

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Jira 執行個體。

2. 將滑鼠指標停留在右側的齒輪圖示上方，然後按一下 [管理應用程式]。
    
    ![螢幕擷取畫面：顯示指向 [齒輪] 圖示的箭號，以及已從下拉式選單中選取 [管理應用程式]。](./media/samlssojira-tutorial/addon1.png)

3. 如果系統將您重新導向至 [管理員存取] 頁面，請輸入 [密碼] 並按一下 [確認] 按鈕。

    ![顯示 [系統管理員存取權] 頁面的螢幕擷取畫面。](./media/samlssojira-tutorial/addon2.png)

4. Jira 通常會將您重新導向至 Atlassian marketplace。 如果沒有，請按一下左側面板中的 [尋找新的應用程式]。 搜尋 **SAML Single Sign On (SSO) for JIRA** ，然後按一下 [安裝] 按鈕以安裝 SAML 外掛程式。

    ![顯示 [Atlassian Marketplace for JIRA] 頁面的螢幕擷取畫面，其中有箭號指向 "SAML Single Sign On (SSO) Jira, SAML/SSO" 應用程式的 [安裝] 按鈕。](./media/samlssojira-tutorial/store.png)

5. 外掛程式將會開始安裝。 完成時，按一下 [關閉] 按鈕。

    ![顯示 [安裝中] 對話方塊的螢幕擷取畫面。](./media/samlssojira-tutorial/store-2.png)

    ![顯示 [已安裝並準備就緒!] 對話方塊的螢幕擷取畫面， 其中已選取 [關閉] 按鈕。](./media/samlssojira-tutorial/store-3.png)

6. 然後，按一下 [管理]。

    ![螢幕擷取畫面：顯示已選取 [管理] 按鈕的 "SAML Single Sign On (SSO) Jira, SAML/SSO" 應用程式。](./media/samlssojira-tutorial/store-4.png)
    
8. 之後按一下 [設定] 以設定剛剛安裝的外掛程式。

    ![顯示 [管理應用程式] 頁面的螢幕擷取畫面，其中已針對 "SAML SingleSignOn for Jira" 應用程式選取 [設定] 按鈕。](./media/samlssojira-tutorial/store-5.png)

9. 在 [SAML SingleSignOn 外掛程式設定] 精靈中，按一下 [新增 IdP] 將 Azure AD 設定為新的身分識別提供者。

    ![顯示 [歡迎使用] 頁面的螢幕擷取畫面，其中已選取 [新增 IdP] 按鈕。](./media/samlssojira-tutorial/addon4.png) 

10. 在 [選擇您的 SAML 身分識別提供者] 頁面上，執行下列步驟：

    ![顯示 [選擇 SAML 識別提供者] 頁面的螢幕擷取畫面，其中已醒目提示 [IdP 類型] 和 [名稱] 文字方塊，並已選取 [下一步] 按鈕。](./media/samlssojira-tutorial/addon5a.png)
 
    a. 將 **Azure AD** 設定為識別提供者類型。
    
    b. 新增身分識別提供者的 [名稱] (例如 Azure AD)。
    
    c. (選擇性) 新增身分識別提供者的 [描述] (例如 Azure AD)。
    
    d. 按 [下一步] 。
    
11. 在 [識別提供者設定] 頁面上，按 [下一步] 按鈕。
 
    ![顯示 [識別提供者設定] 頁面的螢幕擷取畫面。](./media/samlssojira-tutorial/addon5b.png)

12. 在 [匯入 SAML 識別提供者中繼資料] 頁面上，執行下列步驟：

    ![螢幕擷取畫面：顯示已選取 [選取中繼資料 XML 檔案] 動作的 [匯入 SAML IdP 中繼資料] 頁面。](./media/samlssojira-tutorial/addon5c.png)

    a. 按一下 [選取中繼資料 XML 檔案] 按鈕，然後選擇您之前下載的 **同盟中繼資料 XML** 檔案。

    b. 按一下 [匯入] 按鈕。
     
    c. 請稍候以等待成功匯入。  
     
    d. 按 [下一步] 按鈕。
    
13. 在 [使用者識別碼屬性與轉換] 頁面上，按一下 [下一步] 按鈕。

    ![螢幕擷取畫面：顯示 [使用者識別碼屬性和轉換] 頁面，並已選取 [下一步] 按鈕。](./media/samlssojira-tutorial/addon5d.png)
    
14. 在 [使用者建立與更新] 頁面上，按一下 [儲存並繼續下一步] 以儲存設定。
    
    ![顯示 [使用者建立和更新] 頁面的螢幕擷取畫面，其中已選取 [儲存並繼續下一步] 按鈕。](./media/samlssojira-tutorial/addon6a.png)
    
15. 在 [測試您的設定] 頁面上，按一下 [跳過測試和手動設定] 以暫時略過使用者測試。 此作業將在下一節中執行，且需要在 Azure 入口網站中進行一些設定。
    
    ![顯示 [測試設定] 頁面的螢幕擷取畫面，其中已選取 [跳過測試和手動設定] 按鈕。](./media/samlssojira-tutorial/addon6b.png)
    
16. 按一下 [確定] 以略過警告。
    
    ![螢幕擷取畫面：顯示已選取 [確定] 按鈕的警告對話方塊。](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。 您將透過使用者測試單一登入。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 選擇畫面頂端的 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者內容] 中執行下列步驟：

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中輸入 **Britta Simon** 。
  
    b. 在 [使用者名稱] 欄位中，輸入 <b>BrittaSimon@contoso.com</b>。

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此節中，您會將 Britta Simon 新增至企業應用程式，讓她能夠使用單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。 

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，搜尋您已在此教學課程開始時建立的企業應用程式。 如果您是遵循此教學課程的步驟進行，其名稱為 **SAML SSO for Jira by resolution GmbH** 。 如果您已指定其他名稱，請搜尋該名稱。

    ![應用程式清單中的 SAML SSO for Jira by resolution GmbH 連結](common/all-applications.png)

3. 在左側面板中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊中，從使用者清單選取 **Britta Simon** ，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期 SAML 判斷提示中會有任何角色值，請在 [選取角色] 對話方塊中，從清單選取適當的使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-the-test-user-also-in-jira"></a>也在 Jira 中建立測試使用者

若要讓 Azure AD 使用者能夠登入 SAML SSO for Jira by resolution GmbH，必須將他們佈建到 SAML SSO for Jira by resolution GmbH 之中。 在此教學課程的案例中，您必須手動佈建。 但是 resolution 的 SAML SSO 外掛程式也支援其他佈建模式，例如 **Just In Time** 佈建。 請前往 [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all) \(英文\) 參閱其文件。 如果您有任何相關疑問，請洽詢 [resolution 支援](https://www.resolution.de/go/support) \(英文\)。

**若要手動佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入 Jira 執行個體。

2. 將滑鼠指標停留在齒輪上方，然後選取 [使用者管理]。

   ![螢幕擷取畫面：顯示指向 [齒輪] 圖示的箭號，並已從下拉式選單中選取 [使用者管理]。](./media/samlssojira-tutorial/user1.png)

3. 如果系統將您重新導向至 [管理員存取] 頁面，請輸入 [密碼] 並按一下 [確認] 按鈕。

    ![顯示 [系統管理員存取權] 頁面的螢幕擷取畫面，其中已醒目提示 [密碼] 文字方塊。](./media/samlssojira-tutorial/user2.png) 

4. 在 [使用者管理] 索引標籤區段底下，按一下 [建立使用者]。

    ![顯示 [使用者管理] 索引標籤的螢幕擷取畫面，其中已選取 [建立使用者] 按鈕。](./media/samlssojira-tutorial/user3-new.png) 

5. 在 [建立新的使用者] 對話方塊頁面上，執行下列步驟。 您必須建立和 Azure AD 中完全相同的使用者：

    ![新增員工](./media/samlssojira-tutorial/user4-new.png) 

    a. 在 [電子郵件地址] 文字方塊中，輸入使用者的電子郵件地址：<b>BrittaSimon@contoso.com</b>。

    b. 在 [全名] 文字方塊中，輸入使用者的全名： **Britta Simon** 。

    c. 在 [使用者名稱] 文字方塊中，輸入使用者的電子郵件地址：<b>BrittaSimon@contoso.com</b>。 

    d. 在 [密碼] 文字方塊中，輸入使用者的密碼。

    e. 按一下 [建立使用者] 以完成使用者建立作業。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下 [存取面板] 中的 [SAML SSO for Jira by resolution GmbH] 圖格時，應該會自動登入您已設定 SSO 的 SAML SSO for Jira by resolution GmbH。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

如果您瀏覽至 `https://<server-base-url>/plugins/servlet/samlsso`，也可以測試單一登入。 以 Jira 執行個體的基底 URL 取代 **\<server-base-url>** 。


## <a name="enable-single-sign-on-redirection-for-jira"></a>為 Jira 啟用單一登入重新導向

如前一節所述，目前有兩種方式可觸發單一登入。 使用 **Azure 入口網站** ，或使用 **您 Jira 執行個體的特殊連結** 。 resolution GmbH 的 SAML SSO 外掛程式也可讓您透過簡單地 **存取任何指向您 Jira 執行個體的 URL** ，以觸發單一登入。

在本質上，所有存取 Jira 的使用者將會在啟用外掛程式中的任一選項之後，被重新導向至單一登入。

若要啟用 SSO 重新導向，請在 **您的 Jira 執行個體** 中執行下列動作：

1. 在 Jira 中存取 SAML SSO 外掛程式的 [設定] 頁面。
1. 按一下左側面板中的 [重新導向]。

   ![Jira SAML SingleSignOn 外掛程式設定頁面的部分螢幕擷取畫面，並醒目提示左側導覽中的重新導向連結。](./media/samlssojira-tutorial/ssore1.png)

1. 選取 [啟用 SSO 重新導向]。

   ![Jira SAML SingleSignOn 外掛程式設定頁面的部分螢幕擷取畫面，並醒目提示已選取的 [啟用 SSO 重新導向] 核取方塊。](./media/samlssojira-tutorial/ssore2.png) 

1. 按下右上角的 [儲存設定] 按鈕。

啟用此選項之後，如果已選取 [啟用 nosso] 選項，就仍可透過瀏覽至 `https://\<server-base-url>/login.jsp?nosso` 來取得使用者名稱/密碼提示。 一如往常，請以您的基底 URL 取代 **\<server-base-url>** 。


## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)