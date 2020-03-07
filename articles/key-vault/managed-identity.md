---
title: 使用系統指派的受控識別來存取 Azure Key Vault
description: 瞭解如何為 App Service 應用程式建立受控識別，以及如何使用它來存取 Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 36a4871339401629300eedd77b6441aed10aabf3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395942"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>提供受控識別的 Key Vault 驗證

Azure Active Directory 的受控識別可讓您的應用程式輕鬆地存取其他 Azure AD 保護的資源。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 如需詳細資訊，請參閱[適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 

本文說明如何建立 App Service 應用程式的受控識別，並使用它來存取 Azure Key Vault。 針對 Azure Vm 中裝載的應用程式，請參閱[使用 WINDOWS VM 系統指派的受控識別來存取 Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites 

若要完成本指南，您必須擁有下列資源。 

- 一個金鑰保存庫。 您可以使用現有的金鑰保存庫，或依照下列其中一個快速入門中的步驟建立新的金鑰保存庫：
   - [使用 Azure CLI 建立金鑰保存庫](quick-create-cli.md)
   - [使用 Azure PowerShell 建立金鑰保存庫](quick-create-powershell.md)
   - [使用 Azure 入口網站建立金鑰保存庫](quick-create-portal.md)。
- 要對其授與金鑰保存庫存取權的現有 App Service 應用程式。 您可以遵循[App Service 檔](../app-service/overview.md)中的步驟來快速建立一個。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)或[Azure PowerShell](/powershell/azure/overview)。 或者，您可以使用 [Azure 入口網站](https://portal.azure.com)。


## <a name="adding-a-system-assigned-identity"></a>新增系統指派的身分識別 

首先，您必須將系統指派的身分識別新增至應用程式。 
 
### <a name="azure-portal"></a>Azure 入口網站 

若要在入口網站中設定受控身分識別，您需要先像平常一樣建立應用程式，然後再啟用此功能。 

1. 如果您使用函式應用程式，請瀏覽至 [平台功能]。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]。 

1. 選取 [受控身分識別]。 

1. 在 [系統指派] 索引標籤內，將 [狀態] 切換為 [開啟]。 按一下 [檔案]。 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

本快速入門需要 Azure CLI 版2.0.4 版或更新版本。 執行 `az --version` 來尋找您目前的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

若要以 Azure CLI 登入，請使用[az login](/cli/azure/reference-index?view=azure-cli-latest#az-login)命令：

```azurecli-interactive
az login
```

如需有關使用 Azure CLI 登入選項的詳細資訊，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。 

若要建立此應用程式的身分識別，請使用 Azure CLI [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign)命令或[az functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)命令：


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

請記下 `PrincipalId`，這將在下一節中需要。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>將您的應用程式存取權授與 Key Vault 

### <a name="azure-portal"></a>Azure 入口網站

1.  流覽至 Key Vault 資源。 

1.  選取 [**存取原則**]，然後按一下 [**新增存取原則**]。 

1.  在 [**秘密許可權**] 中，選取 [**取得]、[清單**]。 

1.  選擇 [**選取主體**]，然後在 [搜尋] 欄位中輸入應用程式的名稱。  在結果清單中選取應用程式，然後按一下 [**選取**]。 

1.  按一下 **[新增]** 完成新增存取原則的作業。

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

若要授與您的應用程式金鑰保存庫的存取權，請使用 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)命令，並提供**ObjectId**參數與您先前記下的**principalId** 。

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 安全性：身分識別和存取管理](overview-security.md#identity-and-access-management)
- [使用存取控制原則提供 Key Vault 驗證](key-vault-group-permissions-for-apps.md)
- [關於金鑰、密碼與憑證](about-keys-secrets-and-certificates.md)
- [保護您的 Key Vault](key-vault-secure-your-key-vault.md)。
- [Azure Key Vault 開發人員指南](key-vault-developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](key-vault-best-practices.md)