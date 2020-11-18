---
title: 教學課程：Azure Active Directory 與 KnowBe4 Security Awareness Training 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 KnowBe4 Security Awareness Training 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: jeedes
ms.openlocfilehash: 7bc2ebf394eb5b20e5f0a7d3722f8bcc2a9127a6
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928020"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>教學課程：Azure Active Directory 與 KnowBe4 Security Awareness Training 整合

在本教學課程中，您會了解如何將 KnowBe4 Security Awareness Training 與 Azure Active Directory (Azure AD) 進行整合。
將 KnowBe4 Security Awareness Training 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中管控可存取 KnowBe4 Security Awareness Training 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 KnowBe4 Security Awareness Training (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 KnowBe4 Security Awareness Training 的整合作業，需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 KnowBe4 Security Awareness Training 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* KnowBe4 Security Awareness Training 支援由 **SP** 起始的 SSO

* KnowBe4 Security Awareness Training 支援 **Just In Time** 使用者佈建

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-knowbe4-from-the-gallery"></a>從資源庫加入 KnowBe4

若要設定 KnowBe4 與 Azure AD 的整合作業，您需要從資源庫將 KnowBe4 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **KnowBe4**。
1. 從結果面板中選取 [KnowBe4]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 KnowBe4 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 KnowBe4 中相關使用者之間的連結關聯性。

若要設定及測試與 KnowBe4 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 以 Britta Simon 測試 Azure AD SSO。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓使用者 Britta Simon 能夠使用 Azure AD SSO。
2. **[設定 KnowBe4 Security Awareness Training SSO](#configure-knowbe4-security-awareness-training-sso)** - 在應用程式端設定 SSO 設定。
    * **[建立 KnowBe4 Security Awareness Training 測試使用者](#create-knowbe4-security-awareness-training-test-user)** - 使 KnowBe4 Security Awareness Training 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [KnowBe4] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > 登入 URL 值不是真正的值。 使用實際的登入 URL 來更新此值。 請連絡 [KnowBe4 Security Awareness Training 用戶端支援小組](mailto:support@KnowBe4.com)以取得此值。 您也可以參考 Azure 入口網站中的 [基本 SAML 組態] 區段所顯示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (原始)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificateraw.png)

6. 在 [設定 KnowBe4 Security Awareness Training] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 KnowBe4 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [KnowBe4] 。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-knowbe4-security-awareness-training-sso"></a>設定 KnowBe4 Security Awareness Training SSO

若要設定 **KnowBe4 Security Awareness Training** 端的單一登入，您必須將從 Azure 入口網站下載的 [憑證 (原始)] 和複製的適當 URL 傳送給 [KnowBe4 Security Awareness Training 支援小組](mailto:support@KnowBe4.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-knowbe4-security-awareness-training-test-user"></a>建立 KnowBe4 Security Awareness Training 測試使用者

本節會在 KnowBe4 中建立名為 Britta Simon 的使用者。 KnowBe4 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 KnowBe4 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

1. 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 KnowBe4 登入 URL。 

2. 直接移至 KnowBe4 登入 URL，然後從該處起始登入流程。

3. 您可以使用 Microsoft 存取面板。 當您按一下存取面板中的 KnowBe4 圖格時，將會重新導向至 KnowBe4 登入 URL。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 KnowBe4 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

