---
title: 教學課程：整合 Azure Active Directory 單一登入 (SSO) 與 Splan 訪客 |Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Splan Visitor 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: ed136d06f18190124abe4598d580f40e41bf8592
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704215"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>教學課程：整合 Azure Active Directory 單一登入 (SSO) 與 Splan 訪客

在本教學課程中，您會了解如何將 Splan Visitor 與 Azure Active Directory (Azure AD) 進行整合。 在整合 Splan Visitor 與 Azure AD 時，您可以︰

* 使用 Azure AD 來控制可存取 Splan 訪客的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Splan 訪客。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* Splan 訪客單一登入 (SSO) 啟用的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

Splan 訪客支援 IdP 起始的 SSO。

## <a name="add-splan-visitor-from-the-gallery"></a>從資源庫新增 Splan 訪客

若要設定 Splan 訪客與 Azure AD 的整合，請從資源庫將 Splan 訪客新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中，輸入 **Splan 訪客** 。
1. 從結果面板中選取 [ **Splan 訪客** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>設定和測試 Splan Visitor 的 Azure AD SSO

使用名為 **b. Simon** 的測試使用者，設定及測試與 Splan 訪客 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Splan Visitor 中相關使用者之間的連結關聯性。

若要設定及測試與 Splan Visitor 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，使用測試使用者 b. Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Splan 訪客 SSO](#configure-splan-visitor-sso)** ，以使用 Splan 訪客來設定單一登入設定。
    1. **[建立 Splan 訪客測試使用者](#create-a-splan-visitor-test-user)** ，使 Splan 訪客中 Simon 的對應使用者連結到該使用者在 Azure AD 的代表身分。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 Azure 入口網站的 **Splan Visitor** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [**以 SAML 設定單一 Sign-On** ] 頁面上，選取 [**基本 saml** 設定] 的 **編輯/畫筆** 圖示，以編輯設定。

   ![顯示基本 SAML 設定的編輯/畫筆圖示的螢幕擷取畫面。](common/edit-urls.png)

1. 在 [ **基本 SAML** 設定] 區段中，會預先設定應用程式，並使用 Azure 預先填入必要的 url。 選取 [ **儲存** ] 按鈕以儲存設定。

1. 在 [ **以 Saml 設定單一登入** ] 頁面的 [ **saml 簽署憑證** ] 區段中，尋找 [ **同盟中繼資料 XML**]。 選取 [ **下載** ] 以下載憑證，並將它儲存到您的電腦。

    ![反白顯示同盟中繼資料 XML 下載連結的螢幕擷取畫面。](common/metadataxml.png)

1. 在 [ **設定 Splan 訪客** ] 區段上，根據您的需求複製適當的 Url 或 url。

    ![反白顯示 [設定 Url] 區段的螢幕擷取畫面。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 [Azure 入口網站] 的左窗格中，選取 [ **Azure Active Directory**]，選取 [ **使用者**]，然後選取 [ **所有使用者**]。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 **B.Simon**。  
   1. 在 [ **使用者名稱** ] 欄位中，以格式輸入您的使用者名稱 _username@companydomain.extension_ 。 例如，輸入 **B.Simon@contoso.com** 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Splan Visitor 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [ **Splan 訪客** ] 以開啟應用程式總覽。
1. 尋找 [ **管理** ] 區段，然後選取 [ **使用者和群組**]。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [**使用者和群組**] 對話方塊中，從 [**使用者**] 清單中選取 [ **b. Simon** ]，然後按一下畫面底部的 [**選取**]。
1. 如果要將角色指派給使用者，請從 [ **選取角色** ] 下拉式功能表中選取該角色。 如果未針對此應用程式設定任何角色，請將 **預設存取** 角色保持選取狀態。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-splan-visitor-sso"></a>設定 Splan Visitor SSO

若要使用 Splan 訪客設定單一登入，請將您下載的 **同盟中繼資料 XML** 和複製的適當 url 從 Azure 入口網站傳送給 [Splan 訪客支援小組](mailto:support@splan.com)。 這可確保兩端的 SAML SSO 連線都已正確設定。

### <a name="create-a-splan-visitor-test-user"></a>建立 Splan 訪客測試使用者

在 Splan 訪客中建立名為 **Britta Simon** 的測試使用者。 請與 [Splan 訪客支援小組](mailto:support@splan.com) 合作，將使用者新增至 Splan 訪客。 您必須先建立並啟用使用者，才能使用單一登入。

## <a name="test-sso"></a>測試 SSO

使用下列其中一個選項測試您的 Azure AD 單一登入設定：

* **Azure 入口網站**：選取 [ **測試此應用程式** ] 以自動登入您已設定 SSO 的 Splan 訪客。
* **Microsoft 我的應用程式入口網站**：選取 **Splan 訪客** 磚，以自動登入您已設定 SSO 的 Splan 訪客。 如需我的應用程式入口網站的詳細資訊，請參閱[登入我的應用程式入口網站並啟動應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 Splan 訪客之後，您可以 [瞭解如何在 Microsoft Cloud App Security 中強制執行會話控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。 會話控制項可協助您即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。
