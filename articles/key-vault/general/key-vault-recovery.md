---
title: Azure Key Vault 復原總覽 |Microsoft Docs
description: Key Vault 復原功能的設計目的，是為了避免意外或惡意刪除金鑰保存庫中儲存的金鑰保存庫和秘密、金鑰和憑證。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: ShaneBala-keyvault
ms.author: sudbalas
manager: ravijan
ms.date: 12/15/2020
ms.openlocfilehash: 485da2230de80150c9a5d13b262d1857c8c172fc
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587106"
---
# <a name="how-to-enable-soft-delete-and-purge-protection"></a>如何啟用虛刪除和清除保護

本文涵蓋 Azure Key Vault、虛刪除和清除保護的兩項復原功能。 本檔概要說明這些功能，並說明如何透過 Azure 入口網站、Azure CLI 和 Azure PowerShell 來管理它們。

## <a name="what-are-soft-delete-and-purge-protection"></a>什麼是虛刪除和清除保護

虛刪除和清除保護有兩種不同的金鑰保存庫修復功能。
> [!IMPORTANT]
> 開啟虛刪除很重要，可確保您的金鑰保存庫和認證受到保護，不會遭到意外刪除。 不過，開啟虛刪除會被視為重大變更，因為它可能會要求您變更應用程式邏輯，或為您的服務主體提供額外的許可權。 使用下列指示開啟虛刪除之前，請先確定您的應用程式與使用此檔的變更相容 [ ****。](soft-delete-change.md)

虛 **刪除** 是設計來防止意外刪除金鑰保存庫、金鑰、秘密，以及儲存在金鑰保存庫中的憑證。 請考慮虛刪除，例如回收站。 當您刪除金鑰保存庫或金鑰保存庫物件時，它會保持可供使用者設定的保留期間使用，或預設為90天。 也可以 **清除** 虛刪除狀態中的金鑰保存庫，這表示這些金鑰保存庫會永久刪除。 這可讓您以相同的名稱重新建立金鑰保存庫和金鑰保存庫物件。 復原和刪除金鑰保存庫和物件都需要較高的存取原則許可權。 **啟用虛刪除之後，即無法停用。**

請務必注意，金鑰保存 **庫名稱是全域唯一** 的，因此您將無法使用與已虛刪除狀態的金鑰保存庫相同的名稱來建立金鑰保存庫。 同樣地，金鑰、秘密和憑證的名稱在金鑰保存庫中是唯一的。 您將無法在「虛刪除」狀態中，建立與另一個名稱相同的秘密、金鑰或憑證。

**清除保護** 的設計目的是防止惡意 insider 刪除您的金鑰保存庫、金鑰、秘密和憑證。 請將此視為具有以時間為基礎之鎖定的回收站。 您可以在可設定的保留期間內的任何時間點復原專案。 **在保留期限到期之前，您將無法永久刪除或清除金鑰保存庫。** 一旦超過保留期限，將會自動清除金鑰保存庫或金鑰保存庫物件。

> [!NOTE]
> 清除保護的設計目的是讓沒有系統管理員角色或許可權可以覆寫、停用或規避清除保護。 **一旦啟用清除保護，任何人（包括 Microsoft）都無法停用或覆寫它。** 這表示在重複使用金鑰保存庫名稱之前，您必須先復原已刪除的金鑰保存庫，或等候保留期限經過。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>確認是否已在金鑰保存庫上啟用虛刪除，並啟用虛刪除

1. 登入 Azure 管理入口網站。
1. 選取您的金鑰保存庫。
1. 按一下 [屬性] 分頁。
1. 確認虛刪除旁邊的選項按鈕是否設定為「啟用復原」。
1. 如果未在金鑰保存庫上啟用虛刪除，請按一下選項按鈕以啟用虛刪除，然後按一下 [儲存]。

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="在 [屬性] 上，[虛刪除] 會反白顯示，也就是啟用它的值。":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>授與服務主體的存取權，以清除和復原已刪除的秘密

1. 登入 Azure 管理入口網站。
1. 選取您的金鑰保存庫。
1. 按一下 [存取原則] 分頁。
1. 在資料表中，找出您想要授與存取權給 (的安全性主體，或新增安全性主體) 的資料列。
1. 按一下 [金鑰]、[憑證] 和 [秘密] 的下拉式清單。
1. 滾動至下拉式清單底部，然後按一下 [復原] 和 [清除]
1. 安全性主體也需要取得和列出功能，才能執行大部分的作業。

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="在左側流覽窗格中，會反白顯示存取原則。在 [存取原則] 上，會顯示 [秘密位置] 下拉式清單，並選取四個專案：取得、列出、復原和清除。":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>列出、復原或清除虛刪除的金鑰保存庫

1. 登入 Azure 管理入口網站。
1. 按一下頁面頂端的 [搜尋] 列。
1. 在 [最近使用的服務] 下，按一下 [Key Vault]。 請勿按一下個別的金鑰保存庫。
1. 在畫面頂端，按一下 [管理已刪除的保存庫] 選項
1. 內容窗格會在螢幕的右邊開啟。
1. 選取您的訂用帳戶。
1. 如果您的金鑰保存庫已虛刪除，則會出現在右側的內容窗格中。
1. 如果有太多保存庫，您可以按一下內容窗格底部的 [載入更多]，或使用 CLI 或 PowerShell 來取得結果。
1. 一旦您找到想要復原或清除的保存庫，請選取它旁邊的核取方塊。
1. 如果您想要復原金鑰保存庫，請選取內容窗格底部的 [復原] 選項。
1. 如果您想要永久刪除金鑰保存庫，請選取 [清除] 選項。

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="在金鑰保存庫上，會反白顯示 [管理已刪除的保存庫] 選項。":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="在 [管理已刪除的金鑰保存庫] 上，會反白顯示並選取唯一列出的金鑰保存庫，並醒目提示 [復原] 按鈕。":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>列出、復原或清除虛刪除的秘密、金鑰和憑證

1. 登入 Azure 管理入口網站。
1. 選取您的金鑰保存庫。
1. 選取對應至您要管理之秘密類型的分頁， (金鑰、秘密或憑證) 。
1. 在畫面頂端，按一下 [管理已刪除的 (金鑰、秘密或憑證) 
1. 內容窗格將會出現在畫面的右側。
1. 如果您的秘密、金鑰或憑證未出現在清單中，則不會處於已虛刪除的狀態。
1. 選取您要管理的秘密、金鑰或憑證。
1. 在內容窗格的底部，選取要復原或清除的選項。

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="在 [金鑰] 上，會反白顯示 [管理已刪除的金鑰] 選項。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI) 

* 確認金鑰保存庫已啟用虛刪除

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 在金鑰保存庫上啟用虛刪除

    所有新的金鑰保存庫預設會啟用虛刪除。 如果您目前有未啟用虛刪除的金鑰保存庫，請使用下列命令來啟用虛刪除。

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* 如果已啟用虛刪除，則刪除金鑰保存庫 (可復原) 

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 列出所有虛刪除的金鑰保存庫

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* 復原虛刪除的金鑰-保存庫

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 清除虛刪除的金鑰保存庫 **(警告！這項作業會永久刪除您的金鑰保存庫)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 在金鑰保存庫上啟用清除保護

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a> (CLI) 的憑證

* 授與清除和復原憑證的存取權

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Delete certificate

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 列出已刪除的憑證

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 復原已刪除的憑證

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 清除虛刪除的憑證 **(警告！此操作會永久刪除您的憑證)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a> (CLI) 的金鑰

* 授與清除和復原金鑰的存取權

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Delete 鍵

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 列出已刪除的金鑰

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 復原已刪除的金鑰

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 清除虛刪除的金鑰 **(警告！這項操作會永久刪除您的金鑰)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a> (CLI) 的秘密

* 授與清除和復原秘密的存取權

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* 刪除秘密

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 列出已刪除的秘密

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 復原已刪除的秘密

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 清除虛刪除的秘密 **(警告！這種作業會永久刪除您的秘密)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell) 

* 確認金鑰保存庫已啟用虛刪除

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* 刪除 key vault

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* 列出所有虛刪除的金鑰保存庫

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* 復原虛刪除的金鑰-保存庫

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* 清除虛刪除的金鑰-保存庫 **(警告！這項作業會永久刪除您的金鑰保存庫)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* 在金鑰保存庫上啟用清除保護

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a> (PowerShell) 的憑證

* 授與復原和清除憑證的許可權

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* 刪除憑證

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* 列出金鑰保存庫中所有已刪除的憑證

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* 復原已刪除狀態的憑證

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* 清除虛刪除的憑證 **(警告！此操作會永久刪除您的憑證)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a> (PowerShell) 的金鑰

* 授與復原和清除金鑰的許可權

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* 刪除金鑰

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* 列出金鑰保存庫中所有已刪除的憑證

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* 復原虛刪除的金鑰

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* 清除虛刪除的金鑰 **(警告！這項操作會永久刪除您的金鑰)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a> (PowerShell) 的秘密

* 授與復原和清除秘密的許可權

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* 刪除名為 SQLPassword 的秘密

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* 列出金鑰保存庫中所有已刪除的秘密

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* 復原已刪除狀態的秘密

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* 清除處於已刪除狀態的秘密 **(警告！這項操作會永久刪除您的金鑰)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
