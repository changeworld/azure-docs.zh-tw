---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 HRworks Single Sign-On 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 HRworks Single Sign-On 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: 1a0fff21c85104498895c9de20de9961a77cffeb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820489"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 HRworks Single Sign-On 整合

在本教學課程中，您將了解如何整合 HRworks Single Sign-On 與 Azure Active Directory (Azure AD)。 整合 HRworks Single Sign-On 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 HRworks Single Sign-On 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 HRworks Single Sign-On。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 HRworks Single Sign-On 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* HRworks Single Sign-On 支援 **SP** 起始的 SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>從資源庫新增 HRworks Single Sign-On

若要設定將 HRworks Single Sign-On 整合到 Azure AD 中，您需要從資源庫將 HRworks Single Sign-On 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **HRworks Single Sign-On**。
1. 從結果面板中選取 [HRworks Single Sign-On]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>設定及測試 HRworks Single Sign-On 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 HRworks Single Sign-On 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 HRworks Single Sign-On 中相關使用者之間的連結關聯性。

若要設定及測試與 HRworks Single Sign-On 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 HRworks Single Sign-On SSO](#configure-hrworks-single-sign-on-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 HRworks Single Sign-On 測試使用者](#create-hrworks-single-sign-on-test-user)** - 使 HRworks Single Sign-On 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [HRworks Single Sign-On]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [HRworks Single Sign-On 客戶支援小組](mailto:nadja.sommerfeld@hrworks.de)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 HRworks Single Sign-On]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 HRworks Single Sign-On 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [HRworks Single Sign-On]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-hrworks-single-sign-on-sso"></a>設定 HRworks Single Sign-On SSO

1. 若要自動執行 HRworks Single Sign-On 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 HRworks Single Sign-On]  便會將您導向至 HRworks Single Sign-On 應用程式。 請從該處提供用以登入 HRworks Single Sign-On 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 4。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 HRworks Single Sign-On，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 HRworks Single Sign-On 公司網站，然後執行下列步驟：

1. 從功能表列左側按一下 [系統管理員]   > [基本資訊]   > [安全性]   > [單一登入]  ，然後執行下列步驟：

    ![設定單一登入](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. 核取 [使用單一登入]  方塊。

    b. 選取 [XML 中繼資料]  作為 [中繼資料輸入法]  。

    c. 選取 [個別 NameID 識別碼]  作為 [NameID 的值]  。

    d. 在「記事本」中開啟從 Azure 入口網站下載的中繼資料 XML，複製其內容，然後貼到 [中繼資料]  文字方塊中。

    e. 按一下 [檔案]  。

### <a name="create-hrworks-single-sign-on-test-user"></a>建立 HRworks Single Sign-On 測試使用者

若要讓 Azure AD 使用者能夠登入 HRworks Single Sign-On，必須將這些使用者佈建到 HRworks Single Sign-On。 在 HRworks Single Sign-On 中，需手動進行佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入 HRworks Single Sign-On。

1. 從功能表列左側按一下 [系統管理員]   > [人員]   > [人員]   > [新增人員]  。

     ![顯示 HR works 頁面的螢幕擷取畫面，其中已選取 [人員] 和 [新增人員]。](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. 在快顯視窗中，按 [下一步]  。

    ![顯示國家/地區清單供您為人員選擇的螢幕擷取畫面。](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. 在 [建立法律條款的新人員與國家/地區]  快顯上，填入個別的詳細資料 (例如，[名字]  、[姓氏]  )，然後按一下 [建立]  。

    ![顯示文字方塊的螢幕擷取畫面，您可以在其中輸入人員的名字和姓氏。](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [HRworks Single Sign-On] 圖格時，應該會自動登入您設定 SSO 的 HRworks Single Sign-On。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 HRworks Single Sign-On](https://aad.portal.azure.com/)