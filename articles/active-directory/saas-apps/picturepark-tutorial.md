---
title: 教學課程：Azure Active Directory 與 Picturepark 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Picturepark 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: bb12b666315f6d30df64b33b6a87d6ce831fd225
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553832"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>教學課程：Azure Active Directory 與 Picturepark 整合

在本教學課程中，您會了解如何整合 Picturepark 與 Azure Active Directory (Azure AD)。
Picturepark 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Picturepark 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Picturepark (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Picturepark 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Picturepark 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Picturepark 支援由 **SP** 起始的 SSO

## <a name="adding-picturepark-from-the-gallery"></a>從資源庫新增 Picturepark

若要設定將 Picturepark 整合到 Azure AD 中，您需要從資源庫將 Picturepark 新增到受控的 SaaS 應用程式清單。

**若要從資源庫新增 Picturepark，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Picturepark**，並從結果面板中選取 [Picturepark]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Picturepark](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Picturepark 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Picturepark 中相關使用者之間的連結關聯性。

若要設定及測試與 Picturepark 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Picturepark 單一登入](#configure-picturepark-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Picturepark 測試使用者](#create-picturepark-test-user)** - 使 Picturepark 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Picturepark 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Picturepark]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Picturepark 網域和 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<companyname>.picturepark.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Picturepark 客戶支援小組](https://picturepark.com/company/picturepark-customer-support)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [SAML 簽署憑證]  區段中，按一下 [編輯]  按鈕以開啟 [SAML 簽署憑證]  對話方塊。

    ![編輯 SAML 簽署憑證](common/edit-certificate.png)

6. 在 [SAML 簽署憑證]  區段中，複製 [指紋]  並儲存在您的電腦上。

    ![複製指紋值](common/copy-thumbprint.png)

7. 在 [設定 Picturepark]  區段上，依據您的需求複製適當的 URL。 針對 [登入 URL]  ，請使用下列格式的值：`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ 是 Azure AD 訂用帳戶的租用戶識別碼。

    ![複製組態 URL](./media/picturepark-tutorial/configurls.png)

    a. Azure AD 識別碼

    b. 登出 URL

### <a name="configure-picturepark-single-sign-on"></a>設定 Picturepark 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Picturepark 公司網站。

2. 在最上面的工具列中，按一下 [系統管理工具]  ，然後按一下 [管理主控台]  。
   
    ![管理主控台](./media/picturepark-tutorial/ic795062.png "管理主控台")

3. 按一下 [驗證]  ，然後按一下 [識別提供者]  。
   
    ![驗證](./media/picturepark-tutorial/ic795063.png "驗證")

4. 在 [識別提供者組態]  區段中，執行下列步驟：
   
    ![識別提供者組態](./media/picturepark-tutorial/ic795064.png "識別提供者組態")
   
    a. 按一下 [新增]  。
  
    b. 輸入組態的名稱。
   
    c. 選取 [設定為預設值]  。
   
    d. 在 [簽發者 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。
   
    e. 在 [信任的簽發者指紋] 文字方塊中，貼上您從 [SAML 簽署憑證] 區段複製的 [指紋] 值。 

5. 按一下 [JoinDefaultUsersGroup]  。

6. 若要在 [宣告] 文字方塊中設定 [Emailaddress] 屬性，請輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`，然後按一下 [儲存]。

      ![組態](./media/picturepark-tutorial/ic795065.png "組態")

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Picturepark 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Picturepark]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Picturepark]  。

    ![應用程式清單中的 Picturepark 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-picturepark-test-user"></a>建立 Picturepark 測試使用者

若要讓 Azure AD 使用者能夠登入 Picturepark，必須將他們佈建到 Picturepark。 Picturepark 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **Picturepark** 租用戶。

1. 在最上面的工具列中，按一下 [系統管理工具]  ，然後按一下 [使用者]  。
   
    ![使用者](./media/picturepark-tutorial/ic795067.png "使用者")

1. 在 [使用者總覽]  索引標籤中，按一下 [新增]  。
   
    ![使用者管理](./media/picturepark-tutorial/ic795068.png "使用者管理")

1. 在 [建立使用者]  對話方塊中，針對您想要佈建的有效 Azure Active Directory 使用者，執行下列步驟：
   
    ![建立使用者](./media/picturepark-tutorial/ic795069.png "建立使用者")
   
    a. 在 [電子郵件地址] 文字方塊中，輸入使用者 `BrittaSimon@contoso.com` 的**電子郵件地址**。  
   
    b. 在 [密碼]  和 [確認密碼]  文字方塊中，輸入 BrittaSimon 的**密碼**。 
   
    c. 在 [名字]  文字方塊中，輸入使用者的**名字**：**Britta**。 
   
    d. 在 [姓氏]  文字方塊中，輸入使用者的**姓氏**：**Simon**。
   
    e. 在 [公司]  文字方塊中，輸入使用者的**公司名稱**。 
   
    f. 在 [國家/地區]  文字方塊中，選取使用者的**國家/地區**。
  
    g. 在 [ZIP]  文字方塊中，輸入縣/市的**郵遞區號**。
   
    h. 在 [縣/市]  文字方塊中，輸入使用者的**縣/市名稱**。

    i. 選取 [語言]  。
   
    j. 按一下頁面底部的 [新增]  。

>[!NOTE]
>您可以使用任何其他的 Picturepark 使用者帳戶建立工具，或 Picturepark 提供的 API，以佈建 Azure AD 使用者帳戶。
>

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Picturepark] 圖格時，應該會自動登入您已設定 SSO 的 Picturepark。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

