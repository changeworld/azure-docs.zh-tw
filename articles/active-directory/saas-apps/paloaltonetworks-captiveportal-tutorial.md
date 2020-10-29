---
title: 教學課程：Azure Active Directory 與 Palo Alto Networks Captive Portal 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Palo Alto Networks Captive Portal 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 66e363b43bf9028e3075efa0f5a1f54e6bda190d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92512665"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>教學課程：Azure Active Directory 與 Palo Alto Networks Captive Portal 整合

在本教學課程中，您將了解如何整合 Palo Alto Networks Captive Portal 與 Azure Active Directory (Azure AD)。
整合 Palo Alto Networks Captive Portal 與 Azure AD 可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Palo Alto Networks Captive Portal 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Palo Alto Networks Captive Portal (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要整合 Azure AD 與 Palo Alto Networks Captive Portal，您需要下列項目：

* Azure Active Directory 訂用帳戶。 如果您沒有 Azure AD，您可以取得[一個月的試用](https://azure.microsoft.com/pricing/free-trial/)。
* 已啟用 Palo Alto Networks Captive Portal 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Palo Alto Networks Captive Portal 支援 **IDP** 起始的 SSO
* Palo Alto Networks Captive Portal 支援 **Just In Time** 使用者佈建

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>從資源庫新增 Palo Alto Networks Captive Portal

若要設定將 Palo Alto Networks Captive Portal 整合到 Azure AD 中，您需要從資源庫將 Palo Alto Networks Captive Portal 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Palo Alto Networks Captive Portal** 。
1. 從結果面板中選取 [Palo Alto Networks Captive Portal]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

在本節中，您會以名為 **B.Simon** 的測試使用者為基礎，使用 Palo Alto Networks Captive Portal 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Palo Alto Networks Captive Portal 中相關使用者之間的連結關聯性。

若要使用 Palo Alto Networks Captive Portal 設定並測試 Azure AD 單一登入，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓使用者能夠使用這項功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 以使用者 B.Simon 的身分測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 將 B.Simon 設定為使用 Azure AD 單一登入。
2. **[設定 Palo Alto Networks Captive Portal SSO](#configure-palo-alto-networks-captive-portal-sso)** - 在應用程式中設定單一登入設定。
    * **[建立 Palo Alto Networks Captive Portal SSO 測試使用者](#create-a-palo-alto-networks-captive-portal-test-user)** - 使 Palo Alto Networks Captive Portal SSO 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **Palo Alto Networks Captive Portal** 應用程式整合頁面上，找到 [管理] 區段並選取 [單一登入]。
1. 在 [ **選取單一登入方法** ] 頁面上，選取 [ **SAML** ]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 窗格中，執行下列步驟：

   1. 針對 [識別碼]  ，輸入具有 `https://<customer_firewall_host_name>/SAML20/SP` 模式的 URL。

   2. 針對 [回覆 URL]  ，輸入具有 `https://<customer_firewall_host_name>/SAML20/SP/ACS` 模式的 URL。

      > [!NOTE]
      > 請使用實際的識別碼和回覆 URL 更新此步驟中的預留位置值。 若要取得實際值，請連絡 [Palo Alto Networks Captive Portal 用戶端支援小組](https://support.paloaltonetworks.com/support)。

5. 在 [SAML 簽署憑證]  區段中的 [同盟中繼資料 XML]  旁邊，選取 [下載]  。 將下載的檔案儲存在電腦上。

    ![同盟中繼資料 XML 下載連結](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Palo Alto Networks Captive Portal SSO 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Palo Alto Networks Captive Portal SSO]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>設定 Palo Alto Networks Captive Portal SSO

接著，在 Palo Alto Networks Captive Portal 中設定單一登入：

1. 在不同的瀏覽器視窗中，以系統管理員身分登入 Palo Alto Networks 網站。

2. 選取 [裝置]  索引標籤。

    ![Palo Alto Networks 網站裝置索引標籤](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 在功能表中選取 [SAML 識別提供者]  ，然後選取 [匯入]  。

    ![匯入按鈕](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 在 [SAML 識別提供者伺服器設定檔匯入]  對話方塊中，完成下列步驟：

    ![設定 Palo Alto Networks 單一登入](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. 針對 [設定檔名稱]  輸入名稱，例如 **AzureAD-CaptivePortal** 。
    
    2. 在 [識別提供者中繼資料]  旁邊，選取 [瀏覽]  。 選取您在 Azure 入口網站中下載的 metadata.xml 檔案。
    
    3. 選取 [確定]  。

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>建立 Palo Alto Networks Captive Portal 測試使用者

接著，在 Palo Alto Networks Captive Portal 中建立名為 *Britta Simon* 的使用者。 Palo Alto Networks Captive Portal 支援依預設啟用的 Just-in-Time 佈建。 在本節中，您不需要完成任何工作。 如果 Palo Alto Networks Captive Portal 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!NOTE]
> 如果您想要手動建立使用者，請連絡 [Palo Alto Networks Captive Portal 用戶端支援小組](https://support.paloaltonetworks.com/support)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 Palo Alto Networks Captive Portal

您可以使用 Microsoft 存取面板。 當您在存取面板中按一下 Palo Alto Networks Captive Portal 圖格時，應該會自動登入您設定 SSO 的 Palo Alto Networks Captive Portal。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>後續步驟

設定 Palo Alto Networks Captive Portal 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。