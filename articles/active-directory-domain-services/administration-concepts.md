---
title: Azure AD Domain Services 的管理概念 |Microsoft Docs
description: 瞭解如何管理 Azure Active Directory Domain Services 受控網域，以及使用者帳戶和密碼的行為
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: iainfou
ms.openlocfilehash: 4f1f6c60ef2e0ccdd3e166e2272fe917ead3ed2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735042"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 中的使用者帳戶、密碼和管理的管理概念

當您建立並執行 Azure Active Directory Domain Services （AD DS）受控網域時，相較于傳統內部部署 AD DS 環境，其行為會有一些差異。 您使用 Azure AD DS 中的相同系統管理工具作為自我管理的網域，但是您無法直接存取網域控制站（DC）。 根據使用者帳戶建立的來源而定，密碼原則和密碼雜湊的行為也有一些差異。

此概念性文章會詳細說明如何管理受控網域，以及使用者帳戶的不同行為（視其建立方式而定）。

## <a name="domain-management"></a>網域管理

受控網域是 DNS 命名空間和相符目錄。 在受控網域中，包含所有資源（例如使用者和群組、認證和原則）的網域控制站（Dc）是受控服務的一部分。 針對冗余，會建立兩個 Dc 做為受控網域的一部分。 您無法登入這些 Dc 來執行管理工作。 相反地，您會建立已加入受控網域的管理 VM，然後安裝一般的 AD DS 管理工具。 例如，您可以使用 Active Directory 管理中心或 Microsoft Management Console （MMC）嵌入式管理單元（如 DNS）或群組原則物件。

## <a name="user-account-creation"></a>建立使用者帳戶

您可以使用多種方式，在受控網域中建立使用者帳戶。 大部分的使用者帳戶會在中從 Azure AD 同步處理，也可以包含從內部部署 AD DS 環境同步處理的使用者帳戶。 您也可以直接在受控網域中手動建立帳戶。 某些功能（例如初始密碼同步處理或密碼原則）的行為會根據建立使用者帳戶的方式和位置而有所不同。

* 使用者帳戶可以從 Azure AD 同步處理。 這包括直接在 Azure AD 中建立的僅限雲端使用者帳戶，以及使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的混合式使用者帳戶。
    * 受控網域中的大部分使用者帳戶都是透過 Azure AD 的同步處理常式來建立。
* 使用者帳戶可以手動建立在受控網域中，而且不存在於 Azure AD 中。
    * 如果您需要為只在受控網域中執行的應用程式建立服務帳戶，您可以在受控網域中手動建立它們。 由於同步處理是從 Azure AD 的一種方式，因此在受控網域中建立的使用者帳戶不會同步處理回 Azure AD。

## <a name="password-policy"></a>密碼原則

Azure AD DS 包含預設密碼原則，可定義帳戶鎖定、密碼最長使用期限和密碼複雜度等專案的設定。 帳戶鎖定原則之類的設定適用于受控網域中的所有使用者，而不論上一節中所述的使用者建立方式為何。 一些設定（例如最小密碼長度和密碼複雜度）僅適用于直接在受控網域中建立的使用者。

您可以建立自己的自訂密碼原則，以覆寫受控網域中的預設原則。 然後，這些自訂原則就可以視需要套用到特定的使用者群組。

如需有關如何根據使用者建立的來源套用密碼原則之差異的詳細資訊，請參閱[受控網域上的密碼和帳戶鎖定原則][password-policy]。

## <a name="password-hashes"></a>密碼雜湊

若要在受控網域上驗證使用者，Azure AD DS 需要其格式適用 NT LAN Manager (NTLM) 和 Kerberos 驗證的密碼雜湊。 除非您針對租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式產生或儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

對於僅限雲端的使用者帳戶，使用者必須變更其密碼，才能使用受控網域。 此密碼變更程序會在 Azure AD 中產生並儲存 Kerberos 和 NTLM 驗證的密碼雜湊。 在密碼變更前，帳戶不會從 Azure AD 同步處理到 Azure AD DS。

針對使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的使用者，[啟用密碼雜湊的同步][hybrid-phs]處理。

> [!IMPORTANT]
> Azure AD Connect 只有在為 Azure AD 租使用者啟用 Azure AD DS 時，才會同步處理舊版密碼雜湊。 如果您只使用 Azure AD Connect 來同步處理內部部署 AD DS 環境與 Azure AD，則不會使用舊版密碼雜湊。
>
> 如果您的繼承應用程式未使用 NTLM 驗證或 LDAP 簡單系結，我們建議您停用 Azure AD DS 的 NTLM 密碼雜湊同步處理。 如需詳細資訊，請參閱[停用弱式加密套件和 NTLM 認證雜湊同步][secure-domain]處理。

正確設定後，可用的密碼雜湊就會儲存在受控網域中。 如果您刪除受控網域，當時已儲存的任何密碼雜湊也會一併刪除。 如果您稍後再建立另一個受控網域，則 Azure AD 中同步處理的認證資訊無法重複使用-您必須重新設定密碼雜湊同步處理，才能再次儲存密碼雜湊。 先前加入網域的 VM 或使用者將無法立即進行驗證 - Azure AD 需要在新的受控網域中產生密碼雜湊並儲存。 如需詳細資訊，請參閱 [Azure AD DS 和 Azure AD Connect 的密碼雜湊同步程序][azure-ad-password-sync]。

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在此受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

## <a name="forests-and-trusts"></a>樹系和信任

「樹系」是 Active Directory Domain Services (AD DS) 用以分組一或多個「網域」的邏輯建構。 然後，這些網域就會儲存使用者或群組的物件，並提供驗證服務。

在 Azure AD DS 中，樹系僅包含一個網域。 但內部部署 AD DS 樹系通常會包含許多網域。 在大型組織中，特別是併購後，會有多個內部部署樹系，每個樹系又包含多個網域。

根據預設，受控網域會建立為*使用者*樹系。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 使用者帳戶可以直接對受控網域進行驗證，例如登入已加入網域的 VM。 當可同步處理密碼雜湊，但使用者不使用獨佔式登入方法 (例如智慧卡驗證) 時，使用者樹系即可發揮功能。

在 Azure AD DS 的「資源」樹系中，使用者會透過其內部部署 AD DS 的單向樹系「信任」驗證。 使用此方法，使用者物件和密碼雜湊不會同步處理至 Azure AD DS。 使用者物件和認證僅存在於內部部署 AD DS。 這種方法可讓企業裝載 Azure 中依賴 LDAPS、Kerberos 或 NTLM 等傳統驗證的資源和應用程式平台，但會移除所有驗證問題或顧慮。 Azure AD DS 資源樹系目前為預覽狀態。

如需有關 Azure AD DS 中樹系類型的詳細資訊，請參閱[什麼是資源][concepts-forest]樹系？和[樹系信任在 Azure AD DS 中如何運作？][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS Sku

在 Azure AD DS 中，可用的效能和功能是以 SKU 為基礎。 當您建立受控網域時，您會選取 SKU，而且您可以在部署受控網域之後，隨著業務需求變更來切換 Sku。 下表列出可用的 Sku 和兩者之間的差異：

| SKU 名稱   | 物件計數上限 | 備份頻率 | 輸出樹系信任的最大數目 |
|------------|----------------------|------------------|----|
| 標準   | 無限制            | 每7天     | 0  |
| Enterprise | 無限制            | 每3天     | 5  |
| Premium    | 無限制            | 每日            | 10 |

在這些 Azure AD DS Sku 之前，會使用以受控網域中的物件（使用者和電腦帳戶）數目為基礎的計費模型。 根據受控網域中的物件數目，不再有可變的定價。

如需詳細資訊，請參閱[AZURE AD DS 定價頁面][pricing]。

### <a name="managed-domain-performance"></a>受控網域效能

網域效能會根據應用程式的驗證方式而有所不同。 額外的計算資源可能有助於改善查詢回應時間，並縮短同步作業所花費的時間。 隨著 SKU 層級的增加，適用于受控網域的計算資源也會增加。 監視應用程式的效能，並規劃所需的資源。

如果您的商務或應用程式需求變更，而您的受控網域需要額外的計算能力，您可以切換到不同的 SKU。

### <a name="backup-frequency"></a>備份頻率

備份頻率會決定採用受控網域的快照集的頻率。 備份是由 Azure 平臺管理的自動化程式。 如果您的受控網域發生問題，Azure 支援可協助您從備份進行還原。 由於同步處理只會*從*Azure AD 進行一次，因此受控網域中的任何問題都不會影響 Azure AD 或內部部署 AD DS 環境和功能。

隨著 SKU 層級的增加，這些備份快照的頻率也會增加。 請檢查您的業務需求和復原點目標（RPO），以判斷受控網域所需的備份頻率。 如果您的商務或應用程式需求變更，而您需要更頻繁的備份，您可以切換到不同的 SKU。

### <a name="outbound-forest-trusts"></a>輸出樹系信任

上一節詳述的單向輸出樹系信任從受控網域到內部部署 AD DS 環境（目前處於預覽階段）。 SKU 會決定您可以為受控網域建立的樹系信任數目上限。 請檢查您的商務和應用程式需求，以判斷您實際需要多少信任，並挑選適當的 Azure AD DS SKU。 同樣地，如果您的業務需求變更，而您需要建立額外的樹系信任，您可以切換到不同的 SKU。

## <a name="next-steps"></a>後續步驟

若要開始使用，請[建立 AZURE AD DS 受控網域][create-instance]。

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
