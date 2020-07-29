---
title: 攜帶您自己的金鑰規格-Azure Key Vault |Microsoft Docs
description: 本檔說明了攜帶您自己的金鑰規格。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 80796d852c07952b7100c6dd7802bc9279f3218c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84198785"
---
# <a name="bring-your-own-key-specification"></a>「攜帶您自己的金鑰」規格

本檔說明將受 HSM 保護的金鑰從客戶的內部部署 Hsm 匯入 Key Vault 的規格。

## <a name="scenario"></a>狀況

Key Vault 客戶想要安全地將金鑰從 Azure 外部的內部部署 HSM 傳輸至 HSM 支援 Azure Key Vault。 匯入 Key Vault 外部產生之金鑰的程式，通常稱為攜帶您自己的金鑰（BYOK）。

以下是需求：
* 要傳輸的金鑰永遠不會以純文字形式存在於 HSM 外部。
* 在 HSM 外，要傳輸的金鑰一律受到 Azure Key Vault HSM 中保留的金鑰保護

## <a name="terminology"></a>詞彙

|金鑰名稱|索引鍵類型|來源|Description|
|---|---|---|---|
|金鑰交換金鑰（KEK）|RSA|Azure Key Vault HSM|在 Azure Key Vault 中產生的 HSM 支援的 RSA 金鑰組
包裝金鑰|AES|廠商 HSM|HSM 內部內部部署所產生的 [暫時] AES 金鑰
目標金鑰|RSA、EC、AES|廠商 HSM|要傳輸至 Azure Key Vault HSM 的金鑰

**金鑰交換金鑰**：客戶在將匯入 BYOK 金鑰的金鑰保存庫中產生的 HSM 備份金鑰。 此 KEK 必須具有下列屬性：

* 它是一個 RSA HSM 金鑰（4096位或3072位或2048位）
* 它將會有固定的 key_ops （僅限「匯入」），只允許在 BYOK 期間使用它
* 必須位於將匯入目標金鑰的同一個保存庫中

## <a name="user-steps"></a>使用者步驟

若要執行金鑰傳輸，使用者會執行下列步驟：

1. 產生 KEK。
2. 取出 KEK 的公開金鑰。
3. 使用 HSM 廠商提供的 BYOK 工具-將 KEK 匯入目標 HSM，並匯出 KEK 所保護的目標金鑰。
4. 將受保護的目標金鑰匯入 Azure Key Vault。

客戶使用 HSM 廠商所提供的 BYOK 工具和檔來完成步驟3。 它會產生金鑰傳輸 Blob （". byok" 檔案）。


## <a name="hsm-constraints"></a>HSM 條件約束

現有的 HSM 可能會對其管理的金鑰套用條件約束，包括：
* HSM 可能必須設定為允許以金鑰包裝為基礎的匯出
* 目標金鑰可能必須標示為 HSM 的 CKA_EXTRACTABLE，才能允許受控制的匯出
* 在某些情況下，KEK 和包裝金鑰可能需要標示為 CKA_TRUSTED。 這可讓它用來將金鑰包裝在 HSM 中。

來源 HSM 的設定通常是超出此規格的範圍。 Microsoft 預期 HSM 廠商會產生隨附于其 BYOK 工具的檔，以包含任何這類的設定步驟。

> [!NOTE]
> 下面所述的步驟1、2和4，可以使用其他介面（例如 Azure PowerShell 和 Azure 入口網站）來執行。 您也可以使用 Key Vault SDK 中的對等函式，以程式設計方式執行這些工作。

### <a name="step-1-generate-kek"></a>步驟1：產生 KEK

使用**az keyvault key create**命令來建立 KEK，並將金鑰作業設定為 [匯入]。 記下下列命令所傳回的金鑰識別碼 ' 小孩 '。

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>步驟2：取出 KEK 的公開金鑰

下載 KEK 的公開金鑰部分，並將它儲存到 PEM 檔案中。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>步驟3：使用 HSM 廠商提供的 BYOK 工具來產生金鑰傳輸 blob

客戶將會使用 HSM 廠商提供的 BYOK 工具來建立金鑰傳輸 blob （儲存為 ". BYOK" 檔案）。 KEK 公開金鑰（作為 pem 檔案）將是此工具的其中一項輸入。

#### <a name="key-transfer-blob"></a>金鑰傳輸 Blob
長期來說，Microsoft 想要使用 PKCS # 11 CKM_RSA_AES_KEY_WRAP 機制將目標金鑰傳輸到 Azure Key Vault，因為這項機制會產生單一 blob，而且更重要的是，中繼 AES 金鑰是由兩個 Hsm 處理，而且保證是暫時的。 這項機制目前無法在某些 Hsm 中使用，而是使用 AES 金鑰來保護目標金鑰的組合，以及透過 CKM_RSA_PKCS_OAEP 來保護 AES 金鑰的 CKM_AES_KEY_WRAP_PAD 會產生對等的 blob。

目標金鑰純文字取決於索引鍵類型： 
* 就 RSA 金鑰而言，私密金鑰 asn.1 DER 編碼 [RFC3447] 包裝在 PKCS # 8 中 [RFC5208] 
* 針對 EC 金鑰，私密金鑰 asn.1 DER 編碼 [RFC5915] 包裝在 PKCS # 8 中 [RFC5208]
* 若為八位金鑰，則為金鑰的原始位元組

然後，使用 CKM_RSA_AES_KEY_WRAP 機制來轉換純文字金鑰的位元組：
* 系統會使用具有 SHA1 的 RSA-OAEP，以包裝 RSA 金鑰來產生並加密暫時的 AES 金鑰。
* 編碼的純文字金鑰會使用 AES 金鑰以填補來加密，並使用 aes 金鑰換行。
* 加密的 AES 金鑰和加密的純文字金鑰會串連，以產生最終的加密文字 blob。

傳輸 blob 的格式會使用 JSON Web 加密 compact 序列化（RFC7516），主要是用來將必要的中繼資料傳遞給服務以進行正確解密的車輛。

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

* 小孩 = KEK 的金鑰識別碼。 對於 Key Vault 金鑰，它看起來像這樣：https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = 演算法。 
* dir = Direct 模式，亦即，參考的孩子用來直接保護加密文字，這是精確的標記法 CKM_RSA_AES_KEY_WRAP
* 產生器 = 參考欄位，表示 BYOK 工具的名稱和版本，以及來源 HSM 製造商和型號。 這項資訊的目的是要用於疑難排解和支援。

JSON blob 會儲存在副檔名為 "byok" 的檔案中，因此，當使用 ' AzKeyVaultKey ' （PSH）或 ' az keyvault key import ' （CLI）命令時，Azure PowerShell/CLI 用戶端會正確地處理它。

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>步驟4：上傳金鑰傳輸 blob 以匯入 HSM 金鑰

客戶會將金鑰傳輸 Blob （". byok" 檔案）傳送到線上工作站，然後執行**az keyvault Key import**命令，將此 Blob 當做新的 HSM 備份金鑰匯入 Key Vault。 

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
"key_hsm" 值是以 Base64 格式編碼的 KeyTransferPackage-ContosoFirstHSMkey. byok 的完整內容。

## <a name="references"></a>參考資料

### <a name="azure-key-vault-rest-api"></a>Azure Key Vault REST API

* [建立金鑰](https://docs.microsoft.com/rest/api/keyvault/createkey/createkey)
* [取得索引鍵（僅限索引鍵屬性和公用金鑰）](https://docs.microsoft.com/rest/api/keyvault/getkey/getkey)
* [匯入金鑰](https://docs.microsoft.com/rest/api/keyvault/importkey/importkey)


### <a name="azure-cli-commands"></a>Azure CLI 命令
* [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)
* [az keyvault key 下載](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download)
* [az keyvault key import](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)


## <a name="next-steps"></a>後續步驟
* 逐步 BYOK 指示：[將受 HSM 保護的金鑰匯入至 Key Vault （BYOK）](hsm-protected-keys-byok.md)

