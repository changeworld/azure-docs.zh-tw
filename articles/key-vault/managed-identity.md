---
title: 使用系統分配的託管標識訪問 Azure 金鑰保存庫
description: 瞭解如何為應用服務應用程式創建託管標識，以及如何使用它訪問 Azure 金鑰保存庫
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270949"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>使用託管標識提供金鑰保存庫身份驗證

Azure 活動目錄的託管標識允許應用輕鬆訪問其他受 Azure AD 保護的資源。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 如需詳細資訊，請參閱[適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 

本文介紹如何為應用服務應用程式創建託管標識，並使用它訪問 Azure 金鑰保存庫。 對於在 Azure VM 中託管的應用程式，請參閱[使用 Windows VM 系統分配的託管標識訪問 Azure 金鑰保存庫](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites 

要完成本指南，您必須具有以下資源。 

- 一個金鑰保存庫。 您可以使用現有的金鑰保存庫，或依照下列其中一個快速入門中的步驟建立新的金鑰保存庫：
   - [使用 Azure CLI 建立金鑰保存庫](quick-create-cli.md)
   - [使用 Azure PowerShell 建立金鑰保存庫](quick-create-powershell.md)
   - [使用 Azure 入口網站建立金鑰保存庫](quick-create-portal.md)。
- 要向其授予金鑰保存庫存取權限的現有應用服務應用程式。 您可以按照[應用服務文件中](../app-service/overview.md)的步驟快速建立一個。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)或[Azure 電源外殼](/powershell/azure/overview)。 或者，您可以使用 [Azure 入口網站](https://portal.azure.com)。


## <a name="adding-a-system-assigned-identity"></a>新增系統指派的身分識別 

首先，必須向應用程式添加系統分配的標識。 
 
### <a name="azure-portal"></a>Azure 入口網站 

若要在入口網站中設定受控身分識別，您需要先像平常一樣建立應用程式，然後再啟用此功能。 

1. 如果您使用函式應用程式，請瀏覽至 [平台功能]****。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]****。 

1. 選取 [受控身分識別]****。 

1. 在 [系統指派]**** 索引標籤內，將 [狀態]**** 切換為 [開啟]****。 按一下 [儲存]****。 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

此快速入門需要 Azure CLI 版本 2.0.4 或更高版本。 執行 `az --version` 來尋找您目前的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

要使用 Azure CLI 登錄，請使用[az 登錄](/cli/azure/reference-index?view=azure-cli-latest#az-login)命令：

```azurecli-interactive
az login
```

有關使用 Azure CLI 的登錄選項的詳細資訊，請參閱[使用 Azure CLI 登錄](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。 

要為此應用程式創建標識，請使用 Azure CLI [az Webapp 標識分配](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign)命令或[az 函數應用標識分配](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)命令：


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

記下 下一節`PrincipalId`將需要的 。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>授予應用對金鑰保存庫的存取權限 

### <a name="azure-portal"></a>Azure 入口網站

1.  導航到金鑰保存庫資源。 

1.  選擇**訪問策略**，然後按一下 **"添加訪問策略**"。 

1.  在 **"機密許可權**"中，選擇 **"獲取、清單**"。 

1.  選擇 **"選擇主體**"，並在搜索欄位中輸入應用的名稱。  在結果清單中選擇應用，然後按一下"**選擇**"。 

1.  按一下"**添加**"以完成添加新訪問策略。

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

要授予應用程式對金鑰保存庫的存取權限，請使用 Azure CLI [az 金鑰保存庫集策略](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)命令，將**ObjectId**參數提供上面提到的**主體 Id。**

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>後續步驟

- [Azure 金鑰保存庫安全性：標識和訪問管理](overview-security.md#identity-and-access-management)
- [使用存取控制原則提供 Key Vault 驗證](key-vault-group-permissions-for-apps.md)
- [關於金鑰、機密和證書](about-keys-secrets-and-certificates.md)
- [保護您的金鑰保存庫](key-vault-secure-your-key-vault.md)。
- [Azure 金鑰保存庫開發人員指南](key-vault-developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](key-vault-best-practices.md)