---
title: Azure 磁片加密與 Azure AD 應用先決條件（上一個版本）
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970571"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure 磁片加密與 Azure AD（上一個版本）

Azure 磁片加密的新版本消除了提供 Azure 活動目錄 （Azure AD） 應用程式參數以啟用 VM 磁片加密的要求。 若使用新版本，您就不再需要在啟用加密步驟期間提供 Azure AD 認證。 使用新版本，必須對所有新 VM 進行加密，而無需 Azure AD 應用程式參數。 有關如何使用新版本啟用 VM 磁片加密的說明，請參閱 Linux VM[的 Azure 磁片加密](disk-encryption-overview.md)。 已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，應該繼續使用 AAD 語法進行維護。

本文為 Linux VM 提供了[Azure 磁片加密](disk-encryption-overview.md)的補充，以及使用 Azure AD 進行 Azure 磁片加密的其他要求和先決條件（上一版本）。

這些部分中的資訊保持不變：

- [支援的 VM 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 要求](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a> 網路和群組原則

要使用較舊的 AAD 參數語法啟用 Azure 磁片加密功能，基礎結構即服務 （IaaS） VM 必須滿足以下網路終結點配置要求： 
  - 要獲取權杖以連接到金鑰保存庫，IaaS VM 必須能夠連接到 Azure AD 終結點，login.microsoftonline.com \[ \]。
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果安全性原則限制從 Azure VM 訪問 Internet，則可以解析前面的 URI 並配置特定規則以允許向外站連接到 IP。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。
  - 在 Windows 上，如果 TLS 1.0 被顯式禁用，並且 .NET 版本未更新到 4.6 或更高版本，則以下註冊表更改使 Azure 磁片加密能夠選擇較新的 TLS 版本：
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>群組原則
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 對於加入域的 VM，不要推送任何強制實施 TPM 保護器的群組原則。 有關選項 **"允許未相容 TPM 的 BitLocker"** 選項的群組原則的資訊，請參閱[BitLocker 群組原則參考](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

- 具有自訂群組策略的域加入虛擬機器上的 BitLocker 策略必須包括以下設置：[配置 BitLocker 恢復資訊的使用者存儲 ->允許 256 位修復金鑰](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組策略設置不相容時，Azure 磁片加密將失敗。 在沒有正確策略設置的電腦上，應用新策略，強制更新新策略 （gpupdate.exe /force），然後根據需要重新開機。 

## <a name="encryption-key-storage-requirements"></a>加密金鑰存儲要求 

Azure 磁片加密需要 Azure 金鑰保存庫來控制和管理磁片加密金鑰和機密。 金鑰保存庫和 VM 必須駐留在同一 Azure 區域和訂閱中。

有關詳細資訊，請參閱使用[Azure AD 創建和配置 Azure 磁片加密的金鑰保存庫（上一版本）。](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 創建和配置 Azure 磁片加密的金鑰保存庫（上一版本）](disk-encryption-key-vault-aad.md)
- [在 Linux VM 上使用 Azure AD 啟用 Azure 磁片加密（上一個版本）](disk-encryption-linux-aad.md)
- [Azure 磁片加密先決條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁片加密先決條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)