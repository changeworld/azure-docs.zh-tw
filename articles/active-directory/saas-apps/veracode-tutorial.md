---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Veracode 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Veracode 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: a62863607798e7f64a74926ebea4f1a1d05cbd6c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517713"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Veracode 整合

在本教學課程中，您會了解如何整合 Veracode 與 Azure Active Directory (Azure AD)。 在整合 Veracode 與 Azure AD 時，您可以︰

* 在 Azure AD 中管控可存取 Veracode 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Veracode。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Veracode 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Veracode 支援識別提供者起始的 SSO 及 Just-In-Time 使用者佈建。

## <a name="add-veracode-from-the-gallery"></a>從資源庫新增 Veracode

若要設定將 Veracode 整合到 Azure AD 中，請從資源庫將 Veracode 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 Veracode。
1. 從結果面板選取 [Veracode]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>設定及測試 Veracode 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Veracode 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Veracode 中相關使用者之間的連結。

若要設定及測試與 Veracode 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Veracode SSO](#configure-veracode-sso)** 在應用程式端設定單一登入設定。
    * **[建立 Veracode 測試使用者](#create-veracode-test-user)** - 使 Veracode 中具有與 Azure AD 中的 B.Simon 連結的相對應使用者。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Veracode]  應用程式整合頁面上，尋找 [管理]  區段。 選取 [單一登入]  。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 組態]  的鉛筆圖示，以編輯設定。

   ![[以 SAML 設定單一登入] 的螢幕擷取畫面，其中已醒目提示鉛筆圖示](common/edit-urls.png)

1. 在 [基本 SAML 設定]  設定區段上，已預先設定好應用程式，並已經為 Azure 預先填入必要的 URL。 選取 [儲存]  。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  。 選取 [下載]  以下載憑證，並將其儲存在您的電腦上。

    ![已醒目提示 [下載] 連結的 [SAML 簽署憑證] 區段螢幕擷取畫面](common/certificatebase64.png)

1. Veracode 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![使用者屬性與宣告區段的螢幕擷取畫面](common/default-attributes.png)

1. Veracode 也需要在 SAML 回應中傳回更多屬性。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | 電子郵件 |User.mail |

1. 在 [設定 Veracode]  區段上，根據您的需求複製適當的 URL。

    ![醒目提示設定 URL 的 [設定 Veracode] 區段螢幕擷取畫面](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>設定 Veracode SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Veracode 公司網站。

1. 在頂端的功能表中，選取 [設定]   > [管理員]  。
   
    ![Veracode 管理的螢幕擷取畫面，其中的 [設定] 圖示和 [管理員] 已醒目提示](./media/veracode-tutorial/ic802911.png "系統管理")

1. 選取 [SAML]  索引標籤。

1. 在 [組織 SAML 設定]  區段中，執行下列步驟：

    ![[組織 SAML 設定] 區段的螢幕擷取畫面](./media/veracode-tutorial/ic802912.png "系統管理")

    a.  請在 [簽發者]  貼上您從 Azure 入口網站複製的 **Azure AD 識別碼** 值。

    b. 在 [判斷提示簽署憑證]  選取 [選擇檔案]  ，從 Azure 入口網站上傳您下載的憑證。

    c. 在 [自助式登錄]  選取 [啟用自助式登錄]  。

1. 請在 [自助式登錄設定]  區段上，執行下列步驟，然後選取 [儲存]  ：

    ![[自助式登錄設定] 區段的螢幕擷取畫面，其中的各種選項已醒目提示](./media/veracode-tutorial/ic802913.png "系統管理")

    a. 在 [啟用新的使用者]  選取 [不需要啟用]  。

    b. 在 [使用者資料更新]  選取 [Veracode 使用者資料喜好設定]  。

    c. 針對 [SAML 屬性詳細資料]  ，請選取下列選項：
      * **使用者角色**
      * **[原則系統管理員]**
      * **[檢閱者]**
      * **[安全性負責人]**
      * **[行政人員]**
      * **[傳送者]**
      * **[建立者]**
      * **[所有掃描類型]**
      * **[小組成員資格]**
      * **[預設小組]**

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   >[使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：

   1. 針對 [名稱]  輸入 `B.Simon`。  
   1. 針對 [使用者名稱]  ，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  ，並記下顯示的值。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節會將 Veracode 的存取權授與 B.Simon，的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [Veracode]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[管理] 區段的螢幕擷取畫面，其中已醒目提示 [使用者和群組]](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![使用者和群組使用者及群組頁面的螢幕擷取畫面，其中已醒目提示 [新增使用者]](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，在 [使用者]  選取 [B.Simon]  。 然後，選擇畫面底部的 [選取]  按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 然後，選擇畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="create-veracode-test-user"></a>建立 Veracode 測試使用者

若要登入 Veracode，必須將 Azure AD 的使用者佈建到 Veracode。 這項工作會自動進行，您不需要手動執行任何動作。 第一次嘗試單一登入時，會視需要自動建立使用者。

> [!NOTE]
> 您可以使用任何其他的 Veracode 使用者帳戶建立工具或Veracode 提供的 API 來佈建 Azure AD 使用者帳戶。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

您在存取面板中選取 [Veracode]  時，應該會自動登入您設定 SSO 的 Veracode。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 Veracode](https://aad.portal.azure.com/)