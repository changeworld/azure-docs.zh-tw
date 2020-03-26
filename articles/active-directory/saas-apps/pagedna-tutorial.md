---
title: 教學課程：Azure Active Directory 與 PageDNA 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 PageDNA 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "68227471"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>教學課程：Azure Active Directory 與 PageDNA 整合

在本教學課程中，您會了解如何整合 PageDNA 與 Azure Active Directory (Azure AD)。

PageDNA 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 PageDNA 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 PageDNA (單一登入)。
* 您可以集中管理您的帳戶：Azure 入口網站。

如需軟體即服務 (SaaS) 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 PageDNA 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入的 PageDNA 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入並整合 PageDNA 與 Azure AD。

PageDNA 支援下列功能︰

* SP 起始的單一登入 (SSO)。

* Just-In-Time 使用者佈建。

## <a name="add-pagedna-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 PageDNA

若要設定將 PageDNA 整合到 Azure AD 中，您需要從 Azure Marketplace 將 PageDNA 新增到受控 SaaS 應用程式清單：

1. 登入 [Azure 入口網站](https://portal.azure.com?azure-portal=true)。
1. 在左窗格中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 選項](common/select-azuread.png)

1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 若要新增應用程式，請選取窗格頂端的 [+ 新增應用程式]  。

    ![新增應用程式選項](common/add-new-app.png)

1. 在搜尋方塊中，輸入 **PageDNA**。 在搜尋結果中，選取 [PageDNA]  ，然後選取 [新增]  以新增應用程式。

    ![結果清單中的 PageDNA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 PageDNA 搭配運作的 Azure AD 單一登入。 若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 PageDNA 中相關使用者之間的連結。

若要設定及測試與 PageDNA 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** ，讓您的使用者能夠使用此功能。
1. **[設定 PageDNA 單一登入](#configure-pagedna-single-sign-on)** - 在應用程式端設定單一登入設定。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 Britta Simon 測試 Azure AD 單一登入。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[建立 PageDNA 測試使用者](#create-a-pagedna-test-user)** ，PageDNA 中便有名為 Britta Simon 的使用者，其已連結至名為 Britta Simon 的 Azure AD 使用者。
1. **[測試單一登入](#test-single-sign-on)** 驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 PageDNA 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [PageDNA]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入選項](common/select-sso.png)

1. 在 [選取單一登入方法]  窗格中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

1. 在 [以 SAML 設定單一登入]  窗格上選取 [編輯]  (鉛筆圖示)，以開啟 [基本 SAML 組態]  窗格。

    ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  窗格中，執行下列步驟：

    ![PageDNA 網域與 URL 單一登入資訊](common/sp-identifier.png)

    1. 在 [登入 URL]  方塊中，以下列其中一種模式輸入 URL：

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. 在 [識別碼 (實體 ID)]  方塊中，以下列其中一種模式輸入 URL：

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 若要取得這些值，請連絡 [PageDNA 支援小組](mailto:success@pagedna.com)。 您也可以參考 Azure 入口網站中 [基本 SAML 組態]  窗格所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，選取 [下載]  以從指定的選項下載 [憑證 (原始)]  並儲存在電腦上。

    ![憑證 (原始) 下載選項](common/certificateraw.png)

1. 在 [設定 PageDNA]  區段中，複製您所需的一或多個 URL：

   * **登入 URL**
   * **Azure AD 識別碼**
   * **登出 URL**

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>設定 PageDNA 單一登入

若要在 PageDNA 端設定單一登入，請將從 Azure 入口網站下載的 [憑證 (原始)] 和複製的適當 URL 傳送給 [PageDNA 支援小組](mailto:success@pagedna.com)。 PageDNA 小組會確保兩端的 SAML SSO 連線已正確設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站中，於左窗格選取 [Azure Active Directory]     > [使用者]   > [所有使用者]  。

    ![[使用者] 和 [所有使用者] 選項](common/users.png)

1. 選取畫面頂端的 [+ 新增使用者]  。

    ![新增使用者選項](common/new-user.png)

1. 在 [使用者]  窗格中，執行下列步驟：

    ![[使用者] 窗格](common/user-properties.png)

    1. 在 [名稱]  方塊中，輸入 **BrittaSimon**。
  
    1. 在 [使用者名稱]  方塊中，輸入 **BrittaSimon\@\<yourcompanydomain>.\<extension>** 。 例如 **BrittaSimon\@contoso.com**。

    1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。

    1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 PageDNA 的存取權授與使用者 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]   > [PageDNA]  。

    ![企業應用程式窗格](common/enterprise-applications.png)

1. 在應用程式清單中，選取 **PageDNA**。

    ![應用程式清單中的 PageDNA](common/all-applications.png)

1. 在左側窗格中，選取 [管理]  之下的 [使用者和群組]  。

    ![[使用者和群組] 選項](common/users-groups-blade.png)

1. 選取 [+ 新增使用者]  ，然後在 [新增指派]  窗格中選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

1. 在 [使用者和群組]  窗格中，選取 [使用者]  清單中的 [Britta Simon]  ，然後選擇窗格底部的 [選取]  。

1. 如果您預期使用 SAML 判斷提示中的角色值，則在 [選取角色]  窗格的清單中選取適當的使用者角色。 選擇窗格底部的 [選取]  。

1. 在 [新增指派]  窗格中，選取 [指派]  。

### <a name="create-a-pagedna-test-user"></a>建立 PageDNA 測試使用者

PageDNA 中現在會建立名為 Britta Simon 的使用者。 您不需要採取任何動作來建立此使用者。 PageDNA 支援依預設啟用的 Just-In-Time 使用者佈建。 如果 PageDNA 中還沒有名為 Britta Simon 的使用者存在，在驗證之後就會建立新的使用者。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

當您在 My Apps 入口網站中選取 [PageDNA]  時，應該會自動登入您已設定單一登入的 PageDNA 訂用帳戶。 如需 My Apps 入口網站的詳細資訊，請參閱[在 My Apps 入口網站上存取和使用應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

* [整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

