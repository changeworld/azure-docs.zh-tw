---
title: Azure AD Domain Services 的管理概念 |Microsoft Docs
description: 瞭解如何管理 Azure Active Directory Domain Services 受控網域以及使用者帳戶和密碼的行為
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: joflore
ms.openlocfilehash: 95c6a1f24335849fb2d2c4de56b4ed60e8e5f73f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962814"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 中的使用者帳戶、密碼和管理的管理概念

當您建立和執行 Azure Active Directory Domain Services (AD DS) 受控網域時，相較于傳統內部部署 AD DS 環境的行為有一些差異。 您使用 Azure AD DS 中的相同系統管理工具作為自我管理的網域，但無法直接存取 (DC) 的網域控制站。 根據建立使用者帳戶的來源，密碼原則和密碼雜湊的行為也有一些差異。

本文將詳細說明如何根據建立受控網域和使用者帳戶的不同行為來管理它們。

## <a name="domain-management"></a>網域管理

受控網域是 DNS 命名空間和相符目錄。 在受控網域中，網域控制站 (Dc) ，其中包含所有資源（例如使用者和群組、認證和原則）是受管理服務的一部分。 針對冗余，會建立兩個 Dc 作為受控網域的一部分。 您無法登入這些 Dc 來執行管理工作。 相反地，您會建立已加入受控網域的管理 VM，然後安裝定期 AD DS 管理工具。 例如，您可以使用 Active Directory 管理中心或 Microsoft Management Console (MMC) 嵌入式管理單元（例如 DNS 或群組原則物件）。

## <a name="user-account-creation"></a>建立使用者帳戶

您可以透過多種方式在受控網域中建立使用者帳戶。 大部分的使用者帳戶都會從 Azure AD 同步處理，也可以包含從內部部署 AD DS 環境同步處理的使用者帳戶。 您也可以直接在受控網域中手動建立帳戶。 某些功能（例如，初始密碼同步化或密碼原則）會根據使用者帳戶的建立方式和位置而有不同的行為。

* 您可以從 Azure AD 同步處理使用者帳戶。 這包括直接在 Azure AD 中建立的僅限雲端使用者帳戶，以及使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的混合式使用者帳戶。
    * 受控網域中大部分的使用者帳戶是透過 Azure AD 的同步處理常式所建立。
* 您可以在受控網域中手動建立使用者帳戶，而且不存在於 Azure AD 中。
    * 如果您需要為只在受控網域中執行的應用程式建立服務帳戶，您可以在受控網域中手動建立它們。 由於同步處理是 Azure AD 的其中一種方式，因此在受控網域中建立的使用者帳戶不會同步回到 Azure AD。

## <a name="password-policy"></a>密碼原則

Azure AD DS 包含預設的密碼原則，可定義帳戶鎖定、密碼最長使用期限和密碼複雜性等專案的設定。 帳戶鎖定原則等設定會套用至受控網域中的所有使用者，而不論上一節所述的使用者建立方式為何。 幾項設定，例如密碼長度下限和密碼複雜度，只會套用至直接在受控網域中建立的使用者。

您可以建立自己的自訂密碼原則，以覆寫受控網域中的預設原則。 然後，您可以視需要將這些自訂原則套用至特定的使用者群組。

如需有關如何根據使用者建立的來源來套用密碼原則差異的詳細資訊，請參閱 [受控網域上的密碼和帳戶鎖定原則][password-policy]。

## <a name="password-hashes"></a>密碼雜湊

若要在受控網域上驗證使用者，Azure AD DS 需要其格式適用 NT LAN Manager (NTLM) 和 Kerberos 驗證的密碼雜湊。 除非您針對租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式產生或儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

針對僅限雲端的使用者帳戶，使用者必須先變更其密碼，才能使用受控網域。 此密碼變更程序會在 Azure AD 中產生並儲存 Kerberos 和 NTLM 驗證的密碼雜湊。 在密碼變更前，帳戶不會從 Azure AD 同步處理到 Azure AD DS。

針對使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的使用者，請 [啟用密碼雜湊的同步][hybrid-phs]處理。

> [!IMPORTANT]
> 當您為 Azure AD 租使用者啟用 Azure AD DS 時，Azure AD Connect 只會同步處理舊版密碼雜湊。 如果您只使用 Azure AD Connect 來同步處理內部部署 AD DS 環境與 Azure AD，則不會使用舊版密碼雜湊。
>
> 如果您的繼承應用程式未使用 NTLM 驗證或 LDAP 簡單系結，我們建議您針對 Azure AD DS 停用 NTLM 密碼雜湊同步處理。 如需詳細資訊，請參閱 [停用弱式加密套件和 NTLM 認證雜湊同步][secure-domain]處理。

正確設定後，可用的密碼雜湊就會儲存在受控網域中。 如果您刪除受控網域，當時已儲存的任何密碼雜湊也會一併刪除。 如果您稍後建立另一個受控網域，Azure AD 中的同步認證資訊無法重複使用-您必須重新設定密碼雜湊同步處理，才能再次儲存密碼雜湊。 先前加入網域的 VM 或使用者將無法立即進行驗證 - Azure AD 需要在新的受控網域中產生密碼雜湊並儲存。 如需詳細資訊，請參閱 [Azure AD DS 和 Azure AD Connect 的密碼雜湊同步程序][azure-ad-password-sync]。

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在此受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

## <a name="forests-and-trusts"></a>樹系和信任

「樹系」是 Active Directory Domain Services (AD DS) 用以分組一或多個「網域」的邏輯建構。 然後，這些網域就會儲存使用者或群組的物件，並提供驗證服務。

在 Azure AD DS 中，樹系僅包含一個網域。 但內部部署 AD DS 樹系通常會包含許多網域。 在大型組織中，特別是併購後，會有多個內部部署樹系，每個樹系又包含多個網域。

依預設，受控網域會建立為 *使用者* 樹系。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 使用者帳戶可以直接對受控網域進行驗證，例如登入已加入網域的 VM。 當可同步處理密碼雜湊，但使用者不使用獨佔式登入方法 (例如智慧卡驗證) 時，使用者樹系即可發揮功能。

在 Azure AD DS 的「資源」樹系中，使用者會透過其內部部署 AD DS 的單向樹系「信任」驗證。 使用此方法，使用者物件和密碼雜湊不會同步處理至 Azure AD DS。 使用者物件和認證僅存在於內部部署 AD DS。 這種方法可讓企業裝載 Azure 中依賴 LDAPS、Kerberos 或 NTLM 等傳統驗證的資源和應用程式平台，但會移除所有驗證問題或顧慮。

如需有關 Azure AD DS 中的樹系類型的詳細資訊，請參閱 [什麼是資源][concepts-forest] 樹系？以及 [樹系信任在 Azure AD DS 中的運作方式為何？][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS Sku

在 Azure AD DS 中，可用的效能和功能是以 SKU 為基礎。 當您建立受控網域時，您會選取一個 SKU，而您可以在部署受控網域之後，將 Sku 切換為您的商務需求變更。 下表列出可用的 Sku 以及兩者之間的差異：

| SKU 名稱   | 最大物件計數 | 備份頻率 | 輸出樹系信任的最大數目 |
|------------|----------------------|------------------|----|
| 標準   | 無限制            | 每7天     | 0  |
| Enterprise | 無限制            | 每3天     | 5  |
| 進階    | 無限制            | 每日            | 10 |

在這些 Azure AD DS Sku 之前，會使用根據受控網域中) 的物件數目 (的計費模型。 根據受控網域中的物件數目，不再有變化的定價。

如需詳細資訊，請參閱 [AZURE AD DS 定價頁面][pricing]。

### <a name="managed-domain-performance"></a>受控網域效能

網域效能會根據應用程式的驗證執行方式而有所不同。 額外的計算資源可能有助於改善查詢回應時間，並減少花費在同步處理作業的時間。 當 SKU 層級增加時，可供受控網域使用的計算資源會增加。 監視應用程式的效能，並規劃所需的資源。

如果您的商務或應用程式需求變更，而且您需要受控網域的額外計算能力，您可以切換到不同的 SKU。

### <a name="backup-frequency"></a>備份頻率

備份頻率決定了受控網域快照集的執行頻率。 備份是 Azure 平臺所管理的自動化流程。 如果您的受控網域發生問題，Azure 支援可以協助您從備份還原。 因為同步處理只會 *從* Azure AD 中執行一次，所以受控網域中的任何問題都不會影響 Azure AD 或內部部署 AD DS 環境和功能。

當 SKU 層級增加時，這些備份快照集的頻率也會增加。 請參閱您的商務需求和復原點目標 (RPO) 來判斷受控網域所需的備份頻率。 如果您的商務或應用程式需求變更，而您需要更頻繁的備份，您可以切換至不同的 SKU。

### <a name="outbound-forest-trusts"></a>輸出樹系信任

上一節詳細說明的單向輸出樹系信任從受控網域到內部部署 AD DS 環境。 SKU 會決定您可以為受控網域建立的樹系信任數目上限。 檢查您的商務和應用程式需求，以判斷您真正需要多少信任，並挑選適當的 Azure AD DS SKU。 同樣地，如果您的業務需求變更，而您需要建立額外的樹系信任，則可以切換至不同的 SKU。

## <a name="next-steps"></a>後續步驟

若要開始使用，請 [建立 AZURE AD DS 受控網域][create-instance]。

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
