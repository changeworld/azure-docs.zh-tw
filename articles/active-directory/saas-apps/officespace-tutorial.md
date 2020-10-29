---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 OfficeSpace Software 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 OfficeSpace Software 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: bb994e154f410fb15b66588b41688326509a8f4a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518526"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 OfficeSpace Software 整合

在本教學課程中，您將了解如何整合 OfficeSpace Software 與 Azure Active Directory (Azure AD)。 在整合 OfficeSpace Software 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 OfficeSpace Software 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 OfficeSpace Software。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 OfficeSpace Software 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* OfficeSpace Software 支援由 **SP** 起始的 SSO


* OfficeSpace Software 支援 **Just In Time** 使用者佈建


## <a name="adding-officespace-software-from-the-gallery"></a>從資源庫加入 OfficeSpace Software

若要設定 OfficeSpace Software 與 Azure AD 整合，您需要從資源庫將 OfficeSpace Software 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **OfficeSpace Software** 。
1. 從結果面板中選取 [OfficeSpace Software]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>設定及測試 OfficeSpace Software 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 OfficeSpace Software 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 OfficeSpace Software 中相關使用者之間的連結關聯性。

若要設定及測試與 OfficeSpace Software 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 OfficeSpace Software SSO](#configure-officespace-software-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 OfficeSpace Software 測試使用者](#create-officespace-software-test-user)** - 使 OfficeSpace Software 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [OfficeSpace Software]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`<company name>.officespacesoftware.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [OfficeSpace Software 用戶端支援小組](mailto:support@officespacesoftware.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. OfficeSpace Software 應用程式會預期要有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 OfficeSpace Softwarex 應用程式會預期 **nameidentifier** 與 **user.mail** 對應，因此您必須按一下 [編輯]  圖示並變更屬性對應，來編輯屬性對應。

    ![image](common/edit-attribute.png)

1. 除了上述屬性外，OfficeSpace Software 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | ---------------| --------------- |
    | 電子郵件 | user.mail |
    | NAME | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. 在 [SAML 簽署憑證]  區段中，按一下 [編輯]  按鈕以開啟 [SAML 簽署憑證]  對話方塊。

    ![編輯 SAML 簽署憑證](common/edit-certificate.png)

1. 在 [SAML 簽署憑證]  區段中，複製 [指紋值]  並儲存在您的電腦上。

    ![複製指紋值](common/copy-thumbprint.png)

1. 在 [安裝 OfficeSpace Software]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 OfficeSpace Software 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [OfficeSpace Software]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="configure-officespace-software-sso"></a>設定 OfficeSpace Software SSO

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 OfficeSpace Software 租用戶。

2. 移至 [設定]  ，然後按一下 [連接器]  。

    ![顯示 [設定] 下拉式清單的螢幕擷取畫面，其中已選取 [連接器]。](./media/officespace-tutorial/tutorial_officespace_002.png)

3. 按一下 [SAML 驗證]  。

    ![顯示 [驗證] 區段的螢幕擷取畫面，其中已選取 [S A M L 驗證] 動作。](./media/officespace-tutorial/tutorial_officespace_003.png)

4. 在 [SAML 驗證]  區段中，執行下列步驟：

    ![在應用程式端設定單一登入](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. 在 [Logout provider url] \(登出提供者 URL\)  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    b. 在 [Client idp target url] \(用戶端 Idp 目標 URL\)  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    c. 將您從 Azure 入口網站複製的 [指紋]  值，貼到 [用戶端 IDP 憑證指紋]  文字方塊。 

    d. 按一下 [儲存設定]  。

### <a name="create-officespace-software-test-user"></a>建立 OfficeSpace Software 測試使用者

本節會在 OfficeSpace Software 中建立名為 B.Simon 的使用者。 OfficeSpace Software 支援預設會啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 OfficeSpace Software 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!NOTE]
> 如果您需要手動建立使用者，您需要連絡 [OfficeSpace Software 支援小組](mailto:support@officespacesoftware.com)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [OfficeSpace Software] 圖格時，應該會自動登入您已設定 SSO 的 OfficeSpace Software。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 OfficeSpace Software](https://aad.portal.azure.com/)