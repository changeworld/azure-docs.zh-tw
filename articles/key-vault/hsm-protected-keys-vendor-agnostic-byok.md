---
title: 如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰 - Azure Key Vault | Microsoft Docs
description: 使用這份文件協助您規劃、產生，並傳輸受 HSM 保護的金鑰，以搭配 Azure 金鑰保存庫使用。 也稱為 BYOK 或「攜帶您自己的金鑰」。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: bd70cfb58c9d89f1d454537721e22f36b1fd3d3e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429289"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>將受 HSM 保護的金鑰匯入 Key Vault （預覽）

> [!NOTE]
> 這項功能目前為預覽狀態，且僅適用于**美國東部 2 EUAP**和**美國中部 EUAP**區域。 

若要在使用 Azure Key Vault 時增加保證，您可以在不離開 HSM 界限的硬體安全性模組（Hsm）中匯入或產生金鑰。 此案例通常稱為 *自備金鑰*或 BYOK。 Azure Key Vault 使用 Hsm 的 nCipher nShield 系列（已驗證 FIPS 140-2 Level 2）來保護您的金鑰。

使用本主題中的資訊，協助您規劃、產生然後傳送自己受 HSM 保護的金鑰，以搭配使用 Azure 金鑰保存庫。

> [!NOTE]
> 此功能不適用於 Azure 中國世紀。 
> 
> 此匯入方法僅適用于[支援的 hsm](#supported-hsms)。 

如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](key-vault-overview.md)  如需入門教學課程 (包括建立受 HSM 保護之金鑰的金鑰保存庫)，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。

## <a name="overview"></a>概觀

* 在 key vault 中產生金鑰（稱為「金鑰交換金鑰」或 KEK）。 這必須是具有「匯入」的 RSA HSM 金鑰，做為唯一的金鑰操作。 只有 key vault premium SKU 支援 RSA-HSM 金鑰。
* 以 pem 檔案的形式下載 KEK 的公開金鑰
* 將 KEK 公開金鑰傳輸到連線到內部部署 HSM 的離線工作站。
* 從您的離線工作站，使用 HSM 廠商提供的 BYOK 工具來建立 BYOK 檔案。 
* 目標金鑰會以 KEK 加密，在傳輸到 Azure Key Vault Hsm 之前會保持加密狀態。 只有金鑰的加密版本會離開內部部署 HSM。
* 在 Azure Key Vault Hsm 內產生的 KEK，而且無法匯出。 Hsm 會強制 Key Vault Hsm 以外的 KEK 不能有明確的版本。
* KEK 必須位於要匯入目標金鑰的相同金鑰保存庫中。
* 將 BYOK 檔案上傳至 Key Vault 時，Key Vault Hsm 會使用 KEK 私密金鑰來解密目標金鑰內容，並將它匯入為 HSM 金鑰。 這項作業完全在 Key Vault Hsm 內部執行，而目標金鑰一律會保留在 HSM 保護界限內。

## <a name="prerequisites"></a>Prerequisites

請參閱下表的必要條件清單以取得 Azure 金鑰保存庫的自備金鑰 (BYOK)。

| 需求 | 詳細資訊 |
| --- | --- |
| Azure 訂用帳戶 |若要建立 Azure 金鑰保存庫，您需要 Azure 訂用帳戶： [註冊免費試用](https://azure.microsoft.com/pricing/free-trial/) |
| 用來匯入受 HSM 保護之金鑰的金鑰保存庫（Premium SKU） |如需 Azure 金鑰保存庫的服務層級和功能的詳細資訊，請參閱 [Azure 金鑰保存庫價格](https://azure.microsoft.com/pricing/details/key-vault/) 網站。 |
| 來自支援的 Hsm 清單的 HSM，以及 HSM 廠商所提供的 BYOK 工具和指示 | 您必須能夠存取您的 Hsm 的硬體安全性模組和基本操作知識。 請參閱[支援的 hsm](#supported-hsms)。 |
| Azure CLI 2.0.82 或更新版本 | 如需詳細資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 。|

## <a name="supported-hsms"></a>支援的 Hsm

|HSM 廠商名稱|支援的 HSM 模型|其他詳細資料|
|---|---|---|
|Thales|SafeNet Luna HSM 7 系列，含7.3 或更新版本的固件| [SafeNet Luna BYOK 工具和檔](https://safenet.gemalto.com/blah-blah)|


> [!NOTE]
> 若要從 Hsm 的 nCipher nShield 系列匯入受 HSM 保護的金鑰，[請使用舊版 BYOK](hsm-protected-keys-legacy.md)程式


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>產生金鑰並將其傳輸至 Azure 金鑰保存庫 HSM

您將使用下列步驟來產生金鑰，並將其傳輸至 Azure Key Vault HSM：

* [步驟1：產生 KEK](#step-1-generate-a-kek)
* [步驟2：下載 KEK 公開金鑰](#step-2-download-kek-public-key)
* [步驟3：產生並準備您的金鑰以進行傳輸](#step-3-generate-and-prepare-your-key-for-transfer)
* [步驟4：將您的金鑰傳輸至 Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>步驟1：產生 KEK

KEK （金鑰交換金鑰）是在 Key Vault 的 HSM 中產生的 RSA 金鑰。 這個金鑰是用來加密要匯入的金鑰（目標金鑰）。

KEK 必須是：
1. **RSA-HSM**金鑰（2048位或3072位或4096位）
2. 在您要匯入目標金鑰的相同金鑰保存庫中產生
3. 建立時允許的金鑰作業設定為匯**入**

使用[az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令來建立 KEK，並將金鑰作業設定為 [匯入]。 記下下列命令所傳回的金鑰識別碼 ' 小孩 '。 您將在[步驟 3](#step-3-generate-and-prepare-your-key-for-transfer)中需要它。


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>步驟2：下載 KEK 公開金鑰

使用[az keyvault key 下載](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download)，將 KEK 公開金鑰下載到 pem 檔案中。 您匯入的目標金鑰會使用 KEK 公開金鑰進行加密。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

將 KEKforBYOK. publickey. pem 檔案傳送到您的離線工作站。 在下一個步驟中，您將需要此檔案。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>步驟3：產生並準備您的金鑰以進行傳輸

請參閱 HSM 廠商的檔，以下載並安裝 BYOK 工具。 遵循 HSM 廠商提供的指示來產生目標金鑰，然後建立金鑰傳輸套件（BYOK 檔案）。 BYOK 工具會使用步驟[1](#step-1-generate-a-kek)中所下載的金鑰識別碼，以及您在[步驟 2](#step-2-download-kek-public-key)下載的 KEKforBYOK. publickey. PEM 檔案，在 BYOK 檔案中產生加密的目標金鑰。

將 BYOK 檔案傳送到已連線的工作站。

> [!NOTE] 
> 目標金鑰必須是2048位或3072位或4096位大小的 RSA 金鑰。 目前不支援匯入橢圓曲線索引鍵。
> <br/><strong>已知問題：</strong>從 SafeNet Luna Hsm 匯入 RSA 4K 目標金鑰失敗。 解決此問題之後，將會更新此檔。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>步驟4：將您的金鑰傳輸至 Azure Key Vault

在最後一個步驟中，請將金鑰傳輸套件（BYOK 檔案）從中斷連線的工作站轉移到網際網路連線的工作站，然後使用[az keyvault Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令將 BYOK 檔案上傳 Azure Key Vault HSM，以完成金鑰匯入。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上傳成功，您會看到顯示您剛匯入的金鑰屬性。

## <a name="next-steps"></a>後續步驟

您現在可以在您的金鑰保存庫中使用這個受 HSM 保護的金鑰。 如需詳細資訊，請參閱此價格與功能[比較](https://azure.microsoft.com/pricing/details/key-vault/)。
