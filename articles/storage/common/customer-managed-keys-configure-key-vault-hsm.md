---
title: 使用儲存在 Azure Key Vault 受控 HSM (preview) 中的客戶管理金鑰來設定加密
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure CLI，以使用儲存在 Azure Key Vault 受控 HSM (preview) 中客戶管理的金鑰來設定 Azure 儲存體加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9c742ca2fd9779589a3c8aea7f030460c5db8b5d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994402"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>使用儲存在 Azure Key Vault 受控 HSM (preview) 中的客戶管理金鑰來設定加密

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以管理自己的金鑰。 客戶管理的金鑰必須儲存在 Azure Key Vault 或 Key Vault 受控硬體安全性模型 (HSM)  (preview) 。 受 Azure Key Vault 管理的 HSM 是經過 FIPS 140-2 層級3驗證的 HSM。

本文說明如何使用 Azure CLI，透過受管理的 HSM 中儲存的客戶管理金鑰來設定加密。 若要瞭解如何使用儲存在金鑰保存庫中客戶管理的金鑰來設定加密，請參閱 [使用儲存在 Azure Key Vault 中的客戶管理金鑰來設定加密](customer-managed-keys-configure-key-vault.md)。

> [!NOTE]
> Azure Key Vault 和 Azure Key Vault 受控 HSM 支援相同的 Api 和管理介面進行設定。

## <a name="assign-an-identity-to-the-storage-account"></a>將身分識別指派給儲存體帳戶

首先，將系統指派的受控識別指派給儲存體帳戶。 您將使用此受控識別來授與儲存體帳戶存取受控 HSM 的許可權。 如需系統指派的受控識別的詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 Azure CLI 指派受控識別，請呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update)。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>將角色指派給儲存體帳戶，以存取受管理的 HSM

接下來，將 **受控 Hsm 加密服務加密** 角色指派給儲存體帳戶的受控識別，讓儲存體帳戶具有受控 hsm 的許可權。 Microsoft 建議您將角色指派的範圍設為個別金鑰的層級，以便將最少的可能許可權授與受控識別。

若要建立儲存體帳戶的角色指派，請呼叫 [az key vault 角色指派 create](/cli/azure/role/assignment#az_role_assignment_create)。 請記得以您自己的值取代括弧中的預留位置值。
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>使用受控 HSM 中的金鑰設定加密

最後，使用客戶管理的金鑰來設定 Azure 儲存體加密，以使用儲存在受控 HSM 中的金鑰。 支援的金鑰類型包括 RSA-HSM 金鑰，大小為2048、3072和4096。 安裝 Azure CLI 2.12.0 或更新版本，以將加密設定為在受管理的 HSM 中使用客戶管理的金鑰。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

若要自動更新客戶管理金鑰的金鑰版本，請在您針對儲存體帳戶使用客戶管理的金鑰設定加密時，省略金鑰版本。 呼叫 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 以更新儲存體帳戶的加密設定，如下列範例所示。 包含 `--encryption-key-source parameter` 並將它設定為， `Microsoft.Keyvault` 以針對帳戶啟用客戶管理的金鑰。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

若要手動更新客戶管理金鑰的版本，請在設定儲存體帳戶的加密時，包含金鑰版本：

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

當您手動更新金鑰版本時，您必須將儲存體帳戶的加密設定更新為使用新版本。 首先，藉由呼叫 [az keyvault show](/cli/azure/keyvault#az-keyvault-show)來查詢金鑰保存庫 URI，並藉由呼叫 [az keyvault key list-](/cli/azure/keyvault/key#az-keyvault-key-list-versions)version 來查詢金鑰版本。 然後呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 更新儲存體帳戶的加密設定，以使用新版本的金鑰，如先前範例所示。

## <a name="next-steps"></a>下一步

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [Azure 儲存體加密的客戶管理金鑰](customer-managed-keys-overview.md)