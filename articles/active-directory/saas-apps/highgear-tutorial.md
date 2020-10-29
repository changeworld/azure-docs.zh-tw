---
title: 教學課程：Azure Active Directory 與 HighGear 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 HighGear 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: b3806d768e8335801e2dd097eac7a440b157de38
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92444630"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>教學課程：Azure Active Directory 與 HighGear 整合

在本教學課程中，您將了解如何整合 HighGear 與 Azure Active Directory (Azure AD)。
HighGear 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 HighGear 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 HighGear (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 HighGear 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 具有企業或無限制授權的 HighGear 系統

## <a name="scenario-description"></a>案例描述

在本教學課程中，您將了解如何在測試環境中設定和測試 Azure AD 單一登入。

* HighGear 支援由 **SP 和 IdP** 起始的 SSO

## <a name="adding-highgear-from-the-gallery"></a>從資源庫新增 HighGear

若要進行將 HighGear 整合到 Azure AD 中的設定，您必須從資源庫將 HighGear 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 HighGear，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **HighGear** ，並從結果面板中選取 [HighGear]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 HighGear](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您將了解如何以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 HighGear 系統搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 HighGear 系統中相關使用者之間的連結關聯性。

若要設定及測試與 HighGear 系統搭配運作的 Azure AD 單一登入，您必須完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 HighGear 單一登入](#configure-highgear-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 HighGear 測試使用者](#create-highgear-test-user)** - 使 HighGear 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。 
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您將了解如何在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 HighGear 系統搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [HighGear]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上按一下 [編輯]  圖示，以開啟 [基本 SAML 組態]  對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![顯示基本 SAML 設定的螢幕擷取畫面，您可以在其中輸入識別碼、回覆 URL 以及選取 [儲存]。](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，貼上在 HighGear 系統的 [單一登入設定] 頁面上位於 [服務提供者實體識別碼]  欄位內的值。

    ![服務提供者實體識別碼欄位](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > 您必須登入 HighGear 系統以存取 [單一登入設定] 頁面。 登入後，請將滑鼠移至 HighGear 中的 [系統管理] 索引標籤上，然後按一下 [單一登入設定] 功能表項目。
    
    ![單一登入設定功能表項目](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. 在 [回覆 URL]  文字方塊中，貼上在 HighGear 系統的 [單一登入設定] 頁面上位於 [判斷提示取用者服務 (ACS) URL]  內的值。

    ![判斷提示取用者服務 (ACS) URL 欄位](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

     ![顯示您可以在其中輸入登入 URL 的設定額外 URL 螢幕擷取畫面。](common/metadata-upload-additional-signon.png)

     在 [登入 URL]  文字方塊中，貼上在 HighGear 系統的 [單一登入設定] 頁面上位於 [服務提供者實體識別碼]  欄位內的值。 (此實體識別碼也是由 SP 起始的登入所將使用的 HighGear 系統基底 URL)。

    ![服務提供者實體識別碼欄位](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > 這些都不是真正的值。 請使用 HighGear 系統的 [單一登入設定]  頁面中包含的實際識別碼、回覆 URL 和登入 URL 來更新這些值。 如需協助，請連絡 [HighGear 支援小組](mailto:support@highgear.com)。

4. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，按一下 [下載]  以下載 **憑證 (Base64)** 並將其儲存在電腦上。 您在單一登入設定的後續步驟中將需要這項資料。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 HighGear]  區段上，記下以下 URL 的位置。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL。 您在後續的 **設定 HighGear 單一登入** 底下的步驟 2 中將需要此值。

    b. Azure AD 識別碼。 您在後續的 **設定 HighGear 單一登入** 底下的步驟 3 中將需要此值。

    c. 登出 URL。 您在後續的 **設定 HighGear 單一登入** 底下的步驟 4 中將需要此值。

### <a name="configure-highgear-single-sign-on"></a>設定 HighGear 單一登入

若要設定 HighGear 的單一登入，請登入您的 HighGear 系統。 登入後，請將滑鼠移至 HighGear 中的 [系統管理] 索引標籤上，然後按一下 [單一登入設定] 功能表項目。

![單一登入設定功能表項目](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. 在 [識別提供者名稱]  中，輸入將在 [登入] 頁面上出現在 HighGear [單一登入] 按鈕中的簡短描述。 例如：Azure AD

2. 在 HighGear 的 [單一登入 (SSO) URL]  欄位中，貼上在 Azure 的 [設定 HighGear]  區段中位於 [登入 URL]  欄位內的值。

3. 在 HighGear 的 [識別提供者實體識別碼]  欄位中，貼上在 Azure 的 [設定 HighGear]  區段中位於 [Azure AD 識別碼]  欄位內的值。

4. 在 HighGear 的 [單一登出 (SLO) URL]  欄位中，貼上在 Azure 的 [設定 HighGear]  區段中位於 [登出 URL]  欄位內的值。

5. 使用「記事本」開啟您從 Azure 中的 [SAML 簽署憑證]  區段下載的憑證。 您應已下載 **憑證 (Base64)** 格式。 從「記事本」中複製憑證的內容，並將其貼到 HighGear 的 [識別提供者憑證]  欄位中。

6. 透過電子郵件向 [HighGear 支援小組](mailto:support@highgear.com)索取您的 HighGear 憑證。 請依照他們提供的指示填寫 [HighGear 憑證]  和 [HighGear 憑證密碼]  欄位。

7. 按一下 [儲存]  按鈕以儲存您的 HighGear 單一登入組態。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 HighGear 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [HighGear]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [HighGear]  。

    ![應用程式清單中的 HighGear 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-highgear-test-user"></a>建立 HighGear 測試使用者

若要建立 HighGear 測試使用者以測試您的單一登入組態，請登入 HighGear 系統。

1. 按一下 [建立新的連絡人]  按鈕。

    ![建立新的連絡人按鈕](media/highgear-tutorial/create-new-contact-button.png)

    此時會出現功能表，讓您選擇所要建立的連絡人類型。

2. 按一下 [個人]  功能表項目以建立 HighGear 使用者。

    右側會滑出一個窗格，讓您輸入新使用者的資訊。  
    ![新增連絡人表單](media/highgear-tutorial/new-contact-form.png)

3. 在 [名稱]  欄位中，輸入連絡人的名稱。 例如：Britta Simon

4. 按一下 [更多選項]  功能表，然後選取 [帳戶資訊]  功能表項目。

    ![按一下帳戶資訊功能表項目](media/highgear-tutorial/account-info-menu-item.png)

5. 將 [可以登入]  欄位設為 [是]。

    [啟用單一登入]  欄位也會自動設為 [是]。

6. 在 [單一登入使用者識別碼]  欄位中，輸入使用者的識別碼。 例如： BrittaSimon@contoso.com

    [帳戶資訊] 區段此時應會顯示如下：  
    ![已完成的帳戶資訊區段](media/highgear-tutorial/finished-account-info-section.png)

7. 若要儲存連絡人，請按一下窗格底部的 [儲存]  按鈕。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [HighGear] 圖格時，應該會自動登入您已設定 SSO 的 HighGear。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)