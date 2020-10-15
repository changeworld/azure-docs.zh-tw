---
title: 教學課程：Azure Active Directory 與 Zscaler Private Access (ZPA) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Private Access (ZPA) 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.openlocfilehash: 8c7347aabb3aa6f122ea82a46ad8a09f53e271bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545674"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>教學課程：整合 Zscaler Private Access (ZPA) 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Zscaler Private Access (ZPA) 與 Azure Active Directory (Azure AD)。 在整合 Zscaler Private Access (ZPA) 與 Azure AD 後，您可以︰

* 在 Azure AD 中控制可存取 Zscaler Private Access (ZPA) 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Zscaler Private Access (ZPA)。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Zscaler Private Access (ZPA) 單一登入 (SSO) 的訂用帳戶。

> [!NOTE]
> 也可以在 Azure AD 美國政府雲端環境中使用此整合。 您可以在 Azure AD 美國政府雲端應用程式庫中找到此應用程式，並以與公用雲端相同的方式進行設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Zscaler Private Access (ZPA) 支援 **SP** 起始的 SSO。

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>從資源庫加入 Zscaler Private Access (ZPA)

若要設定 Zscaler Private Access (ZPA) 與 Azure AD 整合，您需要從資源庫將 Zscaler Private Access (ZPA) 加入受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **Zscaler Private Access (ZPA)** 。
1. 從結果面板中選取 [Zscaler Private Access (ZPA)]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 Zscaler Private Access (ZPA) 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Zscaler Private Access (ZPA) 中相關使用者之間的連結關聯性。

若要設定及測試與 Zscaler Private Access (ZPA) 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
2. **[設定 Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** ，在應用程式端設定 SSO 設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Zscaler Private Access (ZPA) 測試使用者](#create-zscaler-private-access-zpa-test-user)** ，讓 Zscaler Private Access (ZPA) 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Zscaler Private Access (ZPA)] 應用程式整合頁面上，尋找 [管理] 區段，然後選取 [單一登入]。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定] 頁面上，輸入下列欄位的值：

    1. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL：`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. 在 [識別碼 (實體識別碼)] 文字方塊中，輸入 URL：`https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 使用實際的登入 URL 來更新此值。 請連絡 [Zscaler Private Access (ZPA) 用戶端支援小組](https://help.zscaler.com/zpa-submit-ticket)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，尋找 [同盟中繼資料 XML]，然後選取 [下載] 來下載憑證，並將其儲存在電腦上。

   ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Zscaler Private Access (ZPA)] 區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>設定 Zscaler Private Access (ZPA)

1. 若要自動執行 Zscaler Private Access (ZPA) 內的設定，您必須按一下 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器擴充功能」。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Zscaler Private Access (ZPA)] 便會將您導向到 Zscaler Private Access (ZPA) 應用程式。 請從該處提供用以登入 Zscaler Private Access (ZPA) 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 Zscaler Private Access (ZPA)，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Zscaler Private Access (ZPA) 公司網站，然後執行下列步驟：

4. 從功能表左側按一下 [管理]  並瀏覽至 [驗證]  區段，然後按一下 [IdP 設定]  。

    ![Zscaler Private Access Administrator 管理](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. 按一下右上角的 [Add IdP Configuration] \(新增 IdP 設定\)  。 

    ![Zscaler Private Access Administrator idp](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. 在 [Add IdP Configuration] \(新增 IdP 設定\)  頁面上，執行下列步驟：
 
    ![Zscaler Private Access Administrator 選取](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. 按一下 [IdP Metadata File Upload] \(IdP 中繼資料檔案上傳\)  欄位中的 [Select File] \(選取檔案)  ，以上傳從 Azure AD 下載的中繼資料檔案。

    b. 它會從 Azure AD 讀取 **IdP 中繼資料**，並填入所有欄位資訊，如下所示。

    ![Zscaler Private Access Administrator 設定](./media/zscalerprivateaccess-tutorial/config.png)

    c. 從 [Domains] \(網域\)  欄位中選取您的網域。
    
    d. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Zscaler Private Access (ZPA) 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Zscaler Private Access (ZPA)]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊中，從使用者清單選取 **Britta Simon**，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-zscaler-private-access-zpa-test-user"></a>建立 Zscaler Private Access (ZPA) 測試使用者

在本節中，您要在 Zscaler Private Access (ZPA) 中建立名為 Britta Simon 的使用者。 請和 [Zscaler 私用存取 (ZPA) 支援小組 (Zscaler Private Access (ZPA) support team)](https://help.zscaler.com/zpa-submit-ticket) 一起，在 Zscaler Private Access (ZPA) 平台新增使用者。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Zscaler Private Access (ZPA)] 圖格時，應該會自動登入您已設定 SSO 的 Zscaler Private Access (ZPA)。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)