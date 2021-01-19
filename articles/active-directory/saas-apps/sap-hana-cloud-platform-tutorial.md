---
title: 教學課程：Azure Active Directory 與 SAP 雲端平台整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP 雲端平台之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964043"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>教學課程：Azure Active Directory 與 SAP 雲端平台整合

在本教學課程中，您會了解如何整合 SAP 雲端平台與 Azure Active Directory (Azure AD)。 在整合 SAP Cloud Platform 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 SAP Cloud Platform 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SAP Cloud Platform。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 SAP 雲端平台整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 SAP 雲端平台單一登入的訂用帳戶

完成本教學課程之後，您指派給 SAP 雲端平台的 Azure AD 使用者就能夠使用[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)對應用程式進行單一登入。

>[!IMPORTANT]
>您需要在 SAP 雲端平台帳戶上部署您自己的應用程式或訂閱應用程式來測試單一登入。 在本教學課程中，帳戶中已部署一個應用程式。
> 

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SAP 雲端平台支援 **SP** 起始的 SSO

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>從資源庫新增 SAP 雲端平台

若要設定將 SAP 雲端平台整合到 Azure AD 中，您需要從資源庫將 SAP 雲端平台新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **SAP Cloud Platform**。
1. 從結果面板中選取 [SAP Cloud Platform]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>設定及測試 SAP Cloud Platform 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 SAP Cloud Platform 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SAP Cloud Platform 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP Cloud Platform 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 SAP Cloud Platform SSO](#configure-sap-cloud-platform-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 SAP 雲端平台測試使用者](#create-sap-cloud-platform-test-user)** - 在 SAP 雲端平台中建立一個與 Azure AD 中代表使用者之項目連結的 Britta Simon 對應項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **SAP Cloud Platform** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    ![SAP 雲端平台網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，輸入使用者用來登入 **SAP 雲端平台** 應用程式的 URL。 這是您的 SAP 雲端平台應用程式中受保護資源的帳戶特定 URL。 此 URL 根據下列模式：`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >這是您 SAP 雲端平台應用程式中需要使用者驗證的 URL。
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. 在 [識別碼]  文字方塊中，您會提供 SAP 雲端平台的識別碼，請使用下列其中一個模式輸入 URL： 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡[ SAP 雲端平台用戶端支援小組](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html)，以取得單一登入 URL 和識別碼。 您可以從信任管理區段取得的「回覆 URL」，稍後會在本教學課程中說明。
    > 
4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAP Cloud Platform 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SAP 雲端平台]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-sap-cloud-platform-sso"></a>設定 SAP Cloud Platform SSO

1. 在不同的網頁瀏覽器視窗中，登入 SAP 雲端平台駕駛座，網址為 `https://account.<landscape host>.ondemand.com/cockpit`(例如： https://account.hanatrial.ondemand.com/cockpit) 。

2. 按一下 [信任]  索引標籤。
   
    ![信任](./media/sap-hana-cloud-platform-tutorial/ic790800.png "信任")

3. 在 [信任管理] 區段的 [本機服務提供者]  下，執行下列步驟：

    ![螢幕擷取畫面：顯示 [信任管理] 區段，其中已選取 [本地服務提供者] 索引標籤，並已醒目提示所有文字方塊。](./media/sap-hana-cloud-platform-tutorial/ic793931.png "信任管理")
   
    a. 按一下 **[編輯]** 。

    b. 針對 [組態類型]  ，選取 [自訂]  。

    c. 針對 [本機提供者名稱]  ，保留預設值。 複製這個值，然後在 Azure AD 的 SAP 雲端平台設定中，貼到 [識別碼]  欄位中。

    d. 若要產生 **簽署金鑰** 和 **簽署憑證** 金鑰組，請按一下 [產生金鑰組]  。

    e. 針對 [主體傳播]  ，選取 [已停用]  。

    f. 針對 [強制驗證]  ，選取 [已停用]  。

    g. 按一下 [檔案]  。

4. 儲存 [本機服務提供者]  設定之後，執行下列命令以取得「回覆 URL」：
   
    ![取得中繼資料](./media/sap-hana-cloud-platform-tutorial/ic793930.png "取得中繼資料")

    a. 按一下 [取得中繼資料]  ，以下載 SAP 雲端平台中繼資料檔案。

    b. 開啟已下載的 SAP 雲端平台中繼資料檔案，然後找出 **ns3:AssertionConsumerService** 標記。
 
    c. 複製 [位置]  屬性的值，然後在 Azure AD 的 SAP 雲端平台設定中，貼到 [回覆 URL]  欄位中。

5. 按一下 [信任的識別提供者]  索引標籤，然後再按一下 [新增信任的識別提供者]  。
   
    ![螢幕擷取畫面：顯示 [信任管理] 頁面，其中已選取 [信任的識別提供者] 索引標籤。](./media/sap-hana-cloud-platform-tutorial/ic790802.png "信任管理")
   
    >[!NOTE]
    >若要管理信任的識別提供者清單，您必須已經在 [本機服務提供者] 區段中選擇了 [自訂組態類型]。 針對 [預設組態類型]，您對 SAP ID 服務有不可編輯且隱含的信任。 針對 [無]，您不具任何信任設定。
    > 
    > 

6. 按一下 [一般]  索引標籤，然後再按一下 [瀏覽]  來上傳下載的中繼資料檔案。
    
    ![信任管理](./media/sap-hana-cloud-platform-tutorial/ic793932.png "信任管理")
    
    >[!NOTE]
    >上傳中繼資料檔之後，將會自動填入 [單一登入 URL]  、[單一登出 URL]  和 [簽署憑證]  的值。
    > 
     
7. 按一下 [屬性]  索引標籤。

8. 在 [屬性]  索引標籤上，執行下列步驟：
    
    ![屬性](./media/sap-hana-cloud-platform-tutorial/ic790804.png "屬性") 

    a. 按一下 [新增判斷提示式的屬性]  ，然後新增下列判斷提示式屬性：
       
    | 判斷提示屬性 | 主體屬性 |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |電子郵件 |
   
     >[!NOTE]
     >「屬性」的設定取決於 SCP 上的應用程式如何開發，亦即在 SAML 回應中預期哪個屬性，以及在程式碼中以哪個名稱 (主體屬性) 存取該屬性。
     > 
    
    b. 螢幕擷取畫面中的 [預設屬性]  僅供說明之用。 其並不是案例進行所必須的要素。  
 
    c. 螢幕擷取畫面所顯示的 **主體屬性** 名稱與值取決於應用程式是如何開發的。 您的應用程式很可能需要不同的對應。

### <a name="assertion-based-groups"></a>以判斷提示為基礎的群組

在選擇性的步驟中，您可以為 Azure Active Directory 識別提供者設定以判斷提示為基礎的群組。

在 SAP 雲端平台上使用群組可讓您在 SAP 雲端平台應用程式中，以動態方式將一或多個使用者指派給一或多個角色，這是由 SAML 2.0 判斷提示中的屬性值所決定。 

例如，如果判斷提示包含屬性「合約＝暫存」  ，您可能會希望將所有受影響的使用者新增至「暫存」  群組。 「暫存」  群組可能包含來自一或多個部署在 SAP 雲端平台帳戶中之應用程式的一或多個角色。
 
當您想要同時指派許多使用者給 SAP 雲端平台帳戶中之應用程式的一個或多個角色時，請使用以判斷提示為基礎的群組。 如果您只想要指派單一或少數使用者給特定角色，我們建議直接在 SAP 雲端平台駕駛座的 [授權]  索引標籤中指派。

### <a name="create-sap-cloud-platform-test-user"></a>建立 SAP 雲端平台測試使用者

若要讓 Azure AD 使用者能夠登入 SAP 雲端平台，您必須將 SAP 雲端平台中的角色指派給他們。

**若要指派角色給使用者，請執行下列步驟：**

1. 登入您的 **SAP 雲端平台** 駕駛座。

2. 請執行下列步驟：
   
    ![授權](./media/sap-hana-cloud-platform-tutorial/ic790805.png "授權")
   
    a. 按一下 [授權]  。

    b. 按一下 [使用者]  索引標籤。

    c. 在 [使用者]  文字方塊中，輸入使用者的電子郵件地址。

    d. 按一下 [指派]  將使用者指派給角色。

    e. 按一下 [檔案] 。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 SAP Cloud Platform 登入 URL。 

* 直接移至 SAP Cloud Platform 登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您在存取面板中按一下 [SAP Cloud Platform] 圖格時，應該會自動登入您設定 SSO 的 SAP Cloud Platform。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

設定 SAP Cloud Platform 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。