---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Moxtra 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Moxtra 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: 132da93bcb1e8138b63361cdb448c05b985cdf27
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940900"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Moxtra 整合

在本教學課程中，您將了解如何整合 Moxtra 與 Azure Active Directory (Azure AD)。 在整合 Moxtra 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Moxtra 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Moxtra。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Moxtra 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Moxtra 支援由 **SP** 起始的 SSO

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-moxtra-from-the-gallery"></a>從資源庫新增 Moxtra

若要設定將 Moxtra 整合到 Azure AD 中，您需要從資源庫將 Moxtra 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Moxtra**。
1. 從結果面板選取 [Moxtra]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>設定及測試 Moxtra 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Moxtra 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Moxtra 中相關使用者之間的連結關聯性。

若要設定及測試與 Moxtra 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Moxtra SSO](#configure-moxtra-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Moxtra 測試使用者](#create-moxtra-test-user)** - 使 Moxtra 中對應的 B.Simo 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Moxtra]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    在 [登入 URL]  文字方塊中，輸入 URL：`https://www.moxtra.com/service/#login`

1. Moxtra 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯]  圖示以開啟 [使用者屬性] 對話方塊。

    ![image](common/edit-attribute.png)

1. 除了以上屬性外，Moxtra 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 名稱 | 來源屬性|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | <Azure AD 識別碼>

    > [!Note]
    > **idpid** 值不是真實的屬性。 您可以從 [設定 Moxtra]  區段中的步驟 8 取得實際值。 

    1. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    1. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    1. 讓 [命名空間]  保持空白。

    1. 選取 [來源] 作為 [屬性]  。

    1. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    1. 按一下 [確定]  。

    1. 按一下 [檔案]  。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Moxtra]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Moxtra 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Moxtra]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-moxtra-sso"></a>設定 Moxtra SSO

1. 在另一個瀏覽器視窗中，以系統管理員身分登入您的 Moxtra 公司網站。

2. 在左邊工具列中，按一下 [管理主控台] > [SAML 單一登入]  ，然後按一下 [新增]  。
   
    ![此螢幕擷取畫面顯示 [SAML 單一登入] 頁面，其中包含建立新 SAML 單一登入的選項。](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. 在 [SAML]  頁面上，執行下列步驟：
   
    ![此螢幕擷取畫面顯示可輸入所述值的 [SAML] 頁面。](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. 在 [Name] \(名稱\)  文字方塊中，輸入您的設定名稱 (例如：*SAML*)。 
  
    b. 在 [IdP 實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。 
 
    c. 在 [登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。 
 
    d. 在 [AuthnContextClassRef]  文字方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**。 
 
    e. 在 [名稱識別碼格式]  文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**。 
 
    f. 在記事本中開啟您從 Azure 入口網站下載的憑證，複製其內容，然後貼到 [憑證]  文字方塊中。    
 
    g. 在 SAML 電子郵件網域文字方塊中，輸入您的 SAML 電子郵件網域。    
  
    >[!NOTE]
    >若要查看用來驗證網域的步驟，請按一下下方的 "**i**"。

    h. 按一下 [更新]  。

### <a name="create-moxtra-test-user"></a>建立 Moxtra 測試使用者

本節的目標是要在 Moxtra 中建立一個名為 B.simon 的使用者。

**若要在 Moxtra 中建立名為 B.simon 的使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 Moxtra 公司網站。

1. 在左邊工具列中，按一下 [管理主控台] > [使用者管理]  ，然後按一下 [新增使用者]  。
   
    ![顯示 [使用者管理] 頁面的螢幕擷取畫面，其中已選取 [新增使用者]。](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. 在 [加入使用者]  對話方塊中，執行下列步驟：
  
    a. 在 [名字]  文字方塊中，輸入 **B**。
  
    b. 在 [姓氏]  文字方塊中，輸入 **Simon**。
  
    c. 在 [電子郵件]  文字方塊中，輸入 B.simon 在 Azure 入口網站中的同一個電子郵件地址。
  
    d. 在 [事業處]  文字方塊中，輸入 **Dev**。
  
    e. 在 [部門]  文字方塊中，輸入 **IT**。
  
    f. 選取 [系統管理員]  。
  
    g. 按一下 [新增]  。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Moxtra] 圖格時，應該會自動登入您已設定 SSO 的 Moxtra。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Moxtra](https://aad.portal.azure.com/)

