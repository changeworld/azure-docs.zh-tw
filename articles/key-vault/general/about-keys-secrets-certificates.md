---
title: Azure Key Vault 的金鑰、祕密與憑證概觀
description: 概述 Azure Key Vault REST 介面和開發人員在金鑰、祕密和憑證方面的詳細資料。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: c02a2658325fdd88ef1052937edc3b84c4545872
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296832"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Azure Key Vault 的金鑰、祕密與憑證概觀

Azure Key Vault 可讓 Microsoft Azure 應用程式和使用者儲存及使用數種類型的祕密/金鑰資料。 Key Vault 資源提供者支援兩種資源類型：保存庫和受控 HSM。

## <a name="dns-suffixes-for-base-url"></a>基底 URL 的 DNS 尾碼
 下表顯示在各種雲端環境中，資料平面端點針對保存庫和受控 HSM 集區所使用的基底 URL DNS 尾碼。

雲端環境 | 保存庫的 DNS 尾碼 | 受控 HSM 的 DNS 尾碼
---|---|---
Azure 雲端 | .vault.azure.net | .managedhsm.azure.net
Azure China Cloud | .vault.azure.cn | 不支援
Azure 美國政府 | .vault.usgovcloudapi.net | 不支援
Azure 德國雲端 | .vault.microsoftazure.de | 不支援
|||


## <a name="object-types"></a>物件類型
 下表顯示基底 URL 中的物件類型及其尾碼。

物件類型|URL 尾碼|保存庫|受控 HSM 集區
--|--|--|--
**密碼編譯金鑰**||
受 HSM 保護的金鑰|/keys|支援|支援
受軟體保護的金鑰|/keys|支援|不支援
**其他物件類型**||
密碼|/secrets|支援|不支援
憑證|/certificates|支援|不支援
儲存體帳戶金鑰|/storageaccount|支援|不支援
|||
- **密碼編譯金鑰**：支援多種金鑰類型和演算法，可讓您使用受軟體保護和受 HSM 保護的金鑰。 如需詳細資訊，請參閱[關於金鑰](../keys/about-keys.md)。
- **祕密**：提供儲存祕密 (例如密碼和資料庫連接字串) 的安全機制。 如需詳細資訊，請參閱[關於祕密](../secrets/about-secrets.md)。
- **憑證**：支援以金鑰和祕密為基礎且新增自動更新功能的憑證。 如需詳細資訊，請參閱[關於憑證](../certificates/about-certificates.md)。
- **Azure 儲存體帳戶金鑰**：可為您管理「Azure 儲存體」帳戶的金鑰。 Key Vault 可在內部列出 (同步) 金鑰與 Azure 儲存體帳戶，以及定期重新產生 (輪替) 金鑰。 如需詳細資訊，請參閱[使用 Key Vault 管理儲存體帳戶金鑰](../secrets/overview-storage-keys.md)。

如需 Key Vault 的一般詳細資訊，請參閱[關於 Azure Key Vault](overview.md)。 如需受控 HSM 集區的詳細資訊，請參閱[Azure Key Vault 受控 HSM？](../managed-hsm/overview.md)


## <a name="data-types"></a>資料類型

請參閱 JOSE 規格，以了解金鑰、加密及簽章的相關資料類型。  

-   **演算法** - 支援的金鑰作業演算法，例如 RSA1_5  
-   **加密文字值** - 加密文字的八位元組，使用 Base64URL 編碼  
-   **摘要值** - 雜湊演算法的輸出，使用 Base64URL 編碼  
-   **金鑰類型** - 其中一種支援的金鑰類型，例如 RSA (Rivest-Shamir-Adleman)。  
-   **純文字值** - 純文字的八位元組，使用 Base64URL 編碼  
-   **簽章值** - 簽章演算法的輸出，使用 Base64URL 編碼  
-   **base64URL** - Base64URL [RFC4648] 編碼的二進位值  
-   **布林值** - True 或 False  
-   **身分識別** - Azure Active Directory (AAD) 中的身分識別。  
-   **IntDate** - JSON 十進位值，代表從 1970-01-01T0:0:0Z UTC 到指定 UTC 日期/時間的 的秒數。 如需關於一般日期/時間和特定 UTC 時間的詳細資料，請參閱 RFC3339。  

## <a name="objects-identifiers-and-versioning"></a>物件、識別碼和版本控制

儲存於 Key Vault 中的物件在每次有新的物件執行個體建立時，就會建立版本。 每個版本會有指派的唯一識別碼和 URL。 第一次建立的物件會獲得唯一的版本識別碼，並標示為物件的目前版本。 以相同物件名稱建立新的執行個體，會使新物件獲得唯一版本識別碼，並使其成為目前的版本。  

Key Vault 中的物件可以藉由指定版本來定址，或藉由對現行物件版本的作業省略版本來定址。 例如，假設某個金鑰的名稱為 `MasterKey`，在未指定版本的情況下執行作業時，系統就會使用最新的可用版本。 若以版本專屬的識別碼來執行作業，則系統就會使用該特定版本的物件。  

物件在 Key Vault 中可使用 URL 進行唯一識別。 系統中的任兩個物件都不會有相同的 URL，無論地理位置為何。 物件的完整 URL 稱為物件識別碼。 URL 包含可識別 Key Vault 的前置詞、物件類型、使用者提供的物件名稱和物件版本。 物件名稱不區分大小寫且無法變更。 不包含物件版本的識別碼稱為「基底識別碼」。  

如需詳細資訊，請參閱[驗證、要求與回應](authentication-requests-and-responses.md)

物件識別碼具有下列一般格式 (視容器類型而定)：  

- **針對保存庫**：`https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **針對受控 HSM 集區**：`https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> 如需每個容器類型所支援的物件類型，請參閱[物件類型支援](#object-types)。

其中：  

| 項目 | 描述 |  
|-|-|  
|`vault-name` 或 `hsm-name`|Microsoft Azure Key Vault 服務中保存庫或受控 HSM 集區的名稱。<br /><br />保存庫名稱和受控 HSM 集區名稱由使用者選取，且具有全域唯一性。<br /><br />保存庫名稱和受控 HSM 集區名稱必須是 3-24 個字元的字串，且只能包含 0-9、a-z、A-Z 和 -。|  
|`object-type`|物件的類型，「金鑰」、「秘密」或「憑證」。|  
|`object-name`|`object-name` 是由使用者提供的名稱，而且在 Key Vault 中必須是唯一的。 其名稱必須是 1-127 個字元的字串，開頭必須是字母，且只能包含 0-9、a-z、A-Z 和 -。|  
|`object-version`|`object-version` 是由系統產生的、包含 32 個字元的字串識別碼，可選擇性地用來當作物件的唯一版本。|  

## <a name="next-steps"></a>後續步驟

- [關於金鑰](../keys/about-keys.md)
- [關於秘密](../secrets/about-secrets.md)
- [關於憑證](../certificates/about-certificates.md)
- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
