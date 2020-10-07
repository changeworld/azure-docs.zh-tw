---
title: 教學課程：Azure AD 單一登入與 F5 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 F5 之間的單一登入 (SSO)。
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
ms.openlocfilehash: 9db53e36dee318d39d34d26a548d1d32cbbec3b2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266016"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 F5 整合

在本教學課程中，您會了解如何整合 F5 與 Azure Active Directory (Azure AD)。 在整合 F5 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 F5 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 F5。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

* 已啟用 F5 單一登入 (SSO) 的訂用帳戶。

* 要部署聯合解決方案，必須具備下列授權：
    * F5 BIG-IP&reg; 最佳套件組合 (或)

    * F5 BIG-IP Access Policy Manager&trade; (APM) 獨立授權

    * 現有 BIG-IP F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM) 上的 F5 BIG-IP Access Policy Manager&trade; (APM) 附加元件授權。

    * 除了上述授權以外，F5 系統也可以使用下列授權：

        * URL 篩選訂用帳戶，可供使用 URL 類別資料庫

        * F5 IP 智慧訂用帳戶，用來偵測及封鎖已知的攻擊者和惡意流量

        * 網路硬體安全模組 (HSM)，用來保護和管理增強式驗證的數位金鑰

* 在 F5 BIG-IP 系統中會佈建 APM 模組 (LTM 是選擇性的)

* 雖然在[同步/容錯移轉裝置群組](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) 中部署 F5 系統並非必要作業，但強烈建議您這麼做，因為其中包含作用中待命配對，且具有用於高可用性 (HA) 的浮動 IP 位址。 使用連結彙總控制通訊協定 (LACP) 可達成進一步的介面備援。 LACP 會將連線的實體介面當作單一虛擬介面 (彙總群組) 來管理，並偵測群組內的任何介面失敗。

* 針對 Kerberos 應用程式，需要用於限制委派的內部部署 AD 服務帳戶。  請參閱 [F5 文件](https://support.f5.com/csp/article/K43063049)以建立 AD 委派帳戶。

## <a name="access-guided-configuration"></a>存取引導式設定

* F5 TMOS version 13.1.0.8 版和更新版本支援存取引導式設定。 如果您的 BIG-IP 系統執行低於 13.1.0.8 的版本，請參閱**進階設定**一節。

* 存取引導式設定提供全新且簡化的使用者體驗。 這個以工作流程為基礎的架構提供了針對選取的拓撲而設計的直覺、可重新進入的設定步驟。

* 繼續進行設定之前，請先從 [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748) 下載最新的使用案例套件，以升級引導式設定。 若要升級，請遵循下列程序。

    >[!NOTE]
    >下列螢幕擷取畫面顯示的是最新發行的版本 (具有 AGC 5.0 版的 BIG-IP 15.0)。 以下設定步驟適用於 13.1.0.8 到最新 BIG-IP 版本的這個使用案例。

1. 在 F5 BIG-IP Web UI 上，按一下 [存取] >> [引導式設定]。

2. 在 [引導式設定] 頁面上，按一下左上角的 [升級引導式設定]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure14.png) 

3. 在 [升級引導式設定] 快顯畫面中，選取 [選擇檔案] 以上傳已下載的使用案例套件，然後按一下 [上傳並安裝] 按鈕。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure15.png) 

4. 在升級完成後，按一下 [繼續] 按鈕。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* F5 支援 **SP 和 IDP** 起始的 SSO
* 您可以使用三種不同的方式設定 F5 SSO。

- [設定適用於 Kerberos 應用程式的 F5 單一登入](#configure-f5-single-sign-on-for-kerberos-application)

- [設定適用於標頭式應用程式的 F5 單一登入](headerf5-tutorial.md)

- [設定適用於進階 Kerberos 應用程式的 F5 單一登入](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>主要驗證案例

除了對 Open ID Connect、SAML 和 WS-Fed 等新式驗證通訊協定的 Azure Active Directory 原生整合支援外，F5 也針對 Azure AD 的內部和外部存取，擴充了舊版驗證應用程式的安全存取，而支援對這些應用程式的新式案例 (例如，不需要密碼的存取)。 其中包括：

* 標頭型驗證應用程式

* Kerberos 驗證應用程式

* 匿名驗證或沒有內建驗證的應用程式

* NTLM 驗證應用程式 (對使用者提出雙重提示的保護)

* 表單式驗證應用程式 (對使用者提出雙重提示的保護)

## <a name="adding-f5-from-the-gallery"></a>從資源庫新增 F5

若要設定將 F5 整合到 Azure AD 中，您需要從資源庫將 F5 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **F5**。
1. 從結果面板選取 [F5]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

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

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [F5] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<YourCustomFQDN>.f5.com/`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<YourCustomFQDN>.f5.com/`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [F5 用戶端支援小組](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，尋找 [同盟中繼資料 XML] 和 [憑證 (Base64)]，然後選取 [下載]，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 F5] 區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 F5 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取[F5]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。
1. 按一下 [條件式存取]。
1. 按一下 [新增原則]。
1. 現在，您可以看到您的 F5 應用程式已成為 CA 原則的資源，並套用任何條件式存取，包括多重要素驗證、裝置型存取控制或身分識別保護原則。

## <a name="configure-f5-sso"></a>設定 F5 SSO

- [設定適用於標頭式應用程式的 F5 單一登入](headerf5-tutorial.md)

- [設定適用於進階 Kerberos 應用程式的 F5 單一登入](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>設定適用於 Kerberos 應用程式的 F5 單一登入

### <a name="guided-configuration"></a>引導式設定

1. 開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 F5 (Kerberos) 公司網站，然後執行下列步驟：

1. 您必須將中繼資料憑證匯入至稍後將在設定程序中使用的 F5。

1. 瀏覽至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]。 從右下角選取 [匯入]。 指定 [憑證名稱] (稍後將在設定中參考)。 在 [憑證來源] 中，選取 [上傳檔案]，指定在設定 SAML 單一登入時從 Azure 下載的憑證。 按一下 [匯入] 。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure01.png) 

1. 此外，您將需要**應用程式主機名稱的 SSL 憑證。瀏覽至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]** 。 從右下角選取 [匯入]。 [匯入類型] 將是 [PKCS 12(IIS)]。 指定 [金鑰名稱] (稍後將在設定中參考)，並指定 PFX 檔案。 指定 PFX 的 [密碼]。 按一下 [匯入] 。

    >[!NOTE]
    >在此範例中，我們的應用程式名稱為 `Kerbapp.superdemo.live`，並使用萬用字元憑證，金鑰名稱則為 `WildCard-SuperDemo.live`

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure02.png) 
 
1. 我們將使用引導式體驗來設定 Azure AD 同盟和應用程式存取。 移至 – F5 BIG-IP [主要]，然後選取 [存取] > [引導式設定] > [同盟] > [SAML 服務提供者]。 按 [下一步]，然後按 [下一步] 開始設定。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure03.png) 

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure04.png)

1. 提供 [設定名稱]。 指定 [實體識別碼] (與您在 Azure AD 應用程式設定中設定的識別碼相同)。 指定 [主機名稱]。 新增參考的 [描述]。 接受其餘的預設項目，然後選取並按一下 [儲存並繼續下一步]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure05.png) 

1. 在此範例中，我們會建立新的虛擬伺服器 192.168.30.200，並使用連接埠 443。 在 [目的地位址] 中，指定虛擬伺服器 IP 位址。 選取 [用戶端 SSL 設定檔]，然後選取 [新建]。 指定先前上傳的應用程式憑證 (在此範例中為萬用字元憑證) 和相關聯的金鑰，然後按一下 [儲存並繼續下一步]。

    >[!NOTE]
    >在此範例中，我們的內部 Web 伺服器會在連接埠 80 上執行，而我們想要使用 443 加以發佈。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure06.png)

1. 在 [選取設定 IdP 連接器的方法] 底下指定中繼資料，按一下 [選擇檔案]，然後上傳先前從 Azure AD 下載的中繼資料 XML 檔案。 為 SAML IDP 連接器指定唯一的**名稱**。 選擇先前上傳的 [中繼資料簽署憑證]。 按一下 [儲存並繼續下一步]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure07.png)  

1. 在 [選取集區] 中，指定 [新建] (或選取已存在的集區)。 讓其他值保留為預設值。    在 [集區伺服器] 下的 [IP 位址/節點名稱] 底下，輸入 IP 位址。 指定 [連接埠]。 按一下 [儲存並繼續下一步]。
 
    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure08.png)

1. 在 [單一登入設定] 畫面上，選取 [啟用單一登入]。 在 [選取的單一登入類型] 底下，選擇 [Kerberos]。 在 [使用者名稱來源] 底下，將 **session.saml.last.Identity** 取代為 **session.saml.last.attr.name.Identity** (此變數會使用 Azure AD 中的宣告對應來設定)。 選取 [顯示進階設定]。 在 [Kerberos 領域] 底下，輸入 [網域名稱]。 在 [帳戶名稱/帳戶密碼] 底下，指定 [APM 委派帳戶] 和 [密碼]。 在 [KDC] 欄位中指定 [網域控制站 IP]。 按一下 [儲存並繼續下一步]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure09.png)   

1. 基於本指引的目的，我們將略過端點檢查。  請參閱 F5 文件以取得詳細資訊。  在畫面上選取 [儲存並繼續下一步]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure10.png) 

1. 接受預設值，然後按一下 [儲存並繼續下一步]。 如需關於 SAML 工作階段管理設定的詳細資訊，請參閱 F5 文件。


    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure11.png) 
 
1. 檢閱摘要畫面，然後選取 [部署] 以設定 BIG-IP。
 
    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure12.png)

1. 在應用程式設定後，按一下 [完成]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>進階設定

>[!NOTE]
>如需參考，請按一下[這裡](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>設定 Active Directory AAA 伺服器

您可以在 Access Policy Manager (APM) 中設定 Active Directory AAA 伺服器，以指定 APM 用來驗證使用者的網域控制站和認證。

1. 在 [主要] 索引標籤上，按一下 [存取原則] > [AAA 伺服器] > [Active Directory]。 [Active Directory 伺服器清單] 畫面隨即開啟。

2. 按一下 [建立]。 [新增伺服器屬性] 畫面隨即開啟。

3. 在 [名稱] 欄位中，輸入驗證伺服器的唯一名稱。

4. 在 [網域名稱] 欄位中，輸入 Windows 網域的名稱。

5. 針對 [伺服器連線] 設定中，選取下列其中一個選項：

   * 選取 [使用集區] 以設定 AAA 伺服器的高可用性。

   * 選取 [直接]，以設定 AAA 伺服器的獨立功能。

6. 如果您選取 [直接]，請在 [網域控制站] 欄位中輸入名稱。

7. 如果您選取 [使用集區]，請設定集區：

   * 在 [網域控制站集區名稱] 欄位中輸入名稱。

   * 輸入各個網域控制站的 IP 位址和主機名稱，然後按一下 [新增] 按鈕，以指定集區中的 [網域控制站]。

   * 若要監視 AAA 伺服器的健康情況，您可以選取健康情況監視器：在此案例中，只有 **gateway_icmp** 監視器是適用選項；您可以從 [伺服器集區監視] 清單中選取監視器。

8. 在 [管理員名稱] 欄位中，針對具有 Active Directory 管理權限的管理員，輸入區分大小寫的名稱。 APM 會使用 [AD 查詢] 的 [管理員名稱] 和 [管理員密碼] 欄位中的資訊。 如果已設定 Active Directory 以進行匿名查詢，則不需要提供管理員名稱。 否則，APM 需要有足夠權限可繫結至 Active Directory 伺服器、提取使用者群組資訊，以及提取 Active Directory 密碼原則的帳戶，以支援密碼相關功能。 (例如，如果您在 [AD 查詢] 動作中選取了 [在密碼到期前提示使用者變更密碼] 選項，APM 就必須提取密碼原則。)如果您未在此設定中提供管理員帳戶資訊，APM 將會使用使用者帳戶來提取資訊。 這適用於使用者帳戶有足夠的權限時。

9. 在 [管理員密碼] 欄位中，輸入與網域名稱相關聯的管理員密碼。

10. 在 [驗證管理員密碼] 欄位中，再次輸入與 [網域名稱] 設定相關聯的管理員密碼。

11. 在 [群組快取存留期] 欄位中，輸入天數。 預設存留期為 30 天。

12. 在 [密碼安全性物件快取存留期] 欄位中，輸入天數。 預設存留期為 30 天。

13. 從 [Kerberos 預先驗證加密類型] 清單中，選取加密類型。 預設值為 [無]。 如果您指定加密類型，則 BIG-IP 系統會在第一個驗證服務要求 (AS-REQ) 封包中包含 Kerberos 預先驗證資料。

14. 在 [逾時] 欄位中，輸入 AAA 伺服器的逾時間隔 (以秒為單位)。 (這項設定是選擇性的。)

15. 按一下 [完成]。 新的伺服器會顯示在清單上。 這會將新的 Active Directory 伺服器新增至 [Active Directory 伺服器] 清單。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML 設定

1. 您必須將中繼資料憑證匯入至稍後將在設定程序中使用的 F5。 瀏覽至 [系統] > [憑證管理] > [流量憑證管理] > [SSL 憑證清單]。 從右下角選取 [匯入]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure18.png)

2. 若要設定 SAML IDP，請**瀏覽至 [存取] > [同盟] > [SAML：服務提供者] > [外部 Idp 連接器]** ，然後按一下 [建立] > [從中繼資料]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure19.png)

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure20.png)

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure21.png)

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure22.png)

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure23.png)

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure24.png)

1. 若要設定 SAML SP，請瀏覽至 [存取] > [同盟] > [SAML 服務提供者] > [本機 SP 服務]，然後按一下 [建立]。 完成下列資訊，然後按一下 [確定]。

    * 類型名稱：KerbApp200SAML
    * 實體識別碼*： https://kerb-app.com.cutestat.com
    * SP 名稱設定
    * 配置：HTTPS
    * 主機：kerbapp200.superdemo.live
    * 描述：kerbapp200.superdemo.live

     ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure25.png)

     b. 選取 SP 設定 KerbApp200SAML，然後按一下 [繫結/取消繫結 IdP 連接器]。

     ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure26.png)

     ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure27.png)

     c. 按一下 [新增資料列]，並選取在先前的步驟中建立的 [外部 IdP 連接器]，按一下 [更新]，然後按一下 [確定]。

     ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure28.png)

1. 若要設定 Kerberos SSO，請瀏覽至 [存取] > [單一登入] > [Kerberos]，並在完成資訊後按一下 [完成]。

    >[!Note]
    > 您將需要建立和指定 Kerberos 委派帳戶。 請參閱 KCD 一節 (參閱「變數參考」的附錄)

    * **使用者名稱來源**：session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **使用者領域來源**：session.logon.last.domain

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure29.png)

1. 若要設定存取設定檔，請瀏覽至 [存取] > [設定檔/原則] > [存取設定檔 (遵循工作階段原則)]，按一下 [建立]，並在完成下列資訊後按一下 [完成]。

    * 名稱：KerbApp200
    * 設定檔類型：全部
    * 設定檔範圍：設定檔
    * 語言：英文

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure30.png)

1. 按一下名稱 KerbApp200，並在完成下列資訊後按一下 [更新]。

    * 網域 Cookie：superdemo.live
    * SSO 設定：KerAppSSO_sso

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure31.png)

1. 按一下 [存取原則]，然後針對設定檔 "KerbApp200" 按一下 [編輯存取原則]。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure32.png)

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure33.png)

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain  TEXT superdemo.live**

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure36.png)

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. 若要新增節點，請瀏覽至 [本機流量] > [節點] > [節點清單]，按一下 [建立]，並在完成下列資訊後按一下 [完成]。

    * 名稱：KerbApp200
    * 描述：KerbApp200
    * 位址：192.168.20.200

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure39.png)

1. 若要建立新的集區，請瀏覽至 [本機流量] > [集區] > [集區清單]，按一下 [建立]，並在完成下列資訊後按一下 [完成]。

    * 名稱：KerbApp200-Pool
    * 描述：KerbApp200-Pool
    * 健全狀況監視：http
    * 位址：192.168.20.200
    * 服務連接埠：81

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure40.png)

1. 若要建立虛擬伺服器，請瀏覽至 [本機流量] > [虛擬伺服器] > [虛擬伺服器清單] > [+]，並在完成下列資訊後按一下 [完成]。

    * 名稱：KerbApp200
    * 目的地位址/遮罩：主機 192.168.30.200
    * 服務連接埠：連接埠 443 HTTPS
    * 存取設定檔：KerbApp200
    * 指定在先前的步驟中建立的存取設定檔

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure41.png)

        ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>設定 Kerberos 委派 

>[!NOTE]
>如需參考，請按一下[這裡](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **步驟 1：** 建立委派帳戶

    **範例︰**
    * 網域名稱：**superdemo.live**

    * SAM 帳戶名稱：**big-ipuser**

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **步驟 2：** 設定 SPN (在 APM 委派帳戶上)

    **範例︰**
    * setspn – **host/big-ipuser.superdemo.live** big-ipuser

* **步驟 3：** SPN 委派 (針對 App Service 帳戶) 為 F5 委派帳戶設定適當的委派。
    下列範例會為 FRP-App1.superdemo.live 應用程式設定適用於 KCD 的 APM 委派 帳戶。

    ![F5 (Kerberos) 設定](./media/kerbf5-tutorial/configure43.png)

* 在[這裡](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)底下，提供上方參考文件中所述的詳細資料。

### <a name="create-f5-test-user"></a>建立 F5 測試使用者

在本節中，您會在 F5 中建立名為 B.Simon 的使用者。 請與 [F5 客戶支援小組](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)合作，在 F5 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [F5] 圖格時，應該會自動登入您已設定 SSO 的 F5。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 F5](https://aad.portal.azure.com/)

- [設定適用於標頭式應用程式的 F5 單一登入](headerf5-tutorial.md)

- [設定適用於進階 Kerberos 應用程式的 F5 單一登入](advance-kerbf5-tutorial.md)

