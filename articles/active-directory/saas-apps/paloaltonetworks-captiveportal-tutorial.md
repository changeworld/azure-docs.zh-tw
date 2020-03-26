---
title: 教學課程：Azure Active Directory 與 Palo Alto Networks Captive Portal 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Palo Alto Networks Captive Portal 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160157"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>教學課程：Azure Active Directory 與 Palo Alto Networks Captive Portal 整合

在本教學課程中，您將了解如何整合 Palo Alto Networks Captive Portal 與 Azure Active Directory (Azure AD)。

將 Palo Alto Networks Captive Portal 與 Azure AD 整合，可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Palo Alto Networks Captive Portal 的人員。
* 您可以利用使用者的 Azure AD 帳戶在 Palo Alto Networks Captive Portal 中自動登入使用者 (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要整合 Azure AD 與 Palo Alto Networks Captive Portal，您需要下列項目：

* Azure Active Directory 訂用帳戶。 如果您沒有 Azure AD，您可以取得[一個月的試用](https://azure.microsoft.com/pricing/free-trial/)。
* 已啟用 Palo Alto Networks Captive Portal 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

Palo Alto Networks Captive Portal 支援下列案例：

* **由 IDP 起始的單一登入**
* **Just-in-Time 使用者佈建**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>從資源庫新增 Palo Alto Networks Captive Portal

若要開始使用，請在資源庫中將 Palo Alto Networks Captive Portal 新增至受控 SaaS 應用程式清單：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 選取 [企業應用程式]   > [所有應用程式]  。

    ![功能表中的企業應用程式選項](common/enterprise-applications.png)

3. 選取 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Palo Alto Networks Captive Portal**。 在搜尋結果中選取 [Palo Alto Networks - Captive Portal]  ，然後選取 [新增]  。

     ![結果清單中的 Palo Alto 網路 - 網頁驗證入口](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

您會以名為 *Britta Simon* 的測試使用者為基礎，設定及測試與 Palo Alto Networks Captive Portal 搭配運作的 Azure AD 單一登入。 若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 Palo Alto Networks Captive Portal 中的相同使用者之間的關聯性。 

若要設定及測試與 Palo Alto Networks Captive Portal 搭配運作的 Azure AD 單一登入，請完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** ：讓使用者能夠使用這項功能。
2. **[設定 Palo Alto Networks Captive Portal 單一登入](#configure-palo-alto-networks-captive-portal-single-sign-on)** ：在應用程式中設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ：以使用者 *Britta Simon* 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ：將 Britta Simon 設定為使用 Azure AD 單一登入。
5. **建立 Palo Alto Networks Captive Portal 測試使用者**：在 Palo Alto Networks Captive Portal 中建立連結到 Azure AD 使用者的對應使用者 *Britta Simon*。
6. **[測試單一登入](#test-single-sign-on)** ：驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

首先，在 Azure 入口網站中啟用 Azure AD 單一登入：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Palo Alto Networks - 網頁驗證入口]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  窗格中，選取 [SAML]  。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  窗格中，選取鉛筆狀的 [編輯]  圖示。

    ![鉛筆狀的編輯圖示](common/edit-urls.png)

4. 在 [基本 SAML 組態]  窗格中，完成下列步驟：

    ![Palo Alto Networks Captive Portal 基本 SAML 組態窗格](common/idp-intiated.png)

   1. 針對 [識別碼]  ，輸入具有 `https://<customer_firewall_host_name>/SAML20/SP` 模式的 URL。

   2. 針對 [回覆 URL]  ，輸入具有 `https://<customer_firewall_host_name>/SAML20/SP/ACS` 模式的 URL。

      > [!NOTE]
      > 請使用實際的識別碼和回覆 URL 更新此步驟中的預留位置值。 若要取得實際值，請連絡 [Palo Alto Networks Captive Portal 用戶端支援小組](https://support.paloaltonetworks.com/support)。

5. 在 [SAML 簽署憑證]  區段中的 [同盟中繼資料 XML]  旁邊，選取 [下載]  。 將下載的檔案儲存在電腦上。

    ![同盟中繼資料 XML 下載連結](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>設定 Palo Alto Networks Captive Portal 單一登入

接著，在 Palo Alto Networks Captive Portal 中設定單一登入：

1. 在不同的瀏覽器視窗中，以系統管理員身分登入 Palo Alto Networks 網站。

2. 選取 [裝置]  索引標籤。

    ![Palo Alto Networks 網站裝置索引標籤](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 在功能表中選取 [SAML 識別提供者]  ，然後選取 [匯入]  。

    ![匯入按鈕](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 在 [SAML 識別提供者伺服器設定檔匯入]  對話方塊中，完成下列步驟：

    ![設定 Palo Alto Networks 單一登入](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. 針對 [設定檔名稱]  輸入名稱，例如 **AzureAD-CaptivePortal**。
    
    2. 在 [識別提供者中繼資料]  旁邊，選取 [瀏覽]  。 選取您在 Azure 入口網站中下載的 metadata.xml 檔案。
    
    3. 選取 [確定]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

接著，在 Azure 入口網站中建立名為 *Britta Simon* 的測試使用者：

1. 在 Azure 入口網站中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 選取 [新增使用者]  。

    ![新增使用者按鈕](common/new-user.png)

3. 在 [使用者]  窗格中，完成下列步驟：

    ![[使用者] 對話方塊](common/user-properties.png)

    1. 針對 [名稱]  ，輸入 **BrittaSimon**。
  
    2. 針對 [使用者名稱]  ，輸入 **BrittaSimon\@\<your_company_domain\>** 。 例如 **BrittaSimon\@contoso.com**。

    3. 針對 [密碼]  ，輸入密碼。 建議您為輸入的密碼保留記錄。 您可以選取 [顯示密碼]  核取方塊，以顯示密碼。

    4. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

接著，為 Britta Simon 授與對 Palo Alto Networks Captive Portal 的存取權，讓她能夠使用 Azure 單一登入：

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

2. 在應用程式清單中輸入 [Palo Alto Networks - Captive Portal]  ，然後選取該應用程式。

    ![應用程式清單中的 Palo Alto Networks - Captive Portal 連結](common/all-applications.png)

3. 在功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者]  。 然後在 [新增指派]  窗格中，選取 [使用者和群組]  。

    ![新增指派窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  窗格的 [使用者]  清單中，選取 [Britta Simon]  。 選取 [選取]  。

6. 若要將角色值新增至 SAML 判斷提示，請在 [選取角色]  窗格中為使用者選取相關的角色。 選取 [選取]  。

7. 在 [新增指派]  窗格中，選取 [指派]  。

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>建立 Palo Alto Networks Captive Portal 測試使用者

接著，在 Palo Alto Networks Captive Portal 中建立名為 *Britta Simon* 的使用者。 Palo Alto Networks Captive Portal 支援依預設啟用的 Just-in-Time 佈建。 在本節中，您不需要完成任何工作。 如果 Palo Alto Networks Captive Portal 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!NOTE]
> 如果您想要手動建立使用者，請連絡 [Palo Alto Networks Captive Portal 用戶端支援小組](https://support.paloaltonetworks.com/support)。

### <a name="test-single-sign-on"></a>測試單一登入 

Palo Alto Networks Captive Portal 安裝於 Windows VM 的防火牆後方。 若要測試 Palo Alto Networks Captive Portal 中的單一登入，請使用遠端桌面通訊協定 (RDP) 登入 Windows VM。 在 RDP 工作階段中開啟瀏覽器，並移至任何網站。 SSO URL 會開啟，且系統會提示您進行驗證。 在完成驗證後，您即可存取網站。

## <a name="additional-resources"></a>其他資源

若要深入了解，請參閱下列文章：

- [關於整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory 中的條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

