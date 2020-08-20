---
title: 教學課程：Azure Active Directory 與 Signagelive 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Signagelive 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: 5a8fe24e5fa1935b2363ed5d2c15c72af8af11ff
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539720"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>教學課程：Azure Active Directory 與 Signagelive 整合

在本教學課程中，您會了解如何整合 Signagelive 與 Azure Active Directory (Azure AD)。
Signagelive 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制誰有 Signagelive 的存取權。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Signagelive (單一登入)。
* 您可以集中管理您的帳戶：Azure 入口網站。

如需 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Signagelive 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以取得[一個月的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 已啟用 Signagelive 單一登入的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Signagelive 支援郵 SP 起始的 SSO。

## <a name="add-signagelive-from-the-gallery"></a>從資源庫新增 Signagelive

若要設定 Signagelive 與 Azure AD 整合，先從資源庫將 Signagelive 新增到受控 SaaS App 清單。

若要從資源庫新增 Signagelive，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Signagelive**。 

     ![結果清單中的 Signagelive](common/search-new-app.png)

5. 從結果面板中選取 [Signagelive]  ，然後選取 [新增]  按鈕以新增應用程式。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Signagelive 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 Signagelive 中相關使用者之間的連結。

若要設定及測試與 Signagelive 搭配運作的 Azure AD 單一登入，請先完成下列建構元素：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [設定 Signagelive 單一登入](#configure-signagelive-single-sign-on)，在應用程式端設定單一登入設定。
3. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [建立 Signagelive 測試使用者](#create-a-signagelive-test-user)，讓 Signagelive 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
6. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Signagelive 搭配運作的 Azure AD 單一登入功能，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Signagelive]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上選取 [編輯]  ，以開啟 [基本 SAML 組態]  對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段中，執行下列步驟：

    ![Signagelive 網域與 URL 單一登入資訊](common/sp-signonurl.png)

    在 [登入 URL]  方塊中，輸入採用下列模式的 URL：`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的登入 URL 來更新此值。 若要取得此值，請連絡 [Signagelive 用戶端支援小組](mailto:support@signagelive.com)。 您也可以參考 Azure 入口網站中 [基本 SAML 組態]  區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，選取 [下載]  ，以依據您的需求從指定的選項下載 [憑證 (原始)]  。 然後將它儲存在您的電腦上。

    ![憑證下載連結](common/certificateraw.png)

6. 在 [設定 Signagelive]  區段中，複製您所需的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-signagelive-single-sign-on"></a>設定 Signagelive 單一登入

若要設定 Signagelive 端的單一登入，請將從 Azure 入口網站下載的 [憑證 (原始)]  和複製的 URL 傳送給 [Signagelive 支援小組](mailto:support@signagelive.com)。 他們會確保兩端的 SAML SSO 連線已正確設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者]  對話方塊中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 "brittasimon@yourcompanydomain.extension"。 例如，在此情況下，您可以輸入 "BrittaSimon@contoso.com"。

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Signagelive 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Signagelive]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Signagelive]  。

    ![應用程式清單中的 Signagelive 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]  按鈕。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者]  清單中，選取 [Britta Simon]  。 然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 接著，按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，選取 [指派]  按鈕。

### <a name="create-a-signagelive-test-user"></a>建立 Signagelive 測試使用者

在本節中，您要在 Signagelive 中建立名為 Britta Simon 的使用者。 請與 [Signagelive 支援小組](mailto:support@signagelive.com)合作，以在 Signagelive 平台中新增使用者。 您必須先建立和啟動使用者，才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

當您在 MyApps 入口網站中選取 **Signagelive** 圖格時，您應該會自動登入。 如需 MyApps 入口網站的詳細資訊，請參閱[什麼是 MyApps 入口網站？](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

