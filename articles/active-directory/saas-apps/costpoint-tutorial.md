---
title: 教學課程：Azure Active Directory 與 Costpoint 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Costpoint 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 7b32c5576ef38f07d28777e1a72aafa935042930
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91775656"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>教學課程：整合 Costpoint 與 Azure Active Directory

在本教學課程中，您會了解如何將 Costpoint 與 Azure Active Directory (Azure AD) 整合。 在整合 Costpoint 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Costpoint 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Costpoint。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Costpoint 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您將在測試環境中設定和測試 Azure AD SSO。 Costpoint 支援由 **SP 和 IDP** 起始的 SSO。

## <a name="generate-costpoint-metadata"></a>產生 Costpoint 中繼資料

**DeltekCostpoint711Security.pdf** 指南提供了 Costpoint SAML SSO 組態的說明。 從 Deltek Costpoint 支援網站下載本指南，並參閱 **SAML 單一登入設定** > **在 Costpoint 與 Microsoft Azure 之間設定 SAML 單一登入**一節。 依照指示執行，並產生 **Costpoint SP 同盟中繼資料 XML** 檔案。 

![顯示 [產品設定公用程式] 的螢幕擷取畫面，其中已選取 [Weblogic - 安全性] 索引標籤。](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>從資源庫新增 Costpoint

若要整合 Costpoint 與 Azure AD，先從 Azure 入口網站中的資源庫將 Costpoint 新增至受控 SaaS 應用程式清單：

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側瀏覽窗格中，選取 [Azure Active Directory]  服務。

   ![Azure Active Directory 按鈕](common/select-azuread.png)

1. 選取 [企業應用程式]   > [所有應用程式]  。

   ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 若要新增新的應用程式，請選取 [新增應用程式]  。

   ![新增應用程式按鈕](common/add-new-app.png)

1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Costpoint**。

   ![結果清單中的 Costpoint](common/search-new-app.png)

1. 從結果面板選取 [Costpoint]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>設定及測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Costpoint 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Costpoint 中相關使用者之間的連結關聯性。

若要設定及測試與 Costpoint 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
1. **[設定 Costpoint](#configure-costpoint)** 以在應用程式端設定 SAML SSO 設定。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[建立 Costpoint 測試使用者](#create-a-costpoint-test-user)** ，使 Costpoint 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 [Costpoint]  應用程式整合頁面上，選取 [單一登入]  。

   ![設定單一登入連結](common/select-sso.png)

1. 如果您有「服務提供者中繼資料檔案」  ，請在 [基本 SAML 設定]  區段中完成下列步驟：

   > [!NOTE]
   > 您會在[產生 Costpoint 中繼資料](#generate-costpoint-metadata)中取得服務提供者中繼資料檔案。 此教學課程稍後將會說明使用此檔案的方式。
 
   1. 選取 [上傳中繼資料檔案]  按鈕、選取 Costpoint 先前產生的 [Costpoint SP 同盟中繼資料 XML]  檔案，然後選取 [新增]  按鈕來上傳檔案。

      ![上傳中繼資料檔案](./media/costpoint-tutorial/upload-metadata.png)
    
   1. 成功上傳中繼資料檔案之後，即會自動在 [Costpoint] 區段中填入 [識別碼]  和 [回覆 URL]  值。

      > [!NOTE]
      > 若未自動填入 [識別碼]  和 [回覆 URL]  ，請根據您的需求手動輸入這些值。 確認 [識別碼 (實體識別碼)]  和 [回覆 URL (判斷提示取用者服務 URL)]  皆已正確設定，且 [ACS URL]  是以 **/LoginServlet.cps** 結尾的有效 Costpoint URL。

   1. 選取 [設定其他 URL]  。 針對 [轉送狀態]  ，使用下列模式輸入值：`system=[your system]` (例如 **system=DELTEKCP**)。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，選取 [複製]  圖示以複製**應用程式同盟中繼資料 URL**，並將它儲存到 [筆記本]。

   ![SAML 簽署憑證](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>設定 Costpoint

1. 返回 Costpoint 設定公用程式。 在 [IdP 同盟中繼資料 XML]  文字方塊中，貼上「應用程式同盟中繼資料 URL」  檔案的內容。 

   ![Costpoint 組態公用程式](./media/costpoint-tutorial/config-utility-idp.png)

1. 繼續執行 **DeltekCostpoint711Security.pdf** 指南中的指示，以完成 Costpoint SAML 設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站中，於左窗格選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。

   ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

1. 選取 [新增使用者]  。

   ![[新增使用者] 按鈕](common/new-user.png)

1. 在 [使用者]  屬性中，完成下列步驟：

   ![[使用者] 對話方塊](common/user-properties.png)

   1. 在 [名稱]  欄位中，輸入 **B.Simon**。
   
   1. 在 [使用者名稱]  欄位中，輸入 `b.simon\@yourcompanydomain.extension` (例如 B.Simon@contoso.com)。
   
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  欄位中顯示的值。
   
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Costpoint 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。

1. 在應用程式清單中，選取 [Costpoint]  。

1. 在應用程式概觀頁面的 [管理]  區段中，選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

   ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者]  清單中，選取 [B.Simon]  。 然後選擇 [選取]  。

1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色，然後選擇 [選取]  。

1. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-a-costpoint-test-user"></a>建立 Costpoint 測試使用者

在本節中，您將於 Costpoint 中建立使用者。 假設使用者識別碼為 **B.SIMON**，而使用者的名稱為 **B.Simon**。 請與 [Costpoint 用戶端支援小組](https://www.deltek.com/about/contact-us)合作，在 Costpoint 平台中新增使用者。 您必須先建立並啟動使用者，然後他們才能使用單一登入。

建立使用者之後，該使用者的 [驗證方法]  選取項目必須是 [Active Directory]  、必須選取 [SAML 單一登入]  核取方塊，且 Azure Active Directory 中的使用者名稱必須是 [Active Directory 或憑證識別碼]  (如下列螢幕擷取畫面所示)。

![Costpoint 使用者](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Costpoint] 圖格時，應該會自動登入 Costpoint 應用程式 (因為您已設定 SSO)。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) \(部分機器翻譯\)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
