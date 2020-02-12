---
title: Azure AD Domain Services 中同步處理的運作方式 |Microsoft Docs
description: 瞭解從 Azure AD 租使用者或內部部署 Active Directory Domain Services 環境中的物件和認證，到 Azure Active Directory Domain Services 受控網域的同步處理常式如何運作。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: b2a1bcedcc459a21bbc8a461ba9c8d9a8d65aebe
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132195"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>如何在 Azure AD Domain Services 受控網域中同步處理物件和認證

Azure Active Directory Domain Services （AD DS）受控網域中的物件和認證可以在網域內的本機建立，或從 Azure Active Directory （Azure AD）租使用者進行同步處理。 當您第一次部署 Azure AD DS 時，會設定並啟動自動單向同步處理，以從 Azure AD 複寫物件。 這個單向同步處理會繼續在背景中執行，讓 Azure AD DS 受控網域與 Azure AD 的任何變更保持最新狀態。 從 Azure AD DS 回到 Azure AD 不會進行任何同步處理。

在混合式環境中，來自內部部署 AD DS 網域的物件和認證，可以使用 Azure AD Connect 同步處理至 Azure AD。 一旦這些物件成功同步處理到 Azure AD，自動背景同步處理就會將這些物件和認證提供給使用 Azure AD DS 受控網域的應用程式。

下圖說明 Azure AD DS、Azure AD 和選擇性內部部署 AD DS 環境之間的同步處理運作方式：

![Azure AD Domain Services 受控網域的同步處理總覽](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>從 Azure AD 同步處理至 Azure AD DS

使用者帳戶、群組成員資格及認證雜湊會以一種方式從 Azure AD 同步處理到 Azure AD DS。 此同步處理程序是自動執行的。 您不需要設定、監視或管理此同步處理常式。 根據 Azure AD 目錄中的物件數目而定，初始同步處理可能需要數小時到幾天的時間。 完成初始同步處理之後，Azure AD 中進行的變更（例如密碼或屬性變更），就會自動同步處理到 Azure AD DS。

同步處理常式的設計是單向/單向。 不會將 Azure AD DS 的變更反向同步處理回 Azure AD。 Azure AD DS 受控網域主要是唯讀的，但您可以建立的自訂 Ou 除外。 您無法變更 Azure AD DS 受控網域內的使用者屬性、使用者密碼或群組成員資格。

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Azure AD DS 的屬性同步處理和對應

下表列出一些常見的屬性，以及它們如何同步處理至 Azure AD DS。

| Azure AD DS 中的屬性 | 來源 | 注意 |
|:--- |:--- |:--- |
| UPN | Azure AD 租使用者中的使用者*UPN*屬性 | Azure AD 租使用者的 UPN 屬性會依相同方式同步處理，以 Azure AD DS。 最可靠的登入 Azure AD DS 受控網域的方式是使用 UPN。 |
| SAMAccountName | Azure AD 租使用者中的使用者*mailNickname*屬性或自動產生 | *SAMAccountName*屬性是源自 Azure AD 租使用者中的*mailNickname*屬性。 如果有多個使用者帳戶具有相同的*mailNickname*屬性，則會自動產生*SAMAccountName* 。 如果使用者的*mailNickname*或*UPN*前置詞超過20個字元，則會自動產生*samaccountname*以符合*samaccountname*屬性的20個字元限制。 |
| 密碼 | Azure AD 租使用者的使用者密碼 | NTLM 或 Kerberos 驗證所需的舊版密碼雜湊會從 Azure AD 租使用者進行同步處理。 如果 Azure AD 租使用者已設定為使用 Azure AD Connect 進行混合式同步處理，這些密碼雜湊就源自內部部署 AD DS 環境。 |
| 主要使用者/群組 SID | 自動產生 | 使用者/群組帳戶的主要 SID 會在 Azure AD DS 中自動產生。 此屬性不符合內部部署 AD DS 環境中物件的主要使用者/群組 SID。 這種不相符的原因是 Azure AD DS 受控網域的 SID 命名空間與內部部署 AD DS 網域不同。 |
| 使用者和群組的 SID 歷程記錄 | 內部部署主要使用者和群組 SID | Azure AD DS 中使用者和群組的*SidHistory*屬性會設定為符合內部部署 AD DS 環境中對應的主要使用者或群組 SID。 這項功能有助於將內部部署應用程式隨即轉移至 Azure AD DS，因為您不需要重新 ACL 資源。 |

> [!TIP]
> **使用 UPN 格式登入受控網域**Azure AD DS 受控網域中的某些使用者帳戶可能會自動產生*SAMAccountName*屬性（例如 `CONTOSO\driley`）。 使用者自動產生的*SAMAccountName*可能與其 UPN 前置詞不同，因此不一定是可靠的登入方式。
>
> 例如，如果有多個使用者具有相同的*mailNickname*屬性，或使用者的 UPN 前置詞太長，可能會自動產生這些使用者的*SAMAccountName* 。 使用 UPN 格式（例如 `driley@contoso.com`），以可靠的方式登入 Azure AD DS 受控網域。

### <a name="attribute-mapping-for-user-accounts"></a>使用者帳戶的屬性對應

下表說明如何將 Azure AD 中使用者物件的特定屬性同步處理至 Azure AD DS 中的對應屬性。

| Azure AD 中的使用者屬性 | Azure AD DS 中的使用者屬性 |
|:--- |:--- |
| accountEnabled |userAccountControl (設定或清除 ACCOUNT_DISABLED 位元) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName （有時可能會自動產生） |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (設定或清除 DONT_EXPIRE_PASSWORD 位元) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>群組的屬性對應

下表說明如何將 Azure AD 中群組物件的特定屬性同步處理至 Azure AD DS 中的對應屬性。

| Azure AD 中的群組屬性 | Azure AD DS 中的群組屬性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName （有時可能會自動產生） |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>從內部部署 AD DS 同步處理至 Azure AD 和 Azure AD DS

Azure AD Connect 可用來將使用者帳戶、群組成員資格及認證雜湊從內部部署 AD DS 環境同步處理至 Azure AD。 使用者帳戶的屬性（例如 UPN 和內部部署安全識別碼（SID））會進行同步處理。 若要使用 Azure AD DS 登入，NTLM 和 Kerberos 驗證所需的舊版密碼雜湊也會同步處理至 Azure AD。

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在 Azure AD DS 受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

如果您設定回寫，Azure AD 的變更會同步處理回內部部署 AD DS 環境。 例如，如果使用者使用 Azure AD 自助式密碼管理來變更其密碼，則會在內部部署 AD DS 環境中更新密碼。

> [!NOTE]
> 請一律使用最新版的 Azure AD Connect 版本，以確保您已具備所有已知問題的修正。

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>從多樹系內部部署環境同步處理

許多組織都有一個非常複雜的內部部署 AD DS 環境，其中包含多個樹系。 Azure AD Connect 支援將使用者、群組和認證雜湊從多樹系環境同步處理至 Azure AD。

Azure AD 具有更簡單和一般的命名空間。 若要讓使用者可靠地存取受 Azure AD 保護的應用程式，請解決不同樹系中各個使用者帳戶之間的 UPN 衝突。 Azure AD DS 受控網域會使用類似于 Azure AD 的平面 OU 結構。 即使您已在內部部署環境中設定階層式 OU 結構，所有使用者帳戶和群組都會儲存在*AADDC Users*容器中（儘管已從不同的內部部署網域或樹系進行同步處理）。 Azure AD DS 受控網域會將任何階層式 OU 結構壓平合併。

如先前所述，不會從 Azure AD DS 回到 Azure AD 的同步處理。 您可以在 Azure AD DS 中[建立自訂群組織單位（OU）](create-ou.md) ，然後在這些自訂 ou 內建立使用者、群組或服務帳戶。 在自訂 Ou 中建立的任何物件都不會同步處理回 Azure AD。 這些物件只能在 Azure AD DS 受控網域內使用，而且不能使用 Azure AD PowerShell Cmdlet、Microsoft Graph API，或使用 Azure AD 管理 UI 來顯示。

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>什麼不會同步處理至 Azure AD DS

下列物件或屬性不會從內部部署 AD DS 環境同步處理至 Azure AD 或 Azure AD DS：

* **排除的屬性：** 您可以使用 Azure AD Connect，選擇排除特定屬性，使其無法從內部部署 AD DS 環境同步處理至 Azure AD。 這些排除的屬性接著不會在 Azure AD DS 中提供。
* **群組原則：** 在內部部署 AD DS 環境中設定的群組原則不會同步處理至 Azure AD DS。
* **Sysvol 資料夾：** 內部部署 AD DS 環境中*Sysvol*資料夾的內容不會同步處理至 Azure AD DS。
* **電腦物件：** 加入內部部署 AD DS 環境之電腦的電腦物件不會同步處理至 Azure AD DS。 這些電腦沒有與 Azure AD DS 受控網域的信任關係，而且僅屬於內部部署 AD DS 環境。 在 Azure AD DS 中，只會顯示已明確加入受控網域之電腦的電腦物件。
* **使用者和群組的 SidHistory 屬性：** 內部部署 AD DS 環境中的主要使用者和主要群組 Sid 會同步處理到 Azure AD DS。 不過，使用者和群組的現有*SidHistory*屬性不會從內部部署 AD DS 環境同步處理至 Azure AD DS。
* **組織單位（OU）結構：** 內部部署 AD DS 環境中定義的組織單位不會同步處理至 Azure AD DS。 Azure AD DS 中有兩個內建 Ou-一個供使用者，另一個用於電腦。 Azure AD DS 受控網域具有單層的 OU 結構。 您可以選擇[在您的受控網域中建立自訂 OU](create-ou.md)。

## <a name="password-hash-synchronization-and-security-considerations"></a>密碼雜湊同步處理和安全性考量

當您啟用 Azure AD DS 時，必須要有 NTLM + Kerberos 驗證的舊版密碼雜湊。 Azure AD 不會儲存純文字密碼，因此無法為現有的使用者帳戶自動產生這些雜湊。 一旦產生並儲存，NTLM 和 Kerberos 相容的密碼雜湊一律會以加密的方式儲存在 Azure AD 中。

加密金鑰對每個 Azure AD 租使用者而言是唯一的。 這些雜湊會進行加密，因此只有 Azure AD DS 可以存取解密金鑰。 Azure AD 中沒有任何其他服務或元件具有解密金鑰的存取權。

然後，舊版密碼雜湊會從 Azure AD 同步處理到 Azure AD DS 受控網域的網域控制站。 Azure AD DS 中這些受管理網域控制站的磁片會進行待用加密。 這些密碼雜湊會儲存在這些網域控制站上並受到保護，類似于在內部部署 AD DS 環境中儲存和保護密碼的方式。

對於僅限雲端的 Azure AD 環境，[使用者必須重設/變更其密碼](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，才能產生必要的密碼雜湊，並將其儲存在 Azure AD 中。 針對在啟用 Azure AD Domain Services 之後於 Azure AD 中建立的任何雲端使用者帳戶，以 NTLM 和 Kerberos 相容的格式產生並儲存密碼雜湊。 這些新帳戶不需要重設或變更其密碼，就會產生舊版密碼雜湊。

針對使用 Azure AD Connect 從內部部署 AD DS 環境同步的混合式使用者帳戶，您必須[將 Azure AD Connect 設定為以 NTLM 和 Kerberos 相容的格式同步處理密碼雜湊](tutorial-configure-password-hash-sync.md)。

## <a name="next-steps"></a>後續步驟

如需有關密碼同步化細節的詳細資訊，請參閱[密碼雜湊同步處理如何與 Azure AD Connect 搭配運作](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)。

若要開始使用 Azure AD DS，請[建立受控網域](tutorial-create-instance.md)。
