---
title: 教學課程：Azure Active Directory 與 AirWatch 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 AirWatch 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 049066ffc5ce0aea2af956343dfa7ba97b6b5bb4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542814"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>教學課程：整合 AirWatch 與 Azure Active Directory

在本教學課程中，您將了解如何整合 AirWatch 與 Azure Active Directory (Azure AD)。 在整合 AirWatch 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 AirWatch 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 AirWatch。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 AirWatch 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 AirWatch 支援由 **SP** 起始的 SSO。

## <a name="adding-airwatch-from-the-gallery"></a>從資源庫新增 AirWatch

若要設定 AirWatch 與 Azure AD 整合，您需要從資源庫將 AirWatch 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **AirWatch**。
1. 從結果面板中選取 [AirWatch]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 AirWatch 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 AirWatch 中相關使用者之間的連結關聯性。

若要設定及測試與 AirWatch 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 AirWatch SSO](#configure-airwatch-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 AirWatch 測試使用者](#create-airwatch-test-user)** - 使 AirWatch 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
5. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [AirWatch]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

    1. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入如下的值：`AirWatch`

    > [!NOTE]
    > 這不是真正的值。 使用實際的「登入 URL」來更新此值。 請連絡 [AirWatch 用戶端支援小組](https://www.air-watch.com/company/contact-us/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. AirWatch 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image](common/edit-attribute.png)

1. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，使用 [編輯]  圖示來編輯宣告或使用 [新增宣告]  來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱 |  來源屬性|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  以下載中繼資料 XML，並將其儲存在電腦上。

   ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 AirWatch]  區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>設定 AirWatch SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 AirWatch 公司網站。

1. 在 [設定] 頁面上。 選取 [設定] > [企業整合] > [目錄服務]  。

   ![設定](./media/airwatch-tutorial/ic791921.png "設定")

1. 按一下 [使用者]  索引標籤，在 [基準 DN]  文字方塊中輸入您的網域名稱，然後按一下 [儲存]  。

   ![使用者](./media/airwatch-tutorial/ic791922.png "User")

1. 按一下 [伺服器]  索引標籤。

   ![Server](./media/airwatch-tutorial/ic791923.png "伺服器")

1. 在 [LDAP]  區段上執行下列步驟：

    ![上傳](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. 針對 [目錄類型]  ，選取 [無]  。

    b. 選取 [使用 SAML 進行驗證]  。

1. 在 [SAML 2.0]  區段上，若要上傳已下載的憑證，請按一下 [上傳]  。

    ![上傳](./media/airwatch-tutorial/ic791932.png "上傳")

1. 在 [要求]  區段中，執行下列步驟：

    ![要求](./media/airwatch-tutorial/ic791925.png "要求")  

    a. 針對 [要求繫結類型]  ，選取 [POST]  。

    b. 在 Azure 入口網站的 [設定在 AirWatch 單一登入]  對話頁面上，複製 [登入 URL]  值，然後將其貼到 [識別提供者單一登入 URL]  文字方塊中。

    c. 針對 [NameID 格式]  ，選取 [電子郵件地址]  。

    d. 選取 [無]  作為 [驗證要求安全性]  。

    e. 按一下 [檔案]  。

1. 再按一次 [使用者]  索引標籤。

    ![使用者](./media/airwatch-tutorial/ic791926.png "User")

1. 在 [屬性]  區段中，執行下列步驟：

    ![Attribute](./media/airwatch-tutorial/ic791927.png "屬性")

    a. 在 [物件識別碼]  文字方塊中，輸入 `http://schemas.microsoft.com/identity/claims/objectidentifier`。

    b. 在 [使用者名稱]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    c. 在 [顯示名稱]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    d. 在 [名字]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    e. 在 [姓氏]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。

    f. 在 [電子郵件]  文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    g. 按一下 [檔案]  。

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

在本節中，您會將 AirWatch 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [AirWatch]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-airwatch-test-user"></a>建立 AirWatch 測試使用者

若要讓 Azure AD 使用者能夠登入 AirWatch，必須將他們佈建到 AirWatch 中。 AirWatch 需以手動方式佈建。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **AirWatch** 公司網站。

2. 在左側導覽窗格中按一下 [帳戶]  ，然後按一下 [使用者]  。
  
   ![使用者](./media/airwatch-tutorial/ic791929.png "使用者")

3. 在 [使用者]  功能表中，按一下 [清單檢視]  ，然後按一下 [新增] > [新增使用者]  。
  
   ![加入使用者](./media/airwatch-tutorial/ic791930.png "新增使用者")

4. 在 [新增/編輯使用者]  對話方塊中，執行下列步驟：

   ![加入使用者](./media/airwatch-tutorial/ic791931.png "新增使用者")

   a. 在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的 [使用者名稱]  、[密碼]  、[確認密碼]  、[名字]  、[姓氏]  、[電子郵件地址]  。

   b. 按一下 [檔案]  。

> [!NOTE]
> 您可以使用任何其他的 AirWatch 使用者帳戶建立工具或 AirWatch 提供的 API，來佈建 Azure AD 使用者帳戶。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [AirWatch] 圖格時，應該會自動登入您已設定 SSO 的 AirWatch。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
