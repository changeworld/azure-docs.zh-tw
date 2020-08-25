---
title: 教學課程：Azure Active Directory 與 Kontiki 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Kontiki 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: cb652cfdae0ce785d6a076b8b1592d9f700b3383
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549954"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>教學課程：Azure Active Directory 與 Kontiki 整合

在本教學課程中，您會了解如何整合 Kontiki 與 Azure Active Directory (Azure AD)。

Kontiki 與 Azure AD 整合提供下列優點：

* 您可以使用 Azure AD 來控制可存取 Kontiki 的人員。
* 使用者可以使用其 Azure AD 帳戶自動登入 Kontiki (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

如需軟體即服務 (SaaS) 應用程式與 Azure AD 的整合詳細資訊，請參閱 [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Kontiki 的整合作業，需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入的 Kontiki 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入並整合 Kontiki 與 Azure AD。

Kontiki 支援下列功能︰

* **SP 起始的單一登入**
* **Just-in-Time 使用者佈建**

## <a name="add-kontiki-in-the-azure-portal"></a>在 Azure 入口網站中新增 Kontiki

若要整合 Kontiki 與 Azure AD，您必須將 Kontiki 新增到受控 SaaS 應用程式清單。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側功能表中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 選項](common/select-azuread.png)

1. 選取 [企業應用程式]   > [所有應用程式]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 若要新增應用程式，請選取 [新增應用程式]  。

    ![新增應用程式選項](common/add-new-app.png)

1. 在搜尋方塊中，輸入 **Kontiki**。 在搜尋結果中，選取 [Kontiki]  ，然後選取 [新增]  。

    ![結果清單中的 Kontiki](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Kontiki 搭配運作的 Azure AD 單一登入。 若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 Kontiki 中相關使用者之間的連結關聯性。

若要設定及測試與 Kontiki 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

| Task | 描述 |
| --- | --- |
| **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** | 讓使用者能夠使用此功能。 |
| **[設定 Kontiki 單一登入](#configure-kontiki-single-sign-on)** | 在應用程式中設定單一登入設定。 |
| **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** | 為名為 Britta Simon 的使用者測試 Azure AD 單一登入。 |
| **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** | 讓 Britta Simon 能夠使用 Azure AD 單一登入。 |
| **[建立 Kontiki 測試使用者](#create-a-kontiki-test-user)** | 在 Kontiki 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。 |
| **[測試單一登入](#test-single-sign-on)** | 驗證組態是否能運作。 |

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中設定與 Kontiki 搭配運作的 Azure AD 單一登入。

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Kontiki]  應用程式整合窗格上，選取 [單一登入]  。

    ![設定單一登入選項](common/select-sso.png)

1. 在 [選取單一登入方法]  窗格中，選取 [SAML]  或 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

1. 在 [以 SAML 設定單一登入]  窗格中選取 [編輯]  (鉛筆圖示)，以開啟 [基本 SAML 組態]  窗格。

    ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  窗格的 [登入 URL]  文字方塊中，輸入具有下列模式的 URL：`https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki 網域與 URL 單一登入資訊](common/sp-signonurl.png)

    > [!NOTE]
    > 請連絡 [Kontiki 用戶端支援小組](https://customersupport.kontiki.com/enterprise/contactsupport.html)以取得要使用的正確值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入] 窗格的 [SAML 簽署憑證] 區段中，選取 [同盟中繼資料 XML] 旁邊的 [下載]。 根據您的需求選取下載選項。 將憑證儲存在您的電腦上。

    ![同盟中繼資料 XML 憑證下載選項](common/metadataxml.png)

1. 在 [設定 Kontiki]  區段中，根據您的需求複製下列 URL：

    * 登入 URL
    * Azure AD 識別碼
    * 登出 URL

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>設定 Kontiki 單一登入

若要在 Kontiki 端設定單一登入，請將您從 Azure 入口網站下載的同盟中繼資料 XML 檔案和所複製的相關 URL 傳送給 [Kontiki 支援小組](https://customersupport.kontiki.com/enterprise/contactsupport.html)。 Kontiki 支援小組會使用您傳送給他們的資訊，確保兩端的 SAML 單一登入連線已正確設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。

    ![[使用者] 和 [所有使用者] 選項](common/users.png)

1. 選取 [新增使用者]  。

    ![[新增使用者] 選項](common/new-user.png)

1. 在 [使用者]  窗格中，完成下列步驟：

    1. 在 [名稱]  方塊中，輸入 **BrittaSimon**。
  
    1. 在 [使用者名稱] 方塊中，輸入**brittasimon\@\<your-company-domain>.\<extension>** 。 例如，**brittasimon\@contoso.com**。

    1. 選取 [顯示密碼]  核取方塊。 記下 [密碼]  方塊中顯示的值。

    1. 選取 [建立]  。

    ![[使用者] 窗格](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Kontiki 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]   > [Kontiki]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Kontiki]  。

    ![應用程式清單中的 Kontiki](common/all-applications.png)

1. 在功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 選項](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 然後在 [新增指派]  窗格中，選取 [使用者和群組]  。

    ![新增指派窗格](common/add-assign-user.png)

1. 在 [使用者和群組]  窗格中，選取使用者清單中的 [Britta Simon]  。 選擇 [選取]  。

1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  窗格的清單中選取相關的使用者角色。 選擇 [選取]  。

1. 在 [新增指派]  窗格中，選取 [指派]  。

### <a name="create-a-kontiki-test-user"></a>建立 Kontiki 測試使用者

沒有動作項目可讓您設定 Kontiki 中的使用者佈建。 當指派的使用者嘗試使用「我的應用程式」入口網站登入 Kontiki 時，Kontiki 會檢查使用者是否存在。 如果不找到任何使用者帳戶，Kontiki 會自動建立使用者帳戶。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

設定單一登入之後，當您在「我的應用程式」入口網站中選取 [Kontiki]  時，您會自動登入 Kontiki。 如需「我的應用程式」入口網站的詳細資訊，請參閱[在「我的應用程式」入口網站中存取和使用應用程式](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解，請檢閱下列文章：

- [用於整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
