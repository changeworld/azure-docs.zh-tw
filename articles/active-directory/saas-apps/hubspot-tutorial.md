---
title: 教學課程：Azure Active Directory 與 HubSpot 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 HubSpot 之間的單一登入。
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
ms.openlocfilehash: 51e27526b10bcdd74d6e2bffb8bf620d7b022aac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551432"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>教學課程：Azure Active Directory 與 HubSpot 整合

在本教學課程中，您將了解如何整合 HubSpot 與 Azure Active Directory (Azure AD)。

HubSpot 與 Azure AD 整合提供下列優點：

* 您可以使用 Azure AD 來控制可存取 HubSpot 的人員。
* 使用者可以使用其 Azure AD 帳戶自動登入 HubSpot (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

如需軟體即服務 (SaaS) 應用程式與 Azure AD 的整合詳細資訊，請參閱 [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要設定與 HubSpot 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入的 HubSpot 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入並整合 HubSpot 與 Azure AD。

HubSpot 支援下列功能︰

* **SP 起始的單一登入**
* **由 IDP 起始的單一登入**

## <a name="add-hubspot-in-the-azure-portal"></a>在 Azure 入口網站中新增 HubSpot

若要整合 HubSpot 與 Azure AD，您必須將 HubSpot 新增到受控 SaaS 應用程式清單。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側功能表中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 選項](common/select-azuread.png)

1. 選取 [企業應用程式]   > [所有應用程式]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 若要新增應用程式，請選取 [新增應用程式]  。

    ![新增應用程式選項](common/add-new-app.png)

1. 在搜尋方塊中，輸入 **HubSpot**。 在搜尋結果中，選取 [HubSpot]  ，然後選取 [新增]  。

    ![結果清單中的 HubSpot](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 HubSpot 搭配運作的 Azure AD 單一登入。 若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 HubSpot 中相關使用者之間的連結關聯性。

若要設定及測試與 HubSpot 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

| Task | 描述 |
| --- | --- |
| **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** | 讓使用者能夠使用此功能。 |
| **[設定 HubSpot 單一登入](#configure-hubspot-single-sign-on)** | 在應用程式中設定單一登入設定。 |
| **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** | 為名為 Britta Simon 的使用者測試 Azure AD 單一登入。 |
| **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** | 讓 Britta Simon 能夠使用 Azure AD 單一登入。 |
| **[建立 HubSpot 測試使用者](#create-a-hubspot-test-user)** | 在 HubSpot 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。 |
| **[測試單一登入](#test-single-sign-on)** | 驗證組態是否能運作。 |

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中設定與 HubSpot 搭配運作的 Azure AD 單一登入。

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [HubSpot]  應用程式整合窗格中，選取 [單一登入]  。

    ![設定單一登入選項](common/select-sso.png)

1. 在 [選取單一登入方法]  窗格中，選取 [SAML]  或 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

1. 在 [以 SAML 設定單一登入]  窗格中選取 [編輯]  (鉛筆圖示)，以開啟 [基本 SAML 組態]  窗格。

    ![編輯基本 SAML 組態](common/edit-urls.png)

1. 若要在 [基本 SAML 組態]  窗格中設定「IDP 起始模式」  ，請完成下列步驟：

    1. 在**識別碼**方塊中輸入具有下列模式的 URL：https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>。

    1. 在**回覆 URL** 方塊中輸入具有下列模式的 URL：https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>。

    ![HubSpot 網域與 URL 單一登入資訊](common/idp-intiated.png)

    > [!NOTE]
    > 若要格式化 URL，您也可以參考 Azure 入口網站中 [基本 SAML 組態]  窗格所示的模式。

1. 若要在「SP 起始的模式」  中設定應用程式：

    1. 選取 [設定其他 URL]  。

    1. 在 [登入 URL]  方塊中，輸入 **https:\//app.hubspot.com/login**。

    ![[設定其他 URL] 選項](common/metadata-upload-additional-signon.png)

1. 在 [以 SAML 設定單一登入]  窗格的 [SAML 簽署憑證]  區段中，選取 [憑證 (Base64)]  旁邊的 [下載]  。 根據您的需求選取下載選項。 將憑證儲存在您的電腦上。

    ![憑證 (Base64) 下載選項](common/certificatebase64.png)

1. 在 [設定 HubSpot]  區段中，根據您的需求複製下列 URL：

    * 登入 URL
    * Azure AD 識別碼
    * 登出 URL

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>設定 HubSpot 單一登入

1. 在瀏覽器中開啟新索引標籤，登入您的 HubSpot 系統管理員帳戶。

1. 選取頁面右上角的 [設定]  圖示。

    ![HubSpot 中的設定圖示](./media/hubspot-tutorial/config1.png)

1. 選取 [帳戶預設值]  。

    ![HubSpot 中的帳戶預設值選項](./media/hubspot-tutorial/config2.png)

1. 向下捲動至 [安全性]  區段，然後選取 [設定]  。

    ![HubSpot 中的設定選項](./media/hubspot-tutorial/config3.png)

1. 在 [設定單一登入]  區段中，完成下列步驟：

    1. 在 [對象 URl (服務提供者實體識別碼)]  方塊中，選取 [複製]  來複製值。 在 Azure 入口網站的 [基本 SAML 組態]  窗格中，將值貼入 [識別碼]  方塊。

    1. 在 [登入 URl、ACS、收件者或重新導向]  方塊中，選取 [複製]  來複製值。 在 Azure 入口網站的 [基本 SAML 組態]  窗格中，將值貼入 [回覆 URL]  方塊。

    1. 在 HubSpot 中的 [識別提供者識別碼或簽發者 URL]  方塊中，貼上您在 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    1. 在 HubSpot 中的 [識別提供者單一登入 URL]  方塊中，貼上您在 Azure 入口網站複製的 [登入 URL]  值。

    1. 在 Windows 的「記事本」中，開啟您下載的憑證 (Base64) 檔案。 選取並複製該檔案的內容。 然後，將其貼入 HubSpot 中的 [X.509 憑證]  方塊。

    1. 選取 [驗證]  。

        ![HubSpot 中的設定單一登入區段](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。

    ![[使用者] 和 [所有使用者] 選項](common/users.png)

1. 選取 [新增使用者]  。

    ![[新增使用者] 選項](common/new-user.png)

1. 在 [使用者]  窗格中，完成下列步驟：

    1. 在 [名稱]  方塊中，輸入 **BrittaSimon**。
  
    1. 在 [使用者名稱] 方塊中，輸入 **brittasimon\@\<your-company-domain>.\<extension\>** 。 例如，**brittasimon\@contoso.com**。

    1. 選取 [顯示密碼]  核取方塊。 記下 [密碼]  方塊中顯示的值。

    1. 選取 [建立]  。

    ![[使用者] 窗格](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 HubSpot 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]   > [HubSpot]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [HubSpot]  。

    ![應用程式清單中的 HubSpot](common/all-applications.png)

1. 在功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 選項](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 然後在 [新增指派]  窗格中，選取 [使用者和群組]  。

    ![新增指派窗格](common/add-assign-user.png)

1. 在 [使用者和群組]  窗格中，選取使用者清單中的 [Britta Simon]  。 選擇 [選取]  。

1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  窗格的清單中選取相關的使用者角色。 選擇 [選取]  。

1. 在 [新增指派]  窗格中，選取 [指派]  。

### <a name="create-a-hubspot-test-user"></a>建立 HubSpot 測試使用者

若要讓 Azure AD 使用者可以登入 HubSpot，則必須將他們佈建至 HubSpot。 在 HubSpot 中，需以手動方式佈建。

若要在 HubSpot 中佈建使用者帳戶：

1. 以系統管理員身分登入您的 HubSpot 公司網站。

1. 選取頁面右上角的 [設定]  圖示。

    ![HubSpot 中的設定圖示](./media/hubspot-tutorial/config1.png)

1. 選取 [使用者及小組]  。

    ![HubSpot 中的使用者及小組選項](./media/hubspot-tutorial/user1.png)

1. 選取 [建立使用者]  。

    ![HubSpot 中的建立使用者選項](./media/hubspot-tutorial/user2.png)

1. 在 [新增電子郵件地址]  方塊中，輸入格式為 brittasimon\@contoso.com 的使用者電子郵件地址，然後選取 [下一步]  。

    ![HubSpot 建立使用者區段中的新增電子郵件地址方塊](./media/hubspot-tutorial/user3.png)

1. 在 [建立使用者]  區段中，選取每個索引標籤。在每個索引標籤上，設定使用者的相關選項和權限。 然後，選取 [下一步]  。

    ![HubSpot 建立使用者區段中的索引標籤](./media/hubspot-tutorial/user4.png)

1. 若要傳送邀請給使用者，請選取 [傳送]  。

    ![HubSpot 中的傳送選項](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > 使用者會在接受邀請之後啟動。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

設定單一登入之後，當您在「我的應用程式」入口網站中選取 [HubSpot]  時，您會自動登入 HubSpot。 如需「我的應用程式」入口網站的詳細資訊，請參閱[在「我的應用程式」入口網站中存取和使用應用程式](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解，請檢閱下列文章：

- [用於整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
