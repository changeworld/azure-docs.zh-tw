---
title: 如何產生及傳輸受 HSM 保護的金鑰，以用於 Azure Key Vault 受控 HSM-Azure Key Vault |Microsoft Docs
description: 您可以使用本文來協助您規劃、產生及傳輸您自己受 HSM 保護的金鑰，以搭配使用受管理的 HSM。 也稱為「攜帶您自己的金鑰 (BYOK)」。
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 39486b076f9284436207f823ea48fddc98bb48a0
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372841"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>將受 HSM 保護的金鑰匯入至受控 HSM (BYOK) 

 Azure Key Vault 受控 HSM 支援匯入內部部署硬體安全模組中產生的金鑰 (HSM) ;金鑰永遠不會離開 HSM 保護界限。 此案例通常稱為「攜帶您自己的金鑰」(BYOK)。 受控 HSM 會使用 (FIPS 140-2 層級3驗證) 的 Marvell LiquidSecurity HSM 介面卡來保護您的金鑰。

使用本文中的資訊可協助您規劃、產生及傳輸您自己的受 HSM 保護金鑰，以搭配使用受控 HSM。

> [!NOTE]
> 此功能不適用於 Azure China 21Vianet。 此匯入方法僅適用於[支援的 HSM](#supported-hsms)。 

如需詳細資訊，以及開始使用受控 HSM 的教學課程，請參閱 [什麼是受控 hsm？](overview.md)。

## <a name="overview"></a>概觀

以下是程序概觀。 本文稍後會說明要完成的特定步驟。

* 在受控 HSM 中， ( (KEK) # A3 的金鑰 *交換金鑰* 來產生金鑰。 KEK 必須是只能進行 `import` 金鑰作業的 RSA HSM 金鑰。 
* 將 KEK 金鑰下載為 .pem 檔案。
* 將 KEK 公開金鑰傳送至連線到內部部署 HSM 的離線電腦。
* 在離線電腦中，使用 HSM 廠商提供的 BYOK 工具來建立 BYOK 檔案。 
* 目標金鑰會以 KEK 加密，並在傳輸到受控 HSM 之前保持加密狀態。 只有加密版本的金鑰可以離開內部部署 HSM。
* 在受控 HSM 內產生的 KEK 不可匯出。 Hsm 會強制執行此規則，而不會有任何明確版本的 KEK 存在於受控 HSM 之外。
* KEK 必須位於將匯入目標金鑰的相同 managed HSM 中。
* 將 BYOK 檔案上傳到受控 HSM 時，受控 HSM 會使用 KEK 私密金鑰來解密目標金鑰內容，並將它匯入為 HSM 金鑰。 這種操作完全是在 HSM 內部執行。 目標金鑰一律會保留在 HSM 保護界限內。


## <a name="prerequisites"></a>必要條件

若要使用本文中的 Azure CLI 命名，您必須具有下列項目：

* Microsoft Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 2.12.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。
* 受控 HSM：訂用帳戶中 [支援的 hsm 清單](#supported-hsms) 。 請參閱[快速入門：使用 Azure CLI 佈建並啟動受控 HSM](quick-create-cli.md)，以佈建並啟動受控 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

若要進一步了解透過 CLI 的登入選項，請參閱[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="supported-hsms"></a>支援的 HSM

|廠商名稱|廠商類型|支援的 HSM 模型|詳細資訊|
|---|---|---|---|
|nCipher|製造商，<br/>HSM 即服務|<ul><li>HSM 的 nShield 系列</li><li>nShield 即服務</ul>|[nCipher 新的 BYOK 工具和文件](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|製造商|<ul><li>Luna HSM 7 系列 (含韌體版本7.3 或更新版本)</li></ul>| [Luna BYOK 工具和文件](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|製造商，<br/>HSM 即服務|<ul><li>自我保護金鑰管理服務 (SDKMS)</li><li>Equinix SmartKey</li></ul>|[將 SDKMS 金鑰匯出至 BYOK 的雲端提供者 - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|製造商|所有 LiquidSecurity HSM，具有<ul><li>韌體版本 2.0.4 或更新版本</li><li>韌體版本 3.2 或更新版本</li></ul>|[Marvell BYOK 工具和文件](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (企業金鑰管理系統)|多個 HSM 品牌和型號，包括<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>如需詳細資訊，請參閱 [Cryptomathic 網站](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK 工具和文件](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|製造商、HSM 即服務|Primus 其 HSM 系列，Securosys 雲 HSM|[Primus 其 BYOK 工具和檔](https://www.securosys.com/primus-azure-byok)|
||||


## <a name="supported-key-types"></a>支援的金鑰類型

|機碼名稱|金鑰類型|金鑰大小|來源|描述|
|---|---|---|---|---|
|金鑰交換金鑰 (KEK)|RSA| 2048 位元<br />3072 位元<br />4096 位元|受控 HSM|受控 HSM 中所產生的 HSM 支援的 RSA 金鑰組|
|目標金鑰|RSA|2048 位元<br />3072 位元<br />4096 位元|廠商 HSM|要傳送到受控 HSM 的金鑰|

## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>產生金鑰並將其傳輸到受控 HSM

若要產生金鑰並將其傳輸到受控 HSM：

* [步驟 1：產生 KEK](#step-1-generate-a-kek)
* [步驟 2：下載 KEK 公開金鑰](#step-2-download-the-kek-public-key)
* [步驟 3：產生並準備您的金鑰以進行傳輸](#step-3-generate-and-prepare-your-key-for-transfer)
* [步驟4：將您的金鑰傳輸至受控 HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>步驟 1:產生 KEK

KEK 是在受控 HSM 中產生的 RSA 金鑰。 KEK 用來加密您要匯入的金鑰 (「目標」金鑰)。

KEK 必須是：
- RSA-HSM 金鑰 (2048 位元；3072 位元或 4096 位元)
- 在您想要匯入目標金鑰的相同 managed HSM 中產生
- 建立時允許的金鑰作業設定為 `import`

> [!NOTE]
> 「匯入」必須是 KEK 唯一允許的金鑰作業。 「匯入」與所有其他金鑰作業互斥。

使用 [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-create) 命令，建立將金鑰作業設定為 `import` 的 KEK。 記錄從下列命令傳回的金鑰識別碼 (`kid`)。 (您將使用[步驟 3](#step-3-generate-and-prepare-your-key-for-transfer) 中的 `kid` 值。)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>步驟 2:下載 KEK 公開金鑰

使用 [az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-download) 將 KEK 公開金鑰下載至 .pem 檔案。 您匯入的目標金鑰會使用 KEK 公開金鑰加密。

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

將 KEKforBYOK.publickey.pem 檔案傳送到您的離線電腦。 您會在下一個步驟中使用此檔案。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>步驟 3：產生並準備您的金鑰以進行傳輸

請參閱 HSM 廠商的文件，以下載並安裝 BYOK 工具。 遵循 HSM 廠商提供的指示來產生目標金鑰，然後建立金鑰傳輸套件 (BYOK 檔案)。 BYOK 工具會使用[步驟 1](#step-1-generate-a-kek) 的 `kid` 以及[步驟 2](#step-2-download-the-kek-public-key) 中下載的 KEKforBYOK.publickey.pem 檔案，在 BYOK 檔案中產生加密的目標金鑰。

將 BYOK 檔案傳送到已連線的電腦。

> [!NOTE] 
> 不支援匯入 RSA 1024 位元金鑰。 目前不支援匯入Elliptic Curve (EC) 金鑰。
>
> **已知問題** ：只有韌體 7.4.0 或更新版本才支援從 Luna HSM 匯入 RSA 4K 目標金鑰。

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>步驟4：將您的金鑰傳輸至受控 HSM

若要完成金鑰匯入，請將金鑰傳輸套件 (BYOK 檔案) 從已中斷連線的電腦傳輸到連線網際網路的電腦。 使用 [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-import) 命令將 BYOK 檔案上傳至受管理的 HSM。

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

如果上傳成功，Azure CLI 會顯示匯入金鑰的屬性。

## <a name="next-steps"></a>後續步驟

您現在可以在受控 HSM 中使用此受 HSM 保護的金鑰。 如需詳細資訊，請參閱[本價格和功能比較](https://azure.microsoft.com/pricing/details/key-vault/)。



