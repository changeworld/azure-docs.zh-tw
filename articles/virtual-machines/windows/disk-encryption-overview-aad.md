---
title: 'Azure 磁碟加密與 Azure AD (上一版) '
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: dc8349ed86a11b6588dbe4de3bf3d274de289374
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284501"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure 磁碟加密與 Azure AD (上一版) 

**Azure 磁碟加密的新版本不需要提供 Azure AD 應用程式參數，即可啟用 VM 磁片加密。在新版本中，您不再需要在啟用加密步驟期間提供 Azure AD 認證。所有新的 Vm 都必須使用新版本，而不使用 Azure AD 應用程式參數進行加密。若要查看使用新版本啟用 VM 磁片加密的指示，請參閱 [Windows vm 的 Azure 磁碟加密](disk-encryption-overview.md)。已使用 Azure AD 應用程式參數加密的 Vm 仍受支援，且應該繼續使用 AAD 語法進行維護。**

本文會針對 [Windows vm 補充 Azure 磁碟加密](disk-encryption-overview.md) ，其中包含 Azure AD (先前版本) Azure 磁碟加密的其他需求和必要條件。 [ [支援的 vm 與作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems) ] 區段仍維持不變。

## <a name="networking-and-group-policy"></a> 網路和群組原則

**若要使用較舊的 AAD 參數語法啟用 Azure 磁碟加密功能，IaaS VM 必須符合下列網路端點組態需求：** 
  - 若要取得用來連線至金鑰保存庫的權杖，IaaS VM 必須能連線至 Azure Active Directory 端點 \[login.microsoftonline.com\]。
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。
  - 要加密的 VM 必須設定為使用 TLS 1.2 作為預設通訊協定。 如果 TLS 1.0 已明確停用，且 .NET 版本尚未更新為4.6 或更高版本，下列登錄變更將可讓 ADE 選取較新的 TLS 版本：

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**群組原則：**
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如需關於「在不含相容 TPM 的情形下允許使用 BitLocker」的群組原則相關資訊，請參閱 [BitLocker 群組原則參考文件](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

-  已加入網域的虛擬機器上使用自訂群組策略的 BitLocker 原則必須包含下列設定：設定 [BitLocker 修復資訊的使用者儲存體-> 允許256位修復金鑰](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。  

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存體需求  

Azure 磁碟加密需要 Azure Key Vault 來控制及管理磁碟加密金鑰與祕密。 您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂閱中。

如需詳細資訊，請參閱 [使用 Azure AD (舊版) 建立和設定 Azure 磁碟加密的金鑰保存庫 ](disk-encryption-key-vault-aad.md)。
 
## <a name="next-steps"></a>接下來的步驟

- [使用 Azure AD (舊版建立和設定 Azure 磁碟加密的金鑰保存庫) ](disk-encryption-key-vault-aad.md)
- [在 Windows Vm 上使用 Azure AD (舊版啟用 Azure 磁碟加密) ](disk-encryption-windows-aad.md)
- [Azure 磁碟加密的必要 CLI 指令碼](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密的必要 PowerShell 指令碼](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
