---
title: 教學課程：Azure Active Directory 與 Adobe Experience Manager 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Adobe Experience Manager 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154109"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>教學課程：Azure Active Directory 與 Adobe Experience Manager 整合

在本教學課程中，您將了解如何整合 Adobe Experience Manager 與 Azure Active Directory (Azure AD)。
Adobe Experience Manager 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Adobe Experience Manager 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Adobe Experience Manager (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Adobe Experience Manager 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Adobe Experience Manager 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Adobe Experience Manager 支援由 **SP 和 IDP** 初始化的 SSO

* Adobe Experience Manager 支援 **Just In Time** 使用者佈建。

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>從資源庫新增 Adobe Experience Manager

若要設定 Adobe Experience Manager 與 Azure AD 整合，您需要從資源庫將 Adobe Experience Manager 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Adobe Experience Manager，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Adobe Experience Manager**，從結果面板中選取 [Adobe Experience Manager]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Adobe Experience Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 [應用程式名稱] 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 [應用程式名稱] 中相關使用者之間的連結關聯性。

若要設定及測試與 [應用程式名稱] 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Adobe Experience Manager 單一登入](#configure-adobe-experience-manager-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Adobe Experience Manager 測試使用者](#create-adobe-experience-manager-test-user)** - 使 Adobe Experience Manager 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 [應用程式名稱] 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Adobe Experience Manager]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Adobe Experience Manager 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，輸入您也會在 AEM 伺服器上定義的唯一值。

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > [回覆 URL] 不是真實的值。 請使用實際的回覆 URL 來更新 [回覆 URL] 值。 若要取得此值，請連絡 [Adobe Experience Manager 用戶端支援小組](https://helpx.adobe.com/support/experience-manager.html)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![Adobe Experience Manager 網域及 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，輸入您的 Adobe Experience Manager 伺服器 URL。

6. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

7. 在 [設定 Adobe Experience Manager]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>設定 Adobe Experience Manager 單一登入

1. 在另一個瀏覽器視窗中，開啟 **Adobe Experience Manager** 管理入口網站。

2. 選取 [Settings]  \(設定\) > [Security]  \(安全性\) > [Users]  \(使用者\)。

    ![設定單一登入 [儲存] 按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. 選取 [Administrator]  \(系統管理員\) 或任何其他相關的使用者。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. 選取 [Account Settings] \(帳戶設定\)   > [Manage TrustStore] \(管理 TrustStore\)  。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. 在 [Add Certificate from CER file] \(從 CER 檔案新增憑證\)  底下，按一下 [Select Certificate File] \(選取憑證檔案\)  。 瀏覽並選取您已經從 Azure 入口網站下載的憑證檔案。

    ![設定單一登入 [儲存] 按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. 憑證即會新增至 TrustStore。 請記下憑證的別名。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. 在 [Users] \(使用者\)  頁面上，選取 [authentication-service]  。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. 選取 [Account settings]  \(帳戶設定\) > [Create/Manage KeyStore]  \(建立/管理 KeyStore\)。 提供密碼以建立 KeyStore。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. 返回管理畫面。 然後選取 [Settings] \(設定\)   > [Operations] \(作業\)   > [Web Console] \(Web 主控台\)  。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    這會開啟設定頁面。

    ![設定單一登入 [儲存] 按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. 找出 **Adobe Granite SAML 2.0 Authentication Handler**。 然後選取 [Add] \(新增\)  圖示。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. 在此頁面上執行下列動作。

    ![設定單一登入儲存按鈕](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. 在 [Path] \(路徑\)  方塊中，輸入 **/** 。

    b. 在 [IDP URL]  方塊中，輸入您從 Azure 入口網站複製的 [登入 URL]  值。

    c. 在 [IDP Certificate Alias] \(IDP 憑證別名\)  方塊中，輸入您已在 TrustStore 中新增的**憑證別名**值。

    d. 在 [服務提供者實體識別碼]  方塊中，輸入您在 Azure 入口網站中設定的唯一 [Azure AD 識別碼]  值。

    e. 在 [Assertion Consumer Service URL] \(判斷提示取用者服務 URL\)  方塊中，輸入您已在 Azure 入口網站中設定的**回覆 URL** 值。

    f. 在 [Password of Key Store] \(金鑰存放區的密碼\)  方塊中，輸入您已在 KeyStore 中設定的**密碼**。

    g. 在 [User Attribute ID] \(使用者屬性識別碼\)  方塊中，輸入**名稱識別碼**或您案例中相關的其他使用者識別碼。

    h. 選取 [Autocreate CRX Users] \(自動建立 CRX 使用者\)  。

    i. 在 [登出 URL]  方塊中，貼上您從 Azure 入口網站取得的 [登出 URL]  值。

    j. 選取 [儲存]  。

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

在本節中，您會將 Adobe Experience Manager 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Adobe Experience Manager]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Adobe Experience Manager]  。

    ![應用程式清單中的 Adobe Experience Manager 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-adobe-experience-manager-test-user"></a>建立 Adobe Experience Manager 測試使用者

在本節中，您會在 Adobe Experience Manager 中建立名為 Britta Simon 的使用者。 如果您已選取 [Autocreate CRX Users] \(自動建立 CRX 使用者\)  選項，系統就會在成功驗證之後，自動建立使用者。

如果您想要手動建立使用者，請與 [Adobe Experience Manager 支援小組](https://helpx.adobe.com/support/experience-manager.html) 合作，在 Adobe Experience Manager 平台中新增使用者。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Adobe Experience Manager 圖格時，應該會自動登入您設定 SSO 的 Adobe Experience Manager。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
