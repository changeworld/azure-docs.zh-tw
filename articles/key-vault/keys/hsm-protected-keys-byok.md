---
title: 如何產生並傳輸受 HSM 保護的金鑰 - 攜帶您自己的金鑰 - Azure Key Vault
description: 使用這份文件協助您規劃、產生，並傳輸受 HSM 保護的金鑰，以搭配 Azure 金鑰保存庫使用。 也稱為「攜帶您自己的金鑰 (BYOK)」。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 1869ec9b617a7451ec42fa9d092ea3bb5834f9e8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585469"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>將受 HSM 保護的金鑰匯入 Key Vault (BYOK)

為了加強保證，使用 Azure Key Vault 時，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。 此案例通常稱為「攜帶您自己的金鑰」(BYOK)。 Key Vault 使用 HSM 的 nCipher nShield 系列 (已經過 FIPS 140-2 Level 2 驗證) 來保護您的金鑰。

使用本文中的資訊，協助您規劃、產生然後傳送自己受 HSM 保護的金鑰，以搭配使用 Azure 金鑰保存庫。

> [!NOTE]
> 此功能不適用於 Azure China 21Vianet。 
> 
> 此匯入方法僅適用於[支援的 HSM](#supported-hsms)。 

如需詳細資訊，以及開始使用 Key Vault 的教學課程 (包括如何為受 HSM 保護的金鑰建立金鑰保存庫)，請參閱[什麼是 Azure Key Vault？](../general/overview.md)。

## <a name="overview"></a>概觀

以下是程序概觀。 本文稍後會說明要完成的特定步驟。

* 在 Key Vault 中產生金鑰 (稱為「金鑰交換金鑰」 (KEK))。 KEK 必須是只能進行 `import` 金鑰作業的 RSA HSM 金鑰。 只有 Key Vault Premium SKU 支援 RSA-HSM 金鑰。
* 將 KEK 金鑰下載為 .pem 檔案。
* 將 KEK 公開金鑰傳送至連線到內部部署 HSM 的離線電腦。
* 在離線電腦中，使用 HSM 廠商提供的 BYOK 工具來建立 BYOK 檔案。 
* 此目標金鑰利用 KEK 加密，且加密狀態會維持到金鑰傳輸至 Key Vault HSM 為止。 只有加密版本的金鑰可以離開內部部署 HSM。
* 在 Key Vault HSM 內產生的 KEK 無法匯出。 HSM 會強制執行規則，指出除了 Key Vault HSM 以外沒有任何明確的 KEK 版本存在。
* KEK 必須位於要匯入目標金鑰的相同金鑰保存庫中。
* 將 BYOK 檔案上傳至 Key Vault 時，Key Vault HSM 會使用 KEK 的私密金鑰來解密目標金鑰內容，並匯入為 HSM 金鑰。 此作業完全在 Key Vault HSM 內執行。 目標金鑰一律會保留在 HSM 保護界限內。

## <a name="prerequisites"></a>必要條件

下表列出在 Azure Key Vault 中使用 BYOK 的必要條件：

| 需求 | 更多資訊 |
| --- | --- |
| Azure 訂用帳戶 |若要在 Azure Key Vault 中建立金鑰保存庫，您需要 Azure 訂用帳戶。 [註冊以免費試用](https://azure.microsoft.com/pricing/free-trial/)。 |
| 要匯入受 HSM 保護金鑰的 Key Vault Premium SKU |如需 Azure 金鑰保存庫的服務層級和功能的詳細資訊，請參閱 [Key Vault 定價](https://azure.microsoft.com/pricing/details/key-vault/)。 |
| 支援的 HSM 清單中的 HSM，以及 HSM 廠商提供的 BYOK 工具和指示 | 您必須具備 HSM 的權限，以及如何使用 HSM 的基本知識。 請參閱[支援的 HSM](#supported-hsms)。 |
| Azure CLI 2.1.0 版或更新版本 | 請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。|

## <a name="supported-hsms"></a>支援的 HSM

|廠商名稱|廠商類型|支援的 HSM 模型|詳細資訊|
|---|---|---|---|
|nCipher|製造商，<br/>HSM 即服務|<ul><li>HSM 的 nShield 系列</li><li>nShield 即服務</ul>|[nCipher 新的 BYOK 工具和文件](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|製造商|<ul><li>Luna HSM 7 系列 (含韌體版本7.3 或更新版本)</li></ul>| [Luna BYOK 工具和文件](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|製造商，<br/>HSM 即服務|<ul><li>自我保護金鑰管理服務 (SDKMS)</li><li>Equinix SmartKey</li></ul>|[將 SDKMS 金鑰匯出至 BYOK 的雲端提供者 - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|製造商|所有 LiquidSecurity HSM，具有<ul><li>韌體版本 2.0.4 或更新版本</li><li>韌體版本 3.2 或更新版本</li></ul>|[Marvell BYOK 工具和文件](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (企業金鑰管理系統)|多個 HSM 品牌和型號，包括<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>如需詳細資訊，請參閱 [Cryptomathic 網站](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK 工具和文件](https://www.cryptomathic.com/azurebyok)|



## <a name="supported-key-types"></a>支援的金鑰類型

|機碼名稱|金鑰類型|金鑰大小|來源|描述|
|---|---|---|---|---|
|金鑰交換金鑰 (KEK)|RSA| 2048 位元<br />3072 位元<br />4096 位元|Azure Key Vault HSM|在 Azure Key Vault 中產生之受 HSM 支援的 RSA 金鑰組|
|目標金鑰|RSA|2048 位元<br />3072 位元<br />4096 位元|廠商 HSM|要傳輸至 Azure Key Vault HSM 的金鑰|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>產生金鑰並傳輸至 Key Vault HSM

要產生金鑰並傳輸至 Key Vault HSM：

* [步驟 1：產生 KEK](#step-1-generate-a-kek)
* [步驟 2：下載 KEK 公開金鑰](#step-2-download-the-kek-public-key)
* [步驟 3：產生並準備您的金鑰以進行傳輸](#step-3-generate-and-prepare-your-key-for-transfer)
* [步驟 4：將金鑰傳輸至 Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>步驟 1:產生 KEK

KEK 是在 Key Vault HSM 中產生的 RSA 金鑰。 KEK 用來加密您要匯入的金鑰 (「目標」金鑰)。

KEK 必須是：
- RSA-HSM 金鑰 (2048 位元；3072 位元或 4096 位元)
- 在您要匯入目標金鑰的相同金鑰保存庫中產生
- 建立時允許的金鑰作業設定為 `import`

> [!NOTE]
> 「匯入」必須是 KEK 唯一允許的金鑰作業。 「匯入」與所有其他金鑰作業互斥。

使用 [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 命令，建立將金鑰作業設定為 `import` 的 KEK。 記錄從下列命令傳回的金鑰識別碼 (`kid`)。 (您將使用[步驟 3](#step-3-generate-and-prepare-your-key-for-transfer) 中的 `kid` 值。)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>步驟 2:下載 KEK 公開金鑰

使用 [az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) 將 KEK 公開金鑰下載至 .pem 檔案。 您匯入的目標金鑰會使用 KEK 公開金鑰加密。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

將 KEKforBYOK.publickey.pem 檔案傳送到您的離線電腦。 您會在下一個步驟中使用此檔案。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>步驟 3：產生並準備您的金鑰以進行傳輸

請參閱 HSM 廠商的文件，以下載並安裝 BYOK 工具。 遵循 HSM 廠商提供的指示來產生目標金鑰，然後建立金鑰傳輸套件 (BYOK 檔案)。 BYOK 工具會使用[步驟 1](#step-1-generate-a-kek) 的 `kid` 以及[步驟 2](#step-2-download-the-kek-public-key) 中下載的 KEKforBYOK.publickey.pem 檔案，在 BYOK 檔案中產生加密的目標金鑰。

將 BYOK 檔案傳送到已連線的電腦。

> [!NOTE] 
> 不支援匯入 RSA 1024 位元金鑰。 目前不支援匯入Elliptic Curve (EC) 金鑰。
> 
> **已知問題**：只有韌體 7.4.0 或更新版本才支援從 Luna HSM 匯入 RSA 4K 目標金鑰。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>步驟 4：將金鑰傳輸至 Azure Key Vault

若要完成金鑰匯入，請將金鑰傳輸套件 (BYOK 檔案) 從已中斷連線的電腦傳輸到連線網際網路的電腦。 使用 [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 命令，將 BYOK 檔案上傳至 Key Vault HSM。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上傳成功，Azure CLI 會顯示匯入金鑰的屬性。

## <a name="next-steps"></a>後續步驟

您現在可以在您的金鑰保存庫中使用這個受 HSM 保護的金鑰。 如需詳細資訊，請參閱[本價格和功能比較](https://azure.microsoft.com/pricing/details/key-vault/)。



