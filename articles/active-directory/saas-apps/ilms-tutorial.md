---
title: 教學課程：Azure Active Directory 與 iLMS 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 iLMS 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 03b8110db94a08f44035e75371fd7641fcd91626
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826365"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>教學課程：整合 iLMS 與 Azure Active Directory

在本教學課程中，您會了解如何將 iLMS 與 Azure Active Directory (Azure AD) 進行整合。 在整合 iLMS 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 iLMS 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 iLMS。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 iLMS 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 iLMS 支援 **SP 和 IDP** 起始的 SSO

## <a name="adding-ilms-from-the-gallery"></a>從資源庫新增 iLMS

若要設定將 iLMS 整合到 Azure AD 中，您需要從資源庫將 iLMS 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **iLMS**。
1. 從結果面板選取 [iLMS]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 iLMS 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 iLMS 中相關使用者之間的連結關聯性。

若要設定及測試與 iLMS 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 iLMS SSO](#configure-ilms-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 iLMS 測試使用者](#create-ilms-test-user)** - 使 iLMS 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [iLMS]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，將您從 iLMS 系統管理入口網站中 SAML 設定的 [服務提供者]  區段複製的 [識別碼]  值貼上。

    b. 在 [回覆 URL]  文字方塊中，將您從 iLMS 系統管理入口網站中 SAML 設定的 [服務提供者]  區段複製的 [端點 (URL)]  值貼上，並包含下列模式：`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，將您從 iLMS 系統管理入口網站中 SAML 設定的 [服務提供者]  區段複製的 [端點 (URL)]  值貼上，如同：`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. 若要啟用 JIT 佈建，iLMS 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增到您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯]  圖示以開啟 [使用者屬性] 對話方塊。

    > [!NOTE]
    > 您必須啟用 iLMS 中的 [建立無法辨識的使用者帳戶]  來對應這些屬性。 請依照[這裡](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon)的指示，了解屬性的設定。

1. 除了以上屬性外，iLMS 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 名稱 | 來源屬性|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 iLMS]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-ilms-sso"></a>設定 iLMS SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **iLMS 系統管理入口網站**。

2. 按一下 [設定]  索引標籤下的 [SSO:SAML]  ，開啟 SAML 設定並執行下列步驟︰

    ![顯示 ILMS 設定索引標籤的螢幕擷取畫面，您可以在其中選取 [SSO：SAML]。](./media/ilms-tutorial/1.png)

3. 展開 [服務提供者]  區段，然後複製**識別碼**和**端點 (URL)** 值。

    ![顯示 [SAML 設定] 的螢幕擷取畫面，您可以在其中取得值。](./media/ilms-tutorial/2.png) 

4. 在 [識別提供者]  區段中，按一下 [匯入中繼資料]  。

5. 在 [SAML 簽章憑證]  區段中，選取從 Azure 入口網站下載的 [同盟中繼資料]  檔案。

    ![顯示 [SAML 設定] 的螢幕擷取畫面，您可以在其中選取中繼資料檔案。](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. 如果您想要啟用 JIT 佈建來建立無法辨識之使用者的 iLMS 帳戶，請遵循以下步驟︰

    a. 請勾選 [建立無法辨識的使用者帳戶]  。

    ![顯示 [建立無法辨識使用者帳戶] 選項的螢幕擷取畫面。](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. 將 Azure AD 中的屬性與 iLMS 中的屬性進行對應。 在屬性資料行中，指定屬性名稱或預設值。

    c. 移至 [商務規則]  索引標籤，然後執行下列步驟︰

    ![顯示 [商務規則] 設定的螢幕擷取畫面，您可以在其中輸入此步驟中的資訊。](./media/ilms-tutorial/5.png)

    d. 請勾選 [建立無法辨識的區域、事業處和部門]  ，來建立單一登入時尚未存在的區域、事業處和部門。

    e. 請勾選 [在登入期間更新使用者設定檔]  ，來指定使用者的設定檔是否隨著每個單一登入而更新。

    f. 如果勾選 [更新使用者設定檔中非必要欄位的空白值]  選項，則登入時為空白的選擇性設定檔欄位也將會造成使用者的 iLMS 設定檔包含這些欄位的空白值。

    g. 請勾選 [傳送錯誤通知電子郵件]  ，並輸入您要接收錯誤通知電子郵件之使用者的電子郵件。

7. 按一下 [儲存]  按鈕以儲存設定。

    ![顯示 [儲存] 按鈕的螢幕擷取畫面。](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 iLMS 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [iLMS]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，從使用者清單選取 **Britta Simon**，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-ilms-test-user"></a>建立 iLMS 測試使用者

應用程式支援及時 (Just In Time) 使用者佈建，而在驗證之後，則會在應用程式中自動建立使用者。 如果在 iLMS 系統管理入口網站設定 SAML 組態期間，您已按下 [建立無法辨識的使用者帳戶]  核取方塊，JIT 就會正常運作。

如果您需要手動建立使用者，請依照下列步驟進行︰

1. 以系統管理員身分登入您的 iLMS 公司網站。

2. 按一下[使用者]  索引標籤下的 [註冊使用者]  開啟 [註冊使用者]  頁面。

   ![顯示 ILMS 設定索引標籤的螢幕擷取畫面，您可以在其中選取 [註冊使用者]。](./media/ilms-tutorial/3.png)

3. 在 [註冊使用者]  頁面上，執行下列步驟。

    ![顯示 [註冊使用者] 頁面的螢幕擷取畫面，您可以在其中輸入指定的資訊。](./media/ilms-tutorial/create_testuser_add.png)

    a. 在 [名字]  文字方塊中輸入名字，例如 Britta。

    b. 在 [姓氏]  文字方塊中輸入姓氏，例如 Simon。

    c. 在 [電子郵件識別碼]  文字方塊中，輸入使用者的電子郵件地址，例如 BrittaSimon@contoso.com。

    d. 在 [區域]  下拉式清單中，選取區域的值。

    e. 在 [事業處]  下拉式清單中，選取事業處的值。

    f. 在 [部門]  下拉式清單中，選取部門的值。

    g. 按一下 [檔案]  。

    > [!NOTE]
    > 您也可以選取 [傳送註冊郵件]  核取方塊，將註冊郵件傳送給使用者。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [iLMS] 圖格時，應該會自動登入您已設定 SSO 的 iLMS。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
