---
title: 具有兩組認證的資源輪替教學課程
description: 使用此教學課程來了解如何針對使用兩組驗證認證的資源，自動輪替祕密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 72541b8d8f8d8865c680c36f7f84cd91a4ce8ba2
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903321"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>針對具有兩組驗證認證的資源，將秘密的輪替自動化

向 Azure 服務進行驗證的最佳方式是使用[受控識別](../general/authentication.md)，但在某些情況下並無法使用此選項。 在這類情況下，應使用存取金鑰或密碼。 您應經常輪替存取金鑰和密碼。

本教學課程示範如何針對使用兩組驗證認證的資料庫和服務自動執行定期的秘密輪替。 具體而言，本教學課程會說明如何輪替儲存在 Azure Key Vault 中作為祕密的 Azure 儲存體帳戶金鑰。 您將使用 Azure 事件方格通知所觸發的函式。 

> [!NOTE]
> 如果您提供共用存取簽章權杖以供委派存取儲存體帳戶，則會在 Key Vault 中自動管理儲存體帳戶金鑰。 有一些服務需要具有存取金鑰的儲存體帳戶連接字串。 針對這類案例，我們建議採用此解決方案。

以下是本教學課程中說明的輪替解決方案： 

![顯示輪替解決方案的圖表。](../media/secrets/rotation-dual/rotation-diagram.png)

在此解決方案中，Azure Key Vault 會將儲存體帳戶個別存取金鑰儲存為相同秘密的版本，並在後續版本的主要和次要金鑰之間進行交替。 在最新版本的秘密中儲存一個存取金鑰時，會重新產生替代金鑰，並將其新增至 Key Vault 作為最新版本的祕密。 此解決方案會提供應用程式的整個輪替週期，以重新整理為最新的重新產生金鑰。 

1. 在祕密到期日的 30 天前，Key Vault 會將即將到期事件發佈至事件方格。
1. 事件方格會檢查事件訂閱，並使用 HTTP POST 呼叫訂閱此事件的函式應用程式端點。
1. 函式應用程式會識別替代金鑰 (非最新版本)，並呼叫儲存體帳戶來加以重新產生。
1. 函式應用程式會將重新產生的新金鑰新增至 Azure Key Vault，作為新版本的祕密。

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 [建立免費訂用帳戶。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault。
* 兩個 Azure 儲存體帳戶。

如果您沒有現有的金鑰保存庫和現有的儲存體帳戶，您可以使用此部署連結：

[![標示為 [部署至 Azure] 的連結。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. 在 [資源群組] 下方，選取 [新建]。 將群組命名為 **akvrotation**，然後選取 [確定]。
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。

    ![顯示如何建立資源群組的螢幕擷取畫面。](../media/secrets/rotation-dual/dual-rotation-1.png)

您現在會有一個金鑰保存庫和兩個儲存體帳戶。 您可以執行下列命令，在 Azure CLI 中驗證這項設定：

```azurecli
az resource list -o table -g akvrotation
```

結果將如下列輸出所示：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>建立及部署金鑰輪替函式

接下來，您將使用系統管理的身分識別以及其他必要元件建立函式應用程式。 您也會部署儲存體帳戶金鑰的輪替函式。

函式應用程式輪替函式需要下列元件和設定：
- Azure App Service 方案
- 用來管理函式應用程式觸發程序的儲存體帳戶
- 在 Key Vault 中存取祕密的存取原則
- 指派給函式應用程式的儲存體帳戶金鑰操作員服務角色，使其可以存取儲存體帳戶存取金鑰
- 具有事件觸發程序和 HTTP 觸發程序的金鑰輪替函式 (隨選輪替)
- **SecretNearExpiry** 事件的「事件方格」事件訂閱

1. 選取 Azure 範本部署連結： 

   [![Azure 範本部署連結。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. 在 [資源群組] 清單中，選取 [akvrotation]。
1. 在 [儲存體帳戶 RG] 方塊中，輸入您的儲存體帳戶所在的資源群組名稱。 如果您的儲存體帳戶已位於您將在其中部署金鑰輪替函式的相同資源群組中，請保留預設值 **[resourceGroup().name]** 。
1. 在 [儲存體帳戶名稱] 方塊中，輸入要輪替的存取金鑰所屬的儲存體帳戶名稱。
1. 在 [Key Vault RG] 方塊中，輸入您的金鑰保存庫所在的資源群組名稱。 如果您的金鑰保存庫已存在於您將在其中部署金鑰輪替函式的相同資源群組中，請保留預設值 **[resourceGroup().name]** 。
1. 在 [金鑰保存庫名稱] 方塊中，輸入金鑰保存庫的名稱。
1. 在 [函式應用程式名稱] 方塊中，輸入函式應用程式的名稱。
1. 在 [秘密名稱] 方塊中，輸入您將用來儲存存取金鑰的秘密名稱。
1. 在 [存放庫 URL] 方塊中，輸入函式程式碼的 GitHub 位置： **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** 。
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。

   ![顯示如何建立第一個儲存體帳戶的螢幕擷取畫面。](../media/secrets/rotation-dual/dual-rotation-2.png)

完成上述步驟之後，您將會有儲存體帳戶、伺服器陣列、函式應用程式和 Application Insights。 部署完成後，您會看到下列頁面：![顯示「您的部署已完成」頁面的螢幕擷取畫面。](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> 如果發生失敗的狀況，您可以選取 [重新部署] 以完成元件的部署。


您可以在 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell) 上找到輪替函式的部署範本和程式碼。

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>將儲存體帳戶存取金鑰新增至 Key Vault

首先，設定存取原則，將「管理秘密」權限授與使用者：

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

現在，您可以使用儲存體帳戶存取金鑰作為值，以建立新的祕密。 您也需要儲存體帳戶資源識別碼、秘密有效期間和金鑰識別碼以新增至秘密，讓輪替函式可以在儲存體帳戶中重新產生金鑰。

確認儲存體帳戶資源識別碼。 您可以在 `id` 屬性中找到此值。
```azurecli
az storage account show -n akvrotationstorage
```

列出儲存體帳戶存取金鑰，以便取得金鑰值：

```azurecli
az storage account keys list -n akvrotationstorage 
```

使用您擷取的 `key1Value` 和 `storageAccountResourceId` 值，執行下列命令：

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

如果您建立的秘密到期日較短，則 `SecretNearExpiry` 事件會在數分鐘內發佈。 此事件接著會觸發函式以輪替秘密。

您可以擷取儲存體帳戶金鑰和 Key Vault 秘密並進行比較，以確認存取金鑰已重新產生。

使用下列命令取得秘密資訊：
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
請注意，`CredentialId` 會更新為替代 `keyName`，而 `value` 會重新產生：![此螢幕擷取畫面顯示第一個儲存體帳戶的 az keyvault secret show 命令的輸出。](../media/secrets/rotation-dual/dual-rotation-4.png)

擷取存取金鑰以比較其值：
```azurecli
az storage account keys list -n akvrotationstorage 
```
![此螢幕擷取畫面顯示第一個儲存體帳戶的 az storage account keys list 命令的輸出。](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>新增用於輪替的儲存體帳戶

您可以重複使用相同的函式應用程式，為多個儲存體帳戶輪替金鑰。 

若要將儲存體帳戶金鑰新增至現有的輪替函式，您需要：
- 指派給函式應用程式的儲存體帳戶金鑰操作員服務角色，使其可以存取儲存體帳戶存取金鑰。
- **SecretNearExpiry** 事件的「事件方格」事件訂閱。

1. 選取 Azure 範本部署連結： 

   [![Azure 範本部署連結。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. 在 [資源群組] 清單中，選取 [akvrotation]。
1. 在 [儲存體帳戶名稱] 方塊中，輸入要輪替的存取金鑰所屬的儲存體帳戶名稱。
1. 在 [金鑰保存庫名稱] 方塊中，輸入金鑰保存庫的名稱。
1. 在 [函式應用程式名稱] 方塊中，輸入函式應用程式的名稱。
1. 在 [秘密名稱] 方塊中，輸入您將用來儲存存取金鑰的秘密名稱。
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。

   ![顯示如何建立其他儲存體帳戶的螢幕擷取畫面。](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>將另一個儲存體帳戶存取金鑰新增至 Key Vault

確認儲存體帳戶資源識別碼。 您可以在 `id` 屬性中找到此值。
```azurecli
az storage account show -n akvrotationstorage2
```

列出儲存體帳戶存取金鑰，以便取得 key2 值：

```azurecli
az storage account keys list -n akvrotationstorage2 
```

使用您擷取的 `key2Value` 和 `storageAccountResourceId` 值，執行下列命令：

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

使用下列命令取得秘密資訊：
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
請注意，`CredentialId` 會更新為替代 `keyName`，而 `value` 會重新產生：![此螢幕擷取畫面顯示第二個儲存體帳戶的 az keyvault secret show 命令的輸出。](../media/secrets/rotation-dual/dual-rotation-8.png)

擷取存取金鑰以比較其值：
```azurecli
az storage account keys list -n akvrotationstorage 
```
![此螢幕擷取畫面顯示第二個儲存體帳戶的 az storage account keys list 命令的輸出。](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Key Vault 雙重認證輪替函式

- [儲存體帳戶](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis 快取](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>後續步驟
- 概觀：[使用 Azure 事件方格監視 Key Vault](../general/event-grid-overview.md)
- 如何：[在 Azure 入口網站中建立您的第一個函式](../../azure-functions/functions-create-first-azure-function.md)
- 操作說明：[在 Key Vault 祕密變更時接收電子郵件](../general/event-grid-logicapps.md)
- 參考：[Azure Key Vault 的 Azure 事件方格事件結構描述](../../event-grid/event-schema-key-vault.md)
