---
title: 具有 Azure AD 應用程式必要條件的 Azure 磁碟加密（舊版）
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: cc9f3b54d427a30b587d8335f6ce9b013f407374
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792559"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD 的 Azure 磁碟加密（上一個版本）

新版本的 Azure 磁碟加密不需要提供 Azure Active Directory （Azure AD）應用程式參數，就能啟用 VM 磁片加密。 若使用新版本，您就不再需要在啟用加密步驟期間提供 Azure AD 認證。 所有新的 Vm 都必須使用新版本，在沒有 Azure AD 應用程式參數的情況下進行加密。 如需如何使用新版本來啟用 VM 磁片加密的指示，請參閱[Linux vm 的 Azure 磁碟加密](disk-encryption-overview.md)。 已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，應該繼續使用 AAD 語法進行維護。

本文針對具有 Azure AD （舊版） Azure 磁碟加密的額外需求和必要條件的[Linux vm，提供 Azure 磁碟加密的](disk-encryption-overview.md)補充。

這些章節中的資訊保持不變：

- [支援的 Vm 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 需求](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a> 網路和群組原則

若要使用較舊的 AAD 參數語法來啟用 Azure 磁碟加密功能，基礎結構即服務（IaaS） Vm 必須符合下列網路端點設定需求： 
  - 若要取得權杖以連線到您的金鑰保存庫，IaaS VM 必須能夠連接到 Azure AD 端點\[login.microsoftonline.com。\]
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure Vm 到網際網路的存取，您可以解析上述的 URI，並設定特定的規則，以允許連到 Ip 的輸出連線能力。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。
  - 在 Windows 上，如果已明確停用 TLS 1.0，而 .NET 版本未更新為4.6 或更高版本，則下列登錄變更可讓 Azure 磁碟加密選取較新的 TLS 版本：
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>群組原則
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 針對已加入網域的 Vm，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如**需 [允許不含相容 TPM 的 bitlocker**] 選項群組原則的詳細資訊，請參閱[BitLocker 群組原則參考](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

- 已加入網域之虛擬機器上具有自訂群組原則的 BitLocker 原則必須包括下列設定：設定[BitLocker 修復資訊的使用者儲存體-> 允許256位的修復金鑰](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密會失敗。 在沒有正確原則設定的電腦上，套用新的原則，強制新的原則更新（gpupdate/force），然後視需要重新開機。 

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存需求 

Azure 磁碟加密需要 Azure Key Vault 來控制及管理磁片加密金鑰和密碼。 您的金鑰保存庫和 Vm 必須位於相同的 Azure 區域和訂用帳戶中。

如需詳細資訊，請參閱[建立和設定具有 Azure AD 之 Azure 磁碟加密的金鑰保存庫（舊版）](disk-encryption-key-vault-aad.md)。
 
## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 建立和設定 Azure 磁碟加密的金鑰保存庫（舊版）](disk-encryption-key-vault-aad.md)
- [在 Linux Vm 上使用 Azure AD 啟用 Azure 磁碟加密（舊版）](disk-encryption-linux-aad.md)
- [Azure 磁碟加密必要條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密必要條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)