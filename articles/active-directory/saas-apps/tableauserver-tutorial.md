---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Tableau Server 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Tableau Server 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 323df3ded868aeda25f89300f19bf03a61cbc8bc
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516438"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Tableau Server 整合

在此教學課程中，您將了解如何整合 Tableau Server 與 Azure Active Directory (Azure AD)。 在整合 Tableau Server 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Tableau Server 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Tableau Server。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Tableau Server 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Tableau Server 支援由 **SP** 起始的 SSO
* 設定 Tableau Server 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>從資源庫新增 Tableau Server

若要設定 Tableau Server 與 Azure AD 整合，您需要從資源庫將 Tableau Server 加入到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 [Tableau Server]。
1. 從結果面板選取 [Tableau Server]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>設定及測試 Tableau Server 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Tableau Server 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Tableau Server 中相關使用者之間的連結關聯性。

若要設定及測試與 Tableau Server 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Tableau Server SSO](#configure-tableau-server-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Tableau Server 測試使用者](#create-tableau-server-test-user)** - 使 Tableau Server 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Tableau Server] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，輸入下列欄位的值：

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://azure.<domain name>.link`

    b. 在 [識別碼] 方塊中，使用下列模式輸入 URL：`https://azure.<domain name>.link`

    c. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > 上述值並非真正的值。 請使用 [Tableau Server 設定] 頁面中實際的 URL 和識別碼來更新這些值，如本教學課程稍後所說明。

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，尋找 [同盟中繼資料 XML]，然後選取 [下載]，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Tableau Server] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Tableau Server 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Tableau Server] 。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-tableau-server-sso"></a>設定 Tableau Server SSO

1. 若要取得為應用程式設定的 SSO，您必須以系統管理員身分登入 Tableau Server 租用戶。

2. 在 [組態] 索引標籤上選取 [使用者身分識別和存取權]，然後選取 [驗證方法] 索引標籤。

    ![螢幕擷取畫面：顯示已從 [使用者身分識別和存取權] 選取 [驗證]。](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. 在 [組態] 頁面上，執行下列步驟：

    ![此螢幕擷取畫面顯示 [組態] 頁面，您可以在其中輸入所述的值。](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. 選取 [SAML] 作為 [驗證方法]。

    b. 選取 [為伺服器啟用 SAML 驗證] 的核取方塊。

    c. Tableau Server 傳回 URL — Tableau Server 使用者將存取的 URL，例如 `http://tableau_server`。 不建議使用 `http://localhost`。 不支援使用包含結尾斜線的 URL (例如，`http://tableau_server/`)。 複製 [Tableau Server 傳回 URL]，並將其貼到 Azure 入口網站上 [基本 SAML 組態] 區段的 [登入 URL] 文字方塊中。

    d. SAML 實體識別碼—實體識別碼可唯一識別安裝至 IdP 的 Tableau Server。 您可以依意願再次在這裡輸入 Tableau Server URL，但是它不一定是您的 Tableau Server URL。 複製 [SAML 實體識別碼] 值，並將其貼到 Azure 入口網站上 [基本 SAML 組態] 區段的 [識別碼] 文字方塊中。

    e. 按一下 [下載 XML 中繼資料檔案]，並在文字編輯器應用程式中將其開啟。 利用 Http Post 與索引 0 找出判斷提示取用者服務 URL，並複製 URL。 現在將其貼到 Azure 入口網站上 [基本 SAML 組態] 區段的 [回覆 URL] 文字方塊中。

    f. 尋找從 Azure 入口網站下載的同盟中繼資料檔案，然後在 [SAML Idp 中繼資料檔案] 中將其上傳。

    g. 輸入 IdP 用來保留使用者名稱、顯示名稱和電子郵件地址的屬性名稱。

    h. 按一下 [儲存] 

    > [!NOTE]
    > 客戶必須上傳具有 .crt 副檔名的 PEM 編碼 x509 憑證檔案，以及具有 .key 副檔名的 RSA 或 DSA 私密金鑰檔案，作為憑證金鑰檔案。 如需憑證檔案和憑證金鑰檔案的詳細資訊，請參閱[這份](https://help.tableau.com/current/server/en-us/saml_requ.htm)文件。 如果您需要在 Tableau Server 上設定 SAML 的協助，請參閱[設定全伺服器的 SAML](https://help.tableau.com/current/server/en-us/config_saml.htm)。

### <a name="create-tableau-server-test-user"></a>建立 Tableau Server 測試使用者

本節目標是在 Tableau Server 中建立名為 B.Simon 的使用者。 您必須在 Tableau Server 中佈建所有使用者。

使用者的使用者名稱應符合您在 Azure AD 自訂屬性 **username** 中設定的值。 透過正確的對應，應該可以有效整合，設定 Azure AD 單一登入。

> [!NOTE]
> 如果您需要以手動方式建立使用者，您必須連絡貴組織的 Tableau Server 系統管理員。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Tableau Server 圖格時，應該會自動登入您設定 SSO 的 Tableau Server。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 Tableau Server](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)