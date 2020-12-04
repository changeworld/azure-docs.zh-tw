---
title: Azure AD Domain Services 中的同步處理運作方式 |Microsoft Docs
description: 瞭解從 Azure AD 租使用者或內部部署 Active Directory Domain Services 環境到 Azure Active Directory Domain Services 受控網域的物件和認證，同步處理常式的運作方式。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 92d440d019942219b322ef084b45317983d04fbe
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602235"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>如何在 Azure Active Directory Domain Services 受控網域中同步處理物件和認證

Azure Active Directory Domain Services (Azure AD DS) 受控網域中的物件和認證，可以在網域內本機建立，或從 Azure Active Directory (Azure AD) 租使用者進行同步處理。 當您第一次部署 Azure AD DS 時，會設定並啟動自動單向同步處理，以從 Azure AD 複寫物件。 此單向同步處理會繼續在背景中執行，以 Azure AD 進行任何變更，讓 Azure AD DS 受控網域保持在最新狀態。 從 Azure AD DS 回到 Azure AD 不會進行任何同步處理。

在混合式環境中，您可以使用 Azure AD Connect，將內部部署 AD DS 網域中的物件和認證同步處理到 Azure AD。 一旦將這些物件順利同步處理至 Azure AD 之後，自動背景同步就會將這些物件和認證提供給使用受控網域的應用程式。

如果使用 ADFS 針對同盟驗證設定了內部內部部署 AD DS 和 Azure AD，則 Azure DS 中不會有 (目前/有效的) 密碼雜湊。 在執行送出驗證之前建立的 Azure AD 使用者帳戶可能會有舊的密碼雜湊，但這可能不符合其內部內部部署密碼的雜湊。 因此 Azure AD DS 將無法驗證使用者認證。

下圖說明同步處理在 Azure AD DS、Azure AD 和選擇性內部部署 AD DS 環境之間的運作方式：

![Azure AD Domain Services 受控網域的同步處理總覽](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>從 Azure AD 同步處理至 Azure AD DS

使用者帳戶、群組成員資格和認證雜湊會以一種方式從 Azure AD 同步處理至 Azure AD DS。 此同步處理程序是自動執行的。 您不需要設定、監視或管理此同步處理常式。 初始同步處理可能需要數小時到數天的時間，視 Azure AD 目錄中的物件數目而定。 初始同步處理完成之後，Azure AD 中所做的變更，例如密碼或屬性變更，則會自動同步處理至 Azure AD DS。

在 Azure AD 中建立使用者時，他們在變更 Azure AD 中的密碼之前，並不會同步處理至 Azure AD DS。 此密碼變更程序會在 Azure AD 中產生並儲存 Kerberos 和 NTLM 驗證的密碼雜湊。 需要密碼雜湊才能成功驗證 Azure AD DS 中的使用者。

同步處理常式是設計的單向/單向。 不會將變更從 Azure AD DS 反向同步處理回 Azure AD。 受控網域大多是唯讀的，但您可以建立的自訂 Ou 除外。 您無法對受控網域內的使用者屬性、使用者密碼或群組成員資格進行變更。

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>屬性同步處理和對應至 Azure AD DS

下表列出一些常見的屬性，以及它們如何同步處理至 Azure AD DS。

| Azure AD DS 中的屬性 | 來源 | 備註 |
|:--- |:--- |:--- |
| UPN | Azure AD 租使用者中的使用者 *UPN* 屬性 | Azure AD 租使用者的 UPN 屬性會依原樣同步處理至 Azure AD DS。 登入受控網域最可靠的方式是使用 UPN。 |
| SAMAccountName | Azure AD 租使用者中的使用者 *mailNickname* 屬性或自動產生 | *SAMAccountName* 屬性是源自 Azure AD 租使用者中的 *mailNickname* 屬性。 如果有多個使用者帳戶具有相同的 *mailNickname* 屬性，就會自動產生 *SAMAccountName* 。 如果使用者的 *mailNickname* 或 *UPN* 前置長度超過20個字元，則會自動產生 *samaccountname* 以符合 *samaccountname* 屬性的20個字元限制。 |
| 密碼 | Azure AD 租使用者的使用者密碼 | NTLM 或 Kerberos 驗證所需的舊版密碼雜湊會從 Azure AD 租使用者同步處理。 如果 Azure AD 租使用者已設定為使用 Azure AD Connect 進行混合式同步處理，則這些密碼雜湊源自內部部署 AD DS 環境。 |
| 主要使用者/群組 SID | 貨 | 使用者/群組帳戶的主要 SID 會在 Azure AD DS 中自動產生。 此屬性與內部部署 AD DS 環境中物件的主要使用者/群組 SID 不相符。 這種不相符的原因是受控網域具有與內部部署 AD DS 網域不同的 SID 命名空間。 |
| 使用者和群組的 SID 歷程記錄 | 內部部署主要使用者和群組 SID | Azure AD DS 中使用者和群組的 *SidHistory* 屬性會設定為符合內部部署 AD DS 環境中對應的主要使用者或群組 SID。 這項功能有助於將內部部署應用程式隨即轉移至 Azure AD DS，因為您不需要重新建立 ACL 資源。 |

> [!TIP]
> **使用 UPN 格式登入受控網域** 可能 *SAMAccountName* 會 `AADDSCONTOSO\driley` 針對受控網域中的某些使用者帳戶自動產生 SAMAccountName 屬性（例如）。 使用者的自動產生 *SAMAccountName* 可能與 UPN 首碼不同，因此不一定是可靠的登入方式。
>
> 例如，如果有多個使用者具有相同的 *mailNickname* 屬性，或使用者的 UPN 前置詞太長，可能會自動產生這些使用者的 *SAMAccountName* 。 使用 UPN 格式（例如）， `driley@aaddscontoso.com` 可靠地登入受控網域。

### <a name="attribute-mapping-for-user-accounts"></a>使用者帳戶的屬性對應

下表說明 Azure AD 中的使用者物件特定屬性如何同步處理至 Azure AD DS 中的對應屬性。

| Azure AD 中的使用者屬性 | Azure AD DS 中的使用者屬性 |
|:--- |:--- |
| accountEnabled |userAccountControl (設定或清除 ACCOUNT_DISABLED 位元) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| employeedId |employeeId |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (有時可能會自動產生)  |
| manager |manager |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (設定或清除 DONT_EXPIRE_PASSWORD 位元) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>群組的屬性對應

下表說明 Azure AD 中群組物件的特定屬性如何同步處理至 Azure AD DS 中的對應屬性。

| Azure AD 中的群組屬性 | Azure AD DS 中的群組屬性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (有時可能會自動產生)  |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>從內部部署 AD DS 同步處理至 Azure AD 和 Azure AD DS

Azure AD Connect 用來將使用者帳戶、群組成員資格和認證雜湊從內部部署 AD DS 環境同步處理至 Azure AD。 系統會同步處理使用者帳戶的屬性，例如 UPN 和內部部署安全識別碼 (SID) 。 若要使用 Azure AD DS 登入，NTLM 和 Kerberos 驗證所需的舊版密碼雜湊也會同步處理到 Azure AD。

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在此受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

如果您設定回寫，Azure AD 的變更會同步處理回內部部署 AD DS 環境。 例如，如果使用者使用 Azure AD 自助式密碼管理來變更其密碼，則會在內部部署 AD DS 環境中更新密碼。

> [!NOTE]
> 請一律使用最新版的 Azure AD Connect 版本，以確保您已具備所有已知問題的修正。

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>從多樹系內部部署環境同步處理

許多組織都有相當複雜的內部部署 AD DS 環境，其中包含多個樹系。 Azure AD Connect 支援將使用者、群組和認證雜湊從多樹系環境同步處理到 Azure AD。

Azure AD 有更簡單且一般的命名空間。 若要讓使用者可靠地存取受 Azure AD 保護的應用程式，請解決不同樹系中各個使用者帳戶之間的 UPN 衝突。 受控網域使用類似于 Azure AD 的平面 OU 結構。 即使您已在內部部署環境中設定階層式 OU 結構，所有使用者帳戶和群組都會儲存在 *AADDC Users* 容器中（儘管是從不同的內部部署網域或樹系進行同步處理）。 受控網域會壓平合併任何階層式 OU 結構。

如先前所述，不會從 Azure AD DS 同步處理回 Azure AD。 您可以在 Azure AD DS 中 [建立自訂群組織單位 (OU) ](create-ou.md) ，然後在這些自訂 ou 內建立使用者、群組或服務帳戶。 在自訂 Ou 中建立的物件都不會同步回到 Azure AD。 這些物件僅適用于受控網域中，而且不會使用 Azure AD PowerShell Cmdlet、Microsoft Graph API 或使用 Azure AD 管理 UI 來顯示。

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>未同步至 Azure AD DS 的內容

下列物件或屬性不會從內部部署 AD DS 環境同步處理至 Azure AD 或 Azure AD DS：

* **排除的屬性：** 您可以選擇使用 Azure AD Connect，將某些屬性排除 Azure AD 從內部部署 AD DS 環境同步處理至。 這些排除的屬性接著無法在 Azure AD DS 中使用。
* **群組原則：** 在內部部署 AD DS 環境中設定的群組原則不會同步到 Azure AD DS。
* **Sysvol 資料夾：** 內部部署 AD DS 環境中 *Sysvol* 資料夾的內容不會同步到 Azure AD DS。
* **電腦物件：** 加入內部部署 AD DS 環境之電腦的電腦物件不會同步處理至 Azure AD DS。 這些電腦與受控網域沒有信任關係，而且只屬於內部部署 AD DS 環境。 在 Azure AD DS 中，只會顯示已明確將網域加入受控網域之電腦的電腦物件。
* **使用者和群組的 SidHistory 屬性：** 內部部署 AD DS 環境中的主要使用者和主要群組 Sid 會同步處理至 Azure AD DS。 不過，使用者和群組的現有 *SidHistory* 屬性不會從內部部署 AD DS 環境同步至 Azure AD DS。
* **組織單位 (OU) 結構：** 內部部署 AD DS 環境中定義的組織單位不會與 Azure AD DS 同步處理。 Azure AD DS 中有兩個內建 Ou-一個用於使用者，另一個用於電腦。 受控網域具有平面 OU 結構。 您可以選擇 [在受控網域中建立自訂 OU](create-ou.md)。

## <a name="password-hash-synchronization-and-security-considerations"></a>密碼雜湊同步處理和安全性考量

當您啟用 Azure AD DS 時，需要 NTLM + Kerberos 驗證的舊版密碼雜湊。 Azure AD 不會儲存純文字密碼，因此無法為現有的使用者帳戶自動產生這些雜湊。 一旦產生並儲存之後，NTLM 和 Kerberos 相容的密碼雜湊一律會以加密方式儲存在 Azure AD 中。

加密金鑰對每個 Azure AD 租使用者而言都是唯一的。 這些雜湊會經過加密，因此只有 Azure AD DS 才有解密金鑰的存取權。 Azure AD 中沒有任何其他服務或元件具有解密金鑰的存取權。

然後，舊版密碼雜湊會從 Azure AD 同步處理至受控網域的網域控制站。 這些受管理的網域控制站在 Azure AD DS 中的磁片會在靜止時加密。 這些密碼雜湊會儲存在這些網域控制站上並受到保護，類似于在內部部署 AD DS 環境中儲存和保護密碼的方式。

針對僅限雲端的 Azure AD 環境， [使用者必須重設/變更其密碼](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) ，才能產生所需的密碼雜湊，並將其儲存在 Azure AD 中。 針對在啟用 Azure AD Domain Services 之後於 Azure AD 中建立的任何雲端使用者帳戶，以 NTLM 和 Kerberos 相容的格式產生並儲存密碼雜湊。 所有雲端使用者帳戶都必須先變更密碼，才能將其同步處理至 Azure AD DS。

針對使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的混合式使用者帳戶，您必須 [設定 Azure AD Connect，以同步處理 NTLM 和 Kerberos 相容格式的密碼雜湊](tutorial-configure-password-hash-sync.md)。

## <a name="next-steps"></a>後續步驟

如需有關密碼同步化細節的詳細資訊，請參閱 Azure AD Connect 中的 [密碼雜湊同步處理的運作方式](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)。

若要開始使用 Azure AD DS，請 [建立受控網域](tutorial-create-instance.md)。
