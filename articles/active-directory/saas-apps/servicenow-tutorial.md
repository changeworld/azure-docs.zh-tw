---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 ServiceNow 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ServiceNow 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 3413e594179cdca0704cb5db7908276b7502e719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004750"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 ServiceNow 整合

在本教學課程中，您將了解如何整合 ServiceNow 與 Azure Active Directory (Azure AD)。 在整合 ServiceNow 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 ServiceNow 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 ServiceNow。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 ServiceNow 單一登入 (SSO) 的訂用帳戶。
* 針對 ServiceNow，ServiceNow 的執行個體或租用戶支援 Calgary、Kingston、London、Madrid、New York、Orlando 和 Paris 版本或更新版本。
* 針對 ServiceNow Express，要有 ServiceNow Express 的執行個體 (Helsinki 版本或更新版本)。
* ServiceNow 租用戶必須啟用[多個提供者單一登入外掛程式](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)。
* 若要進行自動設定，請為 ServiceNow 啟用多重提供者外掛程式。
* 若要安裝 ServiceNow Classic (行動) 應用程式，請移至適當的存放區，並搜尋 ServiceNow Classic 應用程式。 然後，加以下載。

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 

* ServiceNow 支援由 **SP** 起始的 SSO。

* ServiceNow 支援[自動使用者佈建](servicenow-provisioning-tutorial.md)。

* 您可以設定與 Azure AD 搭配以啟用 SSO 的 ServiceNow Classic (行動) 應用程式。 它可同時支援 Android 和 iOS 使用者。 在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

## <a name="add-servicenow-from-the-gallery"></a>從資源庫新增 ServiceNow

若要設定 ServiceNow 與 Azure AD 整合，您需要從資源庫將 ServiceNow 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **ServiceNow**。
1. 從結果面板中選取 [ServiceNow]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>設定和測試 ServiceNow 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 ServiceNow 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 ServiceNow 中相關使用者之間的連結關聯性。

若要設定及測試與 ServiceNow 搭配運作的 Azure AD SSO，請執行下列步驟：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。
    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
    1. [針對 ServiceNow Express 設定 Azure AD SSO](#configure-azure-ad-sso-for-servicenow-express)，讓您的使用者能夠使用此功能。
2. [設定 ServiceNow](#configure-servicenow) 以在應用程式端設定 SSO 設定。
    1. [建立 ServiceNow 測試使用者](#create-servicenow-test-user)，使 ServiceNow 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
    1. [設定 ServiceNow Express SSO](#configure-servicenow-express-sso) - 在應用程式端設定單一登入設定。  
3. [測試 SSO](#test-sso)，以驗證組態是否能運作。
4. [測試 ServiceNow Classic (行動) 的 SSO](#test-sso-for-servicenow-classic-mobile)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [ServiceNow] 應用程式整合頁面上，尋找 [管理] 區段。 選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 組態]  的畫筆圖示，以編輯設定。

   ![醒目提示畫筆圖示的 [以 SAML 設定單一登入] 頁面螢幕擷取畫面](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段中，執行下列步驟：

    a. 在 [登入 URL]  中，輸入採用下列模式的 URL：`https://<instancename>.service-now.com/navpage.do`

    b. 在 [識別碼 (實體識別碼)]  中，輸入採用下列模式的 URL：`https://<instance-name>.service-now.com`

    c. 針對 [回覆 URL]，輸入下列其中一個 URL 模式：

    | 回覆 URL|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. 在 [登出 URL] 中，輸入採用下列模式的 URL：`https://<instancename>.service-now.com/navpage.do`

    > [!NOTE]
    > 如果 [識別碼] 值中新增了 "/"，請手動將其移除。

    > [!NOTE]
    > 這些都不是真正的值。 您必須使用實際的登入 URL、回覆 URL、登出 URL 及識別碼來更新這些值 (本教學課程稍後會說明)。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  。 

   ![[SAML 簽署憑證] 區段的螢幕擷取畫面，其中已醒目提示 [下載]](common/certificatebase64.png)

   a. 選取 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其貼到 [記事本] 中。 稍後在教學課程中將會用到此 URL。

    b. 選取 [下載]  以下載 [憑證 (Base64)]  ，然後將憑證檔案儲存在您的電腦上。

1. 在 [設定 ServiceNow]  區段中，依據您的需求複製適當的 URL。

   ![[設定 ServiceNow] 區段的螢幕擷取畫面，其中已醒目提示 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 針對 [名稱] 輸入 `B.Simon`。  
   1. 針對 [使用者名稱]，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  ，然後記下 [密碼]  方塊中顯示的值。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 ServiceNow 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [ServiceNow]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  。 在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊中，從使用者清單中選取 [B.Simon]  ，然後選擇 [選取]  。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>為 ServiceNow Express 設定 Azure AD SSO

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [ServiceNow]  應用程式整合頁面上，選取 [單一登入]  。

    ![ServiceNow 應用程式整合頁面的螢幕擷取畫面，其中已醒目提示 [單一登入]](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![選取單一登入方法的螢幕擷取畫面，其中已醒目提示 SAML](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取畫筆圖示，以開啟 [基本 SAML 組態]  對話方塊。

    ![[以 SAML 設定單一登入] 頁面的螢幕擷取畫面，其中已醒目提示畫筆圖示](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段中，執行下列步驟：

    a. 針對 [登入 URL]  ，輸入採用下列模式的 URL：`https://<instancename>.service-now.com/navpage.do`

    b. 針對 [識別碼 (實體識別碼)]  ，輸入採用下列模式的 URL：`https://<instance-name>.service-now.com`

    c. 針對 [回覆 URL]  ，輸入下列其中一個 URL：

    | 回覆 URL |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. 在 [登出 URL] 中，輸入採用下列模式的 URL：`https://<instancename>.service-now.com/navpage.do`
    
    > [!NOTE]
    > 如果 [識別碼] 值中新增了 "/"，請手動將其移除。

    > [!NOTE]
    > 這些都不是真正的值。 您必須使用實際的登入 URL、回覆 URL、登出 URL 及識別碼來更新這些值 (本教學課程稍後會說明)。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中選取 [下載]  ，以依據您的需求從指定的選項下載 [憑證 (Base64)]  。 將其儲存在您的電腦上。

    ![已醒目提示 [下載] 的 [SAML 簽署憑證] 區段螢幕擷取畫面](common/certificatebase64.png)

6. 您可以讓 Azure AD 自動設定 ServiceNow，以進行 SAML 型驗證。 若要啟用此服務，請移至 [設定 ServiceNow]  區段，然後選取 [檢視逐步指示]  以開啟 [設定登入]  視窗。

    ![[設定 ServiceNow] 區段的螢幕擷取畫面，其中已醒目提示 [檢視逐步指示]](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. 在 [設定單一登入]  表單中輸入 ServiceNow 執行個體名稱、管理員使用者名稱和管理員密碼。 選取 [立即設定]  。 提供的管理員使用者名稱必須在 ServiceNow 中指派 **security_admin** 角色，才能運作。 否則，若要手動設定 ServiceNow 以使用 Azure AD 作為 SAML 識別提供者，請選取 [手動設定單一登入]  。 從 [快速參考] 區段中複製 **登出 URL、Azure AD 識別碼，和登入 URL**。

    ![設定登入表單的螢幕擷取畫面，其中已醒目提示 [立即設定]](./media/servicenow-tutorial/configure.png "設定應用程式 URL")

## <a name="configure-servicenow"></a>設定 ServiceNow

1. 以系統管理員身分登入您的 ServiceNow 應用程式。

1. 依照下列步驟啟用 [整合 - 多個提供者單一登入安裝程式]  外掛程式︰

    a. 在左窗格中，從搜尋方塊搜尋 [系統定義]  區段，然後選取 [外掛程式]  。

    ![[系統定義] 區段的螢幕擷取畫面，其中已醒目提示 [系統定義] 和 [外掛程式]](./media/servicenow-tutorial/tutorial-servicenow-03.png "啟用外掛程式")

    b. 搜尋 **整合 - 多個提供者單一登入安裝程式**。

     ![[系統外掛程式] 頁面的螢幕擷取畫面，其中已醒目提示 [整合 - 多個提供者單一登入安裝程式]](./media/servicenow-tutorial/tutorial-servicenow-04.png "啟用外掛程式")

    c. 選取外掛程式。 按一下滑鼠右鍵並選取 [啟用/升級]  。

     ![外掛程式滑鼠右鍵功能表的螢幕擷取畫面，其中已醒目提示 [啟用/升級]](./media/servicenow-tutorial/tutorial-activate.png "啟用外掛程式")

    d. 選取 [啟用]  。

     ![[啟用外掛程式] 對話方塊的螢幕擷取畫面，其中已醒目提示 [啟用]](./media/servicenow-tutorial/tutorial-activate-1.png "啟用外掛程式")

1. 在左窗格中，從搜尋列搜尋 [多重提供者 SSO]  區段，然後選取 [屬性]  。

    ![[多重提供者 SSO] 區段的螢幕擷取畫面，其中已醒目提示 [多重提供者 SSO] 和 [屬性]](./media/servicenow-tutorial/tutorial-servicenow-06.png "設定應用程式 URL")

1. 在 [多重提供者 SSO 屬性]  對話方塊上，執行下列步驟：

    ![[多重提供者 SSO 屬性] 對話方塊的螢幕擷取畫面](./media/servicenow-tutorial/ic7694981.png "設定應用程式 URL")

    * 針對 [啟用多個提供者 SSO]  ，選取 [是]  。
  
    * 針對 [啟用從所有識別提供者將使用者自動匯入使用者資料表]  ，選取 [是]  。

    * 針對 [啟用偵錯記錄以供多個提供者 SSO 整合]  ，選取 [是]  。

    * 針對 [使用者資料表上的欄位...]  ，輸入 **電子郵件**。
  
    * 選取 [儲存]  。

1. 您可以自動或手動設定 ServiceNow。 若要自動設定 ServiceNow，請依照下列步驟操作：

    1. 返回 Azure 入口網站中的 [ServiceNow]  單一登入頁面。

    1. ServiceNow 提供單鍵設定服務。 若要啟用這項服務，請移至 [ServiceNow 設定]  區段，選取 [設定 ServiceNow]  以開啟 [設定單一登入]  視窗。

        ![[設定 ServiceNow] 的螢幕擷取畫面，其中已醒目提示 [檢視逐步指示]](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. 在 [設定單一登入]  表單中輸入 ServiceNow 執行個體名稱、管理員使用者名稱和管理員密碼。 選取 [立即設定]  。 提供的管理員使用者名稱必須在 ServiceNow 中指派 **security-admin** 角色，才能運作。 否則，若要手動設定 ServiceNow 以使用 Azure AD 作為 SAML 識別提供者，請選取 [手動設定單一登入]  。 從 [快速參考] 區段中複製 **登出 URL、SAML 實體識別碼，和 SAML 單一登入服務 URL**。

        ![設定登入表單的螢幕擷取畫面，其中已醒目提示 [立即設定]](./media/servicenow-tutorial/configure.png "設定應用程式 URL")

    1. 以系統管理員身分登入您的 ServiceNow 應用程式。

       * 在自動設定中，所有必要的設定都是在 **ServiceNow** 端設定的，但 **X.509 憑證** 預設不會啟用，並會將 [單一登入指令碼] 值提供為 [MultiSSOv2_SAML2_custom]。 您必須以手動方式將其對應至您在 ServiceNow 中的身分識別提供者。 請遵循下列步驟：

         1. 在左窗格中，從搜尋方塊搜尋 [多重提供者 SSO]  區段，然後選取 [識別提供者]  。

            ![[多重提供者 SSO] 區段的螢幕擷取畫面，其中已醒目提示 [識別提供者]](./media/servicenow-tutorial/tutorial-servicenow-07.png "設定單一登入")

         1. 選取自動產生的識別提供者。

            ![識別提供者的螢幕擷取畫面，其中已醒目提示自動產生的識別提供者](./media/servicenow-tutorial/tutorial-servicenow-08.png "設定單一登入")

         1.  在 [識別提供者]  區段上，執行下列步驟：

             ![[識別提供者] 區段的螢幕擷取畫面](./media/servicenow-tutorial/automatic-config.png "設定單一登入")

               a. 針對 [名稱]，輸入您的組態名稱 (例如 **Microsoft Azure Federated Single Sign-On**)。

               b. 複製 [ServiceNow 首頁]  值，然後將其貼到 Azure 入口網站的 [ServiceNow 基本 SAML 組態]  區段的 [登入 URL]  中。

                > [!NOTE]
                > 將您的 [ServieNow 租用戶 URL]  與 **/navpage.do** 串連即是 ServiceNow 執行個體首頁(例如：`https://fabrikam.service-now.com/navpage.do`)。

              c. 複製 [實體識別碼/簽發者]  值，並將其貼到 Azure 入口網站的 [ServiceNow 基本 SAML 組態]  區段的 [識別碼]  中。

              d. 確認 [NameID 原則]  設定為 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。 

              e。 按一下 [進階]，並將 [單一登入指令碼] 值提供為 [MultiSSOv2_SAML2_custom]。

         1. 向下捲動至 [X.509 憑證]  區段，然後選取 [編輯]  。

             ![[X.509 憑證] 區段的螢幕擷取畫面，其中已醒目提示 [編輯]](./media/servicenow-tutorial/tutorial-servicenow-09.png "設定單一登入")

         1. 選取憑證，然後選取向右箭號圖示以新增憑證

            ![[集合] 的螢幕擷取畫面，其中已醒目提示憑證和向右箭號圖示](./media/servicenow-tutorial/tutorial-servicenow-11.png "設定單一登入")

          1. 選取 [儲存]  。

          1. 在頁面的右上角，選取 [測試連線]  。

             ![頁面的螢幕擷取畫面，其中已醒目提示 [測試連線]](./media/servicenow-tutorial/tutorial-activate-2.png "啟用外掛程式")

             > [!NOTE]
             > 如果測試連線失敗，而且您也無法啟動此連線的話，ServiceNow 就會提供覆寫參數。 您必須在 [搜尋導覽]  中輸入 **Sys_properties.LIST**，新的 [系統屬性] 頁面會隨即開啟。 在這裡，您必須建立新的屬性，並將名稱設為 **glide.authenticate.multisso.test.connection.mandatory**、將 [資料類型]  設為 **True/False**，然後將 [值]  設為 **False**。

             > ![[測試連線] 頁面的螢幕擷取畫面](./media/servicenow-tutorial/test-connection-fail.png "設定單一登入")
        
          1. 在系統要求您提供認證時，輸入您的認證。 您會看見下列頁面。 出現 **SSO 登出測試結果** 錯誤是預期的行為。 請忽略錯誤，然後選取 [啟用]  。

             ![[認證] 頁面的螢幕擷取畫面](./media/servicenow-tutorial/servicenow-activate.png "設定單一登入")
  
1. 若要手動設定 **ServiceNow**，請依照下列步驟操作：

    1. 以系統管理員身分登入您的 ServiceNow 應用程式。

    1. 在左窗格中，選取 [識別提供者]  。

        ![[多重提供者 SSO] 的螢幕擷取畫面，其中已醒目提示 [識別提供者]](./media/servicenow-tutorial/tutorial-servicenow-07.png "設定單一登入")

    1. 在 [識別提供者]  對話方塊中，選取 [新增]  。

        ![[識別提供者] 對話方塊的螢幕擷取畫面，其中已醒目提示 [新增]](./media/servicenow-tutorial/ic7694977.png "設定單一登入")

    1. 在 [識別提供者]  對話方塊中，選取 [SAML]  。

        ![[識別提供者] 對話方塊的螢幕擷取畫面，其中已醒目提示 [SAML]](./media/servicenow-tutorial/ic7694978.png "設定單一登入")

    1. 在 [匯入識別提供者中繼資料]  中，執行下列步驟：

        ![[匯入識別提供者中繼資料] 的螢幕擷取畫面，其中已醒目提示 [URL] 和 [匯入]](./media/servicenow-tutorial/idp.png "設定單一登入")

        1. 輸入您從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  。

        1. 選取 [匯入]  。

    1. 這樣會讀取 IdP 中繼資料 URL 並填入所有欄位資訊。

        ![[識別提供者] 的螢幕擷取畫面](./media/servicenow-tutorial/ic7694982.png "設定單一登入")

        a. 針對 [名稱]  ，輸入您的組態名稱 (例如 **Microsoft Azure Federated Single Sign-On**)。

        b. 複製 [ServiceNow 首頁]  值。 將其貼到 Azure 入口網站的 [ServiceNow 基本 SAML 組態]  區段的 [登入 URL]  中。

        > [!NOTE]
        > 將您的 [ServieNow 租用戶 URL]  與 **/navpage.do** 串連即是 ServiceNow 執行個體首頁(例如：`https://fabrikam.service-now.com/navpage.do`)。

        c. 複製 [實體識別碼/簽發者]  值。 將其貼到 Azure 入口網站的 [ServiceNow 基本 SAML 組態]  區段的 [識別碼]  中。

        d. 確認 [NameID 原則]  設定為 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。

        e。 選取 [進階]  。 在 [使用者欄位]  中，輸入 **電子郵件**。

        > [!NOTE]
        > 您可以設定 Azure AD 以發出 Azure AD 使用者識別碼 (使用者主體名稱) 或電子郵件地址，作為 SAML 權杖中的唯一識別碼。 若要這麼做，請移至 Azure 入口網站的 [ServiceNow]   > [屬性]   > [單一登入]  區段，並將所需的欄位對應至 **nameidentifier** 屬性。 所選屬性儲存在 Azure AD 中的值 (例如使用者主體名稱) 必須符合所輸入欄位 (例如 user_name) 儲存在 ServiceNow 中的值。

        如 在頁面的右上角選取 [測試連線]  。

        > [!NOTE]
        > 如果測試連線失敗，而且您也無法啟動此連線的話，ServiceNow 就會提供覆寫參數。 您必須在 [搜尋導覽]  中輸入 **Sys_properties.LIST**，新的 [系統屬性] 頁面會隨即開啟。 在這裡，您必須建立新的屬性，並將名稱設為 **glide.authenticate.multisso.test.connection.mandatory**、將 [資料類型]  設為 **True/False**，然後將 [值]  設為 **False**。

          > ![[測試連線] 的螢幕擷取畫面](./media/servicenow-tutorial/test-connection-fail.png "設定單一登入")

        h. 在系統要求您提供認證時，輸入您的認證。 您會看見下列頁面。 出現 **SSO 登出測試結果** 錯誤是預期的行為。 請忽略錯誤，然後選取 [啟用]  。

          ![credentials](./media/servicenow-tutorial/servicenow-activate.png "設定單一登入")

### <a name="create-servicenow-test-user"></a>建立 ServiceNow 測試使用者

本節的目標是要在 ServiceNow 中建立一個名為 B.Simon 的使用者。 ServiceNow 支援自動使用者佈建，該功能預設為啟用。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [ServiceNow 用戶端支援小組](https://www.servicenow.com/support/contact-support.html)。

### <a name="configure-servicenow-express-sso"></a>設定 ServiceNow Express SSO

1. 以系統管理員身分登入您的 ServiceNow Express 應用程式。

2. 在左窗格中，選取 [單一登入]  。

    ![ServiceNow Express 應用程式的螢幕擷取畫面，其中已醒目提示 [單一登入]](./media/servicenow-tutorial/ic7694980ex.png "設定應用程式 URL")

3. 在 [單一登入]  對話方塊中，選取右上方的設定圖示，然後設定下列屬性︰

    ![[單一登入] 對話方塊的螢幕擷取畫面](./media/servicenow-tutorial/ic7694981ex.png "設定應用程式 URL")

    a. 將 [啟用多個提供者 SSO]  切換到右邊。

    b. 將 [啟用偵錯記錄以供多個提供者 SSO 整合]  切換到右邊。

    c. 在 [使用者資料表上的欄位...]  中，輸入 **user_name**。

4. 在 [單一登入]  對話方塊中，選取 [新增憑證]  。

    ![[單一登入] 對話方塊的螢幕擷取畫面，其中已醒目提示 [新增憑證]](./media/servicenow-tutorial/ic7694973ex.png "設定單一登入")

5. 在 [X.509 憑證]  對話方塊中，執行下列步驟：

    ![[X.509 憑證] 對話方塊的螢幕擷取畫面](./media/servicenow-tutorial/ic7694975.png "設定單一登入")

    a. 針對 [名稱]  ，輸入您的組態名稱 (例如：**TestSAML2.0**)。

    b. 選取 [使用中]  。

    c. 針對 [格式]  ，選取 [PEM]  。

    d. 針對 [類型]  ，選取 [信任存放區憑證]  。

    e. 在 [記事本] 中開啟您從 Azure 入口網站下載的 Base64 編碼憑證。 將其內容複製到剪貼簿，然後貼到 [IdP 憑證]  文字方塊中。

    f. 選取 [更新]

6. 在 [單一登入]  對話方塊中，選取 [新增 IdP]  。

    ![[單一登入] 對話方塊的螢幕擷取畫面，其中已醒目提示 [新增 IdP]](./media/servicenow-tutorial/ic7694976ex.png "設定單一登入")

7. 在 [新增識別提供者]  對話方塊的 [設定識別提供者]  下方，執行下列步驟：

    ![[新增識別提供者] 對話方塊的螢幕擷取畫面](./media/servicenow-tutorial/ic7694982ex.png "設定單一登入")

    a. 針對 [名稱]  ，輸入您的組態名稱 (例如：**SAML 2.0**)。

    b. 針對 [識別提供者 URL]  ，貼上您從 Azure 入口網站複製的識別提供者識別碼值。

    c. 針對 [識別提供者的 AuthnRequest]  ，貼上您從 Azure 入口網站複製的驗證要求 URL 值。

    d. 針對 [識別提供者的 SingleLogoutRequest]  ，貼上您從 Azure 入口網站複製的登出 URL 值。

    e. 針對 [識別提供者憑證]  ，選取您在先前的步驟中建立的憑證。

8. 選取 [進階設定]  。 在 [其他識別提供者屬性]  下方，執行下列步驟︰

    ![[新增識別提供者] 對話方塊的螢幕擷取畫面，其中已醒目提示 [進階設定]](./media/servicenow-tutorial/ic7694983ex.png "設定單一登入")

    a. 針對 [IDP SingleLogoutRequest 的通訊協定繫結]  ，輸入 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**。

    b. 針對 [名稱識別碼原則]  ，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**。

    c. 針對 [AuthnContextClassRef 方法]  ，輸入 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`。

    d. 針對 [建立 AuthnCoNtextClass]  ，將其切換為關閉 (未選取)。

9. 在 [其他服務提供者屬性]  之下，執行下列步驟︰

    ![[新增識別提供者] 對話方塊的螢幕擷取畫面，其中已醒目提示多個不同屬性](./media/servicenow-tutorial/ic7694984ex.png "設定單一登入")

    a. 針對 [ServiceNow 首頁]  ，輸入您 ServiceNow 執行個體首頁的 URL。

    > [!NOTE]
    > 將您的 [ServieNow 租用戶 URL]  與 **/navpage.do** 串連即是 ServiceNow 執行個體首頁 (例如：`https://fabrikam.service-now.com/navpage.do`)。

    b. 針對 [實體識別碼/簽發者]  ，輸入您 ServiceNow 租用戶的 URL。

    c. 針對 [對象 URI]  ，輸入您 ServiceNow 租用戶的 URL。

    d. 針對 [時鐘誤差]  ，輸入 **60**。

    e. 針對 [使用者欄位]  ，輸入 **電子郵件**。

    > [!NOTE]
    > 您可以設定 Azure AD 以發出 Azure AD 使用者識別碼 (使用者主體名稱) 或電子郵件地址，作為 SAML 權杖中的唯一識別碼。 若要這麼做，請移至 Azure 入口網站的 [ServiceNow]   > [屬性]   > [單一登入]  區段，並將所需的欄位對應至 **nameidentifier** 屬性。 所選屬性儲存在 Azure AD 中的值 (例如使用者主體名稱) 必須符合所輸入欄位 (例如 user_name) 儲存在 ServiceNow 中的值。

    f. 選取 [儲存]  。

## <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [ServiceNow] 圖格時，應該會自動登入您已設定 SSO 的 ServiceNow。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="test-sso-for-servicenow-classic-mobile"></a>測試 ServiceNow Classic (行動) 的 SSO

1. 開啟您的 **ServiceNow Classic (行動)** 應用程式，並執行下列步驟：

    a. 選取位於右下角的加號。

    ![ServiceNow Classic 應用程式的螢幕擷取畫面，其中已醒目提示加號](./media/servicenow-tutorial/test-03.png)

    b. 輸入您的 ServiceNow 執行個體名稱，然後選取 [繼續]  。

    ![[新增執行個體] 頁面的螢幕擷取畫面，其中已醒目提示 [繼續]](./media/servicenow-tutorial/test-04.png)

    c. 在 [登入]  頁面上，執行下列步驟：

    ![[登入] 頁面的螢幕擷取畫面，其中已醒目提示 [使用外部登入]](./media/servicenow-tutorial/test-01.png)

    *  輸入 [使用者名稱]  ，例如 B.simon@contoso.com。

    *  選取 [新增外部登入]  。 系統會將您重新導向至 Azure AD 頁面進行登入。

    *  輸入認證。 如果啟用了任何第三方驗證或任何其他安全性功能，使用者就必須據以因應。 應用程式的 **首頁** 會隨即出現。

        ![應用程式首頁的螢幕擷取畫面](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>後續步驟

設定 ServiceNow 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

