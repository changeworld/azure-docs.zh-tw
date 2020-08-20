---
title: 教學課程：Azure Active Directory 與 Mitel Connect 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Mitel Connect 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: 63f1d1d40d8aff21641f3fa4ee10a289de40800d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552589"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>教學課程：Azure Active Directory 與 Mitel MiCloud Connect 或 CloudLink Platform 整合

在本教學課程中，您將了解如何使用 Mitel Connect 應用程式，將 Azure Active Directory (Azure AD) 與 Mitel MiCloud Connect 或 CloudLink Platform 整合。 Mitel Connect 應用程式可在 Azure 資源庫中取得。 將 Azure AD 與 MiCloud Connect 或 CloudLink Platform 整合可提供下列優點：

* 您可以使用使用者的企業認證，控制使用者是否能存取 MiCloud Connect 應用程式和 Azure AD 中的 CloudLink 應用程式。
* 您可以讓帳戶上的使用者使用其 Azure AD 帳戶自動登入 MiCloud Connect 或 CloudLink (單一登入)。

如需有關 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果您沒有 Azure 訂用帳戶，請先[建立免費帳戶](https://azure.microsoft.com/free/)，再開始整合 Azure AD 與 Mitel MiCloud Connect 或 CloudLink Platform。

## <a name="prerequisites"></a>先決條件

若要設定與 MiCloud Connect 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* Mitel MiCloud Connect 帳戶或 Mitel CloudLink 帳戶 (視您想要設定的應用程式而定)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會設定和測試 Azure AD 單一登入 (SSO)。

* Mitel Connect 支援由 **SP** 起始的 SSO
* 設定 Mitel Connect 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-mitel-connect-from-the-gallery"></a>從資源庫新增 Mitel Connect

若要設定將 Mitel Connect 整合到 Azure AD 中，您需要從資源庫將 Mitel Connect 新增到 Azure 入口網站中的受控 SaaS 應用程式清單。

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 選取 [企業應用程式]****，然後選取 [所有應用程式]****。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 選取 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋欄位中輸入 [Mitel Connect]，並從結果面板中選取 [Mitel Connect]，然後選取 [新增]。

     ![結果清單中的 Mitel Connect](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **_Britta Simon_** 的測試使用者為基礎，設定及測試與 MiCloud Connect 或 CloudLink Platform 搭配運作的 Azure AD SSO。 若要讓單一登入正常運作，您必須在 Azure AD 入口網站中的使用者與 Mitel 平台上的對應使用者之間建立連結。 請參閱下列各節，以了解如何設定及測試與 MiCloud Connect 或 CloudLink Platform 搭配運作的 Azure AD SSO。
* 設定及測試與 MiCloud Connect 搭配運作的 Azure AD SSO
* 設定及測試與 CloudLink Platform 搭配運作的 Azure AD SSO

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>設定及測試與 MiCloud Connect 搭配運作的 Azure AD SSO

若要設定及測試與 MiCloud Connect 搭配運作的 Azure AD 單一登入：

1. **[設定 MiCloud Connect 以搭配 Azure AD 使用 SSO](#configure-micloud-connect-for-sso-with-azure-ad)** - 讓使用者能夠使用此功能，並在應用程式端進行 SSO 設定。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
4. **[建立 Mitel MiCloud Connect 測試使用者](#create-a-mitel-micloud-connect-test-user)** - 在 MiCloud Connect 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
5. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>設定 MiCloud Connect 以搭配 Azure AD 使用 SSO

在本節中，您將在 Azure 入口網站中為 MiCloud Connect 啟用 Azure AD 單一登入，並使用 Azure AD 將 MiCloud Connect 帳戶設定為可使用 SSO。

若要針對 Azure AD 設定 MiCloud Connect 的 SSO，就最簡單的方式是並排開啟 Azure 入口網站和 Mitel 帳戶入口網站。 您將必須在 Azure 入口網站和 Mitel 帳戶入口網站之間來回複製一些資訊。


1. 若要在 [Azure 入口網站](https://portal.azure.com/)中開啟設定頁面：

    1. 在 **Mitel Connect** 應用程式整合頁面上，選取 [單一登入]。

       ![設定單一登入連結](common/select-sso.png)

    1. 在 [選取單一登入方法] 對話方塊中，選取 [SAML]。
    
       ![單一登入選取模式](common/select-saml-option.png)
    
       SAML 登入頁面會隨即顯示。

2. 若要在 Mitel 帳戶入口網站中開啟設定對話方塊：

    1. 在 [電話系統] 功能表上，選取 [附加元件功能]。

    1. 在 [單一登入] 右側，選取 [啟動] 或 [設定]。
    
    [Connect 單一登入設定] 對話方塊會隨即出現。
    
3. 選取 [啟用單一登入]**** 核取方塊。
    
    ![顯示 [Mitel Connect 單一登入設定] 頁面的螢幕擷取畫面，其中已選取 [啟用單一登入] 核取方塊。](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. 在 Azure 入口網站中，選取 [基本 SAML 設定] 區段中的 [編輯] 圖示。
   
    ![image](common/edit-urls.png)

    [基本 SAML 組態] 對話方塊會隨即出現。

5.  從 Mitel 帳戶入口網站中的 [Mitel 識別碼 (實體識別碼)]**** 欄位複製 URL，並將其貼到 Azure 入口網站中的 [識別碼 (實體識別碼)]**** 欄位。

6. 從 Mitel 帳戶入口網站中的 [回覆 URL (判斷提示取用者服務 URL)]**** 欄位中複製 URL，然後將其貼到 Azure 入口網站中的 [回覆 URL (判斷提示取用者服務 URL)]**** 欄位。

   ![image](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. 在 [登入 URL]**** 文字方塊中，輸入下列其中一個 URL：

    1. **https://portal.shoretelsky.com** - 使用 Mitel 帳戶入口網站作為您預設的 Mitel 應用程式
    1. **https://teamwork.shoretel.com** - 使用 Teamwork 作為您預設的 Mitel 應用程式

    > [!NOTE]
    > 預設 Mitel 應用程式是當使用者選取存取面板中的 [Mitel Connect] 圖格時，所存取的應用程式。 這也是從 Azure AD 進行測試設定時所存取的應用程式。

8. 在 Azure 入口網站的 [基本 SAML 設定] 對話方塊中，選取 [儲存]。

9. 在 Azure 入口網站的 [SAML 型登入] 頁面上，從 [SAML 簽署憑證] 區段中選取 [憑證 (Base64)] 旁的 [下載]，下載 [簽署憑證] 並將其儲存在您的電腦中。

    ![image](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. 在文字編輯器中開啟簽署憑證檔案，複製檔案中的所有資料，然後將資料貼到 Mitel 帳戶入口網站中的 [簽署憑證]**** 欄位。 

      ![image](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. 在 Azure 入口網站中，於 [SAML 型登入] 頁面上的 [設定 Mitel Connect] 區段：

     1. 從 [登入 URL]**** 欄位複製 URL，並將其貼到 Mitel 帳戶入口網站的 [登入 URL]**** 欄位中。

     1. 從 [Azure AD 識別碼]**** 欄位複製 URL，並將其貼到 Mitel 帳戶入口網站的 [實體識別碼]**** 欄位中。
         
         ![image](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. 在 Mitel 帳戶入口網站中的 [Connect 單一登入設定] 對話方塊上選取 [儲存]。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者屬性] 對話方塊中執行下列步驟：

    ![[使用者] 對話方塊](common/user-properties.png)

    1. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    1. 在 [使用者名稱] 欄位中，輸入 brittasimon@\<yourcompanydomain\>.\<extension\>。  例如： BrittaSimon@contoso.com 。

    1. 選取 [顯示密碼]**** 核取方塊，然後記下 [密碼]**** 方塊中顯示的值。

    1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Mitel Connect 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Mitel Connect]****。

    ![應用程式清單中的 Mitel Connect 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]****，然後在 [新增指派]**** 對話方塊中選取 [使用者和群組]****。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後選擇畫面底部的 [選取]。

6. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後選擇畫面底部的 [選取]。

7. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-a-mitel-micloud-connect-test-user"></a>建立 Mitel MiCloud Connect 測試使用者

在本節中，您會在 MiCloud Connect 帳戶中建立名為 Britta Simon 的使用者。 必須先建立和啟動使用者，才能使用單一登入。

如需有關在 Mitel 帳戶入口網站新增使用者的詳細資訊，請參閱 Mitel 知識庫中的[新增使用者](https://oneview.mitel.com/s/article/Adding-a-User-092815)文章。

使用下列詳細資料在 MiCloud Connect 帳戶上建立使用者：

* **名稱：** Britta Simon
* **公司電子郵件地址：** `brittasimon@<yourcompanydomain>.<extension>`   
  (範例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com))
* **使用者名稱：** `brittasimon@<yourcompanydomain>.<extension>`  
  (範例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com)；使用者的使用者名稱通常與使用者的公司電子郵件地址相同)

> [!NOTE]
> 使用者的 MiCloud Connect 使用者名稱必須與 Azure 中的使用者電子郵件地址相同。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您選取存取面板中的 [Mitel Connect] 圖格時，系統應該會自動將您重新導向，讓您登入您在 [登入 URL] 欄位中設定的預設 MiCloud Connect 應用程式。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>設定及測試與 CloudLink Platform 搭配運作的 Azure AD SSO

本節說明如何在 Azure 入口網站中啟用 CloudLink Platform 的 Azure AD SSO，以及如何設定 CloudLink Platform 帳戶以允許使用 Azure AD 來進行單一登入。

若要使用 Azure AD 的單一登入來設定 CloudLink Platform，建議您並排開啟 Azure 入口網站和 CloudLink 帳戶入口網站，因為您必須將某些資訊從 Azure 入口網站複製到 CloudLink 帳戶入口網站，反之亦然。

1. 若要在 [Azure 入口網站](https://portal.azure.com/)中開啟設定頁面：

    1. 在 **Mitel Connect** 應用程式整合頁面上，選取 [單一登入]。

       ![設定單一登入連結](common/select-sso.png)

    1. 在 [選取單一登入方法] 對話方塊中，選取 [SAML]。

       ![單一登入選取模式](common/select-saml-option.png)
    
       [SAML 型登入] 頁面隨即開啟，並顯示 [基本 SAML 設定] 區段。

       ![image](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. 若要存取 CloudLink 帳戶入口網站中的 [Azure AD 單一登入] 設定面板：

    1. 移至您要用來啟用整合的客戶帳戶所具有的 [帳戶資訊] 頁面。

    1. 在 [整合] 區段中，選取 [+ 新增]。 隨即會有快顯畫面顯示 [整合] 面板。

    1. 選取 [第三方] 索引標籤。隨即會顯示所支援的第三方應用程式清單。 選取與 [Azure AD 單一登入] 相關聯的 [新增] 按鈕，然後選取 [完成]。

       ![image](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       客戶的帳戶便會啟用 [Azure AD 單一登入]，並新增至 [帳戶資訊] 頁面的 [整合] 區段中。   

   1. 選取 [完成設定]。
    
      ![image](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      [Azure AD 單一登入] 設定面板隨即開啟。
      
       ![image](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel 建議您不要選取 [選擇性的 Mitel 認證] 區段中的 [啟用 Mitel 認證 (選擇性)] 核取方塊。 只有當您想要讓使用者除了單一登入選項之外也能使用 Mitel 認證來登入 CloudLink 應用程式時，才選取此核取方塊。

3. 在 Azure 入口網站的 [SAML 型登入] 頁面上，選取 [基本 SAML 設定] 區段中的 [編輯] 圖示。 [基本 SAML 設定] 面板隨即開啟。

    ![image](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. 從 CloudLink 帳戶入口網站中的 [Mitel 識別碼 (實體識別碼)] 欄位複製 URL，並將其貼到 Azure 入口網站中的 [識別碼 (實體識別碼)] 欄位。

 5. 從 CloudLink 帳戶入口網站中的 [回覆 URL (判斷提示取用者服務 URL)] 欄位中複製 URL，然後將其貼到 Azure 入口網站中的 [回覆 URL (判斷提示取用者服務 URL)] 欄位。  
    
    ![image](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. 在 [登入 URL] 文字方塊中，輸入 URL `https://accounts.mitel.io`，以使用 CloudLink 帳戶入口網站作為預設 Mitel 應用程式。
     
     ![image](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > 預設 Mitel 應用程式是當使用者選取存取面板中的 [Mitel Connect] 圖格時，所開啟的應用程式。 這也是使用者從 Azure AD 進行測試設定時所存取的應用程式。

7. 在 [基本 SAML 設定] 對話方塊中，選取 [儲存]。

8. 在 Azure 入口網站的 [SAML 型登入] 頁面上，從 [SAML 簽署憑證] 區段中選取 [憑證 (Base64)] 旁的 [下載]，下載 [簽署憑證]。 將憑證儲存在您的電腦上。
  
    ![image](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. 在文字編輯器中開啟簽署憑證檔案，複製檔案中的所有資料，然後將資料貼到 CloudLink 帳戶入口網站中的 [簽署憑證] 欄位。  

    > [!NOTE]
    > 如果您有多個憑證，建議您依序貼上。 
       
    ![image](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. 在 Azure 入口網站中，於 [SAML 型登入] 頁面上的 [設定 Mitel Connect] 區段：

     1. 從 [登入 URL] 欄位複製 URL，並將其貼到 CloudLink 帳戶入口網站的 [登入 URL] 欄位中。

     1. 從 [Azure AD 識別碼] 欄位複製 URL，並將其貼到 CloudLink 帳戶入口網站的 [IDP 識別碼 (實體識別碼)] 欄位中。
     
        ![image](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. 在 CloudLink 帳戶入口網站的 [Azure AD 單一登入] 面板上，選取 [儲存]。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者屬性] 對話方塊中執行下列步驟：

    ![[使用者] 對話方塊](common/user-properties.png)

    1. 在 [名稱]**** 欄位中，輸入 **BrittaSimon**。
  
    1. 在 [使用者名稱] 欄位中，輸入 brittasimon@\<yourcompanydomain\>.\<extension\>。  例如： BrittaSimon@contoso.com 。

    1. 選取 [顯示密碼]**** 核取方塊，然後記下 [密碼]**** 方塊中顯示的值。

    1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Mitel Connect 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Mitel Connect]****。

    ![應用程式清單中的 Mitel Connect 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]****，然後在 [新增指派]**** 對話方塊中選取 [使用者和群組]****。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後選擇畫面底部的 [選取]。

6. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後選擇畫面底部的 [選取]。

7. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-a-cloudlink-test-user"></a>建立 CloudLink 測試使用者

本節說明如何在 CloudLink Platform 上建立名為 **_Britta Simon_** 的測試使用者。 您必須先建立並啟動使用者，然後使用者才能使用單一登入。

如需如何在 CloudLink 帳戶入口網站中新增使用者的詳細資訊，請參閱 [CloudLink 帳戶文件](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html)中的**_管理使用者_**。

在 CloudLink 帳戶入口網站上使用下列詳細資料建立使用者：

* 名稱：Britta Simon
* 名字：Britta
* 姓氏：Simon
* 電子郵件：BrittaSimon@contoso.com

> [!NOTE]
> 使用者的 CloudLink 電子郵件地址必須與 Azure 入口網站中的 [使用者主體名稱] 相同。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD SSO 設定。

當您選取存取面板中的 [Mitel Connect] 圖格時，系統會自動將您重新導向，讓您登入您在 [登入 URL] 欄位中設定的預設 CloudLink 應用程式。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)