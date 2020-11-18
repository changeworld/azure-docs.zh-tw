---
title: 關於金鑰 - Azure Key Vault
description: 概述 Azure Key Vault REST 介面和開發人員在金鑰方面的詳細資料。
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 2ae7b28d5e9e7a520ee8cbd090b6681d5ad7015a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422751"
---
# <a name="about-keys"></a>關於金鑰

Azure Key Vault 提供兩種資源類型，以儲存和管理密碼編譯金鑰：

|資源類型|金鑰保護方法|資料平面端點基底 URL|
|--|--|--|
| **保存庫** | 受軟體保護<br/><br/>及<br/><br/>受 HSM 保護 (具有進階 SKU)</li></ul> | https://{vault-name}.vault.azure.net |
| **受控 HSM 集區** | 受 HSM 保護 | https://{hsm-name}.managedhsm.azure.net |
||||

- **保存庫** - 保存庫提供低成本、易於部署、多租用戶、區域復原 (如果有的話)、高可用性金鑰管理解決方案，適用於最常見的雲端應用程式案例。
- **受控 HSM** 受控 HSM 提供單一租用戶、區域復原 (如果有的話)、高可用性 HSM 來儲存和管理您的密碼編譯金鑰。 最適合處理高價值金鑰的應用程式和使用案例。 也有助於符合最嚴格的安全性、合規性和法規需求。 

> [!NOTE]
> 除了密碼編譯金鑰之外，保存庫也可讓您儲存及管理數種類型的物件，例如祕密、憑證和儲存體帳戶金鑰。

Key Vault 中的密碼編譯金鑰會表示為 JSON Web 金鑰 [JWK] 物件。 JavaScript 物件標記法 (JSON) 和 JavaScript 物件簽章與加密 (JOSE) 規格如下：

-   [JSON Web 金鑰 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web 加密 (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web 演算法 (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web 簽章 (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

基底 JWK/JWA 規格也可延伸，讓Azure Key Vault 和受控 HSM 實作使用特有的金鑰類型。 

受 HSM 保護的金鑰 (也稱為 HSM 金鑰) 會在 HSM (硬體安全模組) 中進行處理，一律維持 HSM 保護界限。 

- 保存庫會使用 **FIPS 140-2 層級 2** 已驗證 HSM 來保護共用 HSM 後端基礎結構中的 HSM 金鑰。 
- 受控 HSM 集區會使用 **FIPS 140-2 層級 3** 已驗證 HSM 模組來保護您的金鑰。 每個 HSM 集區都是一個隔離的單一租用戶執行個體，具有自己的[安全性網域](../managed-hsm/security-domain.md)，提供與共用相同硬體基礎結構的其他所有 HSM 集區的完整密碼編譯隔離。

這些金鑰會在單一租用戶 HSM 集區中受到保護。 您可以匯入軟體形式的 RSA、EC 和對稱金鑰，或從支援的 HSM 裝置匯出。 您也可以在 HSM 集區中產生金鑰。 當您使用 [BYOK (攜帶您自己的金鑰) 規格](../keys/byok-specification.md)中所述方法匯入 HSM 金鑰時，會對受控 HSM 集區啟用安全傳輸金鑰材質。 

如需地理界限的詳細資訊，請參閱 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>金鑰類型和保護方法

Key Vault 支援 RSA、EC 和對稱金鑰。 

### <a name="hsm-protected-keys"></a>受 HSM 保護的金鑰

|金鑰類型|保存庫 (僅限進階 SKU)|受控 HSM 集區|
|--|--|--|--|
**EC-HSM**：橢圓曲線金鑰|FIPS 140-2 層級 2 HSM|FIPS 140-2 層級 3 HSM
**RSA-HSM**：RSA 金鑰|FIPS 140-2 層級 2 HSM|FIPS 140-2 層級 3 HSM
**oct-HSM**：對稱|不支援|FIPS 140-2 層級 3 HSM
||||

### <a name="software-protected-keys"></a>受軟體保護的金鑰

|金鑰類型|保存庫|受控 HSM 集區|
|--|--|--|--|
**RSA**：「受軟體保護」的 RSA 金鑰|FIPS 140-2 層級 1|不支援
**EC**：「受軟體保護」的橢圓曲線金鑰|FIPS 140-2 層級 1|不支援
||||

如需每個金鑰類型、演算法、作業、屬性和標籤的詳細資訊，請參閱[金鑰類型、演算法和作業](about-keys-details.md)。

## <a name="next-steps"></a>後續步驟
- [關於 Key Vault](../general/overview.md)
- [關於受控 HSM](../managed-hsm/overview.md)
- [關於秘密](../secrets/about-secrets.md)
- [關於憑證](../certificates/about-certificates.md)
- [Key Vault REST API 概觀](../general/about-keys-secrets-certificates.md)
- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)