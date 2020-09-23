---
title: 'Azure 磁碟加密 Azure AD 應用程式必要條件 (上一版) '
description: 本文提供 Linux Vm Azure 磁碟加密的補充，以及使用 Azure AD Azure 磁碟加密的其他需求和必要條件。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b258d499c78aa5fb734cbee01fb753c292bf2678
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970891"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure 磁碟加密與 Azure Active Directory (AD)  (先前的版本) 

Azure 磁碟加密的新版本不需要提供 Azure Active Directory (Azure AD) 應用程式參數，即可啟用 VM 磁片加密。 若使用新版本，您就不再需要在啟用加密步驟期間提供 Azure AD 認證。 您必須使用新的版本，在沒有 Azure AD 應用程式參數的情況下加密所有新的 Vm。 如需如何使用新版本啟用 VM 磁片加密的指示，請參閱 [Linux vm 的 Azure 磁碟加密](disk-encryption-overview.md)。 已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，應該繼續使用 AAD 語法進行維護。

本文提供 [Linux vm Azure 磁碟加密](disk-encryption-overview.md) 的補充，以及 Azure AD (先前版本) Azure 磁碟加密的其他需求和必要條件。

這些區段中的資訊維持不變：

- [支援的 VM 與作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 需求](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a> 網路和群組原則

若要使用較舊的 AAD 參數語法來啟用 Azure 磁碟加密功能，基礎結構即服務 (IaaS) Vm 必須符合下列網路端點設定需求： 
  - 若要取得權杖以連接到您的金鑰保存庫，IaaS VM 必須能夠連接到 Azure AD 端點 \[ login.microsoftonline.com \] 。
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure Vm 到網際網路的存取，您可以解析上述的 URI，並設定特定的規則，以允許連至 Ip 的輸出連線能力。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。
  - 在 Windows 上，如果 TLS 1.0 已明確停用，而 .NET 版本未更新為4.6 或更高版本，則下列登錄變更可讓 Azure 磁碟加密選取較新的 TLS 版本：

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>群組原則
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 針對已加入網域的 Vm，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如 **需 [允許沒有相容 TPM 的 bitlocker**] 選項群組原則的詳細資訊，請參閱 [bitlocker 群組原則參考](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

- 已加入網域之虛擬機器上具有自訂群組原則的 BitLocker 原則必須包含下列設定：設定 [BitLocker 修復資訊的使用者儲存體-> 允許256位修復金鑰](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密會失敗。 在沒有正確原則設定的電腦上，套用新的原則、強制新原則更新 ( # A0/force) ，然後視需要重新開機。 

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存體需求 

Azure 磁碟加密需要 Azure Key Vault 控制和管理磁片加密金鑰和秘密。 您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂閱中。

如需詳細資訊，請參閱 [使用 Azure AD (舊版) Azure 磁碟加密建立和設定金鑰保存庫 ](disk-encryption-key-vault-aad.md)。
 
## <a name="next-steps"></a>下一步

- [使用 Azure AD (舊版建立和設定 Azure 磁碟加密的金鑰保存庫) ](disk-encryption-key-vault-aad.md)
- [在 Linux Vm 上使用 Azure AD 啟用 Azure 磁碟加密 (舊版) ](disk-encryption-linux-aad.md)
- [Azure 磁碟加密的必要 CLI 指令碼](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密的必要 PowerShell 指令碼](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
