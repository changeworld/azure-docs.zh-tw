---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Roadmunk 整合
description: 了解如何設定 Azure Active Directory 與 Roadmunk 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 26424106098a8385faff5ab6d3de33d98576ef4e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381298"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Roadmunk 整合

在本教學課程中，您將了解如何整合 Roadmunk 與 Azure Active Directory (Azure AD)。 在整合 Roadmunk 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Roadmunk 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Roadmunk。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用單一登入 (SSO) 的 Roadmunk 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

Roadmunk 支援由「服務提供者」(SP) 和「識別提供者」(IDP) 啟動的 SSO。

## <a name="add-roadmunk-from-the-gallery"></a>從資源庫新增 Roadmunk

若要將 Roadmunk 整合到 Azure AD，請從資源庫將 Roadmunk 新增至受控 SaaS 應用程式清單：

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格中，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增新的應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Roadmunk**。
1. 從結果中選取 [Roadmunk]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>設定及測試 Roadmunk 的 Azure AD SSO

使用名為 *B.Simon* 的測試使用者，設定及測試與 Roadmunk 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Roadmunk 中相關使用者之間的連結關聯性。

以下概述如何使用 Roadmunk 來設定及測試 Azure AD SSO：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者可以使用這項功能。
    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user) 使用 B.Simon 測試 Azure AD SSO。
    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD SSO。
1. [設定 Roadmunk SSO](#configure-roadmunk-sso) 以在應用程式端設定 SSO 設定。
    1. [建立 Roadmunk 測試使用者](#create-roadmunk-test-user)，讓您可以將 Roadmunk 中 B.Simon 的對應項連接到代表使用者的 Azure AD。
1. [測試 SSO](#test-sso)，驗證組態是否正常運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 Azure 入口網站的 [Roadmunk] 應用程式整合頁面上，尋找 [管理] 區段，然後選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 組態]  的畫筆圖示，以編輯設定。

   ![螢幕擷取畫面，顯示基本 SAML 組態的編輯圖示。](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段中，若您想要以 IDP 起始模式設定 SP 中繼資料檔案，請遵循下列步驟：

    a. 選取 [上傳中繼資料檔案]。

    ![顯示上傳中繼資料檔案連結的螢幕擷取畫面。](common/upload-metadata.png)

    b. 選取資料夾圖示，選擇您在「設定 Roadmunk SSO」程序的步驟 4 中下載的中繼資料檔案。 然後選取 [上傳]。

    ![顯示如何選擇中繼資料的螢幕擷取畫面。](common/browse-upload-metadata.png)

    上傳中繼資料檔案後，系統就會在 [基本 SAML 組態] 區段中自動填入 [識別碼] 和 [回覆 URL] 等值。

    ![此螢幕擷取畫面顯示 [基本 SAML 組態] 區段。 [識別碼] 欄位和 [回復 URL] 欄位會反白顯示。](common/idp-intiated.png)

    > [!Note]
    > 如果未自動填入 [識別碼] 和 [回覆 URL] 值，請手動輸入。

1. 如果您想要以 SP 起始模式設定應用程式，請選取 [設定其他 URL]。 在 [登入 URL] 欄位中，輸入 `https://login.roadmunk.com`

    ![螢幕擷取畫面，顯示設定 SP 起始模式登入 URL 的位置。](common/metadata-upload-additional-signon.png)

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，尋找 **同盟中繼資料 XML**。 接著選取 [下載] 以下載憑證，並將其儲存在您的電腦上。

    ![顯示 SAML 簽署憑證下載連結的螢幕擷取畫面。](common/metadataxml.png)

1. 在 [設定 Roadmunk] 區段中，複製您所需的一或多個 URL。

    ![螢幕擷取畫面，顯示要將設定 URL 複製到何處。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立測試使用者。 您會將使用者命名為 B.Simon。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在視窗頂端選取 [新增使用者]。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如，輸入 `B.Simon@contoso.com`。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Roadmunk 的存取權授與 B.Simon，讓其能夠使用 Azure SSO。

1. 在 Azure 入口網站中，選取 [企業應用程式] > [所有應用程式]。
1. 在應用程式清單中，選取 [Roadmunk]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。
1. 選取 [新增使用者]  。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon]。 接著在對話方塊底部，選擇 [選取]。
1. 如果您需要將角色指派給使用者，可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，則系統會選取 **預設存取** 角色。
1. 在 [新增指派] 對話方塊中，選取 [指派]。

## <a name="configure-roadmunk-sso"></a>設定 Roadmunk SSO

1. 以系統管理員身分登入 Roadmunk 網站。

1. 選取頁面底部的使用者圖示，然後選取 [帳戶設定]。

    ![螢幕擷取畫面，顯示要選取使用者帳戶選項的位置。](./media/roadmunk-tutorial/account.png)

1. 移至 **公司** > **驗證設定**。

1. 在 [驗證設定] 頁面上，遵循這些步驟：

    ![螢幕擷取畫面，顯示 [驗證設定] 頁面。](./media/roadmunk-tutorial/saml-sso.png)

    a. 開啟 **SAML 單一登入 (SSO)** 。

    b. 在 **步驟 1** 區段中，上傳中繼資料 XML 檔案或提供中繼資料的 URL。

    c. 在 **步驟 2** 區段中，下載 Roadmunk 中繼資料檔案，然後儲存在您的電腦上。

    d. 如果要使用 SSO 登入，請在 **步驟 3** 區段中，選取 [僅強制執行 SAML 登入]。

    e. 選取 [儲存]  。


### <a name="create-roadmunk-test-user"></a>建立 Roadmunk 測試使用者

1. 以系統管理員身分登入 Roadmunk 網站。

1. 選取頁面底部的使用者圖示，然後選取 [帳戶設定]。

    ![螢幕擷取畫面，顯示如何開啟測試使用者的帳戶設定。](./media/roadmunk-tutorial/account.png)

1. 開啟 **使用者** 索引標籤，然後選取 [邀請使用者]。

    ![顯示使用者索引標籤的螢幕擷取畫面。[邀請使用者] 按鈕會反白顯示。 在開啟的視窗中，[電子郵件] 和 [角色] 欄位會反白顯示。](./media/roadmunk-tutorial/create-user.png)

1. 在顯示的表單中填入必要資訊，然後選取 [邀請]。


## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD SSO 組態。

當您在 [我的應用程式] 入口網站中選取 **Roadmunk** 圖格時，系統應該會將您自動登入設定 SSO 的 Roadmunk 帳戶。 如需詳細資訊，請參閱[登入我的應用程式入口網站並啟動應用程式](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)。

## <a name="next-steps"></a>後續步驟

設定 Roadmunk 後，您可以強制執行工作階段控制項。 工作階段控制項可即時保護組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項會從條件式存取延伸。 

了解如何[使用 Microsoft Cloud App Security 強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。


