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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082892"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>將受 HSM 保護的金鑰導入金鑰保存庫

為了加強保證，當您使用 Azure 金鑰保存庫時，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。 此案例通常稱為 *自備金鑰*或 BYOK。 Azure 金鑰保存庫使用 nCipher nShield 系列 HSM（FIPS 140-2 級別 2 驗證）來保護金鑰。

此功能不適用於 Azure 中國 21Vianet。

> [!NOTE]
> 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-overview.md)  
> 如需入門教學課程 (包括建立受 HSM 保護之金鑰的金鑰保存庫)，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。

## <a name="supported-hsms"></a>支援 HSM

根據您使用的 HSM，可通過兩種不同的方法將受 HSM 保護的金鑰傳輸到金鑰保存庫。 使用下表確定應該使用哪種方法來生成 HSM，然後傳輸自己的受 HSM 保護的金鑰，以便與 Azure 金鑰保存庫一起使用。 

|廠商名稱|供應商類型|支援的 HSM 型號|支援的 HSM 金鑰傳輸方法|
|---|---|---|---|
|n 奇普赫|製造商|<ul><li>nShield 系列 HSM</li></ul>|[使用舊 BYOK 方法](hsm-protected-keys-legacy.md)|
|泰萊斯|製造商|<ul><li>SafeNet Luna HSM 7 系列，固件版本 7.3 或更新</li></ul>| [使用新的 BYOK 方法（預覽）](hsm-protected-keys-vendor-agnostic-byok.md)|
|福塔尼克斯|HSM 作為服務|<ul><li>自防金鑰管理服務 （SDKMS）</li></ul>|[使用新的 BYOK 方法（預覽）](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>後續步驟

遵循[關鍵保存庫最佳實踐](key-vault-best-practices.md)，以確保金鑰的安全性、耐用性和監控。
