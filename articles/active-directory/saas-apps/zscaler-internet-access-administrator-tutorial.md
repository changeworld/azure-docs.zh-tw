---
title: 教學課程：Azure Active Directory 與 Zscaler Internet Access Administrator 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Internet Access Administrator 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a149527c6e00972991bf0b18e6f7c599799a0c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161031"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>教學課程：Azure Active Directory 與 Zscaler Internet Access Administrator 整合

在本教學課程中，您將了解如何整合 Zscaler Internet Access Administrator 與 Azure Active Directory (Azure AD)。
將 Zscaler Internet Access Administrator 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Zscaler Internet Access Administrator 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Zscaler Internet Access Administrator (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Zscaler Internet Access Administrator 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* Zscaler Internet Access Administrator 訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Zscaler Internet Access Administrator 支援 **IDP** 初始的 SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>從資源庫新增 Zscaler Internet Access Administrator

若要設定將 Zscaler Internet Access Administrator 整合到 Azure AD 中，您需要將 Zscaler Internet Access Administrator 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Zscaler Internet Access Administrator，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Zscaler Internet Access Administrator**，從結果面板中選取 [Zscaler Internet Access Administrator]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Zscaler Internet Access Administrator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Zscaler Internet Access Administrator 搭配運作的 Azure AD 單一登入。
為了讓單一登入運作，必須在 Azure AD 使用者與 Zscaler Internet Access Administrator 中的相關使用者之間建立連結關聯性。

若要設定及測試與 Zscaler Internet Access Administrator 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Zscaler Internet Access Administrator 單一登入](#configure-zscaler-internet-access-administrator-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Zscaler Internet Access Administrator 測試使用者](#create-zscaler-internet-access-administrator-test-user)** - 在 Zscaler Internet Access Administrator 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Zscaler Internet Access Administrator 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Zscaler Internet Access Administrator]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [基本 SAML 組態]  對話方塊。

    ![Zscaler Internet Access Administrator 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，依據您的需求鍵入 URL：

    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. 在 [回覆 URL]  文字方塊中，依據您的需求鍵入 URL：

    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Zscaler Internet Access Administrator 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性與宣告]  區段中管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性與宣告]  對話方塊。

    ![屬性連結](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱  | 來源屬性  |
    | ---------| ------------ |
    | 角色     | user.assignedroles |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. 從 [來源屬性]  清單中，選取屬性值。

    c. 按一下 [確定]  。

    d. 按一下 [檔案]  。

    > [!NOTE]
    > 請按一下[這裡](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)，以了解如何在 Azure AD 中設定角色

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

8. 在 [設定 Zscaler Internet Access Administrator]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>設定 Zscaler Internet Access Administrator 單一登入

1. 在不同的 Web 瀏覽器視窗中，登入您的 Zscaler Internet Access Admin UI。

2. 移至 管理員 > 管理員管理  ，執行下列步驟並按一下 儲存：

    ![管理](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "系統管理")

    a. 核取 [啟用 SAML 驗證]  。

    b. 按一下 [上傳]  ，以上傳您從 Azure 入口網站的 [公開 SSL 憑證]  下載的 Azure SAML 簽署憑證。

    c. (選擇性) 為了增加安全性，新增 [簽發者]  詳細資料以驗證 SAML 回應的簽發者。

3. 在 Admin UI 上，執行下列步驟：

    ![系統管理](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. 將滑鼠停留在靠近左下方的 [啟用]  功能表上。

    b. 按一下 [啟用]  。

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

在本節中，您會將 Zscaler Internet Access Administrator 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Zscaler Internet Access Administrator]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **Zscaler Internet Access Administrator** 並加以選取。

    ![應用程式清單中的 Zscaler Internet Access Administrator 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-zscaler-internet-access-administrator-test-user"></a>建立 Zscaler Internet Access Administrator 測試使用者

本節目標是在 Zscaler Internet Access Administrator 中建立名為 Britta Simon 的使用者。 Zscaler Internet Access 不支援 Administrator SSO 的 Just-In-Time 佈建。 您必須手動建立系統管理員帳戶。
如需有關如何建立系統管理員帳戶的步驟，請參閱 Zscaler 文件：

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 Zscaler Internet Access Administrator 圖格時，應該會自動登入您設定 SSO 的 Zscaler Internet Access Admin UI。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
