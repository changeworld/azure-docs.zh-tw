---
title: 教學課程：Azure Active Directory 與 Shmoop For Schools 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Shmoop For Schools 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.openlocfilehash: 7838e4f2ced5f47a0fb52b6e0f07d30edd770dca
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522068"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>教學課程：整合 Shmoop For Schools 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Shmoop For Schools 與 Azure Active Directory (Azure AD)。 當您整合 Shmoop For Schools 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 Shmoop For Schools 的人員。
* 讓使用者使用他們的 Azure AD 帳戶自動登入 Shmoop For Schools。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Shmoop For Schools 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Shmoop For Schools 支援 **SP** 起始的 SSO
* Shmoop For Schools 支援 **Just In Time** 使用者佈建

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>從資源庫新增 Shmoop For Schools

若要設定將 Shmoop For Schools 整合到 Azure AD 中，您需要從資源庫將 Shmoop For Schools 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Shmoop For Schools** 。
1. 從結果面板選取 [Shmoop For Schools]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-shmoop-for-schools"></a>設定和測試 Shmoop For Schools 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Shmoop For Schools 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Shmoop For Schools 中相關使用者之間的連結關聯性。

若要使用 Shmoop For Schools 來設定並測試 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Shmoop For Schools SSO](#configure-shmoop-for-schools-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Shmoop For Schools 測試使用者](#create-shmoop-for-schools-test-user)** - 讓 Shmoop For Schools 中對應的 B.Simon 連結到使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Shmoop For Schools]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://schools.shmoop.com/<uniqueid>`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Shmoop For Schools 用戶端支援小組](mailto:support@shmoop.com)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. Shmoop For Schools 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

    > [!NOTE]
    > Shmoop For Schools 支援兩種使用者角色： **老師** 和 **學生** 。 在 Azure AD 中設定這些角色，以便您可以將使用者指派為適當的角色。 若要了解如何在 Azure AD 中設定角色，請參閱[此文章](../develop/active-directory-enterprise-app-role-management.md)。

1. 除了以上屬性外，Shmoop For Schools 應用程式還需要在 SAML 回應中傳回更多屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 |  來源屬性|
    | --------- | --------------- |
    | 角色 (role)      | user.assignedroles |

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

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

在本節中，您會將 Shmoop For Schools 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Shmoop For Schools]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-shmoop-for-schools-sso"></a>設定 Shmoop For Schools SSO

若要在 **Shmoop For Schools** 端設定單一登入，您必須將 **應用程式同盟中繼資料 URL** 傳送給 [Shmoop For Schools 支援小組](mailto:support@shmoop.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-shmoop-for-schools-test-user"></a>建立 Shmoop For Schools 測試使用者

本節會在 Shmoop For Schools 中建立名為 B.Simon 的使用者。 Shmoop For Schools 支援依預設啟用的 Just-in-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Shmoop For Schools 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [Shmoop For Schools 支援小組](mailto:support@shmoop.com)。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Shmoop For Schools 圖格時，應該會自動登入您設定 SSO 的 Shmoop For Schools。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [搭配 Azure AD 使用 Shmoop For Schools](https://aad.portal.azure.com/)