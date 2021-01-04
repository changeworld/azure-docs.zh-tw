---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 New Relic 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 New Relic 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504044"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 New Relic 整合

在本教學課程中，您將了解如何整合 New Relic 與 Azure Active Directory (Azure AD)。 在整合 New Relic 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 New Relic 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 New Relic。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用單一登入 (SSO) 的 New Relic 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* New Relic 支援由服務提供者或身分識別提供者起始的 SSO。
* 設定 New Relic 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-new-relic-from-the-gallery"></a>從資源庫新增 New Relic

若要進行將 New Relic 整合到 Azure AD 中的設定，您必須從資源庫將 **New Relic (依組織)** 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [Azure Active Directory]  服務。
1. 選取 [企業應用程式] > [新增應用程式]。
1. 在 [瀏覽 Azure AD 資源庫] 頁面的搜尋方塊中，輸入 **New Relic (依組織)** 。
1. 從結果中選取 [New Relic (依組織)]，然後選取 [建立]。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>設定和測試 New Relic 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 New Relic 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 New Relic 中相關使用者之間的連結關聯性。

若要使用 New Relic 設定及測試 Azure AD SSO：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。
   1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
   1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. [設定 New Relic SSO](#configure-new-relic-sso) - 在 New Relic 端設定單一登入設定。
   1. [建立 New Relic 測試使用者](#create-a-new-relic-test-user) - 使 New Relic 中對應的 B.Simon 連結到 Azure AD 使用者。
1. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [New Relic (依組織)] 應用程式整合頁面上，尋找 [管理] 區段。 然後選取 [單一登入]。

1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。

1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的鉛筆圖示，以編輯設定。

   ![[以 SAML 設定單一登入] 的螢幕擷取畫面，其中已醒目提示鉛筆圖示。](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段中，輸入 [識別碼] 和 [回覆 URL] 的值。

   * 使用 New Relic **My Organization** 應用程式來擷取這些值。 若要使用此應用程式：
      1. [登入](https://login.newrelic.com/) New Relic。
      1. 在頂端功能表上，選取 [應用程式]。
      1. 在 **您的應用程式** 區段中，選取 [My Organization] > [驗證網域]。
      1. 選擇您要讓 Azure AD SSO 連線到的驗證網域 (如果您有多個驗證網域)。 大部分的公司只會有一個名為 **預設** 的驗證網域。 如果只有一個驗證網域，就不需要選取任何項目。
      1. 在 [驗證] 區段中，[判斷提示取用者 URL] 包含 [回覆 URL] 所使用的值。
      1. 在 [驗證] 區段中，[我們的實體識別碼] 包含 [識別碼] 所使用的值。

1. 在 [使用者屬性與宣告] 區段中，請確定 [唯一使用者識別碼] 對應至 New Relic 所使用的電子郵件地址所在的欄位。

   * 預設欄位 **user.userprincipalname** 的值若與 New Relic 電子郵件地址相同，則可以使用預設值。
   * 如果 **user.userprincipalname** 不是 New Relic 電子郵件地址，則 **user.mail** 欄位可能較為適用。

1. 在 [SAML 簽署憑證] 區段中，複製 [應用程式同盟中繼資料 URL] 並儲存其值，以供後續使用。

1. 在 [設定 New Relic (依組織)] 區段中，複製 [登入 URL] 並儲存其值，以供後續使用。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

這裡說明如何 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站 中，選取 [Azure Active Directory]。
1. 選取 [使用者] > [新增使用者]。
1. 在 **新增使用者** 頁面上：
   1. 在 [使用者名稱]  欄位中，輸入 `username@companydomain.extension`。 例如： `b.simon@contoso.com` 。 此項目應與您將在 New Relic 端使用的電子郵件地址相符。
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 選取 [顯示密碼]，並儲存顯示的值。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

這裡說明如何將「New Relic (依組織)」應用程式的存取權授與 B.Simon，讓其能夠使用 Azure AD 單一登入。

1. 在 Azure 入口網站 中，選取 [Azure Active Directory]。
1. 選取 [企業應用程式] > [New Relic (依組織)]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[管理] 區段的螢幕擷取畫面，其中已醒目提示 [使用者和群組]。](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 在 [新增指派]中選取 [使用者和群組] (或選取 [使用者]，視您的方案層級而定)。

   ![[新增使用者] 選項的螢幕擷取畫面。](common/add-assign-user.png)

1. 在 [使用者和群組] (或 [使用者]) 的 **使用者** 清單中選取 [B.Simon]，然後選擇畫面底部的 [選取]。
1. 在 [新增指派] 中，選取 [指派]。

## <a name="configure-new-relic-sso"></a>設定 New Relic SSO

請依照下列步驟在 New Relic 上設定 SSO。

1. [登入](https://login.newrelic.com/) New Relic。

1. 在頂端功能表上，選取 [應用程式]。

1. 在 **您的應用程式** 區段中，選取 [My Organization] > [驗證網域]。

1. 選擇您要讓 Azure AD SSO 連線到的驗證網域 (如果您有多個驗證網域)。 大部分的公司只會有一個名為 **預設** 的驗證網域。 如果只有一個驗證網域，就不需要選取任何項目。

1. 在 [驗證] 區段中，選取 [設定]。

   1. 在 [SAML 中繼資料的來源] 中，輸入您先前從 Azure AD 的 [應用程式同盟中繼資料 URL] 欄位儲存的值。

   1. 在 [SSO 目標 URL] 中，輸入您先前從 Azure AD 的 [登入 URL] 欄位儲存的值。

   1. 確認 Azure AD 和 New Relic 端都已設定妥當後，選取 [儲存]。 如果兩端未正確設定，您的使用者將無法登入 New Relic。

### <a name="create-a-new-relic-test-user"></a>建立 New Relic 測試使用者

在本節中，您會在 New Relic 中建立名為 B.Simon 的使用者。

1. [登入](https://login.newrelic.com/) New Relic。

1. 在頂端功能表上，選取 [應用程式]。

1. 在 [您的應用程式] 區段中，選取 [使用者管理]。

1. 選取 [新增使用者]  。

   1. 在 [名稱] 中，輸入 **B.Simon**。
   
   1. 在 [電子郵件] 中，輸入 Azure AD SSO 將傳送的值。
   
   1. 選擇使用者的使用者 [類型] 和使用者 [群組]。 針對測試使用者，可以選擇 [基本使用者] 作為類型，並選擇 [使用者] 作為群組。
   
   1. 若要儲存使用者，請選取 [新增使用者]。

## <a name="test-sso"></a>測試 SSO 

這裡說明如何使用存取面板來測試您的 Azure AD 單一登入組態。

在存取面板中選取 [New Relic (依組織)] 時，應該會自動登入 New Relic。 如需存取面板的詳細資訊，請參閱[登入我的應用程式入口網站並啟動應用程式](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 New Relic](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)
