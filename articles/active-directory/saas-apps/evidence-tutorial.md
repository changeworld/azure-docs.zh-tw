---
title: 教學課程：Azure Active Directory 與 Evidence.com 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Evidence.com 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3a80c7ff16ae1d0d7c2b7de2b0ceb9e46e724da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158234"
---
# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>教學課程：Azure Active Directory 與 Evidence.com 整合

在本教學課程中，您將了解如何整合 Evidence.com 與 Azure Active Directory (Azure AD)。
Evidence.com 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中管控可存取 Evidence.com 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Evidence.com (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Evidence.com 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Evidence.com 單一登入功能的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Evidence.com 支援 **SP** 起始的 SSO

## <a name="adding-evidencecom-from-the-gallery"></a>從資源庫新增 Evidence.com

若要設定將 Evidence.com 整合到 Azure AD 中，您需要從資源庫將 Evidence.com 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Evidence.com，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Evidence.com**，從結果面板中選取 [Evidence.com]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Evidence.com](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Evidence.com 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Evidence.com 中相關使用者之間的連結關聯性。

若要設定並測試透過 Evidence.com 使用 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Evidence.com 單一登入](#configure-evidencecom-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Evidence.com 測試使用者](#create-evidencecom-test-user)** - 使 Evidence.com 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定透過 Evidence.com 使用 Azure AD 單一登入功能，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Evidence.com]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Evidence.com 網域及 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<yourtenant>.evidence.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<yourtenant>.evidence.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Evidence.com 用戶端支援小組](https://communities.taser.com/support/SupportContactUs?typ=LE)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Evidence.com]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-evidencecom-single-sign-on"></a>設定 Evidence.com 單一登入

1. 在個別的網頁瀏覽器視窗中，以系統管理員身分登入您的 Evidence.com 租用戶，並瀏覽至 [Admin]  索引標籤

2. 按一下 [機構單一登入] 

3. 選取 [SAML 型單一登入] 

4. 將顯示在 Azure 入口網站中的 **Azure AD 識別碼**、**登入 URL** 和**登出 URL** 值複製到 Evidence.com 中的對應欄位。

5. 在記事本中開啟您下載的憑證 (Base64) 檔案，將其內容複製到剪貼簿，然後貼到 [安全性憑證]  方塊中。 

6. 在 Evidence.com 中儲存組態。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Evidence.com 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Evidence.com]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Evidence.com]  。

    ![應用程式清單中的 Evidence.com 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-evidencecom-test-user"></a>建立 Evidence.com 測試使用者

必須先針對 Evidence.com 應用程式內的存取佈建 Azure AD 使用者，才可以讓他們登入。 本節描述如何建立 Evidence.com 內的 Azure AD 使用者帳戶。

**若要在 Evidence.com 中佈建使用者帳戶：**

1. 在網頁瀏覽器視窗中，以系統管理員身分登入您的 Evidence.com 公司網站。

2. 瀏覽至 [Admin]  索引標籤。

3. 按一下 [新增使用者]  。

4. 按一下 [新增]  按鈕。

5. 所新增之使用者的 [電子郵件地址]  必須符合 Azure AD 中想要允許存取的使用者名稱。 如果使用者名稱和電子郵件地址不是您組織中的相同值，您可以使用 Azure 入口網站的 **[Evidence.com] > [屬性] > [單一登入]** 區段，將傳送至 Evidence.com 的 nameidenitifer 變更為電子郵件地址。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Evidence.com] 圖格時，應該會自動登入您已設定 SSO 的 Evidence.com。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

