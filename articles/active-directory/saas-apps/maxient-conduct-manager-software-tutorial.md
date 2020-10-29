---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Maxient Conduct Manager 軟體整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Maxient Conduct Manager 軟體之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 4b57b3fbb338774eb8d66fc4d3c0c817e19aff4c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458195"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Maxient Conduct Manager 軟體整合

在此教學課程中，您將了解如何整合 Maxient Conduct Manager 軟體與 Azure Active Directory (Azure AD)。 在整合 Maxient Conduct Manager 軟體與 Azure AD 時，您可以：

* 利用 Azure AD 來驗證 Maxient Conduct Manager 軟體的使用者
* 讓使用者使用其 Azure AD 帳戶自動登入 Maxient Conduct Manager 軟體。


若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Maxient Conduct Manager 軟體單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會設定要與 Maxient Conduct Manager 軟體搭配使用的 Azure AD。


* Maxient Conduct Manager 軟體支援由 **SP 和 IDP** 起始的 SSO

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>從資源庫新增 Maxient Conduct Manager 軟體

若要設定 Maxient Conduct Manager 軟體與 Azure AD 整合，您需要從資源庫將 Maxient Conduct Manager 軟體新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Maxient Conduct Manager 軟體** 。
1. 從結果面板選取 [Maxient Conduct Manager 軟體]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>設定和測試適用於 Maxient Conduct Manager 軟體的 Azure AD 單一登入

設定和測試適用於 Maxient Conduct Manager 軟體的 Azure AD SSO。 若要讓 SSO 運作，您必須建立 Azure AD 與 Maxient Conduct Manager 軟體之間的連線。

若要使用 Maxient Conduct Manager 軟體設定並測試 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓使用者進行驗證，以搭配使用 Maxient Conduct Manager 軟體
    1. **[指派所有使用者使用 Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** - 讓您機構中的每個人都能夠進行驗證。
1. **[使用 Maxient測試 Azure AD 安裝程式](#test-with-maxient)** - 驗證組態是否正常運作，以及是否已發行正確的屬性

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Maxient Conduct Manager 軟體] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，已預先以 **IDP** 起始的模式設定好應用程式，並已經為 Azure 預先填入必要的 URL。 使用者必須按一下 [儲存]  按鈕，才能儲存設定。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 請與您的 Maxient 實作/支援代表合作，以取得此值。

1. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將資料儲存在您的電腦上。  您需要向提供 Maxient 的實作/支援代表提供此 URL。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>指派所有使用者能夠驗證 Maxient Conduct Manager 軟體

在本節中，您會授與所有帳戶的存取權，以使用適用於 Maxient Conduct Manager 軟體的 Azure 系統進行驗證。  請務必注意，此步驟是 **必要的** ，如此 Maxient 才能正常運作。  Maxient 會利用您的 Azure AD 系統 *驗證* 使用者。 使用者的「授權」會在 Maxient 系統內針對其嘗試執行的特定功能執行。 Maxient 不會使用您目錄中的屬性來進行這些決策。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Maxient Conduct Manager 軟體]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使者和群組] 對話方塊中，選取所有使用者 (或適當的群組)，然後 **指派** 他們以便能夠使用 Maxient 進行驗證。

## <a name="test-with-maxient"></a>使用 Maxient 進行測試 

如果 Maxient 實作/支援代表尚未開啟支援票證，請將電子郵件傳送至 [support@maxient.com](mailto:support@maxient.com)，主旨為「校園型驗證/Azure 設定 - \<\<School Name\>\>」。 在電子郵件的本文中，提供 **應用程式同盟中繼資料 URL** 。 Maxient 人員會以測試連結回應，以驗證是否已發行適當的屬性。  
    
## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)

- [嘗試搭配 Azure AD 使用 Maxient Conduct Manager 軟體](https://aad.portal.azure.com/)