---
title: 同步在 Azure AD 域服務中的工作原理 |微軟文檔
description: 瞭解同步過程如何適用于從 Azure AD 租戶或本地活動目錄域服務環境到 Azure 活動目錄域服務託管域的物件和憑據。
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
ms.openlocfilehash: 7e0e904b182a57a51b5d76f0acebc13bce5902b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944423"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>如何在 Azure AD 域服務託管域中同步物件和憑據

Azure 活動目錄域服務 （AD DS） 託管域中的物件和憑據可以在域中本地創建，也可以從 Azure 活動目錄 （Azure AD） 租戶同步。 首次部署 Azure AD DS 時，將配置自動單向同步，並開始從 Azure AD 複製物件。 此單向同步將繼續在後臺運行，以保持 Azure AD 託管域與 Azure AD 的任何更改保持最新。 從 Azure AD DS 到 Azure AD 不執行任何同步。

在混合環境中，本地 AD DS 域中的物件和憑據可以使用 Azure AD 連接同步到 Azure AD。 成功將這些物件同步到 Azure AD 後，自動後臺同步將使用這些物件和憑據提供給使用 Azure AD DS 託管域的應用程式。

下圖說明瞭同步如何在 Azure AD DS、Azure AD 和可選的本地 AD DS 環境之間工作：

![Azure AD 域服務託管域的同步概述](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>從 Azure AD 到 Azure AD DS 的同步

使用者帳戶、組成員身份和憑據雜湊以 Azure AD 到 Azure AD DS 的方式同步。 此同步處理程序是自動執行的。 您無需配置、監視或管理此同步過程。 初始同步可能需要幾個小時到幾天，具體取決於 Azure AD 目錄中的物件數。 完成初始同步後，在 Azure AD 中所做的更改（如密碼或屬性更改）將自動同步到 Azure AD DS。

同步過程是單向的/單向的。 從 Azure AD DS 到 Azure AD 的更改沒有反向同步。 Azure AD DS 託管域大部分是唯讀的，但您可以創建的自訂 O 除外。 不能更改 Azure AD DS 託管域中的使用者屬性、使用者密碼或組成員身份。

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>屬性同步和映射到 Azure AD DS

下表列出了一些常見屬性以及它們如何同步到 Azure AD DS。

| Azure AD DS 中的屬性 | 來源 | 注意 |
|:--- |:--- |:--- |
| UPN | Azure AD 租戶中的使用者的*UPN*屬性 | Azure AD 租戶中的 UPN 屬性與 Azure AD DS 同步。 登錄到 Azure AD DS 託管域的最可靠方法是使用 UPN。 |
| SAMAccountName | Azure AD 租戶或自動生成的使用者*的郵件昵稱*屬性 | *SAMAccountName*屬性來自 Azure AD 租戶中的*郵件昵稱*屬性。 如果多個使用者帳戶具有相同的*郵件昵稱*屬性，則*SAMAccountName*將自動生成。 如果使用者*的郵件昵稱*或*UPN*首碼超過 20 個字元，則會自動生成*SAMAccountName*以滿足*SAMAccountName*屬性上的 20 個字元限制。 |
| 密碼 | 來自 Azure AD 租戶的使用者密碼 | NTLM 或 Kerberos 身份驗證所需的舊密碼雜湊從 Azure AD 租戶同步。 如果 Azure AD 租戶配置為使用 Azure AD Connect 進行混合同步，則這些密碼雜湊值來自本地 AD DS 環境。 |
| 主要使用者/群組 SID | 自動生成 | 使用者/群組帳戶的主 SID 在 Azure AD DS 中自動生成。 此屬性與本地 AD DS 環境中物件的主使用者/組 SID 不匹配。 這種不匹配的原因是 Azure AD DS 託管域的 SID 命名空間與本地 AD DS 域不同。 |
| 使用者和群組的 SID 歷程記錄 | 內部部署主要使用者和群組 SID | Azure AD DS 中使用者和組的*Sid 歷史*屬性設置為與本地 AD DS 環境中的相應主使用者或組 SID 匹配。 此功能有助於使本地應用程式升降和移位到 Azure AD DS 變得更加容易，因為您不需要重新使用 ACL 資源。 |

> [!TIP]
> **使用 UPN 格式登錄到託管域***SAMAccountName*屬性（如`AADDSCONTOSO\driley`）可能是為 Azure AD DS 託管域中的某些使用者帳戶自動生成的。 使用者自動生成的*SAMAccountName*可能與其 UPN 首碼不同，因此並不總是一種可靠的登錄方式。
>
> 例如，如果多個使用者具有相同的*郵件昵稱*屬性或使用者具有過長的 UPN 首碼，則這些使用者的*SAMAccountName*可能是自動生成的。 使用 UPN 格式（如`driley@aaddscontoso.com`）可靠地登錄到 Azure AD DS 託管域。

### <a name="attribute-mapping-for-user-accounts"></a>使用者帳戶的屬性對應

下表說明了 Azure AD 中使用者物件的特定屬性如何與 Azure AD DS 中的相應屬性同步。

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
| mailNickname |SAMAccountName（有時可能自動生成） |
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

下表說明了 Azure AD 中組物件的特定屬性如何與 Azure AD DS 中的相應屬性同步。

| Azure AD 中的組屬性 | Azure AD DS 中的組屬性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName（有時可能自動生成） |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>從本地 AD DS 到 Azure AD 和 Azure AD DS 的同步

Azure AD 連接用於將使用者帳戶、組成員身份和憑據雜湊從本地 AD DS 環境同步到 Azure AD。 使用者帳戶的屬性（如 UPN 和本地安全識別碼 （SID） 是同步的。 要使用 Azure AD DS 登錄，NTLM 和 Kerberos 身份驗證所需的舊密碼雜湊也會同步到 Azure AD。

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在 Azure AD DS 受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

如果配置回寫，Azure AD 的更改將同步回本地 AD DS 環境。 例如，如果使用者使用 Azure AD 自助服務密碼管理更改其密碼，則密碼將更新回本地 AD DS 環境中。

> [!NOTE]
> 請一律使用最新版的 Azure AD Connect 版本，以確保您已具備所有已知問題的修正。

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>從多樹系內部部署環境同步處理

許多組織具有相當複雜的本地 AD DS 環境，其中包括多個林。 Azure AD 連接支援將使用者、組和憑據雜湊從多林環境同步到 Azure AD。

Azure AD 具有更簡單、更平坦的命名空間。 若要讓使用者可靠地存取受 Azure AD 保護的應用程式，請解決不同樹系中各個使用者帳戶之間的 UPN 衝突。 Azure AD DS 託管域使用平面 OU 結構，類似于 Azure AD。 所有使用者帳戶和組都存儲在*AADDC 使用者*容器中，儘管從不同的本地域或林同步，即使您在本地配置了分層 OU 結構也是如此。 Azure AD DS 託管域可平展任何分層 OU 結構。

如之前詳述的，Azure AD DS 沒有同步回 Azure AD。 您可以在 Azure AD DS 中[創建自訂群組織單位 （OU），](create-ou.md)然後在這些自訂 OU 中創建使用者、組或服務帳戶。 在自訂 O 中創建的物件都不會同步回 Azure AD。 這些物件僅在 Azure AD DS 託管域中可用，並且使用 Azure AD PowerShell Cmdlet、Microsoft 圖形 API 或使用 Azure AD 管理 UI 不可見。

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>未同步到 Azure AD DS 的內容

以下物件或屬性不會從本地 AD DS 環境同步到 Azure AD 或 Azure AD DS：

* **排除的屬性：** 您可以選擇使用 Azure AD 連接從本地 AD DS 環境中排除某些屬性從同步到 Azure AD。 這些排除的屬性在 Azure AD DS 中不可用。
* **群組原則：** 在本地 AD DS 環境中配置的群組原則不會同步到 Azure AD DS。
* **Sysvol 資料夾：** 本地 AD DS 環境中的*Sysvol*資料夾的內容未同步到 Azure AD DS。
* **電腦物件：** 連接到本地 AD DS 環境的電腦的電腦物件不會同步到 Azure AD DS。 這些電腦與 Azure AD DS 託管域沒有信任關係，並且僅屬於本地 AD DS 環境。 在 Azure AD DS 中，僅顯示已顯式加入託管域的電腦的電腦物件。
* **使用者和組的 Sid 歷史屬性：** 本地 AD DS 環境中的主使用者和主組 SID 同步到 Azure AD DS。 但是，使用者和組的現有*SidHistory*屬性不會從本地 AD DS 環境同步到 Azure AD DS。
* **組織單位 （OU） 結構：** 在本地 AD DS 環境中定義的組織單位不同步到 Azure AD DS。 Azure AD DS 中有兩個內置的 O，一個用於使用者，一個用於電腦。 Azure AD DS 託管域具有扁平的 OU 結構。 您可以選擇[在託管域中創建自訂 OU。](create-ou.md)

## <a name="password-hash-synchronization-and-security-considerations"></a>密碼雜湊同步處理和安全性考量

啟用 Azure AD DS 時，需要 NTLM 和 Kerberos 身份驗證的舊密碼雜湊。 Azure AD 不存儲純文字密碼，因此無法自動為現有使用者帳戶生成這些雜湊。 生成和存儲後，NTLM 和 Kerberos 相容的密碼雜湊始終以加密方式存儲在 Azure AD 中。

加密金鑰對於每個 Azure AD 租戶是唯一的。 這些雜湊是加密的，以便只有 Azure AD DS 才能訪問解密金鑰。 Azure AD 中沒有任何其他服務或元件具有解密金鑰的存取權。

然後，舊密碼雜湊從 Azure AD 同步到 Azure AD DS 託管域的網域控制站中。 Azure AD DS 中這些託管網域控制站的磁片在靜態時進行加密。 這些密碼雜湊存儲在這些網域控制站上並加以保護，類似于密碼在本地 AD DS 環境中的存儲和保護方式。

對於僅雲 Azure AD 環境，[使用者必須重置/更改其密碼](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便生成和存儲在 Azure AD 中所需的密碼雜湊。 針對在啟用 Azure AD Domain Services 之後於 Azure AD 中建立的任何雲端使用者帳戶，以 NTLM 和 Kerberos 相容的格式產生並儲存密碼雜湊。 這些新帳戶不需要重置或更改其密碼即可生成舊密碼雜湊。

對於使用 Azure AD Connect 從本地 AD DS 環境同步的混合使用者帳戶，必須[配置 Azure AD 連接以同步 NTLM 和 Kerberos 相容格式的密碼雜湊](tutorial-configure-password-hash-sync.md)。

## <a name="next-steps"></a>後續步驟

有關密碼同步細節的詳細資訊，請參閱[密碼雜湊同步如何與 Azure AD 連接配合](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)使用。

要開始使用 Azure AD DS，[請創建一個託管域](tutorial-create-instance.md)。
