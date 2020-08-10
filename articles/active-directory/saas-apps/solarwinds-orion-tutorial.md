---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 SolarWinds Orion 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SolarWinds Orion 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 34d50638-6783-4def-93fc-be0d11fbc320
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86eae9d44607d2e997625d91924483c86766f01b
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449401"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SolarWinds Orion 整合

在本教學課程中，您會了解如何將 SolarWinds Orion 與 Azure Active Directory (Azure AD) 進行整合。 在整合 SolarWinds Orion 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 SolarWinds Orion 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SolarWinds Orion。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SolarWinds Orion 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* SolarWinds Orion 支援由 **SP 和 IDP** 起始的 SSO
* 設定 SolarWinds Orion 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-solarwinds-orion-from-the-gallery"></a>從資源庫新增 SolarWinds Orion

若要設定 SolarWinds Orion 與 Azure AD 整合，您需要從資源庫將 SolarWinds Orion 新增到受控 SaaS App 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **SolarWinds Orion**。
1. 從結果面板選取 [SolarWinds Orion]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>設定和測試 SolarWinds Orion 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 SolarWinds Orion 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SolarWinds Orion 中相關使用者之間的連結關聯性。

若要設定及測試與 SolarWinds Orion 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 SolarWinds Orion SSO](#configure-solarwinds-orion-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 SolarWinds Orion 測試使用者](#create-solarwinds-orion-test-user)** - 使 SolarWinds Orion 中 B.Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SolarWinds Orion] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [SolarWinds Orion 用戶端支援小組](mailto:technicalsupport@solarwinds.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. SolarWinds Orion 應用程式需要特定格式的 SAML 判斷提示，而您需要將自訂屬性對應新增到 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，SolarWinds Orion 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。
    
    | 名稱 |  來源屬性|
    | ----------- | --------- |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |
    | 電子郵件 |user.mail |

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，尋找 [憑證 (Base64)] 並選取 [下載]，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 SolarWinds Orion] 區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SolarWinds Orion 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [SolarWinds Orion]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-solarwinds-orion-sso"></a>設定 SolarWinds Orion SSO

1. 登入 SolarWinds Orion，並移至 [設定] -> [所有設定]。

    ![ SolarWinds Orion 組態 ](./media/solarwinds-orion-tutorial/settings.png)

1. 在 [使用者帳戶] 區段中，選取 [SAML 組態]。

    ![ SolarWinds Orion 組態 ](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. 按一下 [新增識別提供者]。

    ![ SolarWinds Orion 組態 ](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. 在 [新增識別提供者] 頁面上，執行下列步驟：

    ![ SolarWinds Orion 組態 ](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. 移至 [設定] 索引標籤。

    b. 在 [身分識別提供者名稱] 文字方塊中，輸入有效的名稱，例如 `My SSO service`。

    c. 在 [SSO 目標 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    d.  在 [簽發者 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼**值。

    e. 從 Azure 入口網站將所下載的**憑證(Base64)** 以記事本開啟，然後將內容貼至 [X.509 簽署憑證] 文字方塊。

    f. 按一下 [ **儲存**]。

### <a name="create-solarwinds-orion-test-user"></a>建立 SolarWinds Orion 測試使用者

1. 登入 SolarWinds Orion 網站，並移至 [設定] -> [所有設定]。

    ![ SolarWinds Orion 建立測試使用者 ](./media/solarwinds-orion-tutorial/settings.png)

1. 在 [使用者帳戶] 區段中，選取 [管理帳戶]。

    ![ SolarWinds Orion 建立測試使用者 ](./media/solarwinds-orion-tutorial/user-accounts.png)

1. 在 [個別帳戶] 索引標籤中，按一下 [新增新帳戶]。

    ![ SolarWinds Orion 建立測試使用者 ](./media/solarwinds-orion-tutorial/create-user.png)

1. 選取帳戶類型，您需要建立 SAML 個別使用者或群組。

    ![ SolarWinds Orion 建立測試使用者 ](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  在 [名稱識別碼] 文字方塊中，輸入必須與 Azure AD 完全相同的使用者名稱或群組名稱。

1.  按一下 [下一步] 然後提交頁面。

    ![ SolarWinds Orion 建立測試使用者 ](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 SolarWinds Orion 圖格時，應該會自動登入您設定 SSO 的 SolarWinds Orion。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [透過 Azure AD 試用 SolarWinds Orion](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 SolarWinds Orion](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

