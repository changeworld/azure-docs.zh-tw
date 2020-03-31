---
title: 教學課程：Azure Active Directory 與 PagerDuty 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 PagerDuty 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b7a522aaf35303bbd87e7aafe65b1302f1b98bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095340"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>教學課程：Azure Active Directory 與 PagerDuty 整合

在本教學課程中，您會了解如何整合 PagerDuty 與 Azure Active Directory (Azure AD)。
將 PagerDuty 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中管控可存取 PagerDuty 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 PagerDuty (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 PagerDuty 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 PagerDuty 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* PagerDuty 支援由 **SP** 起始的 SSO

## <a name="adding-pagerduty-from-the-gallery"></a>從資源庫新增 PagerDuty

若要設定將 PagerDuty 整合到 Azure AD 中，您需要從資源庫將 PagerDuty 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 PagerDuty，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **PagerDuty**，從結果面板中選取 [PagerDuty]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 PagerDuty](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 PagerDuty 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 PagerDuty 中相關使用者之間的連結關聯性。

若要使用 PagerDuty 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 PagerDuty 單一登入](#configure-pagerduty-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 PagerDuty 測試使用者](#create-pagerduty-test-user)** - 使 PagerDuty 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 PagerDuty 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [PagerDuty]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![PagerDuty 網域及 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<tenant-name>.pagerduty.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [PagerDuty 用戶端支援小組](https://www.pagerduty.com/support/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 PagerDuty]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-pagerduty-single-sign-on"></a>設定 PagerDuty 單一登入

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Pagerduty 公司網站。

2. 在頂端的功能表中，按一下 [帳戶設定]  。

    ![帳戶設定](./media/pagerduty-tutorial/ic778535.png "帳戶設定")

3. 按一下 [單一登入]  。

    ![單一登入](./media/pagerduty-tutorial/ic778536.png "單一登入")

4. 在 [啟用單一登入 (SSO)]  頁面上執行下列步驟：

    ![啟用單一登入](./media/pagerduty-tutorial/ic778537.png "啟用單一登入")

    a. 在記事本中開啟從 Azure 入口網站下載的 Base-64 編碼憑證，將其內容複製到您的剪貼簿，然後貼到 [X.509 憑證]  文字方塊中
  
    b. 在 [登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  。
  
    c. 在 [登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  。

    d. 選取 [Allow username/password login] \(允許使用者名稱/密碼登入\)  。

    e. 選取 [Require EXACT authentication context comparison] \(要求完全相符的驗證內容比較\)  核取方塊。

    f. 按一下 **[儲存變更]** 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 PagerDuty 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [PagerDuty]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [PagerDuty]  。

    ![應用程式清單中的 PagerDuty 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-pagerduty-test-user"></a>建立 PagerDuty 測試使用者

若要讓 Azure AD 使用者能夠登入 PagerDuty，必須將他們佈建到 PagerDuty 中。  
PagerDuty 需以手動方式佈建。

>[!NOTE]
>您可以使用任何其他的 Pagerduty 使用者帳戶建立工具或 Pagerduty 提供的 API 來佈建 Azure Active Directory 使用者帳戶。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **Pagerduty** 租用戶。

2. 在頂端的功能表中，按一下 [使用者]  。

3. 按一下 [加入使用者]  。
   
    ![新增使用者](./media/pagerduty-tutorial/ic778539.png "新增使用者")

4.  在 [邀請小組]  對話方塊中，執行下列步驟：
   
    ![邀請您的小組](./media/pagerduty-tutorial/ic778540.png "邀請您的小組")

    a. 輸入使用者的**名字及姓氏**，如 **Britta Simon**。 
   
    b. 輸入使用者的**電子郵件**地址，例如 **brittasimon\@contoso.com**。
   
    c. 按一下 [新增]  ，然後按一下 [傳送邀請]  。
   
    >[!NOTE]
    >所有加入的使用者將會收到建立 PagerDuty 帳戶的邀請。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [PagerDuty] 圖格時，應該會自動登入您已設定 SSO 的 PagerDuty。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

