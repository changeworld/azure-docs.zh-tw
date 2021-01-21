---
title: 教學課程：Azure Active Directory 與 Overdrive 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Overdrive 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 745a82300cbbc87070a117cd8dd094236821aee7
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625393"
---
# <a name="tutorial-azure-active-directory-integration-with-overdrive"></a>教學課程：Azure Active Directory 與 Overdrive 整合

在本教學課程中，您將瞭解如何整合加速和 Azure Active Directory (Azure AD) 。 當您整合加速與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取加速的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入加速。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：
 
* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 加速單一登入 (SSO) 啟用的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Overdrive 支援由 **SP** 起始的 SSO

* Overdrive 支援 **Just In Time** 使用者佈建

## <a name="add-overdrive-from-the-gallery"></a>從資源庫新增加速

若要設定加速至 Azure AD 的整合，請執行下列動作，以從資源庫將加速新增至受控 SaaS 應用程式清單：
 
1. 使用公司、學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中輸入 **加速** 。
1. 在結果窗格中，選取 [ **加速**]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-overdrive"></a>設定及測試 Azure AD SSO 以加速

使用名為 **b. Simon** 的測試使用者，設定及測試與加速搭配的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須在 [加速] 中建立 Azure AD 使用者與相關使用者之間的連結關聯性。

若要設定及測試與加速的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定加速 SSO](#configure-overdrive-sso)** -在應用程式端設定單一登入設定。
    1. **[建立加速測試使用者](#create-overdrive-test-user)** -使 Simon 中對應的 b. 連結到使用者的 Azure AD 標記法。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [ **加速** 應用程式整合] 頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`http://<subdomain>.libraryreserve.com`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [Overdrive 客戶支援小組](https://help.overdrive.com/)以取得此值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Overdrive] 區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Overdrive 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Overdrive]。

2. 在應用程式清單中，選取 [Overdrive]。

3. 在左側功能表中，選取 [使用者和群組]  。

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="configure-overdrive-sso"></a>設定加速 SSO

若要在 **Overdrive** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML] 和所複製的適當 URL 傳送給 [Overdrive 支援小組](https://help.overdrive.com/)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-overdrive-test-user"></a>建立 Overdrive 測試使用者

本節會在 Overdrive 中建立名為 Britta Simon 的使用者。 Overdrive 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Overdrive 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

>[!NOTE]
>您可以使用任何其他的 OverDrive 使用者帳戶建立工具或 OverDrive 提供的 API，來佈建 Azure AD 使用者帳戶。
>

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至加速登入 URL，您可以在其中起始登入流程。 

* 直接前往 [加速登入 URL]，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下我的應用程式中的 [加速] 圖格時，這會重新導向至 [加速登入 URL]。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定加速之後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
