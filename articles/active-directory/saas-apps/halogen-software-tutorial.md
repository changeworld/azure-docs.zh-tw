---
title: 教學課程：Azure Active Directory 與 Saba TalentSpace 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Saba TalentSpace 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77561322"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Saba TalentSpace 整合

在本教學課程中，您會了解如何整合 Saba TalentSpace 與 Azure Active Directory (Azure AD)。 當您將 Saba TalentSpace 與 Azure AD 整合時，您可以：

* 在 Azure AD 中控制可存取 Saba TalentSpace 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Saba TalentSpace。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Saba TalentSpace 單一登入 (SSO) 功能的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Saba TalentSpace 支援 **SP** 起始的 SSO
* 設定 Saba TalentSpace 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-saba-talentspace-from-the-gallery"></a>從資源庫新增 Saba TalentSpace

若要設定將 Saba TalentSpace 整合到 Azure AD 中，您需要從資源庫將 Saba TalentSpace 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Saba TalentSpace**。
1. 從結果面板選取 [Saba TalentSpace]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>設定及測試 Saba TalentSpace 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Saba TalentSpace 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Saba TalentSpace 中相關使用者之間的連結關聯性。

若要設定及測試與 Saba TalentSpace 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Saba TalentSpace SSO](#configure-saba-talentspace-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Saba TalentSpace 測試使用者](#create-saba-talentspace-test-user)** - 使 Saba TalentSpace 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Saba TalentSpace]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://global.hgncloud.com/[companyname]/saml/login`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://global.hgncloud.com/[companyname]/saml/metadata`

    c. 在 [回覆 URL (判斷提示取用者服務 URL)]  文字方塊中，以下列模式輸入 URL：`https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Saba TalentSpace 用戶端支援小組](https://support.saba.com/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Saba TalentSpace]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Saba TalentSpace 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Saba TalentSpace]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-saba-talentspace-sso"></a>設定 Saba TalentSpace SSO

1. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 **Saba TalentSpace**。

2. 按一下 [選項]  索引標籤。
  
    ![何謂 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. 在左瀏覽窗格中，按一下 [SAML 組態]  。
  
    ![何謂 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. 在 [SAML 組態]  頁面上，執行下列步驟：

    ![何謂 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. 對於 [唯一識別碼]  ，選取 [NameID]  。

    b. 對於 [唯一識別碼對應到]  ，選取 [Username]  。
  
    c. 若要更新您已下載的中繼資料檔，請按一下 [瀏覽]  來選取檔案，然後按一下 [上傳檔案]  。

    d. 若要測試組態，請按一下 [執行測試]  。

    > [!NOTE]
    > 您需要等候「SAML 測試已完成。  請關閉此視窗。」訊息顯示。 然後關閉已開啟的瀏覽器視窗。 完成測試之後才會啟用 [啟用 SAML]  核取方塊。

    e. 選取 [啟用 SAML]  。

    f. 按一下 **[儲存變更]** 。

### <a name="create-saba-talentspace-test-user"></a>建立 Saba TalentSpace 測試使用者

本節目標是在 Saba TalentSpace 中建立名為 Britta Simon 的使用者。

**若要在 Saba TalentSpace 中建立名為 Britta Simon 的使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Saba TalentSpace** 應用程式。

2. 按一下 [使用者中心]  索引標籤，然後按一下 [建立使用者]  。

    ![何謂 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. 在 [新增使用者]  對話頁面上，執行下列步驟：

    ![何謂 Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. 在 [名字]  文字方塊中，輸入使用者的名字，例如 **B**。

    b. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [使用者名稱]  文字方塊中輸入 **B.Simon**，亦即 Azure 入口網站中的使用者名稱。

    d. 在 [密碼]  文字方塊中輸入 B.Simon 的密碼。

    e. 按一下 [檔案]  。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Saba TalentSpace] 圖格時，應該會自動登入您已設定 SSO 的 Saba TalentSpace。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Saba TalentSpace](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)