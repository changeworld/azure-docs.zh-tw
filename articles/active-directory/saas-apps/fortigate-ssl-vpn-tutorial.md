---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 FortiGate SSL VPN 整合 | Microsoft Docs
description: 了解您整合 FortiGate SSL VPN 與 Azure Active Directory (Azure AD) 所需執行的步驟。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: b9a22025f124e7639aa1b9a157dbbd020e2ff966
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020259"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 FortiGate SSL VPN 整合

在本教學課程中，您會了解如何將 FortiGate SSL VPN 與 Azure Active Directory (Azure AD) 進行整合。 在整合 FortiGate SSL VPN 與 Azure AD 時，您可以︰

* 使用 Azure AD 來控制可以存取 FortiGate SSL VPN 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 FortiGate SSL VPN。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用單一登入 (SSO) 的 FortiGate SSL VPN 訂用帳戶。

## <a name="tutorial-description"></a>教學課程說明

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

FortiGate SSL VPN 支援由 SP 起始的 SSO。


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>從資源庫新增 FortiGate SSL VPN

若要進行將 FortiGate SSL VPN 整合到 Azure AD 中的設定，您必須從資源庫將 FortiGate SSL VPN 新增至受控 SaaS 應用程式清單：

1. 使用公司或學校帳戶或使用個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左窗格中，選取 [Azure Active Directory]。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **FortiGate SSL VPN**。
1. 在結果面板中選取 [FortiGate SSL VPN]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>設定和測試 FortiGate SSL VPN 的 Azure AD SSO

您將使用名為 B.Simon 的測試使用者，設定及測試與 FortiGate SSL VPN 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 FortiGate SSL VPN 中對應使用者之間的連結關聯性。

若要設定及測試與 FortiGate SSL VPN 搭配運作的 Azure AD SSO，您將完成下列高階步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以測試 Azure AD 單一登入。
    1. **[將存取權授予測試使用者](#grant-access-to-the-test-user)** ，讓該使用者能夠使用 Azure AD 單一登入。
1. 在應用程式端 **[設定 FortiGate SSL VPN SSO](#configure-fortigate-ssl-vpn-sso)** 。
    1. **建立 FortiGate SSL VPN 測試使用者** 作為 Azure AD 中出現的使用者對應項。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟，在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 Azure 入口網站的 **FortiGate SSL VPN** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，選取 [基本 SAML 組態] 的鉛筆按鈕，以編輯設定：

   ![顯示用於編輯基本 SAML 設定之鉛筆按鈕的螢幕擷取畫面。](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入] 頁面上，輸入下列值：

    a. 在 [登入 URL] 方塊中，輸入 `https://<FQDN>/remote/login` 模式的 URL。

    b. 在 [識別碼] 方塊中，輸入 `https://<FQDN>/remote/saml/metadata` 模式的 URL。

    c. 在 [回覆 URL] 方塊中，輸入 `https://<FQDN>/remote/saml/login` 模式的 URL。

    d. 在 [登出 URL] 方塊中，輸入 `https://<FQDN>/remote/saml/logout` 模式的 URL。

    > [!NOTE]
    > 這些值只是模式。 您需要使用實際的「登入 URL」、「識別碼」、「回覆 URL」及「登出 URL」。 如需指引，請連絡 [Fortinet 支援](https://support.fortinet.com)。 您也可以參考 Fortinet 文件和 Azure 入口網站的 [基本 SAML 設定] 區段中顯示的範例模式。

1. FortiGate SSL VPN 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至設定中。 以下螢幕擷取畫面顯示預設屬性清單。

    ![顯示預設屬性的螢幕擷取畫面。](common/default-attributes.png)

1. 下表顯示 FortiGate SSL VPN 所需的兩個額外宣告。 這些宣告的名稱必須符合本教學課程的 **執行 FortiGate 命令列設定** 一節中使用的名稱。 

   | 名稱 |  來源屬性|
   | ------------ | --------- |
   | username | user.userprincipalname |
   | 群組 | user.groups |
   
   若要建立這些額外的宣告：

   a. 在 [使用者屬性與宣告] 旁，選取 [編輯]。

   b. 選取 [新增宣告]。

   c. 針對 [名稱]，輸入 **username**。

   d. 針對 [來源屬性]，選取 [user.userprincipalname]。

   e. 選取 [儲存]  。

   f. 選取 [新增群組宣告]。

   g. 選取 [所有群組]。

   h. 選取 [自訂群組宣告的名稱] 核取方塊。

   i. 針對 [名稱]，輸入 **group**。
   
   j. 選取 [儲存]。   

1. 在 [設定使用 SAML 的單一登入] 頁面的 [SAML 簽署憑證] 區段中，選取 [憑證 (Base64)] 旁的 [下載] 連結，以下載憑證並將其儲存在電腦上：

    ![顯示憑證下載連結的螢幕擷取畫面。](common/certificatebase64.png)

1. 在 [設定 FortiGate SSL VPN] 區段上，根據您的需求複製適當的一或多個 URL：

    ![顯示組態 URL 的螢幕擷取畫面。](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左側窗格中，選取 [Azure Active Directory]。 選取 [使用者 ]，然後選取 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者] 屬性中，完成下列步驟：
   1. 在 [名稱] 方塊中，輸入 **B.Simon**。  
   1. 在 [使用者名稱] 方塊中，輸入 \<username>@\<companydomain>.\<extension>。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]，然後記下 [密碼] 方塊中顯示的值。
   1. 選取 [建立]。

#### <a name="grant-access-to-the-test-user"></a>將存取權授予測試使用者

在本節中，您會將 FortiGate SSL VPN 的使用者存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [FortiGate SSL VPN]。
1. 在應用程式的概觀頁面上，請在 [管理] 區段中，選取 [使用者和群組]。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

#### <a name="create-a-security-group-for-the-test-user"></a>建立測試使用者的安全性群組

在本節中，您會在測試使用者的 Azure Active Directory 中建立安全性群組。 FortiGate 會使用此安全性群組透過 VPN 授與使用者網路存取權。

1. 在 Azure 入口網站的左側窗格中，選取 [Azure Active Directory]。 然後選取 [群組]。
1. 在畫面頂端選取 [新增群組]。
1. 在 [新增群組] 屬性中，完成這些步驟：
   1. 在 [群組類型] 清單中，選取 [安全性]。
   1. 在 [群組名稱] 方塊中，輸入 **FortiGateAccess**。
   1. 在 [群組描述] 方塊中，輸入 [用來授與 FortiGate VPN 存取的群組]。
   1. 針對 **系統可以將 Azure AD 角色指派到群組 (預覽)** 設定，選取 [否]。
   1. 在 [成員資格類型] 方塊中，選取 [已指派]。
   1. 在 **成員** 下，選取 [未選取任何成員]。
   1. 在 [使用者及群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
   1. 選取 [建立]。
1. 回到 Azure Active Directory 中的 [群組] 區段之後，尋找 [FortiGate Access] 群組，並記下 [物件識別碼]。稍後您將會用到此資訊。

### <a name="configure-fortigate-ssl-vpn-sso"></a>設定 FortiGate SSL VPN SSO

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>將 Base64 SAML 憑證上傳至 FortiGate 設備

在您的租用戶中完成 FortiGate 應用程式的 SAML 設定後，您已下載 Base64 編碼的 SAML 憑證。 您必須將此憑證上傳至 FortiGate 設備：

1. 登入 FortiGate 設備的管理入口網站。
1. 在左側窗格中，選取 [系統]。
1. 在 [系統] 下，選取 [憑證]。
1. 選取 [匯入] > [遠端憑證]。
1. 瀏覽至 Azure 租用戶中從 FortiGate 應用程式部署下載的憑證並加以選取，然後選取 [確定]。

上傳憑證後，記下其在 **系統** > **憑證** > **遠端憑證** 下的名稱。 根據預設，系統會將憑證命名為 REMOTE_Cert_N，其中 N 是整數值。

#### <a name="complete-fortigate-command-line-configuration"></a>完成 FortiGate 命令列組態

下列步驟會要求您設定 Azure 登出 URL。 此 URL 包含問號字元 (?)。 您必須採取特定的步驟，才能成功提交此字元。 您無法從 FortiGate CLI 主控台完成這些步驟。 相反地，請使用 PuTTY 之類的工具，建立 FortiGate 設備的 SSH 工作階段。 如果您的 FortiGate 設備是 Azure 虛擬機器，可以從 Azure 虛擬機器完成下列步驟。

若要完成這些步驟，您需要先前記錄的值：

- 實體識別碼
- 回覆 URL
- 登出 URL
- Azure 登入 URL
- Azure AD 識別碼
- Azure 登出 URL
- Base64 SAML 憑證名稱 (REMOTE_Cert_N)

1. 建立 FortiGate 設備的 SSH 工作階段，並使用 FortiGate 系統管理員帳戶登入。
1. 執行下列命令：

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Azure 登出 URL 包含 `?` 字元。 您必須輸入特殊的金鑰序列，才能正確提供此 FortiGate 序列主控台的 URL。 URL 通常是 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`。
   >
   > 若要在序列主控台中輸入 Azure 登出 URL，請輸入 `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`。
   > 
   > 然後選取 CTRL+V 並貼入 URL 的其餘部分，以完成該行命令：`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`。

#### <a name="configure-fortigate-for-group-matching"></a>設定 FortiGate 以進行群組比對

在本節中，您會將 FortiGate 設定為辨識包含測試使用者的安全性群組物件識別碼。 此組態可讓 FortiGate 根據此群組成員資格判斷是否要提供存取權。

若要完成這些步驟，您需要稍早在此教學課程中建立的 FortiGateAccess 安全性群組物件識別碼。

1. 建立 FortiGate 設備的 SSH 工作階段，並使用 FortiGate 系統管理員帳戶登入。
1. 執行下列命令：

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>建立 FortiGate VPN 入口網站和防火牆原則

在本節中，您會設定 FortiGate VPN 入口網站和防火牆原則，以授與您先前在此教學課程中所建立 FortiGateAccess 安全性群組的存取權。

請與 [FortiGate 支援小組](mailto:tac_amer@fortinet.com)合作，將 VPN 入口網站和防火牆原則新增至 FortiGate VPN 平台。 您必須先完成此步驟，才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至您可以在其中起始登入流程的 FortiGate VPN 登入 URL。 

* 直接移至 FortiGate VPN 登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 [FortiGate VPN] 圖格時，將會重新導向至 FortiGate VPN 登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 FortiGate VPN 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-aad)
