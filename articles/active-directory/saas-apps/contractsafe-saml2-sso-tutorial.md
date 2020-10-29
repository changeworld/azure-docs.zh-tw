---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 ContractSafe Saml2 SSO 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ContractSafe Saml2 SSO 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 2d236b9910e2eda8e574d020544d625a68aefb81
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455301"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>教學課程：整合 Azure Active Directory 單一登入 (SSO) 與 ContractSafe Saml2 SSO

在此教學課程中，您會了解如何整合 ContractSafe Saml2 SSO 與 Azure Active Directory (Azure AD)。 在整合 ContractSafe Saml2 SSO 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 ContractSafe Saml2 SSO 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 ContractSafe Saml2 SSO。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SSO 的 ContractSafe Saml2 SSO 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 ContractSafe Saml2 SSO 支援 **IDP** 起始的 SSO。

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>從資源庫新增 ContractSafe Saml2 SSO

若要設定將 ContractSafe Saml2 SSO 整合到 Azure AD 中，您需要從資源庫將 ContractSafe Saml2 SSO 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **ContractSafe Saml2 SSO** 。
1. 從結果面板選取 [ContractSafe Saml2 SSO]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>設定及測試適用於 ContractSafe Saml2 SSO 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 ContractSafe Saml2 SSO 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 ContractSafe Saml2 SSO 中相關使用者之間的連結關聯性。

若要設定及測試與 ContractSafe Saml2 SSO 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。
   * [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以 **B.Simon** 帳戶測試 Azure AD SSO。
   * [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 **B.Simon** 能夠使用 Azure AD SSO。

1. [設定 ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso)，以在應用程式端設定 SSO 設定。
   * [建立 ContractSafe Saml2 SSO 測試使用者](#create-a-contractsafe-saml2-sso-test-user)，使 ContractSafe Saml2 SSO 中對應的 **B.Simon** 連結到該使用者在 Azure AD 中的代表項目。
2. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [ContractSafe Saml2 SSO]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 組態]  的編輯 (畫筆) 圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入]  頁面上的對應欄位中輸入下列值：

    a. 在 [識別碼]  文件方塊中，以下列格式輸入 URL：`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. 在 [回覆 URL]  文字方塊中，以下列格式輸入 URL：`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 來更新這些值。 請連絡 [ContractSafe Saml2 SSO 用戶端支援小組](mailto:support@contractsafe.com)以取得這些值。 您也可以參考 Azure 入口網站中 [基本 SAML 組態]  區段所示的格式。

1. ContractSafe Saml2 SSO 會預期要有特定格式的 SAML 判斷提示，這會要求您在 SAML 權杖屬性設定中新增自訂的屬性對應。 以下螢幕擷取畫面顯示預設屬性清單。

    ![常見預設屬性](common/default-attributes.png)

1. 除了預設屬性外，ContractSafe Saml2 SSO 應用程式還需要在 SAML 回應中傳回更多屬性。 這些屬性會預先填入，但您可以根據您的需求進行檢閱。 下列清單顯示其他屬性。

    | 名稱 | 來源屬性|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | 電子郵件 | user.onpremisesuserprincipalname |

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 **同盟中繼資料 XML** 。 選取 [下載]  以下載憑證，然後將其儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 ContractSafe Saml2 SSO]  區段中，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 **B.Simon** 的測試使用者。

1. 在 Azure 入口網站的左側窗格中，選取 [Azure Active Directory]  。 選取 [使用者 ]  ，然後選取 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，以 `username@companydomain.extension` 格式輸入電子郵件地址。 例如 `B.Simon@contoso.com`。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 選取 [建立]  。

## <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 ContractSafe Saml2 SSO 的存取權授與 **B.Simon** ，讓其能夠使用 Azure SSO。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [ContractSafe Saml2 SSO]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

   ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者]  清單中，選取 [B.Simon]  。 然後，選取畫面底部的 [選取]  按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 然後選擇畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  按鈕。

## <a name="configure-contractsafe-saml2-sso"></a>設定 ContractSafe Saml2 SSO

若要在 **ContractSafe Saml2 SSO** 端設定 SSO，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和所複製的適當 URL 傳送給 [ContractSafe Saml2 SSO 支援小組](mailto:support@contractsafe.com)。 小組會負責在兩端正確地設定 SAML SSO 連線。

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>建立 ContractSafe Saml2 SSO 測試使用者

在 ContractSafe Saml2 SSO 中建立名為 B.Simon 的使用者。 請與[ContractSafe Saml2 SSO 支援小組合作](mailto:support@contractsafe.com)，在 ContractSafe Saml2 SSO 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用 SSO。

## <a name="test-sso"></a>測試 SSO

請使用「存取面板」來測試您的 Azure AD SSO 設定。 當您在存取面板中選取 ContractSafe Saml2 SSO 圖格時，應該會自動登入您已設定 SSO 的 ContractSafe Saml2 SSO。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 ContractSafe Saml2 SSO](https://aad.portal.azure.com/)