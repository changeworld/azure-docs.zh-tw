---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Kemp LoadMaster Azure AD 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Kemp LoadMaster Azure AD 整合之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f42e1731-0477-4955-9571-b69a0f3de0bc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd436462d157a7e416f0f6a468edd33520ccec7d
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009868"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>教學課程：Azure Active Directory SSO 與 Kemp LoadMaster Azure AD 整合

在本教學課程中，您將了解如何整合 Kemp LoadMaster Azure AD 整合與 Azure Active Directory (Azure AD)。 在整合 Kemp LoadMaster Azure AD 整合與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Kemp LoadMaster Azure AD 整合的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Kemp LoadMaster Azure AD 整合。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Kemp LoadMaster Azure AD 整合單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Kemp LoadMaster Azure AD 整合支援 **IDP** 起始的 SSO
* 設定 Kemp LoadMaster Azure AD 整合後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>從資源庫新增 Kemp LoadMaster Azure AD 整合

若要設定 Kemp LoadMaster Azure AD 整合與 Azure AD 整合，您需要從資源庫將 Kemp LoadMaster Azure AD 整合新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Kemp LoadMaster Azure AD 整合**。
1. 從結果面板選取 [Kemp LoadMaster Azure AD 整合]，然後新增該應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>設定和測試適用於 Kemp LoadMaster Azure AD 整合的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Kemp LoadMaster Azure AD 整合搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Kemp LoadMaster Azure AD 整合中相關使用者之間的連結關聯性。

若要設定及測試與 Kemp LoadMaster Azure AD 整合搭配運作的 Azure AD SSO，請完成下列建置區塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Kemp LoadMaster Azure AD 整合 SSO](#configure-kemp-loadmaster-azure-ad-integration-sso)** - 在應用程式端設定單一登入設定。

1. **[發佈 Web 伺服器](#publishing-web-server)**
    1. **[建立虛擬伺服器](#create-a-virtual-service)**
    1. **[憑證和安全性](#certificates-and-security)**
    1. **[Kemp LoadMaster Azure AD 整合 SAML 設定檔](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[確認變更](#verify-the-changes)**
1. **[設定 Kerberos 式驗證](#configuring-kerberos-based-authentication)**
    1. **[建立適用於 Kemp LoadMaster Azure AD 整合的 Kerberos 委派帳戶](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Kemp LoadMaster Azure AD 整合 KCD (Kerberos 委派帳戶)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp LoadMaster Azure AD 整合 ESP](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[建立 Kemp LoadMaster Azure AD 整合測試使用者](#create-kemp-loadmaster-azure-ad-integration-test-user)** - 使 Kemp LoadMaster Azure AD 整合中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Kemp LoadMaster Azure AD 整合] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，輸入下列欄位的值：

    a. 在 [識別碼 (實體識別碼)] 文字方塊中，輸入 URL：`https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. 在 [回覆 URL]  文字方塊中，鍵入 URL：`https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Kemp LoadMaster Azure AD 整合用戶端支援](mailto:support@kemp.ax)小組來取得此值。 您也可以參考 Azure 入口網站中 [基本 SAML 設定] 區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，尋找 [憑證 (Base64)] 和 [同盟中繼資料 XML]，然後選取 [下載]，以下載憑證和同盟中繼資料 XML 檔案並儲存在電腦上。

    ![憑證下載連結](./media/kemp-tutorial/certificate-base-64.png)

1. 在 [設定 Kemp LoadMaster Azure AD 整合] 區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Kemp LoadMaster Azure AD 整合的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Kemp LoadMaster Azure AD 整合]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>設定 Kemp LoadMaster Azure AD 整合 SSO

## <a name="publishing-web-server"></a>發佈 Web 伺服器 

### <a name="create-a-virtual-service"></a>建立虛擬服務 

1. 移至 Kemp LoadMaster Azure AD 整合 Load Master Web UI > 虛擬服務 > 新增。

1. 按一下 [新增]。

1. 指定虛擬服務的參數。

    ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-1.png)

    a. 虛擬位址
    
    b. 連接埠
    
    c. 服務名稱 (選用)
    
    d. 通訊協定 

1. 瀏覽至 Real Server 區段。

1. 按一下 [新增]。

1. 指定 Real Server 的參數。
    
    ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-2.png)

    a. 選取 [允許遠端位址]
    
    b. 輸入 Real Server 位址
    
    c. 連接埠
    
    d. 轉接方法
    
    e. Weight
    
    f. 連線限制
    
    g. 按一下 [新增此 Real Server]

## <a name="certificates-and-security"></a>憑證和安全性
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>在 Kemp LoadMaster Azure AD 整合上匯入憑證 
 
1. 移至 Kemp LoadMaster Azure AD 整合 Web 入口網站 > 憑證與安全性 > SSL 憑證。

1. 在管理憑證 > 憑證組態下。

1. 按一下 [匯入憑證]。

1. 指定包含憑證的檔案名稱。 檔案也可以存放私密金鑰。 如果檔案不包含私密金鑰，那麼也必須指定包含私密金鑰的檔案。 憑證可以是 .PEM 或 .PFX (IIS) 格式。

1. 按一下 [在憑證檔案上選擇檔案]。

1. 按一下 [金鑰檔案] (選用)。

1. 按一下 [儲存]。

### <a name="ssl-acceleration"></a>SSL 加速
 
1. 移至 Kemp Load Master Web UI > 虛擬服務 > 虛擬服務。

1. 按一下作業下的 [修改]。

1. 在 tcp/x.x.x.:443 (ID:6) 屬性下 (第 7 層作業)，按一下 [SSL 屬性]。
    
    ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-3.png)
    
    a. 在 SSL 加速中按一下 [已啟用]。
    
    b. 在可用的憑證下，選取匯入的憑證，然後按一下 `>` 符號。
    
    c. 一旦所需的 SSL 憑證出現在「已指派的憑證」中，請按一下 [設定憑證]。

    > [!NOTE]
    > 請確定您按下了 [設定憑證]。

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Kemp LoadMaster Azure AD 整合 SAML 設定檔
 
### <a name="import-idp-certificate"></a>匯入 IdP 憑證

移至 Kemp LoadMaster Azure AD 整合 Web 主控台 

1. 按一下 [憑證和授權] 下的 [中繼憑證]。

    ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-6.png)

    a. 按一下 [新增新中繼憑證] 中的 [選擇檔案]。
    
    b. 瀏覽至先前從 Azure AD 企業應用程式下載的憑證檔案。
    
    c. 按一下 [開啟]。
    
    d. 在 [憑證名稱] 中提供名稱。
    
    e. 按一下 [新增憑證]。

### <a name="create-authentication-policy"></a>建立驗證原則 
 
移至 [虛擬服務] 下的 [管理 SSO]。

   ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-7.png)
   
   a. 在提供名稱後，按一下 [新增用戶端組態] 下的 [新增]。

   b. 在 [驗證通訊協定] 下，選取 [SAML]。

   c. 選取 [IdP 佈建] 下的 [中繼資料檔案]。 

   d. 按一下 [選擇檔案]。

   e. 瀏覽至先前從 Azure 入口網站下載的 XML。

   f. 按一下 [開啟]，然後按一下 [匯入 IdP 中繼資料檔案]。

   g. 選取來自 IdP 憑證的中繼憑證。

   h. 設定應符合在 Azure 入口網站中建立之身分識別的 SP 實體識別碼 

   i. 按一下 [設定 SP 實體識別碼]。

### <a name="set-authentication"></a>設定驗證  
 
在 Kemp LoadMaster Azure AD 整合 Web 主控台中

1. 按一下 [虛擬服務]。

1. 按一下 [檢視/修改服務]。

1. 按一下 [修改] 並瀏覽至 [ESP 選項]。
    
    ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-8.png)

    a. 按一下 [啟用 ESP]。
    
    b. 在用戶端驗證模式中選取 SAML。
    
    c. 在 SSO 網域中選取先前建立的用戶端驗證。
    
    d. 在 [允許的虛擬主機] 中輸入主機名稱，然後按一下 [設定允許的虛擬主機]。
    
    e. 在「允許的虛擬目錄 (根據存取需求)」中輸入 /*，然後按一下 [設定允許的目錄]。

### <a name="verify-the-changes"></a>確認變更 
 
瀏覽至應用程式 URL 

您應該會看到租用戶登入頁面，而不是先前未驗證的存取。 

![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>設定 Kerberos 式驗證 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>建立適用於 Kemp LoadMaster Azure AD 整合的 Kerberos 委派帳戶 

1. 建立使用者帳戶 (在此範例中為 AppDelegation)。
    
    ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-10.png)


    a. 選取 [屬性編輯器] 索引標籤。

    b. 瀏覽至 servicePrincipalName。 

    c. 選取 servicePrincipalName，然後按一下 [編輯]。

    d. 在 [要新增的值] 欄位中輸入 http/kcduser，然後按一下 [新增]。 

    e. 依序按一下 [套用] 和 [確定]。 再次開啟視窗之前必須先關閉 (以查看新的「委派」索引標籤)。 

1. 再次開啟 [使用者屬性] 視窗，這時您會看到 [委派] 索引標籤變成可用。 

1. 選取 [委派] 索引標籤。

    ![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-11.png)

    a. 選取 [信任這個使用者，但只委派指定的服務]。

    b. 選取 [使用任何驗證通訊協定]。

    c. 新增 Real Server，並新增 http 作為服務。 
    
    d. 選取 [已展開] 核取方塊。 

    e. 您可以看到具有主機名稱和 FQDN 的所有伺服器。
    
    f. 按一下 [確定]。

> [!NOTE]
> 將應用程式/網站上的 SPN 設定為適用。 在設定應用程式集區身分識別時，存取應用程式。 若要使用 FQDN 名稱存取 IIS 應用程式，請移至 Real Server 命令提示字元，並使用必要參數輸入 SetSpn。 例如，Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp LoadMaster Azure AD 整合 KCD (Kerberos 委派帳戶) 

移至 Kemp LoadMaster Azure AD 整合 Web 主控台 > 虛擬服務 > 管理 SSO。

![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-12.png)

a. 瀏覽至 [伺服器端單一登入組態]。

b. 在 [新增新的伺服器端組態] 中輸入名稱，然後按一下 [新增]。

c. 在驗證通訊協定中選取 [Kerberos 限制委派]。

d. 在 Kerberos 領域中輸入網域名稱。

e. 按一下 [設定 Kerberos 領域]。

f. 在 Kerberos 金鑰分派中心中，中輸入網域控制站 IP 位址。

g. 按一下 [設定 Kerberos KDC]。

h. 在 [受 Kerberos 信任的使用者名稱] 中，輸入 KCD 的使用者名稱。

i. 按一下 [設定受 KDC 信任的使用者名稱]。

j. 在 [受 Kerberos 信任的使用者密碼] 中輸入密碼。

k. 按一下 [設定受 KDC 信任的使用者密碼]。

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Azure AD 整合 ESP 

移至虛擬服務 > 檢視/修改服務。

![Kemp LoadMaster Azure AD 整合 Web 伺服器](./media/kemp-tutorial/kemp-13.png)

a. 按一下 [修改虛擬服務暱稱]。
    
b. 按一下 [ESP 選項]。
    
c. 在伺服器驗證模式下，選取 KCD。
        
d. 在 [伺服器端組態] 下，選取先前建立的伺服器端設定檔。

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>建立 Kemp LoadMaster Azure AD 整合測試使用者

在本節中，您會在 Kemp LoadMaster Azure AD 整合中建立名為 B.Simon 的使用者。 請與[Kemp LoadMaster Azure AD 整合支援小組](mailto:support@kemp.ax)，以在 Kemp LoadMaster Azure AD 整合平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Kemp LoadMaster Azure AD 整合] 圖格時，應該會自動登入您設定 SSO 的 Kemp LoadMaster Azure AD 整合。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [透過 Azure AD 試用 Kemp LoadMaster Azure AD 整合](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見度和控制項保護 Kemp LoadMaster Azure AD 整合](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
