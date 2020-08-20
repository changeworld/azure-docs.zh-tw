---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 SumoLogic 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SumoLogic 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: a14235cb4fc20147accc8eb8a0e421cbc4df90f8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533605"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SumoLogic 整合

在本教學課程中，您將了解如何整合 SumoLogic 與 Azure Active Directory (Azure AD)。 在整合 SumoLogic 與 Azure AD 時，您可以︰

* 在 Azure AD 中管控可存取 SumoLogic 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SumoLogic。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SumoLogic 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* SumoLogic 支援由 **IDP** 起始的 SSO

## <a name="adding-sumologic-from-the-gallery"></a>從資源庫新增 SumoLogic

若要設定 SumoLogic 與 Azure AD 整合，您需要從資源庫將 SumoLogic 加入受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **SumoLogic**。
1. 從結果面板中選取 [SumoLogic]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>設定及測試 SumoLogic 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 SumoLogic 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SumoLogic 中相關使用者之間的連結關聯性。

若要設定及測試與 SumoLogic 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 SumoLogic SSO](#configure-sumologic-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 SumoLogic 測試使用者](#create-sumologic-test-user)** - 使 SumoLogic 中 B.Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SumoLogic]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入]  頁面上，輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [SumoLogic 客戶支援小組](https://www.sumologic.com/contact-us/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. SumoLogic 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，SumoLogic 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    |  名稱 | 來源屬性 |
    | ---------------| --------------- |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |
    | 角色 | user.assignedroles |

    > [!NOTE]
    > 請按一下[這裡](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)，以了解如何在 Azure AD 中設定**角色**。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 SumoLogic]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 SumoLogic 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SumoLogic]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-sumologic-sso"></a>設定 SumoLogic SSO

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 SumoLogic 公司網站。

1. 移至 [管理] \> [安全性]  。

    ![管理](./media/sumologic-tutorial/ic778556.png "管理")

1. 按一下 [SAML]  。

    ![全域安全性設定](./media/sumologic-tutorial/ic778557.png "全域安全性設定")

1. 從 [選取組態或建立新的組態]  清單中選取 [Azure AD]  ，然後按一下 [設定]  。

    ![設定 SAML 2.0](./media/sumologic-tutorial/ic778558.png "設定 SAML 2.0")

1. 在 [設定 SAML 2.0]  對話方塊上，執行下列步驟：

    ![設定 SAML 2.0](./media/sumologic-tutorial/ic778559.png "設定 SAML 2.0")

    a. 在 [組態名稱]  文字方塊中，輸入 **Azure AD**。

    b. 選取 [偵錯模式]  。

    c. 在 [簽發者]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    d. 在 [驗證要求 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    e. 在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後將整個憑證貼到 [X.509 憑證]  文字方塊中。

    f. 在 [電子郵件屬性]  選取 [使用 SAML 主體]  。  

    g. 選取 [服務提供者起始登入組態]  。

    h. 在 [登入路徑]  文字方塊中輸入 **Azure**，然後按一下 [儲存]  。

### <a name="create-sumologic-test-user"></a>建立 SumoLogic 測試使用者

若要讓 Azure AD 使用者能夠登入 SumoLogic，必須將他們佈建到 SumoLogic。 SumoLogic 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **SumoLogic** 租用戶。

1. 移至 [管理] \> [使用者]  。

    ![使用者](./media/sumologic-tutorial/ic778561.png "使用者")

1. 按一下 [新增]  。

    ![使用者](./media/sumologic-tutorial/ic778562.png "使用者")

1. 在 [新增使用者]  對話方塊上，執行下列步驟：

    ![新增使用者](./media/sumologic-tutorial/ic778563.png "新增使用者")

    a. 在 [名字]  、[姓氏]  和 [電子郵件]  文字方塊中，輸入您想要佈建之 Azure AD 帳戶的相關資訊。
  
    b. 選取角色。
  
    c. 在 [狀態]  選取 [作用中]  。
  
    d. 按一下 [檔案]  。

> [!NOTE]
> 您可以使用任何其他的 SumoLogic 使用者帳戶建立工具或 SumoLogic 提供的 API，來佈建 Azure AD 使用者帳戶。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 SumoLogic 圖格時，應該會自動登入您設定 SSO 的 SumoLogic。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 SumoLogic](https://aad.portal.azure.com/)