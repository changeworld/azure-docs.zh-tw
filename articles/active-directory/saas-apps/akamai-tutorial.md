---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Akamai 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Akamai 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979134"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Akamai 整合

在本教學課程中，您會了解如何整合 Akamai 與 Azure Active Directory (Azure AD)。 在整合 Akamai 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Akamai 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Akamai。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Akamai 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

- Slack 支援由 IDP 起始的 SSO

## <a name="adding-akamai-from-the-gallery"></a>從資源庫新增 Akamai

若要設定將 Akamai 整合到 Azure AD 中，您需要從資源庫將 Akamai 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Akamai**。
1. 從結果面板選取 [Akamai]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>設定及測試 Akamai 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Akamai 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Akamai 中相關使用者之間的連結關聯性。

若要設定及測試與 Akamai 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Akamai SSO](#configure-akamai-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Akamai 測試使用者](#create-akamai-test-user)** - 使 Akamai 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Akamai]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Akamai 用戶端支援小組](https://www.akamai.com/us/en/contact-us/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Akamai]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Akamai 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Akamai]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-akamai-sso"></a>設定 Akamai SSO

### <a name="setting-up-idp"></a>設定 IDP

1. 登入 **Akamai 企業應用程式存取**主控台。
1. 在 [Akamai EAA 主控台]  上，選取 [身分識別]   > [識別提供者]  。

    ![設定 Akamai](./media/header-akamai-tutorial/configure01.png)

1. 按一下 [新增識別提供者]  。

    ![設定 Akamai](./media/header-akamai-tutorial/configure02.png)

    a. 指定 [唯一名稱]  。
    
    b. 選擇 [第三方 SAML]  ，然後按一下 [建立識別提供者並設定]  。

### <a name="general-settings"></a>一般設定

1. **身分識別攔截** - 指定名稱 (屬於 SP 基底 URL - 將用於 Azure AD 設定)

    > [!NOTE]
    > 您可以選擇擁有您自己的自訂網域 (需要有 DNS 項目和憑證)。 在此範例中，我們將使用 Akamai 網域。

1. **Akamai 雲端區域** - 選取適當的雲端區域。
1. **憑證驗證** - 查看 Akamai 文件 (選擇性)

    ![設定 Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>驗證設定

1. URL - 指定與身分識別攔截相同的 URL (這是在驗證之後，使用者的重新導向目的地)。
2. 登出 URL：更新登出 URL。
3. 簽署 SAML 要求：預設為未核取。
4. 針對 IDP 中繼資料檔案，在 Azure AD 主控台中新增應用程式。

    ![設定 Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>以標頭為基礎的驗證

以 Akamai 標頭為基礎的驗證

1. 從「新增應用程式」精靈選擇 [自訂 HTTP]  。

    ![設定 Akamai](./media/header-akamai-tutorial/configure05.png)

    ![設定 Akamai](./media/header-akamai-tutorial/configure06.png)

    ![設定 Akamai](./media/header-akamai-tutorial/configure07.png)

    ![設定 Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>驗證

![設定 Akamai](./media/header-akamai-tutorial/configure09.png)

![設定 Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>服務

1. 按一下 [儲存] 並移至 [驗證]。

![設定 Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>進階設定

1. 在 [客戶 HTTP 標頭]  底下，指定 [CustomerHeader]  和 [SAML 屬性]  。

    ![設定 Akamai](./media/header-akamai-tutorial/configure12.png)

1. 按一下 [儲存]  並移至 [部署] 按鈕。

    ![設定 Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>部署應用程式

1. 按一下 [部署應用程式]  按鈕。

    ![設定 Akamai](./media/header-akamai-tutorial/configure14.png)

1. 確認應用程式已部署成功。

    ![設定 Akamai](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Kerberos 驗證

#### <a name="remote-desktop"></a>遠端桌面

1. 從「新增應用程式」精靈選擇 [RDP]  。

    ![設定 Akamai](./media/header-akamai-tutorial/configure16.png)

    ![設定 Akamai](./media/header-akamai-tutorial/configure17.png)

    ![設定 Akamai](./media/header-akamai-tutorial/configure18.png)

1. 指定會提供此項目的連接器。

    ![設定 Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>驗證

按一下 [儲存]  並移至 [服務]。

![設定 Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>服務

按一下 [儲存]  並移至 [進階設定]。

![設定 Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>進階設定

按一下 [儲存]  並移至 [部署]。

![設定 Akamai](./media/header-akamai-tutorial/configure22.png)

![設定 Akamai](./media/header-akamai-tutorial/configure23.png)

![設定 Akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>部署

#### <a name="ssh"></a>SSH

1. 移至 [新增應用程式]，選擇 [SSH]  。

    ![設定 Akamai](./media/header-akamai-tutorial/configure25.png)

    ![設定 Akamai](./media/header-akamai-tutorial/configure26.png)

1. 設定應用程式識別碼。

    ![設定 Akamai](./media/header-akamai-tutorial/configure27.png)

    a. 指定名稱/描述。

    b. 指定 SSH 的應用程式伺服器 IP/FQDN 和連接埠。

    c. 指定 SSH 使用者名稱/複雜密碼 *核取 Akamai EAA。

    d. 指定外部主機名稱。

    e. 指定連接器的位置，然後選擇連接器。

#### <a name="authentication"></a>驗證

按一下 [儲存]  並移至 [服務]。

![設定 Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>服務

按一下 [儲存]  並移至 [進階設定]。

![設定 Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>進階設定

按一下 [儲存] 並移至 [部署]

![設定 Akamai](./media/header-akamai-tutorial/configure30.png)

![設定 Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>部署

按一下 [部署應用程式]  。

![設定 Akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Kerberos 應用程式

#### <a name="adding-directory"></a>新增目錄

![設定 Akamai](./media/header-akamai-tutorial/configure33.png)

![設定 Akamai](./media/header-akamai-tutorial/configure34.png)

![設定 Akamai](./media/header-akamai-tutorial/configure35.png)

![設定 Akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>建立 Akamai 測試使用者

在本節中，您要在 Akamai 中建立名為 B.Simon 的使用者。 請與  [Akamai 用戶端支援小組](https://www.akamai.com/us/en/contact-us/)合作，在 Akamai 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Akamai] 圖格時，應該會自動登入您已設定 SSO 的 Akamai。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Akamai](https://aad.portal.azure.com/)
