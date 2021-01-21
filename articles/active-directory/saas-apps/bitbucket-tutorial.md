---
title: 教學課程：Azure Active Directory 與 SAML SSO for Bitbucket by resolution GmbH 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAML SSO for Bitbucket by resolution GmbH 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621242"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>教學課程：Azure Active Directory 與 SAML SSO for Bitbucket by resolution GmbH 整合

在本教學課程中，您將瞭解如何整合 SAML SSO for Bitbucket by 解析度 GmbH 和 Azure Active Directory (Azure AD) 。 當您將 SAML SSO for Bitbucket by 解析 GmbH 與 Azure AD 整合時，您可以：

* 在 Azure AD 中控制可存取 toSAML SSO for Bitbucket by 解析 GmbH 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 toSAML SSO for Bitbucket by 解析 GmbH。
* 在 Azure 入口網站中集中管理您的帳戶。


## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 SAML SSO for Bitbucket by resolution GmbH 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 SAML SSO for Bitbucket by resolution GmbH 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SAML SSO for Bitbucket by resolution GmbH 支援由 **SP 和 IDP** 起始的 SSO
* SAML SSO for Bitbucket by resolution GmbH 支援 **Just In Time** 使用者佈建


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>從資源庫新增 SAML SSO for Bitbucket by 解析 GmbH

若要設定 SAML SSO for Bitbucket by resolution GmbH 到 Azure AD 的整合，您必須從資源庫將 SAML SSO for Bitbucket by resolution GmbH 新增至受控 SaaS 應用程式的清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格上，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中，輸入 **SAML SSO for Bitbucket by 解析 GmbH** 。
1. 從結果中選取 [ **SAML SSO For Bitbucket by 解析 GmbH** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>設定及測試 SAML SSO for Bitbucket by 解析 GmbH 的 Azure AD SSO

使用名為 **Simon** 的測試使用者，設定及 Azure AD 測試與 SAML SSO for Bitbucket By 解析 GmbH 搭配使用的 sso。 若要讓 SSO 能夠運作，您必須在 Azure AD 使用者與 SAML SSO for Bitbucket by 解析 GmbH 中的相關使用者之間建立連結關聯性。

若要設定及測試與 SAML SSO for Bitbucket by 解析 GmbH Azure AD SSO，請執行下列步驟：


1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 SAML SSO For Bitbucket by 解析 GMBH SSO](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** -在應用程式端設定單一 Sign-On 設定。
    1. **[建立 SAML SSO for Bitbucket by resolution GmbH 測試使用者](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** - 使 SAML SSO for Bitbucket by resolution GmbH 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD SSO。
 
1. 在 Azure 入口網站的 **SAML SSO For Bitbucket by 解析 GmbH** 應用程式整合頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的鉛筆圖示，以編輯設定。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 若您想要以 **IDP** 起始模式設定應用程式，請在 [基本 SAML 組態] 區段執行下列步驟：


    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    c. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：
    
    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [SAML SSO for Bitbucket by resolution GmbH 用戶端支援小組](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下密碼。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAML SSO for Bitbucket by 解析 GmbH 的存取權授與 Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [SAML SSO for Bitbucket by resolution GmbH]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊的使用者清單中，選取 [B.Simon]  。 然後，選擇畫面底部的 [選取]  按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>設定 SAML SSO for Bitbucket by 解析 GmbH SSO

1. 以系統管理員身分登入 SAML SSO for Bitbucket by resolution GmbH 公司網站。

2. 在主要工具列右邊，按一下 [設定]  。

3. 移至 [帳戶] 區段，按一下功能表列上的 [SAML 單一登入]  。

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. 在 [SAML 單一登入外掛程式設定]  頁面上，按一下 [新增 idp]  。 

    ![新增 idp](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. 在 [選擇您的 SAML 識別提供者]  頁面上，執行下列步驟：

    ![識別提供者](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. 針對 [Idp 類型]  選取 [AZURE AD]  。

    b. 在 [名稱]  文字方塊中，輸入名稱。

    c. 在 [描述]  文字方塊中，輸入描述。

    d. 按 [下一步]  。

6. 在 [識別提供者設定]  頁面上，按 [下一步]  按鈕。

    ![識別設定](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  在 [匯入 SAML Idp 中繼資料]  頁面上，按一下 [載入檔案]  ，以上傳您已從 Azure 入口網站下載的 **中繼資料 XML** 檔案。

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. 按 [下一步]  。

9. 按一下 [儲存設定]  。

    ![儲存](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>建立 SAML SSO for Bitbucket by resolution GmbH 測試使用者

本節目標是在 SAML SSO for Bitbucket by resolution GmbH 中建立名為 Britta Simon 的使用者。 SAML SSO for Bitbucket by resolution GmbH 支援 Just-In-Time 佈建，而且也可以手動建立使用者，請根據您的每個需求連絡 [SAML SSO for Bitbucket by resolution GmbH Client 支援小組](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 SAML SSO for Bitbucket by 解析 GmbH 登入 URL，您可以在其中起始登入流程。  

* 直接前往 SAML SSO for Bitbucket by 解析 GmbH 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 按一下 [在 Azure 入口網站中 **測試這個應用程式** ，您應該會自動登入您已設定 SSO 的 SAML SSO for Bitbucket By 解析 GmbH。

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您在我的應用程式中按一下 [SAML SSO for Bitbucket by 解析 GmbH] 圖格時，如果在 SP 模式中設定，系統會將您重新導向至應用程式登入頁面以起始登入流程，如果是在 IDP 模式中設定，您應該會自動登入您已設定 SSO 的 SAML SSO for Bitbucket by 解析 GmbH。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 SAML SSO for Bitbucket by 解析 GmbH 之後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。