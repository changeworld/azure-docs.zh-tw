---
title: 教學課程：Azure Active Directory 與 Mitel Connect 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Mitel Connect 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 26a761708f56ff7aba8daf86d2991579e60291cb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81870203"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>教學課程：Azure Active Directory 與 Mitel MiCloud Connect 整合

在本教學課程中，您會了解如何將 Mitel MiCloud Connect 與 Azure Active Directory (Azure AD) 進行整合。 將 MiCloud Connect 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控管誰可以使用其企業認證存取 MiCloud Connect 應用程式。
* 您可以讓帳戶上的使用者使用其 Azure AD 帳戶自動登入 MiCloud Connect (單一登入)。


若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定與 MiCloud Connect 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶

  如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* Mitel MiCloud Connect 帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會設定和測試 Azure AD 單一登入 (SSO)。

* Mitel Connect 支援由 **SP** 起始的 SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>從資源庫新增 Mitel Connect

若要設定將 Mitel Connect 整合到 Azure AD 中，您需要從資源庫將 Mitel Connect 新增到 Azure 入口網站中的受控 SaaS 應用程式清單。

**若要從資源庫新增 Mitel Connect，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 按一下 [企業應用程式]  ，然後按一下 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 按一下 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋欄位中輸入 [Mitel Connect]  ，並從結果面板中按一下 [Mitel Connect]  ，然後按一下 [新增]  。

     ![結果清單中的 Mitel Connect](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 MiCloud Connect 搭配運作的 Azure AD 單一登入。 若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 MiCloud Connect 中相關使用者之間的連結關聯性。

若要設定及測試與 MiCloud Connect 搭配運作的 Azure AD 單一登入，您需要完成下列步驟：

1. **[設定 MiCloud Connect 以搭配 Azure AD 使用 SSO](#configure-micloud-connect-for-sso-with-azure-ad)** - 讓使用者能夠使用此功能，並在應用程式端進行 SSO 設定。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
4. **[建立 Mitel MiCloud Connect 測試使用者](#create-a-mitel-micloud-connect-test-user)** - 在 MiCloud Connect 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
5. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>設定 MiCloud Connect 以搭配 Azure AD 使用 SSO

在本節中，您將在 Azure 入口網站中為 MiCloud Connect 啟用 Azure AD 單一登入，並使用 Azure AD 將 MiCloud Connect 帳戶設定為可使用 SSO。

若要針對 Azure AD 設定 MiCloud Connect 的 SSO，就最簡單的方式是並排開啟 Azure 入口網站和 Mitel 帳戶入口網站。 您將必須在 Azure 入口網站和 Mitel 帳戶入口網站之間來回複製一些資訊。


1. 若要在 [Azure 入口網站](https://portal.azure.com/)中開啟設定頁面，請執行下列動作：

    a. 在 **Mitel Connect** 應用程式整合頁面上，按一下 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

    b. 在 [選取單一登入方法]  對話方塊中，按一下 [SAML]  。

    ![單一登入選取模式](common/select-saml-option.png)
    
    SAML 登入頁面會隨即顯示。

2. 若要在 Mitel 帳戶入口網站中開啟設定對話方塊，請執行下列動作：

    a. 在 [電話系統]  功能表上，按一下 [附加元件功能]  。

    b. 在 [單一登入]  右側，按一下 [啟動]  或 [設定]  。
    
    [Connect 單一登入設定] 對話方塊會隨即出現。
    
3. 選取 [啟用單一登入]  核取方塊。
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. 在 Azure 入口網站中，按一下 [基本 SAML 組態]  區段中的 [編輯]  圖示。
    ![image](common/edit-urls.png)

    [基本 SAML 組態] 對話方塊會隨即出現。

5.  從 Mitel 帳戶入口網站中的 [Mitel 識別碼 (實體識別碼)]  欄位複製 URL，並將其貼到 Azure 入口網站中的 [識別碼 (實體識別碼)]  欄位。

6. 從 Mitel 帳戶入口網站中的 [回覆 URL (判斷提示取用者服務 URL)]  欄位中複製 URL，然後將其貼到 Azure 入口網站中的 [回覆 URL (判斷提示取用者服務 URL)]  欄位。  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. 在 [登入 URL]  文字方塊中，輸入下列其中一個 URL：

    * **https://portal.shoretelsky.com** - 使用 Mitel 帳戶入口網站作為您預設的 Mitel 應用程式
    * **https://teamwork.shoretel.com** - 使用 Teamwork 作為您預設的 Mitel 應用程式

    **注意**：預設 Mitel 應用程式是當使用者按一下存取面板中的 [Mitel Connect] 圖格時，所存取的應用程式。 這也是從 Azure AD 進行測試設定時所存取的應用程式。

8. 在 Azure 入口網站的 [基本 SAML 組態]  對話方塊中，按一下 [儲存]  。

9. 在 Azure 入口網站的 [SAML 登入]  頁面上，從 [SAML 簽署憑證]  區段中按一下 [憑證 (Base64)]  旁的 [下載]  ，下載 [簽署憑證]  並將其儲存在您的電腦中。
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. 在文字編輯器中開啟簽署憑證檔案，複製檔案中的所有資料，然後將資料貼到 Mitel 帳戶入口網站中的 [簽署憑證]  欄位。 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. 在 Azure 入口網站中，從 [SAML 登入]  頁面上的 [設定 Mitel Connect]  區段執行下列動作：

    a. 從 [登入 URL]  欄位複製 URL，並將其貼到 Mitel 帳戶入口網站的 [登入 URL]  欄位中。

    b. 從 [Azure AD 識別碼]  欄位複製 URL，並將其貼到 Mitel 帳戶入口網站的 [實體識別碼]  欄位中。
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. 在 Mitel 帳戶入口網站中的 [Connect 單一登入設定]  對話方塊上按一下 [儲存]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序按一下 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端按一下 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者屬性] 對話方塊中執行下列步驟：

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 brittasimon@\<yourcompanydomain\>.\<副檔名\>。  
例如： BrittaSimon@contoso.com 。

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Mitel Connect 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，按一下 [企業應用程式]  ，然後按一下 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，按一下 [Mitel Connect]  。

    ![應用程式清單中的 Mitel Connect 連結](common/all-applications.png)

3. 在左側功能表中，按一下 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  ，然後在 [新增指派]  對話方塊中按一下 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者]  清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  。

6. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  。

### <a name="create-a-mitel-micloud-connect-test-user"></a>建立 Mitel MiCloud Connect 測試使用者

在本節中，您會在 MiCloud Connect 帳戶中建立名為 Britta Simon 的使用者。 必須先建立和啟動使用者，才能使用單一登入。

如需有關在 Mitel 帳戶入口網站新增使用者的詳細資訊，請參閱 Mitel 知識庫中的[新增使用者](https://oneview.mitel.com/s/article/Adding-a-User-092815)文章。

使用下列詳細資料在 MiCloud Connect 帳戶上建立使用者：

  * **名稱：** Britta Simon

* **公司電子郵件地址：** `brittasimon@<yourcompanydomain>.<extension>`   
(範例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **使用者名稱：** `brittasimon@<yourcompanydomain>.<extension>`  
(範例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com)；使用者的使用者名稱通常與使用者的公司電子郵件地址相同)

**注意：** 使用者的 MiCloud Connect 使用者名稱必須與 Azure 中的使用者電子郵件地址相同。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下存取面板中的 [Mitel Connect] 圖格時，系統應該會自動將您重新導向，讓您登入您在 [登入 URL]  欄位中設定的預設 MiCloud Connect 應用程式。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
