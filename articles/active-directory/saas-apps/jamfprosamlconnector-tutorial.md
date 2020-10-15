---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Jamf Pro 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Jamf Pro 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.openlocfilehash: 780421d93916c7da7897dfa15d09dc895cf56280
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552657"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>教學課程：Azure Active Directory SSO 與 Jamf Pro 整合

在本教學課程中，您將了解如何整合 Jamf Pro 與 Azure Active Directory (Azure AD)。 在整合 Jamf Pro 與 Azure AD 時，您可以︰

* 以使用 Azure AD 來控制可存取 Jamf Pro 的人員。
* 讓您的使用者使用其 Azure AD 帳戶自動登入 Jamf Pro。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱[搭配 Azure Active Directory 的單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 Jamf Pro 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 

* Jamf Pro 支援 **SP 起始**和 **IDP 起始**的 SSO。
* 設定 Jamf Pro 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>從資源庫新增 Jamf Pro

若要設定 Jamf Pro 與 Azure AD 整合，您需要從資源庫將 Jamf Pro 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 Jamf Pro  。
1. 從結果面板選取 [Jamf Pro]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>針對 Jamf Pro 在 Azure AD 中設定和測試 SSO

以名為 B.Simon 的測試使用者，設定及測試與 Jamf Pro 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Jamf Pro 中相關使用者之間的連結關聯性。

在本節中，您會設定及測試搭配 Jamf Pro 的 Azure AD SSO。

1. [在 Azure AD 中設定 SSO](#configure-sso-in-azure-ad)，讓您的使用者可以使用這項功能。
    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以 B.Simon 帳戶測試 Azure AD SSO。
    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 可以在 Azure AD 中使用 SSO。
1. [設定 Jamf Pro 中的 SSO](#configure-sso-in-jamf-pro)，以在應用程式端進行 SSO 設定。
    1. [建立 Jamf Pro 測試使用者](#create-a-jamf-pro-test-user)，使 Jamf Pro 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. [測試 SSO 組態](#test-the-sso-configuration)，以驗證組態是否能運作。

## <a name="configure-sso-in-azure-ad"></a>在 Azure AD 中設定 SSO

在本節中，您會在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Jamf Pro]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的畫筆圖示，以編輯設定。

   ![編輯 [基本 SAML 組態] 頁面。](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段中，如果您想要以 **IDP 起始**模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，以下列公式輸入 URL：`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. 在 [回覆 URL]  文字方塊中，以下列公式輸入 URL：`https://<subdomain>.jamfcloud.com/saml/SSO`

1. 選取 [設定其他 URL]  。 如果您想要以 **SP 起始**模式設定應用程式，請在 [登入 URL]  文字方塊中使用下列公式輸入 URL：`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼、回覆 URL 及登入 URL 來更新這些值。 您將會從 Jamf Pro 入口網站的 [單一登入]  區段取得實際的識別碼值，本教學課程稍後將說明此值。 您可以從識別碼值擷取實際的子網域值，並使用該子網域資訊作為登入 URL 和回覆 URL。 您也可以參考 Azure 入口網站中 [基本 SAML 組態]  區段所示的公式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，選取 [複製]  按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將資料儲存在您的電腦上。

    ![SAML 簽署憑證下載連結](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。
   1. 在 [使用者名稱]  欄位中，輸入 [name]@[companydomain].[extension]。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會對 B.Simon 授與 Jamf Pro 的存取權。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Jamf Pro]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![選取 [使用者和群組]](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![選取 [新增使用者] 按鈕](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [B.Simon]  ，然後選取畫面底部的 [選取]  按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊中選取適當的使用者角色。 然後，選取畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  按鈕。

## <a name="configure-sso-in-jamf-pro"></a>在 Jamf Pro 中設定 SSO

1. 若要自動執行 Jamf Pro 內的設定，您必須選取 [安裝擴充功能]  以安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式安全登入瀏覽器擴充功能頁面](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，請選取 [設定 Jamf Pro]  。 當 Jamf Pro 應用程式開啟時，請提供系統管理員認證以登入。 瀏覽器擴充功能會自動設定應用程式，並自動執行步驟 3 到 7。

    ![Jamf Pro 中的設定組態頁面](common/setup-sso.png)

3. 若要手動設定 Jamf Pro，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Jamf Pro 公司網站。 然後執行下列步驟。

4. 選取頁面右上角的 [設定]  圖示。

    ![選取 Jamf Pro 中的設定圖示](./media/jamfprosamlconnector-tutorial/configure1.png)

5. 選取 [單一登入]  。

    ![在 Jamf Pro 中選取單一登入](./media/jamfprosamlconnector-tutorial/configure2.png)

6. 在 [單一登入]  頁面上，執行下列步驟。

    ![Jamf Pro 中的單一登入頁面](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. 選取 [編輯]  。

    b. 選取 [啟用單一登入驗證]  核取方塊。

  c. 從 [識別提供者]  下拉式功能表中，選取 [Azure]  作為選項。

  d. 複製 [實體識別碼]  值，並將其貼至 Azure 入口網站中 [基本 SAML 組態]  區段上的 [識別碼 (實體識別碼)]  欄位中。

> [!NOTE]
> 在 `<SUBDOMAIN>` 中使用此值來完成 Azure 入口網站上 [基本 SAML 設定]  區段中的 [登入 URL] 和 [回覆 URL]。

  e. 從 [識別提供者中繼資料來源]  下拉式功能表中選取 [中繼資料 URL]  。 在顯示的欄位中，貼上您從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  值。

  f. (選擇性) 編輯權杖到期日，或選取 [停用 SAML 權杖到期日]。

7. 在相同頁面上，向下捲動到 [使用者對應]  區段。 然後執行下列步驟。

    ![Jamf Pro 中 [單一登入] 頁面的 [使用者對應] 區段。](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. 針對 [識別提供者使用者對應]  選取 [NameID]  選項。 根據預設，此選項會設定為 [NameID]  ，但您可以定義自訂屬性。

    b. 針對 [Jamf Pro 使用者對應]  選取 [電子郵件]  。 Jamf Pro 會先根據使用者，然後再根據群組來對應由 IdP 所傳送的 SAML 屬性。 當使用者嘗試存取 Jamf Pro 時，Jamf Pro 會從識別提供者中取得該使用者的相關資訊，並比對所有 Jamf Pro 使用者帳戶。 如果找不到傳入的使用者帳戶，則 Jamf Pro 會嘗試依群組名稱來進行比對。

    c. 將值 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 貼到 [識別提供者群組屬性名稱]  欄位中。

    d. 在相同頁面上，向下捲動至 [安全性]  區段，然後選取 [允許使用者略過單一登入驗證]  。 如此一來，使用者將不會重新導向至身分識別提供者登入頁面來進行驗證，而是可以直接登入 Jamf Pro。 當使用者嘗試透過識別提供者存取 Jamf Pro 時，就會進行 IdP 啟始的 SSO 驗證和授權。

    e. 選取 [儲存]  。

### <a name="create-a-jamf-pro-test-user"></a>建立 Jamf Pro 測試使用者

為了讓 Azure AD 使用者能夠登入 Jamf Pro，必須將他們佈建到 Jamf Pro。 在 Jamf Pro 中，需以手動方式進行佈建。

若要佈建使用者帳戶，請執行下列步驟：

1. 以系統管理員身分登入您的 Jamf Pro 公司網站。

2. 選取頁面右上角的 [設定]  圖示。

    ![Jamf Pro 中的設定圖示](./media/jamfprosamlconnector-tutorial/configure1.png)

3. 選取 [Jamf Pro 使用者帳戶和群組]  。

    ![Jamf Pro 設定中的 [Jamf Pro 使用者帳戶和群組] 圖示](./media/jamfprosamlconnector-tutorial/user1.png)

4. 選取 [ **新增**]。

    ![Jamf Pro 使用者帳戶和群組的系統設定頁面](./media/jamfprosamlconnector-tutorial/user2.png)

5. 選取 [建立標準帳戶]  。

    ![[Jamf Pro 使用者帳戶和群組] 頁面中的 [建立標準帳戶] 選項](./media/jamfprosamlconnector-tutorial/user3.png)

6. 在 [新增帳戶]  對話方塊中，執行下列步驟：

    ![Jamf Pro 系統設定中的新增帳戶設定選項](./media/jamfprosamlconnector-tutorial/user4.png)

    a. 在 [使用者名稱]  欄位中，輸入 `Britta Simon` (測試使用者的全名)。

    b. 選取符合您組織的 [存取層級]  、[授權集合]  和 [存取狀態]  選項。

    c. 在 [全名]  欄位中，輸入 `Britta Simon`。

    d. 在 [電子郵件地址]  欄位中，輸入 Britta Simon 帳戶的電子郵件地址。

    e. 在 [密碼]  欄位中，輸入使用者的密碼。

    f. 在 [驗證密碼]  欄位中，再次輸入使用者的密碼。

    g. 選取 [儲存]  。

## <a name="test-the-sso-configuration"></a>測試 SSO 組態

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Jamf Pro] 圖格時，應該會自動登入您已設定 SSO 的 Jamf Pro 帳戶。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [嘗試搭配 Azure AD 使用 Jamf Pro](https://aad.portal.azure.com/)