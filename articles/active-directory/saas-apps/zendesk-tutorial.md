---
title: 教學課程：Azure Active Directory 與 Zendesk 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zendesk 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70e7067a78b439d3dcaf0b83460296cad7b4485
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585809"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Zendesk 整合

在本教學課程中，您會了解如何整合 Zendesk 與 Azure Active Directory (Azure AD)。 在整合 Zendesk 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Zendesk 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Zendesk。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Zendesk 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Zendesk 支援 **SP** 起始的 SSO
* Zendesk 支援[**自動**使用者佈建](zendesk-provisioning-tutorial.md)
* 設定 Zendesk 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-zendesk-from-the-gallery"></a>從資源庫新增 Zendesk

若要設定將 Zendesk 整合到 Azure AD 中，您需要從資源庫將 Zendesk 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Zendesk**。
1. 從結果面板選取 [Zendesk]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>設定及測試 Zendesk 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Zendesk 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Zendesk 中相關使用者之間的連結關聯性。

若要設定及測試與 Zendesk 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Zendesk SSO](#configure-zendesk-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Zendesk 測試使用者](#create-zendesk-test-user)** - 讓 Zendesk 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Zendesk]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.zendesk.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.zendesk.com`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」、「識別碼」和「回覆 URL」來更新這些值。 請連絡 [Zendesk 用戶端支援小組](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. Zendesk 應用程式需要特定格式的 SAML 判斷提示。 沒有任何必要 SAML 屬性，但您可以視需要從應用程式整合頁面的 [使用者屬性]  區段管理。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > 您可以使用擴充屬性來新增預設不在 Azure AD 中的屬性。 按一下[可以在 SAML 中設定的使用者屬性](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)，以取得 **Zendesk**接受的完整 SAML 屬性清單。

1. 在 [SAML 簽署憑證]  區段中，按一下 [編輯]  按鈕以開啟 [SAML 簽署憑證]  對話方塊。

    ![編輯 SAML 簽署憑證](common/edit-certificate.png)

1. 在 [SAML 簽署憑證]  區段中，複製 [指紋值]  並儲存在您的電腦上。

    ![複製指紋值](common/copy-thumbprint.png)

1. 在 [設定 Zendesk]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 Zendesk 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Zendesk]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-zendesk-sso"></a>設定 Zendesk SSO

1. 如果您想要手動設定 Zendesk，請開啟新的網頁瀏覽器視窗，並以管理員身分登入 Zendesk 公司網站，然後執行下列步驟：

2. 按一下 Admin  。

3. 在左側導覽窗格中按一下 [設定]  ，然後按一下 [安全性]  。

4. 在 [安全性]  頁面上執行下列步驟：

    ![安全性](./media/zendesk-tutorial/ic773089.png "安全性")

    ![單一登入](./media/zendesk-tutorial/ic773090.png "單一登入")

    a. 按一下 [系統管理員和代理程式]  索引標籤。

    b. 選取 [單一登入 (SSO) 和 SAML]  ，然後選取 [SAML]  。

    c. 在 [SAML SSO URL]  文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    d. 在 [遠端登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    e. 在 [憑證指紋]  文字方塊中，貼上您從 Azure 入口網站複製的憑證 [指紋]  值。

    f. 按一下 [檔案]  。

### <a name="create-zendesk-test-user"></a>建立 Zendesk 測試使用者

本節的目標是要在 Zendesk 中建立名為 Britta Simon 的使用者。 Zendesk 支援自動使用者佈建，該功能預設為啟用。 您可以在[這裡](Zendesk-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

**如果您需要手動建立使用者，請執行下列步驟：**

> [!NOTE]
> [使用者]  帳戶會在登入時自動佈建。 登入之前，必須在 **Zendesk** 中手動佈建 [代理程式]  和 [系統管理員]  帳戶。

1. 登入您的 **Zendesk** 租用戶。

2. 選取 [客戶清單]  索引標籤。

3. 選取 [使用者]  索引標籤，然後按一下 [新增]  。

    ![新增使用者](./media/zendesk-tutorial/ic773632.png "新增使用者")
4. 輸入您要佈建之現有 Azure AD 帳戶的**名稱**和**電子郵件**，然後按一下 [儲存]  。

    ![新增使用者](./media/zendesk-tutorial/ic773633.png "新增使用者")

> [!NOTE]
> 您可以使用任何其他的 Zendesk 使用者帳戶建立工具或 Zendesk 提供的 API，來佈建 Azure AD 使用者帳戶。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Zendesk 圖格時，應該會自動登入您已設定 SSO 的 Zendesk。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Zendesk](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Zendesk](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [設定使用者佈建](zendesk-provisioning-tutorial.md)