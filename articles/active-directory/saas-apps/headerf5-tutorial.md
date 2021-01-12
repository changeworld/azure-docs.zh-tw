---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 F5 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 F5 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 6bf21205c028eaff08eef91757424f8b52764ada
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936136"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>教學課程：設定 Azure Active Directory 與 F5 之間的單一登入 (SSO)

在本教學課程中，您會了解如何整合 F5 與 Azure Active Directory (Azure AD)。 在整合 F5 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 F5 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 F5。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 中單一登入的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

* 已啟用 F5 單一登入 (SSO) 的訂用帳戶。

* 要部署聯合解決方案，必須具備下列授權：

    * F5 BIG-IP® Best 套件組合 (或) 

    * F5 BIG-IP Access Policy Manager™ (APM) 獨立授權 

    * 現有 BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) 上的 F5 BIG-IP Access Policy Manager™ (APM) 附加元件授權。

    * 除了上述授權以外，F5 系統也可以使用下列授權： 

        * URL 篩選訂用帳戶，可供使用 URL 類別資料庫

        * F5 IP 智慧訂用帳戶，用來偵測及封鎖已知的攻擊者和惡意流量
     
        * 網路硬體安全模組 (HSM)，用來保護和管理增強式驗證的數位金鑰

* 在 F5 BIG-IP 系統中會佈建 APM 模組 (LTM 是選擇性的)

* 雖然在[同步/容錯移轉裝置群組](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) 中部署 F5 系統並非必要作業，但強烈建議您這麼做，因為其中包含作用中待命配對，且具有用於高可用性 (HA) 的浮動 IP 位址。 使用連結彙總控制通訊協定 (LACP) 可達成進一步的介面備援。 LACP 會將連線的實體介面當作單一虛擬介面 (彙總群組) 來管理，並偵測群組內的任何介面失敗。

* 針對 Kerberos 應用程式，需要用於限制委派的內部部署 AD 服務帳戶。  請參閱 [F5 文件](https://support.f5.com/csp/article/K43063049)以建立 AD 委派帳戶。

## <a name="access-guided-configuration"></a>存取引導式設定

* F5 TMOS version 13.1.0.8 版和更新版本支援存取引導式設定。 如果您的 BIG-IP 系統執行低於 13.1.0.8 的版本，請參閱 **進階設定** 一節。

* 存取引導式設定提供全新且簡化的使用者體驗。 這個以工作流程為基礎的架構提供了針對選取的拓撲而設計的直覺、可重新進入的設定步驟。

* 繼續進行設定之前，請先從 [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748) 下載最新的使用案例套件，以升級引導式設定。 若要升級，請遵循下列程序。

    >[!NOTE]
    >下列螢幕擷取畫面顯示的是最新發行的版本 (具有 AGC 5.0 版的 BIG-IP 15.0)。 以下設定步驟適用於 13.1.0.8 到最新 BIG-IP 版本的這個使用案例。

1. 在 F5 BIG-IP Web UI 上，按一下 [存取] >> [引導式設定]  。

1. 在 [引導式設定]  頁面上，按一下左上角的 [升級引導式設定]  。

    ![顯示 [引導式設定] 頁面的螢幕擷取畫面，其中具有「更新引導式設定」連結。](./media/headerf5-tutorial/configure14.png) 

1. 在 [升級引導式設定] 快顯畫面中，選取 [選擇檔案]  以上傳已下載的使用案例套件，然後按一下 [上傳並安裝]  按鈕。

    ![顯示 [升級引導式設定] 對話方塊的螢幕擷取畫面，其中已選取 [選擇檔案]。](./media/headerf5-tutorial/configure15.png) 

1. 在升級完成後，按一下 [繼續]  按鈕。

    ![顯示 [升級引導式設定] 對話方塊的螢幕擷取畫面，其中具有完成訊息。](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* 您可以使用三種不同的方式設定 F5 SSO。

- [設定適用於標頭式應用程式的 F5 單一登入](#configure-f5-single-sign-on-for-header-based-application)

- [設定適用於 Kerberos 應用程式的 F5 單一登入](kerbf5-tutorial.md)

- [設定適用於進階 Kerberos 應用程式的 F5 單一登入](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>主要驗證案例

* 除了對 Open ID Connect、SAML 和 WS-Fed 等新式驗證通訊協定的 Azure Active Directory 原生整合支援外，F5 也針對 Azure AD 的內部和外部存取，擴充了舊版驗證應用程式的安全存取，而支援對這些應用程式的新式案例 (例如，不需要密碼的存取)。 其中包括：

* 標頭型驗證應用程式

* Kerberos 驗證應用程式

* 匿名驗證或沒有內建驗證的應用程式

* NTLM 驗證應用程式 (對使用者提出雙重提示的保護)

* 表單式驗證應用程式 (對使用者提出雙重提示的保護)

## <a name="adding-f5-from-the-gallery"></a>從資源庫新增 F5

若要設定將 F5 整合到 Azure AD 中，您需要從資源庫將 F5 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **F5**。
1. 從結果面板選取 [F5]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>設定及測試 F5 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 F5 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 F5 中相關使用者之間的連結關聯性。

若要設定及測試與 F5 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 F5 SSO](#configure-f5-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 F5 測試使用者](#create-f5-test-user)** - 使 F5 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [F5]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<YourCustomFQDN>.f5.com/`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<YourCustomFQDN>.f5.com/`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [F5 用戶端支援小組](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  和 [憑證 (Base64)]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 F5]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 F5 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取[F5]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。
1. 按一下 [條件式存取]  。
1. 按一下 [新增原則]  。
1. 現在，您可以看到您的 F5 應用程式已成為條件式存取原則的資源，並套用任何條件式存取，包括多重要素驗證、裝置型存取控制或身分識別保護原則。

## <a name="configure-f5-sso"></a>設定 F5 SSO

- [設定適用於 Kerberos 應用程式的 F5 單一登入](kerbf5-tutorial.md)

- [設定適用於進階 Kerberos 應用程式的 F5 單一登入](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>設定適用於標頭式應用程式的 F5 單一登入

### <a name="guided-configuration"></a>引導式設定

1. 開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 F5 (標頭式) 公司網站，然後執行下列步驟：

1. 瀏覽至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]  。 從右下角選取 [匯入]  。 指定 [憑證名稱]  (稍後將在設定中參考)。 在 [憑證來源]  中，選取 [上傳檔案]，指定在設定 SAML 單一登入時從 Azure 下載的憑證。 按一下 [匯入]  。

    ![顯示 [SSL 憑證清單] 的螢幕擷取畫面，您可以在其中選取 [憑證名稱] 和 [憑證來源]。](./media/headerf5-tutorial/configure12.png)
 
1. 此外，您將需要 **應用程式主機名稱的 SSL 憑證。瀏覽至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]** 。 從右下角選取 [匯入]  。 [匯入類型]  將是 [PKCS 12(IIS)]  。 指定 [金鑰名稱]  (稍後將在設定中參考)，並指定 PFX 檔案。 指定 PFX 的 [密碼]  。 按一下 [匯入]  。

    >[!NOTE]
    >在此範例中，我們的應用程式名稱為 `Headerapp.superdemo.live`，並使用萬用字元憑證，金鑰名稱則為 `WildCard-SuperDemo.live`。

    ![顯示 SSL 憑證/金鑰來源頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure13.png)

1. 我們將使用引導式體驗來設定 Azure AD 同盟和應用程式存取。 移至 – F5 BIG-IP [主要]  ，然後選取 [存取] > [引導式設定] > [同盟] > [SAML 服務提供者]  。 按 [下一步]  ，然後按 [下一步]  開始設定。

    ![顯示 [引導式設定] 頁面的螢幕擷取畫面，其中已選取 [同盟]。](./media/headerf5-tutorial/configure01.png)

    ![顯示 [SAML 服務提供者] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure02.png)
 
1. 提供 [設定名稱]  。 指定 [實體識別碼]  (與您在 Azure AD 應用程式設定中設定的識別碼相同)。 指定 [主機名稱]  。 新增參考的 [描述]  。 接受其餘的預設項目，然後選取並按一下 [儲存並繼續下一步]  。

    ![顯示 [服務提供者屬性] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure03.png) 

1. 在此範例中，我們會建立新的虛擬伺服器 192.168.30.20，並使用連接埠 443。 在 [目的地位址]  中，指定虛擬伺服器 IP 位址。 選取 [用戶端 SSL 設定檔]  ，然後選取 [新建]。 指定先前上傳的應用程式憑證 (在此範例中為萬用字元憑證) 和相關聯的金鑰，然後按一下 [儲存並繼續下一步]  。

    >[!NOTE]
    >在此範例中，我們的內部 Web 伺服器會在連接埠 888 上執行，而我們想要使用 443 加以發佈。

    ![顯示 [虛擬伺服器屬性] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure04.png) 

1. 在 [選取設定 IdP 連接器的方法]  底下指定中繼資料，按一下 [選擇檔案]，然後上傳先前從 Azure AD 下載的中繼資料 XML 檔案。 為 SAML IDP 連接器指定唯一的 **名稱**。 選擇先前上傳的 [中繼資料簽署憑證]  。 按一下 [儲存並繼續下一步]  。

    ![顯示 [外部識別提供者連接器設定] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure05.png)
 
1. 在 [選取集區]  中，指定 [新建]  (或選取已存在的集區)。 讓其他值保留為預設值。 在 [集區伺服器] 下的 [IP 位址/節點名稱]  底下，輸入 IP 位址。 指定 [連接埠]  。 按一下 [儲存並繼續下一步]  。

    ![顯示 [集區屬性] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure06.png)

1. 在 [單一登入設定] 畫面上，選取 [啟用單一登入]  。 在 [選取的單一登入類型] 底下，選擇 [HTTP 標頭式]  。 在 [使用者名稱來源] 底下，將 **session.saml.last.Identity** 取代為 **session.saml.last.attr.name.Identity** (此變數會使用 Azure AD 中的宣告對應來設定)。 在 [SSO 標頭] 底下：

    * **HeaderName：MyAuthorization**

    * **標頭值：%{session.saml.last.attr.name.Identity}**

    * 按一下 [儲存並繼續下一步]

    如需變數和值的完整清單，請參閱附錄。 您可以視需要新增更多標頭。

    >[!NOTE]
    >帳戶名稱是已建立的 F5 委派帳戶 (請查看 F5 文件)。

    ![顯示 [單一登入設定] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure07.png) 

1. 基於本指引的目的，我們將略過端點檢查。  請參閱 F5 文件以取得詳細資訊。 選取 [儲存並繼續下一步]  。

    ![顯示 [端點檢查屬性] 頁面的。](./media/headerf5-tutorial/configure08.png)

1. 接受預設值，然後按一下 [儲存並繼續下一步]  。 如需關於 SAML 工作階段管理設定的詳細資訊，請參閱 F5 文件。

    ![顯示 [逾時設定] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure09.png)

1. 檢閱摘要畫面，然後選取 [部署]  以設定 BIG-IP。 按一下 [完成]  。

    ![顯示您的應用程式已準備就緒進入部署頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure10.png)

    ![顯示您的應用程式已部署頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>進階設定

如果您無法使用引導式設定，或想要新增/修改其他參數，您可以參考本節。 您將需要應用程式主機名稱的 TLS/SSL 憑證。

1. 瀏覽至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]  。 從右下角選取 [匯入]  。 [匯入類型]  將是 [PKCS 12(IIS)]  。 指定 [金鑰名稱]  (稍後將在設定中參考)，並指定 PFX 檔案。 指定 PFX 的 [密碼]  。 按一下 [匯入]  。

    >[!NOTE]
    >在此範例中，我們的應用程式名稱為 `Headerapp.superdemo.live`，並使用萬用字元憑證，金鑰名稱則為 `WildCard-SuperDemo.live`。
  
    ![顯示 SSL 憑證/金鑰來源頁面進階設定的螢幕擷取畫面。](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>將新的 Web 伺服器新增至 BigIP-F5

1. 按一下 [主要] > [IApps] > [應用程式服務] > [應用程式] > [建立]  。

1. 提供 [名稱]  ，並在 [範本]  底下選擇 [f5.http]  。
 
    ![顯示具有範本選擇的 [應用程式服務] 頁面螢幕擷取畫面。](./media/headerf5-tutorial/configure18.png)

1. 在此案例中，我們會在外部將 HeaderApp2 發佈為 HTTPS，而在 [BIG-IP 系統如何處理 SSL 流量？]  中，我們指定 [從用戶端終止 SSL，以純文字傳送到伺服器 (SSL 卸載)]  。 在 **您要使用哪個 SSL 憑證？** 和 **您要使用哪個 SSL 私密金鑰？** 下，指定您的憑證和金鑰。 在 [您要將哪個 IP 位址用於虛擬伺服器？]  底下，指定虛擬伺服器 IP。 

    * **指定其他詳細資料**

        * FQDN  

        * 指定現有的應用程式集區，或建立一個新的。

        * 如果建立新的應用程式伺服器，請指定 **內部 IP 位址** 和 **連接埠號碼**。

        ![顯示窗格的螢幕擷取畫面，您可以在其中指定這些詳細資料。](./media/headerf5-tutorial/configure19.png) 

1. 按一下 [完成]  。

    ![顯示頁面完成之後的螢幕擷取畫面。](./media/headerf5-tutorial/configure20.png) 

1. 確定應用程式屬性可以修改。 按一下 **[主要] > [IApps] > [應用程式服務：應用程式] >> [HeaderApp2]** 。 取消核取 [嚴格更新]  (我們將修改 GUI 以外的部分設定)。 按一下 [更新]  按鈕。

    ![顯示 [應用程式服務] 頁面的螢幕擷取畫面，其中已選取 [屬性] 索引標籤。](./media/headerf5-tutorial/configure21.png) 

1. 此時，您應該能夠瀏覽虛擬伺服器。

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>將 F5 設定為 SP，並將 Azure 設為 IDP

1.  按一下 [存取] > [同盟] > [SAML 服務提供者] > [本機 SP 服務]  ，然後按一下 [建立] 或 + 號。

    ![顯示 [關於此 BIG IP] 頁面的螢幕擷取畫面。 ](./media/headerf5-tutorial/configure22.png)

1. 指定服務提供者服務的詳細資料。 指定代表 F5 SP 設定的 [名稱]  。 指定 [實體識別碼]  (通常與應用程式 URL 相同)。

    ![顯示 [SAML 服務提供者] 頁面的螢幕擷取畫面，其中具有 [建立新的 SAML SP 服務] 對話方塊。](./media/headerf5-tutorial/configure23.png)

    ![顯示 [建立新的 SAML SP 服務] 對話方塊的螢幕擷取畫面，其中已選取 [端點設定]。](./media/headerf5-tutorial/configure24.png)

    ![顯示 [建立新的 SAML SP 服務] 對話方塊的螢幕擷取畫面，其中已選取 [安全性設定]。](./media/headerf5-tutorial/configure25.png)

    ![顯示 [建立新的 SAML SP 服務] 對話方塊的螢幕擷取畫面，其中已選取 [驗證內容]。](./media/headerf5-tutorial/configure26.png)

    ![顯示 [建立新的 SAML SP 服務] 對話方塊的螢幕擷取畫面，其中已選取 [要求的屬性]。](./media/headerf5-tutorial/configure27.png)

    ![顯示 [編輯 SAML SP 服務] 對話方塊的螢幕擷取畫面，其中已選取 [進階設定]。](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>建立 IdP 連接器

1. 按一下 [繫結/解除繫結 IdP 連接器]  按鈕，選取 [建立新的 IdP 連接器]  ，並選擇 [從中繼資料]  選項，然後執行下列步驟：
 
    ![顯示使用此 SP 對話方塊的 [編輯 SAML IdP] 螢幕擷取畫面，其中已選取 [建立新的 IdP 連接器]。](./media/headerf5-tutorial/configure29.png)

    a. 瀏覽至從 Azure AD 下載的中繼資料 metadata.xml 檔案，並指定 [識別提供者名稱]  。

    b. 按一下 [確定]  。

    c. 系統會建立連接器，並自動從中繼資料 xml 檔案備妥憑證。
    
    ![顯示 [建立新的 SAML IdP 連接器] 對話方塊的螢幕擷取畫面。](./media/headerf5-tutorial/configure30.png)

    d. 設定 F5BIG-IP，以將所有要求傳送至 Azure AD。

    e. 按一下 [新增資料列]  ，選擇 [AzureIDP]  (如先前的步驟中所建立)，指定 

    f. **相符的來源  =  %{session.server.landinguri}** 

    g. **相符的值     = /** _

    h. 按一下 [更新]*

    i. 按一下 [檔案] &gt; [新增] &gt; [專案]

    j. **SAML IDP 設定完成**
    
    ![顯示使用此 SP 對話方塊的 [編輯 SAML IdP] 螢幕擷取畫面。](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>設定 F5 原則以將使用者重新導向至 Azure SAML IDP

1. 若要設定 F5 原則以將使用者重新導向至 Azure SAML IDP，請執行下列步驟：

    a. 按一下 [主要] > [存取] > [設定檔/原則] > [存取設定檔]  。

    b. 按一下 [建立]  按鈕。

    ![顯示 [存取設定檔] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure32.png)
 
    c. 指定 [名稱]  (在此範例中為 HeaderAppAzureSAMLPolicy)。

    d. 您可以自訂其他設定，請參閱 F5 文件。

    ![顯示 [一般屬性] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure33.png)

    ![顯示 [一般屬性] 頁面的螢幕擷取畫面 (續)。](./media/headerf5-tutorial/configure34.png) 

    e. 按一下 [完成]  。

    f. 在原則建立完成後，按一下 [原則] 並移至 [存取原則]  索引標籤。

    ![顯示具有 [一般屬性] 的 [存取原則] 索引標籤螢幕擷取畫面。](./media/headerf5-tutorial/configure35.png)
 
    g. 按一下 [視覺化原則編輯器]  ，編輯 [設定檔的存取原則]  連結。

    h. 按一下 [視覺化原則編輯器] 中的 + 號，然後選擇 [SAML 驗證]  。

    ![顯示 [存取原則] 的螢幕擷取畫面。](./media/headerf5-tutorial/configure36.png)

    ![顯示搜尋對話方塊的螢幕擷取畫面，其中已選取 [SAML 驗證]。](./media/headerf5-tutorial/configure37.png)
 
    i. 按一下 [新增項目]  。

    j. 在 [屬性]  底下指定 [名稱]  ，並在 [AAA 伺服器]  底下選取先前設定的 SP，然後按一下 [儲存]  。
 
    ![顯示項目屬性的螢幕擷取畫面，包括其 AAA 伺服器。](./media/headerf5-tutorial/configure38.png)

    k. 基本原則已準備就緒，您可以自訂原則，以併入其他來源/屬性存放區。

    ![顯示自訂原則的螢幕擷取畫面。](./media/headerf5-tutorial/configure39.png)
 
    l. 確實按一下頂端的 [套用存取原則]  連結。

### <a name="apply-access-profile-to-the-virtual-server"></a>將存取設定檔套用至虛擬伺服器

1. 將存取設定檔指派給虛擬伺服器，以便讓 F5 BIG-IP APM 將設定檔設定套用至傳入的流量，並執行先前定義的存取原則。

    a. 按一下 [主要]   > [本機流量]   > [虛擬伺服器]  。

    ![顯示 [虛擬伺服器清單] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure40.png)
 
    b. 按一下虛擬伺服器，捲動至 [存取原則]  區段，然後在 [存取設定檔]  下拉式清單中，選取已建立的 SAML 原則 (在此範例中為 HeaderAppAzureSAMLPolicy)

    c. 按一下 [更新]
 
    ![顯示 [存取原則] 窗格的螢幕擷取畫面。](./media/headerf5-tutorial/configure41.png)

    d. 建立 F5 BIG-IP iRule®，以從傳入的判斷提示中擷取自訂 SAML 屬性，並將其作為 HTTP 標頭傳至後端測試應用程式。 按一下 [主要] > [本機流量] > [iRules] > [iRule 清單]，然後按一下 [建立]

    ![顯示 [本機流量 iRule 清單] 的螢幕擷取畫面。](./media/headerf5-tutorial/configure42.png)
 
    e. 將以下 F5 BIG-IP iRule 文字貼到 [定義] 視窗中。

    ![顯示 [新增 iRule] 頁面的螢幕擷取畫面。](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **範例輸出如下**

    ![顯示範例輸出的螢幕擷取畫面。](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>建立 F5 測試使用者

在本節中，您會在 F5 中建立名為 B.Simon 的使用者。 請與 [F5 客戶支援小組](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)合作，在 F5 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [F5] 圖格時，應該會自動登入您已設定 SSO 的 F5。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 F5](https://aad.portal.azure.com/)

- [設定適用於 Kerberos 應用程式的 F5 單一登入](kerbf5-tutorial.md)

- [設定適用於進階 Kerberos 應用程式的 F5 單一登入](advance-kerbf5-tutorial.md)