---
title: 如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰 - Azure Key Vault | Microsoft Docs
description: 使用本文可説明您規劃、生成和傳輸自己的受 HSM 保護的金鑰，以便與 Azure 金鑰保存庫一起使用。 也稱為自帶金鑰 （BYOK）。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080143"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>將受 HSM 保護金鑰匯入 Key Vault (預覽)

> [!NOTE]
> 此功能處於預覽狀態，僅在 Azure 區域中可用，*東部美國 2 EUAP*和美國*中部 EUAP。* 

為了在使用 Azure 金鑰保存庫時添加保證，可以在硬體安全模組 （HSM） 中導入或生成金鑰;金鑰永遠不會離開 HSM 邊界。 此方案通常稱為*自帶金鑰*（BYOK）。 金鑰保存庫使用 nCipher nShield 系列 HSM（FIPS 140-2 級驗證）來保護金鑰。

使用本文中的資訊可説明您規劃、生成和傳輸自己的受 HSM 保護的金鑰，以便與 Azure 金鑰保存庫一起使用。

> [!NOTE]
> 此功能不適用於 Azure 中國 21Vianet。 
> 
> 此導入方法僅適用于[受支援的 HSM。](#supported-hsms) 

有關詳細資訊，以及如何開始使用金鑰保存庫（包括如何為受 HSM 保護的金鑰創建金鑰保存庫）的教程，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-overview.md)

## <a name="overview"></a>總覽

以下是程序概觀。 本文稍後將介紹要完成的具體步驟。

* 在金鑰保存庫中，生成金鑰（稱為*金鑰交換金鑰*（KEK）。 KEK 必須是僅具有金鑰操作的`import`RSA-HSM 金鑰。 只有金鑰保存庫高級 SKU 支援 RSA-HSM 金鑰。
* 將 KEK 公開金鑰下載為 .pem 檔。
* 將 KEK 公開金鑰傳輸到連接到本地 HSM 的離線電腦。
* 在離線電腦中，使用 HSM 供應商提供的 BYOK 工具創建 BYOK 檔。 
* 目標金鑰使用 KEK 加密，KEK 保持加密，直到將其傳輸到金鑰保存庫 HSM。 只有金鑰的加密版本才會離開本地 HSM。
* 在金鑰保存庫 HSM 中生成的 KEK 不可匯出。 HSM 強制實施規則，即金鑰保存庫 HSM 之外不存在 KEK 的明確版本。
* KEK 必須位於將導入目標金鑰的同一金鑰保存庫中。
* 當 BYOK 檔上載到金鑰保存庫時，金鑰保存庫 HSM 使用 KEK 私密金鑰解密目標金鑰材料並將其導入為 HSM 金鑰。 此操作完全發生在金鑰保存庫 HSM 中。 目標金鑰始終保留在 HSM 保護邊界中。

## <a name="prerequisites"></a>Prerequisites

下表列出了在 Azure 金鑰保存庫中使用 BYOK 的先決條件：

| 需求 | 詳細資訊 |
| --- | --- |
| Azure 訂用帳戶 |要在 Azure 金鑰保存庫中創建金鑰保存庫，需要 Azure 訂閱。 [註冊免費試用](https://azure.microsoft.com/pricing/free-trial/)。 |
| 用於導入 HSM 保護金鑰的金鑰保存庫高級 SKU |有關 Azure 金鑰保存庫中的服務層和功能的詳細資訊，請參閱[金鑰保存庫定價](https://azure.microsoft.com/pricing/details/key-vault/)。 |
| 支援 HSM 清單的 HSM 和 BYOK 工具和 HSM 供應商提供的說明 | 您必須擁有 HSM 的許可權，並具備如何使用 HSM 的基本知識。 請參閱[支援的 HSM](#supported-hsms)。 |
| Azure CLI 版本 2.1.0 或更高版本 | 請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。|

## <a name="supported-hsms"></a>支援 HSM

|供應商名稱|供應商類型|支援的 HSM 型號|詳細資訊|
|---|---|---|---|
|泰萊斯|製造商|SafeNet Luna HSM 7 系列，固件版本 7.3 或更高版本| [SafeNet Luna BYOK 工具和文檔](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|福塔尼克斯|HSM 作為服務|自防金鑰管理服務 （SDKMS）|[將 SDKMS 金鑰匯出到 BYOK 的雲供應商 - Azure 金鑰保存庫](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> 要從 nCipher nShield 系列導入受 HSM 保護的金鑰，請使用[傳統的 BYOK 過程](hsm-protected-keys-legacy.md)。

## <a name="supported-key-types"></a>支援金鑰類型

|機碼名稱|金鑰類型|金鑰大小|來源|描述|
|---|---|---|---|---|
|金鑰交換金鑰 （KEK）|RSA| 2，048 位<br />3，072 位<br />4，096 位|Azure 金鑰保存庫 HSM|在 Azure 金鑰保存庫中生成的由 HSM 支援的 RSA 金鑰組|
|目標金鑰|RSA|2，048 位<br />3，072 位<br />4，096 位|供應商 HSM|要傳輸到 Azure 金鑰保存庫 HSM 的金鑰|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>生成金鑰並將其傳輸到金鑰保存庫 HSM

要生成金鑰並將其傳輸到金鑰保存庫 HSM：

* [第 1 步：生成 KEK](#step-1-generate-a-kek)
* [第 2 步：下載 KEK 公開金鑰](#step-2-download-the-kek-public-key)
* [第 3 步：生成並準備金鑰進行傳輸](#step-3-generate-and-prepare-your-key-for-transfer)
* [步驟 4：將金鑰傳輸到 Azure 金鑰保存庫](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>第 1 步：生成 KEK

KEK 是在金鑰保存庫 HSM 中生成的 RSA 金鑰。 KEK 用於加密要導入的金鑰（*目標*金鑰）。

KEK 必須：
- RSA-HSM 鍵（2，048 位;3，072 位;或 4，096 位）
- 在要導入目標金鑰的同一金鑰保存庫中生成
- 使用允許的鍵操作設置為`import`

> [!NOTE]
> KEK 必須具有"導入"作為唯一允許的金鑰操作。 "導入"與所有其他關鍵操作是互斥的。

使用[az 金鑰庫金鑰創建](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令創建將鍵操作設置為`import`的 KEK。 記錄從以下命令返回`kid`的金鑰識別碼 （ ）。 （您將使用步驟`kid`[3](#step-3-generate-and-prepare-your-key-for-transfer)中的值 。）

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>第 2 步：下載 KEK 公開金鑰

使用[az 金鑰庫金鑰下載](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download)將 KEK 公開金鑰下載到 .pem 檔。 使用 KEK 公開金鑰加密您導入的目標金鑰。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

將 KEKforBYOK.publickey.pem 檔案傳輸到離線電腦。 在下一步中，您將需要此檔。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>第 3 步：生成並準備金鑰進行傳輸

請參閱 HSM 供應商的文檔以下載並安裝 BYOK 工具。 按照 HSM 供應商的說明生成目標金鑰，然後創建金鑰傳輸包（BYOK 檔）。 BYOK 工具將使用`kid`從步驟[1](#step-1-generate-a-kek)和 KEKforBYOK.publickey.pem 檔在步驟[2](#step-2-download-the-kek-public-key)中下載，以在 BYOK 檔中生成加密的目標金鑰。

將 BYOK 檔案傳輸到已連接的電腦。

> [!NOTE] 
> 不支援導入 RSA 1，024 位金鑰。 目前，不支援導入橢圓曲線 （EC） 鍵。
> 
> **已知問題**：僅支援從 SafeNet Luna HSM 導入 RSA 4K 目標金鑰，但固件 7.4.0 或更高版本才受支援。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>步驟 4：將金鑰傳輸到 Azure 金鑰保存庫

要完成金鑰導入，請將金鑰傳輸包（BYOK 檔）從斷開連接的電腦傳輸到聯網電腦。 使用[az 金鑰庫金鑰導入](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令將 BYOK 檔上載到金鑰保存庫 HSM。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上載成功，Azure CLI 將顯示導入金鑰的屬性。

## <a name="next-steps"></a>後續步驟

您現在可以在您的金鑰保存庫中使用這個受 HSM 保護的金鑰。 有關詳細資訊，請參閱[此價格和功能比較](https://azure.microsoft.com/pricing/details/key-vault/)。



