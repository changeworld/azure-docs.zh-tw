---
title: 混合式 FIDO2 安全性金鑰部署的常見問題-Azure Active Directory
description: '深入瞭解使用 Azure Active Directory (preview 來無密碼混合式 FIDO2 安全性金鑰登入的常見問題) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16e232cedb13dc246bf7a568adfad401c1fe3eb8
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236617"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>Azure AD (preview 中混合式 FIDO2 安全性金鑰的部署常見問題 (常見問題) ) 

本文涵蓋 (常見問題) 適用于混合式 Azure AD 聯結裝置，以及無密碼登入內部內部部署資源的常見問題。 使用這項無密碼功能，您可以在 Windows 10 裝置上，使用 FIDO2 安全性金鑰來啟用混合式 Azure AD 聯結裝置的 Azure AD 驗證。 使用者可以在其裝置上使用新式認證（例如 FIDO2 金鑰）登入 Windows，並存取傳統 Active Directory Domain Services (AD DS) 的資源，並透過無縫單一登入 (內部部署資源的 SSO) 體驗。

支援混合式環境中的使用者下列案例：

* 使用 FIDO2 安全性金鑰登入已加入混合式 Azure AD 的裝置，並取得內部內部部署資源的 SSO 存取權。
* 使用 FIDO2 安全性金鑰登入 Azure AD 加入的裝置，並取得內部內部部署資源的 SSO 存取權。

若要開始使用 FIDO2 安全性金鑰和內部部署資源的混合式存取，請參閱下列文章：

* [無密碼 FIDO2 安全性金鑰](howto-authentication-passwordless-security-key.md)
* [無密碼 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [無密碼內部部署](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2 安全性金鑰是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="security-keys"></a>安全性金鑰

* [我的組織需要雙因素驗證才能存取資源。我可以如何支援這項需求？](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [哪裡可以找到符合規範的 FIDO2 安全性金鑰？](#where-can-i-find-compliant-fido2-security-keys)
* [如果我遺失安全性金鑰，該怎麼辦？](#what-if-i-lose-my-security-key)
* [如何保護 FIDO2 安全性金鑰上的資料？](#how-is-the-data-protected-on-the-fido2-security-key)
* [註冊 FIDO2 安全性金鑰的運作方式為何？](#how-does-the-registering-of-fido2-security-keys-work)
* [系統管理員是否有辦法直接為使用者布建金鑰？](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的組織需要多重要素驗證，才能存取資源。 我可以如何支援這項需求？

FIDO2 安全性金鑰有各式各樣的外型規格。 請與感興趣的裝置製造商討論，如何使用 PIN 或生物特徵辨識作為第二個因素來啟用裝置。 如需支援的提供者清單，請參閱 [FIDO2 安全性金鑰提供者](concept-authentication-passwordless.md#fido2-security-key-providers)。

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>哪裡可以找到符合規範的 FIDO2 安全性金鑰？

如需支援的提供者清單，請參閱 [FIDO2 安全性金鑰提供者](concept-authentication-passwordless.md#fido2-security-key-providers)。

### <a name="what-if-i-lose-my-security-key"></a>如果我遺失安全性金鑰，該怎麼辦？

您可以流覽至 [ **安全性資訊** ] 頁面，並移除 FIDO2 安全性金鑰，以移除 Azure 入口網站中的索引鍵。

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>如何保護 FIDO2 安全性金鑰上的資料？

FIDO2 安全性金鑰具有安全記憶體保護區，可保護儲存在這些金鑰上的私密金鑰。 FIDO2 安全性金鑰也有內建的反 hammering 屬性，例如在 Windows Hello 中，您無法解壓縮私密金鑰。

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>註冊 FIDO2 安全性金鑰的運作方式為何？

如需如何註冊及使用 FIDO2 安全性金鑰的詳細資訊，請參閱 [啟用無密碼安全性金鑰登入](howto-authentication-passwordless-security-key.md)。

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>系統管理員是否有辦法直接為使用者布建金鑰？

否，目前沒有。

## <a name="prerequisites"></a>先決條件

* [如果沒有網際網路連線能力，這項功能是否適用？](#does-this-feature-work-if-theres-no-internet-connectivity)
* [有哪些特定端點需要開放給 Azure AD？](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [如何? 識別 (Azure AD 加入的網域加入類型，或) 裝置的混合式 Azure AD 聯結 Windows 10？](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [應修補的 Dc 數目建議為何？](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [我可以在僅限內部部署裝置上部署 FIDO2 認證提供者嗎？](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [FIDO2 安全性金鑰登入無法針對我的網域系統管理員或其他高許可權帳戶運作。為什麼？](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>如果沒有網際網路連線能力，這項功能是否適用？

網際網路連線是啟用這項功能的必要條件。 使用者第一次使用 FIDO2 安全性金鑰登入時，他們必須具有網際網路連線能力。 針對後續的登入事件，快取登入應該會運作，並讓使用者在沒有網際網路連線的情況下進行驗證。

為保持一致的體驗，請確定裝置能夠存取網際網路，並可看到 Dc。

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>有哪些特定端點需要開放給 Azure AD？

下列是註冊和驗證所需的端點：

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

如需使用 Microsoft 線上產品所需端點的完整清單，請參閱 [Office 365 url 和 IP 位址範圍](/microsoft-365/enterprise/urls-and-ip-address-ranges)。

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>如何? 識別 (Azure AD 加入的網域加入類型，或) 裝置的混合式 Azure AD 聯結 Windows 10？

若要檢查 Windows 10 用戶端裝置是否具有正確的網域聯結類型，請使用下列命令：

```console
Dsregcmd/status
```

下列範例輸出顯示當 *>azureadjoined* 設定為 *[是]* 時，裝置已 Azure AD 聯結：

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

下列範例輸出顯示裝置為混合式 Azure AD 聯結，因為 *DomainedJoined* 也設定為 *[是]*。 *DomainName*也會顯示：

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

在 Windows Server 2016 或2019網域控制站上，檢查是否已套用下列修補程式。 如有需要，請執行 Windows Update 安裝它們：

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

從用戶端裝置執行下列命令，以確認已安裝修補程式的適當網域控制站的連線能力：

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>應修補的 Dc 數目建議為何？

建議您使用修補程式來修補大部分的 Windows Server 2016 或2019網域控制站，以確保它們可以處理您組織的驗證要求負載。

在 Windows Server 2016 或2019網域控制站上，檢查是否已套用下列修補程式。 如有需要，請執行 Windows Update 安裝它們：

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>我可以在僅限內部部署裝置上部署 FIDO2 認證提供者嗎？

否，僅限內部部署裝置不支援此功能。 FIDO2 認證提供者將不會顯示。

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>FIDO2 安全性金鑰登入無法針對我的網域系統管理員或其他高許可權帳戶運作。 原因為何？

預設安全性原則不會授與在內部部署資源上簽署高許可權帳戶的 Azure AD 許可權。

若要解除封鎖帳戶，請使用**Active Directory 消費者和電腦**修改*msDS-NeverRevealGroup* *Azure AD Kerberos 電腦物件的 Msds-neverrevealgroup 屬性 (CN = AzureADKerberos，OU = 網域控制站， \<domain-DN>) *。

## <a name="under-the-hood"></a>背後原理

* [Azure AD Kerberos 如何連結至我的內部部署 Active Directory Domain Services 環境？](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [我可以在哪裡查看在 AD 中建立並在 Azure AD 發佈的 Kerberos 伺服器物件？](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [為什麼我們不能將公開金鑰登錄到內部部署 AD DS 因此沒有網際網路的相依性？](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [如何在 Kerberos 伺服器物件上輪替金鑰？](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [為什麼需要 Azure AD Connect？它會從 Azure AD 將任何資訊寫回 AD DS 嗎？](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [要求 PRT + 部分 TGT 時，HTTP 要求/回應看起來會像什麼樣子？](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Azure AD Kerberos 如何連結至我的內部部署 Active Directory Domain Services 環境？

有兩個部分-內部部署 AD DS 環境，以及 Azure AD 的租使用者。

**Active Directory Domain Services (AD DS) **

Azure AD 的 Kerberos 伺服器會在內部部署 AD DS 環境中表示為網域控制站 (DC) 物件。 此 DC 物件是由多個物件所組成：

* *CN = AzureADKerberos，OU = 網域控制站，\<domain-DN>*
    
    *電腦*物件，代表 AD DS 中 (RODC) 的唯讀網域控制站。 沒有與這個物件相關聯的電腦。 相反地，它是 DC 的邏輯標記法。

* *CN = krbtgt_AzureAD，CN = Users，\<domain-DN>*

    代表 RODC Kerberos 票證授與票證 (TGT) 加密金鑰的 *使用者* 物件。

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335，CN = AzureAD，CN = System，\<domain-DN>*

    *ServiceConnectionPoint*物件，儲存 Azure AD Kerberos 伺服器物件的相關中繼資料。 系統管理工具會使用此物件來識別並找出 Azure AD 的 Kerberos 伺服器物件。

**Azure Active Directory**

Azure AD 的 Kerberos 伺服器會以 Azure AD 表示為 *KerberosDomain* 物件。 每個內部部署 AD DS 環境都會表示為 Azure AD 租使用者中的單一 *KerberosDomain* 物件。

例如，您可能有一個具有兩個網域（例如 *contoso.com* 和 *fabrikam.com*）的 AD DS 樹系。 如果您允許 Azure AD 對整個樹系發出 Kerberos 票證授與票證 (Tgt) ，Azure AD 中有兩個 *KerberosDomain* 物件可供 *contoso.com* ，另一個用於 *fabrikam.com*。

如果您有多個 AD DS 樹系，每個樹系中的每個網域都有一個 *KerberosDomain* 物件。

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>我可以在哪裡查看 AD DS 中建立併發布至 Azure AD 的 Kerberos 伺服器物件？

若要查看所有物件，請使用最新版 Azure AD Connect 隨附的 Azure AD Kerberos Server PowerShell Cmdlet。

如需詳細資訊，包括如何查看物件的指示，請參閱 [建立 Kerberos 伺服器物件](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object)。

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>為什麼我們不能將公開金鑰登錄到內部部署 AD DS 因此沒有網際網路的相依性？

我們收到 Windows Hello 企業版的部署模型複雜性的意見反應，因此想要簡化部署模型，而不需要使用憑證和 PKI (FIDO2 不會使用憑證) 。

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>如何在 Kerberos 伺服器物件上輪替金鑰？

就像任何其他 DC 一樣，Azure AD 的 Kerberos 伺服器加密 *krbtgt* 金鑰也應該定期輪替。 建議遵循與您用來旋轉所有其他 AD DS *krbtgt* 金鑰相同的排程。

> [!NOTE]
> 雖然有其他工具可輪替 *krbtgt* 金鑰，但您必須 [使用 PowerShell Cmdlet 來輪替 Azure AD Kerberos 伺服器的 *krbtgt* 金鑰](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) 。 此方法可確保在內部部署 AD DS 環境和 Azure AD 中都已更新金鑰。

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>為什麼需要 Azure AD Connect？ 它會從 Azure AD 將任何資訊寫回 AD DS 嗎？

Azure AD Connect 不會將資訊從 Azure AD 寫回 AD DS。 此公用程式包含 PowerShell 模組，可在 AD DS 中建立 Kerberos 伺服器物件，並將它發佈到 Azure AD。

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>要求 PRT + 部分 TGT 時，HTTP 要求/回應看起來會像什麼樣子？

HTTP 要求是標準的主要重新整理權杖， (PRT) 要求。 此 PRT 要求包含一個宣告，指出需要 (TGT) 的 Kerberos 票證授權票證。

| 宣告 | 值 | 說明                             |
|-------|-------|-----------------------------------------|
| Tgt   | true  | 宣告指出用戶端需要 TGT。 |

Azure AD 將加密的用戶端金鑰和訊息緩衝區合併為 PRT 回應中的其他屬性。 承載會使用 Azure AD 裝置工作階段金鑰進行加密。

| 欄位              | 類型   | 說明  |
|--------------------|--------|--------------|
| tgt_client_key     | 字串 | Base64 編碼的用戶端金鑰 (秘密) 。 此金鑰是用來保護 TGT 的用戶端密碼。 在此無密碼案例中，用戶端密碼會由伺服器在每個 TGT 要求中產生，然後在回應中傳回給用戶端。 |
| tgt_key_type       | int    | 內部部署 AD DS 金鑰類型，用於 KERB_MESSAGE_BUFFER 中所包含的用戶端金鑰和 Kerberos 工作階段金鑰。 |
| tgt_message_buffer | 字串 | Base64 編碼 KERB_MESSAGE_BUFFER。 |

## <a name="next-steps"></a>後續步驟

若要開始使用 FIDO2 安全性金鑰和內部部署資源的混合式存取，請參閱下列文章：

* [無密碼 FIDO2 安全性金鑰](howto-authentication-passwordless-security-key.md)
* [無密碼 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [無密碼內部部署](howto-authentication-passwordless-security-key-on-premises.md)
