---
title: 教學課程：Azure Active Directory 與 BambooHR 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 BambooHR 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180331"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>教學課程：Azure Active Directory 與 BambooHR 整合

在本教學課程中，您將了解如何整合 BambooHR 與 Azure Active Directory (Azure AD)。 在整合 BambooHR 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 BambooHR 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 BambooHR。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 BambooHR 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* BambooHR 支援 **SP** 起始的 SSO

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。


## <a name="adding-bamboohr-from-the-gallery"></a>從資源庫新增 BambooHR

若要設定將 BambooHR 整合到 Azure AD 中，您需要從資源庫將 BambooHR 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **BambooHR**。
1. 從結果面板中選取 [BambooHR]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>設定和測試 BambooHR 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 BambooHR 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 BambooHR 中相關使用者之間的連結關聯性。

若要設定及測試與 BambooHR 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 BambooHR SSO](#configure-bamboohr-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 BambooHR 測試使用者](#create-bamboohr-test-user)** - 使 BambooHR 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [BambooHR] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<company>.bamboohr.com`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：

    | 回覆 URL |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的登入 URL 和回覆 URL 來更新這些值。 請連絡 [BambooHR 用戶端支援小組](https://www.bamboohr.com/contact.php)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 BambooHR] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 BambooHR 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [BambooHR]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-bamboohr-sso"></a>設定 BambooHR SSO

1. 在新視窗中，以系統管理員身分登入您的 BambooHR 公司網站。

2. 在首頁上，執行下列操作：
   
    ![[BambooHR 單一登入] 頁面](./media/bamboo-hr-tutorial/ic796691.png "單一登入")   

    a. 選取 [應用程式]  。
   
    b. 在 [Apps] \(應用程式\) 窗格中，選取 [Single Sign-On] \(單一登入\)。
   
    c. 選取 [SAML Single Sign-On] \(SAML 單一登入\)。

3. 在 [SAML Single Sign-On] \(SAML 單一登入\) 窗格中，執行下列操作：
   
    ![[SAML 單一登入] 窗格](./media/bamboo-hr-tutorial/IC796692.png "SAML 單一登入")
   
    a. 在 [SSO Login Url] \(SSO 登入 URL\) 方塊中，貼上您在步驟 6 中從 Azure 入口網站複製的 **登入 URL**。
      
    b. 在「記事本」中開啟從 Azure 入口網站下載的 Base-64 編碼憑證，複製其內容，然後貼到 [X.509 Certificate] \(X.509 憑證\) 方塊中。
   
    c. 選取 [儲存]。

### <a name="create-bamboohr-test-user"></a>建立 BambooHR 測試使用者

若要讓 Azure AD 使用者能夠登入 BambooHR，請執行下列操作，在 BambooHR 中手動設定這些使用者：

1. 以系統管理員身分登入您的 **BambooHR** 網站。

2. 在頂端的工具列中，選取 [Settings] \(設定\)。
   
    ![[設定] 按鈕](./media/bamboo-hr-tutorial/IC796694.png "設定")

3. 選取 [概觀]。

4. 在左側窗格中，選取 [Security] \(安全性\) > [Users] \(使用者\)。

5. 輸入您想要設定之有效 Azure AD 帳戶的使用者名稱、密碼及電子郵件地址。

6. 選取 [儲存]。
        
>[!NOTE]
>若要設定 Azure AD 使用者帳戶，您也可以使用 BambooHR 使用者帳戶建立工具或 API。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

1. 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 BambooHR 登入 URL。 

2. 直接移至 BambooHR 登入 URL，然後從該處起始登入流程。

3. 您可以使用 Microsoft 存取面板。 當您按一下存取面板中的 BambooHR 圖格時，將會重新導向至 BambooHR 登入 URL。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 BambooHR 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。