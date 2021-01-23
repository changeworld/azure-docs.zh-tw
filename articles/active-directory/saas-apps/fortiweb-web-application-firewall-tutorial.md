---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 FortiWeb Web Application Firewall 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 FortiWeb Web Application Firewall 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 FortiWeb Web Application Firewall 整合

在本教學課程中，您將了解如何整合 FortiWeb Web Application Firewall 與 Azure Active Directory (Azure AD)。 整合 FortiWeb Web Application Firewall 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 FortiWeb Web Application Firewall 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 FortiWeb Web Application Firewall。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 FortiWeb Web Application Firewall 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* FortiWeb Web Application Firewall 支援 **SP** 起始的 SSO。

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>從資源庫新增 FortiWeb Web Application Firewall

如要設定將 FortiWeb Web Application Firewall 整合到 Azure AD 中，您需要從資源庫將 FortiWeb Web Application Firewall 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **FortiWeb Web Application Firewall**。
1. 從結果面板中選取 [FortiWeb Web Application Firewall]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>設定和測試 FortiWeb Web Application Firewall 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 FortiWeb Web Application Firewall 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 FortiWeb Web Application Firewall 中相關使用者之間的連結關聯性。

若要設定及測試與 FortiWeb Web Application Firewall 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 FortiWeb Web Application Firewall SSO](#configure-fortiweb-web-application-firewall-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 FortiWeb Web Application Firewall 測試使用者](#create-fortiweb-web-application-firewall-test-user)** - 使 FortiWeb Web Application Firewall 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **FortiWeb Web Application Firewall** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

   a. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://www.<CUSTOMER_DOMAIN>.com`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://www.<CUSTOMER_DOMAIN>.com`

    d. 在 [登出 URL] 文字方塊中，以下列模式輸入 URL：`https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` 是稍後提供 FortiWeb 相關組態時，將會使用的名稱識別碼。
    > 請連絡 [FortiWeb Web Application Firewall 支援小組](mailto:support@fortinet.com)以取得實際的 URL 值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)


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

在本節中，您會將 FortiWeb Web Application Firewall 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [FortiWeb Web Application Firewall]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-fortiweb-web-application-firewall-sso"></a>設定 FortiWeb Web Application Firewall SSO

1.  瀏覽至 `https://<address>:8443`，其中 `<address>` 是指派給 FortiWeb VM 的 FQDN 或公用 IP 位址。

2.  使用 FortiWeb VM 部署期間所提供的管理員認證來登入。

1. 在下列頁面上，執行下列步驟。

    ![SAML 伺服器頁面](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  在左側功能表中，按一下 [使用者]。

    b.  在使用者底下，按一下 [遠端伺服器]。

    c.  按一下 [SAML 伺服器]。

    d.  按一下 [建立新的]。

    e.  在 **名稱** 欄位中，提供「設定 Azure AD」區段中所使用的 `<fwName>` 值。

    f.  在 [實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    g. 在 **中繼資料** 旁按一下 [選擇檔案]，並選取從 Azure 入口網站下載的 **同盟中繼資料 XML** 檔案。

    h.  按一下 [確定]。

### <a name="create-a-site-publishing-rule"></a>建立網站發佈規則

1.  瀏覽至 `https://<address>:8443`，其中 `<address>` 是指派給 FortiWeb VM 的 FQDN 或公用 IP 位址。

1.  使用 FortiWeb VM 部署期間所提供的管理員認證來登入。
1. 在下列頁面上，執行下列步驟。

    ![網站發佈規則](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  在左側功能表中，按一下 [應用程式傳遞]。
    
    b.  在 **應用程式傳遞** 下，按一下 [網站發佈]。
    
    c.  在 **網站發佈** 下，按一下 [網站發佈]。
    
    d.  按一下 [網站發佈規則]。
    
    e.  按一下 [建立新的]。
    
    f.  提供網站發佈規則的名稱。
    
    g.  在 **發行的網站類型** 旁，按一下 [規則運算式]。
    
    i.  在 **發行的網站** 旁提供字串，以符合您要發佈之網站的主機標頭。
    
    j.  在 **路徑** 旁，提供 /。
    
    k.  在 **用戶端驗證方法** 旁，選取 [SAML 驗證]。
    
    l.  在 **SAML 伺服器** 下拉式選單中，選取您稍早建立的 SAML 伺服器。
    
    m.  按一下 [確定]。


### <a name="create-a-site-publishing-policy"></a>建立網站發佈原則

1.  瀏覽至 `https://<address>:8443`，其中 `<address>` 是指派給 FortiWeb VM 的 FQDN 或公用 IP 位址。

2.  使用 FortiWeb VM 部署期間所提供的管理員認證來登入。

1. 在下列頁面上，執行下列步驟。

    ![網站發佈原則](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  在左側功能表中，按一下 [應用程式傳遞]。

    b.  在 **應用程式傳遞** 下，按一下 [網站發佈]。

    c.  在 **網站發佈** 下，按一下 [網站發佈]。

    d.  按一下 [網站發佈原則]。

    e.  按一下 [建立新的]。

    f.  提供網站發佈原則的名稱。

    g.  按一下 [確定]。

    h.  按一下 [建立新的]。

    i.  在 **規則** 下拉式選單中，選取您稍早建立的網站發佈規則。

    j.  按一下 [確定]。

### <a name="create-and-assign-a-web-protection-profile"></a>建立及指派 Web 保護設定檔

1.  瀏覽至 `https://<address>:8443`，其中 `<address>` 是指派給 FortiWeb VM 的 FQDN 或公用 IP 位址。

2.  使用 FortiWeb VM 部署期間所提供的管理員認證來登入。
3.  在左側功能表中，按一下 [原則]。
4.  在 [原則] 下，按一下 [Web 保護設定檔]。
5.  按一下 [內嵌標準保護]，然後按一下 [複製]。
6.  提供新 Web 保護設定檔的名稱，然後按一下 [確定]。
7.  選取新的 Web 保護設定檔，然後按一下 [編輯]。
8.  在 **網站發佈** 旁，選取您稍早建立的網站發佈原則。
9.  按一下 [確定]。
 
    ![網站發佈](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. 在左側功能表中，按一下 [原則]。
11. 在 [原則] 下，按一下 [伺服器原則]。
12. 選取用來發佈您要使用 Azure Active Directory 進行驗證之網站的伺服器原則。
13. 按一下 **[編輯]** 。
14. 在 **Web 保護設定檔** 下拉式選單中，選取您剛建立的 Web 保護設定檔。
15. 按一下 [確定]。
16. 嘗試存取 FortiWeb 發佈網站的外部 URL。 系統應該會將您重新導向至使用 Azure Active Directory 驗證。


### <a name="create-fortiweb-web-application-firewall-test-user"></a>建立 FortiWeb Web Application Firewall 測試使用者

在本節中，您會在 FortiWeb Web Application Firewall 中建立名為 Britta Simon 的使用者。 請與 [FortiWeb Web Application Firewall 支援小組](mailto:support@fortinet.com)合作，在 FortiWeb Web Application Firewall 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 FortiWeb Web Application Firewall 登入 URL。 

* 直接移至 FortiWeb Web Application Firewall 登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 按一下我的應用程式中的 FortiWeb Web Application Firewall 圖格時，系統會將您重新導向至 FortiWeb Web Application Firewall 登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 FortiWeb Web Application Firewall 後，您可以強制執行工作階段控制，以即時防止組織的敏感性資料遭到外洩及滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。