---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與受 F5 BIG-IP APM 保護的 Oracle PeopleSoft 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: a8d34d60cfb4037192abcc20649b478e9f58a6bb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992108"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與受 F5 BIG-IP APM 保護的 Oracle PeopleSoft 整合

在本教學課程中，您將了解如何整合「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」與 Azure Active Directory (Azure AD)。 在整合受 F5 BIG-IP APM 保護的 Oracle PeopleSoft 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」。
* 在 Azure 入口網站集中管理您的帳戶。
* 本教學課程涵蓋 Oracle PeopleSoft ELM 的指示。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

1. Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
1. 已啟用「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」單一登入 (SSO) 的訂用帳戶。

1. 要部署聯合解決方案，必須具備下列授權：

    1. F5 BIG-IP® Best 套件組合 (或) 
    2. F5 BIG-IP Access Policy Manager™ (APM) 獨立授權 
    3. 現有 BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) 上的 F5 BIG-IP Access Policy Manager™ (APM) 附加元件授權。
    4. 除了上述授權以外，F5 系統也可以使用下列授權： 
        * URL 篩選訂用帳戶，可供使用 URL 類別資料庫 
        * F5 IP 智慧訂用帳戶，用來偵測及封鎖已知的攻擊者和惡意流量 
        * 網路硬體安全模組 (HSM)，用來保護和管理增強式驗證的數位金鑰
1. 在 F5 BIG-IP 系統中會佈建 APM 模組 (LTM 是選擇性的) 
1. 雖然在[同步/容錯移轉裝置群組](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) 中部署 F5 系統並非必要作業，但強烈建議您這麼做，因為其中包含作用中待命配對，且具有用於高可用性 (HA) 的浮動 IP 位址。 使用連結彙總控制通訊協定 (LACP) 可達成進一步的介面備援。 LACP 會個別管理連線的實體介面 

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* 「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>從資源庫新增受 F5 BIG-IP APM 保護的 Oracle PeopleSoft

若要進行將「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」整合到 Azure AD 中的設定，您必須從資源庫將「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入**受 F5 BIG-IP APM 保護的 Oracle PeopleSoft**。
1. 從結果面板中選取 [受 F5 BIG-IP APM 保護的 Oracle PeopleSoft]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>設定和測試「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」中相關使用者之間的連結關聯性。

若要設定及測試與「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定受 F5 BIG-IP APM 保護的 Oracle PeopleSoft SSO](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立受 F5 BIG-IP APM 保護的 Oracle PeopleSoft 測試使用者](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** - 使「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [受 F5 BIG-IP APM 保護的 Oracle PeopleSoft] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<FQDN>.peoplesoft.f5.com`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. 在 [登出 URL] 文字方塊中，以下列模式輸入 URL：`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >這些都不是真正的值。 使用實際的登入 URL、識別碼、回覆 URL 和登出 URL 來更新這些值。 請連絡[受 F5 BIG-IP APM 保護的 Oracle PeopleSoft 用戶端支援小組](https://support.f5.com)以取得值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。
    
    | 名稱 |  來源屬性|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，下載 [同盟中繼資料 XML] 和 [憑證 (Base64)]，並將其儲存在您的電腦上。

    ![憑證下載連結](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

在本節中，您會將「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [受 F5 BIG-IP APM 保護的 Oracle PeopleSoft]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>設定受 F5 BIG-IP APM 保護的 Oracle PeopleSoft SSO

### <a name="f5-saml-sp-configuration"></a>F5 SAML SP 設定

將中繼資料憑證匯入至稍後將在設定程序中使用的 F5。 瀏覽至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]  。 從右下角選取 [匯入]  。

![F5 SAML SP 設定](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>設定 SAML IDP 連接器 

1. 瀏覽至 [存取] > [同盟] > [SAML：服務提供者] > [外部 Idp 連接器]，然後按一下 [建立] > [從中繼資料]。

    ![F5 SAML IDP 連接器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. 在下列頁面中，按一下 [瀏覽] 以上傳 xml 檔案。

1. 在 [識別提供者名稱] 文字方塊中提供有效名稱，然後按一下 [確定]。

    ![新增 SAML IDP 連接器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. 在 [安全性設定] 索引標籤中執行必要的步驟，然後按一下 [確定]。

    ![編輯 SAML IDP 連接器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>設定 SAML SP

1. 瀏覽至 [存取] > [同盟] > [SAML 服務提供者] > [本機 SP 服務]，然後按一下 [建立]。 完成下列資訊，然後按一下 [確定]。

    * 名稱：`<Name>`
    * 實體識別碼：`https://<FQDN>.peoplesoft.f5.com`
    * SP 名稱設定
        * 配置：`https`
        * 主機：`<FQDN>.peoplesoft.f5.com`
        * 說明：`<Description>`

    ![新增 SAML SP 服務](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. 選取 SP 設定 PeopleSoftAppSSO，然後按一下 [繫結/解除繫結 IdP 連接器]。
按一下 [新增資料列]，並選取在先前的步驟中建立的 [外部 IdP 連接器]，按一下 [更新]，然後按一下 [確定]。

    ![建立 SAML SP 服務](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>設定應用程式

### <a name="create-a-new-pool"></a>建立新的集區
1. 瀏覽至 [本機流量] > [集區] > [集區清單]，按一下 [建立]，並在完成下列資訊後按一下 [完成]。

    * 名稱：`<Name>`
    * 說明：`<Description>`
    * 健康情況監視：`http`
    * 位址：`<Address>`
    * 服務連接埠：`<Service Port>`

    ![建立新集區](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>建立新的用戶端 SSL 設定檔

瀏覽至 [本機流量] > [設定檔] > [SSL] > [用戶端] > [+]，並在完成下列資訊後按一下 [完成]。

* 名稱：`<Name>`
* 憑證：`<Certificate>`
* 索引鍵︰`<Key>`

![新增用戶端 SSL 設定檔](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>建立新的虛擬伺服器

1. 瀏覽至 [本機流量] > [虛擬伺服器] > [虛擬伺服器清單] > [+]，並在完成下列資訊後按一下 [完成]。
    * 名稱：`<Name>`
    * 目的地位址/遮罩：`<Address>`
    * 服務連接埠：連接埠 443 HTTPS
    * HTTP 設定檔 (用戶端)：HTTP

    ![建立新的虛擬伺服器](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. 在下方的頁面中填入下列值：

    * SSL 設定檔 (用戶端)：`<SSL Profile>`
    * 來源位址轉譯：自動對應
    * 存取設定檔：`<Access Profile>`
    * 預設集區：`<Pool>`


    ![建立新的虛擬伺服器 peoplesoft 集區](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>設定 PeopleSoft 應用程式以支援將 F5 Big-IP APM 作為單一登入解決方案

>[!Note]
> 參考 https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. 使用管理員認證登入 Peoplesoft 主控台 `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` (範例：PS/PS)

    ![管理員自助服務](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. 在 PeopleSoft 應用程式中，建立 **OAMPSFT** 作為新的使用者設定檔，並建立低安全性角色 (例如 **PeopleSoft 使用者** 的關聯)。
瀏覽至 [Peopletools] > [安全性] > [使用者設定檔] > [使用者設定檔]，以建立新的使用者設定檔 (例如：**OAMPSFT**)，然後新增 [Peoplesoft 使用者]。

    ![Peoplesoft 使用者](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. 存取 Web 設定檔，並輸入 **OAMPSFT** 作為公用存取**使用者識別碼**。

    ![使用者設定檔](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. 從 [PeopleTools 應用程式設計工具] 開啟 **FUNCLIB_LDAP** 記錄。

    ![Web 設定檔設定](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. 使用 **OAMSSO_AUTHENTICATION** 函式的 **PS_SSO_UID** 更新使用者標頭。
在 **getWWWAuthConfig()** 函式中，將指派給 **&defaultUserId** 的值取代為我們在 Web 設定檔中定義的 **OAMPSFT**。 儲存記錄定義。

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. 存取 [登入 PeopleCode] 頁面 (PeopleTools、安全性、安全性物件、登入 PeopleCode)，並啟用 **OAMSSO_AUTHENTICATION** 函式 — 用於 Oracle Access Manager 單一登入的登錄 PeopleCode。

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>設定 F5 Big IP APM，以在 "PS_SSO_UID" HTTP 標頭中填入 PeopleSoft 使用者識別碼

### <a name="configuring-per-request-policy"></a>設定每個要求的原則
1. 瀏覽至 [存取] > [設定檔/原則] > [每個要求的原則]，按一下 [建立]，並在完成下列資訊後按一下 [完成]。

    * 名稱：`<Name>`
    * 設定檔類型：全部
    * 語言：`<Language>`

    ![設定每個要求的原則 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. 按一下 [編輯] 以編輯每個要求的原則 `<Name>` ![編輯每個要求原則 PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>將每個要求的原則指派給虛擬伺服器

瀏覽至 [本機流量] > [虛擬伺服器] > [虛擬伺服器清單] > [PeopleSoftApp]，並將 `<Name>` 指定為每個要求的原則

![以 PeopleSoftApp 作為每個要求的原則 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>設定 F5 Big IP APM 以支援 PeopleSoft 應用程式的單一登出

若要為所有 PeopleSoft 使用者新增單一登出支援，請依照下列步驟操作：

1. 確認 PeopleSoft 入口網站的正確登出 URL
    * 若要確認 PeopleSoft 應用程式用來結束使用者工作階段的位址，您必須使用網頁瀏覽器開啟入口網站，並啟用瀏覽器偵錯工具，如下列範例所示：

        ![PeopleSoft 入口網站的登出 URL ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * 尋找具有 `PT_LOGOUT_MENU` 識別碼的元素，並使用查詢參數儲存 URL 路徑。 在此處的範例中，我們取得了下列值：`/psp/ps/?cmd=logout`

1. 建立會將使用者重新導向至 APM 登出 URL 的 LTM iRule：`/my.logout.php3`

    * 瀏覽至 [本機流量] > [iRule]，按一下 [建立]，並在完成下列資訊後按一下 [完成]。

        名稱：`<Name>`  
        定義：  
                    _when HTTP_REQUEST { switch -glob -- [HTTP::uri] { `/psp/ps/?cmd=logout` { HTTP::redirect `/my.logout.php3` } } }_

1. 將建立的 iRule 指派給虛擬伺服器

    * 瀏覽至 [本機流量] > [虛擬伺服器] > [虛擬伺服器清單] > [PeopleSoftApp] > [資源]。 按一下 [管理...] 按鈕：   

    * 將 `<Name>` 指定為已啟用的 iRule，然後按一下 [完成]

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * 將 [名稱] 文字方塊中的值指定為 `<Name>`。 

        ![_iRule_PeopleSoftApp 已完成](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>建立受 F5 BIG-IP APM 保護的 Oracle PeopleSoft 測試使用者

在本節中，您會在「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」中建立名為 B.Simon 的使用者。 請與[受 F5 BIG-IP APM 保護的 Oracle PeopleSoft 支援小組](https://support.f5.com)合作，在「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」登入 URL。  

* 直接移至「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」登入 URL，然後從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」 

您也可以使用 Microsoft 存取面板，以任何模式測試應用程式。 當您按一下存取面板中的「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」圖格時，如果是在 SP 模式中設定，您會重新導向至 [應用程式登入] 頁面來起始登入流程，如果是在 IDP 模式中設定，則應該會自動登入已設定 SSO 的「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定「受 F5 BIG-IP APM 保護的 Oracle PeopleSoft」後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
