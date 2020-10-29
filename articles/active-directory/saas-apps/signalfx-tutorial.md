---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 SignalFx 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SignalFx 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516064"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SignalFx 整合

在本教學課程中，您將了解如何整合 SignalFx 與 Azure Active Directory (Azure AD)。 在整合 SignalFx 與 Azure AD 時，您可以︰

* 從 Azure AD 中控制可存取 SignalFx 的人員；
* 讓使用者使用其 Azure AD 帳戶自動登入 SignalFx；以及
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

在開始之前，您必須：

* Azure AD 訂用帳戶
    * 如果您沒有訂用帳戶，可以[在此取得免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SignalFx 單一登入 (SSO) 的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您將在測試環境中設定和測試 Azure AD SSO。

* SignalFx 支援由 **IDP** 起始的 SSO
* SignalFx 支援 **Just In Time** 使用者佈建
* 設定 SignalFx 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。

## <a name="step-1-add-the-signalfx-application-in-azure"></a>步驟 1:在 Azure 中新增 SignalFx 應用程式

使用這些指示，將 SignalFx 應用程式新增至受控 SaaS 應用程式清單。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左側導覽視窗中，選取 [Azure Active Directory]  。
1. 選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入並選取 [SignalFx]  。
     * 您可能需要等候幾分鐘，應用程式才會新增至您的租用戶。
1. 保持 Azure 入口網站為開啟，然後開啟新的網頁索引標籤。    

## <a name="step-2-begin-signalfx-sso-configuration"></a>步驟 2:開始 SignalFx SSO 組態

使用這些指示開始進行 SignalFx SSO 的組態流程。

1. 在新開啟的索引標籤中，存取並登入 SignalFx UI。 
1. 在頂端功能表中，按一下 [整合]  。 
1. 在搜尋欄位中，輸入並選取 [Azure Active Directory]  。
1. 按一下 [建立新整合]  。
1. 在 [名稱]  中，輸入您的使用者可輕鬆理解的易辨識名稱。
1. 選擇 [在登入頁面上顯示]  。
    * 這項功能會在您的使用者可以點按的登入頁面中顯示自訂按鈕。 
    * 您在 [名稱]  中輸入的資訊將會出現在按鈕上。 因此，請輸入您的使用者可以辨識的 **名稱** 。 
    * 只有使用 SignalFx 應用程式的自訂子網域 (例如 **yourcompanyname.signalfx.com** ) 時，此選項才會運作。 若要取得自訂子網域，請聯絡 SignalFx 支援。 
1. 複製 **整合識別碼** 。 稍後的步驟將會需要這項資訊。 
1. 讓 SignalFx UI 保持開啟。 

## <a name="step-3-configure-azure-ad-sso"></a>步驟 3：設定 Azure AD SSO

使用這些指示，在 Azure 入口網站中啟用 Azure AD SSO。

1. 回到 [Azure 入口網站](https://portal.azure.com/)的 [SignalFx]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 組態]  的畫筆 (編輯) 圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入]  頁面上，完成下列欄位： 

    a. 在 [識別碼]  中，輸入下列 URL `https://api.<realm>.signalfx.com/v1/saml/metadata` 並將 `<realm>` 以您的 SignalFx 領域取代。 

    b. 在 [回覆 URL]  中，輸入下列 URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` 並將 `<realm>` 以您的 SignalFx 領域取代，並將 `<integration ID>` 以您先前從 SignalFx UI 複製的 **整合識別碼** 取代。

1. SignalFx 應用程式需要特定格式的 SAML 判斷提示，您需要將自訂屬性對應加入您的 SAML 權杖屬性組態。 
    
1. 請檢閱並確認下列宣告是否對應到 Active Directory 中填入的來源屬性。 

    | 名稱 |  來源屬性|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > 此流程需要使用至少一個已驗證的自訂網域來設定您的 Active Directory，以及能夠存取此網域中的電子郵件帳戶的權限。 如果您不確定或需要此組態的協助，請連絡 SignalFx 支援。  

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  ，接著選取 [下載]  。 下載憑證，並儲存在您的電腦上。 接著，複製 **應用程式同盟中繼資料 URL** 值；您在 SignalFx UI 的後續步驟中會需要這項資訊。 

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 SignalFx]  區段中，複製 [Azure AD 識別碼]  值。 您在 SignalFx UI 的後續步驟中會需要這項資訊。 

## <a name="step-4-create-an-azure-ad-test-user"></a>步驟 4：建立 Azure AD 測試使用者

使用這些指示在 Azure 入口網站中建立名為 **B.Simon** 的測試使用者。

1. 在 Azure 入口網站的左側瀏覽視窗中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在頁面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性視窗中：
   1. 在 [使用者名稱]  中，輸入 `username@companydomain.extension`，例如 `b.simon@contoso.com`。
   1. 在 [名稱]  中輸入 `B.Simon`。
   1. 選擇 [顯示密碼]  ，然後在 [密碼]  中複製顯示的值。 您在後續步驟中會需要這項資訊，才能測試此整合。 
   1. 按一下頁面底部的 [新增]  。

## <a name="step-5-assign-the-azure-ad-test-user"></a>步驟 5：指派 Azure AD 測試使用者

使用這些指示，讓測試使用者可以使用適用於 SignalFx 的 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SignalFx]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者]  清單中選取 [B.Simon]  ，然後按一下頁面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  。

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>步驟 6：完成 SignalFx SSO 組態 

1. 開啟之前的索引標籤，然後返回 SignalFx UI 以檢視目前的 Azure Active Directory 整合頁面。 
1. 在 [憑證 (Base64)]  旁，按一下 [上傳檔案]  ，然後找出您先前從 Azure 入口網站下載的 **Base64 編碼憑證** 檔案。
1. 在 [Azure AD 識別碼]  旁，貼上您先前從 Azure 入口網站複製的 **Azure AD 識別碼** 值。 
1. 在 [同盟中繼資料 URL]  旁，貼上您先前從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  值。 
1. 按一下 [檔案]  。

## <a name="step-7-test-sso"></a>步驟 7：測試 SSO

請檢閱下列有關如何測試 SSO 的資訊，以及第一次登入 SignalFx 的預期體驗。 

### <a name="test-logins"></a>測試登入

* 若要測試登入，您應該使用私人/incognito 視窗，也可以登出 Azure 入口網站。 如果不這麼做，設定應用程式的使用者 Cookie 將會干擾系統，並阻止測試使用者成功登入。

* 當新的測試使用者第一次登入時，Azure 會強制變更密碼。 發生這種情況時，SSO 登入流程將不會完成；系統會將測試使用者導向至 Azure 入口網站。 若要進行疑難排解，測試使用者應變更密碼，然後瀏覽至 SignalFx 登入頁面或存取面板，然後再試一次。
    * 您在存取面板中按一下 SignalFx 圖格時，應該會自動登入 SignalFx。 
        * 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

* 您可以從存取面板或透過指派給組織的自訂登入頁面，存取 SignalFx 應用程式。 測試使用者應該從這些位置的其中一個開始測試整合。
    * 測試使用者可以使用稍早在此流程中針對 **b.simon\@contoso.com** 所建立的認證。

### <a name="first-time-logins"></a>第一次登入

* 當使用者第一次從 SAML SSO 登入 SignalFx 時，會收到包含連結的 SignalFx 電子郵件。 使用者必須按一下連結，以進行驗證。 只有第一次登入的使用者才會收到電子郵件驗證。 

* SignalFx 支援 **Just In Time** 使用者建立，這表示如果使用者不存在於 SignalFx 中，則會在第一次嘗試登入時建立使用者的帳戶。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](/cloud-app-security/proxy-intro-aad)

- [嘗試搭配 Azure AD 使用 SignalFx](https://aad.portal.azure.com/)