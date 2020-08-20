---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 monday.com 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 monday.com 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 2128559d8074281c1019d99e1316da34c7ec6797
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554595"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 monday.com 整合

在本教學課程中，您將了解如何整合 monday.com 與 Azure Active Directory (Azure AD)。 在整合 monday.com 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 monday.com 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 monday.com。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 monday.com 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* monday.com 支援由 **SP 和 IDP** 起始的 SSO
* monday.com 支援 **Just In Time** 使用者佈建

## <a name="adding-mondaycom-from-the-gallery"></a>從資源庫新增 monday.com

若要設定將 monday.com 整合到 Azure AD 中，您需要從資源庫將 monday.com 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **monday.com**。
1. 從結果面板選取 [monday.com]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-mondaycom"></a>設定及測試 monday.com 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 monday.com 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 monday.com 中相關使用者之間的連結關聯性。

若要設定及測試與 monday.com 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 monday.com SSO](#configure-mondaycom-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 monday.com 測試使用者](#create-mondaycom-test-user)** - 使monday.com 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [monday.com]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您有**服務提供者中繼資料檔案**，而想要以 **IDP** 起始模式進行設定，請執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]  。

    ![上傳中繼資料檔案](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    ![選擇中繼資料檔案](common/browse-upload-metadata.png)

    c. 成功上傳中繼資料檔案後，就會在 [基本 SAML 組態] 區段中自動填入 [識別碼]  和 [回覆 URL]  值。

    ![image](common/idp-intiated.png)

    > [!Note]
    > 如果 [識別碼]  和 [回覆 URL]  值未自動填入，則請手動填入這些值。 [識別碼]  和 [回覆 URL]  相同，其值模式如下：`https://<your-domain>.monday.com/saml/saml_callback`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![image](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 請連絡 [monday.com 用戶端支援小組](https://monday.com/contact-us/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. monday.com 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增到您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，monday.com 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性 |
    |--|--|
    | 電子郵件 | user.mail |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 monday.com]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 monday.com 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [monday.com]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-mondaycom-sso"></a>設定 monday.com SSO

1. 若要自動執行 monday.com 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

1. 將擴充功能新增至瀏覽器之後，按一下 [安裝 monday.com]  便會將您導向至 monday.com 應用程式。 請從該處提供用以登入 monday.com 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 6。

    ![設定組態](common/setup-sso.png)

1. 如果您想要手動設定 monday.com，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 monday.com，然後執行下列步驟：

1. 移至頁面右上角的 [設定檔]  ，然後按一下 [管理員]  。

    ![monday.com 組態](./media/mondaycom-tutorial/configuration01.png)

1. 選取 [安全性]  並且確定按一下 SAML 旁邊的 [開啟]  。

    ![monday.com 組態](./media/mondaycom-tutorial/configuration02.png)

1. 從您的 IDP 填寫以下詳細資料。

    ![monday.com 組態](./media/mondaycom-tutorial/configuration03.png)

    > [!NOTE]
    > 如需詳細資料，請參閱[這篇](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642)文章

### <a name="create-mondaycom-test-user"></a>建立 monday.com 測試使用者

本節會在 monday.com 中建立名為 B.Simon 的使用者。 monday.com 支援預設會啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 如果 monday.com 中還沒有使用者存在，在您嘗試存取 monday.com 時就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [monday.com] 圖格時，應該會自動登入您設定 SSO 的 monday.com。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 monday.com](https://aad.portal.azure.com/)
