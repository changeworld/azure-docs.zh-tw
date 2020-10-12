---
title: 攜帶您自己的金鑰規格-Azure Key Vault |Microsoft Docs
description: 本檔說明「攜帶您自己的金鑰」規格。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: feef35ef86a933f32949468366fea85eb87d4866
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315774"
---
# <a name="bring-your-own-key-specification"></a>「攜帶您自己的金鑰」規格

本檔說明將受 HSM 保護的金鑰從客戶的內部部署 Hsm 匯入 Key Vault 的規格。

## <a name="scenario"></a>狀況

Key Vault 客戶想要從 Azure 外部的內部部署 HSM 安全地傳輸金鑰到 HSM 支援 Azure Key Vault。 匯入 Key Vault 外部產生之金鑰的流程，通常稱為攜帶您自己的金鑰 (BYOK) 。

以下是需求：
* 要傳送的金鑰絕不會以純文字形式存在於 HSM 之外。
* 在 HSM 之外，要傳輸的金鑰一律受到 Azure Key Vault HSM 中保留的金鑰保護

## <a name="terminology"></a>詞彙

|金鑰名稱|索引鍵類型|來源|描述|
|---|---|---|---|
|金鑰交換金鑰 (KEK)|RSA|Azure Key Vault HSM|Azure Key Vault 產生 HSM 支援的 RSA 金鑰組
包裝金鑰|AES|廠商 HSM|HSM 內部內部部署產生的 [暫時] AES 金鑰
目標金鑰|RSA、EC、AES|廠商 HSM|要傳輸至 Azure Key Vault HSM 的金鑰

**金鑰交換金鑰**：客戶在要匯入 BYOK 金鑰的金鑰保存庫中產生的 HSM 支援金鑰。 此 KEK 必須具有下列屬性：

* 它是 (4096 位或3072位或2048位) 的 RSA HSM 金鑰
* 它會有固定的 key_ops 只 (「匯入」 ) ，只允許在 BYOK 時使用它
* 必須位於將匯入目標金鑰的相同保存庫中

## <a name="user-steps"></a>使用者步驟

若要執行金鑰傳輸，使用者需執行下列步驟：

1. 產生 KEK。
2. 取出 KEK 的公開金鑰。
3. 使用 HSM 廠商提供的 BYOK 工具-將 KEK 匯入目標 HSM，並匯出 KEK 所保護的目標金鑰。
4. 匯入受保護的目標金鑰以 Azure Key Vault。

客戶可以使用 HSM 廠商所提供的 BYOK 工具和檔來完成步驟3。 它會產生 ("byok" 檔案的金鑰傳輸 Blob) 。


## <a name="hsm-constraints"></a>HSM 條件約束

現有的 HSM 可能會對其所管理的金鑰套用條件約束，包括：
* HSM 可能必須設定為允許以金鑰換行為基礎的匯出
* 目標金鑰可能必須標示為 CKA_EXTRACTABLE，HSM 才能允許控制的匯出
* 在某些情況下，KEK 和包裝金鑰可能需要標記為 CKA_TRUSTED。 這可讓它用來包裝 HSM 中的金鑰。

來源 HSM 的設定通常是在此規格的範圍之外。 Microsoft 預期 HSM 廠商會產生其 BYOK 工具隨附的檔，以包含任何這類設定步驟。

> [!NOTE]
> 您可以使用其他介面（例如 Azure PowerShell 和 Azure 入口網站）來執行以下所述的步驟1、2和4。 您也可以使用 Key Vault SDK 中的對等函式，以程式設計方式執行這些工作。

### <a name="step-1-generate-kek"></a>步驟1：產生 KEK

使用 **az keyvault key create** 命令來建立 KEK，並將金鑰作業設定為匯入。 記下下列命令所傳回的金鑰識別碼「小孩」。

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>步驟2：取出 KEK 的公開金鑰

下載 KEK 的公開金鑰部分，並將它儲存到 PEM 檔案中。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>步驟3：使用 HSM 廠商提供的 BYOK 工具來產生金鑰傳輸 blob

客戶將使用 HSM 廠商提供的 BYOK 工具來建立金鑰傳輸 blob， (儲存為 ". BYOK" 檔案) 。 KEK 公開金鑰 (為 pem 檔案) 會是此工具的其中一個輸入。

#### <a name="key-transfer-blob"></a>金鑰傳輸 Blob
長期來說，Microsoft 想要使用 PKCS # 11 CKM_RSA_AES_KEY_WRAP 機制，將目標金鑰傳輸到 Azure Key Vault，因為這項機制會產生單一 blob，而且更重要的是，中繼 AES 金鑰是由兩個 Hsm 處理，並保證是暫時的。 這項機制目前無法在某些 Hsm 中使用，但使用 AES 金鑰保護目標金鑰與 CKM_AES_KEY_WRAP_PAD 的組合，以及使用 CKM_RSA_PKCS_OAEP 來保護 AES 金鑰，會產生對等的 blob。

目標金鑰純文字取決於金鑰類型： 
* 針對 RSA 金鑰，私密金鑰 ASN. 1 DER 編碼 [RFC3447] 包裝在 PKCS # 8 中 [RFC5208] 
* 若為 EC 機碼，私密金鑰的 asn.1 DER 編碼 [RFC5915] 會包裝在 PKCS # 8 中 [RFC5208]
* 若為八位金鑰，則為金鑰的原始位元組

然後使用 CKM_RSA_AES_KEY_WRAP 機制來轉換純文字金鑰的位元組：
* 系統會使用 RSA-具有 SHA1 的 RSA 來產生和加密暫時的 AES 金鑰。
* 編碼的純文字金鑰是使用 aes 金鑰搭配填補來加密。
* 系統會串連加密的 AES 金鑰和加密的純文字金鑰，以產生最終的加密文字 blob。

傳送 blob 的格式會使用 JSON Web 加密 compact 序列化 (RFC7516) 主要是將必要的中繼資料傳遞給服務以進行正確解密的車輛。

如果使用 CKM_RSA_AES_KEY_WRAP_PAD，則傳送 blob 的 JSON 序列化會是：

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* 小孩 = KEK 的金鑰識別碼。 針對 Key Vault 索引鍵，它看起來像這樣： https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = 演算法。 
* dir = Direct 模式，亦即，參考的小孩會用來直接保護加密文字，這是正確表示的 CKM_RSA_AES_KEY_WRAP
* 產生器 = 參考用欄位，代表 BYOK 工具的名稱和版本，以及來源 HSM 製造商和型號。 這項資訊的目的是要用於疑難排解和支援。

JSON blob 會儲存在副檔名為 ". byok" 的檔案中，因此 Azure PowerShell/CLI 用戶端會在使用 ' AzKeyVaultKey ' (PSH) 或 ' az keyvault key import ' (CLI) 命令時正確地加以處理。

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>步驟4：上傳金鑰傳輸 blob 以匯入 HSM 金鑰

客戶會將金鑰傳輸 Blob ( ". byok" 檔案) 傳送到線上工作站，然後執行 **az keyvault Key import** 命令，將此 Blob 作為新的 HSM 支援金鑰匯入 Key Vault。 

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```

執行上述命令時，會導致傳送 REST API 要求，如下所示：

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

要求本文：
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```
"key_hsm" 值是以 Base64 格式編碼的 KeyTransferPackage >contosofirsthsmkey. byok 的完整內容。

## <a name="references"></a>參考
- [Key Vault 開發人員指南](../general/developers-guide.md)

## <a name="next-steps"></a>後續步驟
* 逐步 BYOK 指示： [將受 HSM 保護的金鑰匯入 Key Vault (BYOK) ](hsm-protected-keys-byok.md)

