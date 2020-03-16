---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Salesforce 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Salesforce 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a875cee7e6796a2c865bde4a62f2f0463eb12130
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967710"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Salesforce 整合

在本教學課程中，您會了解如何整合 Salesforce 與 Azure Active Directory (Azure AD)。 在整合 Salesforce 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Salesforce 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Salesforce。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Salesforce 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Salesforce 支援 **SP** 起始的 SSO

* Salesforce 支援[**自動化的**使用者佈建和取消佈建](salesforce-provisioning-tutorial.md) (建議選項)

* Salesforce 支援 **Just In Time** 使用者佈建

* Salesforce 行動應用程式現在可以搭配 Azure AD 設定來啟用 SSO。 在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。
* 設定 Salesforce 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>從資源庫新增 Salesforce

若要設定 Salesforce 與 Azure AD 的整合作業，您必須從資源庫將 Salesforce 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Salesforce**。
1. 從結果面板選取 [Salesforce]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>設定及測試 Salesforce 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Salesforce 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Salesforce 中相關使用者之間的連結關聯性。

若要設定及測試與 Salesforce 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Salesforce SSO](#configure-salesforce-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Salesforce 測試使用者](#create-salesforce-test-user)** - 使 Salesforce 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Salesforce]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入值：

    企業帳戶： `https://<subdomain>.my.salesforce.com`

    開發人員帳戶： `https://<subdomain>-dev-ed.my.salesforce.com`

    b. 在 [識別碼]  文字方塊中，使用下列模式將值輸入：

    企業帳戶： `https://<subdomain>.my.salesforce.com`

    開發人員帳戶： `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Salesforce 用戶端支援小組](https://help.salesforce.com/support)以取得這些值。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Salesforce]  區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Salesforce 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Salesforce]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-salesforce-sso"></a>設定 Salesforce SSO

1. 若要自動執行 Salesforce 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 Salesforce]  便會將您導向至 Salesforce 單一登入應用程式。 請從該處提供用以登入 Salesforce 單一登入的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 13。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 Salesforce，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Salesforce 公司網站，然後執行下列步驟：

1. 按一下分頁右上角**設定圖示**底下的 [設定]  。

    ![設定單一登入](./media/salesforce-tutorial/configure1.png)

1. 在瀏覽窗格中向下捲動至 [設定]  ，按一下 [識別]  以展開相關的區段。 然後按一下 [單一登入設定]  。

    ![設定單一登入](./media/salesforce-tutorial/sf-admin-sso.png)

1. 在 [單一登入設定]  頁面上，按一下 [編輯]  按鈕。

    ![設定單一登入](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > 如果您的 Salesforce 帳戶無法啟用單一登入設定，您可能需要連絡 [Salesforce 用戶端支援小組](https://help.salesforce.com/support)。

1. 選取 [啟用 SAML]  ，然後按一下 [儲存]  。

    ![設定單一登入](./media/salesforce-tutorial/sf-enable-saml.png)

1. 若要設定 SAML 單一登入設定，請按一下 [從中繼資料檔案新增]  。

    ![設定單一登入](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. 按一下 [**選擇檔案**] 以上傳您從 Azure 入口網站下載的中繼資料 XML 檔案，然後按一下 [**建立**]。

    ![設定單一登入](./media/salesforce-tutorial/xmlchoose.png)

1. 在 [SAML 單一登入設定]  頁面上，欄位會自動填入，選取 [啟用使用者佈建]  然後按一下 [儲存]  。

    ![設定單一登入](./media/salesforce-tutorial/salesforcexml.png)

1. 在 Salesforce 的左方導覽窗格中，按一下 [公司設定]  以展開相關的區段，然後按一下 [我的網域]  。

    ![設定單一登入](./media/salesforce-tutorial/sf-my-domain.png)

1. 向下捲動至 [驗證組態]  區段，然後按一下 [編輯]  按鈕。

    ![設定單一登入](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. 在 [驗證組態]  區段中，核取 [AzureSSO]  作為 SAML SSO 設定的 [驗證服務]  ，然後按一下 [儲存]  。

    ![設定單一登入](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 如果選取了一個以上的驗證服務，當使用者嘗試啟動單一登入到您的 Salesforce 環境時，將會提示使用者選取登入時想要使用的驗證服務。 如果您不想發生這種情形，您應該**不要核取其他所有的驗證服務**。

### <a name="create-salesforce-test-user"></a>建立 Salesforce 測試使用者

本節會在 Salesforce 中建立名為 B.Simon 的使用者。 Salesforce 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 如果 Salesforce 中還沒有該使用者，當您嘗試存取 Salesforce 時，就會建立新的使用者。 Salesforce 也支援自動使用者佈建，您可以在[這裡](salesforce-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Salesforce] 圖格時，應該會自動登入您已設定 SSO 的 Salesforce。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="test-sso-for-salesforce-mobile"></a>測試 Salesforce 的 SSO (行動裝置)

1. 開啟 Salesforce 行動應用程式。 在登入頁面上，按一下 [使用自訂網域]  。

    ![Salesforce 行動應用程式](media/salesforce-tutorial/mobile-app1.png)

1. 在 [自訂網域]  文字方塊中，輸入您已註冊的自網域名稱，然後按一下 [繼續]  。

    ![Salesforce 行動應用程式](media/salesforce-tutorial/mobile-app2.png)

1. 輸入您的 Azure AD 認證以登入 Salesforce 應用程式，然後按 [下一步]  。

    ![Salesforce 行動應用程式](media/salesforce-tutorial/mobile-app3.png)

1. 在 [允許存取]  頁面上 (如下所示)，按一下 [允許]  ，以授與 Salesforce 應用程式的存取權。

    ![Salesforce 行動應用程式](media/salesforce-tutorial/mobile-app4.png)

1. 最後，成功登入之後，應用程式首頁會隨即顯示。

    ![Salesforce 行動應用程式](media/salesforce-tutorial/mobile-app5.png) ![Salesforce 行動應用程式](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [設定使用者佈建](salesforce-provisioning-tutorial.md)

- [嘗試搭配 Azure AD 使用 Salesforce](https://aad.portal.azure.com)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Salesforce](https://docs.microsoft.com/cloud-app-security/protect-salesforce)