---
title: 無密碼安全性金鑰登入內部部署資源 (預覽) -Azure Active Directory
description: '瞭解如何使用 Azure Active Directory (preview 啟用無密碼安全性金鑰登入內部部署資源) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88cdad5b2b92d9b7449d93e1abeb09c08df89e7b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964752"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>使用 Azure Active Directory (preview 啟用無密碼安全性金鑰登入內部部署資源) 

本檔著重于在已加入 **Azure AD** 且 **混合式 Azure AD 加入** Windows 10 裝置的環境中，啟用內部部署資源的無密碼 authentication。 這項功能可讓您使用 Microsoft 相容的安全性金鑰， (SSO) 內部部署資源，提供無縫的單一登入。

> [!NOTE]
> FIDO2 安全性金鑰是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>使用 FIDO2 金鑰將 SSO 部署到內部部署資源

Azure Active Directory (AD) 可以針對一或多個) 網域發出 Kerberos 票證授權票證 (Tgt Active Directory。 這項功能可讓使用者以新式認證（例如 FIDO2 安全性金鑰）登入 Windows，並存取傳統的 Active Directory 資源。 Kerberos 服務票證和授權會繼續由您的內部部署 Active Directory 網域控制站控制。

Azure AD 的 Kerberos 伺服器物件會建立在您的內部部署 Active Directory 中，然後安全地發佈至 Azure Active Directory。 物件未與任何實體伺服器相關聯。 這只是 Azure Active Directory 可以用來為您的 Active Directory 網域產生 Kerberos Tgt 的資源。

![從 Azure AD 和 AD DS 取得票證授權票證 (TGT) ](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. 使用者使用 FIDO2 安全性金鑰登入他們的 Windows 10 裝置，並向 Azure AD 進行驗證。
1. Azure AD 會檢查與使用者內部部署 AD 網域相符之 Kerberos 伺服器金鑰的目錄。
   1. Azure AD 為使用者的內部部署 AD 網域產生 Kerberos TGT。 TGT 只包含使用者的 SID。 TGT 中不包含任何授權資料。
1. TGT 會連同 Azure AD 的主要重新整理權杖 (PRT) 傳回給用戶端。
1. 用戶端電腦會連線到內部部署 AD 網域控制站，並為完整格式的 TGT 交易部分 TGT。
1. 用戶端電腦現在有 Azure AD PRT 和完整 Active Directory TGT，而且可以存取雲端和內部部署資源。

## <a name="requirements"></a>規格需求

組織必須完成這些步驟，才能在完成本文中的步驟之前，先將 [無密碼安全性金鑰登入 Windows 10 裝置 (preview) ](howto-authentication-passwordless-security-key.md) 。

組織也必須符合下列軟體需求。

- 裝置必須執行 Windows 10 2004 版或更新版本。
- 您必須有 [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)的版本1.4.32.0 版或更新版本。
  - 如需可用 Azure AD 混合式驗證選項的詳細資訊，請參閱 [為您的 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](../hybrid/choose-ad-authn.md) ，並 [選取要用於 Azure AD Connect 的安裝類型](../hybrid/how-to-connect-install-select-installation.md)。
- 您的 Windows Server 網域控制站必須安裝下列修補程式：
    - 若為 Windows Server 2016- https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - 若為 Windows Server 2019- https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>支援的案例

此案例支援在下列兩種案例中，單一登入 (SSO) ：

- 針對 Microsoft 365 和其他已啟用 SAML 的應用程式等雲端資源。
- 適用于內部部署資源，並 Windows-Integrated 對網站的驗證。 這些資源可以包含需要 IIS 驗證的網站和 SharePoint 網站，以及（或）使用 NTLM 驗證的資源。

### <a name="unsupported-scenarios"></a>不支援的案例

下列案例不受支援：

- Windows Server Active Directory Domain Services (AD DS) 網域加入 (僅限內部部署裝置) 部署。
- 使用安全性金鑰的 RDP、VDI 和 Citrix 案例。
- S/MIME 使用安全性金鑰。
- 使用安全性金鑰的「執行為」。
- 使用安全性金鑰登入伺服器。

## <a name="create-kerberos-server-object"></a>建立 Kerberos 伺服器物件

系統管理員會使用 Azure AD Connect server 的 PowerShell 工具，在其內部部署目錄中建立 Azure AD 的 Kerberos 伺服器物件。 在組織中包含 Azure AD 使用者的每個網域和樹系中，執行下列步驟：

1. 升級至 Azure AD Connect 的最新版本。 這些指示假設您已將 Azure AD Connect 設定為支援您的混合式環境。
1. 在 Azure AD Connect 伺服器上，開啟已提升許可權的 PowerShell 提示字元，然後流覽至 `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 執行下列 PowerShell 命令，在您的內部部署 Active Directory 網域和 Azure Active Directory 租使用者中建立新的 Azure AD Kerberos 伺服器物件。

> [!NOTE]
> 在 `contoso.corp.com` 下列範例中，以您的內部部署 Active Directory 功能變數名稱取代。

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>查看和驗證 Azure AD Kerberos 伺服器

您可以使用下列命令來查看並驗證新建立的 Azure AD Kerberos 伺服器：

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

此命令會輸出 Azure AD Kerberos 伺服器的屬性。 您可以檢查屬性，以確認所有專案都是正確的順序。

| 屬性 | 描述 |
| --- | --- |
| ID | AD DS DC 物件的唯一識別碼。 此識別碼有時稱為「位置」或「分支識別碼」。 |
| DomainDnsName | Active Directory 網域的 DNS 功能變數名稱。 |
| ComputerAccount | Azure AD Kerberos 伺服器物件的電腦帳戶物件 (DC) 。 |
| UserAccount | 停用的使用者帳戶物件，該物件保留 Azure AD Kerberos 伺服器 TGT 加密金鑰。 此帳戶的 DN 是 `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion | Azure AD Kerberos Server TGT 加密金鑰的金鑰版本。 建立金鑰時，會指派此版本。 然後，每次輪替金鑰時，就會遞增版本。 遞增是以複寫中繼資料為基礎，而且可能大於一。 例如，初始 *KeyVersion* 可能是 *192272*。 第一次輪替金鑰時，版本可能前進至 *212621*。 要確認的重要事項是內部部署物件的 *KeyVersion* 和雲端物件的 *CloudKeyVersion* 相同。 |
| KeyUpdatedOn | 更新或建立 Azure AD Kerberos Server TGT 加密金鑰的日期和時間。 |
| KeyUpdatedFrom | 上次更新 Azure AD Kerberos Server TGT 加密金鑰的 DC。 |
| CloudId | Azure AD 物件中的識別碼。 必須符合上述的識別碼。 |
| CloudDomainDnsName | Azure AD 物件中的 *DomainDnsName* 。 必須符合上述 *DomainDnsName* 。 |
| CloudKeyVersion | Azure AD 物件中的 *KeyVersion* 。 必須符合上述 *KeyVersion* 。 |
| CloudKeyUpdatedOn | Azure AD 物件中的 *KeyUpdatedOn* 。 必須符合上述 *KeyUpdatedOn* 。 |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>輪替 Azure AD Kerberos 伺服器金鑰

應定期輪替 Azure AD Kerberos Server encryption krbtgt 金鑰。 建議您遵循用來旋轉所有其他 Active Directory 網網域控制站 krbtgt 金鑰的相同排程。

> [!WARNING]
> 不過，還有其他工具可以輪替 krbtgt 金鑰，但您必須使用本檔中所述的工具，來輪替 Azure AD Kerberos 伺服器的 krbtgt 金鑰。 這可確保內部部署 AD 和 Azure AD 中的金鑰都已更新。

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>移除 Azure AD Kerberos 伺服器

如果您想要還原案例，並從內部部署 Active Directory 和 Azure Active Directory 中移除 Azure AD 的 Kerberos 伺服器，請執行下列命令：

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>多樹系和多網域案例

Azure AD 的 Kerberos 伺服器物件會以 Azure AD 表示為 *KerberosDomain* 物件。 每個內部部署 Active Directory 網域都會表示為 Azure AD 中的單一 *KerberosDomain* 物件。

例如，您的組織有一個具有兩個網域和的 Active Directory 樹系 `contoso.com` `fabrikam.com` 。 如果您選擇允許 Azure AD 發出整個樹系的 Kerberos Tgt，Azure AD 中有兩個 *KerberosDomain* 物件。 的一個 *KerberosDomain* 物件 `contoso.com` ，一個用於 `fabrikam.com` 。 如果您有多個 Active Directory 樹系，每個樹系中的每個網域都有一個 *KerberosDomain* 物件。

您必須執行這些步驟，以在組織中包含 Azure AD 使用者的每個網域和樹系中 [建立 Kerberos 伺服器物件](#create-kerberos-server-object) 。

## <a name="known-behavior"></a>已知行為

如果您的密碼已過期，則會封鎖使用 FIDO 進行登入。 預期使用者必須先重設其密碼，才能夠使用 FIDO 登入。

## <a name="troubleshooting-and-feedback"></a>疑難排解和意見反應

如果您想要在預覽這項功能時分享意見反應或遇到問題，請使用下列步驟透過 Windows 意見反應中樞應用程式共用：

1. 啟動 **意見反應中樞** ，並確認您已登入。
1. 在下列分類下提交意見反應：
   - 類別：安全性和隱私權
   - 子類別： FIDO
1. 若要捕獲記錄，請使用選項來 **重新建立我的問題**

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-this-work-in-my-on-premises-environment"></a>這是否適用于我的內部部署環境？

這項功能不適用於單純的內部部署 Active Directory Domain Services (AD DS) 環境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的組織需要雙因素驗證才能存取資源。 我可以如何支援這項需求？

安全性金鑰有各式各樣的外型規格。 請與感興趣的裝置製造商討論，如何使用 PIN 或生物特徵辨識作為第二個因素來啟用裝置。

### <a name="can-admins-set-up-security-keys"></a>系統管理員可以設定安全性金鑰嗎？

我們正在處理此功能 (GA) 這項功能的一般可用性。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>我可以在哪裡找到符合規範的安全性金鑰？

[FIDO2 安全性金鑰](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果我遺失安全性金鑰，該怎麼辦？

您可以流覽至 [ **安全性資訊** ] 頁面並移除安全性金鑰，以移除 Azure 入口網站中的金鑰。

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>我在建立混合式 Azure AD 加入電腦之後，無法立即使用 FIDO

如果安裝混合式 Azure AD 加入的電腦，在加入網域和重新開機程式之後，您必須使用密碼登入，並等候原則進行同步處理，才能使用 FIDO 登入。

- 在命令視窗中輸入以檢查目前 `dsregcmd /status` 的狀態，並檢查 *>azureadjoined* 和 *DomainJoined* 是否顯示為 *[是*]。
- 此延遲是已加入網域之裝置的已知限制，而且不是 FIDO 特有的限制。

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>使用 FIDO 登入並取得認證提示之後，我無法取得我的 NTLM 網路資源的 SSO

請確定已修補足夠的網域控制站，以回應資源要求的時間。 若要檢查您是否可以看到執行此功能的網域控制站，請參閱的輸出 `nltest /dsgetdc:contoso /keylist /kdc` 。

## <a name="next-steps"></a>後續步驟

[深入瞭解無密碼](concept-authentication-passwordless.md)
