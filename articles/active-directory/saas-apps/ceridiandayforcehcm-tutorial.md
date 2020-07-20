---
title: 教學課程：Azure Active Directory 與 Ceridian Dayforce HCM 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Ceridian Dayforce HCM 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f488f22535c290b5fecbd0ffa9f8867f0b715bac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158679"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>教學課程：Azure Active Directory 與 Ceridian Dayforce HCM 整合

在本教學課程中，您將了解如何整合 Ceridian Dayforce HCM 與 Azure Active Directory (Azure AD)。
Ceridian Dayforce HCM 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Ceridian Dayforce HCM 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Ceridian Dayforce HCM (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Ceridian Dayforce HCM 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Ceridian Dayforce HCM 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Ceridian Dayforce HCM 支援 **SP** 起始的 SSO

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>從資源庫新增 Ceridian Dayforce HCM

若要設定將 Ceridian Dayforce HCM 整合到 Azure AD 中，您需要從資源庫將 Ceridian Dayforce HCM 加入受控 SaaS 應用程式清單中。

**若要從資源庫新增 Ceridian Dayforce HCM，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Ceridian Dayforce HCM**，從結果面板選取 [Ceridian Dayforce HCM]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Ceridian Dayforce HCM](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Ceridian Dayforce HCM 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Ceridian Dayforce HCM 中相關使用者之間的連結關聯性。

若要設定及測試與 Ceridian Dayforce HCM 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Ceridian Dayforce HCM 單一登入](#configure-ceridian-dayforce-hcm-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Ceridian Dayforce HCM 測試使用者](#create-ceridian-dayforce-hcm-test-user)** - 在 Ceridian Dayforce HCM 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Ceridian Dayforce HCM 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Ceridian Dayforce HCM] 應用程式整合分頁上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Ceridian Dayforce HCM 網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，輸入使用者用來登入您 Ceridian Dayforce HCM 應用程式的 URL。

    | 環境 | URL |
    | :-- | :-- |
    | 針對生產環境 | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | 針對測試 | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. 在 [識別碼]  文字方塊中，使用下列模式輸入 URL：

    | 環境 | URL |
    | :-- | :-- |
    | 針對生產環境 | `https://ncpingfederate.dayforcehcm.com/sp` |
    | 針對測試 | `https://fs-test.dayforcehcm.com/sp` |

    c. 在 [回覆 URL]  文字方塊中，輸入 Azure AD 用來公佈回應的 URL。

    | 環境 | URL |
    | :-- | :-- |
    | 針對生產環境 | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | 針對測試 | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Ceridian Dayforce HCM 用戶端支援小組](https://www.ceridian.com/support)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. Ceridian Dayforce HCM 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image](common/edit-attribute.png)

6. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | 名稱 | 來源屬性|
    | ---------| --------- |
    | NAME  | user.extensionattribute2 |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 從 [來源屬性]  清單中，選取您想要用於實作的使用者屬性。 例如，如果您想要使用 EmployeeID 為唯一的使用者識別碼，而且已在 ExtensionAttribute2 中儲存屬性值，則選取 [user.extensionattribute2]。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  以依據您的需求從指定選項下載 [中繼資料 XML]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

8. 在 [安裝 Ceridian Dayforce HCM]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>設定 Ceridian Dayforce HCM 單一登入

若要在 **Ceridian Dayforce HCM**  端設定單一登入，您必須將從 Azure 入口網站下載的 [中繼資料 XML]  和複製的適當 URL 傳送給 [Ceridian Dayforce HCM 支援小組](https://www.ceridian.com/support)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

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

在本節中，您會將 Ceridian Dayforce HCM 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Ceridian Dayforce HCM]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Ceridian Dayforce HCM]  。

    ![應用程式清單中的 Ceridian Dayforce HCM 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-ceridian-dayforce-hcm-test-user"></a>建立 Ceridian Dayforce HCM 測試使用者

在此節中，您會在 Ceridian Dayforce HCM 中建立名為 Britta Simon 的使用者。 請與  [Ceridian Dayforce HCM 支援小組](https://www.ceridian.com/support) 合作，在 Ceridian Dayforce HCM 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Ceridian Dayforce HCM] 圖格時，應該會自動登入您已設定 SSO 的 Ceridian Dayforce HCM。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

