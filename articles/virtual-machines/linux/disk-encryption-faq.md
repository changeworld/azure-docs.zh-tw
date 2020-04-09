---
title: 常見問題解答 ─ 適用於 Linux VM 的 Azure 磁碟加密
description: 本文提供了有關 Linux IaaS VM 的 Microsoft Azure 磁碟加密的常見問題的解答。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: ae3743530440c9df9094a0b9784922d2d6a3dfdf
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985400"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>適用於 Linux 虛擬機器的 Azure 磁碟加密的錯誤

本文提供了有關 Linux 虛擬機器 (VM) Azure 磁碟加密的常見問題 (FAQ) 的答案。 有關此服務的詳細資訊,請參閱[Azure 磁碟加密概述](disk-encryption-overview.md)。

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>什麼是適用於 Linux VM 的 Azure 磁碟加密?

Linux VM 的 Azure 磁碟加密使用 Linux 的 dm-crypt 功能來提供作業系統磁碟* 和資料磁碟的完整磁碟加密。 此外,在使用[加密格式所有功能](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)時,它還提供臨時資源磁碟的加密。 內容從 VM 加密到儲存後端。 因此,使用客戶管理的密鑰提供端到端加密。
 
請參考[的 VM 與作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure 磁碟加密會在那裡正式運作 (GA)？

所有 Azure 公共區域中,Linux VM 的 Azure 磁碟加密處於常規可用性。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁碟加密有哪些使用者體驗？

Azure 磁碟加密 GA 支援 Azure Resource Manager 範本、Azure PowerShell 和 Azure CLI。 不同的使用者體驗可為您賦予彈性。 對於 VM 啟用磁碟加密,您有三種不同的選項。 有關使用者體驗的詳細資訊和 Azure 磁碟加密中提供的分步指南,請參閱 Linux 的[Azure 磁碟加密方案](disk-encryption-linux.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁碟加密如何收費？

使用 Azure 磁碟加密對 VM 磁碟進行加密是免費的,但使用 Azure 密鑰保管庫需要支付相關費用。 如需 Azure Key Vault 成本的詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)頁面。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何開始使用 Azure 磁碟加密？

若要開始使用，請參閱 [Azure 磁碟加密概觀](disk-encryption-overview.md)。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>哪些 VM 大小和作業系統支援 Azure 磁碟加密?

[Azure 磁碟加密概述](disk-encryption-overview.md)一文列出支援 Azure 磁碟加密的[VM 大小](disk-encryption-overview.md#supported-vms)和 VM[作業系統](disk-encryption-overview.md#supported-operating-systems)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密來加密開機和資料磁碟區？

可以,您可以同時加密啟動卷和資料卷,也可以加密資料卷,而無需首先加密操作系統卷。 

加密作業系統捲後,不支援禁用作業系統卷上的加密。 對於規模集中的 Linux VM,只能加密數據卷。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密加密未安裝的捲?

否,Azure 磁碟加密僅加密裝載的卷。

## <a name="what-is-storage-server-side-encryption"></a>什麼是存儲伺服器端加密?

存儲伺服器端加密 Azure 儲存中的 Azure 託管磁碟。 默認情況下,使用平臺管理的密鑰(截至 2017 年 6 月 10 日)使用伺服器端加密對託管磁碟進行加密。 您可以通過指定客戶管理的金鑰,使用自己的密鑰管理託管磁碟的加密。 有關詳細資訊,請參閱[Azure 託管磁碟的伺服器端加密](disk-encryption.md)。
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Azure 磁碟加密與使用客戶管理的密鑰的儲存伺服器端加密有什麼不同,以及何時應該使用每個解決方案?

Azure 磁碟加密使用客戶管理的密鑰為 OS 磁碟、數據磁碟和臨時資源磁碟提供端到端加密。
- 如果要求包括加密上述所有加密和端到端加密,請使用 Azure 磁碟加密。 
- 如果要求包括僅使用客戶管理的金鑰加密靜態資料,則使用[伺服器端加密與客戶管理的金鑰一起使用](disk-encryption.md)。 不能使用客戶管理的金鑰對磁碟進行 Azure 磁碟加密和儲存伺服器端加密。 
- 如果 Linux 發行版未列[在 Azure 磁碟加密支援的作業系統](disk-encryption-overview.md#supported-operating-systems)下,或者您使用的是在[Windows 不支援的方案](disk-encryption-linux.md#unsupported-scenarios)中呼叫的方案,請考慮[使用客戶管理的金鑰進行伺服器端加密](disk-encryption.md)。
- 如果組織的策略允許您使用 Azure 管理的密鑰加密靜態內容,則無需執行任何操作 - 預設情況下對內容進行加密。 對於託管磁碟,默認情況下,使用平臺管理的密鑰對存儲內的內容進行加密。 金鑰由 Azure 儲存服務管理。 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何輪換機密或加密密鑰?

要旋轉機密,只需調用最初用於啟用磁碟加密的相同命令,指定其他密鑰保管庫。 要旋轉金鑰加密金鑰,請呼叫最初用於啟用磁碟加密的相同命令,指定新的金鑰加密。 

>[!WARNING]
> - 如果您以前通過指定 Azure AD 認證此 VM,[從而對 Azure AD 應用使用了 Azure 磁碟加密](disk-encryption-linux-aad.md),則必須繼續使用此選項來加密 VM。 不能在此加密 VM 上使用 Azure 磁碟加密,因為這不是受支援的方案,這意味著尚不支援出於此加密 VM 從 AAD 應用程式切換。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如果我最初沒有使用金鑰加密密鑰,如何添加或刪除金鑰加密金鑰?

要加入金鑰加密金鑰,請再次呼叫啟用命令傳遞金鑰加密金鑰參數。 要刪除金鑰加密金鑰,請再次呼叫啟用命令,而不使用金鑰加密金鑰參數。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁碟加密可讓您使用攜帶自己的金鑰 (BYOK) 嗎？

是，您可以提供自己的金鑰加密金鑰。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 有關金鑰加密金鑰支援方案的詳細資訊,請參閱[建立與設定 Azure 磁碟加密的金鑰保管庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 建立的金鑰加密金鑰？

是，您可以使用 Azure Key Vault 來產生金鑰加密金鑰以供 Azure 磁碟加密使用。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 關於金鑰加密金鑰的詳細資訊,請參考[建立與設定 Azure 磁碟加密的金鑰保存 。](disk-encryption-key-vault.md)

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用內部部署金鑰管理服務或 HSM 來保護加密金鑰？

您無法使用內部部署金鑰管理服務或 HSM 來保護 Azure 磁碟加密的加密金鑰。 只能使用 Azure Key Vault 服務來保護加密金鑰。 有關金鑰加密金鑰支援方案的詳細資訊,請參閱[建立與設定 Azure 磁碟加密的金鑰保管庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[為 Azure 磁碟加密創建和設定金鑰保管庫](disk-encryption-key-vault.md)一文,以建立新的密鑰保管庫,或為磁碟加密存取設定現有金鑰保管庫,以啟用加密並保護機密和密鑰。 有關金鑰加密金鑰支援方案的詳細資訊,請參閱[建立與設定 Azure 磁碟加密的金鑰保管庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 應用程式 (舊版) 來設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[Azure 磁碟加密與 Azure AD](disk-encryption-linux-aad.md)內容建立 Azure 活動目錄應用程式、創建新密鑰保管庫或設定磁碟加密存取的現有金鑰保管庫以啟用加密並保護機密和密鑰。 關於金鑰加密金鑰支援機制的詳細資訊,請參考使用[Azure AD 建立與設定 Azure 磁碟加密的金鑰保存 。](disk-encryption-key-vault-aad.md)

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍支援使用 Azure AD 應用程式 (舊版) 來進行 Azure 磁碟加密？
是。 仍支援使用 Azure AD 應用程式來進行磁碟加密。 不過，在加密新的 VM 時，建議您使用新的方法，而不要使用 Azure AD 應用程式來加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以將使用 Azure AD 應用程式加密的 VM，遷移至未使用 Azure AD 應用程式的加密？
  目前沒有直接的移轉途徑，可將使用 Azure AD 應用程式加密的機器移轉至未使用 Azure AD 應用程式的加密。 此外，也沒有直接的途徑可從未使用 Azure AD 應用程式的加密移轉至使用 AD 應用程式的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁碟加密支援 Azure PowerShell 的什麼版本？

使用最新版的 Azure PowerShell SDK 來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 1.1.0 版「不」** 支援 Azure 磁碟加密。

> [!NOTE]
> Linux Azure 磁碟加密預覽擴展"Microsoft.OSTC 擴展.Azure 磁碟加密為 Linux"被棄用。 此擴展發佈用於 Azure 磁碟加密預覽版本。 不應在測試或生產部署中使用擴展的預覽版本。

> 對於 Azure 資源管理員 (ARM) 等部署方案,需要為 Linux VM 部署 Azure 磁碟加密擴展以在 Linux IaaS VM 上啟用加密,則必須使用 Azure 磁碟加密生產支援的擴展"Microsoft.Azure.Security.AzureDisk加密ForLinux"。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>是否可以在我的自訂 Linux 映像上套用 Azure 磁碟加密？

您無法對自訂 Linux 映像套用 Azure 磁碟加密。 只有前述支援散發版本的資源庫 Linux 映像受到支援。 目前不支援自訂 Linux 映像。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>是否可使用 Yum 更新將更新套用至 Linux Red Hat VM？

可以,您可以在紅帽 Linux VM 上執行 yum 更新。  有關詳細資訊,請參閱[隔離網路上的 Azure 磁碟加密](disk-encryption-isolated-network.md)。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>若為 Linux，建議使用何種 Azure 磁碟加密工作流程？

建議採取下列工作流程，即可在 Linux 上獲得最佳結果：
* 先從與所需的作業系統發行版本和版本相對應的未修改內建資源庫映像開始
* 將會被加密的任何掛接磁碟機備份。  此備份可在發生失敗時用來進行復原，例如，VM 在加密完成前重新開機時。
* 加密 (可能需要數小時或甚至數天，取決於 VM 特性和附加的任何資料磁碟的大小)
* 自訂軟體，並且視需要將軟體新增至映像。

如果此工作流程不可行，靠著平台儲存體帳戶層[的儲存體服務加密](../../storage/common/storage-service-encryption.md) (SSE)，可能可以替代使用 dm crypt 的完整磁碟加密。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>什麼是磁碟 "Bek Volume" 或 "/mnt/azure_bek_disk"？

"Bek 卷"是一個本地資料卷,用於安全地存儲加密密鑰以用於加密 Azure VM。
> [!NOTE]
> 請勿刪除或編輯此磁碟中的任何內容。 由於 IaaS VM 上的任何加密作業都需要加密金鑰存在，因此請勿卸載該磁碟。


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁碟加密會使用何種加密方法？

Azure 磁碟加密使用具有 256 位卷主密鑰的 aes-xts-plain64 的解密預設值。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll，並指定所有的磁碟區類型，是否會因此從已加密的資料磁碟機上清除資料？
否，不會從已使用「Azure 磁碟加密」進行加密的資料磁碟機中清除資料。 就像 EncryptFormatAll 不會重新加密 OS 磁碟機一樣，它也不會重新加密已加密的資料磁碟機。 如需詳細資訊，請參閱 [EncryptFormatAll 準則](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)。        

## <a name="is-xfs-filesystem-supported"></a>是否支援 XFS 檔案系統?
僅使用加密格式所有資料磁碟加密,支援 XFS 卷。 這將格式化卷,並會重複以前存在的任何數據。 如需詳細資訊，請參閱 [EncryptFormatAll 準則](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)。

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>我可以備份和還原加密的 VM 嗎? 

Azure 備份提供了一種機制,用於在同一訂閱和地區中備份和還原加密 VM。  有關說明,請參閱使用[Azure 備份 備份和還原加密虛擬機器](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。  當前不支援將加密 VM 還原到其他區域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>我可以在哪裡提出問題或意見反應？

您可以在 [Azure 磁碟加密論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)提出問題或意見反應。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解有關 Azure 磁碟加密的常見問題。 如需此服務的詳細資訊，請參閱下列文章：

- [Azure 磁碟加密概述](disk-encryption-overview.md)
- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [待用 Azure 資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
