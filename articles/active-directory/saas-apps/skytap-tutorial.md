---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 HSingle Sign-on for Skytap 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Single Sign-on for Skytap 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565758"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Single Sign-on for Skytap 整合

在本教學課程中，您將了解如何整合 Single Sign-on for Skytap 與 Azure Active Directory (Azure AD)。 整合 Single Sign-on for Skytap 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 Single Sign-on for Skytap 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Single Sign-on for Skytap。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Single Sign-on for Skytap 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Single Sign-on for Skytap 支援 SP 和 IDP 起始的 SSO。
* 在設定 Single Sign-on for Skytap 之後，您便可以強制執行工作階段控制。 這可即時保護貴組織的敏感性資料免於外洩和遭到滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>從資源庫新增 Single Sign-on for Skytap

若要設定將 Single Sign-on for Skytap 整合到 Azure AD 中，您需要從資源庫將 Single Sign-on for Skytap 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Single Sign-on for Skytap**。
1. 從結果面板中選取 [Single Sign-on for Skytap]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>設定及測試 Single Sign-on for Skytap 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Single Sign-on for Skytap 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，請在 Azure AD 使用者與 Single Sign-on for Skytap 中的相關使用者之間建立連結關聯性。

下面的一般步驟可供您設定及測試與 Single Sign-on for Skytap 搭配運作的 Azure AD SSO：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。

    a. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。

    b. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Single Sign-on for Skytap SSO](#configure-single-sign-on-for-skytap-sso)** 以在應用程式端設定單一登入設定。

    a. **[建立 Single Sign-on for Skytap 測試使用者](#create-single-sign-on-for-skytap-test-user)** ，讓 Single Sign-on for Skytap 中包括 B.Simon 對應項。 此對應項會與 Azure AD 中出現的使用者連結。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Single Sign-on for Skytap]  應用程式整合頁面上，尋找 [管理]  區段。 選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 組態]  的鉛筆圖示，以編輯設定。

   ![以 SAML 設定單一登入頁面的螢幕擷取畫面，醒目提示鉛筆圖示](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段中，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，以下列模式輸入 URL：`http://pingone.com/<custom EntityID>`

    b. 在 [回覆 URL]  文字方塊中，以下列模式輸入 URL︰`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. 如果您想要以 **SP** 起始的模式設定應用程式，請選取 [設定其他 URL]  ，然後執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. 在 [轉送狀態]  文字方塊中，以下列模式輸入 URL︰`https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「識別碼」、「回覆 URL」、「登入 URL」及「轉送狀態」來更新這些值。 請連絡 [Single Sign-on for Skytap 用戶端支援小組](mailto:support@skytap.com)，以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找**同盟中繼資料 XML**。 選取 [下載]  以下載中繼資料檔案，並將其儲存在您的電腦上。

    ![憑證下載連結的螢幕擷取畫面](common/metadataxml.png)

1. 在 [設定 Single Sign-on for Skytap]  區段上，根據您的需求複製一或多個適當的 URL。

    ![複製設定 URL 的螢幕擷取畫面](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  欄位中顯示的值。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Single Sign-on for Skytap 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [Single Sign-on for Skytap]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![管理區段的螢幕擷取畫面，醒目提示使用者和群組](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![使用者和群組使用者及群組頁面的螢幕擷取畫面，其中已醒目提示 [新增使用者]](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [B.Simon]  。 然後選擇畫面底部的 [選取]  按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 然後選擇畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-single-sign-on-for-skytap-sso"></a>設定 Single Sign-on for Skytap SSO

若要在 Single Sign-on for Skytap 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和所複製的適當 URL 傳送給 [Single Sign-on for Skytap 用戶端支援小組](mailto:support@skytap.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。


### <a name="create-single-sign-on-for-skytap-test-user"></a>建立 Single Sign-on for Skytap 測試使用者

在本節中，您要在 Single Sign-on for Skytap 中建立名為 B.Simon 的使用者。 與 [Single Sign-on for Skytap 用戶端支援小組合作](mailto:support@skytap.com)，在 Single Sign-on for Skytap 平台中新增使用者。 您必須先建立和啟動使用者，才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Single Sign-on for Skytap] 圖格時，應該會自動登入您設定 SSO 的 Single Sign-on for Skytap。 如需詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Slack](https://aad.portal.azure.com/)

