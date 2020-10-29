---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 PurelyHR 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 PurelyHR 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 344e0c142b905558bffbf339c8e7a29c92113c23
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 PurelyHR 整合

在本教學課程中，您會了解如何整合 PurelyHR 與 Azure Active Directory (Azure AD)。 在整合 PurelyHR 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 PurelyHR 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 PurelyHR。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 PurelyHR 單一登入 (SSO) 功能的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* PurelyHR 支援由 **SP 和 IDP** 起始的 SSO
* PurelyHR 支援 **Just In Time** 使用者佈建

## <a name="adding-purelyhr-from-the-gallery"></a>從資源庫新增 PurelyHR

若要設定將 PurelyHR 整合到 Azure AD 中，您需要從資源庫將 PurelyHR 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **PurelyHR** 。
1. 從結果面板選取 [PurelyHR]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>設定及測試 PurelyHR 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 PurelyHR 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 PurelyHR 中相關使用者之間的連結關聯性。

若要設定及測試與 PurelyHR 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 PurelyHR SSO](#configure-purelyhr-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 PurelyHR 測試使用者](#create-purelyhr-test-user)** - 使 PurelyHR 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [PurelyHR]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<companyID>.purelyhr.com/sso-consume`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的回覆 URL 與登入 URL 更新這些值。 請連絡 [PurelyHR 用戶端支援小組](https://support.purelyhr.com/)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 PurelyHR]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 PurelyHR 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [PurelyHR]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-purelyhr-sso"></a>設定 PurelyHR SSO

1. 若要自動執行 PurelyHR 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [設定 PurelyHR]  便會將您導向到 PurelyHR 應用程式。 請從該處提供用以登入 PurelyHR 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 5。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 PurelyHR，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 PurelyHR 公司網站，然後執行下列步驟：

1. 從工具列選項開啟 **儀表板** ，然後按一下 [SSO 設定]  。

1. 如下所述貼上方塊中的值-

    ![設定單一登入](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. 在記事本中開啟您從 Azure 入口網站下載的 **Certificate(Bas64)** ，並複製憑證值。 將複製的值貼至 [X.509 憑證]  方塊。

    b. 在 [Idp 簽發者 URL]  方塊中，貼上從 Azure 入口網站複製的 **Azure AD 識別碼** 。

    c. 在 [IDP 端點 URL]  方塊中，貼上從 Azure 入口網站複製的 [登入 URL]  。 

    d. 核取 [自動建立使用者]  核取方塊，以在 PurelyHR 中啟用自動使用者佈建。

    e. 按一下 [儲存變更]  來儲存這些設定。

### <a name="create-purelyhr-test-user"></a>建立 PurelyHR 測試使用者

您通常不需要進行此步驟，因為應用程式支援即時使用者佈建。 如果自動使用者佈建未啟用，那麼您可以如下所述手動建立使用者。

以系統管理員身分登入您的 Velpic SAML 公司網站，然後執行下列步驟：

1. 按一下 [管理] 索引標籤並移至 [使用者] 區段，然後按一下 [新增] 按鈕來新增使用者。

    ![新增使用者](./media/velpicsaml-tutorial/velpic_7.png)

2. 在 [建立新的使用者]  對話方塊中，執行下列步驟。

    ![user](./media/velpicsaml-tutorial/velpic_8.png)

    a. 在 [名字]  文字方塊中，輸入 B 這個名字。

    b. 在 [姓氏]  文字方塊中，輸入 Simon 這個姓氏。

    c. 在 [使用者名稱]  文字方塊中，輸入 B.Simon 的使用者名稱。

    d. 在 [電子郵件]  文字方塊中，輸入 B.Simon@contoso.com 帳戶的電子郵件地址。

    e. 其餘資訊是選擇性的，您可以視需要來填入。

    f. 按一下 [儲存]  。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [PurelyHR] 圖格時，應該會自動登入您設定 SSO 的 PurelyHR。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 PurelyHR](https://aad.portal.azure.com/)