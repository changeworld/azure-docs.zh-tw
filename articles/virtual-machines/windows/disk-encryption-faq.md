---
title: 常見問題解答 - 適用于 Windows VM 的 Azure 磁片加密
description: 本文提供了有關 Windows IaaS VM 的 Microsoft Azure 磁片加密的常見問題的解答。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: ea2a66a6b012664a9596a02ea32c1a0b677ee3ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384257"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>適用于 Windows VM 的 Azure 磁片加密常見問題解答

本文提供了有關 Windows VM Azure 磁片加密的常見問題 （FAQ） 的答案。 有關此服務的詳細資訊，請參閱[Azure 磁片加密概述](disk-encryption-overview.md)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure 磁碟加密會在那裡正式運作 (GA)？

Azure 磁片加密在所有 Azure 公共區域中都是通用的。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁碟加密有哪些使用者體驗？

Azure 磁碟加密 GA 支援 Azure Resource Manager 範本、Azure PowerShell 和 Azure CLI。 不同的使用者體驗可為您賦予彈性。 對於 VM 啟用磁片加密，您有三種不同的選項。 有關使用者體驗的詳細資訊以及 Azure 磁片加密中提供的分步指南，請參閱 Windows 的[Azure 磁片加密方案](disk-encryption-windows.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁碟加密如何收費？

使用 Azure 磁片加密對 VM 磁片進行加密是免費的，但使用 Azure 金鑰保存庫需要支付相關費用。 如需 Azure Key Vault 成本的詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)頁面。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何開始使用 Azure 磁碟加密？

若要開始使用，請參閱 [Azure 磁碟加密概觀](disk-encryption-overview.md)。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>哪些 VM 大小和作業系統支援 Azure 磁片加密？

[Azure 磁片加密概述](disk-encryption-overview.md)一文列出了支援 Azure 磁片加密的[VM 大小](disk-encryption-overview.md#supported-vm-sizes)和 VM[作業系統](disk-encryption-overview.md#supported-operating-systems)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密來加密開機和資料磁碟區？

您可以同時加密開機磁碟區和資料卷，但如果不首先加密作業系統卷，就無法加密資料。

加密作業系統卷後，不支援禁用作業系統卷上的加密。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>是否可以使用 Azure 磁片加密加密未安裝的卷？

否，Azure 磁片加密僅加密裝載的卷。

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何輪換機密或加密金鑰？

要旋轉機密，只需調用最初用於啟用磁片加密的相同命令，指定其他金鑰保存庫。 要旋轉金鑰加密金鑰，請調用最初用於啟用磁片加密的相同命令，指定新的金鑰加密。 

>[!WARNING]
> - 如果您以前通過指定 Azure AD 憑據來加密此 VM，[從而對 Azure AD 應用使用了 Azure 磁片加密](disk-encryption-windows-aad.md)，則必須繼續使用此選項來加密 VM。 您無法在此加密的 VM 上使用 Azure 磁碟加密，因為這不是支援的案例，表示尚未對此加密的 VM 支援從 AAD 應用程式離開。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如果我最初沒有使用金鑰加密金鑰，如何添加或刪除金鑰加密金鑰？

要添加金鑰加密金鑰，請再次調用啟用命令傳遞金鑰加密金鑰參數。 要刪除金鑰加密金鑰，請再次調用啟用命令，而不使用金鑰加密金鑰參數。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁碟加密可讓您使用攜帶自己的金鑰 (BYOK) 嗎？

是，您可以提供自己的金鑰加密金鑰。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 有關金鑰加密金鑰支援方案的詳細資訊，請參閱[創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 建立的金鑰加密金鑰？

是，您可以使用 Azure Key Vault 來產生金鑰加密金鑰以供 Azure 磁碟加密使用。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 有關金鑰加密金鑰的詳細資訊，請參閱[創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用內部部署金鑰管理服務或 HSM 來保護加密金鑰？

您無法使用內部部署金鑰管理服務或 HSM 來保護 Azure 磁碟加密的加密金鑰。 只能使用 Azure Key Vault 服務來保護加密金鑰。 有關金鑰加密金鑰支援方案的詳細資訊，請參閱[創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[為 Azure 磁片加密創建和配置金鑰保存庫](disk-encryption-key-vault.md)一文，以創建新的金鑰保存庫，或為磁片加密訪問設置現有金鑰保存庫，以啟用加密並保護機密和金鑰。 有關金鑰加密金鑰支援方案的詳細資訊，請參閱[創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 應用程式 (舊版) 來設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[Azure 磁片加密與 Azure AD](disk-encryption-windows-aad.md)內容創建 Azure 活動目錄應用程式、創建新金鑰保存庫或設置磁片加密訪問的現有金鑰保存庫以啟用加密並保護機密和金鑰。 有關金鑰加密金鑰支援方案的詳細資訊，請參閱使用 Azure [AD 創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault-aad.md)。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍支援使用 Azure AD 應用程式 (舊版) 來進行 Azure 磁碟加密？
是。 仍支援使用 Azure AD 應用程式來進行磁碟加密。 不過，在加密新的 VM 時，建議您使用新的方法，而不要使用 Azure AD 應用程式來加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以將使用 Azure AD 應用程式加密的 VM，遷移至未使用 Azure AD 應用程式的加密？
  目前沒有直接的移轉途徑，可將使用 Azure AD 應用程式加密的機器移轉至未使用 Azure AD 應用程式的加密。 此外，也沒有直接的途徑可從未使用 Azure AD 應用程式的加密移轉至使用 AD 應用程式的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁碟加密支援 Azure PowerShell 的什麼版本？

使用最新版的 Azure PowerShell SDK 來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 1.1.0 版「不」** 支援 Azure 磁碟加密。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>什麼是磁碟 "Bek Volume" 或 "/mnt/azure_bek_disk"？

"Bek 卷"是一個本地資料卷，用於安全地存儲加密金鑰以用於加密 Azure VM。

> [!NOTE]
> 請勿刪除或編輯此磁碟中的任何內容。 由於 IaaS VM 上的任何加密作業都需要加密金鑰存在，因此請勿卸載該磁碟。

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁碟加密會使用何種加密方法？

Azure 磁片加密根據 Windows 版本在 BitLocker 中選擇加密方法，如下所示：

| 視窗版本                 | 版本 | 加密方法        |
|----------------------------------|--------|--------------------------|
| Windows 伺服器 2012、Windows 10 或更高  | >#1511 |XTS-AES 256 位           |
| Windows 伺服器 2012， Windows 8， 8.1， 10 | < 1511 |AES 256 位 |              |
| Windows Server 2008R2            |        |AES 256 位，帶擴散器 |

\*Windows 2012 及更高版本不支援具有漫反射器的 AES 256 位。

要確定 Windows 作業系統版本，請運行虛擬機器中的"winver"工具。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll，並指定所有的磁碟區類型，是否會因此從已加密的資料磁碟機上清除資料？
否，不會從已使用「Azure 磁碟加密」進行加密的資料磁碟機中清除資料。 就像 EncryptFormatAll 不會重新加密 OS 磁碟機一樣，它也不會重新加密已加密的資料磁碟機。 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>我可以備份和還原加密的 VM 嗎？ 

Azure 備份提供了一種機制，用於在同一訂閱和地區中備份和還原加密 VM。  有關說明，請參閱使用[Azure 備份 備份和還原加密虛擬機器](../../backup/backup-azure-vms-encryption.md)。  當前不支援將加密 VM 還原到其他區域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>我可以在哪裡提出問題或意見反應？

您可以在 [Azure 磁碟加密論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)提出問題或意見反應。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解有關 Azure 磁碟加密的常見問題。 如需此服務的詳細資訊，請參閱下列文章：

- [Azure 磁片加密概述](disk-encryption-overview.md)
- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)
