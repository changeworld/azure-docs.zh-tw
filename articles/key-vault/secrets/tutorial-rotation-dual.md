---
title: 具有兩組認證的資源輪替教學課程
description: 使用此教學課程來了解如何針對使用兩組驗證認證的資源，自動輪替祕密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: ba9ff0ead1131b091aa1a5ece2ecf94d2319a968
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85800692"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>針對使用兩組驗證認證的資源，將秘密的輪替自動化

向 Azure 服務進行驗證的最佳方式是使用[受控識別](../general/managed-identity.md)，但在某些情況下並無法使用此選項。 在這類情況下，應使用存取金鑰或密碼。 存取金鑰和密碼應經常輪替。

本教學課程示範如何針對使用兩組驗證認證的資料庫和服務自動執行定期的秘密輪替。 具體而言，本教學課程會使用 Azure 事件方格通知所觸發的函式，來輪替儲存在 Azure Key Vault 中做為祕密的 Azure 儲存體帳戶金鑰。 所解碼的字元：

> [!NOTE]
> 儲存體帳戶金鑰可以提供共用存取簽章權杖以供委派存取儲存體帳戶，藉此在 Key Vault 中加以自動管理。 有一些服務需要具有存取金鑰的儲存體帳戶連接字串，在這種情況下建議使用此解決方案

![輪替解決方案圖表](../media/secrets/rotation-dual/rotation-diagram.png)

在上述解決方案中，Azure Key Vault 會將儲存體帳戶個別存取金鑰儲存為相同秘密的版本，並在後續版本的主要和次要金鑰之間進行交替。 在最新版本的秘密中儲存一個存取金鑰時，會重新產生替代金鑰，並將其新增至 Key Vault 做為新版本和最新版本的祕密。 該解決方案會提供應用程式整個輪替週期，以重新整理為最新的重新產生金鑰。 

1. 在祕密到期日的 30 天前，Key Vault 會將「即將到期」事件發佈至事件方格。
1. 事件方格會檢查事件訂閱，並使用 HTTP POST 呼叫訂閱此事件的函式應用程式端點。
1. 函數應用程式會識別替代金鑰 (最新版本以外)，並呼叫儲存體帳戶來加以重新產生
1. 函數應用程式會將新重新產生的金鑰新增至 Azure Key Vault 做為新版本的祕密。

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure 金鑰保存庫
* 兩個 Azure 儲存體帳戶

如果您沒有現有的金鑰保存庫和儲存體帳戶，可以使用下列部署連結：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

1. 在 [資源群組] 下方，選取 [新建]。 將群組命名為 **akvrotation** 然後按一下 [確定]。
1. 請選取 [檢閱 + 建立]。
1. 選取 [建立] 

    ![建立資源群組](../media/secrets/rotation-dual/dual-rotation-1.png)

您現在會有一個金鑰保存庫和兩個儲存體帳戶。 您可以執行下列命令，在 Azure CLI 中驗證這項設定：

```azurecli
az resource list -o table -g akvrotation
```

結果看起來會像下列輸出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>建立及部署儲存體帳戶金鑰輪替函式

接下來，使用系統管理的身分識別以及其他必要元件建立函數應用程式，並部署儲存體帳戶金鑰輪替函式

函數應用程式輪替函式需要這些元件和設定：
- Azure App Service 方案
- 函數應用程式觸發程式管理所需的儲存體帳戶
- 在 Key Vault 中存取祕密的存取原則
- 將儲存體帳戶金鑰運算子服務角色指派給函數應用程式，以存取儲存體帳戶存取金鑰
- 具有事件觸發程序和 HTTP 觸發程序的儲存體帳戶金鑰輪替函式 (隨選輪替)
- **SecretNearExpiry** 事件的 EventGrid 事件訂用帳戶

1. 選取 Azure 範本部署連結：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

1. 在 [資源群組] 清單中，選取 [akvrotation]。
1. 在 [儲存體帳戶名稱] 中，輸入具有所要輪替存取金鑰的儲存體帳戶名稱
1. 在 [Key Vault 名稱] 中，輸入金鑰保存庫名稱
1. 在 [函數應用程式名稱] 中，輸入函數應用程式名稱
1. 在 [秘密名稱] 中，輸入要儲存存取金鑰的秘密名稱
1. 在 [存放庫 URL] 中，輸入函式程式碼 GitHub 位置 ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** )
1. 請選取 [檢閱 + 建立]。
1. 選取 [建立] 

   ![檢閱 + 建立](../media/secrets/rotation-dual/dual-rotation-2.png)

完成上述步驟之後，您將會有儲存體帳戶、伺服器陣列、函數應用程式、Application Insights。 完成部署後，您應該會看到下列畫面：![部署完成](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> 如果發生任何失敗，您可以按一下 [重新部署]，完成其餘元件的部署。


您可以在 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell) 上找到部署範本和輪替函式程式碼。

## <a name="add-storage-account-access-key-to-key-vault"></a>將儲存體帳戶存取金鑰新增至 Key Vault

首先，設定存取原則，將「管理秘密」權限授與使用者：

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

您現在可以使用儲存體帳戶存取金鑰做為值，來建立新的祕密。 您也需要儲存體帳戶資源識別碼、秘密有效期間和金鑰識別碼以新增至秘密，因此輪替函式可以在儲存體帳戶中重新產生金鑰。

取出儲存體帳戶資源識別碼。 可以在 `id` 屬性下找到值
```azurecli
az storage account show -n akvrotationstorage
```

列出儲存體帳戶存取金鑰以取出金鑰值

```azurecli
az storage account keys list -n akvrotationstorage 
```

填入 **key1Value** 和 **storageAccountResourceId** 的取出值

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

建立具有簡短到期日的祕密，系統會在幾分鐘內發佈 `SecretNearExpiry` 事件，進而觸發函式進行秘密輪替。

您可以取出及比較儲存體帳戶金鑰和 Key Vault 秘密，藉以確認是否已重新產生存取金鑰。

您可以使用下列命令來顯示秘密資訊：
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
請注意，`CredentialId` 會更新為替代 `keyName`，`value` 會重新產生![秘密顯示](../media/secrets/rotation-dual/dual-rotation-4.png)

取出存取金鑰以驗證值
```azurecli
az storage account keys list -n akvrotationstorage 
```
![存取金鑰清單](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>新增其他儲存體帳戶以進行輪替

您可以重複使用相同的函數應用程式來輪替多個儲存體帳戶。 

將其他進行輪替的儲存體帳戶金鑰新增至現有函式需要：
- 將儲存體帳戶金鑰運算子服務角色指派給函數應用程式，以存取儲存體帳戶存取金鑰
- **SecretNearExpiry** 事件的 EventGrid 事件訂用帳戶

1. 選取 Azure 範本部署連結：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

1. 在 [資源群組] 清單中，選取 [akvrotation]。
1. 在 [儲存體帳戶名稱] 中，輸入具有所要輪替存取金鑰的儲存體帳戶名稱
1. 在 [Key Vault 名稱] 中，輸入金鑰保存庫名稱
1. 在 [函數應用程式名稱] 中，輸入函數應用程式名稱
1. 在 [秘密名稱] 中，輸入要儲存存取金鑰的秘密名稱
1. 請選取 [檢閱 + 建立]。
1. 選取 [建立] 

   ![檢閱 + 建立](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>將另一個儲存體帳戶存取金鑰新增至 Key Vault

取出儲存體帳戶資源識別碼。 可以在 `id` 屬性下找到值
```azurecli
az storage account show -n akvrotationstorage2
```

列出儲存體帳戶存取金鑰以取出 key2 值

```azurecli
az storage account keys list -n akvrotationstorage2 
```

填入 **key2Value** 和 **storageAccountResourceId** 的取出值

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

使用下列命令顯示秘密資訊：
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
請注意，`CredentialId` 會更新為替代 `keyName`，`value` 會重新產生![秘密顯示](../media/secrets/rotation-dual/dual-rotation-8.png)

取出存取金鑰以驗證值
```azurecli
az storage account keys list -n akvrotationstorage 
```
![存取金鑰清單](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>可用 Key Vault 雙重認證輪替函式

- [儲存體帳戶](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis 快取](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>深入了解
- 概觀：[使用 Azure 事件方格監視 Key Vault (預覽)](../general/event-grid-overview.md)
- 如何：[在 Azure 入口網站中建立您的第一個函式](../../azure-functions/functions-create-first-azure-function.md)
- 如何：[在金鑰保存庫祕密變更時收到電子郵件](../general/event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件方格事件結構描述 (預覽)](../../event-grid/event-schema-key-vault.md)
