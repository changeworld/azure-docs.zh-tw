---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Slack 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Slack 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c80963976783321d05fc6f32bb24daed36fa105
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985528"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Slack 整合

在本教學課程中，您將了解如何整合 Slack 與 Azure Active Directory (Azure AD)。 在整合 Slack 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Slack。
* 讓使用者使用其 Azure AD 帳戶自動登入 Slack。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Slack 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Slack 支援 **SP** 起始的 SSO
* Slack 支援 **Just In Time** 使用者佈建
* Slack 支援[**自動**使用者佈建](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial)
* 設定 Slack 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-slack-from-the-gallery"></a>從資源庫新增 Slack

若要設定將 Slack 整合到 Azure AD 中，您需要從資源庫將 Slack 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Slack**。
1. 從結果面板選取 [Slack]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>設定及測試 Slack 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Slack 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Slack 中相關使用者之間的連結關聯性。

若要設定及測試與 Slack 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Slack SSO](#configure-slack-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Slack 測試使用者](#create-slack-test-user)** - 使 Slack 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Slack]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<companyname>.slack.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，輸入 URL：`https://slack.com`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 使用實際的登入 URL 來更新此值。 請連絡 [Slack 用戶端支援小組](https://slack.com/help/contact)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. Slack 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/edit-attribute.png)

1. 除了上述屬性外，Slack 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。 如果使用者沒有電子郵件地址，請將 **emailaddress** 對應至 **user.userprincipalname**。

    | 名稱 | 來源屬性 |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | | |

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Slack]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Slack 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Slack]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-slack-sso"></a>設定 Slack SSO

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Slack 公司網站。

2. 瀏覽至 [Microsoft Azure AD]  ，然後移至 [小組設定]  。

     ![在應用程式端設定單一登入](./media/slack-tutorial/tutorial_slack_001.png)

3. 在 [小組設定]  區段中，按一下 [驗證]  索引標籤，然後按一下 [變更設定]  。

    ![在應用程式端設定單一登入](./media/slack-tutorial/tutorial_slack_002.png)

4. 在 [SAML 驗證設定]  對話方塊上，執行下列步驟：

    ![在應用程式端設定單一登入](./media/slack-tutorial/tutorial_slack_003.png)

    a.  在 [SAML 2.0 端點 (HTTP)]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    b.  在 [識別提供者簽發者]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    c.  在記事本中開啟您下載的憑證檔，將其內容複製到剪貼簿上，然後貼到 [公開憑證]  文字方塊中。

    d. 針對您的 Slack 小組適當地設定上述三個設定。 如需這些這設定的詳細資訊，請參閱這裡的 **Slack 的 SSO 設定指南**。 `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  按一下 [儲存組態]  。

### <a name="create-slack-test-user"></a>建立 Slack 測試使用者

本節的目標是要在 Slack 中建立一個名為 B.Simon 的使用者。 Slack 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 當您嘗試存取 Slack 時，如果 Slack 還沒有使用者，它就會建立新的使用者。 Slack 也支援自動使用者佈建，您可以在[這裡](slack-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

> [!NOTE]
> 如果您需要手動建立使用者，就需要連絡 [Slack 支援小組](https://slack.com/help/contact)。

> [!NOTE]
> Azure AD Connect 是一種同步處理工具，可以將內部部署的 Active Directory 身分識別同步處理至 Azure AD，之後這些已同步的使用者也可如同其他雲端使用者般地使用應用程式。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Slack] 圖格時，應該會自動登入您已設定 SSO 的 Slack。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Slack](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)