---
title: 教學課程：Azure Active Directory 與 XaitPorter 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 XaitPorter 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: bdea7fc3c295016204a80772bdf367dda7e194fb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546299"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>教學課程：Azure Active Directory 與 XaitPorter 整合

在本教學課程中，您將了解如何整合 XaitPorter 與 Azure Active Directory (Azure AD)。
XaitPorter 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 XaitPorter 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 XaitPorter (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 XaitPorter 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 XaitPorter 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* XaitPorter 支援由 **SP** 起始的 SSO

## <a name="adding-xaitporter-from-the-gallery"></a>從資源庫新增 XaitPorter

若要設定 XaitPorter 與 Azure AD 整合，您需要從資源庫將 XaitPorter 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 XaitPorter，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **XaitPorter**，從結果面板中選取 [XaitPorter]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 XaitPorter](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 XaitPorter 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 XaitPorter 中相關使用者之間的連結關聯性。

若要設定及測試與 XaitPorter 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 XaitPorter 單一登入](#configure-xaitporter-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 XaitPorter 測試使用者](#create-xaitporter-test-user)** - 在 XaitPorter 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表該使用者的項目連結。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 XaitPorter 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [XaitPorter]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![XaitPorter 網域及 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.xaitporter.com/saml/login`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [XaitPorter 用戶端支援小組](https://www.xait.com/support/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

6. 將 **IP 位址**或**應用程式同盟中繼資料 Url** 提供給 [SmartRecruiters 支援小組](https://www.smartrecruiters.com/about-us/contact-us/)，讓 XaitPorter 自行設定允許清單，以確保可以從您 XaitPorter 執行個體可觸達 IP 位址。 

### <a name="configure-xaitporter-single-sign-on"></a>設定 XaitPorter 單一登入

1. 若要自動執行 XaitPorter 內的設定，您必須按一下 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器擴充功能」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 XaitPorter]  便會將您導向到 XaitPorter 應用程式。 請從該處提供用以登入 XaitPorter 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 XaitPorter，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 XaitPorter 公司網站，然後執行下列步驟：

4. 按一下 [管理員]  。

    ![設定單一登入](./media/xaitporter-tutorial/user1.png)

5. 從 [系統安裝] 下拉式清單中，選取 [管理單一登入]。

    ![設定單一登入](./media/xaitporter-tutorial/user2.png)

6. 在 [管理單一登入]  區段中，執行下列步驟：

    ![設定單一登入](./media/xaitporter-tutorial/user3.png)

    a. 選取 [啟用單一登入驗證]  。

    b. 在 [識別提供者設定]  文字方塊中，貼上您從 Azure 入口網站複製的**應用程式同盟中繼資料 Url**，然後按一下 [擷取]  。

    c. 選取 [啟用自動建立使用者]  。

    d. 按一下 [確定]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 brittasimon@yourcompanydomain.extension。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 XaitPorter 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [XaitPorter]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [XaitPorter]  。

    ![應用程式清單中的 XaitPorter 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-xaitporter-test-user"></a>建立 XaitPorter 測試使用者

在本節中，您會在 XaitPorter 中建立名為 Britta Simon 的使用者。 與 [XaitPorter 用戶端支援小組](https://www.xait.com/support/)合作，在 XaitPorter 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 XaitPorter 圖格時，應該會自動登入您設定 SSO 的 XaitPorter。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

