---
title: 教學課程：Azure Active Directory 與 SAML SSO for Bamboo by resolution GmbH 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAML SSO for Bamboo by resolution GmbH 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 286d350493afb1e74f9d2ba6eb445b2c46d995b6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713562"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>教學課程：Azure Active Directory 與 SAML SSO for Bamboo by resolution GmbH 整合

在本教學課程中，您將了解如何整合 SAML SSO for Bamboo by resolution GmbH 與 Azure Active Directory (Azure AD)。
SAML SSO for Bamboo by resolution GmbH 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 SAML SSO for Bamboo by resolution GmbH 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 SAML SSO for Bamboo by resolution GmbH (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 SAML SSO for Bamboo by resolution GmbH 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 SAML SSO for Bamboo by resolution GmbH 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SAML SSO for Bamboo by resolution GmbH 支援由 **SP 和 IDP** 起始的 SSO
* SAML SSO for Bamboo by resolution GmbH 支援 **Just In Time** 使用者佈建

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>從資源庫新增 SAML SSO for Bamboo by resolution GmbH

若要設定 SAML SSO for Bamboo by resolution GmbH 與 Azure AD 的整合，您必須從資源庫將 SAML SSO for Bamboo by resolution GmbH 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 SAML SSO for Bamboo by resolution GmbH，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **SAML SSO for Bamboo by resolution GmbH**、從結果面板中選取 [SAML SSO for Bamboo by resolution GmbH]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 SAML SSO for Bamboo by resolution GmbH](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 SAML SSO for Bamboo by resolution GmbH 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 SAML SSO for Bamboo by resolution GmbH 中相關使用者之間的連結關聯性。

若要使用 SAML SSO for Bamboo by resolution GmbH 設定及測試 Azure AD 單一登入，您需要完成下列基本要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 SAML SSO for Bamboo by resolution GmbH 單一登入](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 SAML SSO for Bamboo by resolution GmbH 測試使用者](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** ：讓 SAML SSO for Bamboo by resolution GmbH 中有對應 Britta Simon 的使用者，並連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 SAML SSO for Bamboo by resolution GmbH 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SAML SSO for Bamboo by resolution GmbH]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![SAML SSO for Bamboo by resolution GmbH 網域和 URL 單一登入資訊。](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![顯示您可以在其中輸入登入 URL 的設定額外 URL 螢幕擷取畫面。](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [SAML SSO for Bamboo by resolution GmbH 用戶端支援小組](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) \(英文\) 以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

7. 在 [設定 SAML SSO for Bamboo by resolution GmbH]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>設定 SAML SSO for Bamboo by resolution GmbH 單一登入

1. 以系統管理員身分登入 SAML SSO for Bamboo by resolution GmbH 公司網站。

1. 在主要工具列右邊，按一下 [Settings]  \(設定\) > [Add-ons]  \(附加元件\)。

    ![設定](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. 移至 [SECURITY] \(安全性\) 區段，按一下功能表列上的 [SAML SingleSignOn]  \(SAML 單一登入\)。

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. 在 [SAML 單一登入外掛程式設定]  頁面上，按一下 [新增 idp]  。

    ![新增 idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. 在 [選擇您的 SAML 識別提供者]  頁面上，執行下列步驟：

    ![識別提供者](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. 針對 [Idp 類型]  選取 [AZURE AD]  。

    b. 在 [名稱]  文字方塊中，輸入名稱。

    c. 在 [描述]  文字方塊中，輸入描述。

    d. 按 [下一步]  。

1. 在 [Identity provider configuration]  \(識別提供者設定\) 頁面上，按一下 [Next]  \(下一步\) 按鈕。

    ![識別設定](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. 在 [匯入 SAML Idp 中繼資料]  頁面上，按一下 [載入檔案]  ，以上傳您已從 Azure 入口網站下載的**中繼資料 XML** 檔案。

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. 按 [下一步]  。

1. 按一下 [儲存設定]  。

    ![儲存](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAML SSO for Bamboo by resolution GmbH 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [SAML SSO for Bamboo by resolution GmbH]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [SAML SSO for Bamboo by resolution GmbH]  。

    ![應用程式清單中的 SAML SSO for Bamboo by resolution GmbH](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>建立 SAML SSO for Bamboo by resolution GmbH 測試使用者

本節目標是在 SAML SSO for Bamboo by resolution GmbH 中建立名為 Britta Simon 的使用者。 SAML SSO for Bamboo by resolution GmbH 支援 Just-In-Time 佈建，而且也可以手動建立使用者。請根據您的需求來連絡 [SAML SSO for Bamboo by resolution GmbH 用戶端支援小組](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) \(英文\)。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下 [存取面板] 中的 [SAML SSO for Bamboo by resolution GmbH] 圖格時，應該會自動登入您已設定 SSO 的 SAML SSO for Bamboo by resolution GmbH。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
