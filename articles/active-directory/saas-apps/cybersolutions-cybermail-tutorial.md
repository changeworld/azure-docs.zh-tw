---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 CyberSolutions CYBERMAILΣ 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 CyberSolutions CYBERMAILΣ 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 41c0bdf3eaac1a78d4cdef09ba418155a5a0c919
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455025"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cybersolutions-cybermail"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 CyberSolutions CYBERMAILΣ 整合

在本教學課程中，您將了解如何整合 CyberSolutions CYBERMAILΣ 與 Azure Active Directory (Azure AD)。 在整合 CyberSolutions CYBERMAILΣ 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 CyberSolutions CYBERMAILΣ 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 CyberSolutions CYBERMAILΣ。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 CyberSolutions CYBERMAILΣ 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* CyberSolutions CYBERMAILΣ 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-cybersolutions-cybermail-from-the-gallery"></a>從資源庫新增 CyberSolutions CYBERMAILΣ

若要進行將 CyberSolutions CYBERMAILΣ 整合到 Azure AD 中的設定，您必須從資源庫將 CyberSolutions CYBERMAILΣ 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **CyberSolutions CYBERMAILΣ** 。
1. 從結果面板中選取 [CyberSolutions CYBERMAILΣ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-cybersolutions-cybermail"></a>設定和測試 CyberSolutions CYBERMAILΣ 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 CyberSolutions CYBERMAILΣ 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 CyberSolutions CYBERMAILΣ 中相關使用者之間的連結關聯性。

若要設定及測試與 CyberSolutions CYBERMAILΣ 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 CyberSolutions CYBERMAIL SSO](#configure-cybersolutions-cybermail-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 CyberSolutions CYBERMAIL 測試使用者](#create-cybersolutions-cybermail-test-user)** - 使 CyberSolutions CYBERMAILΣ 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [CyberSolutions CYBERMAILΣ] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<SUBDOMAIN>.cybercloud.jp/saml/module.php/saml/sp/metadata.php/m2k_generic_sp`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/saml_login/saml2-acs/m2k_generic_sp`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<SUBDOMAIN>.cybercloud.jp`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [CyberSolutions CYBERMAILΣ 用戶端支援小組](mailto:tech@cybersolutions.co.jp)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 CyberSolutions CYBERMAILΣ] 區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 CyberSolutions CYBERMAILΣ 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [CyberSolutions CYBERMAILΣ]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-cybersolutions-cybermail-sso"></a>設定 CyberSolutions CYBERMAIL SSO

若要在 **CyberSolutions CYBERMAILΣ** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML] 和複製的適當 URL 傳送給 [CyberSolutions CYBERMAILΣ 支援小組](mailto:tech@cybersolutions.co.jp)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-cybersolutions-cybermail-test-user"></a>建立 CyberSolutions CYBERMAIL 測試使用者

在本節中，您會在 CyberSolutions CYBERMAILΣ 中建立名為 Britta Simon 的使用者。 請與 [CyberSolutions CYBERMAILΣ 支援小組](mailto:tech@cybersolutions.co.jp)合作，在 CyberSolutions CYBERMAILΣ 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 CyberSolutions CYBERMAILΣ 登入 URL。  

* 直接移至 CyberSolutions CYBERMAILΣ 登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 CyberSolutions CYBERMAILΣ 

您也可以使用 Microsoft 存取面板，以任何模式測試應用程式。 當您按一下存取面板中的 CyberSolutions CYBERMAILΣ 圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果是在 IDP 模式中設定，則應該會自動登入已設定 SSO 的 CyberSolutions CYBERMAILΣ。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

設定 CyberSolutions CYBERMAILΣ 之後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。