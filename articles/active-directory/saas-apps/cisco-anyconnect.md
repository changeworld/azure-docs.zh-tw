---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Cisco AnyConnect 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cisco AnyConnect 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 4403d5470f96ead861196f7c9bcb0b9b6af088b0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Cisco AnyConnect 整合

在本教學課程中，您將了解如何整合 Cisco AnyConnect 與 Azure Active Directory (Azure AD)。 在整合 Cisco AnyConnect 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Cisco AnyConnect 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Cisco AnyConnect。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Cisco AnyConnect 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Cisco AnyConnect 支援 **IDP** 起始的 SSO

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>從資源庫新增 Cisco AnyConnect

若要設定將 Cisco AnyConnect 整合到 Azure AD 中，您需要將 Cisco AnyConnect 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Cisco AnyConnect**。
1. 從結果面板選取 [Cisco AnyConnect]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>設定和測試 Cisco AnyConnect 的 Azure AD SSO

以名為 **B. Simon** 的測試使用者，設定及測試與 Cisco AnyConnect 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Cisco AnyConnect 中相關使用者之間的連結關聯性。

若要設定及測試與 Cisco AnyConnect 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Cisco AnyConnect SSO](#configure-cisco-anyconnect-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Cisco AnyConnect 測試使用者](#create-cisco-anyconnect-test-user)** - 在 Cisco AnyConnect 中建立 B.Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Cisco AnyConnect] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入] 頁面上，輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`< YOUR CISCO ANYCONNECT VPN VALUE >`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`< YOUR CISCO ANYCONNECT VPN VALUE >`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Cisco AnyConnect 用戶端支援小組](https://www.cisco.com/c/en/us/support/index.html)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證檔案並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Cisco AnyConnect]  區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

> [!NOTE]
> 如果您想讓伺服器上的多個 TGT 上架，則必須從資源庫新增多個 Cisco AnyConnect 應用程式執行個體。 此外，您也可以選擇在 Azure AD 中，針對所有這些應用程式執行個體上傳您自己的憑證。 如此一來，您的應用程式就能使用相同的憑證，但可以為每個應用程式設定不同的識別碼和回覆 URL。

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

在本節中，您會將 Cisco AnyConnect 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Cisco AnyConnect]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-cisco-anyconnect-sso"></a>設定 Cisco AnyConnect SSO

1. 您即將先在 CLI 上執行此動作，您可能會回頭進行且以後再進行 ASDM 逐步解說。

1. 連線到您的 VPN 設備，您將會使用執行 9.8 程式碼訓練的 ASA，而您的 VPN 用戶端會是 4.6+。

1. 首先，您會建立 Trustpoint 並匯入我們的 SAML 憑證。

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. 下列命令將提供您的 SAML IdP。

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. 您現在可以將 SAML 驗證套用至 VPN 通道設定。

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > SAML IdP 設定有一項功能 - 如果您對 IdP 設定進行變更，就必須從您的通道群組中移除 SAML 識別提供者設定，然後予以重新套用，讓變更生效。

### <a name="create-cisco-anyconnect-test-user"></a>建立 Cisco AnyConnect 測試使用者

在本節中，您會在 Cisco AnyConnect 中建立名為 Britta Simon 的使用者。 請與 [Cisco AnyConnect 支援小組](https://www.cisco.com/c/en/us/support/index.html)合作，在 Cisco AnyConnect 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 Cisco AnyConnect
* 您可以使用 Microsoft 存取面板。 當您在存取面板中按一下 Cisco AnyConnect 圖格時，應該會自動登入您已設定 SSO 的 Cisco AnyConnect。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 Cisco AnyConnect 後，您可以強制執行工作階段控制項，以即時防止組織的敏感性資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
