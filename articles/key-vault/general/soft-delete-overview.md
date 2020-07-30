---
title: Azure Key Vault 虛刪除 |Microsoft Docs
description: Azure Key Vault 中的虛刪除可讓您復原已刪除的金鑰保存庫和金鑰保存庫物件，例如金鑰、秘密和憑證。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 1affa396407ba9804261c799b559e40928b9b1fa
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388362"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault 虛刪除概觀

> [!IMPORTANT]
> 您必須立即在金鑰保存庫上啟用虛刪除。 退出宣告虛刪除的功能將會在一年結束後淘汰，而且會自動為所有金鑰保存庫開啟虛刪除保護。  請參閱[這裡](soft-delete-change.md)的完整詳細資料

Key Vault 的虛刪除功能可復原已刪除的保存庫，並刪除金鑰保存庫物件（例如金鑰、秘密、憑證），又稱為虛刪除。 具體而言，我們會解決下列情況：這項保護提供下列保護：

- 一旦刪除了秘密、金鑰、憑證或金鑰保存庫，就會繼續進行可設定的7到90日曆天時間。 如果未指定任何設定，預設復原期間將會設為90天。 這可讓使用者有足夠的時間注意到意外的秘密刪除和回應。
- 若要永久刪除秘密，必須執行兩個作業。 首先，使用者必須刪除物件，使其進入虛刪除狀態。 第二，使用者必須清除虛刪除狀態中的物件。 清除作業需要額外的存取原則許可權。 這些額外的保護會降低使用者意外或惡意刪除秘密或金鑰保存庫的風險。  
- 若要清除虛刪除狀態中的密碼，服務主體必須被授與額外的「清除」存取原則許可權。 預設不會將清除存取原則許可權授與任何服務主體，包括金鑰保存庫和訂用帳戶擁有者，而且必須刻意設定。 藉由要求更高的存取原則許可權來清除虛刪除的秘密，它會降低不小心刪除秘密的機率。

## <a name="supporting-interfaces"></a>支援的介面

虛刪除功能最初是透過[REST](/rest/api/keyvault/)、 [CLI](soft-delete-cli.md)、 [PowerShell](soft-delete-powershell.md)和[.Net/c #](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)介面，以及[ARM 範本](https://docs.microsoft.com/azure/templates/microsoft.keyvault/2019-09-01/vaults)提供。

## <a name="scenarios"></a>案例

Azure Key Vault 是由 Azure Resource Manager 管理的追蹤資源。 Azure Resource Manager 也會指定妥善定義的刪除行為，而成功的「刪除」作業必須使資源無法再被存取。 虛刪除功能可復原已刪除的物件，無論是無意或有意刪除的。

1. 在常見的案例中，使用者可能不小心刪除 Key Vault 或 Key Vault 物件；如果該 Key Vault 或 Key Vault 物件在預定期限內是可復原的，使用者可以還原刪除作業並復原其資料。

2. 而在其他案例中，惡意使用者可能會嘗試刪除 Key Vault 或 Key Vault 物件 (例如保存庫內的金鑰) 而使業務中斷。 將 Key Vault 或 Key Vault 物件的刪除與基礎資料的實際刪除做區隔，可做為一種安全措施，例如，將刪除資料的權限限制為其他信任的角色。 實際上，此方法這需要作業仲裁，否則可能導致直接的資料遺失。

### <a name="soft-delete-behavior"></a>虛刪除行為

啟用虛刪除時，標示為已刪除資源的資源會保留一段指定的時間（預設為90天）。 此服務進一步提供復原已刪除物件的機制 (基本上是復原刪除作業)。

建立新的金鑰保存庫時，預設會開啟虛刪除。 您可以建立金鑰保存庫，而不需要透過[Azure CLI](soft-delete-cli.md)或[Azure PowerShell](soft-delete-powershell.md)進行虛刪除。 一旦在金鑰保存庫上啟用虛刪除，即無法停用

預設的保留期限為90天，但在建立金鑰保存庫期間，您可以透過 Azure 入口網站，將保留原則間隔設定為從7到90天的值。 清除保護保留原則會使用相同的間隔。 保留原則間隔一經設定即無法變更。

在達到保留期限之前，您無法重複使用已虛刪除的金鑰保存庫名稱。

### <a name="purge-protection"></a>清除保護 

[清除保護] 是選擇性的 Key Vault 行為，**預設不會啟用**。 只有啟用虛刪除之後，才能啟用清除保護。  您可以透過[CLI](soft-delete-cli.md#enabling-purge-protection)或[PowerShell](soft-delete-powershell.md#enabling-purge-protection)來開啟它。

開啟 [清除保護] 時，除非已達到保留期限，否則無法清除 [已刪除] 狀態的保存庫或物件。 虛刪除的保存庫和物件仍然可以復原，確保會遵循保留原則。 

預設的保留期限為90天，但可以透過 Azure 入口網站將保留原則間隔設定為從7到90天的值。 一旦設定保留原則間隔並加以儲存，就無法對該保存庫進行變更。 

### <a name="permitted-purge"></a>允許的清除作業

在 Proxy 資源上可透過 POST 作業永久刪除、清除 Key Vault，而這需要特殊權限。 一般而言，只有訂用帳戶擁有者可以清除 Key Vault。 POST 作業會對該保存庫觸發立即性且無法復原的刪除作業。 

例外狀況為：
- 當 Azure 訂用帳戶已標示為*undeletable*時。 在此情況下，只有此服務可接著執行實際的刪除作業，而且會以排程的程序執行。 
- 在保存 `--enable-purge-protection flag` 庫本身上啟用時。 在此情況下，Key Vault 會從原始祕密物件標示為要刪除的那天算起，等待 90 天後才永久刪除該物件。

### <a name="key-vault-recovery"></a>Key Vault 復原

一旦刪除 Key Vault，此服務會在訂用帳戶下建立 Proxy 資源，以新增足夠使用於復原的中繼資料。 Proxy 資源是預存物件，和刪除的 Key Vault 位於相同位置。 

### <a name="key-vault-object-recovery"></a>Key Vault 物件復原

刪除金鑰保存庫物件（例如金鑰）時，服務會將物件置於已刪除狀態，使其無法存取任何抓取作業。 此狀態下的 Key Vault 物件只能列出、復原或強制/永久刪除。 若要查看物件，請使用 Azure CLI `az keyvault key list-deleted` 命令（如如何搭配[CLI 使用 Key Vault 虛刪除](soft-delete-cli.md)所述），或 Azure PowerShell `-InRemovedState` 參數（如[如何搭配使用 Key Vault 虛刪除與 PowerShell](soft-delete-powershell.md#secrets)中所述）。  

同時，Key Vault 會根據刪除的 Key Vault 或 Key Vault 物件來排程基礎資料的刪除，並在預定的保留間隔後執行。 在保留間隔期間，也會保留與保存庫相對應的 DNS 記錄。

### <a name="soft-delete-retention-period"></a>虛刪除保留期限

虛刪除的資源會保留一段設定的時間90天。 在虛刪除保留間隔期限內，下列說明是成立的：

- 您可以列出您的訂用帳戶下狀態是虛刪除的所有 Key Vault 和 Key Vault 物件，也能存取與它們相關的刪除和復原資訊。
    - 只有具備特殊權限的使用者可以列出已刪除的保存庫。 我們建議使用者建立具有這些特殊權限的自訂角色，以處理刪除的保存庫。
- 不能在同一個位置建立同名的 Key Vault；同樣地，若指定的 Key Vault 中含有同名且是已刪除狀態的物件，就無法在其中建立該 Key Vault 物件。 
- 只有具備特殊權限的使用者，可以在對應的 Proxy 資源上發出復原命令來還原 Key Vault 或 Key Vault 物件。
    - 身為自訂角色成員 (這個角色有權在資源群組下建立 Key Vault) 的使用者，可以還原保存庫。
- 只有具備特殊權限的使用者，可以在對應的 Proxy 資源上發出刪除命令來強制刪除 Key Vault 或 Key Vault 物件。

除非 Key Vault 或 Key Vault 物件已復原，否則在保留間隔結束時，此服務會對虛刪除的 Key Vault 或 Key Vault 物件及其內容執行清除作業。 資源刪除作業無法重新排程。

### <a name="billing-implications"></a>計費影響

一般情況下，當物件 (金鑰保存庫或金鑰或密碼) 處於已刪除狀態時，可執行的作業只有兩種：「清除」與「復原」。 其他所有作業都會失敗。 因此，即使物件存在，但因為無法執行任何作業，所以不會有使用量發生，當然也就不會有費用。 然而，以下例外狀況不適用：

- 「清除」與「復原」動作都算是一般金鑰保存庫作業，因此將予以計費。
- 如果物件是 HSM 金鑰，而且金鑰版本在過去 30 天內曾經使用過，我們將會針對每個金鑰版本收取「HSM 保護的金鑰」費用的月費。 後續由於物件處於已刪除狀態，沒有可執行的作業，因此我們將不收費。

## <a name="next-steps"></a>後續步驟

下列兩個指南提供使用虛刪除的主要使用方式案例。

- [如何使用 Key Vault 虛刪除與 PowerShell](soft-delete-powershell.md) 
- [如何以 CLI 使用金鑰保存庫虛刪除](soft-delete-cli.md)

