---
title: " (CLI 指派 Azure Key Vault 存取原則) "
description: 如何使用 Azure CLI 將 Key Vault 存取原則指派給服務主體或應用程式身分識別。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 32a323e8cc56a8c45b18737bb55ebe19d4be27c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380817"
---
# <a name="assign-a-key-vault-access-policy"></a>指派 Key Vault 存取原則

Key Vault 存取原則會決定指定的服務主體（也就是應用程式或使用者群組）是否可以對 Key Vault 的 [秘密](../secrets/index.yml)、 [金鑰](../keys/index.yml)和 [憑證](../certificates/index.yml)執行不同的作業。 您可以使用 [Azure 入口網站](assign-access-policy-portal.md)、Azure CLI (本文) 或 [Azure PowerShell](assign-access-policy-powershell.md)來指派存取原則。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

如需使用 Azure CLI 在 Azure Active Directory 中建立群組的詳細資訊，請參閱 [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) 和 [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)。

## <a name="configure-the-azure-cli-and-sign-in"></a>設定 Azure CLI 並登入

1. 若要在本機執行 Azure CLI 命令，請安裝 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
 
    若要直接在雲端中執行命令，請使用 [Azure Cloud Shell](/azure/cloud-shell/overview)。

1. 僅本機 CLI：使用下列方式登入 Azure `az login` ：

    ```bash
    az login
    ```

    此 `az login` 命令會開啟瀏覽器視窗來收集認證（如有需要）。

## <a name="acquire-the-object-id"></a>取得物件識別碼

決定您要指派存取原則的應用程式、群組或使用者的物件識別碼：

- 應用程式和其他服務主體：使用 [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 命令來取出您的服務主體。 檢查命令的輸出，以判斷您要指派存取原則之安全性主體的物件識別碼。

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- 群組：使用 [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) 命令，並使用參數篩選結果 `--display-name` ：

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- 使用者：使用 [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) 命令，並在參數中傳遞使用者的電子郵件地址 `--id` ：

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>指派存取原則
    
使用 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令指派所需的許可權：

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

取代 `<object-id>` 為您服務主體的物件識別碼。

`--secret-permissions` `--key-permissions` `--certificate-permissions` 當您將許可權指派給這些特定類型時，只需要包含、和。 `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [Az keyvault set 原則](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)檔中提供、和的允許值。

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 安全性：身分識別和存取管理](overview-security.md#identity-and-access-management)
- [保護您的金鑰保存庫](secure-your-key-vault.md)。
- [Azure Key Vault 開發人員指南](developers-guide.md)
- [Azure Key Vault 最佳做法](best-practices.md)
