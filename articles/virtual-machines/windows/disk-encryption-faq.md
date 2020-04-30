---
title: 常見問題-適用于 Windows Vm 的 Azure 磁碟加密
description: 本文提供有關 Windows IaaS Vm Microsoft Azure 磁片加密常見問題的解答。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: b71384e0a42af5481af7b17b91cd0b1d0ed82ee8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082589"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Windows 虛擬機器的 Azure 磁碟加密常見問題

本文提供有關 Windows Vm Azure 磁碟加密的常見問題（FAQ）的解答。 如需此服務的詳細資訊，請參閱[Azure 磁碟加密總覽](disk-encryption-overview.md)。

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Windows Vm 的 Azure 磁碟加密是什麼？

適用于 Windows Vm 的 Azure 磁碟加密會使用 Windows 的 Bitlocker 功能來提供作業系統磁片和資料磁片的完整磁片加密。 此外，當[VolumeType 參數為 All](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk)時，它會提供暫時資源磁片的加密。  從 VM 加密到儲存後端的內容流程。 因此，會使用客戶管理的金鑰提供端對端加密。
 
請參閱[支援的 vm 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)。
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure 磁碟加密會在那裡正式運作 (GA)？

Azure 磁碟加密已在所有 Azure 公用區域正式推出。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁碟加密有哪些使用者體驗？

Azure 磁碟加密 GA 支援 Azure Resource Manager 範本、Azure PowerShell 和 Azure CLI。 不同的使用者體驗可為您賦予彈性。 您有三個不同的選項可為您的 Vm 啟用磁片加密。 如需 Azure 磁碟加密中可用之使用者體驗和逐步指引的詳細資訊，請參閱[適用于 Windows 的 Azure 磁碟加密案例](disk-encryption-windows.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁碟加密如何收費？

使用 Azure 磁碟加密加密 VM 磁片不需付費，但使用 Azure Key Vault 時，會產生相關費用。 如需 Azure Key Vault 成本的詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)頁面。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何開始使用 Azure 磁碟加密？

若要開始使用，請參閱 [Azure 磁碟加密概觀](disk-encryption-overview.md)。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>哪些 VM 大小和作業系統支援 Azure 磁碟加密？

[Azure 磁碟加密總覽](disk-encryption-overview.md)文章列出支援 Azure 磁碟加密的[Vm 大小](disk-encryption-overview.md#supported-vms)和[vm 作業系統](disk-encryption-overview.md#supported-operating-systems)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密來加密開機和資料磁碟區？

您可以加密開機和資料磁片區，但不需要先將 OS 磁片區加密，就無法加密資料。

在您將 OS 磁片區加密之後，就不支援在 OS 磁片區上停用加密。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>我可以使用 Azure 磁碟加密加密卸載的磁片區嗎？

否，Azure 磁碟加密只會加密已掛接的磁片區。

## <a name="what-is-storage-server-side-encryption"></a>何謂存放裝置伺服器端加密？

儲存體伺服器端加密會加密 Azure 儲存體中的 Azure 受控磁片。 受控磁片預設會使用伺服器端加密與平臺管理的金鑰（自2017年6月10日起）進行加密。 您可以藉由指定客戶管理的金鑰，使用您自己的金鑰來管理受控磁片的加密。 如需詳細資訊，請參閱[Azure 受控磁片的伺服器端加密](disk-encryption.md)。
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Azure 磁碟加密與使用客戶管理的金鑰的儲存體伺服器端加密有何不同，以及何時應該使用每個解決方案？

Azure 磁碟加密使用客戶管理的金鑰，提供 OS 磁片、資料磁片和暫時資源磁片的端對端加密。

- 如果您的需求包括加密上述所有和端對端加密，請使用 Azure 磁碟加密。 
- 如果您的需求包括僅使用客戶管理的金鑰來加密待用資料，則使用[伺服器端加密搭配客戶管理的金鑰](disk-encryption.md)。 您無法使用客戶管理的金鑰，以 Azure 磁碟加密和儲存體伺服器端加密來加密磁片。
- 如果您使用的是在[不支援的 Windows 案例](disk-encryption-windows.md#unsupported-scenarios)中呼叫的案例，請考慮[使用客戶管理的金鑰來加密伺服器端](disk-encryption.md)。 
- 如果貴組織的原則可讓您使用 Azure 管理的金鑰來加密待用內容，則不需要採取任何動作-內容預設會加密。 對於受控磁片，儲存體中的內容預設會以平臺管理金鑰的伺服器端加密進行加密。 金鑰是由 Azure 儲存體服務所管理。 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何? 輪替秘密或加密金鑰？

若要輪替秘密，只要呼叫您原先用來啟用磁片加密的相同命令，並指定不同的 Key Vault。 若要輪替金鑰加密金鑰，請呼叫您原先用來啟用磁片加密的相同命令，並指定新的金鑰加密。 

>[!WARNING]
> - 如果您先前已使用[Azure 磁碟加密搭配 Azure AD 應用程式](disk-encryption-windows-aad.md)，請指定 Azure AD 認證來加密此 vm，則必須繼續使用此選項來加密您的 vm。 您無法在此加密的 VM 上使用 Azure 磁碟加密，因為這不是支援的案例，亦即不支援從 AAD 應用程式針對此加密 VM 進行切換。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如果我原本未使用金鑰加密金鑰，如何? 新增或移除它？

若要新增金鑰加密金鑰，請再次呼叫 enable 命令，並傳遞金鑰加密金鑰參數。 若要移除金鑰加密金鑰，請在不使用金鑰加密金鑰參數的情況下，再次呼叫 enable 命令。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁碟加密可讓您使用攜帶自己的金鑰 (BYOK) 嗎？

是，您可以提供自己的金鑰加密金鑰。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 建立的金鑰加密金鑰？

是，您可以使用 Azure Key Vault 來產生金鑰加密金鑰以供 Azure 磁碟加密使用。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用內部部署金鑰管理服務或 HSM 來保護加密金鑰？

您無法使用內部部署金鑰管理服務或 HSM 來保護 Azure 磁碟加密的加密金鑰。 只能使用 Azure Key Vault 服務來保護加密金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫一](disk-encryption-key-vault.md)文，以建立新的金鑰保存庫，或設定現有的金鑰保存庫以進行磁片加密存取以啟用加密，以及保護秘密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 應用程式 (舊版) 來設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[具有 Azure AD](disk-encryption-windows-aad.md)內容的 Azure 磁碟加密，以建立 Azure Active Directory 應用程式、建立新的金鑰保存庫，或設定現有的金鑰保存庫以進行磁片加密存取，以啟用加密並保護秘密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定具有 Azure AD 之 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault-aad.md)。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍支援使用 Azure AD 應用程式 (舊版) 來進行 Azure 磁碟加密？
是。 仍支援使用 Azure AD 應用程式來進行磁碟加密。 不過，在加密新的 VM 時，建議您使用新的方法，而不要使用 Azure AD 應用程式來加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以將使用 Azure AD 應用程式加密的 VM，遷移至未使用 Azure AD 應用程式的加密？
  目前沒有直接的移轉途徑，可將使用 Azure AD 應用程式加密的機器移轉至未使用 Azure AD 應用程式的加密。 此外，也沒有直接的途徑可從未使用 Azure AD 應用程式的加密移轉至使用 AD 應用程式的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁碟加密支援 Azure PowerShell 的什麼版本？

使用最新版的 Azure PowerShell SDK 來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 1.1.0 版「不」** 支援 Azure 磁碟加密。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>什麼是磁碟 "Bek Volume" 或 "/mnt/azure_bek_disk"？

"Bek volume" 是本機資料磁片區，可安全地儲存加密 Azure Vm 的加密金鑰。

> [!NOTE]
> 請勿刪除或編輯此磁碟中的任何內容。 由於 IaaS VM 上的任何加密作業都需要加密金鑰存在，因此請勿卸載該磁碟。

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁碟加密會使用何種加密方法？

Azure 磁碟加密會根據 Windows 版本，在 BitLocker 中選取加密方法，如下所示：

| Windows 版本                 | 版本 | 加密方法        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012、Windows 10 或更新版本  | >= 1511 |XTS-AES 256 位           |
| Windows Server 2012、Windows 8、8.1、10 | < 1511 |AES 256 位 *              |
| Windows Server 2008R2            |        |具有擴散器的 AES 256 位 |

\*Windows 2012 及更新版本中不支援具有擴散器的 AES 256 位。

若要判斷 Windows OS 版本，請在您的虛擬機器中執行 ' winver ' 工具。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll，並指定所有的磁碟區類型，是否會因此從已加密的資料磁碟機上清除資料？
否，不會從已使用「Azure 磁碟加密」進行加密的資料磁碟機中清除資料。 就像 EncryptFormatAll 不會重新加密 OS 磁碟機一樣，它也不會重新加密已加密的資料磁碟機。 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>我可以備份和還原已加密的 VM 嗎？ 

Azure 備份提供一種機制，可在相同的訂用帳戶和區域內備份和還原已加密的 VM。  如需指示，請參閱[使用 Azure 備份備份和還原已加密的虛擬機器](../../backup/backup-azure-vms-encryption.md)。  目前不支援將加密的 VM 還原到不同的區域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>我可以在哪裡提出問題或意見反應？

您可以在 [Azure 磁碟加密論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)提出問題或意見反應。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解有關 Azure 磁碟加密的常見問題。 如需此服務的詳細資訊，請參閱下列文章：

- [Azure 磁碟加密總覽](disk-encryption-overview.md)
- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)
