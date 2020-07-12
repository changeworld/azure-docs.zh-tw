---
title: Azure 受控磁碟 - PowerShell 的伺服器端加密
description: Azure 儲存體可以先加密待用資料來保護您的資料，然後再將資料保存在儲存體叢集。 您可以依賴 Microsoft 管理的金鑰來加密您的受控磁碟，或使用客戶管理的金鑰，以利用您自己的金鑰來管理加密。
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0bb0fb268d18ddc152dae45014e2154686762976
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259819"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure 磁碟儲存體的伺服器端加密

伺服器端加密 (SSE) 可保護您的資料安全，並協助您符合組織安全性和合規性承諾。 SSE 會自動將儲存在 Azure 受控磁片上的資料加密 (OS 和資料磁片，在將它保存到雲端時，預設) 待用。 

Azure 受控磁碟中的資料會使用 256 位元的 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (可用的最強區塊編碼器之一)，以透明的方式進行加密，而且符合 FIPS 140-2 規範。 如需有關基礎 Azure 受控磁碟的加密模組詳細資訊，請參閱[密碼編譯 API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

伺服器端加密不會影響受控磁片的效能，也不會產生額外的費用。 

> [!NOTE]
> 暫存磁片不是受控磁片，而且不是由 SSE 加密，除非您在主機上啟用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

您可以依賴由平台管理的金鑰來加密受控磁碟，也可以使用您自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您可以指定*客戶管理的金鑰*，以用於加密和解密受控磁碟中的所有資料。 

下列各節將詳細說明金鑰管理的每個選項。

### <a name="platform-managed-keys"></a>平台管理的金鑰

根據預設，受控磁碟會使用平台管理的加密金鑰。 從 2017 年 6 月 10 日開始，所有新的受控磁碟、快照集、映像和寫入至現有受控磁碟的新資料都會使用平台管理的金鑰自動加密待用資料。

### <a name="customer-managed-keys"></a>客戶管理的金鑰

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>限制

目前，客戶管理的金鑰具有下列限制：

- 如果您的磁碟已啟用此功能，則無法停用。
    如果您需要解決此問題，必須[將所有資料複製到](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)完全不同且未使用客戶管理的金鑰的受控磁碟。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>主機上的加密-VM 資料的端對端加密

端對端加密會從 VM 主機開始，也就是您 VM 所配置的 Azure 伺服器。 暫存磁片上的資料和 OS/資料磁碟快取會儲存在該 VM 主機上。 當您啟用端對端加密時，所有的資料都會在待用時加密，並加密到儲存體服務的流量（保存時）。 端對端加密不會使用您的 VM CPU，也不會影響 VM 的效能。 

當您啟用端對端加密時，暫存磁片會以平臺管理的金鑰進行待用加密。 OS 和資料磁碟快取會使用客戶管理或平臺管理的金鑰進行待用加密，視加密類型而定。 例如，如果使用客戶管理的金鑰來加密磁片，則磁片的快取會使用客戶管理的金鑰進行加密，如果磁片使用平臺管理的金鑰進行加密，則磁片的快取會使用平臺管理的金鑰進行加密。

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>支援的區域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>支援的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>靜態加密

擔心與任何特定加密演算法、執行或金鑰相關聯之風險的高安全性敏感性客戶，現在可以選擇使用平臺管理的加密金鑰，在基礎結構層使用不同的加密演算法/模式來進行額外的加密層級。 這個新的層級可以套用到磁片、快照集和映射，這些都是使用雙重加密進行待用加密。

### <a name="supported-regions"></a>支援的區域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>伺服器端加密與 Azure 磁碟加密

[Azure 磁碟加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)會利用 Windows 的 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 功能，在來賓 VM 中使用客戶管理的金鑰來加密受控磁碟。  使用客戶管理的金鑰的伺服器端加密改善了 ADE 的效能，讓您可藉由加密儲存庫服務中的資料。為您的 VM 使用任何作業系統類型和映像。

> [!IMPORTANT]
> 客戶管理的金鑰須依賴 Azure 資源的受控識別 (Azure Active Directory (Azure AD) 的一項功能)。 當您設定客戶管理的金鑰時，受控識別會在幕後自動指派給您的資源。 如果您之後將訂用帳戶、資源群組或受控磁碟從一個 Azure AD 目錄移至另一個目錄，與受控磁碟相關聯的受控識別不會移轉至新的租用戶，因此，客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱[在 Azure AD 目錄之間移轉訂用帳戶](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。


## <a name="next-steps"></a>後續步驟

- [在主機上使用加密來啟用端對端加密-PowerShell](disks-enable-host-based-encryption-powershell.md)
- [針對您的受控磁片啟用靜態加密-PowerShell](disks-enable-double-encryption-at-rest-powershell.md)
- [為受控磁片啟用客戶管理的金鑰-PowerShell](disks-enable-customer-managed-keys-powershell.md)
- [為受控磁片啟用客戶管理的金鑰-入口網站](disks-enable-customer-managed-keys-portal.md)
- [探索 Azure Resource Manager 範本，以使用客戶管理的金鑰來建立加密的磁碟](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)
