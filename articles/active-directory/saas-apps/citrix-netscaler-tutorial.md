---
title: 教學課程：Azure Active Directory 單一登入與 Citrix NetScaler (Kerberos 型驗證) 整合 | Microsoft Docs
description: 了解如何使用 Kerberos 型驗證來設定 Azure Active Directory 與 Citrix NetScaler 之間的單一登入 (SSO)。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.openlocfilehash: 4ff6154e17408b9e2daaf3c81321ae31693de3aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544599"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>教學課程：Azure Active Directory 單一登入與 Citrix NetScaler (Kerberos 型驗證) 整合

在本教學課程中，您會了解如何整合 Citrix NetScaler 與 Azure Active Directory (Azure AD)。 在整合 Citrix NetScaler 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Citrix NetScaler 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Citrix NetScaler。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Citrix NetScaler 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 本教學課程包含下列案例：

* Citrix NetScaler 的 **SP** 起始 SSO

* Citrix NetScaler 的 **Just In Time** 使用者佈建

* [Citrix NetScaler 的 Kerberos 型驗證](#publish-the-web-server)

* [Citrix NetScaler 的標頭型驗證](header-citrix-netscaler-tutorial.md#publish-the-web-server)

* 設定 Citrix NetScaler 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-citrix-netscaler-from-the-gallery"></a>從資源庫新增 Citrix NetScaler

若要整合 Citrix NetScaler 與 Azure AD，請先將 Citrix NetScaler 從資源庫新增至受控 SaaS 應用程式清單：

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側功能表中，選取 [Azure Active Directory]  。

1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

1. 若要新增新的應用程式，請選取 [新增應用程式]  。

1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Citrix NetScaler**。

1. 從結果中選取 [Citrix NetScaler]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>設定及測試 Citrix NetScaler 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Citrix NetScaler 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Citrix NetScaler 中相關使用者之間的連結關聯性。

若要設定及測試與 Citrix NetScaler 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. [設定 Azure AD SSO](#configure-azure-ad-sso) - 讓您的使用者能夠使用此功能。

    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user) - 以 B.Simon 測試 Azure AD SSO。

    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user) - 讓 B.Simon 能夠使用 Azure AD SSO。

1. [設定 Citrix NetScaler SSO](#configure-citrix-netscaler-sso) - 在應用程式端設定 SSO 設定。

    * [建立 Citrix NetScaler 測試使用者](#create-a-citrix-netscaler-test-user) - 讓 Citrix NetScaler 中有對應 Britta Simon 的使用者，並連結到該使用者在 Azure AD 中的代表項目。

1. [測試 SSO](#test-sso) - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

若要使用 Azure 入口網站啟用 Azure AD SSO，請完成下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Citrix NetScaler]  應用程式整合頁面上，從 [管理]  底下選取 [單一登入]  。

1. 在 [選取單一登入方法]  窗格中，選取 [SAML]  。

1. 在 [以 SAML 設定單一登入]  窗格上，選取 [基本 SAML 組態]  的 [編輯]  畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段中，以 **IDP 起始**模式設定應用程式：

    1. 在 [識別碼]  文字方塊中，輸入具有下列模式的 URL：`https://<Your FQDN>`

    1. 在 [回覆 URL]  文字方塊中，輸入具有下列模式的 URL：`http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. 若要以 **SP 起始**模式設定應用程式，請選取 [設定其他 URL]  ，然後完成下列步驟：

    * 在 [登入 URL]  文字方塊中，輸入具有下列模式的 URL：`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * 本節中使用的 URL 不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」值來更新這些值。 請連絡 [Citrix NetScaler 用戶端支援小組](https://www.citrix.com/contact/technical-support.html)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。
    > * 若要設定 SSO，您必須使用可從公用網站存取的 URL。 您必須在 Citrix NetScaler 端啟用防火牆或其他安全性設定，才能讓 Azure AD 在所設定的 URL 上公佈權杖。

1. 在 [以 SAML 設定單一登入]  窗格的 [SAML 簽署憑證]  區段中，複製 [應用程式同盟中繼資料 URL]  的 URL，並貼到記事本中。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Citrix NetScaler]  區段上，根據您的需求複製相關 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左側功能表中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

1. 在窗格頂端選取 [新增使用者]  。

1. 在 [使用者]  屬性中，完成下列步驟：

   1. 針對 [名稱]  輸入 `B.Simon`。  

   1. 針對 [使用者名稱]  ，輸入 _username@companydomain.extension_ 。 例如： `B.Simon@contoso.com` 。

   1. 選取 [顯示密碼]  核取方塊，然後記下或複製 [密碼]  中顯示的值。

   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Citrix NetScaler 的使用者存取權授與 B.Simon 使用者，使其能夠使用 Azure SSO。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。

1. 在應用程式清單中，選取 [Citrix NetScaler]  。

1. 在應用程式概觀中，選取 [管理]  底下的 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者]  清單中，選取 [B.Simon]  。 選擇 [選取]  。

1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取相關使用者角色，然後選擇 [選取]  。

1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-citrix-netscaler-sso"></a>設定 Citrix NetScaler SSO

針對您想要設定的驗證類型，選取其步驟的連結：

- [設定 Citrix NetScaler SSO 以進行 Kerberos 型驗證](#publish-the-web-server)

- [設定 Citrix NetScaler SSO 以進行標頭型驗證](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>發佈 Web 伺服器 

若要建立虛擬伺服器：

1. 選取 [流量管理]   > [負載平衡]   > [服務]  。
    
1. 選取 [新增]  。

    ![Citrix NetScaler 設定 - 服務窗格](./media/citrix-netscaler-tutorial/web01.png)

1. 針對執行應用程式的 Web 伺服器，設定下列值：

   * **服務名稱**
   * **伺服器 IP/現有伺服器**
   * **通訊協定**
   * **通訊埠**

### <a name="configure-the-load-balancer"></a>設定負載平衡器

設定負載平衡器：

1. 移至 [流量管理]   > [負載平衡]   > [虛擬伺服器]  。

1. 選取 [新增]  。

1. 如下列螢幕擷取畫面所述，設定下列值：

    * **名稱**
    * **通訊協定**
    * **IP 位址**
    * **通訊埠**

1. 選取 [確定]  。

    ![Citrix NetScaler 設定 - 基本設定窗格](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>繫結虛擬伺服器

若要繫結負載平衡器與虛擬伺服器：

1. 在 [服務和服務群組]  窗格中，選取 [無負載平衡虛擬伺服器服務繫結]  。

   ![Citrix NetScaler 設定 - 負載平衡虛擬伺服器服務繫結窗格](./media/citrix-netscaler-tutorial/bind01.png)

1. 確認下列螢幕擷取畫面所示的設定，然後選取 [關閉]  。

   ![Citrix NetScaler 設定 - 驗證虛擬伺服器服務繫結](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>繫結憑證

若要將此服務發佈為 TLS，請繫結伺服器憑證，然後測試您的應用程式：

1. 在 [憑證]  底下選取 [無伺服器憑證]  。

   ![Citrix NetScaler 設定 - 伺服器憑證窗格](./media/citrix-netscaler-tutorial/bind03.png)

1. 確認下列螢幕擷取畫面所示的設定，然後選取 [關閉]  。

   ![Citrix NetScaler 設定 - 驗證憑證](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML 設定檔

若要設定 Citrix ADC SAML 設定檔，請完成下列各節。

### <a name="create-an-authentication-policy"></a>建立驗證原則

若要建立驗證原則：

1. 移至 [安全性]   > [AAA – 應用程式流量]   > [原則]   > [驗證]   > [驗證原則]  。

1. 選取 [新增]  。

1. 在 [建立驗證原則]  窗格中，輸入或選取下列值：

    * **Name**：輸入驗證原則的名稱。
    * **動作**：輸入 **SAML**，然後選取 [新增]  。
    * **運算式**：輸入 **true**。     
    
    ![Citrix NetScaler 設定 - 建立驗證原則窗格](./media/citrix-netscaler-tutorial/policy01.png)

1. 選取 [建立]  。

### <a name="create-an-authentication-saml-server"></a>建立驗證 SAML 伺服器

若要建立驗證 SAML 伺服器，請移至 [建立驗證 SAML 伺服器]  窗格，然後完成下列步驟：

1. 在 [名稱]  中，輸入驗證 SAML 伺服器的名稱。

1. 在 [匯出 SAML 中繼資料]  底下：

   1. 選取 [匯入中繼資料]  核取方塊。

   1. 輸入您先前從 Azure SAML UI 複製的同盟中繼資料 URL。
    
1. 在 [簽發者名稱]  中，輸入相關 URL。

1. 選取 [建立]  。

![Citrix NetScaler 設定 - 建立驗證 SAML 伺服器窗格](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>建立驗證虛擬伺服器

若要建立驗證虛擬伺服器：

1.  移至 [安全性]   > [AAA – 應用程式流量]   > [原則]   > [驗證]   > [驗證虛擬伺服器]  。

1.  選取 [新增]  ，然後完成下列步驟：

    1. 在 [名稱]  中，輸入驗證虛擬伺服器的名稱。

    1. 選取 [無法定址]  核取方塊。

    1. 在 [通訊協定]  中，選取 [SSL]  。

    1. 選取 [確定]  。
    
1. 選取 [繼續]  。

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>將驗證虛擬伺服器設定為使用 Azure AD

修改驗證虛擬伺服器的兩個區段：

1.  在 [進階驗證原則]  窗格中，選取 [無驗證原則]  。

    ![Citrix NetScaler 設定 - 進階驗證原則窗格](./media/citrix-netscaler-tutorial/virtual01.png)

1. 在 [原則繫結]  窗格上，選取驗證原則，然後選取 [繫結]  。

    ![Citrix NetScaler 設定 - 原則繫結窗格](./media/citrix-netscaler-tutorial/virtual02.png)

1. 在 [表單式虛擬伺服器]  窗格上，選取 [無負載平衡虛擬伺服器]  。

    ![Citrix NetScaler 設定 - 表單型虛擬伺服器窗格](./media/citrix-netscaler-tutorial/virtual03.png)

1. 針對**驗證 FQDN**，請輸入完整網域名稱 (FQDN)(必要)。

1. 選擇您想要使用 Azure AD 驗證來保護的負載平衡虛擬伺服器。

1. 選取 [繫結]  。

    ![Citrix NetScaler 設定 - 負載平衡虛擬伺服器繫結窗格](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > 請務必在 [驗證虛擬伺服器設定]  窗格上選取 [完成]  。

1. 若要驗證您的變更，請在瀏覽器中移至應用程式 URL。 您應該會看到您的租用戶登入頁面，而不是您先前看到的未驗證存取。

    ![Citrix NetScaler 設定 - 網頁瀏覽器中的登入頁面](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>設定 Citrix NetScaler SSO 以進行 Kerberos 型驗證

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>建立 Citrix ADC 的 Kerberos 委派帳戶

1. 建立使用者帳戶 (在此範例中，我們使用 AppDelegation  )。

    ![Citrix NetScaler 設定 - 屬性窗格](./media/citrix-netscaler-tutorial/kerberos01.png)

1. 為此帳戶設定主機 SPN。 

    範例： `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    在此範例中：

    * `IDENTT.WORK` 是網域 FQDN。
    * `identt` 是網域 NetBIOS 名稱。
    * `appdelegation` 是委派使用者帳戶名稱。

1. 設定 Web 伺服器的委派，如下列螢幕擷取畫面所示：
 
    ![Citrix NetScaler 設定 - 屬性窗格底下的委派](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > 在螢幕擷取畫面的範例中，執行 Windows 整合式驗證 (WIA) 網站的內部 Web 伺服器名稱是 CWEB2  。

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix AAA KCD ( Kerberos 委派帳戶)

若要設定 Citrix NetScaler AAA KCD 帳戶：

1.  移至 [Citrix 閘道]   > [AAA KCD (Kerberos 限制委派) 帳戶]  。

1.  選取 [新增]  ，然後輸入或選取下列值︰

    * **Name**：輸入 KCD 帳戶的名稱。

    * **Realm**：以大寫輸入網域和擴充功能。

    * **服務 SPN**：`http/<host/fqdn>@<DOMAIN.COM>`。
    
        > [!NOTE]
        > `@DOMAIN.COM` 是必要項目，而且必須是大寫。 範例： `http/cweb2@IDENTT.WORK`.

    * **委派的使用者**：輸入委派的使用者名稱。

    * 選取 [委派使用者的密碼]  核取方塊，然後輸入密碼並確認。

1. 選取 [確定]  。
 
    ![Citrix NetScaler 設定 - 設定 KCD 帳戶窗格](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Citrix 流量原則和流量設定檔

若要設定 Citrix 流量原則和流量設定檔：

1.  移至 [安全性]   > [AAA - 應用程式流量]   > [原則]   > [流量原則、設定檔和表單 SSO ProfilesTraffic 原則]  。

1.  選取 [流量設定檔]  。

1.  選取 [新增]  。

1.  若要設定流量設定檔，請輸入或選取下列值。

    * **Name**：輸入流量設定檔的名稱。

    * **單一登入**：選取 [開啟]  。

    * **KCD 帳戶**：選取您在先前小節中建立的 KCD 帳戶。

1. 選取 [確定]  。

    ![Citrix NetScaler 設定 - 設定流量設定檔窗格](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  選取 [流量原則]  。

1.  選取 [新增]  。

1.  若要設定流量原則，請輸入或選取下列值：

    * **Name**：輸入流量原則的名稱。

    * **設定檔**：選取您在先前小節中建立的流量設定檔。

    * **運算式**：輸入 **true**。

1. 選取 [確定]  。

    ![Citrix NetScaler 設定 - 設定流量原則窗格](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>將流量原則繫結至 Citrix 中的虛擬伺服器

若要使用 GUI 將流量原則繫結至虛擬伺服器：

1. 移至 [流量管理]   > [負載平衡]   > [虛擬伺服器]  。

1. 在虛擬伺服器清單中，選取您要作為重寫原則繫結目標的 [虛擬伺服器]，然後選取 [開啟]  。

1. 在 [負載平衡虛擬伺服器]  窗格的 [進階設定]  底下，選取 [原則]  。 針對 NetScaler 執行個體設定的所有原則都會出現在清單中。
 
    ![Citrix NetScaler 設定 - 負載平衡虛擬伺服器窗格](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Citrix NetScaler 設定 - 原則對話方塊](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  在您要作為此虛擬伺服器繫結目標的原則名稱旁邊，選取核取方塊。
 
    ![Citrix NetScaler 設定 - 負載平衡虛擬伺服器流量原則繫結窗格](./media/citrix-netscaler-tutorial/kerberos09.png)

1. 在 [選擇類型]  對話方塊中：

    1. 針對 [選擇原則]  ，請選取 [流量]  。

    1. 針對 [選擇類型]  ，請選取 [要求]  。

    ![Citrix NetScaler 設定 - 選擇類型窗格](./media/citrix-netscaler-tutorial/kerberos08.png)

1. 當原則已繫結時，請選取 [完成]  。
 
    ![Citrix NetScaler 設定 - 原則窗格](./media/citrix-netscaler-tutorial/kerberos10.png)

1. 使用 WIA 網站測試繫結。

    ![Citrix NetScaler 設定 - 網頁瀏覽器中的測試頁面](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>建立 Citrix NetScaler 測試使用者

本節會在 Citrix NetScaler 中建立名為 B.Simon 的使用者。 Citrix NetScaler 支援預設會啟用的 Just-In-Time 使用者佈建。 這一節沒有您需要進行的動作。 如果 Citrix NetScaler 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [Citrix NetScaler 用戶端支援小組](https://www.citrix.com/contact/technical-support.html)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD SSO 組態。

當您在存取面板中選取 [Citrix NetScaler] 圖格時，應該會自動登入您已設定 SSO 的 Citrix NetScaler。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Citrix NetScaler](https://aad.portal.azure.com/)

- [設定 Citrix NetScaler 單一登入以進行標頭型驗證](header-citrix-netscaler-tutorial.md)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Citrix NetScaler](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)