---
title: Azure 磁碟加密與 Azure AD(上一個版本)
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 025d02ccdf38e72682cf67cc07a8b2edd549e599
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081569"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure 磁碟加密與 Azure AD(上一個版本)

**新版本的 Azure 磁碟加密消除了提供 Azure AD 應用程式參數以啟用 VM 磁碟加密的要求。使用新版本時,不再需要在啟用加密步驟期間提供 Azure AD 認證。使用新版本,必須在沒有 Azure AD 應用程式參數的情況下對所有新 VM 進行加密。要檢視使用新版本啟用 VM 磁碟加密的說明,請參閱 Windows [VM 的 Azure 磁碟加密](disk-encryption-overview.md)。仍支援已使用 Azure AD 應用程式參數加密的 VM,應繼續使用 AAD 語法進行維護。**

本文對[Windows VM 的 Azure 磁碟加密](disk-encryption-overview.md)補充了 Azure 磁碟加密的附加要求和與 Azure AD(上一個版本)一起進行 Azure 磁碟加密的先決條件。 [支援的 VM 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)部分保持不變。

## <a name="networking-and-group-policy"></a> 網路和群組原則

**若要使用較舊的 AAD 參數語法啟用 Azure 磁碟加密功能，IaaS VM 必須符合下列網路端點組態需求：** 
  - 若要取得用來連線至金鑰保存庫的權杖，IaaS VM 必須能連線至 Azure Active Directory 端點 \[login.microsoftonline.com\]。
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。
  - 要加密的 VM 必須配置為使用 TLS 1.2 作為預設協定。 如果 TLS 1.0 已顯示式關閉,並且 .NET 版本未更新到 4.6 或更高版本,則以下註冊表更改將使 ADE 能夠選擇較新的 TLS 版本:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**群組原則:**
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如需關於「在不含相容 TPM 的情形下允許使用 BitLocker」的群組原則相關資訊，請參閱 [BitLocker 群組原則參考文件](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

-  具有自訂群組政策的網域加入虛擬機器上的 BitLocker 政策必須包括以下設定:[設定使用者儲存的 BitLocker 復原資訊 ->允许 256 位元修復金鑰](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。  

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存要求  

Azure 磁碟加密需要 Azure 密鑰保管庫來控制和管理磁碟加密密鑰和機密。 密鑰保管庫和 VM 必須駐留在同一 Azure 區域和訂閱中。

有關詳細資訊,請參閱[使用 Azure AD 創建和配置 Azure 磁碟加密的密鑰保管庫(上一版本)。](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 建立與設定 Azure 磁碟加密的金鑰保管庫(上一版本)](disk-encryption-key-vault-aad.md)
- [在 Windows VM 上使用 Azure AD 啟用 Azure 磁碟加密(上一個版本)](disk-encryption-windows-aad.md)
- [Azure 磁碟加密先決條件 CLI 文稿](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密先決條件 PowerShell 文稿](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
