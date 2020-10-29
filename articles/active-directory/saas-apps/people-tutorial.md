---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 People 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 People 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 8ddebffcfc70440d5ce0988db87b4f6626531c73
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 People 整合

在本教學課程中，您將了解如何整合 People 與 Azure Active Directory (Azure AD)。 在整合 People 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 People 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 People。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 People 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* People 支援由 **SP** 起始的 SSO
* People 行動應用程式現在可以搭配 Azure AD 設定來啟用 SSO。 在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

>[!NOTE]
>此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-people-from-the-gallery"></a>從資源庫新增 People

若要設定將 People 整合到 Azure AD 中，您需要從資源庫將 People 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **People** 。
1. 從結果面板選取 [People]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-people"></a>設定及測試 People 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 People 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 People 中相關使用者之間的連結關聯性。

若要設定及測試與 People 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
2. **[設定 People SSO](#configure-people-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 People 測試使用者](#create-people-test-user)** - 使 People 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [People]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<company name>.peoplehr.net`

    b. 在 [識別碼]  方塊中，輸入 URL：`https://www.peoplehr.com`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「回覆 URL」來更新這些值。 請連絡 [People 用戶端支援小組](mailto:customerservices@peoplehr.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

4. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  來下載憑證，並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 People]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 People 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取[People]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-people-sso"></a>設定 People SSO

1. 若要自動執行 People 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 People]  便會將您導向到 People 應用程式。 請從該處提供用以登入 People 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 People，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 People 公司網站，然後執行下列步驟：
   
4. 在左側功能表中，按一下 [設定]  。

    ![顯示左側功能表的螢幕擷取畫面，其中已選取 [設定]。](./media/people-tutorial/tutorial_people_001.png)

5. 按一下 [公司]  。

    ![顯示已從 [設定] 功能表中選取 [公司] 的螢幕擷取畫面。](./media/people-tutorial/tutorial_people_002.png)

6. 在 [上傳單衣登入 SAML 中繼資料檔案]  上，按一下 [瀏覽]  上傳下載的中繼資料檔案。

    ![設定單一登入](./media/people-tutorial/tutorial_people_003.png)

### <a name="create-people-test-user"></a>建立 People 測試使用者

在本節中，您要在 People 中建立名為 B.Simon 的使用者。 請與 [People 客戶支援小組](mailto:customerservices@peoplehr.com)合作，在 People 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [People] 圖格時，應該會自動登入您設定 SSO 的 People。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="test-sso-for-people-mobile"></a>測試 People 的 SSO (行動裝置)

1. 開啟 People 行動應用程式。 在登入頁面上，輸入 **電子郵件識別碼** ，然後按一下 [單一登入]  。

    ![登入](./media/people-tutorial/test01.png)

2. 輸入 **組織使用者識別碼** ，然後按 [下一步]  。

    ![電子郵件](./media/people-tutorial/test02.png)

3. 最後，成功登入之後，應用程式首頁會隨即顯示，如下所示：

    ![一次](./media/people-tutorial/test03.png)

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [搭配 Azure AD 試用 People](https://aad.portal.azure.com)