---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 edX for Business SAML Integration 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 edX for Business SAML Integration 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e00001b6-a8af-4547-976a-68d1c41d374c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e17ffa9330da4fec0cfa3260f9bb99fbaffc24f6
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466318"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-edx-for-business-saml-integration"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 edX for Business SAML Integration 整合

在本教學課程中，您將了解如何整合 edX for Business SAML Integration 與 Azure Active Directory (Azure AD)。 在整合 edX for Business SAML Integration 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制誰可以存取 edX for Business SAML Integration。
* 允許使用者使用其 Azure AD 帳戶，自動登入 edX for Business SAML Integration。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 可以使用 edX for Business SAML Integration 單一登入 (SSO) 的訂閱。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。
* edX for Business SAML Integration 支援 **SP** 起始的 SSO
* edX for Business SAML Integration 支援 **Just In Time** 使用者佈建

* 當您設定了 edX for Business SAML Integration 之後，就能施行工作階段控制，即時保護您組織的敏感性資料免遭外洩及滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-edx-for-business-saml-integration-from-the-gallery"></a>從資源庫新增 edX for Business SAML Integration

若要設定 edX for Business SAML Integration 與 Azure AD 的整合，必須從資源庫將 edX for Business SAML Integration 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中鍵入 **edX for Business SAML Integration**。
1. 從結果面板中選取 [edX for Business SAML Integration]，然後新增該應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-edx-for-business-saml-integration"></a>為 edX for Business SAML Integration 設定及測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者的身分，設定及測試 Azure AD SSO與 edX for Business SAML Integration 的搭配組合。 SSO 要能夠運作，必須建立 Azure AD 使用者與 edX for Business SAML Integration 中相關使用者之間的連結關係。

若要設定及測試 Azure AD SSO 與 edX for Business SAML Integration 的搭配組合，請完成下列基本組件：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 edX for Business SAML Integration SSO](#configure-edx-for-business-saml-integration-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 edX for Business SAML Integration 測試使用者](#create-edx-for-business-saml-integration-test-user)** - 在 edX for Business SAML Integration 中設定 B.Simon 的對應身分，並將其連結到 Azure AD 中該使用者的代表。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [edX for Business SAML Integration] 應用程式整合頁面上，尋找 [管理] 區段，然後選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，輸入下列欄位的值：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://courses.edx.org/dashboard?tpa_hint=<INSTANCE_NAME>`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 您可以連絡 [edX for Business SAML Integration 用戶端支援小組](mailto:api-support@edx.org)取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. edX for Business SAML Integration 應用程式需要特定格式的 SAML 判斷提示，而這需要您在 SAML 權杖屬性設定中，新增自訂的屬性對應。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性之外，edX for Business SAML Integration 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。
    
    | 名稱 | |  來源屬性|
    | ---------------| --------------- | --------- |
    | country | | user.country  |

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將資料儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)
### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您將透過授與 edX for Business SAML Integration 的存取權，允許 B.Simon 使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [edX for Business SAML Integration]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-edx-for-business-saml-integration-sso"></a>設定 edX for Business SAML Integration SSO

若要在 **edX for Business SAML Integration** 端設定單一登入，必須將**應用程式同盟中繼資料 URL** 傳送給 [edX for Business SAML Integration 支援小組](mailto:api-support@edx.org)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-edx-for-business-saml-integration-test-user"></a>建立 edX for Business SAML Integration 測試使用者

本節中將會在 edX for Business SAML Integration 中建立名為 Britta Simon 的使用者。 edX for Business SAML Integration 支援 Just-In-Time 使用者佈建，這是預設會啟用的功能。 在這一節沒有您需要進行的動作項目。 若 edX for Business SAML Integration 中尚無任何使用者，將會在驗證之後建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下 [存取面板] 中的 [edX for Business SAML Integration] 磚時，應能自動登入已經設定 SSO 的 edX for Business SAML Integration。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [試用 edX for Business SAML Integration 與 Azure AD 的搭配組合](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階的視覺功能與控制項保護 edX for Business SAML Integration](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

