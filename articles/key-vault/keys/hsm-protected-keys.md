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
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 5433d9746cd64d0e942e056cfcd1940eba35c77d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84417917"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>將受 HSM 保護的金鑰匯入 Key Vault

為了加強保證，當您使用 Azure 金鑰保存庫時，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。 此案例通常稱為 *自備金鑰*或 BYOK。 Azure Key Vault 使用 Hsm 的 nCipher nShield 系列（已驗證 FIPS 140-2 Level 2）來保護您的金鑰。

此功能不適用於 Azure 中國世紀。

> [!NOTE]
> 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../general/overview.md)  
> 如需入門教學課程 (包括建立受 HSM 保護之金鑰的金鑰保存庫)，請參閱[什麼是 Azure Key Vault？](../general/overview.md)。

## <a name="supported-hsms"></a>支援的 Hsm

根據您所使用的 Hsm，透過兩種不同的方法來支援將受 HSM 保護的金鑰傳輸至 Key Vault。 使用下表來判斷應該使用哪一種方法來產生 Hsm，然後傳輸您自己的受 HSM 保護的金鑰，以與 Azure Key Vault 搭配使用。 

|廠商名稱|廠商類型|支援的 HSM 模型|支援的 HSM 金鑰傳輸方法|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|負責<br/>HSM 即服務|<ul><li>Hsm 的 nShield 系列</li><li>nShield 即服務</ul>|**方法1：** [nCipher BYOK](hsm-protected-keys-ncipher.md) （具有金鑰匯入和 HSM 驗證的強式證明）<br/>**方法2：** [使用 new BYOK 方法](hsm-protected-keys-byok.md) |
|Thales|製造商|<ul><li>Luna HSM 7 系列（含固件版本7.3 或更新版本）</li></ul>| [使用新的 BYOK 方法](hsm-protected-keys-byok.md)|
|Fortanix|負責<br/>HSM 即服務|<ul><li>自我保護金鑰管理服務（SDKMS）</li><li>Equinix SmartKey</li></ul>|[使用新的 BYOK 方法](hsm-protected-keys-byok.md)|
|Marvell|製造商|所有具有的 LiquidSecurity Hsm<ul><li>2.0.4 版或更新版本的固件</li><li>固件3.2 版或更新版本</li></ul>|[使用新的 BYOK 方法](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV （企業金鑰管理系統）|多個 HSM 品牌和型號，包括<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[如需詳細資訊，請參閱 Cryptomathic 網站](https://www.cryptomathic.com/azurebyok)|[使用新的 BYOK 方法](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>後續步驟

* 遵循[Key Vault 最佳作法](../general/best-practices.md)，以確保金鑰的安全性、耐久性和監視。
* 如需新 BYOK 方法的完整說明，請參閱[BYOK 規格](https://docs.microsoft.com/azure/key-vault/keys/byok-specification)
