---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Akamai 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Akamai 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: c8978504244fbf68875ca8cff7a92f042e8e988b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329494"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Akamai 整合

在本教學課程中，您會了解如何整合 Akamai 與 Azure Active Directory (Azure AD)。 在整合 Akamai 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Akamai 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Akamai。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

Azure Active Directory 和 Akamai 企業應用程式存取的整合，可讓您順暢地存取裝載於雲端或內部部署的舊版應用程式。 整合式解決方案可直接將 Azure Active Directory 的各種新式功能 (例如 [Azure AD 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)、[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 和 [Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)) 運用於舊版應用程式存取，而無須進行應用程式修改或代理程式安裝。

下圖說明如何將 Akamai EAA 運用於更廣泛的混合式安全存取案例

![將 Akamai EAA 運用於更廣泛的混合式安全存取案例](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>主要驗證案例

除了對 Open ID Connect、SAML 和 WS-Fed 等新式驗證通訊協定的 Azure Active Directory 原生整合支援外，Akamai EAA 也針對 Azure AD 的內部和外部存取，擴充了舊版驗證應用程式的安全存取，而支援對這些應用程式的新式案例 (例如，不需要密碼的存取)。 這包括：

* 標頭型驗證應用程式
* 遠端桌面
* SSH (安全殼層)
* Kerberos 驗證應用程式
* VNC (虛擬網路計算)
* 匿名驗證或沒有內建驗證的應用程式
* NTLM 驗證應用程式 (對使用者提出雙重提示的保護)
* 表單式驗證應用程式 (對使用者提出雙重提示的保護)

### <a name="integration-scenarios"></a>整合案例

Microsoft 與 Akamai EAA 的合作關係可根據您的商業需求支援多個整合案例，讓您靈活地達到商業需求。 這些案例可用來為所有應用程式提供零時差涵蓋範圍，並逐漸分類和設定適當的原則分類。

#### <a name="integration-scenario-1"></a>整合案例 1

Akamai EAA 設定為 Azure AD 上的單一應用程式。 管理員可設定應用程式的 CA 原則，一旦符合條件，使用者即可取得 Akamai EAA 入口網站的存取權。

**優點**：

* 您只需設定 IDP 一次

**缺點**：

* 使用者最終會有兩個應用程式入口網站

* 所有應用程式有共同的單一 CA 原則涵蓋範圍。

![整合案例 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>整合案例 2

Akamai EAA 應用程式個別設定於 Azure AD 入口網站上。 管理員可對應用程式設定個別的 CA 原則，一旦符合條件，使用者即可直接重新導向至特定的應用程式。

**優點**：

* 您可以定義個別的 CA 原則

* 所有應用程式都會顯示在 0365 Waffle 和 myApps.microsoft.com 面板上。


**缺點**：

* 您必須設定多個 IDP。

![整合案例 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Akamai 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

- Akamai 支援由 IDP 起始的 SSO

#### <a name="important"></a>重要事項

在**整合案例 1** 和**案例 2** 中，以下所列的所有設定都相同。 在**整合案例 2** 中，您已在 Akamai EAA 中設定個別 IDP，且必須修改 URL 屬性以指向應用程式 URL。

![Akamai 企業應用程式存取中 AZURESSO-SP [一般] 索引標籤的螢幕擷取畫面。 [驗證組態 URL] 欄位會反白顯示。](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>從資源庫新增 Akamai

若要設定將 Akamai 整合到 Azure AD 中，您需要從資源庫將 Akamai 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Akamai**。
1. 從結果面板選取 [Akamai]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>設定及測試 Akamai 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Akamai 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Akamai 中相關使用者之間的連結關聯性。

若要設定及測試與 Akamai 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Akamai SSO](#configure-akamai-sso)** - 在應用程式端設定單一登入設定。
    * **[設定 IDP](#setting-up-idp)**
    * **[以標頭為基礎的驗證](#header-based-authentication)**
    * **[遠端桌面](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos 驗證](#kerberos-authentication)**
    * **[建立 Akamai 測試使用者](#create-akamai-test-user)** - 使 Akamai 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Akamai] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Akamai 用戶端支援小組](https://www.akamai.com/us/en/contact-us/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Akamai] 區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Akamai 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Akamai]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-akamai-sso"></a>設定 Akamai SSO

### <a name="setting-up-idp"></a>設定 IDP

**AKAMAI EAA IDP 設定**

1. 登入 **Akamai 企業應用程式存取**主控台。
1. 在 [Akamai EAA 主控台] 上，選取 [身分識別] > [識別提供者]，然後按一下 [新增識別提供者]。

    ![Akamai EAA 主控台識別提供者視窗的螢幕擷取畫面。 在 [身分識別] 功能表上選取 [識別提供者]，然後選取 [新增識別提供者]。](./media/header-akamai-tutorial/configure01.png)

1. 在 [建立新的識別提供者] 上，執行下列步驟：

    ![Akamai EAA 主控台中 [建立新的識別提供者] 對話方塊的螢幕擷取畫面。](./media/header-akamai-tutorial/configure02.png)

    a. 指定 [唯一名稱]。

    b. 選擇 [第三方 SAML]，然後按一下 [建立識別提供者並設定]。

### <a name="general-settings"></a>一般設定

1. **身分識別攔截** - 指定名稱 (屬於 SP 基底 URL - 將用於 Azure AD 設定)

    > [!NOTE]
    > 您可以選擇擁有您自己的自訂網域 (需要有 DNS 項目和憑證)。 在此範例中，我們將使用 Akamai 網域。

1. **Akamai 雲端區域** - 選取適當的雲端區域。
1. **憑證驗證** - 查看 Akamai 文件 (選擇性)

    ![Akamai EAA 主控台 [一般] 索引標籤的螢幕擷取畫面，其中顯示身分識別攔截、Akamai 雲端區域和憑證驗證的設定。](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>驗證設定

1. URL - 指定與身分識別攔截相同的 URL (這是在驗證之後，使用者的重新導向目的地)。
2. 登出 URL：更新登出 URL。
3. 簽署 SAML 要求：預設為未核取。
4. 針對 IDP 中繼資料檔案，在 Azure AD 主控台中新增應用程式。

    ![Akamai EAA 主控台驗證組態的螢幕擷取畫面，其中顯示 URL、登出 URL、簽署 SAML 要求和 IDP 中繼資料檔案的設定。](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>工作階段設定

將設定保留為預設值。

![Akamai EAA 主控台工作階段設定對話方塊的螢幕擷取畫面。](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>目錄

略過目錄設定。

![Akamai EAA 主控台 [目錄] 索引標籤的螢幕擷取畫面。](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>自訂 UI

您可以將自訂新增至 IDP。

![Akamai EAA 主控台 [自訂] 索引標籤的螢幕擷取畫面，其中顯示自訂 UI、語言設定和佈景主題的設定。](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>進階設定

略過進階設定/如需詳細資訊，請參閱 Akamai 文件。

![Akamai EAA 主控台 [進階設定] 索引標籤的螢幕擷取畫面，其中顯示 EAA 用戶端、進階和 OIDC 至 SAML 橋接的設定。](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>部署

1. 按一下 [部署識別提供者]。

    ![Akamai EAA 主控台 [部署] 索引標籤的螢幕擷取畫面，其中顯示 [部署識別提供者] 按鈕。](./media/header-akamai-tutorial/deployment.png)

2. 確認部署成功

### <a name="header-based-authentication"></a>以標頭為基礎的驗證

以 Akamai 標頭為基礎的驗證

1. 從「新增應用程式」精靈選擇 [自訂 HTTP]。

    ![Akamai EAA 主控台 [新增應用程式] 精靈的螢幕擷取畫面，其中顯示 [存取應用程式] 區段中列出的 CustomHTTP。](./media/header-akamai-tutorial/configure05.png)

2. 輸入 [應用程式名稱] 和 [描述]。

    ![[自訂 HTTP 應用程式] 對話方塊的螢幕擷取畫面，其中顯示應用程式名稱和描述的設定。](./media/header-akamai-tutorial/configure06.png)

    ![Akamai EAA 主控台 [一般] 索引標籤的螢幕擷取畫面，其中顯示 MYHEADERAPP 的一般設定。](./media/header-akamai-tutorial/configure07.png)

    ![Akamai EAA 主控台的螢幕擷取畫面，其中顯示憑證和位置的設定。](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>驗證

1. 選取 [驗證] 索引標籤。

    ![Akamai EAA 主控台的螢幕擷取畫面，其中已選取 [驗證] 索引標籤。](./media/header-akamai-tutorial/configure09.png)

2. 指派 [識別提供者]

    ![Akamai EAA 主控台 [驗證] 索引標籤 (適用於 MYHEADERAPP) 的螢幕擷取畫面，其中顯示已設定為 Azure AD SSO 的識別提供者。](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>服務

按一下 [儲存] 並移至 [驗證]。

![Akamai EAA 主控台 [服務] 索引標籤 (適用於 MYHEADERAPP) 的螢幕擷取畫面，其中顯示右下角的 [儲存並移至進階設定] 按鈕。](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>進階設定

1. 在 [客戶 HTTP 標頭] 底下，指定 [CustomerHeader] 和 [SAML 屬性]。

    ![Akamai EAA 主控台 [進階設定] 索引標籤的螢幕擷取畫面，其中顯示 [驗證] 底下反白顯示的 [SSO 記錄 URL] 欄位。](./media/header-akamai-tutorial/configure12.png)

1. 按一下 [儲存] 並移至 [部署] 按鈕。

    ![Akamai EAA 主控台 [進階設定] 索引標籤的螢幕擷取畫面，其中顯示右下角的 [儲存並移至部署] 按鈕。](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>部署應用程式

1. 按一下 [部署應用程式] 按鈕。

    ![Akamai EAA 主控台 [部署] 索引標籤的螢幕擷取畫面，其中顯示 [部署應用程式] 按鈕。](./media/header-akamai-tutorial/configure14.png)

1. 確認應用程式已部署成功。

    ![Akamai EAA 主控台 [部署] 索引標籤的螢幕擷取畫面，其中顯示應用程式狀態訊息：「已成功部署應用程式」。](./media/header-akamai-tutorial/configure15.png)

1. 使用者體驗。

    ![此螢幕擷取畫面顯示 myapps.microsoft.com 的開啟畫面，其中具有背景影像和 [登入] 對話方塊。](./media/header-akamai-tutorial/enduser01.png)

    ![此螢幕擷取畫面顯示 [應用程式] 視窗的一部分，其中具有增益集、HRWEB、Akamai - CorpApps、費用、群組和存取權檢閱的圖示。 ](./media/header-akamai-tutorial/enduser02.png)

1. 條件式存取。

    ![訊息的螢幕擷取畫面：核准登入要求。 我們已傳送一則通知到您的行動裝置。 請回應以繼續。](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![[應用程式] 畫面的螢幕擷取畫面，其中顯示 MyHeaderApp 的圖示。](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>遠端桌面

1. 從「新增應用程式」精靈選擇 [RDP]。

    ![Akamai EAA 主控台 [新增應用程式] 精靈的螢幕擷取畫面，其中顯示在 [存取應用程式] 區段中應用程式之間列出的 RDP。](./media/header-akamai-tutorial/configure16.png)

1. 輸入 [應用程式名稱] 和 [描述]。

    ![[RDP 應用程式] 對話方塊的螢幕擷取畫面，其中顯示應用程式名稱和描述的設定。](./media/header-akamai-tutorial/configure17.png)

    ![Akamai EAA 主控台 [一般] 索引標籤的螢幕擷取畫面，其中顯示 SECRETRDPAPP 的應用程式身分識別設定。](./media/header-akamai-tutorial/configure18.png)

1. 指定會提供此項目的連接器。

    ![Akamai EAA 主控台的螢幕擷取畫面，其中顯示憑證和位置的設定。 相關聯的連接器會設定為 USWST-CON1。](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>驗證

按一下 [儲存] 並移至 [服務]。

![Akamai EAA 主控台 [驗證] 索引標籤 (適用於 SECRETRDPAPP) 的螢幕擷取畫面，其中顯示右下角的 [儲存並移至服務] 按鈕。](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>服務

按一下 [儲存] 並移至 [進階設定]。

![Akamai EAA 主控台 [服務] 索引標籤 (適用於 SECRETRDPAPP) 的螢幕擷取畫面，其中顯示右下角的 [儲存並移至進階設定] 按鈕。](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>進階設定

1. 按一下 [儲存] 並移至 [部署]。

    ![Akamai EAA 主控台 [進階設定] 索引標籤 (適用於 SECRETRDPAPP) 的螢幕擷取畫面，其中顯示遠端桌面組態的設定。](./media/header-akamai-tutorial/configure22.png)

    ![Akamai EAA 主控台 [進階設定] 索引標籤 (適用於 SECRETRDPAPP) 的螢幕擷取畫面，其中顯示驗證和健康情況檢查組態的設定。](./media/header-akamai-tutorial/configure23.png)

    ![Akamai EAA 主控台 [自訂 HTTP 標題] 索引標籤 (適用於 SECRETRDPAPP) 的螢幕擷取畫面，其中具有右下角的 [儲存並移至部署] 按鈕。](./media/header-akamai-tutorial/configure24.png)

1. 使用者體驗

    ![myapps.microsoft.com 視窗的螢幕擷取畫面，其中具有背景影像和 [登入] 對話方塊。](./media/header-akamai-tutorial/enduser03.png)

    ![myapps.microsoft.com [應用程式] 視窗的螢幕擷取畫面，其中具有增益集、HRWEB、Akamai - CorpApps、費用、群組和存取權檢閱的圖示。](./media/header-akamai-tutorial/enduser02.png)

1. 條件式存取

    ![條件式存取訊息的螢幕擷取畫面：核准登入要求。 我們已傳送一則通知到您的行動裝置。 請回應以繼續。](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![[應用程式] 畫面的螢幕擷取畫面，其中顯示 MyHeaderApp 和 SecretRDPApp 的圖示。](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Windows Server 2012 RS 畫面的螢幕擷取畫面，其中顯示一般使用者圖示。 管理員、user0 和 user1 的圖示會顯示他們已登入。](./media/header-akamai-tutorial/conditionalaccess06.png)

1. 或者，您也可以直接輸入 RDP 應用程式 URL。

#### <a name="ssh"></a>SSH

1. 移至 [新增應用程式]，選擇 [SSH]。

    ![Akamai EAA 主控台 [新增應用程式] 精靈的螢幕擷取畫面，其中顯示在 [存取應用程式] 區段中應用程式之間列出的 SSH。](./media/header-akamai-tutorial/configure25.png)

1. 輸入 [應用程式名稱] 和 [描述]。

    ![[SSH 應用程式] 對話方塊的螢幕擷取畫面，其中顯示應用程式名稱和描述的設定。](./media/header-akamai-tutorial/configure26.png)

1. 設定應用程式識別碼。

    ![Akamai EAA 主控台 [一般] 索引標籤的螢幕擷取畫面，其中顯示 SSH-SECURE 的應用程式身分識別設定。](./media/header-akamai-tutorial/configure27.png)

    a. 指定名稱/描述。

    b. 指定 SSH 的應用程式伺服器 IP/FQDN 和連接埠。

    c. 指定 SSH 使用者名稱/複雜密碼 *核取 Akamai EAA。

    d. 指定外部主機名稱。

    e. 指定連接器的位置，然後選擇連接器。

#### <a name="authentication"></a>驗證

按一下 [儲存] 並移至 [服務]。

![Akamai EAA 主控台 [驗證] 索引標籤 (適用於 SSH-SECURE) 的螢幕擷取畫面，其中顯示右下角的 [儲存並移至服務] 按鈕。](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>服務

按一下 [儲存] 並移至 [進階設定]。

![Akamai EAA 主控台 [服務] 索引標籤 (適用於 SSH-SECURE) 的螢幕擷取畫面，其中顯示右下角的 [儲存並移至進階設定] 按鈕。](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>進階設定

按一下 [儲存] 並移至 [部署]

![Akamai EAA 主控台 [進階設定] 索引標籤 (適用於 SSH-SECURE) 的螢幕擷取畫面，其中顯示驗證和健康情況檢查組態的設定。](./media/header-akamai-tutorial/configure30.png)

![Akamai EAA 主控台 [自訂 HTTP 標題] 索引標籤 (適用於 SSH-SECURE) 的螢幕擷取畫面，其中具有右下角的 [儲存並移至部署] 按鈕。](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>部署

1. 按一下 [部署應用程式]。

    ![Akamai EAA 主控台 [部署] 索引標籤 (適用於 SSH-SECURE) 的螢幕擷取畫面，其中顯示 [部署應用程式] 按鈕。](./media/header-akamai-tutorial/configure32.png)

1. 使用者體驗

    ![myapps.microsoft.com 視窗 [登入] 對話方塊的螢幕擷取畫面。](./media/header-akamai-tutorial/enduser03.png)

    ![myapps.microsoft.com [應用程式] 視窗的螢幕擷取畫面，其中顯示增益集、HRWEB、Akamai - CorpApps、費用、群組和存取權檢閱的圖示。](./media/header-akamai-tutorial/enduser04.png)

1. 條件式存取

    ![顯示訊息的螢幕擷取畫面：核准登入要求。 我們已傳送一則通知到您的行動裝置。 請回應以繼續。](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![[應用程式] 畫面的螢幕擷取畫面，其中顯示 MyHeaderApp、SSH Secure 和 SecretRDPApp 的圖示。](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![ssh-secure-go.akamai-access.com 命令視窗的螢幕擷取畫面，其中顯示密碼提示。](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![ssh-secure-go.akamai-access.com 命令視窗的螢幕擷取畫面，其中顯示應用程式的相關資訊，並顯示命令的提示。](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Kerberos 驗證

在下列範例中，我們將發佈內部網頁伺服器 <code>http://frp-app1.superdemo.live</code>，並使用 KCD 啟用 SSO

#### <a name="general-tab"></a>General Tab

![Akamai EAA 主控台 [一般] 索引標籤 (適用於 MYKERBOROSAPP) 的螢幕擷取畫面。](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>[驗證] 索引標籤

指派識別提供者

![Akamai EAA 主控台 [驗證] 索引標籤 (適用於 MYKERBOROSAPP) 的螢幕擷取畫面，其中顯示已設定為 Azure AD SSO 的識別提供者。](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>服務索引標籤

![Akamai EAA 主控台 [服務] 索引標籤 (適用於 MYKERBOROSAPP) 的螢幕擷取畫面。](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>進階設定

![Akamai EAA 主控台 [進階設定] 索引標籤 (適用於 MYKERBOROSAPP) 的螢幕擷取畫面，其中顯示相關應用程式和驗證的設定。](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> 網頁伺服器的 SPN 必須是 SPN@Domain 格式，以此示範為例，必須是 `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE`。 將其餘設定保留為預設值。

#### <a name="deployment-tab"></a>部署索引標籤

![Akamai EAA 主控台 [部署] 索引標籤 (適用於 MYKERBOROSAPP) 的螢幕擷取畫面，其中顯示 [部署應用程式] 按鈕。](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>新增目錄

1. 從下拉式清單中選取 [AD]。

    ![Akamai EAA 主控台 [目錄] 視窗的螢幕擷取畫面，其中顯示 [建立新目錄] 對話方塊，並在 [目錄類型] 的下拉式清單中選取 [AD]。](./media/header-akamai-tutorial/configure33.png)

1. 提供必要的資料。

    ![Akamai EAA 主控台 SUPERDEMOLIVE 視窗的螢幕擷取畫面，其中具有 DirectoryName、目錄服務、連接器和屬性對應的設定。](./media/header-akamai-tutorial/configure34.png)

1. 確認目錄已建立。

    ![Akamai EAA 主控台 [目錄] 視窗的螢幕擷取畫面，其中顯示已新增目錄 superdemo.live。](./media/header-akamai-tutorial/directorydomain.png)

1. 新增需要存取權的群組/OU。

    ![目錄 superdemo.live 設定的螢幕擷取畫面。 您選取用來新增群組或 OU 的圖示會反白顯示。](./media/header-akamai-tutorial/addgroup.png)

1. 在下列畫面中，群組稱為 EAAGroup，且有 1 個成員。

    ![Akamai EAA 主控台 GROUPS ON SUPERDEMOLIVE DIRECTORY 視窗的螢幕擷取畫面。 具有 1 個使用者的 EAAGroup 會列示在 [群組] 底下。](./media/header-akamai-tutorial/eaagroup.png)

1. 按一下 [身分識別] > [識別提供者] 將目錄新增至您的識別提供者，並按一下 [目錄] 索引標籤，然後按一下 [指派目錄]。

    ![Akamai EAA 主控台 [目錄] 索引標籤 (適用於 Azure AD SSO) 的螢幕擷取畫面，其中顯示目前所指派目錄清單中的 superdemo.live。](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>為 EAA 設定 KCD 委派的逐步解說

#### <a name="step-1-create-an-account"></a>步驟 1:建立帳戶 

1. 在此範例中，我們將使用名為 **EAADelegation** 的帳戶。 您可以使用 **Active Directory 使用者和電腦** Snappin 來執行此作業。

    ![Akamai EAA 主控台 [目錄] 索引標籤 (適用於 Azure AD SSO) 的螢幕擷取畫面。 目錄 superdemo 會列示在目前指派的目錄底下。](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > 根據 [身分識別攔截名稱]，使用者名稱必須採用特定格式。 從圖 1 中，該名稱顯示為 **corpapps.login.go.akamai-access.com**

1. 使用者登入名稱將是：`HTTP/corpapps.login.go.akamai-access.com`

    ![顯示 EAADelegation 屬性的螢幕擷取畫面，其中名字設定為 "EAADelegation"，而使用者登入名稱設定為 HTTP/corpapps.login.go.akamai-access.com。](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>步驟 2:設定此帳戶的 SPN

1. 根據此範例，SPN 將如下所示。

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![管理員命令提示字元的螢幕擷取畫面，其中顯示命令 setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation 的結果。](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>步驟 3：設定委派

1. 針對 EAADelegation 帳戶，按一下 [委派] 索引標籤。

    ![管理員命令提示字元的螢幕擷取畫面，其中顯示用於設定 SPN 的命令。](./media/header-akamai-tutorial/spn.png)

    * 指定使用任何驗證通訊協定
    * 按一下 [新增]，然後新增 Kerberos 網站的應用程式集區帳戶。 如果正確設定，應會自動解析為正確的 SPN。

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>步驟 4：建立 AKAMAI EAA 的金鑰表檔案

1. 以下是一般語法。

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. 範例說明

    | 程式碼片段 | 說明 |
    | - | - |
    | Ktpass /out EAADemo.keytab | // 輸出金鑰表檔案的名稱 |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // EAA 委派帳戶 |
    | /pass RANDOMPASS | // EAA 委派帳戶密碼 |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // 請參閱 Akamai EAA 文件 |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![管理員命令提示字元的螢幕擷取畫面，其中顯示為 AKAMAI EAA 建立金鑰表檔案的命令結果。](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>步驟 5：在 AKAMAI EAA 主控台中匯入金鑰表

1. 按一下 [系統] > [金鑰表]。

    ![Akamai EAA 主控台的螢幕擷取畫面，其中顯示從 [系統] 功能表選取的金鑰表。](./media/header-akamai-tutorial/keytabs.png)

1. 在 [金鑰表類型] 中，選擇 [Kerberos 委派]。

    ![Akamai EAA 主控台 EAAKEYTAB 畫面的螢幕擷取畫面，其中顯示金鑰表設定。 [金鑰表類型] 會設定為 [Kerberos 委派]。](./media/header-akamai-tutorial/keytabdelegation.png)

1. 確定金鑰表顯示為 [已部署] 和 [已驗證]。

    ![Akamai EAA 主控台 KEYTABS 畫面的螢幕擷取畫面，其中將 EAA 金鑰表列為「已部署和已驗證的金鑰表」。](./media/header-akamai-tutorial/keytabs02.png)

1. 使用者體驗

    ![myapps.microsoft.com 中 [登入] 對話方塊的螢幕擷取畫面。 ](./media/header-akamai-tutorial/enduser03.png)

    ![myapps.microsoft.com [應用程式] 視窗的螢幕擷取畫面，其中顯示應用程式圖示。](./media/header-akamai-tutorial/enduser04.png)

1. 條件式存取

    ![顯示「核准登入要求」訊息的螢幕擷取畫面。 訊息。](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![[應用程式] 畫面的螢幕擷取畫面，其中顯示 MyHeaderApp、SSH Secure、SecretRDPApp 和 myKerberosApp 的圖示。](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![myKerberosApp 啟動顯示畫面的螢幕擷取畫面。 「歡迎 superdemo\user1」訊息會顯示在背景影像上。](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>建立 Akamai 測試使用者

在本節中，您要在 Akamai 中建立名為 B.Simon 的使用者。 請與 [Akamai 用戶端支援小組](https://www.akamai.com/us/en/contact-us/)合作，在 Akamai 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Akamai] 圖格時，應該會自動登入您已設定 SSO 的 Akamai。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Akamai](https://aad.portal.azure.com/)
