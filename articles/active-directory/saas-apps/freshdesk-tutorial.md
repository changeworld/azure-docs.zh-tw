---
title: 教學課程：Azure Active Directory 與 FreshDesk 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 FreshDesk 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: d14f69865c254becc8295034b6311b1641dfd498
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623717"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>教學課程：Azure Active Directory 與 FreshDesk 整合

在本教學課程中，您將瞭解如何整合 FreshDesk 與 Azure Active Directory (Azure AD) 。 當您整合 FreshDesk 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 FreshDesk 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 FreshDesk。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：
 
* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* FreshDesk 單一登入 (SSO) 啟用的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* FreshDesk 支援 **SP** 起始的 SSO

## <a name="add-freshdesk-from-the-gallery"></a>從資源庫新增 FreshDesk

若要設定 FreshDesk 與 Azure AD 整合，您需要從資源庫將 FreshDesk 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **FreshDesk**。
1. 從結果面板選取 [FreshDesk]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>設定和測試 FreshDesk 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 FreshDesk 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 FreshDesk 中相關使用者之間的連結關聯性。

若要設定及測試與 FreshDesk 搭配 Azure AD 的 SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[設定 FRESHDESK SSO](#configure-freshdesk-sso)** -在應用程式端設定單一 Sign-On 設定。
    1. **[建立 FreshDesk 測試使用者](#create-freshdesk-test-user)** - 使 FreshDesk 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

1. 在 Azure 入口網站的 [ **FreshDesk** ] 應用程式整合頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [ **以 SAML 設定單一登入** ] 頁面上，按一下 [ **基本 saml** 設定] 的鉛筆圖示以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<tenant-name>.freshdesk.com` 或 Freshdesk 所建議的任何其他值。

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，以下列模式輸入 URL：`https://<tenant-name>.freshdesk.com` 或 Freshdesk 所建議的任何其他值。
     
    c. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」、「識別碼」和「回覆 URL」來更新這些值。 請連絡 [FreshDesk 用戶端支援小組](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. FreshDesk 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 SAML 權杖屬性設定。 下列螢幕擷取畫面顯示預設屬性的清單，而 **唯一的使用者識別碼** 與 **user.userprincipalname** 對應，但 FreshDesk 預期此宣告必須與 **user.mail** 對應，因此您必須按一下 [編輯] 圖示並變更屬性對應，以編輯屬性對應。

    ![image](common/edit-attribute.png)

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 FreshDesk] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 FreshDesk 的存取權授與 b. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [FreshDesk] 。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-freshdesk-sso"></a>設定 FreshDesk SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Freshdesk 公司網站。

2. 選取 **安全性圖示**，並且在 [安全性] 區段中執行下列步驟：

    ![單一登入](./media/freshdesk-tutorial/configure-1.png "單一登入")
  
    a. 針對 [單一登入]，選取 [啟用]。

    b. 在 [登入方法]中，選取 [SAML SSO]。

    c. 在 [IDP 提供的實體識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 [實體識別碼] 值。

    d. 在 [SAML SSO URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    e. 在 [簽署選項] 中，從下拉式清單中選取 [僅已簽署的判斷提示]。

    f. 在 [登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 **登出 URL** 值。

    g. 在 [安全性憑證] 文字方塊中，貼上您先前取得的 **憑證 (Base64)** 值。
  
    h. 按一下 [檔案] 。

## <a name="create-freshdesk-test-user"></a>建立 FreshDesk 測試使用者

若要讓 Azure AD 使用者可以登入 FreshDesk，則必須將他們佈建到 FreshDesk。  
FreshDesk 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **Freshdesk** 租用戶。

1. 在左側功能表中，按一下 [管理員]，然後在 [一般設定] 索引標籤中按一下 [代理程式]。
  
    ![代理程式](./media/freshdesk-tutorial/create-user-1.png "代理程式")

1. 按一下 [新增代理程式] 。

    ![新代理程式](./media/freshdesk-tutorial/create-user-2.png "新代理程式")

1. 在代理程式資訊對話方塊中，輸入必要的欄位，然後按一下 [建立代理程式]。

    ![代理程式資訊](./media/freshdesk-tutorial/create-user-3.png "代理程式資訊")

    >[!NOTE]
    >Azure AD 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。
    >
    >[!NOTE]
    >您可以使用任何其他的 Freshdesk 使用者帳戶建立工具或 Freshdesk 提供的 API，將 Azure AD 使用者帳戶佈建至 Freshdesk。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 FreshDesk 登入 URL，您可以在其中起始登入流程。 

* 直接前往 FreshDesk 登入 URL，並從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [FreshDesk] 圖格時，應該會自動登入您已設定 SSO 的 FreshDesk。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 FreshDesk 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。