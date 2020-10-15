---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Catchpoint 整合
description: 了解如何設定 Azure Active Directory 與 Catchpoint 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: 649396b81402e9229eb9ea2c627b60f249f8c601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88530251"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>教學課程：Azure Active Directory 單一登入與 Catchpoint 整合

在本教學課程中，您將了解如何整合 Catchpoint 與 Azure Active Directory (Azure AD)。 在整合 Catchpoint 與 Azure AD 時，您可以︰

* 控制使用者從 Azure AD 到 Catchpoint 的存取權。
* 為具有 Azure AD 帳戶的使用者啟用自動 Catchpoint 登入。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 Catchpoint 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Catchpoint 支援 SP 起始和 IDP 起始的 SSO。
* Catchpoint 支援 Just-In-Time (JIT) 使用者佈建。
* 設定 Catchpoint 後，您可以強制執行工作階段控制項。 這項預防措施可即時保護貴組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項是條件式存取的延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-catchpoint-from-the-gallery"></a>從資源庫新增 Catchpoint

若要設定將 Catchpoint 整合到 Azure AD 中，請將 Catchpoint 新增到受控 SaaS 應用程式清單。

1. 使用公司、學校或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Catchpoint**。
1. 從結果面板中選取 [Catchpoint]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>設定及測試 Catchpoint 的 Azure AD 單一登入

若要讓 SSO 能夠正常執行，您必須將 Azure AD 使用者與 Catchpoint 中的使用者連結。 在本教學課程中，我們將設定名為 **B.Simon** 的測試使用者。 

請完成下列各節：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓使用者能夠使用此功能。
    * [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
    * [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. [設定 Catchpoint SSO](#configure-catchpoint-sso)，以在應用程式端設定單一登入設定。
    * [建立 Catchpoint 測試使用者](#create-a-catchpoint-test-user)，以允許在 Catchpoint 中將 B.Simon Azure AD 測試帳戶連結至類似的使用者帳戶。
1. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照 Azure 入口網站中的下列步驟，啟用 Azure AD SSO：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在 **Catchpoint** 應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取畫筆圖示以編輯**基本 SAML 組態**的設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 設定 Catchpoint 的起始模式：
   - 針對 **IDP** 起始模式，請輸入以下欄位的值：
     - 針對**識別碼**：`https://portal.catchpoint.com/SAML2`
     - 針對**回覆 URL**：`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - 針對 **SP** 起始模式，請選取 [設定其他 Url]  ，然後輸入下列值：
     - 針對**登入 URL**：`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint 應用程式需要特定格式的 SAML 判斷提示。 將自訂屬性對應新增至 SAML 權杖屬性的組態中。 下表包含預設屬性清單。

    | 名稱 | 來源屬性|
    | ------------ | --------- |
    | Givenname | user.givenneame |
    | Surname | user.surname |
    | Emailaddress | user.mail |
    | 名稱 | user.userprincipalname |
    | 唯一的使用者識別碼 | user.userprincipalname |

    ![使用者屬性與宣告清單的螢幕擷取畫面](common/default-attributes.png)

1. 此外，Catchpoint 應用程式也需要在 SAML 回應中傳遞另一個屬性。 請參閱下表。 這個屬性也會預先填入，但您可以根據需求加以檢閱和更新。

    | 名稱 | 來源屬性|
    | ------------ | --------- |
    | 命名空間 | user.assignedrole |

    > [!NOTE]
    > `namespace` 宣告必須與帳戶名稱對應。 此帳戶名稱應設定為 Azure AD 中的角色，並會在 SAML 回應中傳回。 若要深入了解 Azure AD 中的角色，請參閱[針對企業應用程式設定 SAML 權杖中發出的角色宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)。

1. 移至**以 SAML 設定單一登入**頁面。 在 [SAML 簽署憑證]  區段中，找到 [憑證 (Base64)]  。 選取 [下載]  以將憑證儲存到您的電腦。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Catchpoint]  區段中，複製您在稍後步驟中需要的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會使用 Azure 入口網站建立名為 B.Simon 的 Azure AD 測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如，輸入 `B.Simon@contoso.com`。
   1. 選取 [顯示密碼]  核取方塊。 請記下顯示的密碼值。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Catchpoint 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [Catchpoint]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![「新增使用者」連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的使用者清單中，選取 [B.Simon]  。 按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要角色值，請在 [選取角色]  對話方塊中尋找，並從清單中選取使用者角色。 按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-catchpoint-sso"></a>設定 Catchpoint SSO

1. 在不同的網頁瀏覽器視窗中，以管理員身分登入 Catchpoint 應用程式。

1. 選取 [設定]  圖示，然後選取 [SSO 識別提供者]  。

    ![已選取 SSO 識別提供者的 Catchpoint 設定螢幕擷取畫面](./media/catchpoint-tutorial/configuration1.png)

1. 在 [單一登入]  頁面上，輸入下列欄位：

   ![Catchpoint 單一登入頁面螢幕擷取畫面](./media/catchpoint-tutorial/configuration2.png)

   欄位 | 值
   ----- | ----- 
   **Namespace** | 有效的命名空間值。
   **識別提供者簽發者** | Azure 入口網站中的 `Azure AD Identifier` 值。
   **單一登入 URL** | Azure 入口網站中的 `Login URL` 值。
   **[MSSQLSERVER 的通訊協定內容]** | 從 Azure 入口網站下載的 `Certificate (Base64)` 檔案內容。 使用記事本來檢視和複製。

   您也可以選取 [上傳中繼資料]  選項，來上傳**同盟中繼資料 XML**。

1. 選取 [儲存]  。

### <a name="create-a-catchpoint-test-user"></a>建立 Catchpoint 測試使用者

Catchpoint 支援依預設啟用的 Just-In-Time 使用者佈建。 您在此區段中沒有任何動作項目。 如果 Catchpoint 中還沒有出現 B.Simon 這位使用者，在驗證之後就會建立。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

在我的應用程式入口網站中選取 [Catchpoint] 圖格時，應該會自動登入您已設定 SSO 的 Catchpoint。 如需我的應用程式入口網站的詳細資訊，請參閱[登入我的應用程式入口網站並啟動應用程式](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)。

> [!NOTE]
> 當您透過登入頁面登入 Catchpoint 應用程式時，在提供 **Catchpoint 認證**後，請在 [公司認證 (SSO)]  欄位中輸入有效的**命名空間**值，然後選取 [登入]  。
> 
> ![Catchpoint 組態](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>其他資源

- [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試使用 Catchpoint 搭配Azure AD](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
