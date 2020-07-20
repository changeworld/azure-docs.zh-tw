---
title: 常見問題集 - 適用於 Linux VM 的 Azure 磁碟加密
description: 此文章提供適用於 Linux IaaS VM 的 Microsoft Azure 磁碟加密常見問題集的解答。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: d28d7bd85326e8a4cf27dba1f3f605b64477c5aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829932"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>適用於 Linux 虛擬機器的 Azure 磁碟加密常見問題集

此文章提供適用於 Linux 虛擬機器 (VM) 的 Azure 磁碟加密常見問題集 (FAQ) 的解答。 如需此服務的詳細資訊，請參閱 [Azure 磁碟加密概觀](disk-encryption-overview.md)。

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>什麼是適用於 Linux VM 的 Azure 磁碟加密？

適用於 Linux VM 的 Azure 磁碟加密會使用 Linux 的 dm-crypt 功能，提供 OS 磁碟*和資料磁碟的完整磁碟加密。 此外，使用 [EncryptFormatAll 功能](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)時，其也會提供暫存磁碟的加密。 內容會以加密形式從 VM 流向儲存體後端。 據此，其能透過客戶管理的金鑰提供端對端加密。
 
請參閱[支援的 VM 與作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure 磁碟加密會在那裡正式運作 (GA)？

適用於 Linux VM 的 Azure 磁碟加密已在所有 Azure 公用區域中公開推出。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁碟加密有哪些使用者體驗？

Azure 磁碟加密 GA 支援 Azure Resource Manager 範本、Azure PowerShell 和 Azure CLI。 不同的使用者體驗可為您賦予彈性。 您會有三個不同選項可啟用 VM 的磁碟加密。 如需 Azure 磁碟加密所提供之使用者體驗與逐步指導方針的詳細資訊，請參閱[適用於 Linux 的 Azure 磁碟加密案例](disk-encryption-linux.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁碟加密如何收費？

使用 Azure 磁碟加密對 VM 磁碟進行加密並不需付費，但如果使用 Azure Key Vault 則需付費。 如需 Azure Key Vault 成本的詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)頁面。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何開始使用 Azure 磁碟加密？

若要開始使用，請參閱 [Azure 磁碟加密概觀](disk-encryption-overview.md)。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>哪些 VM 大小和作業系統支援 Azure 磁碟加密？

[Azure 磁碟加密概觀](disk-encryption-overview.md)一文列出支援 Azure 磁碟加密的 [VM 大小](disk-encryption-overview.md#supported-vms)和 [VM 作業系統](disk-encryption-overview.md#supported-operating-systems)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密來加密開機和資料磁碟區？

是，您可以同時將開機和資料磁碟區加密，或者您可以在無須先將 OS 磁碟區加密的情況下，將資料磁碟區加密。 

您將 OS 磁碟區加密之後，就不支援停用 OS 磁碟區上的加密。 針對擴展集中的 Linux VM，只能將資料磁碟區加密。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密來加密已卸載的磁碟區？

否，Azure 磁碟加密只能將已掛接的磁碟區加密。

## <a name="what-is-storage-server-side-encryption"></a>什麼是儲存體伺服器端加密？

儲存體伺服器端加密會將 Azure 儲存體中的 Azure 受控磁碟加密。 受控磁碟預設會採用使用平台管理的金鑰進行伺服器端加密來加密 (從 2017 年 6 月 10 日開始)。 您可以藉由指定客戶管理的金鑰，使用您自己的金鑰來管理受控磁碟的加密。 如需詳細資訊，請參閱：[Azure 受控磁碟的伺服器端加密](disk-encryption.md)。
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Azure 磁碟加密與使用客戶管理的金鑰進行的儲存體伺服器端加密有何不同？其使用時機分別為何？

Azure 磁碟加密會使用客戶管理的金鑰，為 OS 磁碟、資料磁碟和暫存磁碟提供端對端加密。
- 如果您的需求包括將前述所有項目加密和端對端加密，請使用 Azure 磁碟加密。 
- 如果您的需求僅為使用客戶管理的金鑰來將待用資料加密，請採用[使用客戶管理的金鑰進行伺服器端加密](disk-encryption.md)。 您無法同時搭配客戶管理的金鑰使用 Azure 磁碟加密和儲存體伺服器端加密來將磁碟加密。 
- 如果您的 Linux 發行版本並未列於 [Azure 磁碟加密支援的作業系統](disk-encryption-overview.md#supported-operating-systems)底下，或是您是使用[針對 Windows 不支援的案例](disk-encryption-linux.md#unsupported-scenarios)中所列出的案例，請考慮採用[使用客戶管理的金鑰進行伺服器端加密](disk-encryption.md)。
- 如果您組織的原則允許您使用 Azure 管理的金鑰來將待用內容加密，則不需要採取任何動作，因為該內容預設便會加密。 就受控磁碟而言，儲存體內的內容預設會使用平台管理的金鑰進行伺服器端加密來加密。 此金鑰由 Azure 儲存體服務所管理。 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何輪替祕密或加密金鑰？

若要輪替祕密，只要呼叫您最初用來啟用磁碟加密的相同命令，並指定不同的 Key Vault 即可。 若要輪替金鑰加密金鑰，請呼叫您最初用來啟用磁碟加密的相同命令，並指定新的金鑰加密。 

>[!WARNING]
> - 如果您先前曾指定 Azure AD 認證以將此 VM 加密，來使用 [Azure 磁碟加密搭配 Azure AD 應用程式](disk-encryption-linux-aad.md)，則必須繼續使用此選項來將您的 VM 加密。 您無法在此加密的 VM 上使用 Azure 磁碟加密，因為這不是支援的案例，這表示目前尚不支援將此加密的 VM 切換為 AAD 應用程式以外的選項。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如果我最初未使用金鑰加密金鑰，要如何新增或移除此金鑰？

若要新增金鑰加密金鑰，請再次呼叫啟用命令，並傳遞金鑰加密金鑰參數。 若要移除金鑰加密金鑰，請再次呼叫啟用命令，但不使用金鑰加密金鑰參數。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁碟加密可讓您使用攜帶自己的金鑰 (BYOK) 嗎？

是，您可以提供自己的金鑰加密金鑰。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 建立的金鑰加密金鑰？

是，您可以使用 Azure Key Vault 來產生金鑰加密金鑰以供 Azure 磁碟加密使用。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰的詳細資訊，請參閱[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用內部部署金鑰管理服務或 HSM 來保護加密金鑰？

您無法使用內部部署金鑰管理服務或 HSM 來保護 Azure 磁碟加密的加密金鑰。 只能使用 Azure Key Vault 服務來保護加密金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)一文，以建立新的金鑰保存庫，或針對磁碟加密存取設定現有的金鑰保存庫，從而啟用加密並保護祕密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 應用程式 (舊版) 來設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱 [Azure 磁碟加密搭配 Azure AD 應用程式](disk-encryption-linux-aad.md)的內容，以建立 Azure Active Directory 應用程式、建立新的金鑰保存庫，或針對磁碟加密存取設定現有的金鑰保存庫，從而啟用加密並保護祕密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[使用 Azure AD 建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault-aad.md)。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍支援使用 Azure AD 應用程式 (舊版) 來進行 Azure 磁碟加密？
是。 仍支援使用 Azure AD 應用程式來進行磁碟加密。 不過，在加密新的 VM 時，建議您使用新的方法，而不要使用 Azure AD 應用程式來加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以將使用 Azure AD 應用程式加密的 VM，遷移至未使用 Azure AD 應用程式的加密？
  目前沒有直接的移轉途徑，可將使用 Azure AD 應用程式加密的機器移轉至未使用 Azure AD 應用程式的加密。 此外，也沒有直接的途徑可從未使用 Azure AD 應用程式的加密移轉至使用 AD 應用程式的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁碟加密支援 Azure PowerShell 的什麼版本？

使用最新版的 Azure PowerShell SDK 來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 1.1.0 版「不」支援 Azure 磁碟加密。

> [!NOTE]
> Linux Azure 磁碟加密預覽延伸模組 "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" 已經淘汰。 此延伸模組先前是針對 Azure 磁碟加密預覽版本所發佈。 您不應該在測試或生產部署中使用此延伸模組的預覽版本。

> 針對如 Azure Resource Manager (ARM) 的部署案例，其中您必須部署適用於 Linux VM 的 Azure 磁碟加密延伸模組以在 Linux IaaS VM 上啟用加密，您必須使用支援生產環境的 Azure 磁碟加密延伸模組 "Microsoft.Azure.Security.AzureDiskEncryptionForLinux"。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>是否可以在我的自訂 Linux 映像上套用 Azure 磁碟加密？

您無法對自訂 Linux 映像套用 Azure 磁碟加密。 只有前述支援散發版本的資源庫 Linux 映像受到支援。 目前不支援自訂 Linux 映像。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>是否可使用 Yum 更新將更新套用至 Linux Red Hat VM？

是，您可以在 Red Hat Linux VM 上執行 Yum 更新。  如需詳細資訊，請參閱[隔離式網路上的 Azure 磁碟加密](disk-encryption-isolated-network.md)。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>若為 Linux，建議使用何種 Azure 磁碟加密工作流程？

建議採取下列工作流程，即可在 Linux 上獲得最佳結果：
* 先從與所需的作業系統發行版本和版本相對應的未修改內建資源庫映像開始
* 將會被加密的任何掛接磁碟機備份。  此備份可在發生失敗時用來進行復原，例如，VM 在加密完成前重新開機時。
* 加密 (可能需要數小時或甚至數天，取決於 VM 特性和附加的任何資料磁碟的大小)
* 自訂軟體，並且視需要將軟體新增至映像。

如果此工作流程不可行，靠著平台儲存體帳戶層[的儲存體服務加密](../../storage/common/storage-service-encryption.md) (SSE)，可能可以替代使用 dm crypt 的完整磁碟加密。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>什麼是磁碟 "Bek Volume" 或 "/mnt/azure_bek_disk"？

"Bek volume" 為本機資料磁碟區，其會安全地儲存加密的 Azure VM 所使用的加密金鑰。
> [!NOTE]
> 請勿刪除或編輯此磁碟中的任何內容。 由於 IaaS VM 上的任何加密作業都需要加密金鑰存在，因此請勿卸載該磁碟。


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁碟加密會使用何種加密方法？

Azure 磁碟加密會使用 aes-xts-plain64 搭配 256 位元磁碟區主要金鑰的解密預設值。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll，並指定所有的磁碟區類型，是否會因此從已加密的資料磁碟機上清除資料？
否，不會從已使用「Azure 磁碟加密」進行加密的資料磁碟機中清除資料。 就像 EncryptFormatAll 不會重新加密 OS 磁碟機一樣，它也不會重新加密已加密的資料磁碟機。 如需詳細資訊，請參閱 [EncryptFormatAll 準則](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)。        

## <a name="is-xfs-filesystem-supported"></a>是否支援 XFS 檔案系統？
支援對 XFS OS 磁碟進行加密。

只有在使用 EncryptFormatAll 參數的情況下，才支援對 XFS 資料磁碟進行加密。 這會將磁碟區重新格式化，並清除其上方所有先前的資料。 如需詳細資訊，請參閱 [EncryptFormatAll 準則](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)。

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>我是否可以備份及還原加密的 VM？ 

Azure 備份提供一種機制，可以在相同的訂用帳戶和區域內備份及還原加密的 VM。  如需相關指示，請參閱[使用 Azure 備份來備份及還原加密的虛擬機器](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) \(部分機器翻譯\)。  目前不支援將加密的 VM 還原至不同的區域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>我可以在哪裡提出問題或意見反應？

您可以在 [Azure 磁碟加密的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-disk-encryption.html) \(英文\) 上提問或提供意見反應。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解有關 Azure 磁碟加密的常見問題。 如需此服務的詳細資訊，請參閱下列文章：

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [待用 Azure 資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
