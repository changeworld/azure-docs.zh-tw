---
title: 教學課程：Azure Active Directory 與 SAP Qualtrics 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP Qualtrics 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 07340cb66fe9ef2f22e1f3e9b201e46dd07254bb
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622306"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 SAP Qualtrics 整合

在此教學課程中，您將了解如何整合 SAP Qualtrics 與 Azure Active Directory (Azure AD)。 在整合 SAP Qualtrics 與 Azure AD 時，您可以︰

* 您可以在 Azure AD 中控制可存取 SAP Qualtrics 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SAP Qualtrics。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 SAP Qualtrics 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* SAP Qualtrics 支援由 **SP** 和 **IDP** 起始的 SSO。
* SAP Qualtrics 支援 **Just In Time** 使用者佈建。

## <a name="add-sap-qualtrics-from-the-gallery"></a>從資源庫新增 SAP Qualtrics

若要設定將 SAP Qualtrics 整合到 Azure AD 中，您需要從資源庫將 SAP Qualtrics 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格上，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **SAP Qualtrics**。
1. 從結果中選取 [SAP Qualtrics]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>設定及測試 SAP Qualtrics的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 SAP Qualtrics 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SAP Qualtrics 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP Qualtrics 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。
    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. [設定 SAP Qualtrics SSO](#configure-sap-qualtrics-sso) 以在應用程式端設定單一登入設定。
    1. [建立 SAP Qualtrics 測試使用者](#create-sap-qualtrics-test-user)，以在 SAP Qualtrics 中建立 B.Simon 的對應項目，此項目須與 Azure AD 中代表該使用者的項目連結。
1. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [ **SAP Qualtrics** 應用程式整合] 頁面的 [Azure 入口網站] 中，尋找 [ **管理** ] 區段。 選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 組態]  的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入]  頁面上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：
    
    a. 在 [識別碼]  文字方塊中，以下列模式輸入 URL：

    `https://< DATACENTER >.qualtrics.com`
   
    b. 在 [回覆 URL]  文字方塊中，以下列模式輸入 URL︰

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. 在 [轉送狀態]  文字方塊中，以下列模式輸入 URL︰

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. 如果您想要以 **SP** 起始的模式設定應用程式，請選取 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的登入 URL、識別碼、回覆 URL 和轉送狀態來更新這些值。 若要取得這些值，請連絡 [Qualtrics 用戶端支援小組](https://www.qualtrics.com/support/)。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，選取 [複製] 圖示以複製 **應用程式同盟中繼資料 URL**，並儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下密碼。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAP Qualtrics 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [SAP Qualtrics]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  。 然後，在 [新增指派]  對話方塊中，選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊的使用者清單中，選取 [B.Simon]  。 然後，選擇畫面底部的 [選取]  按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 然後，選擇畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

## <a name="configure-sap-qualtrics-sso"></a>設定 SAP Qualtrics SSO

若要在 SAP Qualtrics 端設定單一登入，請將從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  傳送給 [SAP Qualtrics 支援小組](https://www.qualtrics.com/support/)。 此支援小組會確定兩端的 SAML SSO 連線都已正確設定。

### <a name="create-sap-qualtrics-test-user"></a>建立 SAP Qualtrics 測試使用者

SAP Qualtrics 支援依預設啟用的 Just-In-Time 使用者佈建。 沒有您需要進行的額外動作。 如果 SAP Qualtrics 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

#### <a name="sp-initiated"></a>SP 起始：

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 SAP Qualtrics 登入 URL，您可以在其中起始登入流程。  

* 直接移至 SAP Qualtrics 登入 URL，並從該處起始登入流程。

#### <a name="idp-initiated"></a>IDP 起始：

* 按一下 [在 Azure 入口網站 **測試此應用程式** ]，您應該會自動登入您已設定 SSO 的 SAP Qualtrics。

您也可以使用 Microsoft「我的應用程式」，以任何模式測試應用程式。 當您在我的應用程式中按一下 [SAP Qualtrics] 圖格時，如果在 SP 模式中設定，系統會將您重新導向至應用程式登入頁面以起始登入流程，如果是在 IDP 模式中設定，您應該會自動登入您已設定 SSO 的 SAP Qualtrics。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 SAP Qualtrics 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 如需詳細資訊，請參閱[了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。