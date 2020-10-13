---
title: 教學課程：Azure Active Directory 與 EY GlobalOne 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 EY GlobalOne 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 1754929629bd05dc48419e521fa8e07f7c873217
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823403"
---
# <a name="tutorial-integrate-ey-globalone-with-azure-active-directory"></a>教學課程：整合 EY GlobalOne 與 Azure Active Directory

在本教學課程中，您會了解如何將 EY GlobalOne 與 Azure Active Directory (Azure AD) 進行整合。 在整合 EY GlobalOne 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 EY GlobalOne 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 EY GlobalOne。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 EY GlobalOne 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。
* EY GlobalOne 支援由 **SP 和 IDP** 起始的 SSO 
* EY GlobalOne 支援 **Just In Time** 使用者佈建。
* 設定 EY GlobalOne 之後，您可以強制執行工作階段控制項，以即時防止組織的敏感性資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-ey-globalone-from-the-gallery"></a>從資源庫新增 EY GlobalOne

若要設定將 EY GlobalOne 整合到 Azure AD 中，您需要從資源庫將 EY GlobalOne 加入受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **EY GlobalOne**。
1. 從結果面板選取 [EY GlobalOne]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-ey-globalone"></a>設定和測試 EY GlobalOne 的 Azure AD SSO

以名為 **B. Simon** 的測試使用者，設定及測試與 EY GlobalOne 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 EY GlobalOne 中相關使用者之間的連結關聯性。

若要設定及測試與 EY GlobalOne 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)**，以使用 B. Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)**，讓 B. Simon 能夠使用 Azure AD 單一登入。
1. **[設定 EY GlobalOne](#configure-ey-globalone)** 以在應用程式端設定 SSO 設定。
    * **[建立 EY GlobalOne 測試使用者](#create-ey-globalone-test-user)** ，使 EY GlobalOne 中對應的 B. Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [EY GlobalOne] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 設定區段上，已預先設定好應用程式，並已經為 Azure 預先填入必要的 URL。 使用者必須按一下 [儲存] 按鈕，才能儲存設定。

1. EY GlobalOne 應用程式需要特定格式的 SAML 判斷提示，您需要將自訂屬性對應新增到您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 **編輯** 圖示以開啟 [使用者屬性] 對話方塊。

    ![顯示 [使用者屬性] 區段的螢幕擷取畫面，其中已選取 [編輯] 圖示。](common/edit-attribute.png)

1. 除了以上屬性之外，EY GlobalOne 應用程式還預期 SAML 回應中會再多傳回幾個屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 名稱 | 來源屬性|
    | ---------------| --------------- |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |
    | 電子郵件 | user.mail |
    | 公司 | `<YOUR COMPANY NAME>` |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![顯示 [使用者宣告] 區段的螢幕擷取畫面，其中醒目提示 [新增宣告] 和 [儲存] 動作。](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，尋找 [憑證 (原始)] 並選取 [下載]，以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificateraw.png)

1. 在 [設定 EY GlobalOne] 區段上，根據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 B. Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B. Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B. Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 EY GlobalOne 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [EY GlobalOne]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]**** 對話方塊的 [使用者] 清單中選取 [B. Simon]****，然後按一下畫面底部的 [選取]**** 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-ey-globalone"></a>設定 EY GlobalOne

若要設定 **EY GlobalOne** 端的單一登入，您必須將從 Azure 入口網站下載的 [憑證 (原始)] 和複製的適當 URL 傳送給 [EY GlobalOne 支援小組](mailto:globalone.support@ey.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-ey-globalone-test-user"></a>建立 EY GlobalOne 測試使用者

本節會在 EY GlobalOne 中建立名為 Britta Simon 的使用者。 EY GlobalOne 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 EY GlobalOne 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [EY GlobalOne] 圖格時，應該會自動登入您已設定 SSO 的 EY GlobalOne。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)