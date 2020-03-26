---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Netskope 系統管理員主控台整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Netskope 系統管理員主控台之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dec8f8065114b89dfa7bcaceee3f26855953dde2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74081799"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Netskope 系統管理員主控台整合

在本教學課程中，您將了解如何整合 Netskope 系統管理員主控台與 Azure Active Directory (Azure AD)。 在整合 Netskope 系統管理員主控台與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 Netskope 系統管理員主控台的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Netskope 系統管理員主控台。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Netskope 系統管理員主控台單一登入 (SSO) 的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Netskope 系統管理員主控台支援 **SP 和 IDP** 起始的 SSO

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>從資源庫新增 Netskope 系統管理員主控台

若要進行將 Netskope 系統管理員主控台整合到 Azure AD 中的設定，您必須從資源庫將 Netskope 系統管理員主控台新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Netskope 系統管理員主控台**。
1. 從結果面板中選取 [Netskope 系統管理員主控台]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>設定及測試 Netskope 系統管理員主控台的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Netskope 系統管理員主控台搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Netskope 系統管理員主控台中相關使用者之間的連結關聯性。

若要設定及測試與 Netskope 系統管理員主控台搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Netskope 系統管理員主控台 SSO](#configure-netskope-administrator-console-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Netskope 系統管理員主控台測試使用者](#create-netskope-administrator-console-test-user)** - 使 Netskope 系統管理員主控台中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Netskope 系統管理員主控台]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`Netskope_<OrgKey>`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 稍後會在教學課程中知道這些值的說明。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<tenantname>.goskope.com`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 請使用實際的登入 URL 來更新 [登入 URL] 值。 請聯絡 [Netskope 系統管理員主控台用戶端支援小組](mailto:support@netskope.com)，以取得登入 URL 值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. Netskope 系統管理員主控台應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增到 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，Netskope 系統管理員主控台應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 |  來源屬性|
    | ---------| --------- |
    | admin-role | user.assignedroles |

    > [!NOTE]
    > 請按一下[此處](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)來了解如何在 Azure AD 中建立角色。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Netskope 系統管理員主控台]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Netskope 系統管理員主控台的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Netskope 系統管理員主控台]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-netskope-administrator-console-sso"></a>設定 Netskope 系統管理員主控台 SSO

1. 在瀏覽器中開啟新索引標籤，以系統管理員的身分登入您的 Netskope 系統管理員主控台公司網站。

1. 從左側瀏覽面板中，按一下 [設定]  索引標籤。

    ![Netskope 系統管理員主控台設定](./media/netskope-cloud-security-tutorial/config-settings.png)

1. 按一下 [系統管理]  索引標籤。

    ![Netskope 系統管理員主控台設定](./media/netskope-cloud-security-tutorial/config-administration.png)

1. 按一下 [SSO]  索引標籤。

    ![Netskope 系統管理員主控台設定](./media/netskope-cloud-security-tutorial/config-sso.png)

1. 在 [網路設定]  區段上，執行下列步驟：
    
    ![Netskope 系統管理員主控台設定](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. 複製 [判斷提示取用者服務 URL]  值，並將其貼至 Azure 入口網站中 [基本 SAML 組態]  區段中的 [回覆 URL]  文字方塊內。

    b. 複製 [服務提供者實體識別碼]  值，並將其貼至 Azure 入口網站中 [基本 SAML 設定]  區段中的 [識別碼]  文字方塊內。

1. 按一下 [SSO/SLO 設定]  區段下的 [編輯設定]  。

    ![Netskope 系統管理員主控台設定](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. 在 [設定]  快顯示窗中，執行下列步驟：

    ![Netskope 系統管理員主控台設定](./media/netskope-cloud-security-tutorial/configuration.png)

    a. 選取 [啟用 SSO]  。

    b. 在 [IDP URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    c. 在 [IDP 實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    d. 在記事本中開啟您下載的 Base64 編碼憑證，將其內容複製到剪貼簿，然後貼到 [IDP 憑證]  文字方塊。

    e. 選取 [啟用 SSO]  。

    f. 在 [IDP SLO URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    g. 按一下 [提交]  。

### <a name="create-netskope-administrator-console-test-user"></a>建立 Netskope 系統管理員主控台測試使用者

1. 在瀏覽器中開啟新索引標籤，以系統管理員的身分登入您的 Netskope 系統管理員主控台公司網站。

1. 從左側瀏覽面板中，按一下 [設定]  索引標籤。

    ![Netskope 系統管理員主控台的使用者建立](./media/netskope-cloud-security-tutorial/config-settings.png)

1. 按一下 [作用中的平台]  索引標籤。

    ![Netskope 系統管理員主控台的使用者建立](./media/netskope-cloud-security-tutorial/user1.png)

1. 按一下 [使用者]  索引標籤。

    ![Netskope 系統管理員主控台的使用者建立](./media/netskope-cloud-security-tutorial/add-user.png)

1. 按一下 [加入使用者]  。

    ![Netskope 系統管理員主控台的使用者建立](./media/netskope-cloud-security-tutorial/user-add.png)

1. 輸入您要新增的使用者電子郵件地址，然後按一下 [新增]  。

    ![Netskope 系統管理員主控台的使用者建立](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Netskope 系統管理員主控台] 圖格時，應該會自動登入您已設定 SSO 的 Netskope 系統管理員主控台。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Netskope 系統管理員主控台](https://aad.portal.azure.com/)