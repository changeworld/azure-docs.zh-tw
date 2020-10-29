---
title: 教學課程：Azure Active Directory 與 Igloo Software 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Igloo Software 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 033561ac3c4a510927691dc8db4f61196f54ec2a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460378"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>教學課程：Azure Active Directory 與 Igloo Software 整合

在此教學課程中，您將了解如何整合 Igloo Software 與 Azure Active Directory (Azure AD)。
Igloo Software 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Igloo Software 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Igloo Software (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Igloo Software 整合，您需要以下項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Igloo Software 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Igloo Software 支援由 **SP** 起始的 SSO
* Igloo Software 支援 **Just In Time** 使用者佈建

## <a name="adding-igloo-software-from-the-gallery"></a>從資源庫新增 Igloo Software

若要設定將 Igloo Software 整合到 Azure AD 中，您需要從資源庫將 Igloo Software 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Igloo Software，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Igloo Software** ，從結果面板中選取 [Igloo Software]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Igloo Software](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Igloo Software 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Igloo Software 中相關使用者之間的連結關聯性。

若要設定及測試與 Igloo Software 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Igloo Software 單一登入](#configure-igloo-software-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Igloo Software 測試使用者](#create-igloo-software-test-user)** - 在 Igloo Software 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Igloo Software 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Igloo Software]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Igloo Software 網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<company name>.igloocommmunities.com`

    b. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`https://<company name>.igloocommmunities.com/saml.digest`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Igloo Software 用戶端支援小組](https://www.igloosoftware.com/services/support)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [安裝 Igloo Software]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-igloo-software-single-sign-on"></a>設定 Igloo Software 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Igloo Software 公司網站。

2. 移至 [控制台]  。

     ![控制台](./media/igloo-software-tutorial/ic799949.png "控制台")

3. 在 [成員資格]  索引標籤中，按一下 [登入設定]  。

    ![登入設定](./media/igloo-software-tutorial/ic783968.png "登入設定")

4. 在 [SAML 設定] 區段，按一下 [設定 SAML 驗證]  。

    ![SAML 設定](./media/igloo-software-tutorial/ic783969.png "SAML 設定")

5. 在 [一般設定]  區段，執行下列步驟：

    ![一般設定](./media/igloo-software-tutorial/ic783970.png "一般設定")

    a. 在 [連接名稱]  文字方塊中，輸入組態的自訂名稱。

    b. 在 [IdP Login URL] \(IdP 登入 URL\)  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    c. 在 [IdP Logout URL] \(IdP 登出 URL\)  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    d. 選取 [登出回應與要求 HTTP 類型]  作為 [POST]  。

    e. 在從 Azure 入口網站下載的記事本檔案中開啟您的 **base-64** 編碼憑證，將憑證的內容複製到剪貼簿，再貼到 [公開憑證]  文字方塊。

6. 在 [回應與驗證設定]  ，執行以下步驟：

    ![回應與驗證設定](./media/igloo-software-tutorial/IC783971.png "回應與驗證組態")
  
    a. 在 [識別提供者]  ，選取 [Microsoft ADFS]  。

    b. 在 [識別元類型]  ，選取 [電子郵件地址]  。 

    c. 在 [電子郵件屬性]  文字方塊中，輸入 **電子郵件地址** 。

    d. 在 [名字屬性]  文字方塊中，輸入 **名字** 。

    e. 在 [姓氏屬性]  文字方塊中，輸入 **姓氏** 。

7. 執行以下步驟來完成設定：

    ![登入時建立使用者](./media/igloo-software-tutorial/IC783972.png "登入時建立使用者") 

    a. 在 [登入時建立使用者]  ，選取 [使用者登入您的網站時建立新的使用者]  。

    b. 在 [登入設定]  ，選取 [在「登入」畫面使用 SAML 按鈕]  。

    c. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Igloo Software 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Igloo Software]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Igloo Software]  。

    ![應用程式清單中的 [Igloo Software] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-igloo-software-test-user"></a>建立 Igloo Software 測試使用者

沒有動作項目可讓您設定  Igloo Software 使用者佈建。  

當已指派的使用者透過存取面板嘗試登入 Igloo Software 時，Igloo Software 會檢查使用者是否存在。  如果尚無可用的使用者帳戶，Igloo Software 會自動予以建立。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Igloo Software] 圖格時，應該會自動登入您已設定 SSO 的 Igloo Software。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)