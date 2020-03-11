---
title: 如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰 - Azure Key Vault | Microsoft Docs
description: 使用這份文件協助您規劃、產生，並傳輸受 HSM 保護的金鑰，以搭配 Azure 金鑰保存庫使用。 也稱為 BYOK 或「攜帶您自己的金鑰」。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082892"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>將受 HSM 保護的金鑰匯入至 Key Vault

為了加強保證，當您使用 Azure 金鑰保存庫時，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。 此案例通常稱為 *自備金鑰*或 BYOK。 Azure Key Vault 使用 Hsm 的 nCipher nShield 系列（已驗證 FIPS 140-2 Level 2）來保護您的金鑰。

此功能不適用於 Azure 中國世紀。

> [!NOTE]
> 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](key-vault-overview.md)  
> 如需入門教學課程 (包括建立受 HSM 保護之金鑰的金鑰保存庫)，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。

## <a name="supported-hsms"></a>支援的 Hsm

根據您所使用的 Hsm，透過兩種不同的方法來支援將受 HSM 保護的金鑰傳輸至 Key Vault。 使用下表來判斷應該使用哪一種方法來產生 Hsm，然後傳輸您自己的受 HSM 保護的金鑰，以與 Azure Key Vault 搭配使用。 

|廠商名稱|廠商類型|支援的 HSM 模型|支援的 HSM 金鑰傳輸方法|
|---|---|---|---|
|nCipher|製造商|<ul><li>Hsm 的 nShield 系列</li></ul>|[使用舊版 BYOK 方法](hsm-protected-keys-legacy.md)|
|Thales|製造商|<ul><li>SafeNet Luna HSM 7 系列，含7.3 或更新版本的固件</li></ul>| [使用新的 BYOK 方法（預覽）](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM 即服務|<ul><li>自我保護金鑰管理服務（SDKMS）</li></ul>|[使用新的 BYOK 方法（預覽）](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>後續步驟

遵循[Key Vault 最佳作法](key-vault-best-practices.md)，以確保金鑰的安全性、耐久性和監視。
