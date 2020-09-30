---
title: Azure 服務中斷對受控 HSM 造成影響時該如何處理 - Azure Key Vault | Microsoft Docs
description: 了解 Azure 服務中斷對受控 HSM 造成影響時該如何處理。
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 13f62631e4913434699f4c5dd5eb1956ca3e3a36
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992151"
---
# <a name="managed-hsm-disaster-recovery"></a>受控 HSM 災害復原

如果您的原始 HSM 遺失或因下列任何原因而無法使用，您可以建立完整的 HSM 複本：

- 遭到刪除，繼而清除。
- 所在區域發生嚴重失敗，導致所有成員分割區遭到終結。

如果您具有下列項目，則可以在相同或不同的區域中重新建立 HSM 執行個體：
- 來源 HSM 的[安全性網域](security-domain.md)。
- 將安全性網域加密的私密金鑰 (至少達仲裁數目)。
- 來源 HSM 最新的完整 HSM [備份](backup-restore.md)。

以下是災害復原程序的步驟：

1. 建立新的 HSM 執行個體。
1. 啟動「安全性原則復原」。 系統會為安全性網域傳輸產生新的 RSA 金鑰組 (安全性網域交換金鑰)，並以回應傳送，進而以 SecurityDomainExchangeKey (公開金鑰) 的形式供使用者下載。
1. 建立並上傳「安全性網域傳輸檔案」。 您將需要為安全性網域加密的私密金鑰。 私密金鑰會在本機使用，且絕不會在此程序中傳輸至任何位置。
1. 建立新 HSM 的備份。 您必須先備份才能進行還原，即使 HSM 是空的。 備份可讓您輕鬆地復原。
1. 從來源 HSM 還原最新的 HSM 備份

金鑰保存庫的內容會在區域內複寫，以及複寫到至少距離 150 英哩但位於相同地理位置內的次要區域。 此功能可維持金鑰和秘密的高持久性。 請參閱 [Azure 配對的區域](../../best-practices-availability-paired-regions.md)文件，以取得特定區域配對的詳細資料。

## <a name="create-a-new-managed-hsm"></a>建立新的受控 HSM

使用 `az keyvault create` 命令建立受控 HSM。 此指令碼包含三個必要參數：資源群組名稱、HSM 名稱和地理位置。

您必須提供下列輸入，才能建立受控 HSM 資源：

- HSM 的名稱。
- 要放置在訂用帳戶中的資源群組。
- Azure 位置。
- 初始管理員的清單。

下列範例會在位於**美國東部 2** 位置的資源群組 **ContosoResourceGroup** 中建立名為 **ContosoMHSM** 的 HSM，並將**目前登入的使用者**設為唯一管理員。

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> 執行 create 命令可能需要幾分鐘的時間。 成功傳回之後，您即可啟動 HSM。

此命令的輸出會顯示您所建立之受控 HSM 的屬性。 兩個最重要屬性是：

* **名稱**：在此範例中，名稱是 ContosoMHSM。 您將在其他 Key Vault 命令中使用此名稱。
* **hsmUri**：在此範例中，URI 是 https://contosohsm.managedhsm.azure.net 。 透過其 REST API 使用 HSM 的應用程式必須使用此 URI。

您的 Azure 帳戶現已取得在此受控 HSM 上執行任何作業的授權。 而且，沒有其他人已獲授權。

## <a name="activate-the-security-domain-recovery-mode"></a>啟動安全性網域復原模式

在一般的建立程序中，我們會在此時初始化並下載新的安全性網域。 不過，由於要執行災害復原程序，我們會要求 HSM 進入安全性網域復原模式，並下載安全性網域交換金鑰。 安全性網域交換金鑰是一個 RSA 公開金鑰，用來在安全性網域上傳至 HSM 之前予以加密。 對應的私密金鑰會在 HSM 內受到保護，以確保您的安全性網域內容在傳輸期間安全無虞。

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>將安全性網域上傳至目的地 HSM

在此步驟中，您將需要下列各項：
- 您在上一個步驟中下載的安全性網域交換金鑰。
- 來源 HSM 的安全性網域。
- 用來加密安全性網域的私密金鑰 (至少達仲裁數目)。

`az keyvault security-domain upload` 命令會執行下列作業：

- 使用您提供的私密金鑰將來源 HSM 的安全性網域解密。 
- 使用我們在上一個步驟下載的安全性網域交換金鑰，建立加密的安全性網域上傳 Blob，然後
- 將安全性網域上傳 Blob 上傳至 HSM，以完成安全性網域復原

在下列範例中，我們會使用 **ContosoMHSM** 中的安全性網域 (對應私密金鑰的 2)，並將其上傳至正在等待接收安全性網域的 **ContosoMHSM2**。 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

現在，來源 HSM (ContosoMHSM) 和目的地 HSM (ContosoMHSM2) 具有相同的安全性網域。 我們現在可以將來源 HSM 的完整備份還原至目的地 HSM。

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>建立新 HSM 的備份 (作為還原點)

在執行完整的 HSM 還原之前，最好先進行完整備份，如此，萬一還原發生問題時，您將有還原點可供使用。

若要建立 HSM 備份，您將需要下列各項
- 將用來儲存備份的儲存體帳戶
- 此儲存體帳戶中的 Blob 儲存體容器；備份程序會在其中建立新資料夾來儲存加密備份

在下列範例中，我們會使用 `az keyvault backup` 命令，將 HSM 備份儲存在儲存體帳戶 **ContosoBackup** 中的儲存體容器 **mhsmbackupcontainer**。 我們會建立在 30 分鐘後到期的 SAS 權杖，並將其提供給受控 HSM 以寫入備份。

```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>從來源 HSM 還原備份

在此步驟中，您需要下列各項：

- 來源 HSM 的備份儲存所在的儲存體帳戶和 Blob 容器。
- 您要從中還原備份的資料夾名稱。 如果您建立定期備份，此容器內將會有許多資料夾。


```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

現在，您已完成完整的災害復原程序。 取得備份時的來源 HSM 內容會複製到目的地 HSM，包括所有的金鑰、版本、屬性、標籤和角色指派。

## <a name="next-steps"></a>下一步

- 請參閱[關於受控 HSM 安全性網域](security-domain.md)，以深入了解安全性網域的詳細資訊
- 遵循[受控 HSM 最佳做法](best-practices.md)
