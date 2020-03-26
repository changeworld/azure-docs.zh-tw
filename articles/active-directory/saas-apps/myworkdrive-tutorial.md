---
title: 教學課程：Azure Active Directory 與 MyWorkDrive 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 MyWorkDrive 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: c733873a5545bb01f03abd8178ed2f371e2dd3aa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74074074"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>教學課程：整合 MyWorkDrive 與 Azure Active Directory

在本教學課程中，您會了解如何整合 MyWorkDrive 與 Azure Active Directory (Azure AD)。 在整合 MyWorkDrive 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 MyWorkDrive 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 MyWorkDrive。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 MyWorkDrive 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 MyWorkDrive 支援由 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>從資源庫新增 MyWorkDrive

若要設定 MyWorkDrive 與 Azure AD 整合，您需要從資源庫將 MyWorkDrive 新增到受控 SaaS App 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **MyWorkDrive**。
1. 從結果面板選取 [MyWorkDrive]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 MyWorkDrive 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 MyWorkDrive 中相關使用者之間的連結關聯性。

若要設定及測試與 MyWorkDrive 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 MyWorkDrive SSO](#configure-myworkdrive-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 MyWorkDrive 測試使用者](#create-myworkdrive-test-user)** - 讓 MyWorkDrive 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [MyWorkDrive]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的回覆 URL 與登入 URL 更新這些值。 輸入您自己公司的 MyWorkDrive 伺服器主機：例如
    > 
    > 回覆 URL：`https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > 登入 URL：`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > 如果您不確定如何針對這些值設定自己的主機名稱和 SSL 憑證，請連絡 [MyWorkDrive 支援小組](mailto:support@myworkdrive.com)。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以將 [應用程式同盟中繼資料 URL]  複製到剪貼簿。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>設定 MyWorkDrive SSO

1. 若要自動執行 MyWorkDrive 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 MyWorkDrive]  便會將您導向到 MyWorkDrive 應用程式。 請從該處提供用以登入 MyWorkDrive 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 4。

    ![設定組態](common/setup-sso.png)

1. 如果您要手動設定 MyWorkDrive，請在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 MyWorkDrive。

1. 在管理面板中的 MyWorkDrive 伺服器上按一下 **ENTERPRISE**，並執行下列步驟：

    ![管理員](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. 啟用 [SAML/ADFS SSO]  。

    b. 選取 [SAML - Azure AD] 

    c. 在 [Azure App 同盟中繼資料 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  值。

    d. 按一下 [儲存] 

    > [!NOTE]
    > 如需詳細資訊，請檢閱 [MyWorkDrive Azure AD 支援文章](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 MyWorkDrive 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [MyWorkDrive]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，從使用者清單選取 **Britta Simon**，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-myworkdrive-test-user"></a>建立 MyWorkDrive 測試使用者

在本節中，您要在 MyWorkDrive 中建立名為 Britta Simon 的使用者。 請與 [MyWorkDrive 支援小組](mailto:support@myworkdrive.com)合作，在 MyWorkDrive 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [MyWorkDrive] 圖格時，應該會自動登入您設定 SSO 的 MyWorkDrive。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)