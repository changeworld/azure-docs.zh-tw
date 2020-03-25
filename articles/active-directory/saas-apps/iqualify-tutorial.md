---
title: 教學課程：Azure Active Directory 與 iQualify LMS 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 iQualify LMS 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944982"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>教學課程：Azure Active Directory 與 iQualify LMS 整合

在本教學課程中，您將了解如何整合 iQualify LMS 與 Azure Active Directory (Azure AD)。
將 iQualify LMS 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 iQualify LMS 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 iQualify LMS (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定與 iQualify LMS 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 iQualify LMS 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* iQualify LMS 支援由 **SP 和 IDP** 起始的 SSO
* Litmos LMS 支援 **Just In Time** 使用者佈建

## <a name="adding-iqualify-lms-from-the-gallery"></a>從資源庫新增 iQualify LMS

若要設定將 iQualify LMS 整合到 Azure AD 中，您需要從資源庫將 iQualify LMS 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 iQualify LMS，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **iQualify LMS**，從結果面板中選取 [iQualify LMS]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 iQualify LMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 iQualify LMS 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 iQualify LMS 中相關使用者之間的連結關聯性。

若要設定及測試與 iQualify LMS 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 iQualify LMS 單一登入](#configure-iqualify-lms-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 iQualify LMS 測試使用者](#create-iqualify-lms-test-user)** - 使 iQualify LMS 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 iQualify LMS 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [iQualify LMS]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![iQualify LMS 網域與 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：
    | |
    |--|--|
    | 生產環境︰`https://<yourorg>.iqualify.com/`|
    | 測試環境：`https://<yourorg>.iqualify.io`|

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：
    | |
    |--|--|
    | 生產環境︰`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | 測試環境：`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![iQualify LMS 網域與 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰
    | |
    |--|--|
    | 生產環境︰`https://<yourorg>.iqualify.com/login` |
    | 測試環境：`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [iQualify LMS 用戶端支援小組](https://www.iqualify.com/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. iQualify LMS 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 **編輯** 圖示以開啟 [使用者屬性] 對話方塊。 

    ![image](common/edit-attribute.png)

7. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，使用 [編輯]  圖示來編輯宣告或使用 [新增宣告]  來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱 | 來源屬性|
    | --- | --- |
    | 電子郵件 | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | 「您的屬性」 |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

    > [!Note]
    > **person_id** 屬性為**選用**屬性

8. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

9. 在 [設定 iQualify LMS]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-iqualify-lms-single-sign-on"></a>設定 iQualify LMS 單一登入

1. 開啟新的瀏覽器視窗，然後以系統管理員身分登入您的 iQualify 環境。

1. 您登入後，在右上方按一下您的顯示圖片，然後按一下 [帳戶設定]  。

    ![帳戶設定](./media/iqualify-tutorial/setting1.png)

1. 在帳戶設定區域中，按一下左側的功能區功能表，並按一下 [整合]  。

    ![整合](./media/iqualify-tutorial/setting2.png)

1. 在 [整合] 下，按一下 **SAML** 圖示。

    ![SAML 圖示](./media/iqualify-tutorial/setting3.png)

1. 在 [SAML 驗證設定]  對話方塊中，執行下列步驟：

    ![SAML 驗證設定](./media/iqualify-tutorial/setting4.png)

    a. 在 [SAML 單一登入服務 URL]  方塊中，貼上從 Azure AD 應用程式組態視窗複製的 [登入 URL]  值。

    b. 在 [SAML 登出 URL]  方塊中，貼上從 Azure AD 應用程式組態視窗複製的 [登出 URL]  值。

    c. 在記事本中開啟下載的憑證，複製其內容，然後貼到 [公開憑證]  方塊中。

    d. 在 [登入按鈕標籤]  中，輸入要在登入頁面顯示的按鈕名稱。

    e. 按一下 [儲存]  。

    f. 按一下 [更新]  。

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

在本節中，您會將 iQualify LMS 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [iQualify LMS]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [iQualify LMS]  。

    ![應用程式清單中的 iQualify LMS 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-iqualify-lms-test-user"></a>建立 iQualify LMS 測試使用者

本節會在 iQualify LMS 中建立名為 Britta Simon 的使用者。 iQualify LMS 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 iQualify LMS 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下 [存取面板] 中的 [iQualify LMS] 圖格時，您應該會看到 iQualify LMS 應用程式的登入頁面。 

   ![登入頁面](./media/iqualify-tutorial/login.png) 

按一下 [使用 Azure AD 登入]  按鈕，您應該會自動登入 iQualify LMS 應用程式。

如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)