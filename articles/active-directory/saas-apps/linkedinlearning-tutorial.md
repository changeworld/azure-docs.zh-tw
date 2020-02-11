---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 LinkedIn Learning 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 LinkedIn Learning 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15509866980cdf85f54fc03cb77eed36f83c982f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983362"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 LinkedIn Learning 整合

在本教學課程中，您將了解如何整合 LinkedIn Learning 與 Azure Active Directory (Azure AD)。 在整合 LinkedIn Learning 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 LinkedIn Learning 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 LinkedIn Learning。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 LinkedIn Learning 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* LinkedIn Learning 支援 **SP 和 IDP** 起始的 SSO
* LinkedIn Learning 支援 **Just In Time** 使用者佈建
* 設定 LinkedIn Learning 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-linkedin-learning-from-the-gallery"></a>從資源庫新增 LinkedIn Learning

如要設定將 LinkedIn Learning 整合到 Azure AD 中，您需要從資源庫中將 LinkedIn Learning 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **LinkedIn Learning**。
1. 從結果面板選取 [LinkedIn Learning]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>設定及測試 LinkedIn Learning 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 LinkedIn Learning 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 LinkedIn Learning 中相關使用者之間的連結關聯性。

若要設定及測試與 LinkedIn Learning 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 LinkedIn Learning SSO](#configure-linkedin-learning-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 LinkedIn Learning 測試使用者](#create-linkedin-learning-test-user)** - 使 LinkedIn Learning 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [LinkedIn Learning]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

     a. 在 [識別碼]  文字方塊中，輸入從 LinkedIn 入口網站複製的**實體識別碼**。 

    b. 在 [回覆 URL]  文字方塊中，輸入從 LinkedIn 入口網站複製的**判斷提示取用者服務 (ACS) URL**。

    c. 如果您想要以 **SP 起始**模式設定應用程式，則可按一下 [基本 SAML 設定]  區段中的 [設定額外的 URL]  選項，以指定您的登入 URL。 若要建立您的登入 URL，請複製 [判斷提示取用者服務 (ACS) URL]  並將 /saml/ 取代為 /login/。 完成之後，登入 URL 應該會有下列模式：

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![LinkedIn Learning 網域及 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > 這些都不是真正的值。 您將會使用實際的識別碼和回覆 URL 來更新這些值，稍後會在教學課程的**設定 LinkedIn Learning SSO** 一節中加以說明。

1. LinkedIn Learning 應用程式需要特定格式的 SAML 判斷提示，要求您將自訂屬性對應新增到您的 SAML 權杖屬性組態。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 LinkedIn Learning 應用程式要求 **nameidentifier** 需與 **user.mail** 相對應，因此您必須按一下 [編輯]  圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 LinkedIn Learning]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 LinkedIn Learning 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [LinkedIn Learning]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-linkedin-learning-sso"></a>設定 LinkedIn Learning SSO

1. 在不同的網頁瀏覽器視窗中，以管理員身分登入您的 LinkedIn Learning 租用戶。

2. 在 [帳戶中心]  中，按一下 [設定]  底下的 [全域設定]  。 此外，從下拉式清單選取 [學習 - 預設]  。

    ![設定單一登入](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. 按一下 [或按一下這裡以從表單載入和複製個別欄位]  ，並複製 [實體識別碼]  和 [判斷提示取用者服務 (ACS) URL]  ，然後將它貼入 Azure 入口網站中的 [基本 SAML 設定]  區段。

    ![設定單一登入](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. 移至 [LinkedIn 系統管理員設定]  區段。 按一下 [上傳 XML 檔案]  選項，將您從 Azure 入口網站下載的 XML 檔案上傳。

    ![設定單一登入](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. 按一下 [開啟]  以啟用 SSO。 SSO 狀態會從 [未連線]  變更為 [已連線] 

    ![設定單一登入](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>建立 LinkedIn Learning 測試使用者

LinkedIn Learning 應用程式支援及時使用者佈建，且在驗證後會在應用程式中自動建立使用者。 在 LinkedIn Learning 入口網站的系統管理設定頁面上，將 [自動指派授權]  切換成啟用及時佈建，這個動作也會將授權指派給使用者。

   ![建立 Azure AD 測試使用者](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [LinkedIn Learning] 圖格時，應該會自動登入您設定 SSO 的 LinkedIn Learning。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 LinkedIn Learning](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)