---
title: 教學課程：Azure Active Directory 與 MobileIron 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 MobileIron 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 5561a4cdeef725eba7e48d7767aa0ee5d3c6d9cf
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625443"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>教學課程：Azure Active Directory 與 MobileIron 整合

 在本教學課程中，您將瞭解如何整合 MobileIron 與 Azure Active Directory (Azure AD) 。 當您整合 MobileIron 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 MobileIron 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 MobileIron。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 MobileIron 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 MobileIron 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* MobileIron 支援由 **SP 和 IDP** 初始化的 SSO

## <a name="add-mobileiron-from-the-gallery"></a>從資源庫新增 MobileIron

若要設定 MobileIron 與 Azure AD 整合，您需要從資源庫將 MobileIron 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格上，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中，輸入 **MobileIron** 。
1. 從結果中選取 [ **MobileIron** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>設定及測試 Azure AD SSO for MobileIron

使用名為 **b. Simon** 的測試使用者，設定及測試與 MobileIron 搭配使用的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須在 Azure AD 使用者與 MobileIron 中的相關使用者之間建立連結關聯性。

若要設定及測試與 MobileIron 搭配 Azure AD 的 SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
     1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 MOBILEIRON SSO](#configure-mobileiron-sso)** -在應用程式端設定單一 Sign-On 設定。
    1. **[建立 MobileIron 測試使用者](#create-mobileiron-test-user)** - 在 MobileIron 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表該使用者的項目連結。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD SSO。
 
1. 在 Azure 入口網站的 [ **MobileIron** ] 應用程式整合頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的鉛筆圖示，以編輯設定。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 若您想要以 **IDP** 起始模式設定應用程式，請在 [基本 SAML 組態] 區段執行下列步驟：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://www.MobileIron.com/<key>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

     在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 您將會從 MobileIron 的系統管理入口網站取得 key 和 host 的值 (本教學課程稍後說明)。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下密碼。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 MobileIron 的存取權授與 b. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [MobileIron]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊的使用者清單中，選取 [B.Simon]  。 然後，選擇畫面底部的 [選取]  按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-mobileiron-sso"></a>設定 MobileIron SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 MobileIron 公司網站。

2. 移至 [管理員] > [身分識別]，並且在 [雲端 IDP 設定的相關資訊] 欄位中選取 [AAD] 選項。

    ![此螢幕擷取畫面顯示 MobileIron 網站上已選取 [身分識別] 的 [管理員] 索引標籤。](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. 複製 [金鑰]  和 [主機]  的值，並貼到 Azure 入口網站的 [基本 SAML 組態]  區段中來補齊 URL。

    ![顯示 [設定 SAML] 選項與 [金鑰] 和 [主機] 值的螢幕擷取畫面。](./media/MobileIron-tutorial/key.png)

4. 在 [從 AAD 匯出中繼資料檔案並匯入至 MobileIron 雲端]  欄位中，按一下 [選擇檔案]  ，上傳從 Azure 入口網站下載的中繼資料。 上傳之後，按一下 [完成]  。

    ![設定單一登入管理員中繼資料按鈕](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


##  <a name="create-mobileiron-test-user"></a>建立 MobileIron 測試使用者

若要讓 Azure AD 使用者可以登入 MobileIron，則必須將他們佈建到 MobileIron。  
MobileIron 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 MobileIron 公司網站。

1. 移至 [使用者]  ，然後按一下 [新增]   > [單一使用者]  。

    ![設定單一登入使用者按鈕](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. 在 [使用者]  對話方塊頁面上，執行下列步驟：

    ![設定單一登入使用者新增按鈕](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. 在 [電子郵件地址]  文字方塊中，輸入使用者的電子郵件，例如 brittasimon@contoso.com。

    b. 在 [名字]  文字方塊中，輸入使用者的名字，例如 Britta。

    c. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 Simon。

    d. 按一下 [完成]  。

## <a name="test-sso"></a>測試 SSO
在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

## <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 MobileIron 登入 URL，您可以在其中起始登入流程。  

* 直接前往 MobileIron 登入 URL，並從該處起始登入流程。

##  <a name="idp-initiated"></a>IDP 起始：

* 按一下 [在 Azure 入口網站 **測試此應用程式** ]，您應該會自動登入您已設定 SSO 的 MobileIron。

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您在我的應用程式中按一下 [MobileIron] 圖格時，如果在 SP 模式中設定，系統會將您重新導向至應用程式登入頁面以起始登入流程，如果是在 IDP 模式中設定，您應該會自動登入您已設定 SSO 的 MobileIron。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 MobileIron 後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。
